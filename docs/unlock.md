---
layout: default
title: Unlock
nav_exclude: true
---


<p id="success">
<img src="../images/thanks_for_paying.gif"/>
Thanks for paying! Your support is deeply appreciated!
</p>

<p id="failure">
Sorry an error occurred with the purchase

Make sure you accessed this page from the Udemy platform

If you continue to have issues please email me at apc1993@gmail.com
</p>

<button onclick="eraseCookie('purchased')">Reset cookie</button>

<script> setCookieIfReferrer('purchased', true, 'https://www.udemy.com/') </script>
