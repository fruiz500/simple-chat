# Simple Chat

A free, private video-meeting app for small groups. One HTML file, no server of your own, nothing to pay for: video and audio go directly between the participants' browsers (WebRTC), and a free Firebase project is used only to introduce them to each other and to carry the chat.

You host your own copy, so you control who can use it. Meetings are protected by a meeting ID plus a password, and the password is never stored anywhere readable (see [Security and privacy](#security-and-privacy)).

Repository: <https://github.com/fruiz500/simple-chat>

## Features

- Group meetings (comfortable up to about 4 to 6 people), with a meeting ID and a real password
- Video, audio, screen sharing (on computers), text chat and file sharing (drag a file into the chat)
- Works on phones and tablets in the browser, with a layout for portrait screens
- Camera switching (for example a USB document camera or an overhead camera for teaching)
- Background blur, darkening, lightening or replacement with your own picture
- Use a phone or tablet as an extra camera: a camera-only link, with a QR code to scan
- Enlarge any picture, and move the strip of small pictures to any side of the screen
- Captions and a shareable text transcript (each person's browser transcribes their own voice)
- Raise hand and quick reactions
- Save the chat as a text file
- Record the meeting to a file on your own computer
- No accounts, no installation for the people you invite: they open a link

## What you need

- A Google account (free)
- A computer with [Node.js](https://nodejs.org/) installed, only for the one-time deployment
- About 15 minutes

Everything below uses the free Firebase "Spark" plan. No credit card is needed.

## Installation

### 1. Get the code

```
git clone https://github.com/fruiz500/simple-chat.git
cd simple-chat
```

(Or download the ZIP from GitHub and unpack it.) The folder contains:

| File | Purpose |
| --- | --- |
| `public/index.html` | The whole app |
| `firestore.rules` | Security rules for the database |
| `firebase.json` | Tells the Firebase tool what to deploy |
| `LICENSE` | GNU GPL v3 |

### 2. Create a Firebase project

1. Go to <https://console.firebase.google.com/> and sign in.
2. Click **Create a project** (or **Add project**), give it a name, and decline Google Analytics when asked (the app does not need it).
3. Wait for the project to be created.

### 3. Turn on anonymous sign-in

People who join are signed in anonymously, with no accounts.

1. In the left menu open **Build, then Authentication** (on some console versions it is just **Authentication**). Click **Get started** if asked.
2. Open the **Sign-in method** tab.
3. Click **Anonymous**, switch it **Enable**, and **Save**.

### 4. Create the database

1. In the left menu open **Build, then Firestore Database**, and click **Create database**.
2. Choose the **Standard edition** in **native mode** if you are asked to pick an edition or a mode, pick any location near you, and start in **production mode** (the rules from this repository replace the defaults in step 7).

> **Pitfall: "Datastore mode".** If the console says the project "is set up to use Cloud Firestore in Datastore mode", this project already has a Datastore database and cannot use Firestore this way. Create a new Firebase project (step 2) instead and use that one.

### 5. Register a web app and copy its settings

1. On the project's home page click the **web** icon `</>` ("Add app"), give the app a nickname, and register it. Skip the Firebase Hosting checkbox on this screen.
2. The console shows a block of code containing `const firebaseConfig = { ... }`. Keep that page open, or find it again under **Project settings, General, Your apps**.
3. Open `public/index.html` in a text editor and find this block near the top of the script:

   ```js
   const firebaseConfig = {
       apiKey: "PASTE_apiKey",
       authDomain: "PASTE_authDomain",
       projectId: "PASTE_projectId",
       storageBucket: "PASTE_storageBucket",
       messagingSenderId: "PASTE_messagingSenderId",
       appId: "PASTE_appId"
   };
   ```

   Replace each `PASTE_...` value with the matching value from the console, keeping the quotation marks.

   These values identify your project but do not give anyone control of it. They are visible in any page served from your site by design. What actually protects your data is the rules in step 7.

### 6. Install the Firebase tool and sign in

```
npm install -g firebase-tools
firebase login
```

In the `simple-chat` folder, connect the folder to your project:

```
firebase use --add
```

Choose your project from the list and give it any alias, for example `default`.

### 7. Deploy

```
firebase deploy --only hosting,firestore:rules
```

When it finishes, the tool prints a **Hosting URL** like `https://your-project.web.app`. Open it in two browser windows (or on your computer and your phone), create a meeting in one, and join it from the other with the ID and password.

The first time, your browser asks for permission to use the camera and the microphone. Allow both.

### Optional: a shorter web address

The default address is built from the project ID, which may be long. To get a shorter one:

1. In the console open **Build, then Hosting**, then **Add another site**, and choose a name, for example `mychat`. The address becomes `https://mychat.web.app`.
2. In `firebase.json` add `"site": "mychat"` inside the `"hosting"` block:

   ```json
   "hosting": {
     "site": "mychat",
     "public": "public",
     "ignore": ["firebase.json", "**/.*", "**/node_modules/**"]
   },
   ```

3. Run `firebase deploy --only hosting` again.

Site names are shared by everyone on Firebase, so pick something unlikely to be taken.

### Updating later

After editing `public/index.html`, run `firebase deploy --only hosting`. Phones and browsers sometimes keep the old version for a while: reload the page, or clear the site's data, if a change does not show up.

## Using it

1. Open your site, type your name, and click **Create New Meeting**. Leave the password blank to get a random one, or type your own.
2. The box at the top right shows the meeting ID and password, and has **Copy invite link**. The link contains the ID and password, so whoever opens it joins directly. It hides itself after a few seconds; move the mouse into the top-right corner to bring it back (on a phone, tap the "Meeting Room" box at top left).
3. Send the link to the people you want, by any channel you trust. Anyone with the link, or with the ID and password, can join.
4. The bottom bar has Mute, Video, Camera (if you have more than one), Effects, Chat, Share Screen and **More**. More holds reactions, Raise hand, Captions, Save transcript, Save chat and Record.

### Using a phone or tablet as a second camera

In the meeting box click **Copy camera-only link** (or **Show QR code** and scan it). Open it on the phone or tablet and tap **Join**. That device sends only its camera, preferring the rear one, with no microphone and no sound, so there is no echo. Everyone else sees its picture enlarged automatically. Use the thumbtack on any picture to enlarge or reduce it for yourself, and the arrows button on the enlarged picture to move the small pictures to another side.

### Captions and transcript

Each person chooses **Captions: turn on** under More. Their own browser then transcribes their voice and sends the text to the others, and everyone's page keeps all the lines it receives. **Save transcript** writes a text file with times and speaker names, which you can give to an AI assistant to produce minutes. Transcription needs a browser that supports it (Chrome and Edge do; Firefox does not transcribe, but still shows others' captions). In Chrome the audio is processed by Google's speech service, which participants should know.

### Recording

**Record** under More saves a video of the meeting, as you see it, on your own computer. Nothing is uploaded. Everyone sees a red REC mark on the recorder's picture. Stop the recording before closing the tab; leaving the meeting also stops and saves it. Check that your computer handles long recordings before relying on it.

## Settings you may want to change

All of them are near the top of the script in `public/index.html`.

- **TURN server.** Most connections work with the free STUN servers already listed. Some networks (universities, companies, some mobile carriers) block direct connections, and then people see "Could not connect". The fix is a TURN relay. Free and paid hosted ones exist (for example [Metered](https://www.metered.ca/)), or you can run your own with coturn. Add it to the list:

  ```js
  const servers = {
      iceServers: [
          { urls: ['stun:stun1.l.google.com:19302', 'stun:stun2.l.google.com:19302'] },
          { urls: 'turn:your.turn.host:3478', username: 'USER', credential: 'PASSWORD' }
      ]
  };
  ```

  Remember that the username and credential are then visible in the page.
- **Background effects files.** They load from a public CDN (`SEG_BASE`). To serve them yourself, copy the folder of the npm package `@mediapipe/selfie_segmentation` (version 0.1.1675465747) into `public/mediapipe/` and set `SEG_BASE = 'mediapipe/'`.
- **Other CDN libraries.** Tailwind CSS, Font Awesome, the QR-code library and the Firebase SDK also load from public CDNs. The app therefore needs an internet connection and will not work offline.
- **`appId`.** This is only the name of the top-level folder inside Firestore (`zoom-clone`). If you change it, change it in `firestore.rules` too.

## Security and privacy

- **Media.** Video and audio go directly between browsers over encrypted WebRTC connections. They do not pass through Firebase or any server of yours. A TURN relay, if you add one, only forwards encrypted data.
- **Password.** The password is not stored. The room's database address is a SHA-256 hash of the meeting ID and the password together, so you cannot find the room without knowing both. The rules let a client open a room by its exact address but not list rooms.
- **What the database holds.** For each meeting: the hashed address, participants' display names, the connection setup messages, and the chat text. Chat and signaling stay in the database after the meeting; you can delete old data from the Firebase console at any time. Shared files travel directly between browsers and are not stored.
- **No host controls.** A person who has the ID and password can join, and the app has no host or "waiting room" control to remove people. Treat the invite link like a key and start a new meeting if it leaks.
- **Abuse and costs.** The Spark plan stops working at its daily quota instead of billing you. Anyone who has your site's configuration could in theory write junk to your database until the quota runs out. To limit that, restrict the browser API key to your site's address in the Google Cloud Console (APIs & Services, Credentials), and consider enabling Firebase App Check. Do not upgrade to the pay-as-you-go plan unless you set a budget alert.
- **Free-plan limits (at the time of writing).** Firestore: 50,000 reads and 20,000 writes per day; Hosting: about 360 MB of transfer per day (check the Firebase pricing page for current numbers). Video does not count against these, because it does not go through Firebase.

## Limitations

- Every participant connects to every other one (a "mesh"), so each person uploads one copy of their video per other participant. Beyond about 6 people, quality drops quickly on ordinary connections. This is not a replacement for a conference server.
- Screen sharing is not possible from phone browsers. A workaround is to mirror the phone screen to a computer (for example with AirPlay on a Mac) and share that window.
- Background effects use a segmentation model that is good, not perfect, around hair and fast movements, and they use noticeable CPU on old computers and phones.
- Speech recognition, recording formats (.webm in most browsers) and some effects differ between browsers. Chrome and Edge are the best tested.
- There is no host role: anyone can start or stop their own captions or recording, and nobody can remove anyone.

## Troubleshooting

| Message or symptom | Cause and fix |
| --- | --- |
| "Paste your Firebase config into the file" | The `PASTE_...` values are still in `index.html`, or your browser is showing an old cached copy. Fill them in, redeploy and reload. |
| `auth/invalid-api-key` | A value in `firebaseConfig` was copied wrongly. |
| `auth/operation-not-allowed` or sign-in errors | Anonymous sign-in is not enabled (step 3). |
| "Missing or insufficient permissions" | The rules were not deployed, or `appId` and the rules disagree. Run `firebase deploy --only firestore:rules`. |
| Console says the project is in Datastore mode | Create a new Firebase project (step 4). |
| People join but "Could not connect" | A network is blocking direct connections. Add a TURN server. |
| No sound from someone | The browser needs a click on the page before it plays sound, or the microphone is muted or blocked in the browser's site settings. |
| Camera or microphone blocked | Allow them in the browser's site settings for your address, and reload. |
| A change does not appear on a phone | Reload, or clear the site data; phones cache aggressively. |

## Credits and third-party software

Simple Chat loads these from public CDNs at run time and does not redistribute them: [Firebase JS SDK](https://github.com/firebase/firebase-js-sdk) (Apache-2.0), [Tailwind CSS](https://tailwindcss.com/) (MIT), [Font Awesome Free](https://fontawesome.com/) (icons CC BY 4.0, fonts SIL OFL 1.1, code MIT), [MediaPipe Selfie Segmentation](https://github.com/google-ai-edge/mediapipe) (Apache-2.0) and [qrcode-generator](https://github.com/kazuhikoarase/qrcode-generator) (MIT). Voice transcription uses the browser's built-in Web Speech API.

This project was written with the help of AI assistants.

## License

Copyright (C) 2026 Francisco Ruiz

Simple Chat is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program (see [LICENSE](LICENSE)). If not, see <https://www.gnu.org/licenses/>.
