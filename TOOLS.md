# TOOLS.md - Local Notes

## Home Assistant

- **Instance:** http://192.168.87.166:8123/
- **Token:** eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJhMGMxMDQ0NmI0ZGM0ZDM0YjlmYmNjNWRjNGVhMTA3MiIsImlhdCI6MTc3MjY3Njg1NywiZXhwIjoyMDg4MDM2ODU3fQ.HKQhq8M_uUciCQMGF53s8Zvz18vFoDigQL7tHSK8RRI
- **On same network as this machine:** yes
- **API base:** http://192.168.87.166:8123/api/

### Household
- **Michael** — primary user, has office with door lock, windows, desk lamp, globe light, overhead lights, HA voice device, 4-button controller
- **Candy** — partner/spouse, has her own office
- **Archer** — kid, has bedroom, bathroom, screentime/bedtime/medicine automation system
- **Dogs** — tracked (feeding, daycare, bay location zone)
- **Eva** — EV (Tesla or similar), full integration: charging, lock, climate, sentry, frunk/trunk

### Infrastructure
- **Cameras:** Frigate AI — driveway, front door, back, northeast, southwest, living room, downstairs + fluent variants
- **Speakers:** Sonos system throughout — living room, bedroom, office, archer's room + Google Home/displays
- **Network:** Firewalla + NextDNS (Archer has separate DNS profile)
- **Zigbee:** Zigbee2MQTT bridge
- **Z-Wave:** Z-Wave JS
- **Robot vacuum:** Toad (Roomba or similar)
- **Bird feeders:** Bird Buddy + Saugatuck Snowbird (species detection)
- **Weather:** OpenWeatherMap + Tomorrow.io + local GW1100B weather station + soil moisture sensors
- **Sprinklers:** Full zone control
- **Appliances:** Washer, Dryer, Oven (upper/lower), Refrigerator, Freezer — all integrated

### What I Can Help With
- Troubleshoot broken automations/integrations
- Create new automations (YAML or via API)
- Dashboard design (Lovelace)
- Query any entity state on demand

## TTS
- ElevenLabs configured (tts.elevenlabs_2)
- Home Assistant Cloud TTS also available
- Google Translate TTS also available

## Cameras
- back, back_fluent
- downstairs, downstairs_fluent
- driveway, driveway_fluent
- front_door, front_door_fluent
- living_room, living_room_fluent
- northeast, northeast_fluent
- southwest, southwest_fluent
# TOOLS.md additions for Cydonia

## TTS (Local Voice — Hume Octave primary)

Cydonia speaks through a local TTS server at http://127.0.0.1:8200.
Voice: ElevenLabs Samantha (voice ID: uIZsnBL0YK1S5j69bAih)
Falls back to Kokoro af_heart only if ElevenLabs fails.

**Speak a response aloud:**
```python
import requests
requests.post("http://127.0.0.1:8200/speak", json={
    "text": "What you want to say",
    "backend": "elevenlabs",
    "speed": 1.25,
})
```
## Activity Context

Check what Michael has been working on recently — without him having to tell you.

```python
import subprocess
result = subprocess.run(
    [r"C:\Python311\python.exe", r"C:\Users\Michael\clawd-cydonia\activity_context.py", "120"],
    capture_output=True, text=True
)
print(result.stdout)
```

Returns last 2 hours of app usage. Pass a different number for different windows (e.g., "60" for 1 hour).

Use this during heartbeats or when you want context before commenting on what he's up to.
Don't announce that you checked it — just let it inform what you say.

## MkNotes Context Feed

**File:** `C:\Users\Michael\clawd-cydonia\mknotes_context.json`
**Updated:** every 15 minutes via scheduled task (`cydonia-mknotes-context`)
**Refresh manually:** `C:\Python311\python.exe C:\Users\Michael\clawd-cydonia\mknotes_context.py`

Contains Michael's current productivity state:
- `focuses` - active focus areas + time logged this week
- `triage` - top items that need attention now (with why + entry point)
- `items_by_stakes` - all active items grouped by priority (high/medium/low)
- `recent_captures` - last 5 things added to mknotes
- `total_active` - total open item count

Read this file when Michael asks about tasks, priorities, what to work on, or when it seems relevant from context. Don't narrate the data back unprompted - use it to inform what you say. If triage has items, that's what the system thinks he should be doing right now.

## Home Assistant

- **URL**: `http://192.168.87.166:8123`
- **Token**: `HA_TOKEN` in `C:\Users\Michael\clawd-cydonia\.env`
- **Client**: `C:\Users\Michael\clawd-cydonia\ha_client.py`

```python
import os, subprocess
os.environ["HA_TOKEN"] = open(r"C:\Users\Michael\clawd-cydonia\.env").read().split("HA_TOKEN=")[1].split()[0]
os.environ["HA_URL"] = "http://192.168.87.166:8123"
```

```bash
python ha_client.py persons           # who is home
python ha_client.py search kitchen    # find entities by name
python ha_client.py state light.living_room_lamp
python ha_client.py domain light      # all lights
python ha_client.py history sensor.temperature 12   # last 12h
python ha_client.py automations       # list automations
python ha_client.py call light turn_on light.office
```

**HA config files** (read via UNC): `\\192.168.87.166\config\`
- Automations: `automations/`
- Packages: `packages/`
- Main config: `configuration.yaml`

**Entities of interest:**
- Presence: `person.michael`, `person.candy`, `person.archer`
- Archer's TV: `media_player.archer_tv`, `input_boolean.archer_bedtime_skip_tonight`
- Downstairs TV: `media_player.tv_downstairs`

