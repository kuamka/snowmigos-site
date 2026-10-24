---
permalink: /join/
title: "Join the trip on Snowmigos"
layout: single
author_profile: false
---

<div style="text-align:center; max-width:420px; margin:0 auto;">
  <img src="/assets/images/snowmigos-icon.svg" alt="Snowmigos" width="96" height="96" style="border-radius:22px;" />

  <p style="margin-top:1rem;">A friend invited you to see each other on the mountain. Get the app, then tap the link again to join the trip.</p>

  <p style="margin-top:1.5rem;">Snowmigos is coming to the App Store and Google Play</p>

  <p style="margin-top:1rem;"><a href="#" id="sm-open" style="display:none;">Already have the app? Open it</a></p>
</div>

<script>
  // In-app browsers (WhatsApp, Instagram) don't fire Universal Links, so offer a
  // best-effort deep link back into the app using the token from the query string.
  (function () {
    var token = new URLSearchParams(window.location.search).get('token');
    if (!token) { return; }
    var open = document.getElementById('sm-open');
    open.href = 'snowmigos://join/' + encodeURIComponent(token);
    open.style.display = 'inline';
  })();
</script>
