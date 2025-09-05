
## Setup


* Create an `.env` file with the contents of `.env.sample`
* Next, amend the freshly created `.env` with variables appropriate for the environment
* Following, run `docker compose up -d` 
* Once its booted succesfully, following the steps for each app

#### Deluge

* Login into Deluge (`::8112`) with password of `deluge`
* Change the password, make sure its strong!
* Preferences then Plugins and enable `Label`'s

### Radarr

* Goto Radarr (`::7878`)
* Use an authentication method and set a good username/password
* Goto `Settings` then `Download Clients` and add Deluge
* Goto `Media Management` and enable `Rename Movies` and add a root folder of `/movies`
* Finally goto `Settings` general `API Key` and copy it down, you will need it later for Prowlarr

### Sonarr

* Goto Radarr (`::8989`)
* Use an authentication method and set a good username/password
* Goto `Settings` then `Download Clients` and add Deluge
* Goto `Media Management` and enable `Rename Episodes` and add a root folder of `/tv`
* Finally goto `Settings` general `API Key` and copy it down, you will need it later for 


#### Prowlarr

* Goto prowlarr (`::9696`)
* Use an authentication method and set a good username/password
* Goto `Settings` then `Download Clients` and add `Deluge`
* Goto `Settings` then `Apps` and add an Radarr, use the API Key (localhost should work, don't change the server settings) and the same for `Sonarr` with its key.
* Goto `Indexers` and add Indexers and `Sync App Indexers`

#### Overseerr

* Follow the install setup using the api keys and localhosts as before.

## Variables

| Variable | Value | Format Type |
|---|---|---|
| OPENVPN_PROVIDER | private internet access | String |
| OPENVPN_USERNAME | USERNAME | String (Placeholder) |
| OPENVPN_PASSWORD | PASSWORD | String (Placeholder) |
| OPENVPN_REGION | REGION | String (Placeholder) |
| PLAINTEXT_DNS | 1.1.1.1 | IP Address |
| USER_ID | 0 | Integer |
| GROUP_ID | 0 | Integer |
| TIMEZONE | Europe/London | Timezone String |
| DOWNLOAD_PATH | ./downloads | File Path |
| CONFIG_PATH | ./config | File Path |
| MOVIE_PATH | ./movies | File Path |
| TV_PATH | ./tv | File Path |

## The Idea

It will inject the variables into the docker containers on runtime, this means you don't need to touch the compose file for most reasons 

## Disclaimer

This configuration is provided for educational and testing purposes only. It is not intended for production use or for any illegal activities. The user assumes all responsibility for any actions taken using this file. The author is not liable for any misuse, damage, or legal consequences that may arise from its use.