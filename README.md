hints for unicode-range
===============================

A proposal for CSS to let web developers give hints to the user agent about @font-face definitions using unicode-range

The use case: one use case for unicode-range is to define common / uncommon / rare / fallback (full font) ranges for large web fonts such as Chinese, Japanese or Korean languages. 

It would be useful to give hints about these ranges such that the user agent could:
 - use the fallback (full font) if it’s readily available
 - asynchronously download what might be needed in the future (e.g. the uncommon range and/or the full font)

#Strawman#
Taking a hint from the [rel=prefetch](https://developer.mozilla.org/en-US/docs/Web/HTTP/Link_prefetching_FAQ) and [rel=subresource](http://www.chromium.org/spdy/link-headers-and-server-hint/link-rel-subresource) ideas:

```
@font-face {
   font-family: 'Noto Sans Japanese';
   font-style: normal;
   font-weight: 400;
   src: url(//fonts.example.com/v3/NotoSansJPFull-Regular.woff2) format('woff2'),
        url(//fonts.example.com/v3/NotoSansJPFull-Regular.woff) format('woff'),
        rel(alternate);
 }

@font-face {
   font-family: 'Noto Sans Japanese';
   font-style: normal;
   font-weight: 400;
   src: url(//fonts.example.com/v3/NotoSansJPCommon-Regular.woff2) format('woff2'),
        url(//fonts.example.com/v3/NotoSansJPCommon-Regular.woff) format('woff'),
        rel(subresource);
   unicode-range: [... ranges for the common characters used in Japanese...]
 }

@font-face {
   font-family: 'Noto Sans Japanese';
   font-style: normal;
   font-weight: 400;
   src: url(//fonts.example.com/v3/NotoSansJPUncommon-Regular.woff2) format('woff2'),
        url(//fonts.example.com/v3/NotoSansJPUnCommon-Regular.woff) format('woff'),
        rel(prefetch);
   unicode-range: [... ranges for the less common characters in Japanese...]
 }

@font-face {
   font-family: 'Noto Sans Japanese';
   font-style: normal;
   font-weight: 400;
   src: url(//fonts.example.com/v3/NotoSansJPRare-Regular.woff2) format('woff2'),
        url(//fonts.example.com/v3/NotoSansJPRare-Regular.woff) format('woff');
   unicode-range: [... ranges for the rarely used Japanese characters...]
 }
```

##rel(alternate)##
This is to let the user agent know that NotoSansJPfull-regular can be used for any of the other Noto Sans Japanese 400 subsets: if it so happens that NotoSansJPfull-regular is in the browser cache, the browser should feel free to use it instead of looking at the unicode-ranges.

##rel(prefetch)##
This is to let the user agent know that a particular resource might be useful on subsequent page loads.

In our example, we use it to increase the likelihood that should the uncommon set be needed it would be readily available in the HTTP cache.

##rel(subresource)##
While rel(prefetch) provides a low-priority download of resources to be used on subsequent pages, rel(subresource) enables early loading of resources within the current page. Because the resource is intended for use within the current page, it must be loaded at high priority in order to be useful.

In our example, we communicate to the user agent that the common set is extremely likely to be used.
