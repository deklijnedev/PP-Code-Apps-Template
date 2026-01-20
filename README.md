How to create new code apps project?

Prerequisities:
- VSCODE
- Power Platform Extension
- Power Platform CLI working
- Node installed

Steps for new code apps project:
- Init new react project
    - npm create vite@latest POC-Code-Apps -- --template react-ts
    - cd POC-Code-Apps
- pac code init --displayName "POC-Code-Apps"
    - Creates power.config.json
- npm install --save "@microsoft/power-apps"

Steps to run code app in Power Platform environment

1) Change package.json
    change scripts object dev to:
        start pac code run && vite


Potential errors
1) Error 'localhost refused to connect'

Steps to reproduce:

npm run dev (of npm start).

Open de URL die de PAC CLI spuwt (de Local Play URL).

ERROR: De browser zegt "localhost refused to connect" of toont een grijs vlak in het iframe.

Fix (The "Makkie Ouwe" Fix): Pas je package.json scripts aan naar:

JSON
"scripts": {
  "start": "start pac code run && vite --port 3000 --strictPort"
}
Waarom dit werkt (De technische 'kanker' details):

Port Alignment: De Power Apps SDK en de PAC-tunnel luisteren standaard op poort 3000. Vite pakt standaard 5173, waardoor ze langs elkaar heen praten.

StrictPort: Zonder --strictPort springt Vite naar 3001 als poort 3000 even bezet is (bijv. door een oude ghost-process). Dan weigert de Power Apps host de verbinding omdat de tunnel op de verkeerde poort doodloopt.

PAC Runtime: Door start pac code run vóór vite te zetten, zorg je dat de Microsoft-omgeving (die de data-connecties regelt) al "luistert" voordat je React-bundel de eerste API-calls probeert te maken.

Pro Tip voor je TODO: Als het dan nóg weigert: open http://localhost:3000 even handmatig in een nieuw tabblad in hetzelfde browserprofiel waar je Power Apps open hebt staan. Hiermee dwing je de browser om het lokale (vaak onbeveiligde) certificaat te accepteren voor dat iframe.
