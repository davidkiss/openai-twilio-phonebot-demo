# OpenAI Realtime API + Twilio Phone Bot Demo

## Introduction

This is a simple demo on how to use the [OpenAI Realtime API](https://platform.openai.com/docs/guide/realtime-api/overview) with [Twilio](https://www.twilio.com/docs/voice/tutorials/how-to-respond-to-incoming-phone-calls/node) to create a phone bot that can accept incoming phone calls and use OpenAI to generate responses in real-time.

It was created based on the `websocket-server` component in OpenAI's sample git [repo](https://github.com/openai/openai-realtime-twilio-demo).

## Stack

- Typescript
- Node.js
- Express.js
- OpenAI Realtime API

## What You'll Need

- An [OpenAI API key](https://platform.openai.com/account/api-keys)
- A [Twilio account](https://www.twilio.com/try-twilio) with an active phone number
- [ngrok](https://ngrok.com/) installed to test locally
- [pnpm](https://pnpm.io/) installed - alternatively, you can use npm or yarn

## Setup

1. Install dependencies: `pnpm install`
2. Run ngrok to make our server available to Twilio: `ngrok http 8081`
3. Rename the `.env.example` file to `.env`
4. Update the `.env` file with the following environment variables:
   - `PUBLIC_URL`: The Forwarding URL of ngrok (e.g. https://54c5-35-170-32-42.ngrok-free.app)
   - `OPENAI_API_KEY`: Your OpenAI API key
   - `OPENAI_VOICE_KEY`: The key of the voice you want to use
   - `AI_INSTRUCTIONS`: The instructions you want to give to the AI
   - `AI_GREETING_PROMPT`: The greeting prompt you want to use
5. Run the server: `pnpm run dev`
6. Go to the Twilio Console](https://console.twilio.com/)
7. Navigate to Phone Numbers → Manage → Active numbers
8. Select your phone number
9. Under “Voice Configuration”, set the webhook for incoming calls to: https://your-ngrok-url.ngrok-free.app/twiml
10. Set the HTTP method to POST
11. Call the Twilio number associated with your account

## How it works

1. When a user calls the Twilio number, Twilio will make a POST request to the `/twiml` endpoint (see `src/server.ts`)
2. The `/twiml` endpoint will return a [TwiML](https://www.twilio.com/docs/voice/tutorials/how-to-respond-to-incoming-phone-calls/node) response with a Websocket URL that points to our server
3. Twilio will use the Websocket URL to send user's audio to our server
4. Our server will send the audio to the OpenAI Realtime API using a second websocket connection (see `src/sessionManager.ts`)
5. The OpenAI Realtime API will respond with audio to our server
6. Our server will send the audio to the Twilio Websocket URL (see `src/sessionManager.ts`)
7. Twilio will send the audio to the user
