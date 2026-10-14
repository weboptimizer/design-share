# design-share

A small, static way to share designs with clients that looks good and stays private. Each design is usually a working HTML prototype that clients can click through, not a static image.

It's the code behind [design.weboptimizer.agency](https://design.weboptimizer.agency): a branded holding page with an access code field. Each client gets a code; the code opens their design, a clickable prototype in the browser. No code, no design.

## The problem

I needed to share designs with clients in a beautiful and private way. Emailing files or sending a public link didn't feel right, and I didn't want to spend much time on it.

## Features

- Branded holding page ("Design, in progress") with an access code field and a call-to-action link.
- Share working, clickable HTML prototypes, not screenshots or PDFs: clients click through the real thing in their browser.
- Each design lives in its own folder; the folder name is the access code (`/your-code/`).
- Pure static HTML, CSS and a few lines of JavaScript. No build step, no database, no server code.
- Works with self-contained single-file HTML designs (fonts and assets bundled in).
- Folder listing off and search engines kept out (`.htaccess`, `robots.txt`, `noindex`).

## How it works

1. A visitor opens the holding page and types an access code.
2. The script checks the code only contains letters, numbers, `-` or `_`, then sends them to `/<code>/`.
3. If a folder with that name exists, they see the design. If not, they get the server's 404.

The access code is a hard-to-guess folder name, not a password. Use long, random codes, and don't put anything truly confidential behind it.

## Setup

1. Clone the repo:
   ```bash
   git clone https://github.com/<your-user>/design-share.git
   cd design-share
   ```
2. Edit `public/index.html` to use your own branding: name, logo, colours and the call-to-action link (it points to `https://weboptimizer.agency` by default).
   The page is a bundled single file; the readable HTML and CSS are in the `__bundler/template` script near the bottom.
3. Replace `public/favicon.png` with your own.
4. Try it locally:
   ```bash
   cd public && python3 -m http.server 8080
   ```
   Open http://localhost:8080 and enter `example-access-code`.

## Usage: sharing a design

1. Export your design as a working HTML prototype: a self-contained `index.html`, with assets inlined or alongside it. Links and interactions keep working, so clients can click through it.
2. Pick a long, random access code, e.g. `acme-7f3k9q`.
3. Create `public/<code>/` and put the design inside it.
4. Upload the folder to your server and send the client the link and their code.
5. To revoke access, delete or rename the folder.

Real client folders are ignored by git (see `.gitignore`), so they never end up in your repo.

## Deployment

Any static host that serves folders works. I run it on **Cloudways**, which keeps it simple: create a PHP application, point a subdomain at it, add free SSL, and upload the contents of `public/` to the app's `public_html` over SFTP.

👉 [Try Cloudways](https://www.cloudways.com/en/?id=1800578)

> **Affiliate disclosure:** the Cloudways link above is an affiliate link. If you sign up through it I may earn a commission, at no extra cost to you. I only recommend it because it's what I use.

### Optional: deploy from the command line

Copy `.env.example` to `.env`, fill in your server details (never commit `.env`), then:

```bash
set -a; . ./.env; set +a
rsync -avz --delete --exclude '.git' public/ "$DEPLOY_USER@$DEPLOY_HOST:$DEPLOY_PATH/"
```

Note: `--delete` removes files on the server that aren't in your local `public/`, so keep your client folders locally too, or drop that flag.

## License

MIT. See [LICENSE](LICENSE).
