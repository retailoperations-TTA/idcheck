# TTA ID Check

A license and state ID scanner for The Travel Agency's four stores: SoHo, Union Square, Downtown Brooklyn and 5th Ave.

Plug a 2D barcode scanner that reads PDF417 into the computer, open the page, and scan the back of the ID. The page shows CLEARED, UNDER 21, EXPIRED, CHECK ID or BANNED. It reads every US state, DC, US territory and Canadian province.

## Passports

Scan the 2 lines of text at the bottom of the passport photo page (the machine-readable zone). ID Check also reads the 3-line text on passport cards and foreign ID cards. It reads the name, birth date, expiration date, nationality and passport number, and checks the built-in check digits, so a misread asks for a rescan instead of clearing anyone. Passport scans count, save visits and check bans the same way license scans do.

The scanner has to read OCR-B text, which is off by default. On a Zebra scanner (CS6080 and others), open the OCR Programming chapter of its Product Reference Guide and scan **Enable OCR-B**, then **OCR-B Passport** (or **ICAO Travel Documents**, if the guide lists it). Licenses keep scanning normally. Test it by scanning a passport into Notepad: two lines starting with `P<` should come out.

Reports count passports under the passport country (for example "Passport, United States"). Passports don't show a home state, so they're left out of "Out of state".

## Visits and bans

Every barcode scan is saved as a visit. Managers sign in with Google to:

- **Visits**: pick a day and store to see who came in, by time, or find someone by last name. Open anyone's full visit history.
- **Ban**: from any visit, ban the person at all four stores with a reason and a note. The next time their ID is scanned anywhere, the screen shows BANNED.
- **Bans**: see who is banned, lift a ban, or add a ban by name and birth date for someone who isn't in Visits.

Bans match the ID number and also the name and birth date, so a new license number still gets caught. Store computers sign a manager out after 15 idle minutes.

Signing out is a full sign-out, since store computers are shared:

- **Google**: clicking Sign out also signs the browser out of every Google account (a small Google window opens for a few seconds, so allow pop-ups for the page). After the 15-minute automatic sign-out, the next Sign in click finishes the Google sign-out first. Either way, getting back in takes a password.
- **Saved data**: the manager data Firebase kept in the browser (visit names, bans, reports) is wiped once every scan has synced. If another ID Check tab is open, the wipe waits until the page is reloaded with only one tab open.
- **Sign in** always asks which Google account to use.

## Privacy

- **Visits** (managers only): name, age, sex, height, eye color, issuing state or passport country, last 4 of the ID or passport number, store, station, time and result. Kept until deleted.
- **Scan counts** (Reports): age, result, reason, issuing state, store, station and time. No names.
- **Home ZIP counts** (Reports map): how many scans came from each 5-digit ZIP code, per store per day. The ZIP is never saved with a scan or a visit.
- **Never saved**: birth dates, full ID numbers and street addresses. Visits and bans hold a one-way fingerprint of the ID number and of name plus birth date, so they can be matched later without storing the numbers.
- Store computers can check whether a scanned ID is banned, but can't list the ban list or read names.

New York's rule 9 NYCRR 123.10(d)(5) limits recording customer personal information without consent, beyond what is typically collected in a sale. Confirm this use with TTA compliance.

## Setup

1. Create a Firebase project. In Authentication, turn on **Anonymous** and **Google** sign-in, and add `retailoperations-tta.github.io` under Authorized domains.
2. Create a Firestore database and paste `firestore.rules` into its Rules tab.
3. Paste the web app config into `FIREBASE_CONFIG` near the top of the second script in `index.html`. Keep `MANAGER_EMAILS` the same as the list in the rules.
4. On each store computer, open the page and pick the store. A link like `?store=usq` picks it automatically.

## Reports history and map

Reports can show the last 90 days or all time. SoHo's history from Aug 2025 to Nov 8, 2026 was imported from the old IDVisor scanner as daily totals only (no names or ID numbers). That export skips some days.

The map panel shows where customers live: a circle per home ZIP code in New York City, and every scan shaded by the state on the ID. `geo.json` holds the map shapes only (borough outlines, ZIP code positions from the `zipcodes` package, US states from `us-atlas`). It has no customer data.
