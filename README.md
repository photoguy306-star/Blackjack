# # Edge — Multiplayer Blackjack (online friends mode)

Real online play needs a **server** that owns the shoe and deals to everyone, so the
games can't desync and nobody can cheat by drawing their own cards. This is that server
(Node + Socket.IO) plus a client page it serves.

Rules match the trainer: 6 decks, S17, DAS, no surrender, blackjack pays 3:2, Hi-Lo count
shown live and shared by all players.

## Files
- `server.js` — authoritative game server (shoe, dealer, turn order, settlement, count)
- `edge-multiplayer.html` — the client the server serves at `/`
- `package.json` — dependencies

## Run it locally
```bash
npm install        # installs socket.io + express
node server.js     # starts on http://localhost:3000
```
Open `http://localhost:3000` in a browser. One person clicks **Create room**, shares the
4-letter code; friends open the same URL and **Join** with that code.

> On the same Wi-Fi, friends can reach you at `http://YOUR-LOCAL-IP:3000`
> (e.g. `http://192.168.1.20:3000`). Find your IP with `ipconfig` / `ifconfig`.

## Play over the internet
Deploy `server.js` anywhere that runs Node and gives you a public URL:
- **Render / Railway / Fly.io / Heroku** — push the folder, set the start command to
  `node server.js`. They provide a `PORT` env var, which the server already reads.
- Then share `https://your-app.onrender.com` — create/join rooms from anywhere.

## How it works (the important part)
The server is the only place cards are drawn. Clients **never** draw — they send actions
(`hit` / `stand` / `double` / `split` / `place_bet`) and the server validates them:
- rejects actions when it isn't your turn,
- rejects illegal doubles/splits,
- deals from one shoe, hides the dealer hole card until reveal, and counts cards
  authoritatively so everyone sees the identical running/true count.

That single-source-of-truth design is what makes online multiplayer fair.

## Not included (yet)
- Insurance prompts in multiplayer (single-player has them)
- Reconnect-to-seat after a dropped connection (a disconnect currently stands the
  player's hand and frees the table)
- Persistent accounts / chips across sessions

These are straightforward to add on top of the same authoritative model.
