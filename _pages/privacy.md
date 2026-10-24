---
permalink: /privacy/
title: "Snowmigos privacy policy"
layout: single
toc: true
toc_label: "On this page"
toc_sticky: true
author_profile: false
---

_Last updated: 24 October 2026_

This policy explains what personal data the Snowmigos mobile app collects, why we
collect it, who we share it with and the choices you have. Snowmigos helps a group of
friends see where everyone is on the mountain during a day's skiing or snowboarding.

## Who we are

Snowmigos is provided by Kuamka Ltd ("we", "us", "our"), a company registered in
England and Wales under company number 07961814, with its registered office
at 7 Brownlow St, Leamington Spa, CV32 5XH.

For anything in this policy, or to exercise any of your rights, contact us at
[privacy@kuamka.com](mailto:privacy@kuamka.com).

For the purposes of UK data protection law, Kuamka Ltd is the data controller for the
personal data described here.

## What this policy covers

This policy covers the Snowmigos app on iOS and Android, and the backend services that
support it. It does not cover any third-party service you reach from the app, or the
wider kuamka.com website.

It also covers this website, snowmigos.com, which is a handful of static pages. The
site sets no cookies, runs no analytics and asks you for nothing. It is served by
GitHub Pages, which as part of delivering it receives the usual request details,
including your IP address — see the [GitHub Privacy
Statement](https://docs.github.com/site-policy/privacy-policies/github-privacy-statement).

## How you sign in

Snowmigos does not ask for an email address, phone number, password or real name. When
you first open the app it creates an anonymous account for you on our backend. We do not
use Google, Apple or other social sign-in, and we do not know who you are beyond the
profile details you choose to add.

## What we collect and why

### Location

Location is the core of the app. With your permission, Snowmigos collects your device's
location while you are in a trip and sharing is switched on:

- **While you are using the app**, so the map can show where you and your crew are.
- **In the background**, while your phone is in your pocket, so your crew keeps seeing
  your position during the day. On Android this runs as a foreground service with a
  visible notification.

We collect precise location. From it the app derives related movement details — your
heading, speed, altitude (from the barometer), motion state (for example stationary,
walking or in a lift) and an estimate of your current activity (on a lift, on a run,
stopped). It also keeps a short trail of the named lifts, runs and points of interest
you have recently passed through, so your crew can follow where you have been.

Your latest position and activity are shared in real time with the other members of your
trip, and your most recent snapshot is stored so crew members who open the app a little
later can still see where you were.

You stay in control: you can pause sharing at any time from within the app, and you can
revoke the location permission in your device settings.

### Your profile

- **Display name** — the name you choose to show to your crew. There is no requirement to
  use your real name.
- **Profile photo** — optional. If you choose to set one, you can take a photo or pick one
  from your photo library. The image is resized on your device and stored on our backend
  so your crew can see your avatar on the map.

### Trip information

When you create or join a trip we store the trip and your membership of it: a trip
identifier, the join token used to invite others, the resort, and the times the trip was
created and that you joined.

### Identifiers

- A random device identifier generated on your device the first time you open the app,
  used as your stable identity across trips.
- An anonymous account identifier issued by our backend authentication service.

Neither identifies you personally on its own.

### Push notifications

With your permission, we register a push token for your device so we can send silent
"wake" notifications. These prompt the app to refresh your crew's positions in the
background. They carry no message content and are not used for marketing.

### Optional analytics

Analytics are **off by default**. If you choose to turn them on, we use
[PostHog](https://posthog.com) to understand how the app is used so we can improve it. We
have configured analytics to minimise data:

- A separate anonymous identifier is used, distinct from your device identifier.
- Only a fixed set of product events is sent (for example, app opened, trip created,
  sharing paused). No free-form text, no screen recording and no automatic capture of
  your activity.
- Approximate location lookup from network address is disabled.
- Analytics data is processed in the European Union.

You can turn analytics off again at any time from within the app, and we stop sending
events.

## What we do not collect

We do not collect your email address, phone number, password, contacts, calendar,
messages, browsing history or any payment information. Snowmigos contains no advertising
and no third-party advertising or tracking software. We do not sell your personal data.

## Lawful bases

Under UK GDPR we rely on the following lawful bases:

- **To provide the service you have asked for** — creating your account, running your
  trips and sharing your location and activity with your crew while sharing is on. This
  processing is necessary to deliver the app's core function, which you choose to use.
- **Consent** — for optional analytics, and for the device permissions (location, camera,
  photos, notifications) that you grant. You can withdraw consent at any time by turning
  the feature off or revoking the permission in your device settings.
- **Our legitimate interests** — keeping the service secure and working correctly, and
  understanding and improving the app where you have opted in to analytics.

## Who we share data with

**Your crew.** The whole point of the app is to share your live position, activity and
profile with the other members of any trip you join. Only members of the same trip can
see your data, and only while you are sharing.

**Our service providers (sub-processors).** We use a small number of trusted providers to
run the app. They process data on our instructions:

| Provider | Purpose | Data involved |
|----------|---------|---------------|
| Supabase | Backend: accounts, trips, real-time position sharing, profile photo storage, push delivery | Location and activity, display name, profile photo, identifiers, trip data, push token |
| PostHog | Optional product analytics (only if you opt in), processed in the EU | Anonymous analytics identifier and product events |
| Google Maps (Android) | Map rendering on Android devices | Map display data on your device |
| Apple Maps (iOS) | Map rendering on iOS devices | Map display data on your device |

We may also disclose data if required by law, or to protect our rights, users or the
safety of others.

## Where your data is processed

Our backend is hosted by Supabase in the eu-north-1 region. Optional analytics
are processed by PostHog in the European Union. Where data is transferred outside the UK,
we rely on appropriate safeguards such as the UK's international data transfer agreement
or adequacy regulations.

## How long we keep it

- **Live position and activity** — only your most recent snapshot is kept on the backend;
  each update overwrites the previous one.
- **Trips** — a trip exists while at least one person is in it. When the last member
  leaves, the trip and its position and membership data are automatically deleted around
  four weeks later. Rejoining before then keeps the trip.
- **Your profile** — your display name and profile photo are kept until you change them or
  ask us to delete them. They are not removed automatically when you stop using the app. A
  previous photo is deleted when you set a new one.
- **Analytics** — retained by PostHog in line with their retention settings (up to 90 days
  by default), and removed if you withdraw consent.

Data stored on your device (your profile, preferences and session) remains until you clear
the app's data or uninstall it.

## Deleting your data

The quickest way is in the app: open **Settings**, scroll to **Danger zone** and tap
**Delete my data**. This removes your trip membership and status, your display name and
profile photo, and your registered push token from our backend, and resets the app to a
fresh, unconnected state — no email needed, no waiting.

Uninstalling Snowmigos separately removes the data held on your device (your local
profile, preferences and session), but does not by itself delete anything from our
backend — use the in-app button first if you still have the app installed.

If you no longer have the app installed, email
[privacy@kuamka.com](mailto:privacy@kuamka.com) and we will erase your backend data,
subject to any legal obligations to retain it.

## Your rights

Under UK data protection law you have the right to access, correct, delete or restrict
our use of your personal data, to object to processing, to data portability, and to
withdraw consent at any time. To exercise any of these, contact
[privacy@kuamka.com](mailto:privacy@kuamka.com).

You also have the right to complain to the Information Commissioner's Office (ICO), the
UK's data protection regulator, at [ico.org.uk](https://ico.org.uk).

## Children

Snowmigos is not intended for children under 13, and we do not knowingly collect
personal data from anyone under 13. If you believe a child has provided us with personal
data, contact [privacy@kuamka.com](mailto:privacy@kuamka.com) and we will delete it.

## How we protect your data

Data is sent over encrypted connections (HTTPS/TLS). Access to your trip's live data is
restricted to members of that trip, enforced by access rules on our backend. Your account
session is held in your device's secure storage. We do not currently use end-to-end
encryption, which means your service providers process your data to deliver the app.

## Device permissions

The app asks only for the permissions it needs, and only when the relevant feature is
used:

| Permission | Why it is used |
|------------|----------------|
| Location (while using the app and in the background) | Show your position to your crew and keep it updated while your phone is in your pocket |
| Motion and activity | Tell skiing, riding a lift and resting apart, to place you more accurately on the mountain |
| Camera | Scan a QR code to join a trip, and optionally take a profile photo |
| Photos | Optionally choose a profile photo from your library |
| Notifications | Send silent updates that refresh your crew's positions |

You can change any of these in your device settings at any time. Turning off location
stops position sharing.

## Changes to this policy

We may update this policy from time to time. When we do, we will change the date at the
top of this page. Significant changes will be highlighted in the app.

## Contact us

Kuamka Ltd
7 Brownlow St, Leamington Spa, CV32 5XH
[privacy@kuamka.com](mailto:privacy@kuamka.com)
