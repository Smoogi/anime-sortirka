# Anime Sortirka

### 📥 Download

[![Download Latest Release](https://img.shields.io/badge/Download-Latest%20APK-2ea44f?style=for-the-badge&logo=android)](
https://github.com/Smoogi/anime-sortirka/releases/latest)

An Android gallery that helps you **sort a large anime image library by character**. The AI runs on **your own Windows PC** and talks to your phone over your home Wi-Fi: no cloud, no accounts, no commercial APIs.

- The phone shows and manages the images already on it (read-only by default).
- It sends small resized copies of **only the folders you choose** to your PC.
- The PC runs the [WD SwinV2 tagger v3](https://huggingface.co/SmilingWolf/wd-swinv2-tagger-v3) with ONNX Runtime (GPU through DirectML, CPU as fallback) and returns the characters it recognises.
- The app suggests character folders, lets you review and correct them, and optionally turns them into real folders.
- Your originals are never deleted, moved or overwritten without an explicit confirmation.

> **Status:** early version (0.1.0). Tested on Android 13 with an AMD Radeon RX 9070 GRE. Android 14+ and Android 17 are implemented but not yet tested.

## What you need

| | |
|---|---|
| Phone | Android 7.0 or newer. Download the APK from the [Releases page](https://github.com/Smoogi/anime-sortirka/releases/latest) (`arm64-v8a` for almost every modern phone). |
| PC | Windows 10/11 with Python 3.9+ and the **PC server** (see below). Any DirectX 12 GPU can accelerate it; otherwise it falls back to the CPU (much slower). |
| Network | Phone and PC on the **same Wi-Fi**. |

> **The PC server is not published in this repository yet.** This page explains how to use it; the server is a separate Python project that you start with `Start_Server.bat`.

## 1. Install the app

1. On your phone, download `AnimeSortirka-<version>-arm64-v8a.apk` from the [latest release](https://github.com/Smoogi/anime-sortirka/releases/latest). It is built for 64-bit ARM phones, which covers practically every Android phone of the last ~10 years (older releases up to 0.3.0 also have a larger `universal` APK).
2. Open the file and allow "install unknown apps" for your browser or file manager when Android asks.
3. Check the file against `SHA256SUMS.txt` from the same release if you want to verify it.
4. Open **Anime Sortirka** and allow access to **all photos** (not "selected photos only"). The first scan of ~10,000 images takes about a minute.

## 2. Start the PC server

1. Double-click **`Start_Server.bat`**. The first run creates its own Python environment and downloads the model (about 445 MB) automatically.
2. After a few seconds the console shows:
   - the server address, for example `http://192.168.1.134:8765`
   - a **pairing token**
   - a **QR code** drawn with text characters
3. **Keep that window open.** It is the server. Closing it stops the analysis.

### One-time Windows setup (needed to reach the PC from the phone)

- Right-click **`Allow_Firewall.bat`** and choose **Run as administrator**. It only allows connections from your local network, on Private networks only.
- Set your Wi-Fi network profile to **Private**: *Settings → Network & Internet → Wi-Fi → your network → Private*. The firewall rule does not apply to Public networks.
- If your router has "client isolation" / "AP isolation", turn it off.

## 3. Connect the phone to the PC

1. In the app, open the **Library** tab and tap the **sparkle** icon (top right).
2. Tap the PC row at the top, then **Scan QR code from the PC** and point the camera at the QR code in the server window. If it is too small to scan, maximize the console window or zoom in.
3. No camera or QR code not working? Type the **address** and **token** from the server window under *…or enter it manually*, then tap **Test connection and save**.
4. On success you will see **"Connected. GPU (DirectML)"** (or "CPU only").
5. On Android 17 the app may ask for the **local network / Nearby devices** permission. Allow it.

The token is stored securely on the phone. The server only accepts devices on your local network.

## 4. Analyse your images

1. On the same screen, **tick the folders you want analysed**, for example your Download folder. Images in other folders are never sent anywhere.
2. Tap **Start analysis** and allow notifications. It keeps running while the screen is locked.
3. Speed depends on your Wi-Fi and phone, typically 5-20 images per second. You can **Pause** and resume any time; finished images are not redone.
4. For long runs, tap **Allow unrestricted battery use** so Android does not stop the app.
5. Keep the PC awake and the server window open.

The PC does not store your images; they are processed in memory and discarded. Only the tags are kept, on your phone.

## 5. Sort your images

The bottom bar has four tabs:

| Tab | What it is for |
|---|---|
| **Library** | Browse everything, filter by folder, open images full-screen. In the viewer you can see the detected characters and add any image to an album by hand. |
| **Suggestions** | Characters the AI found, grouped (for example "Nero Claudius - 124 images"). The **Confidence** slider (default 0.75) controls how sure it must be. |
| **Unsorted** | Analysed images with **no confident character** (3D renders, photos, characters the model does not know). Sort these by hand: long-press to select several, then **Add to album**. |
| **Albums** | Your virtual albums. Open one to rename, merge, delete (the album only, never the images) or **Organise files…**. |

### Create a folder from a suggested character

1. Open **Suggestions** and tap a character.
2. Images are listed best match first. Tap images (or long-press to multi-select) to choose some, or leave nothing selected to use all.
3. Tap **Create folder**. The app proposes names, for example **Nero**, **Nero Claudius**, **Nero Claudius (Fate)**. Tap one or type your own.
4. If the character has other versions or outfits, they are listed with image counts so you can put them in the same folder.
5. Tap **Create with N**. This makes a **virtual** album only; no file is touched.

Images with several characters keep all of them: in the viewer, tap a character chip to put the image in (or take it out of) that character's folder. Use **Not this character** in a suggestion list to hide a wrong match.

### Turn an album into a real folder (optional)

Open an album, then **⋮ → Organise files…**. Pick a mode and confirm:

- **Copy (default, recommended):** creates new files in `Pictures/Anime Sortirka/<Album name>/`. Originals stay where they are. Copies use extra storage and can be undone from **History** (the clock icon on the Albums tab).
- **Move:** takes the originals out of their folders. Android shows its own "Allow … to modify N photos?" dialog; if you decline, nothing changes. **Important:** Android only allows images in `Pictures` and `DCIM`. A move out of other folders such as **Download cannot be undone by the app**. It warns you first and hides Undo in that case. Copy has no such limit.

Every copy and move is logged under **History**. Name clashes are never overwritten; the new file gets "(1)" appended.

## Troubleshooting

| Problem | Try this |
|---|---|
| "Cannot reach …" when pairing | Is the server window still open? Are phone and PC on the same Wi-Fi? Did you run `Allow_Firewall.bat` as administrator and set the network to **Private**? Is router client isolation off? |
| It worked yesterday, not today | Your router may have given the PC a new address. Look at the address in the server window and pair again. Giving the PC a fixed address in the router settings avoids this. |
| "The server rejected the pairing token" | Start the server with `Start_Server.bat --rotate-token` for a new token, then pair again. |
| Analysis stops when the screen turns off | Allow notifications, and use **Allow unrestricted battery use**. Do not swipe the app away from recent apps while it runs. |
| Slow analysis | Check the pairing message says **GPU (DirectML)**. "CPU only" is much slower. Close other GPU-heavy programs. |
| An image shows as failed | Corrupt or unsupported file. Use **Retry failed** on the analysis screen after fixing it. |
| Only some photos appear | The app was given "selected photos only". Change it to all photos in Android's app settings. |

## Privacy and safety

- Everything stays on your own devices and network. There are no accounts, cloud services or analytics.
- Only folders you tick are analysed. Images are sent as small resized JPEG copies to your PC and are not kept there.
- The app reads your photos and only changes files when you confirm a Copy or Move. It never deletes your images.
- The server is plain HTTP on your local network, protected by the pairing token and limited to local addresses. Do not expose it to the internet.
- The APK is signed with the standard Android debug certificate (sideloading only). Updates must be signed with the same key.

## Known limitations

- Early version; expect rough edges.
- Moves out of folders other than `Pictures`/`DCIM` cannot be undone (an Android restriction).
- After a Copy or Move the app re-reads the whole library, which can take about a minute.
- The AI knows characters from the tag list it was trained on. Newer or obscure characters may land in **Unsorted**.
- Not yet tested on Android 14+, Android 17 or a very large (30,000+) library.

## Credits

Character recognition uses [SmilingWolf/wd-swinv2-tagger-v3](https://huggingface.co/SmilingWolf/wd-swinv2-tagger-v3), downloaded by the PC server from Hugging Face on first run.
