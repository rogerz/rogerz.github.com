---
layout: post
title: "select() in lwip"
comments: true
categories: code
published: true

---

[lwip](http://savannah.nongnu.org/projects/lwip/) has its own select() implementation named `lwip_select()`. There are two key points in the implementation of select().

First, the file descriptor needs to provide an interface to check the readiness of reading or writing. 

In lwip socket, it is implemented in `lwip_selscan()` which will check the data and event binding to a socket.

    if (sock != NULL) {
      lastdata = sock-&gt;lastdata;
      rcvevent = sock-&gt;rcvevent;
      sendevent = sock-&gt;sendevent;
      errevent = sock-&gt;errevent;
    }

Second, the select() function should provide a mechanism to suspend current thread and wake it up when it is ready for read or write.

lwip uses a global list `select_cb_list` to hold all the thread pending on `select()`.

    /* Put this select_cb on top of list */
    select_cb.next = select_cb_list;
    if (select_cb_list != NULL) {
      select_cb_list-&gt;prev = &amp;select_cb;
    }
    select_cb_list = &amp;select_cb;
    /* Increasing this counter tells even_callback that the list has changed. */
    select_cb_ctr++;

Here `select_cb` is a local struct in `select()`, which stores the readset, writeset and exceptset passed by `select()`. A semaphore is created to let the thread wait on and be waken up later.

      waitres = sys_arch_sem_wait(&amp;select_cb.sem, msectimeout);

Then how do we wake the threads up? It is inside `event_callback()` which is triggered whenever there is something new on socket. It traverse the `select_cb_list` and wake the corresponding threads up by signal the semaphore.

    while (1) {
        sys_sem_wait(selectsem);
        for (scb = select_cb_list; scb; scb = scb-&gt;next) {
            if (scb-&gt;sem_signalled == 0) {
            /* Test this select call for our socket */
            if (scb-&gt;readset &amp;&amp; FD_ISSET(s, scb-&gt;readset))
                if (sock-&gt;rcvevent &gt; 0)
                    break;
            if (scb-&gt;writeset &amp;&amp; FD_ISSET(s, scb-&gt;writeset))
                if (sock-&gt;sendevent)
                    break;
            }
        }
        if (scb) {
            scb-&gt;sem_signalled = 1;
            sys_sem_signal(scb-&gt;sem);
            sys_sem_signal(selectsem);
        } else {
            sys_sem_signal(selectsem);
            break;
        }
    }