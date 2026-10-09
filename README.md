# MagicMirror² — Phoenix Timezone Setup

How to get the time right on a MagicMirror² smart mirror in Phoenix, AZ.

**The one Phoenix gotcha:** Arizona does not observe daylight saving time.
Use `America/Phoenix` (UTC-7 year-round). Do **not** use `America/Denver` —
Denver does DST and your mirror will be an hour off for half the year.

## 1. Set the system timezone

MagicMirror² (and the Electron browser behind it) reads the operating
system's timezone, so getting the OS right is 90% of the job.

```bash
sudo timedatectl set-timezone America/Phoenix
```

Verify it took:

```bash
timedatectl
date
```

You should see `Time zone: America/Phoenix (MST, -0700)` and the correct
local time. If `timedatectl` isn't available, use:

```bash
sudo raspi-config
# → Localisation Options → Timezone → US → Arizona / Phoenix
```

or:

```bash
sudo dpkg-reconfigure tzdata
```

## 2. Set 12-hour time in MagicMirror's config

Open `~/MagicMirror/config/config.js` and make sure the top-level setting
reads:

```js
timeFormat: 12,
```

(Use `24` if you prefer 24-hour time.)

The default `clock` module picks up the system timezone automatically —
no per-module timezone setting needed.

## 3. Calendar module (if you use one)

The calendar module also follows the system timezone. Useful settings in
its config block:

```js
{
    module: "calendar",
    position: "top_left",
    config: {
        timeFormat: "absolute",
        getRelative: 6,
        urgency: 7,
    }
},
```

## 4. Restart MagicMirror

```bash
pm2 restart MagicMirror
```

(Or just reboot the Pi: `sudo reboot`.)

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| Off by one hour | Timezone is `America/Denver`, not `America/Phoenix` | Re-run step 1 |
| Showing UTC | Timezone change didn't apply | Reboot, then re-check `timedatectl` |
| Right time, wrong format | `timeFormat` in config.js | Set `12` or `24` (step 2) |
| Correct after reboot, wrong later | NTP not syncing | `sudo timedatectl set-ntp true` |

## Quick reference

- Timezone string: `America/Phoenix`
- UTC offset: -07:00, year-round (no DST in Arizona)
- Config file: `~/MagicMirror/config/config.js`
