---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Building cross browser extensions - My experience"
summary: "Browser APIs have so much potential to elevate the user experience to new heights"
authors: ["Prakhar Gurunani"]
tags: ["Chrome", "Firefox", "Web", "Browser", "JavaScript", "API", "Extension"]
categories: ["Tech"]
date: 2022-01-28
lastmod: 2022-01-28
featured: true
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: ["g-meet-auto-join"]
---

_Browser extensions have been very useful for me as a developer. The underlying browser APIs is what powers these extensions. I have been tinkering around with these 'instant apps' for a while, and have shared my experience with building an extension. Do check it out [here](https://github.com/FirePing32/G-Meet-Auto-Join/)._

## Story

One of my friends suggested if I have some script/bot which can attend online classes on his behalf. The idea was good. There were some extensions in the web store which already did this, but with somewhat negative reviews and in-app purchases, they weren't worth using.

## Chrome APIs

`chrome.alarms` is a super useful API, which you can say, is similar to your phone alarm. It fires an event at the specified time, and we can do whatever we want - run scripts, manipulate DOM and what not. So the idea was quite simple: set an alarm with `chrome.alarms.create`, and use vanilla JS to automatically open the meeting URL in a new tab when the alarm is triggered.

This can be a productivity tool for some, but not for college grads xD.

![What my friends think](https://i.imgur.com/vZoTo6A.jpg)

Most of the APIs used by Chrome extensions aren't available for use in the developer console, as this can be a huge security concern, allowing malicious scripts to take full control over the browser, and thus breaching user privacy.

## Cross browser compatibility

I use Firefox dev edition (`v97.0b5`) as my default browser (Chrome sucks ¬‿¬). After reading a few blog posts, it was somewhat clear that chrome extensions do work in firefox with minor changes, mainly API changes. All native Chrome APIs need to be changed for them to work in Firefox (and some other browsers).

> Chrome, Firefox, and some other browsers don't differ much in the underlying
browser APIs. Chrome uses `chrome.<API_NAME>` to call it's native APIs, whereas Firefox and Opera use `browser.<API_NAME>` to do so.

`manifest.json` is quite different for both the browsers. One good thing about Firefox is that it allows to set the addon ID beforehand, whereas Chrome randomly assigns a long string of characters when submitted in the Chrome web store.

Browsers get very regular updates, with frequent API changes. This can cause an extension to not work in certain versions of the browser. Firefox has an amazing [tool](https://www.extensiontest.com/) to test the compatibility of the extension, giving very detailed insights and minimum requirements for the extension to work correctly.

AFAIK, Chrome does not have any such tool for testing extension compatibility.

MacOS has the habit of creating `.DS_Store` files everywhere, which often get committed to GitHub. Firefox does not like these files getting bundled with the extension, hence they have to be removed, which is a pain the ass. This command removes them recursively -

```bash
find <dir_name> -name .DS_Store -delete
```

## UX Changes

The extension was working perfectly fine in Chrome but things broke when I tested it with Firefox.

- Initially, alerts were displayed using `window.alert()`, and it was working normally. Chrome display alerts from extensions in the center of the window, but Firefox does not do so, and instead displays them in the extension popup itself. When a alert window appears, Firefox forcefully expands the extension popup width if it is less than the width of the alert window, thus leading to an unusual whitespace. This was a quite weird behavior, as normal alert windows in Firefox are itself smaller than those created by the extension.

- Alert windows cannot be resized. So a fix for this is to use a custom [modal](https://getbootstrap.com/docs/4.0/components/modal/). Libraries like [SweetAlert2](https://sweetalert2.github.io/) are easier to implement.

- I use a [custom theme](https://github.com/FirePing32/dotfiles/blob/master/userChrome.css) for Firefox, and it uses a different font size to match my requirements. For users like me, browser extensions having fixed font size may not be comfortable to view. This behavior was noticeable when I disabled `userChrome.css`. A good way to handle this would be to use units like `rem` instead of `px`. `rem` sets the font size relative to the HTML document. An another approach would be to use `em`, which sets the size relative to the parent element. Both of them work perfectly fine.

## Differences between mv2 and mv3

Chrome migrated to manifest v3 in Nov 2020, bringing some breaking changes and introducing new APIs. This move will make many extensions obsolete (to some extent), and they will have to be ported to mv3 soon, as Chrome will be [dropping support](https://developer.chrome.com/docs/extensions/mv3/mv2-sunset/) for mv2 extensions in June this year. See all the changes [here](https://developer.chrome.com/docs/extensions/mv3/intro/mv3-migration/).

Earlier in mv2, the use of remotely hosted code was allowed, and the scripts could be changed anytime without informing the user, thus posing a security concern. Mv3 prohibited the use of remotely hosted code, thus the external scripts will now have to be bundled with the extension and will be verified by the Chrome dev team, which will enhance user privacy and ensure that the extensions don't misuse user data or perform malicious actions. The use of external CSS is still allowed in mv3.

### Content scripts

Chrome changed the way how content scripts are executed in mv3. `chrome.tabs.executeScript()` has been changed to `chrome.scripting.executeScript()`. Thus, now an additional permission `scripting` needs to be specified in the manifest. But as of now, it does not work as expected due to a [bug](https://crbug.com/1191971).

- Mv2 checks for the `pendingUrl` parameter, which mv3 does not seem to be checking. Hence, `chrome.scripting` executes the script on the new tab itself instead of waiting for the URL to load, and throws the [internal page](https://stackoverflow.com/a/45659562) error.
- An optimal solution would be to wait for the page to load completely until all the DOM elements are visible. This can be achieved using the `tabs.onUpdated` API. The `addListener` method receives a event whenever the page is updated. The response contains the status of the page, which can be used in an if/else loop to execute the script.

```javascript
chrome.tabs.onUpdated.addListener((tabId, changeInfo, tab) => {
    if (changeInfo.status === 'complete') {
      chrome.tabs.query({url: url}, function(tabs) {
          try {
              chrome.scripting.executeScript({
                  target: { tabId: tabs[0].id },
                  files: ["js/script.js"]
              })
          }
          catch(err) {
              console.log(err)
          }
      })
    }
});
```

## Mv3 in Firefox ?

Firefox still uses mv2 and is in progress to move to mv3. But Firefox's mv3 maybe somewhat different as compared to Chrome, as there are chances that Mozilla will retain some APIs of Chrome's mv2, apart from incorporating new APIs. [Read more](https://blog.mozilla.org/addons/2021/05/27/manifest-v3-update/).

Due to some API differences between mv2 and mv3, porting the extension from Chrome to Firefox wasn't a piece of cake. Due to the breaking changes, two separate source codes will need to be maintained. This might delay the development process for the upcoming releases until Firefox supports mv3.