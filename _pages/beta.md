---
permalink: /beta/
title: "Test Snowmigos"
layout: single
author_profile: false
noindex: true
sitemap: false
# The three links this page hands out. Empty strings render the "not open yet" state,
# so the page can go live before the TestFlight group and the Google Group exist.
#   testflight_url    — the TestFlight public link for the external testers group
#   android_group_url — the Google Group people join to be let into the closed test
#   android_opt_in_url — the Play opt-in link shown once they are in the group
testflight_url: ""
android_group_url: ""
android_opt_in_url: ""
---

<div class="panel" markdown="1">

![Snowmigos](/assets/images/snowmigos-icon.svg){: .panel__icon}

Snowmigos
{: .panel__title}

Thanks for testing. It shows you which run or lift everyone in your group is on, so pick the
phone you ski with — that is the one we need the app on.
{: .panel__lede}

<div class="beta" markdown="1">

<section class="beta__platform" id="beta-ios" markdown="1">

### iPhone

{% if page.testflight_url != "" %}
Install **TestFlight** from the App Store first, then open the invite:

<a class="btn btn--primary btn--large" href="{{ page.testflight_url }}">Get the beta on TestFlight</a>

You will need an Apple Account, which you already have if you use the App Store. Nothing is
added to any account of ours, and you can leave at any time from inside TestFlight.
{: .beta__note}
{% else %}
Not open yet — the TestFlight link lands here as soon as Apple has looked at the first build.
{: .beta__note}
{% endif %}

</section>

<section class="beta__platform" id="beta-android" markdown="1">

### Android

{% if page.android_group_url != "" %}
Two steps, and both need the **same Google account your phone uses for the Play Store**.

1. [Join the testers group]({{ page.android_group_url }}) — this is what lets Google give you
   the app.
2. [Open the beta on Google Play]({{ page.android_opt_in_url }}) and tap **Become a tester**,
   then install.

If Play says the app is not available, you are almost certainly signed in with a different
account from the one that joined the group.
{: .beta__note}
{% else %}
Not open yet — the Google Play links land here shortly.
{: .beta__note}
{% endif %}

</section>

</div>

Found something odd, or the battery vanished? Tell us what you were doing at the time:
[support@kuamka.com](mailto:support@kuamka.com). Rough notes are genuinely useful — "lift
said skiing for a couple of minutes near the top of Signal" is plenty.
{: .beta__ask}

<img src="/assets/images/beta-qr.svg" alt="QR code for snowmigos.com/beta" class="beta__qr" width="160" height="160">

Share this page: **snowmigos.com/beta**
{: .beta__share}

[What the app does with your location](/privacy/)
{: .panel__footnote}

</div>

<script>
  // Put the section matching the phone you are holding first. Both are always present —
  // this only reorders, so the page is complete without JavaScript and on a desktop, and
  // a wrong guess costs a scroll rather than the link.
  (function () {
    if (!/Android/.test(navigator.userAgent)) { return; }
    var wrap = document.querySelector('.beta');
    var android = document.getElementById('beta-android');
    if (wrap && android) { wrap.insertBefore(android, wrap.firstChild); }
  })();
</script>
