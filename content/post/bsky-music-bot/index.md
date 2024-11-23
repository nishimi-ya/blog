---
title: Building a BlueSky Bot that Shares Your Current Song with MPD
description: Learn how to create a bot that posts your current song to BlueSky with this step-by-step guide.
date: 2024-11-10 18:55:00+0700
slug: bsky-music-bot
image: github.jpg
categories:
    - Tech
tags:
    - BlueSky
    - social media
    - code
author: Nishimiya
--- 

My journey with Bluesky started with a spark of curiosity. After exploring
the platform, I found myself diving deeper into how their system works,
eventually leading me to a fun and practical project: building a bot that
shares your current song with Bluesky using MPD (Music Player Daemon).
This project has been a great opportunity to get hands-on experience with
Bluesky’s API and automate something useful for myself.

While the initial goal was simply to create a basic bot template that
posts something to Bluesky, I decided to focus specifically on building
a bot that automatically shares your current song—if you’re using Linux
and MPD. But I don’t want to stop here. I plan to further improve it with
Last.fm scrobbling, and maybe even integrate with YouTube music streaming.
However, those ideas are still a work in progress, and I need to figure
out how best to implement them.

In this post, I'll break down what this bot does, step by step, and
provide code snippets along the way to show how each piece works.

## Breaking Down the Bot: From Concept to Execution

### 1. Getting Started with Bluesky

Before diving into the code, we need to interact with the Bluesky
platform. Bluesky offers a simple API, which we can use to post updates.
To do this, I used the `@atproto/api` package to interact with the Bluesky
API.

**What we’re doing**:
- **Logging into Bluesky** using your username and password (stored
  securely with `dotenv`).
- **Posting a text update** to Bluesky, which will be our song info.

Here's how we log in and post to Bluesky:

```javascript
const api = require('@atproto/api');
const dotenv = require('dotenv');
dotenv.config();

const agent = new api.BskyAgent({
    service: 'https://bsky.social',
});

async function postToBluesky() {
    try {
        // Login to Bluesky with credentials stored in the environment variables
        await agent.login({
            identifier: process.env.BLUESKY_USERNAME,
            password: process.env.BLUESKY_PASSWORD,
        });

        // The bot will post this text (current song info)
        const songText = await logCurrentSong();

        // Post the song info as a status update on Bluesky
        const response = await agent.post({
            text: songText,
        });
        console.log("Successfully posted to Bluesky:", response);
    } catch (error) {
        console.error('Error posting to Bluesky:', error);
    }
}

postToBluesky();
```

### 2. Getting Current Song Information from MPD

Next, we need to interact with MPD (Music Player Daemon), which is
a server-side application that controls music playback. We’ll use the
`mpd-api` library to fetch the currently playing song from MPD.

Here’s a simple function to retrieve the current song's title, artist, and
album:

```javascript
const mpdapi = require('mpd-api');

async function logCurrentSong() {
    try {
        const config = {
            host: '127.0.0.1', // Localhost
            port: 6600,         // Default MPD port
        };

        // Connect to MPD and get the status
        const client = await mpdapi.connect(config);
        const status = await client.api.status.get();
        const currentSong = await client.api.status.currentsong();

        // If a song is playing, return its info; otherwise, return a default message
        if (status.state === 'play' && currentSong) {
            return `Now playing: ${currentSong.title} by ${currentSong.artist} from the album ${currentSong.album}`;
        } else {
            return "No song currently playing.";
        }
    } catch (error) {
        console.error('Error fetching song info:', error);
        return "Error retrieving song info.";
    }
}
```

In this code:
- We connect to MPD (running locally on `127.0.0.1` at port `6600`).
- We fetch the current song details if MPD is playing, and we return
  a string with the song title, artist, and album.
- If no song is playing, we return a message saying so.

### 3. Putting It All Together

Now that we have the logic to retrieve the song info from MPD and post to
Bluesky, it’s time to combine them into a full working bot. 

Here’s what happens:
- The bot first logs in to Bluesky using your credentials.
- It then gets the current song from MPD.
- Finally, it posts the song details to Bluesky as a status update.

**Here’s the full code for the bot**:

```javascript
"use strict";
var __createBinding = (this && this.__createBinding) || (Object.create ? (function(o, m, k, k2) {
    if (k2 === undefined) k2 = k;
    var desc = Object.getOwnPropertyDescriptor(m, k);
    if (!desc || ("get" in desc ? !m.__esModule : desc.writable || desc.configurable)) {
      desc = { enumerable: true, get: function() { return m[k]; } };
    }
    Object.defineProperty(o, k2, desc);
}) : (function(o, m, k, k2) {
    if (k2 === undefined) k2 = k;
    o[k2] = m[k];
}));
var __setModuleDefault = (this && this.__setModuleDefault) || (Object.create ? (function(o, v) {
    Object.defineProperty(o, "default", { enumerable: true, value: v });
}) : function(o, v) {
    o["default"] = v;
});
var __importStar = (this && this.__importStar) || function (mod) {
    if (mod && mod.__esModule) return mod;
    var result = {};
    if (mod != null) for (var k in mod) if (k !== "default" && Object.prototype.hasOwnProperty.call(mod, k)) __createBinding(result, mod, k);
    __setModuleDefault(result, mod);
    return result;
};
Object.defineProperty(exports, "__esModule", { value: true });
const api_1 = require("@atproto/api");
const dotenv = __importStar(require("dotenv"));
const mpdapi = __importStar(require("mpd-api"));
// Load environment variables
dotenv.config();
// Create a Bluesky Agent
const agent = new api_1.BskyAgent({
    service: 'https://bsky.social',
});
// Function to connect to MPD and log the current song
async function logCurrentSong() {
    try {
        // Define connection configuration
        const config = {
            host: "127.0.0.1", // MPD server host
            port: 6600, // MPD server port
            // password: 'yourpassword',  // Uncomment if MPD requires a password
        };
        // Connect to MPD using the config object
        const client = await mpdapi.connect(config);
        console.log("Connected to MPD");
        // Get the playback state and currently playing song
        const status = await client.api.status.get();
        const currentSong = await client.api.status.currentsong();
        // Check if MPD is currently playing something
        if (status.state === "play" && currentSong) {
            const songInfo = {
                title: currentSong.title || "Unknown Title",
                artist: currentSong.artist || "Unknown Artist",
                album: currentSong.album || "Unknown Album",
            };
            const songText = `Now playing: ${songInfo.title} by ${songInfo.artist} from the album ${songInfo.album}`;
            console.log(songText);
            return songText;
        }
        else {
            console.log("No song currently playing.");
            return "No song currently playing.";
        }
        // Disconnect from MPD
        await client.disconnect();
    }
    catch (error) {
        console.error("Error connecting to MPD or retrieving song information:", error);
        return "Error retrieving song info.";
    }
}
// Function to post current song info to Bluesky
async function postToBluesky() {
    try {
        // Login to Bluesky
        await agent.login({
            identifier: process.env.BLUESKY_USERNAME, // Explicitly cast to string
            password: process.env.BLUESKY_PASSWORD, // Explicitly cast to string
        });
        // Get the current song info
        const currentSongText = await logCurrentSong();
        // Post the current song info to Bluesky
        const response = await agent.post({
            text: currentSongText,
        });
        console.log("Successfully posted to Bluesky:", response);
    }
    catch (error) {
        console.error("Error posting to Bluesky:", error);
    }
}
// Run the Bluesky post function
postToBluesky();
```

## **Running the Bot Locally**

To run the bot locally:
1. Install the necessary dependencies with `npm install`.
2. Create a `.env` file to store your Bluesky credentials:
    ```
    BLUESKY_USERNAME=your_username
    BLUESKY_PASSWORD=your_password
    ```
3. Run the bot using `node index.js`.

When you run the bot, it will:
- Log into Bluesky.
- Retrieve the current song from MPD.
- Post the song info to Bluesky as a status update.

You should see the current song in your Bluesky feed!

## Next Steps: Expanding the Bot

This bot is a great starting point, but there’s a lot of room for improvement:
- **Last.fm Scrobbling**: Integrate Last.fm API to scrobble the songs you listen to.
- **YouTube Integration**: If you want to share music from YouTube, you’ll need to figure out how to extract the currently playing video or song.
- **Scheduled Posts**: You could set up a scheduled task to post updates periodically instead of only when triggered manually.

### Check Out the Full Code on GitHub

If you want to explore the full project, check out the repository on GitHub:
[https://github.com/nishimi-ya/mpd-bsky-bot](https://github.com/nishimi-ya/mpd-bsky-bot)
