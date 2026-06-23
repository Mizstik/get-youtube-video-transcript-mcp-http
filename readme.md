# get-youtube-video-transcript-mcp

Note: This repo is deprecated as the streamable http functionality has been merged into the [original repo](https://github.com/Mizstik/get-youtube-video-transcript-mcp) that can now operate both in stdio mode and in http streamable mode.

This is the HTTP Streamable version of my youtube transcript MCP server which grabs transcripts/subtitles as well as the title from YouTube videos using yt-dlp. This can be easier than stdio to deploy and connect when it comes to some frontends such as OpenWebUI or the dockerized version of Odysseus.

# Installation

```
git clone https://github.com/Mizstik/get-youtube-video-transcript-mcp-http.git
cd get-youtube-video-transcript-mcp-http
npm install
```
You will also need ffmpeg which is used to convert the subtitles into a format that uses fewer tokens and deduplicate the lines.
```
winget install ffmpeg
```

# Start the server
```
node main.js
```

This starts the server on port 12001 and will listen on /mcp. You will need to start this server and leave it running. The frontend will not start/stop this for you unlike the stdio version.

# Adding the MCP server to your AI frontend

In your agent frontend, select Streamable HTTP type when adding the MCP server. In the address, use http (not https), your IP (or localhost if applicable), port 12001, and /mcp path. Example:

```
http://192.168.1.100:12001/mcp
```

Here's an example in Odysseus:

![screenshot](https://github.com/Mizstik/mizstik.github.io/blob/master/get-youtube-video-transcript-mcp-http-odysseus-example1.png?raw=true)

![screenshot](https://github.com/Mizstik/mizstik.github.io/blob/master/get-youtube-video-transcript-mcp-http-odysseus-example2.png?raw=true)


# Initialize & Update yt-dlp

The MCP server includes a tool called "initialize-yt-dlp" which the AI can call to have the server download the yt-dlp executable. You only need to do this once before you use it the first time.

There is also another tool called "update-yt-dlp" which you can also have the AI call, which will automatically update yt-dlp. This will often resolve issues where yt-dlp is showing messages about cookies, login, or not finding formats.

If the above two tool calls don't work, then manually grab the yt-dlp executable from https://github.com/yt-dlp/yt-dlp/releases and place it in the mcp's directory (where main.js is).

After you're done setting up yt-dlp, you can disable the update and initialize tools in your frontend so that they don't clutter the AI context.


# Note on Odysseus

For Odysseus specifically (as of June 2026), the agent has its own context manipulation when it detects a youtube URL which is non-configurable and will invisibly interfere with your chat context. You will have to manipulate the source code (specifically in /odysseus/services/youtube/youtube_handler.py and potentially src/chat_handler.py) to remove/modify these behaviors and you will need to strongly/specifically instruct the model to use the MCP.

Additionally, when the agent tries to use skills, the context may lose the list of available MCP tools/integrations. If this occurs, try providing both the instructions and the youtube URL all in the first chat message and have the agent start the task without referring to skills.


# Note on OpenWebUI

You need to add the tool in the Admin Panel > Settings > Integrations. You cannot add it using the per-user settings. (It will fail with a vague error.)
