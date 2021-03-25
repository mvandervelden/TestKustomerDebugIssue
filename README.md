# TestKustomerDebugIssue
Test project illustrating the issue with a crashing debugger when using the KustomerChat sdk 2.0.7 & 2.1.0

## Instructions:
- Clone the repo
- Run `pod install` to install the KustomerChat pod
- Open `TestKustomerDebugIssue.xcworkspace` in Xcode.
- Make sure the shared breakpoint on `ViewController.viewDidLoad` is enabled
- Run the app using the default scheme.
- When the breakpoint hits, try to use the lldb debugger, like `po self`.

Actual result:
> error: virtual filesystem overlay file '/Users/willjessop/Library/Developer/Xcode/DerivedData/KustomerChat-gazxbyilqfnjffggjihizkgmftvo/Build/Intermediates.noindex/ArchiveIntermediates/KustomerChat/IntermediateBuildFilesPath/KustomerChat.build/Release-iphonesimulator/KustomerChat.build/all-product-headers.yaml' not found
> 
> error: couldn't IRGen expression. Please check the above error messages for possible root causes.

Expected result:
- The debugger responds with a representation of `self`.

## Most likely cause & solution:
[link](https://bugs.swift.org/browse/SR-12783?focusedCommentId=56540&page=com.atlassian.jira.plugin.system.issuetabpanels%3Acomment-tabpanel#comment-56540)

> The problem here is with the binary Instabug framework that you are using. The .dSYM files that are generated from Swift code are not (yet) as portable as the ones generated from Clang code. The binary Swift module encode a hardcoded path to a yaml file that only exists on the original developer's machine. You should let them know that they need to compile their binary framework with -no-serialize-debugging-options if they are planning to distribute them to another machine.

### Related issue in Instabug that got solved this way
https://github.com/Instabug/Instabug-iOS/issues/368
