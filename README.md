# camera-dashboard
Starling Developer Connect API: Nest Camera Web Dashboard Example. Demonstrates connecting to the SDC API, and displaying
a dashboard of all your cameras with periodic snapshots.

Requires a [Starling Home Hub](https://starlinghome.io) with firmware 8.0 or above.

# Create an SDC API key for this app
1. Go to `setup.starlinghome.io` in your browser
2. Go to the *Starling Developer Connect* section, and make sure the HTTP server is enabled
3. Press *Create New API Key*, and give your key *Read* and *Camera* permissions

# Build and Run
1. `git clone https://github.com/starling-home-hub/camera-dashboard.git`
2. `cd camera-dashboard`
3. `npm install`
4. Edit `src/components/Dashboard.vue`, and add your hub's IP address and API key
5. `npm run serve`
6. Open a web browser, go to `http://localhost:8080` and enjoy!

# Output
The web app shows a dashboard of all connected Nest cameras, with regularly updating snapshots.
