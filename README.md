const clientId = '77a0d5c3e2424ff9a3e07edf33458a04';
const redirectUri = 'https://kimnamjoonshoe.github.io/spotify-song-skipper/callback';  // Update this line
const scopes = [
    'user-read-playback-state',
    'user-modify-playback-state',
    'user-read-currently-playing'
];
// rest of your JavaScript code remains unchanged

];

document.getElementById('login-button').addEventListener('click', () => {
    const url = `https://accounts.spotify.com/authorize?client_id=${clientId}&redirect_uri=${redirectUri}&scope=${scopes.join('%20')}&response_type=token&show_dialog=true`;
    window.location = url;
});

window.onSpotifyWebPlaybackSDKReady = () => {
    const token = getTokenFromUrl();
    if (!token) return;

    const player = new Spotify.Player({
        name: 'Web Playback SDK',
        getOAuthToken: cb => { cb(token); },
        volume: 0.5
    });

    player.addListener('ready', ({ device_id }) => {
        console.log('Ready with Device ID', device_id);
    });

    player.addListener('not_ready', ({ device_id }) => {
        console.log('Device ID has gone offline', device_id);
    });

    player.addListener('player_state_changed', state => {
        if (!state) return;
        const track = state.track_window.current_track;
        const duration = track.duration_ms / 1000; // Convert to seconds
        const skipTime = (duration / 2) + 5;

        // Display the current song name
        document.getElementById('player').innerText = `Now playing: ${track.name} by ${track.artists.map(artist => artist.name).join(', ')}`;

        setTimeout(() => {
            player.nextTrack().then(() => {
                console.log('Skipped track:', track.name);
            });
        }, skipTime * 1000);
    });

    player.connect();
};

function getTokenFromUrl() {
    const hash = window.location.hash
        .substring(1)
        .split('&')
        .reduce((initial, item) => {
            if (item) {
                let parts = item.split('=');
                initial[parts[0]] = decodeURIComponent(parts[1]);
            }
            return initial;
        }, {});
    return hash.access_token;
}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Spotify Song Skipper</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="container">
        <h1>Spotify Song Skipper</h1>
        <p>Welcome! Log in to start skipping songs automatically.</p>
        <button id="login-button">Connect with Spotify</button>
        <div id="player"></div>
    </div>
    <script src="https://sdk.scdn.co/spotify-player.js"></script>
    <script src="app.js"></script>
</body>
</html>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@400;700&display=swap');

body {
    font-family: 'Montserrat', sans-serif;
    background-color: #1c1c1c;
    color: #fff;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    margin: 0;
}

.container {
    text-align: center;
    background: linear-gradient(135deg, #6a0dad, #8a2be2);
    border-radius: 10px;
    padding: 50px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
}

h1 {
    font-size: 2.5em;
    margin-bottom: 20px;
}

p {
    font-size: 1.2em;
    margin-bottom: 20px;
}

button {
    padding: 15px 30px;
    font-size: 1em;
    font-weight: bold;
    color: #fff;
    background-color: #4b0082;
    border: none;
    border-radius: 25px;
    cursor: pointer;
    transition: background-color 0.3s;
}

button:hover {
    background-color: #6a0dad;
}

#player {
    margin-top: 20px;
    font-size: 1.2em;
}
