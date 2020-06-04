---
title: "npm和yarn的各种坑"
date: 2019-07-24T09:45:29+08:00
draft: true
tags: ["npm","yarn"]
categroies: ["IT技术"]
---

真是不知道什么人搞出npm，yarn这种东西，各种依赖各种坑各种错误各种翻墙,真是脱了裤子放屁浪费感情，浪费时间，咋办？很多装逼分子都用，你咋办？没办法，装呗。

``````shell
vagrant@homestead:~/Code/laravel-shop$ SASS_BINARY_SITE=http://npm.taobao.org/mirrors/node-sass yarn
yarn install v1.22.4
[1/4] Resolving packages...
[2/4] Fetching packages...
info fsevents@1.2.4: The platform "linux" is incompatible with this module.
info "fsevents@1.2.4" is an optional dependency and failed compatibility check. Excluding it from installation.
[3/4] Linking dependencies...
warning "laravel-mix > img-loader@3.0.1" has unmet peer dependency "imagemin@^5.0.0 || ^6.0.0".
error An unexpected error occurred: "EPROTO: protocol error, symlink '../acorn/bin/acorn' -> '/home/vagrant/Code/laravel-shop/node_modules/acorn-dynamic-import/node_modules/.bin/acorn'".
info If you think this is a bug, please open a bug report with the information provided in "/home/vagrant/Code/laravel-shop/yarn-error.log".
info Visit https://yarnpkg.com/en/docs/cli/install for documentation about this command.
``````

最后这样以下，才通过的。

```
npm install acorn --no-bin-links
```