# Plantar Analysis – Android app (1.1 = desktop v10 features)

The phone version of the desktop Plantar Analysis Framework. Plug the pressure mat
(Arduino) into an Android phone with a USB-OTG adapter, record static and postural
tests, look at the results, and make the same PDF report – without a PC.

Native Kotlin + Jetpack Compose. Sessions are stored in **the same folder and CSV
layout as the desktop app**, so a session recorded on the phone opens in the desktop
app and the other way round.

---

## 1. Features (desktop → phone)

| Desktop | Phone |
|---|---|
| Data Collection: subject form, Static / Postural / Dynamic (coming soon) | Collect tab – same fields, same checks, same tiles |
| Serial port + baud rate | Mat found automatically on USB-OTG; baud rate in Settings |
| Static: live preview → Capture | Live heatmap with contact area and left/right split → **Capture** |
| Postural: countdown → eyes open 10 s → pause 2 s → eyes closed 10 s | Same, with phase tracker, time left, frame counters, manual snapshots |
| Stop → "Do you want to save this data?" | Same: Yes save / Discard / Keep going |
| Data Retrieval tree | Sessions tab – grouped by subject, search, Static/Postural filter |
| Static view: tiles, Heatmap / MAX / COP+CG images, Static Information, load split | Same, plus a pressure map with colour bar; tap a picture to read the pressure |
| CG Variation: Convex hull / Lines / Points / Advanced View, Lock zoom, Reset to centre, zoom; v10 fixed ±150 mm scale | Same: ±150 mm scale and limits, two fingers zoom/move, − / + buttons, double-tap = back to ±150 mm, plus **Fit** (zoom to the data) |
| v10 **CG Along Frames**: A-P vs sample and M-L vs sample, eyes open vs closed, wheel = y-zoom, Ctrl+wheel = both axes, Lock zoom, Reset view (±150 mm) | Same tab: − / + zoom the mm axis, two fingers zoom/move both axes (sample axis kept inside the recording), Lock zoom, Reset view, Fit, touch to read a sample |
| Sway metrics (area, ellipse, path, velocity, RMS, range, Romberg EC/EO) | Same formulas (port of `core/sway.py`), tiles + full table |
| Videos tab | Replay tab – plays every recorded matrix at its recorded timing; play/pause both, restart, seek |
| Mid-Frame Overview (Frame / COP / **COP 4Q** / MAX, Static Info with subject + weight, slider; v10 defaults: Frame off) | Overview tab – same; COP 4Q = four-quadrant COPs (Q1 top-left … Q4 bottom-right) computed from the raw frame, plus each quadrant's share of the load |
| Pressure Variation (max / average per frame, lines/points, y-zoom, v10 Lock zoom + Ctrl-wheel x-zoom, read-out) | Pressure tab – same: − / + zoom the pressure axis, two fingers zoom both axes, Lock zoom, touch and slide to read every frame |
| Print → PDF report with section choices | PDF report (A4) with the same sections (+ CG along frames and COP 4Q frame) → Open, **Print** (Android print dialog), Share |
| Themes, accent colour, white plots, kPa / g/cm² | Settings – same five themes and nine accents |
| – | Demo mat (try everything without hardware), share a session / all data as .zip |

## 2. What you need

* An Android phone, Android 8.0 or newer, that supports **USB-OTG** (almost all do).
* A USB-OTG adapter or cable (USB-C/micro-USB to USB-A) for the Arduino cable.
* The mat with the **same Arduino sketch as for the desktop app** (it sends `START`,
  16 comma-separated rows, `END` – the app reads whatever grid size arrives, e.g. 32 × 32 later).
  Supported USB chips: genuine Arduino, CH340/CH341 clones, FTDI, CP210x, PL2303, CDC.
* The phone powers the Arduino and the mat (~0.5 A max) and **does not charge while
  the mat is connected**. If the phone can't power it, use a powered OTG hub / Y-cable.

## 3. Build and install (Android Studio)

1. Install **Android Studio** (free, developer.android.com/studio).
2. Unzip this folder, then **File → Open** → choose the `PlantarMobile` folder.
3. Wait for "Gradle sync" to finish (first time: it downloads Gradle 8.9, the Android
   SDK 35 and the libraries – needs internet, takes a few minutes). If it offers to
   upgrade the Android Gradle Plugin, you can say *Don't remind me*; the project works as it is.
4. On the phone: **Settings → About phone → tap "Build number" 7 times**, then
   **Developer options → USB debugging = on**. Connect the phone to the PC with a cable,
   allow debugging on the phone.
5. Press the green **Run ▶** button. The app installs and opens; it also appears in the
   phone's app drawer as *Plantar Analysis*.

**APK file instead** (to install on several phones): **Build → Build App Bundle(s) / APK(s)
→ Build APK(s)** → *locate* → `app/build/outputs/apk/debug/app-debug.apk`. Copy it to
the phone and open it (allow "install unknown apps" once).

If the build fails, copy the red error text and send it to me.

## 4. Using it

**First try without the mat:** Settings → *Demo mat* on. A simulated two-foot mat
streams at 20 frames/s; tests and sessions work exactly as with the real mat.

**Connect the mat:** Settings → Demo mat off. Plug the mat in through the OTG adapter.
Android asks *"Open Plantar Analysis to handle USB device?"* – tick **Always open** and
OK: next time the app starts by itself and needs no permission. The Collect tab shows
*Waiting* (the Arduino restarts for ~2 s when the port opens) and then *Live* with the
grid size and frame rate.

**Static test:** enter name, weight, height → *Static* → **Start test** → countdown →
watch the live heatmap → **Capture**. The session opens.

**Postural test:** *Postural* → **Start test** → countdown → eyes open → pause (ask the
subject to close the eyes) → eyes closed. *Save a snapshot now* stores the current frame
(like `s` on the PC). **Stop** asks save / discard. Durations and countdown: Settings.
The screen stays on during a test; the test keeps running if you switch apps.

**Sessions:** tap a session. Postural sessions have CG Variation, CG Along Frames, Replay,
Overview and Pressure tabs. Printer icon = PDF report (uses what the screen shows: CG toggles and
zoom, Advanced view, the Overview frame, Pressure options, the unit). ⋮ menu = share the
session as .zip, delete.

## 5. Data and moving it to / from the PC

Everything is in the phone's storage under

```
Android/data/edu.nitk.plantar/files/PlantarData/<Name>/Static_Analysis/<dd-MM-yyyy_HH-mm>/
Android/data/edu.nitk.plantar/files/PlantarData/<Name>/Postural_Analysis/<dd-MM-yyyy_HH-mm>/
Android/data/edu.nitk.plantar/files/PDF_Reports/<Name>/<Postural|Static> Report/
```

Same files as the desktop app: `session_info_*.csv`, `continuous_EyesOpen_*.csv` /
`continuous_EyesClosed_*.csv` (every matrix received, with time stamps),
`Eyes_open_CG_variation_csv.csv` / `Eyes_close_CG_variation_csv.csv`, static
`snapshot_ADC_*.csv`, `snapshot_Voltage_*.csv` and the four static heatmap PNGs.

* **Phone → PC:** session ⋮ → *Share session data* (or Settings → *Share all data*) and
  send the zip to yourself, or connect the phone by USB (*File transfer*) and copy the
  `PlantarData` folder. Unzip into the desktop app's data folder → it appears in Data Retrieval.
  For a postural session, tick *Add desktop pictures and videos* so the desktop's
  Overview / Pressure / Videos tabs have their Frames / COP / COP4Q / MAX pictures and MP4s
  (the phone itself doesn't need them – it computes everything from the raw frames).
* **PC → phone:** copy a desktop session folder into `PlantarData/<Name>/...` on the
  phone. Sessions with `continuous_*.csv` files work fully; older ones without raw
  frames show the CG variation from the CG CSV files.
* **Uninstalling the app deletes this folder** – copy it first.

## 6. How the numbers are computed – and how they differ from the desktop

The phone computes everything from the **raw ADC values of every frame** (the desktop
works from the heatmap images). Per frame:

* contact cell: ADC ≥ frame minimum + 12 (the desktop live view's noise floor; Settings → *Contact threshold*)
* cell weight = ADC − frame minimum; body weight × 9.81 N is shared over the contact cells
  in proportion to their weight; pressure = cell force / cell area (1 N/cm² = 10 kPa = 101.97 g/cm²)
* CG = weight-averaged cell position; offset from the mat centre, X right-positive,
  Y positive towards row 0 (down on screen) – the desktop's sign convention
* left / right = left / right half of the mat as drawn; MAX point = weighted centre of the cells ≥ 90 % of the peak
* sway metrics: exactly the desktop `core/sway.py` formulas (first/last two samples trimmed)
* COP 4Q (desktop v10 `quadrant_cops`): the mat as drawn is split into four equal quadrants
  (Q1 top-left, Q2 top-right, Q3 bottom-left, Q4 bottom-right); each COP is the weighted centre
  of that quadrant's contact cells. The desktop works from the heatmap image, the phone from the raw cells.

Things to know (not hidden):

1. **Numbers will not match the desktop exactly.** Colour→pressure decoding of images
   (desktop) and raw ADC values (phone) are different measurements of the same frame.
   Both are *relative* pressures scaled to body weight, not calibrated pressures – there
   is still no ADC → force calibration curve.
2. **Mat size inconsistency in the desktop app:** its pressure decoding uses a 28 × 28 cm
   mat, its CG pipeline (`cop_pipeline_2`) assumes 300 mm. The phone uses one value for
   both: Settings → *Mat width / height* (default 280 mm). Set the real active area of your
   mat. Each phone session stores the values it was recorded with (`session_info`).
3. **Mean velocity** uses the real time between the first and last sample (from the
   time stamps); the desktop uses the video frame rate. Same idea, slightly different numbers.
4. The desktop's "Dynamic" test is still a placeholder on both.

## 7. Troubleshooting

| Problem | Fix |
|---|---|
| Gradle: *Could not find com.github.mik3y:usb-serial-for-android:3.8.0* | In `app/build.gradle.kts` use the newest version from github.com/mik3y/usb-serial-for-android/releases (e.g. `3.9.0`), then *Sync Now* |
| Collect says *No mat* | Check the OTG adapter (try a USB stick: does the phone see it?), some phones need *OTG* switched on in Settings |
| *Permission* | Tap Connect and allow; tick *Always open* when Android offers it |
| *Waiting* for more than a few seconds | Wrong baud rate (Settings, must match `Serial.begin`), or the sketch doesn't send `START` / rows / `END` |
| Mat lights flicker / disconnects | Not enough power from the phone: use a powered OTG hub or Y-cable |
| *No PDF viewer* | Use Share → Drive / Files, or install any PDF viewer |

## 8. Project layout

```
app/src/main/java/edu/nitk/plantar/
  core/      pure Kotlin: frame parser, raw-frame analysis, sway, heatmap rendering,
             COP/MAX traces, session files (desktop formats), loading, demo mat
  mat/       USB-OTG connection (usb-serial-for-android) + demo mat
  test/      test runner: countdown, phases, recording, saving
  data/      settings, session list / cache / file sharing
  render/    heatmap bitmaps and COP / MAX / COP+CG overlays
  charts/    CG and pressure charts (Canvas, shared by screen and PDF)
  report/    A4 PDF report (Android PdfDocument) + printing
  export/    session / all-data zips, desktop pictures + MP4 videos (MediaCodec)
  ui/        Compose screens: collect, live test, sessions, session tabs, settings
app/src/test/  JVM unit tests of the core (right-click → Run)
```

## 9. What was verified before handing over

* The analysis core was checked against the desktop's own Python code on a real desktop
  recording (parser, display field = scipy gaussian, video frames vs OpenCV, sway metrics)
  and has JVM unit tests (`app/src/test`, all passing).
* The whole app – including every Compose screen – was compiled with Kotlin 2.0.21 and the
  real Compose compiler checks against API stubs, with no errors.
* **Not** done here: a full Android Gradle build, running on a phone, and testing with the
  real mat (no Android SDK / phone in the build environment). The first build in Android
  Studio and the first test with the mat are the real test – send me anything that fails.
