---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2020-02-28 21:08:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/37.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-03-31 21:54:51'
parent: 0
password: ''
slug: '132'
status: publish
tags: [OSX, Swift, Xcode]
template: ''
title: UIKit, AppKit等类的进程处理
type: post
---
## Overview

The Main Thread Checker is a standalone tool for Swift and C languages that detects invalid usage of AppKit, UIKit, and other APIs on a background thread. Updating UI on a thread other than the main thread is a common mistake that can result in missed UI updates, visual defects, data corruptions, and crashes.

<!--more-->

### How the Main Thread Checker Works

At app launch, the Main Thread Checker dynamically replaces the implementations of methods that should only be called on the main thread with a version that prepends the check. Methods known to be safe for use on background threads are excluded from this check.

Note

Unlike other code diagnostic tools, the Main Thread Checker doesn't require recompilation, and can be used with existing binaries. You can run it on a macOS app without the Xcode debugger, such as on a continuous integration system, by injecting the dynamic library file located at `/Applications/Xcode.app/Contents/Developer/usr/lib/libMainThreadChecker.dylib`.

### Performance Impact

The performance impact of the Main Thread Checker is minimal, with a 1–2% CPU overhead and additional process launch time of <0.1 seconds.

Because of its minimal performance overhead, the Main Thread Checker is automatically enabled when you run your app with the Xcode debugger.

### Updating UI from a Completion Handler

Long-running tasks such as networking are often executed in the background, and provide a completion handler to signal completion. Attempting to read or update the UI from a completion handler may cause problems.

```swift
let task = URLSession.shared.dataTask(with: url) { (data, response, error) in
   if let data = data {      
      self.label.text = "\(data.count) bytes downloaded"
      // Error: label updated on background thread   
   }
}
task.resume()
```

#### Solution

Dispatch the call to update the label text to the main thread.

```swift
let task = URLSession.shared.dataTask(with: url) { (data, response, error) in
   if let data = data {
      DispatchQueue.main.async { // Correct
         self.label.text = "\(data.count) bytes downloaded"
      }
   }
}
task.resume()
```

