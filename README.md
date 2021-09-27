# Instagram, Privacy, and iFrames

This blog post is about a side-channel vulnerability I found back in 2018. At the time there was no better name for the technique but today they are commonly referred to as XSLeaks (Cross-Site Leaks).

Instagram is one of the largest social networks on our planet. The platform reported one billion monthly active users in June 2018.
With privacy taking a major role in Facebook, I decided to check if it was possible for me to bypass Instagram's privacy features.

On Instagram, you can have either a public or private account.
When your account is private, only your profile picture and name are public. The rest of your posts and followers lists can not be seen.
Users can request to follow you, and only if you allow them they will be granted access to your profile which you can revoke at any time.

Ironically, the people using those privacy features was the key to successfully exploiting the bug. I notice that Instagram was rendering a number of iframe tags on public profiles. However, when the account was private, Instagram would render a different page showing only the profile picture, name, and a message saying this account is private. no iframes tags.

The thing is, cross-origin sites are allowed to know the number of iframe tags present in Window objects they have a reference to. This could be achieved by simply using

```js
var popup = window.open("https://instagram.com/...")
```

The "popup" variable will reference the Instagram window object and therefore will leak the number of iframe elements that exist in it.

```js
popup.frames.length // 1 or more for public profiles
popup.frames.length // 0 for private profiles
```

This means any website you visit could "get to know you better" by leaking which people/brands you currently follow on Instagram. I think this is especially dangerous for profiles that promote a specific thing, like protests or other political events since knowing whether you follow them or not reflects a lot of information.

All of this could have been done even if all the parties were using a private Instagram account.

I responsibly disclosed this issue to the Facebook security team on 12 December 2018.
Facebook awarded me by adding me to their wall of fame (https://www.facebook.com/whitehat/thanks) and paid me a $1,000 cash bounty.
