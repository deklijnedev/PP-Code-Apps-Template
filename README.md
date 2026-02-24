How to create new code apps project?

Prerequisities:
- VSCODE
- Power Platform Extension
- Power Platform CLI working
- Node installed
---------------------------------------------------------------------------
Steps for new code apps project:

**1) Init new react project**
    - npm create vite@latest POC-Code-Apps -- --template react-ts
    - cd POC-Code-Apps

2) **Initialize Power Platform connection**
    - pac code init --displayName "POC-Code-Apps"
    - Creates power.config.json

3) **Install Dependencies**
    - npm install --save "@microsoft/power-apps"
    - **important:** last known working version for this template is "@microsoft/power-apps": "^0.3.21",

**4) Setup PowerApps connection (crucial step)**

    - Create PowerProvider.tsx: Maak dit bestand aan in de src folder. Dit zorgt voor de SDK-initialisatie (initialize()) voordat de app rendert.
    - Wrap main.tsx: Importeer de PowerProvider en wrap de <App /> component hierin.

---------------------------------------------------------------------------
Steps to run code app locally

**1) Change package.json**
    - change scripts object to:
        "scripts": {
          "start": "start pac code run && vite --port 3000 --strictPort"
        }

Steps to publish to Power Platform environment

1) npm run build
2) pac code push
   
--------------------------------------------------------
Potential errors
1) Error 'localhost refused to connect'

Steps to reproduce:

- npm run dev (of npm start).
    Open de URL die de PAC CLI spuwt (de Local Play URL).
    ERROR: De browser zegt "localhost refused to connect" of toont een grijs vlak in het iframe.

Fix : Pas je package.json scripts aan naar:

JSON
"scripts": {
  "start": "start pac code run && vite --port 3000 --strictPort"
}

Waarom dit werkt (De technische details):

Port Alignment: De Power Apps SDK en de PAC-tunnel luisteren standaard op poort 3000. Vite pakt standaard 5173, waardoor ze langs elkaar heen praten.

StrictPort: Zonder --strictPort springt Vite naar 3001 als poort 3000 even bezet is (bijv. door een oude ghost-process). Dan weigert de Power Apps host de verbinding omdat de tunnel op de verkeerde poort doodloopt.

PAC Runtime: Door start pac code run vóór vite te zetten, zorg je dat de Microsoft-omgeving (die de data-connecties regelt) al "luistert" voordat je React-bundel de eerste API-calls probeert te maken

--------------------------------------------------

### Error: 'ReferenceError: Wit Scherm na publishen Code App in PP omgeving
**Symptoom:** Je `main.tsx` geeft een foutmelding bij het compileren of de browser blijft wit omdat de SDK-handshake niet plaatsvindt.

**Oorzaak:**
De Power Platform CLI (`pac`) genereert de infrastructuur, maar niet de React-logic voor de SDK-initialisatie. Als je de `PowerProvider` niet handmatig aanmaakt en importeert in `main.tsx`, weet de app niet dat hij in een Power Apps container draait.

**De Fix:**
1. Maak de `PowerProvider.tsx` aan (zie stap 1.4).
2. Zorg dat de import in `main.tsx` exact naar dit bestand verwijst.
3. Controleer of de `<App />` volledig is "ingepakt" door de provider.
