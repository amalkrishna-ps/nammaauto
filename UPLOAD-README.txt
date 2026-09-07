NAMMA AUTO - STATIC WEB GAME

Entry point: index.html at the root of this ZIP.
Upload/extract the complete archive, keeping assets/ beside index.html.
The game is already built. No npm install, build step, backend or API keys needed.
Serve it over HTTPS (or localhost HTTP), not by opening it with a file:// URL.
Assets use relative URLs, including when hosted under a nested path.

Requirements: desktop browser with WebGL 2 and hardware acceleration; keyboard.
If embedded, permit JavaScript and WebGL in the hosting iframe. Audio unlocks on
the Start Mission click. The game pauses when the tab loses focus.

W / arrows: drive. S: brake/reverse. Space: emergency brake. R: recover.
C: camera. H: horn. M: mute. Escape: pause/resume.
Deliver the passenger to Small Brewsky Pub (829m) within 58 seconds.
The auto wears out at six potholes OR ten hits. Collision audio has no voices.

The entry page is an instant, static illustrated preview. It needs neither WebGL
nor external fonts to display, and starts no animation loop. Click OPEN GAME to
load the 3D engine, then START MISSION to drive. All resources are bundled locally.
Personal-best scores are stored only in browser localStorage, when permitted.
No analytics, accounts, server data storage or external game assets are required.
See THIRD-PARTY-NOTICES.txt for the bundled Three.js license.

Matches VibeHub's supplied requirements: pre-built static site, index.html at the
archive root, no server-side dependencies, databases, or web workers. Vite/vanilla
JavaScript output; no custom manifest. Below the stated 300 MB upload limit.
The lightweight preview mitigates screenshot startup delays, but a server-side
VibeHub capture failure cannot be ruled out without retrying the upload.