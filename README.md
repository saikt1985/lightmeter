# Pocket Lux Meter

A single-file web app (`index.html`) that turns a phone camera into a rough lux meter.
Two ways to read:

- **Live mode** (Android Chrome): continuous reading from the camera preview.
- **Photo mode** (any phone, including Safari and Chrome on iPhone): take one photo, the page reads the exposure record the camera wrote into the file.

Chrome on iPhone uses the same Safari engine, so live mode is relative-only there. Use photo mode on iPhone.

## Live site

https://luxcalc.netlify.app (deploys from the main branch of saikt1985/lightmeter). Add it to the phone's home screen.

## Run it on your phone

The camera API only works on an HTTPS page (or `localhost`), so the file has to be served, not opened from the file system.

Quickest options:

1. **Netlify Drop**: open https://app.netlify.com/drop in a desktop browser and drag this `light-meter` folder onto it. You get an HTTPS URL in seconds. Open it on the phone.
2. **GitHub Pages**: push this folder to a repo, enable Pages on the branch, open the Pages URL.
3. **Local LAN test (Android only)**: on the Mac run

       python3 -m http.server 8765 --directory light-meter

   then on the phone open `chrome://flags/#unsafely-treat-insecure-origin-as-secure`, add `http://<mac-ip>:8765`, relaunch Chrome and open that URL.

Add it to the home screen for a full-screen app feel.

## Taking a reading on iPhone (photo mode)

1. Settings → Camera → Formats → **Most Compatible** (so photos are JPEG with full exposure data).
2. In the **Camera app**, point at the surface so it fills the centre of the frame, or hold a sheet of paper over the lens for incident mode. Flash off. Shoot. Only the centre box of the picture is measured, the same box Android live mode uses; the page shows the photo with that box drawn on it.
3. Open the page, pick Surface or Incident mode and the surface finish, press **Pick photo from library** and choose that photo. Changing the finish or mode afterwards re-derives the reading from the same photo.
4. The reading appears at the top. Add a point label and press **Log reading**.

Photos taken through the browser's own camera button lose their exposure tags on iPhone (Safari re-encodes them), which is why the Camera app is used. Verified on a real iPhone: library photos carry the tags, browser captures do not.

The small grey line under the button shows what the photo carried: shutter, ISO, aperture, the metered brightness value for the whole frame, the centre-to-frame ratio used to move it to the centre box, and the two luminance estimates. If the two estimates differ by a lot, reshoot with the centre box evenly filled.

## Taking a live reading (Android)

1. Start camera. Allow the permission.
2. **Surface mode**: fill the dashed box with a plain matte surface lit by the light you want to measure (a sheet of white paper on the desk works). Reading is the illuminance on that surface.
   **Incident mode**: hold one sheet of plain white copier paper flat over the rear lens, paper facing the light. Reading is the illuminance at the lens.
3. Wait for the number to settle, press **Hold 2 s** for a trimmed average, then **Log reading** with a point label. Export the table as CSV.

## Surface finish

Surface mode turns the brightness of a surface into the light falling on it, so it has to know how much the surface reflects. The **Surface finish** selector offers white paper (0.85), white paint (0.75), light finishes and pale wood (0.50), mid tones and medium wood (0.35), dark wood and dark fabric (0.20), an 18% grey card (0.18) or a custom value. The chosen reflectance is shown in the mode badge and recorded in the CSV.

A sheet of white paper laid on the spot is the only accurate route: real woods and paints vary by a third or more around those figures, and glossy finishes add reflections. Reading a wooden desk directly with "medium wood" selected gives an estimate, not a measurement. Incident mode (paper over the lens) is the other way to avoid the question.

## Calibrate once

Put the phone next to a real lux meter under the same light, enter the meter's value, press **Calibrate to reference**. The factor is stored per mode on that phone. Re-do it if you change paper or phone.

## Limits

- Live mode needs shutter time and ISO from the browser. Android Chrome exposes these on most phones (shown in the small grey line under the camera). Safari and Chrome on iPhone do not, so live mode shows a "relative only" badge there. Photo mode is the iPhone path.
- Photo mode depends on the photo keeping its exposure tags. iOS strips GPS from camera uploads but keeps the exposure fields on current versions. If the page reports no exposure data, check the Most Compatible format setting.
- Camera sensors are not V(λ) matched. Expect roughly ±15 to 25 percent after calibration under similar sources, worse across very different colour temperatures or narrow-band LEDs.
- The "clipping" or "too dark" badge means the box is saturated or nearly black; move or change exposure lock.
- Not a substitute for a Class B lux meter for compliance or handover measurements.
- If the phone has an ambient light sensor exposed to the browser (Android Chrome with `chrome://flags/#enable-generic-sensor-extra-classes`), a second panel shows its direct reading, which is usually the better number.
