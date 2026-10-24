---
permalink: /join/
title: "Join the trip on Snowmigos"
layout: single
author_profile: false
---

<div class="panel" markdown="1">

![Snowmigos](/assets/images/snowmigos-icon.svg){: .panel__icon}

A friend invited you to see each other on the mountain. Get the app, then tap the link again to join the trip.

Snowmigos is coming to the App Store and Google Play

<a href="#" id="sm-open" hidden>Already have the app? Open it</a>

[What the app does with your location](/privacy/)
{: .panel__footnote}

</div>

<script>
  // In-app browsers (WhatsApp, Instagram) don't fire Universal Links, so offer a
  // best-effort deep link back into the app using the token from the query string.
  (function () {
    var token = new URLSearchParams(window.location.search).get('token');
    if (!token) { return; }
    var link = document.getElementById('sm-open');
    link.href = 'snowmigos://join/' + encodeURIComponent(token);
    link.hidden = false;
  })();
</script>
