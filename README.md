US-Disaster Prevention

A dark, smoked-glass weather map with live US National Weather Service alerts, county highlighting, hurricane forecasts, custom warning sounds, and optional Azure voice announcements.

Built with **Go · React · TypeScript · Leaflet · Wails**.

## Features

- State and county-equivalent boundaries for all 50 states and Washington, DC.
- Live NWS warnings, watches, advisories, and statements with official event colors and display priorities.
- County highlighting and exact alert polygons where available.
- Interactive county, alert, and storm details that close after 30 seconds.
- Hurricane and tropical storm positions, pressure, winds, movement, forecast tracks, uncertainty cones, and available wind fields from NHC.
- State, event, text, and tropical filters; lower-48, Alaska, and Hawaii navigation.
- Automatic updates every 60 seconds, with loading, error, and stale-data indicators.
- Animated map movement and automatic framing of announced alerts.
- Separate custom sounds for tornado warnings, severe thunderstorm warnings, and other event types.
- Optional Microsoft Azure text-to-speech.
- Optional automatic Facebook Page posts for selected newly issued warning and watch types, using a Page token encrypted for the current Windows account.
- Windows application icon, portable executable, and NSIS installer script.

## Requirements

- Windows 10 or 11, 64-bit.
- [Microsoft Edge WebView2 Runtime](https://developer.microsoft.com/microsoft-edge/webview2/).
- Internet access for current weather data and Azure speech.
- An Azure Speech resource only if you want voice announcements. The map and custom sounds work without Azure.

## Installation

Download the desired build from this repository's **Releases**, when published.

| Download | Use |
| --- | --- |
| `US-Disaster-Prevention-Alpha-v0.0.11-Setup.exe` | Install for your Windows account, with Start menu shortcuts and an optional desktop shortcut. |
| `US-Disaster-Prevention-Alpha-v0.0.11.exe` | Run the portable app directly. |

Close any running copy before updating. Go and Node.js are not needed to run the app. Boundaries and frontend assets are embedded; live data requires internet access.

Installed copies can be removed through **Windows Settings → Apps** or the Start menu uninstall shortcut. Saved settings are retained.

## Using the map

1. Open **Menu** to choose a state or switch between the lower 48, Alaska, and Hawaii.

### Facebook automatic warning and watch posts

Open **Menu → Facebook posting settings**, enter your Facebook Page ID and the Page `access_token` returned by Graph API Explorer, then choose **Verify Page**. Save the settings and turn on automatic posting. While the Windows app is open, each newly issued selected Tornado, Severe Thunderstorm, Flood/Flash Flood, or tropical/hurricane warning or watch opens its detail panel and zooms the live map to the affected area. The app captures its actual window as a PNG, including county highlighting, the exact NWS alert outline when supplied, radar when available, and the warning details panel. The screenshot is saved to `%USERPROFILE%\Pictures\Weather Warning` even when Facebook auto-posting is off. When enabled, that same screenshot is attached to a Facebook Page photo post with the matched counties and official NWS alert link in its caption. Keep the app restored and visible so Windows can capture its window. Startup-existing alerts and updates are not posted. The Page token is encrypted with Windows DPAPI and is never returned to the interface after saving. Use a Page access token with the required Page publishing permissions; the deprecated `publish_actions` permission is not used. Use **Remove saved Page token** to clear it. Meta token expiration or permission changes can stop posting; verify the Page again after refreshing credentials.
2. Filter the alert list by event, search text, or **Tropical / hurricane**.
3. Click a county to see its alerts, or an alert to read its description and instructions.
4. Select a tropical cyclone to view conditions, forecasts, and official NHC links.
5. Use **Locate on map** to frame the affected area.

Details close after **30 seconds**. A new selection restarts the countdown. Newly received matching alerts can open details automatically. Announcements display the associated alert and frame its available polygon or affected counties.

## Separate warning sounds

Open **Menu → Warning sounds**.

1. Enable **Play sounds for new alerts**.
2. Choose an event type, such as **Tornado Warning** or **Severe Thunderstorm Warning**.
3. Select a local audio file.
4. Adjust sound volume and use **Preview** to hear it.

Repeat for other event types. A **default sound** can cover events without a custom sound. Assigned files can be removed.

- Formats: MP3, WAV, OGG, and M4A; playback support depends on Windows codecs.
- Maximum file size: **5 MB**. Playback is limited to **30 seconds** per alert; short clips are recommended.
- Files are copied into local app storage and persist across restarts.
- Sounds work without Azure. With automatic voice enabled, the sound plays before speech in the same queue.
- Only fresh, newly received alerts in the selected view trigger automatic playback. Existing alerts are not replayed at startup.
- **Stop** clears automatic sound/voice playback. **Stop preview** stops a sound preview.

## Azure voice announcements

Open **Menu → Voice settings**.

1. Enter your Azure Speech resource **region** and **key**.
2. Choose a voice and save the settings.
3. Use **Test voice** and adjust voice volume.
4. Enable **Announce new alerts**.

Announcement format:

> The National Weather Service has issued [Weather Warning] in the following: [Counties].

County names include state abbreviations. Alerts without a county match use the affected-area description. Select **Read aloud** in an alert for manual speech.

Automatic speech starts disabled each time the app opens. Existing startup alerts and stale feeds do not trigger automatic announcements.

Azure receives the announcement text, and usage charges may apply. Saved keys are encrypted for the current Windows account using DPAPI. Use **Remove saved key** to delete the saved credential.

## Local settings

The app retains its original settings directory for compatibility:

```text
%APPDATA%\NEQ-US-Weather
```

Azure credentials and WebView2 data are stored there. Desktop and browser-preview storage are separate. Do not commit settings directories, Azure keys, or credential files to GitHub.

## Build from source

Use Windows with Node.js 24+, WebView2, and a Go toolchain compatible with `go.mod` and its dependencies. The main module declares Go 1.25.0; dependencies may require a newer toolchain.

In the application source directory:

```powershell
npm ci
npm run build
go build -tags desktop,production -ldflags "-H windowsgui -s -w" -o "US-Disaster-Prevention-Alpha-v0.0.11.exe" .
```

The frontend builds into `dist/` and is embedded by Go. The desktop app does not run a Node.js development server.

### Windows icon

Include `app_windows_amd64.syso` in the source root to embed the application icon. To regenerate it and `app.ico` from `frontend/public/logo.png`:

```powershell
Push-Location scripts/icon
go run -mod=mod .
Pop-Location
```

Then rebuild the executable.

### NSIS installer

Install [NSIS](https://nsis.sourceforge.io/). In MakeNSISW, choose **File → Load Script** and open `outputs/installer/US-Disaster-Prevention-Alpha-v0.0.11.nsi`. Loading the script compiles it; the blue circular arrow recompiles after changes.

The supplied script expects this layout in the fixed `outputs/` folder:

```text
outputs/
├── US-Disaster-Prevention-Alpha-v0.0.11.exe
├── app.ico
├── README.md
├── SOURCES.md
└── installer/
    └── US-Disaster-Prevention-Alpha-v0.0.11.nsi
```

The setup executable is written to `outputs/US-Disaster-Prevention-Alpha-v0.0.11-Setup.exe`. The installer and uninstaller use the application logo.

### Optional browser preview

```powershell
.\US-Disaster-Prevention-Alpha-v0.0.11.exe --serve 127.0.0.1:4174 --config-dir .\preview-settings
```

Open `http://127.0.0.1:4174`. Preview access is restricted to the local computer.

## Data and limitations

- [NWS API documentation](https://www.weather.gov/documentation/services-web-api)
- [NWS event colors and priorities](https://www.weather.gov/help-map/)
- [NWS zone–county correlation](https://www.weather.gov/gis/ZoneCounty)
- [National Hurricane Center GIS products](https://www.nhc.noaa.gov/gis/)

See [SOURCES.md](SOURCES.md) for boundary datasets, attribution, and provenance.

County shading is an overview: an alert may affect only part of a county. Review exact polygons and official text for coverage. Some marine and territorial alerts have no county match.

The map uses NWS operational geography, including Connecticut's eight historical counties. Kalawao is displayed separately, while NWS generally includes it in the Maui operational alert area.

Tropical cyclone layers can update at different times. The forecast cone represents uncertainty in the storm center's path, not the full area of hazardous weather. Missing or outdated feeds are identified in the interface.

## Alpha status and bug reports

Alpha v0.0.11 fixes alert sound triggering when a new NWS alert shares an event and county list with another active alert. Missing or blocked sound playback now remains visible in the status message. It also includes the v0.0.10 live app screenshot and Facebook photo posting for selected warning and watch types. The app has not been runtime-tested, per request.

Rendering changes reduce unnecessary redraws and add resource cleanup. **60 fps, measured CPU/RAM savings, and absence of memory leaks are not guaranteed.**

Report problems through GitHub Issues with the app version, Windows version, reproduction steps, and a screenshot if helpful. Never include Azure keys or private settings files.

---

An independent NWEQ Bosai project. Not an official NOAA or NWS application. Follow official weather alerts and local emergency instructions.

## Live radar (Alpha v0.0.11)
NOAA MRMS base-reflectivity imagery is enabled by default. Menu → Live radar provides an on/off switch and opacity adjustment. Imagery refreshes for the visible map after navigation and checks every 60 seconds while visible. NOAA normally publishes about every 5 minutes; this is near-real-time, not instantaneous. The status separates image refresh time from the service-wide latest timestamp, which is not a guarantee that every radar site is current. A service timestamp older than 20 minutes is marked stale. On failure, the last loaded image remains with an error notice. Blank areas can indicate missing coverage, not necessarily clear weather. Radar is click-through beneath state outlines and alert polygons. Refresh pauses while minimized to reduce CPU/network usage and resumes on return. This release was compiled without runtime testing.
