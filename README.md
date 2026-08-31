# Forever — releases

Built `.ipa` files for **Forever**, published as a [SideStore](https://sidestore.io)
source so the app can be installed and updated from the phone, with no computer
and no cable.

The app's source code is **not** here — it lives in a private repository. This
repo holds only the built binary, its icon, and the manifest SideStore reads.

## Add the source

In SideStore → **Sources** → **+**, paste:

```
https://raw.githubusercontent.com/12shamali12/forever-releases/main/apps.json
```

Updates then appear in SideStore like any other app: one tap, no computer.

> **The source is live but has nothing to install until the first build
> publishes.** The `apps[0]` entry exists and says version `0.0.0`, with an
> empty `versions` array and a `downloadURL` that 404s. That is deliberate on
> both counts: the build workflow *edits* this entry rather than inventing one,
> because the name, description, icon and tint are hand-written and no build
> should be guessing at them — and an empty `versions` array is how the manifest
> says "nothing yet" honestly instead of advertising a download that is not
> there. Adding the source now is fine; the app becomes installable the moment a
> build lands.

## What Forever is

A private world for two people — the day, messages, plans, memories, promises,
letters and the rest, in one app. Unlike a fully offline app, it talks to a
server: the phone is a client, and everything real lives behind an account.

## What is and is not inside the binary

**Not inside:** every secret the server holds — the database, the JWT signing
key, the mail and S3 credentials, the VAPID private key, and (when it exists)
the APNs `.p8`. Those live in `fly secrets` and never reach a build.

**Inside, and public by design:** the compiled web bundle — every screen, all
the copy — the API's public URL, and the Google OAuth **client ID**, which is
the half of an OAuth pair intended to ship in clients.

So anyone who downloads this `.ipa` can read the app's interface. They cannot
read anything either of us wrote: that needs an account, and accounts are
invitation-only.

## Signing, and the 7-day thing

The `.ipa` here is **unsigned** on purpose. SideStore signs it on the phone with
your own Apple ID, which is what makes installing it possible without a paid
Apple Developer Program membership.

Apple gives a free account a **7-day** signature. SideStore renews it in the
background; the app stops opening if it goes more than a week without being able
to. That is Apple's rule for free accounts, not the app's.

The same rule is why **notifications do not work on a sideloaded build.** Apple
grants the `aps-environment` entitlement only to paid memberships, so this
binary deliberately ships without it: an `.ipa` that asks for an entitlement its
signature cannot grant is at best stripped and at worst refuses to install. The
push channel is built and waiting on the server side.
