# Earworm

Name the hit from one second of it. A phone-friendly music quiz with two games and two song decks:

- **Split Second:** hear 1 second of a big hit and type the artist, the song and the year. Not sure? Wait for 2 seconds from a different part of the song, then 3 seconds. You score 1 point for the artist, 1 for the song and 1 for the exact year (½ if you're within 2 years). Points are tripled if you lock in after 1 second and doubled after 2. Works with 2 or more players, or on your own for practice.
- **Timeline:** hear a hit and slot it into your own timeline of years, before, between or after your cards. If it's in the right spot you keep the card. Name the artist and the song too to earn a ★, which you can spend to skip a song you don't know. First to 5, 8 or 10 cards wins. Works with 2 or more players, or solo.

**Decks:** USA Hits (382 songs, 1960s to now) and K-pop (158 songs, 2000s to now). Answers are checked automatically. The checker forgives typos, missing "The", featured artists and Korean or English spellings. The host can tap any guess to mark it right or wrong.

## Two ways to play
- **One phone:** the phone plays the music, then gets passed round so everyone types their guess in private.
- **Rooms:** the host taps *Start a room* and shares the 4-letter code or invite link. The host's phone plays the music, so turn it up or connect a speaker. Everyone answers on their own phone at the same time. Phones can be on any mix of wifi and mobile data: room messages go through public MQTT relays over secure websockets (three at once, so one being down doesn't matter), so there's no server to run. The host's phone runs the game, so keep Earworm open on it. Anyone who drops can rejoin, and reloading a tab reconnects automatically. Room traffic passes through those public relays, so treat names and answers as public.

## Music
Songs play through the YouTube embedded player on the host's phone (or the single phone). Before the first song, the game asks for one tap on the player, because phones won't play sound until someone taps the player itself.

- 278 songs have a YouTube video saved. The rest need a **YouTube Data API key**: paste one into *⚙ YouTube settings* on the home screen. It's saved on that phone only and never in the page, so restrict the key to your site's address in Google Cloud. With a key, Earworm also checks that each saved video is the right song and finds another upload when one won't play.
- If a video won't play, the game skips to the next song and remembers it.
- Clip starts default to the song's shape: the 1-second clip lands about a third of the way in (usually the chorus), the 2-second clip later on, and the 3-second clip near the start. If a clip misses, tap *Clips sounded off?* on the answer screen to nudge it. Fixes are saved on that phone. *Copy* in settings exports them so they can be added to the deck for good.

A single self-contained `index.html` with no build step.

## Dev notes
- Decks live at the top of the first `<script>` in `index.html`, one song per line: `Title|Artist|Year|YouTube id|clip starts`. Other accepted spellings go after `;`, and artists credited together are joined with ` + `.
- `?yt=fake` swaps YouTube for a pretend player, for testing without sound or internet.
- `?net=p2p` uses direct WebRTC links via PeerJS instead of the relays. `?relay=wss://…,wss://…` points at other MQTT-over-websocket relays.
- `?net=local` swaps the network for a same-browser BroadcastChannel transport, so you can play a room across several tabs with no internet.
- `?peerhost=127.0.0.1:9000` (with p2p) points PeerJS at a self-hosted PeerServer (`npx peer --port 9000`).
