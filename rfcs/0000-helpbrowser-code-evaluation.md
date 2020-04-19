- Title: Help Browser(s) Code Evaluation and WebSockets
- Date proposed: 2020-19-04
- RFC PR: https://github.com/supercollider/rfcs/pull/0000 **update this number after RFC PR has been filed**

# Summary

Assessing and discussing the status of Help Browser implementations, especially about code evaluation, also regarding WebSockets.

I made a pull request (#4883) that has all my proposed fixes/changes, which I explain below.

Summary:
- rely on javascript for code selection in webviews: webviews' clients (scide or sclang) get code from the runJavaScript callback. This means WebView shouldn't have its own implementation of anything "interpret".
- use QcWebView inside the HelpBrowser widget, pretty much as it is used in the HelpBrowser sc class. Let's put all our webviews in one place.
- implement an additional "copy to clipboard" button in helpfiles. Could be helpful if you are not running scide
- avoid WebSockets for now, until there are more plans for them.

# Motivation

We have currently three ways of accessing the Help Browser:
- The Help Browser Widget
- HelpBrowser class (built on QWebView)
- QWebView class (see #4000)
- (well, you can also open an external browser and find HelpDocs on the internet)

And two communication methods between sc(lang/ide) and them:
- Javascript Functions/Callbacks
- WebSockets

This RFC is to collect thoughts and issues with current implementations and to propose and discuss changes and fixes, explaining my pull request #4883.

## Shall we drop webview code execution? (discussion at #4000)
- duplication: we need to maintain two separate code selection mechanisms, one for Documents and the other for WebViews.
- "ideology": SC is not a browser, but Max/PD have interactive help systems
- this feature is felt as very important by users (especially for teachers and beginners?)

Proposals/ideas:
- drop code execution
- add a copy button to help pages' code examples
- fix issues and keep going
- invest on WebSockets

My personal opinion is that this feature provides a nicer experience when browsing documentation in scide. It's a quick way of hearing what a class does. Copy/paste provides almost the same experience, plus the option to save code, in a way that I guess would be more consistent across (non-scide) clients. But I think it's quite straightforward to both add a "Copy to Clipboard" button for every code-block, and still keep code evaluation in javascript for sc-ide and any other client that want to support it. I also think WebSockets would be overkill just for this purposes, although they would be very useful for others.

# Specification

## Code Evaluation Issues
- #4542 (shift-enter doesn't eval)
This is because Shift-Enter keypresses are eaten by the web page's input field.
- #4000 (can't evaluate code)
As far as I see, javascript callbacks implementation for WebView is all broken.
- #656 (step forward doesn't work)
This requires a way to send this preference to the web pages (not addressed in this RFC/PR)

## Duplication
Code duplication actually affects only text selection. Evaluation is performed in three places:
- The standard Document: ok.
- HelpBrowser Widget's evaluateSelection: call javascript function to select code, get that code in a callback, send it to Main::scProcess()->evaluateCode(). Keypresses are shortcuts, defined in settings.
- WebView roughly implements the same mechanism, but in two ways:
  - through a keyPressEvent (currently never firing, eaten by web page's inputs), natively getting selected text without any javascript calls, and then sending it back to client by spawning an interpret signal.
  - through runJavaScript, gets the selection the same way as HelpBrowser Widget. Keypresses are then handled in sclang.

The main problem with these implementations IMO, is that QWebEngineView doesn't seem to send KeyPress Events, but ShortcutOverrides (even for simple keypresses like 'a'). This makes WebView's approach fail at the very beginning, keypress callbacks being never called.
Furthermore, it proved to be tricky to get keypresses like Shift-Enter (but AFAIK only this one) when focusing on an input field or textarea in the WebView.

I would streamline this process like this:
- WebView shouldn't have its own interpret mechanism. This functionality should depend on the rendered page's content.
- Help pages implement two javascript functions to get either selected text/line or selection/region. Clients like HelpBrowser widget or class can call these functions and get back code as a string.
- onJsConsoleMessage signal should be used only to print out console messages

Then the problem is reduced to fix the KeyPress vs. ShortcutOverride issue.
Furthermore, we could have HelpBrowser Widget using QcWebView as well, so that we would have to solve the keypress issue in one place only.

## WebSockets:
A WebSocket is currently used only in the Help widget, to tell sc to call a javascript function (it looks like a workaround for the Shift+Enter issue). This should just be done by sc when the corresponding shortcut is pressed.

I think it's overkill to implement websockets where a simple javascript function/callback can (and actually does the) work. On the other hand, websocket support would be an useful feature to add to sc, just not maybe for the purpose discussed here (see #4534).
As for themes sharing with help docs (for HelpBrowser widget), my personal take is that it would be better to migrate to css themes, that could be shared directly with help pages, just reloading the page on changes (but I haven't look into this in detail yet).

## KeyPresses:
I'm not completely clear on this issue, I would benefit from some help from someone who is more expert in Qt. As far as I know it could also be an upstream "bug", or by design.
What we get:
- WebView gets only ShortcutOverride events, for every keypress, except the ones eaten by the webpage
- WebView->focusProxy() (the web page renderer) gets both a keypress and a shortcut override for every keypress.

What I'm doing:
- Send a KeyPress event from the renderer to WebView for each renderer's KeyPress.
- In WebView's event function, swallow keypress events to avoid duplicates in parent widgets. It looks like even if WebView doesn't get keypresses, eventual parents do (for example if you have a WebView in a View, that parent View would get all keypresses except the ones swallowed by the render, even if WebView doesnt! I admit I'm quite confused by this)
- Since in HelpBrowser Widget we would still get some duplicate shortcuts (like ctrl+shift+D ... god knows why), I add an extra event filter to swallow them

This is the best and simplest workaround I could come up with. I would be very happy if someone could take a look and maybe find a more elegant way.

## Results
- code execution works in both HelpBrowser class and Widget + in WebView, if the rendered webpage has selectLine()/selectRegion() javascript functions.
- keyDownAction works on WebView only when an url is set. It gets all events (including the ones swallowed by the renderer) without duplication
- WebView parent widgets also get all keystrokes, except the ones swallowed by the web renderer

## Copy Button:
I added a copy button to every code textarea in help files. Their look is debatable though, I aimed to keeping it minimal. I've used an icon from FontAwesome... is that even something we are ok with?

# Drawbacks

- Possible breaking change? WebView doesn't have an "interpret" signal anymore, since this functionality is achieved only through javascript callbacks. SC classes' interfaces are not required to change, though (and they are unchanged in my PR).
- Possible CMake ugliness: importing QcWebView in scide requires a switch to disable widget registration (which would pull in a lot of unnecessary things from QtCollider), or an extra WebView class to be wrapped in both HelpBrowser and WebView Widgets.

# Unresolved Questions

- I would benefit from help to best figure out the ShortcutOverride/KeyPress issue.
- javascript callback with QVariant results
- Copy button icon. It's from FontAwesome: they release under attribution requirement. How do we do that? (if we want to do that)
