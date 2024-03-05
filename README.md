# Automated home media server

## Architecture

An architecture point of view

|              Fig.01 - architecture               |
| :----------------------------------------------: |
| ![architecture](architecture.png "architecture") |

<br>

Plex is a great choice for a media server and has many additional features that can enhance your media streaming experience.
In combination with other services such as [Radarr](https://radarr.video/), [Sonarr](https://sonarr.tv/),[qbittorrent](https://hub.docker.com/r/linuxserver/qbittorrent).

## Deployment

Setting up a media server can be a bit complex, but with the right tools and some planning, it can provide a powerful and convenient way to enjoy your media.<br>
Using Docker Compose to set up a media server is a great way to simplify the process and make it easy to manage your server. Docker Compose is a tool that allows you to define and run multiple containers as a single service.<br>
The main motivation behind the compose file is to have a centralized place where you can manage all the services for the media center.

Docker compose file brief walk through

- **Volumes** As you may notice, all the services have a folder named `provision`.
  Using a separate folder for storing the configuration files of your services can be a good idea. This will allow you to easily make changes to the configuration files and also helps you to keep your configs safe.

- **Networking** `media-network`
  This is the network that the services are connected to, it allows the services to communicate with each other.

Here is a summary of the steps went through to create the media server:

1. Create a Docker Compose file: Create a new file called `docker-compose.yml` and copy the example compose file I provided you with into it.

2. Folder structure: Create a folder called `content` and `provision` in the same directory as the compose file, this folder will be used to store your media files and persist service configurations.
   An example of media folders structure (in terms of the compose above), note that you will need to create the folders on the filesystem:

---

1. Start the services: Run the command `docker-compose up -d` in the same directory as the compose file to start the services defined in the compose file.

Overall, the compose file creates a powerful and versatile media server that can automate the process of downloading and managing your media files, while also providing a variety of features and tools to enhance the overall functionality and security of the server.

## Configurations

Configuration of the services.

Please keep in mind that setting up a media server can be a complex task, especially if you are new to the technology and require some level of technical knowledge. It may be necessary to consult additional resources and tutorials to properly set up and configure your media server.<br>
It is also important to keep in mind that downloading copyrighted content from torrents is illegal in most countries and could lead to legal action if caught.<br>
Please keep in mind that the order of configuration may vary depending on your personal preferences and the specific needs of your media server.<br>
And remember that configuring a media server can be a complex task and may require multiple iterations, so take your time to test the services and make sure everything is working as expected.

### Plex

Since Plex is the main service that will be used to view your media files, it's a good idea to set it up first. This includes creating your Plex account, setting up your media library, and configuring any additional settings you may need.

- Once your Plex container is running, you can access the Plex Web UI by navigating to `http://<your_host_ip>:32400/web` in your browser.
- You will be prompted to sign in or create a new account.
- After signing in, you'll be prompted to add your media folders.
- Once you've added your media, Plex will begin analyzing your media files, and then it will be ready to use.
- Enable `Scan my library automatically` and `Run a partial scan when changes are detected` from `Settings`>`Library` tab/

### qBittorrent

Access the Qbittorrent web interface at `http://<your_host_ip>:8080` to set up and configure your download client.

1. Change default credentials<br>
   The default credentials for the qBittorrent service provided in the LinuxServer's image are as follows:

   - Username: admin
   - Password: adminadmin<br>

   It's important to note that these are the default credentials and it's highly recommended that you change them as soon as possible. You can change the credentials by going to the qBittorrent Web UI, and then navigating to the `Settings`>`WebUI` tab, and then change the username and password fields.<br>
   It's also a good practice to use unique and strong passwords for all your services and update them regularly to ensure the security of your media server.

2. Downloads options (used for category-based save paths)  
   When logged in click he "gear icon" to open `Options`.  
   Under `Downloads tab`, `Saving Management section` configure as follows:

   - `Default Torrent Management Mode`: `Automatic` (This is required for category-based save paths to work)
   - `When Torrent Category changed`: `Relocate torrent`
   - `When Default Save Path changed`: `Relocate affected torrents`
   - `When Category Save Path changed`: `Relocate affected torrents`
   - `Default Save Path`: `/data/torrents`
   - Click `Save` at the bottom of the panel

3. Categories
   - Radarr<br>
     In he WebUI in the left menu expand `CATEGORIES` and right click on All, Select `Add category`.<br>
     In the `New Category` windows configure as follows:
     - Category: `radarr` (this must correspond to the category that is later configured in radarr)
     - Save path: `/data/torrents/movies`
     - Click `Add`
   - Sonarr<br>
     Repeat the process for Sonarr<br>
     - Category: `sonarr`(this must correspond to the category that is later configured in sonarr download client, dafault is sonarr-tv but will use sonarr)
     - Save path `/data/torrents/tv`

### Prowlarr

1. Credentials<br>
   First time access Prowlarr WEB UI you will be prompted to create credentials.
2. Indexers<br>
   The first thing to set up in Prowlarr is indexers.<br>
   You will add each indexer individually to Prowlarr, done from sidebar `Indexers` tab.<br>
   You may use priority when configure them.
3. Sync Profiles<br>
   Uder `Settings`>`Apps`, use the default profile "Standard"
4. Apps<br>
   Where we will configure Prowlarr to connect your other apps such as Radarr and Sonarr.<br>
   Navigate to `Settings`>`Apps` and choose:
   - Radarr
     You will need API Key (retrieve it from Radarr WEB UI `Settings`> `General` > `Security`), save it later use<br>
     Sync Level: Use the default, `Add and Remove Only`<br>
     Prowlarr Server: `http://prowlarr:9696` (using the container name, because of the defined network)<br>
     Radarr Server: `http://radarr:7878`<br>
   - Sonarr<br>
     Same steps as Radarr<br>
     API Key<br>
     Prowlarr Server: `http://prowlarr:9696`<br>
     Sonarr Server: `http://sonarr:8989`<br>

Additional resources:

- [Prowlarr Quick Start Guide (Official docs)](https://wiki.servarr.com/prowlarr/quick-start-guide)
- [Prowlarr - A must have for easy automation! (YouTube)](https://www.youtube.com/watch?v=5deZNf2WhwI)
- [Prowlarr is the Jackett alternative you need (unraid-guides.com)](https://unraid-guides.com/2022/04/29/prowlarr-is-the-jackett-alternative-you-need/)
- [Connect Prowlarr to Radarr (quickbox.io)](https://quickbox.io/knowledge-base/v2/applications/prowlarr/connect-prowlarr-to-radarr/)

### Radarr

Access the Radarr Web UI by navigating to `http://<your_host_ip>:7878` in your browser.<br>
Some of the listed configurations are optional, but definitely makes user experience whole new level and they are worth check.

1. Credentials<br>
   Add credentials `Settings`>`General`>`Security`
2. Download Clients<br>
   Located in `Settings`>`Download Clients` tab, click on the "Add" button to add a new download client, select "qBittorrent" from the list<br>
   Host: `qbittorrent`, not IP or localhost<br>
   Port: 8080 (default)<br>
   Username and Password<br>
   Category: radarr (should be fill as default)<br>
   Test and Save buttons
3. Media Management<br>
   Movie Naming<br>

   - In the `Media Management` tab, you can configure how Radarr should organize and rename your downloaded movies.
   - Enable `Rename Movies`
   - Naming pattern I found usefull, for example: `({Release Year}) {Movie Title} {Quality Full}` correspond to `(2010) The Movie Title Bluray-1080p Proper`<br>

   Enable `Unmonitor Deleted Movies`<br>
   Root Folders

   - `/data/media/movies/`
   - `/data/media/movies favorite/` (Optional)<br>
     Have a such folder where all my favorite movies to be in, so if i need to clean up space to inspect only the folder with the movies not in the favorites.<br>

Once you've finished configuring Radarr, you can start adding movies to your library by clicking on the "Add Movies" button on the top right corner of the page.<br>
You can also set up Radarr to automatically search for new movies by clicking on the "Calendar" button on the sidebar of the page.

### Sonarr

Access the Sonarr web interface at `http://<your_host_ip>:8989` to set up your Sonarr and configure your TV shows library.<br>
Sonarr configuration is straight forward, you may follow the guidelines from Radarr, in summary:

1. Credentials
2. Download Clients
   Host: `qbittorrent`<br>
   Category `sonarr`, default is `tv-sonarr`
3. Media Management<br>
   Episode Naming<br>
   - Season Folder Format: `{Series Year} Season {season}`<br>  
     Enable `Unmonitor Deleted Movies`<br>
     Root Folders
   - `/data/media/tv/`
