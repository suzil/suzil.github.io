---
layout: post
title:  "Create a Linux app when there is none"
date:   2020-03-05
---

You know what sucks? The lack of native Linux apps for cool software! That said,
over the years it has gotten better, but it still happens that I sometimes find
a cool piece of software that doesn't support Linux.

I tried using [Remember the Milk](https://www.rememberthemilk.com/) because I
got excited because they offer a native Linux app. But the UI felt really dated,
and navigating around the interface felt clumsy. I told myself, it's /2020/,
to-do apps are the go-to starter app for fancy new web frameworks like React,
there should be some nice UI alternative.

And there was! I found [TickTick](https://ticktick.com/) and it's beautiful. The
UI is so fantastic that you just use it, and intuitively know how it all works
as if by instinct. You press the checkbox, and oh! it marks the item as
complete! (This sounds silly but this was a two-click process with Remember the
Milk). Markdown just works for typing up some stuff under a task. It had a nice
flat aesthetic which I appreciated.

However, there is no native Linux app. They have a web app, but for a to-do app
which I have open every day, I wanted it to be a separate app from the browser
with its own icon and everything. But then I thought of
[Electron](https://www.electronjs.org/), which uses Chromium to make native apps
using web technologies, ie. HTML, CSS, and JavaScript. I trusted my guts and did
a quick search and sure enough I discovered
[Natifiefier](https://github.com/jiahaog/nativefier), a popular program to turn
any web page into a native app (including Linux!).

I'll repeat the steps I did to setup TickTick as a native desktop app for Linux
for myself on Gnome. Spoiler alert, but I'm super happy with the result.

## Setup TickTick as a native app on Ubuntu Gnome

First, download and install Nativefier.

```bash
$ sudo npm install -g nativefier
```

Now simply use nativefier to turn the TickTick web app into an executable app
on your platform.

```bash
$ nativefier --name TickTick ticktick.com
```

Edit the `package.json` file to have the correct program `name` of `TickTick`
instead of the name it automatically gave it.

```json
{"name":"TickTick","version":"1.0.0","description": "..."}
```

Move the resulting program folder to `/var/opt` for safe-keeping.

```bash
$ sudo mv TickTick-linux-x64/ /var/opt/
```

Create a softlink for the executable in `/var/opt/TickTick-linux-x64/`.

```bash
$ sudo ln -s /var/opt/TickTick-linux-x64/TickTick /usr/local/bin/ticktick
```

Now we're ready to create the desktop launch application. Create a new file at
`/usr/share/applications/ticktick.desktop` and paste in the following:

```
[Desktop Entry]
Type=Application
Encoding=UTF-8
Name=TickTick
Icon=/usr/share/icons/ticktick.png
Exec=/usr/local/bin/ticktick
Terminal=false
```

Notice the icon reference? I found a [nice icon with
transparency](https://assets.materialup.com/uploads/8763d323-a869-4f8d-8c06-313cbe791243/preview.png)
you can download to use. Just put it at the specified path above,
`/usr/share/icons/ticktick.png`.

Now you should see a launch application for TickTick with the right icon! It
behaves just as I would expect a native app to behave. I'm really happy with it.
(Psst! Click the screenshot to open it so that it's big and easier to see)

[![TickTick native Linux app screenshot](/assets/ticktick.png)](/assets/ticktick.png)
