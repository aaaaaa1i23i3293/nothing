<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Video Site</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 0; padding: 0; background-color: #f4f4f4; }
        header { background-color: #ff0000; color: white; padding: 10px; display: flex; align-items: center; }
        header h1 { margin: 0; flex: 1; }
        #search { padding: 5px; width: 200px; }
        .container { display: flex; }
        .sidebar { width: 200px; background-color: #fff; padding: 10px; border-right: 1px solid #ddd; }
        .sidebar ul { list-style: none; padding: 0; }
        .sidebar li { margin: 10px 0; }
        .main { flex: 1; padding: 20px; }
        #player { background-color: #fff; padding: 20px; margin-bottom: 20px; text-align: center; }
        #player iframe { width: 100%; height: 400px; }
        .grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); gap: 20px; }
        .video-item { background-color: #fff; border: 1px solid #ddd; border-radius: 8px; overflow: hidden; cursor: pointer; }
        .video-item img { width: 100%; height: auto; }
        .video-item h3 { margin: 10px; font-size: 16px; }
    </style>
</head>
<body>
    <header>
        <h1>My Video Site</h1>
        <input type="text" id="search" placeholder="Search videos...">
    </header>
    <div class="container">
        <div class="sidebar">
            <ul>
                <li>Home</li>
                <li>Subscriptions</li>
                <li>Library</li>
            </ul>
        </div>
        <div class="main">
            <div id="player">
                <p>Select a video to watch.</p>
            </div>
            <div class="grid">
                <!-- Add your videos here -->
                <div class="video-item" data-embed="https://drive.google.com/file/d/1W4RUMOJkFM77lUSEBJDSM80BkxvxMEBE/view">
                    <img src="https://drive.google.com/thumbnail?id=ABC123&sz=w300" alt="Thumbnail">
                    <h3>My First Video</h3>
                </div>
                <div class="video-item" data-embed="https://drive.google.com/file/d/1u2z4uhAbaXyxh8hou_WbNZK6nwc5S2zS/view?usp=sharing">
                    <img src="https://drive.google.com/thumbnail?id=DEF456&sz=w300" alt="Thumbnail">
                    <h3>Adventure Trip</h3>
                </div>
                <!-- Add more as needed -->
            </div>
        </div>
    </div>
    <script>
        const videoItems = document.querySelectorAll('.video-item');
        const player = document.getElementById('player');
        
        videoItems.forEach(item => {
            item.addEventListener('click', () => {
                const embedUrl = item.getAttribute('data-embed');
                player.innerHTML = `<iframe src="${embedUrl}" width="640" height="480" allow="autoplay" frameborder="0" allowfullscreen></iframe>`;
            });
        });
    </script>
</body>
</html>
