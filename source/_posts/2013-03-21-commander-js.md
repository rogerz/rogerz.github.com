---
layout: post
title: "How to use commander.js"
comments: true
categories: nodejs
published: true

---

# program

`program = require 'commander'` brings you the `Command` object. To make it work, use `option` to add options to it and `parse` to parse the args.

```
program
    .version('0.0.1')
    .option('-C, --chdir <path>', 'change the working directory')
    .option('-c, --config <path>', 'set config path. defaults to ./deploy.conf')
    .option('-T, --no-tests', 'ignore test hook');

program.parse(process.argv);
```

After parse, you'll get the value of options as program's properties, and rest of the args in one array.

`cmd -C path --config conf arg1 arg2` results in

```
program = {
  args: ["arg1", "arg2"],
  chdir: "path",
  config: "conf",
  tests: true
  ...
}
```
Then it is time to process these content.

# commands

`program` refers to the cli tool itself and you may extend some action for it just like `git commit`, `git push`. This is done by creating `command` under `program`

```
program
  .command("push [remote] [branch]")
  .option(...)
  .option(...)
  .action(function(remote, branch){})
```

The `action()` register a callback to be invoked when `program.parse()` is called. The args applied to the callback will be the ones defined in `command`.