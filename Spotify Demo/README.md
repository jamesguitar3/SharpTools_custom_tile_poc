# Spotify Demo
<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#overview">Overview</a>
    </li>
    <li>
      <a href="#quick-version">Quick Version (easy)
    </li>
    <li>
      <a href="#full-developer-version">Developer Version (advanced)</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a>
            <ul>
                <li><a href="#create-spotify-developer-app">Create Spotify Developer App</a></li>
                <li><a href="#authorize">Authorize</a></li>
                <li><a href="#get-token">Get Refresh Token</a></li>
            </ul>
        </li>
        <li><a href="#create-custom-tile-in-sharptools">Create Custom Tile in SharpTools</a></li>
        <li><a href="#deploy-custom-tile">Deploy Custom Tile</a></li>
      </ul>
    </li>
  </ol>
</details>

## Overview

This is a proof of concept of using SharpTools Custom Tile feature to create a dashboard tile interacting with Spotify by means of a bespoke Spotify developer app and associated token. 


> Please note that this demo code uses Spotify Web APIs, and some of them require the Spotify Premium account, such as play, pause, skip, prev, and etc. See [Spotify Web API Reference](https://developer.spotify.com/documentation/web-api/reference/#category-player) for more details. 


![Quick View](/Spotify%20Demo/assets/quick_view.png)

[Demo Video](https://youtu.be/088h3uIvKkY)

*Please note that this is NOT an official integration between SharpTools and Spotify --  just a demo of Custom Tile feature. 

## Quick Version
The quick version of this integration uses a Spotify App that I've hosted on Glitch in order to make the initial setup much simpler. All you have to do is start the authentication process and then copy over your Refresh Token.

1. Navigate to [https://spotify-demo-auth.glitch.me/](https://spotify-demo-auth.glitch.me/)
   * Follow the instructions to authorize the demo app to have access to your Spotify account and make note of the 'Refresh Token'
2. Create a Custom Tile in the SharpTools Developer Tools
   1. Select the **HTML** type
   2. Create a setting named `refreshToken`
   3. Set the **Default Dimension** to `3x3`
   4. Copy and paste the custom tile code from `source_simplified_auth.html`
   5. Hit preview and copy the 'refreshToken' from earlier
3. Save your custom tile (and add it to your dashboard)

> If you want full control over the end-to-end process, check out the <a href="#full-developer-version">Full Developer Version</a> instead.

## Full Developer Version
### Prerequisites
#### Create Spotify Developer App
The first step is to create a Spotify app that is to be used to integrate with your Spotify account using the Spotify Web API
* Navigate to https://developer.spotify.com/dashboard/login and login with your Spotify credential
* Click "CREATE AN APP" at top-right to create your app
* Fill-in the App Name and Description, and check the agreements. Hit "Create"
  
  ![Create Spotify App](/Spotify%20Demo/assets/create_spotify_app.png)

* Take a note of the Client ID and Secret in the created app.
  
  ![Spotify App Client ID & Secret](/Spotify%20Demo/assets/spotify_app_client_id_secret.png)

* Click "Edit Settings" at top-right and add a Redirect URI, then hit "Add" and "Save". This redirect URI is only needed for the initial app authorization, and can be any URL, such as "https://google.com". We cannot include this app authorization in the custom tile, because Spotify Account Authorization page is not allowed to be embedded as an iFrame. 
  
  ![Add Redirect URI](/Spotify%20Demo/assets/add_redirect_uri.png)

#### Authorize
* Follow the url format below and replace the app client id (without parentheses), and redirect uri if you used something else in previous step. The scopes included in the url below are needed for this demo, and you may need add additional [scopes](https://developer.spotify.com/documentation/general/guides/scopes/) based on the features you needed.

  https://accounts.spotify.com/en/authorize?client_id=(your_app_client_id)&response_type=code&scope=playlist-read-private%20user-modify-playback-state%20user-read-playback-state&redirect_uri=https%3A%2F%2Fgoogle.com

* Paste the URL to a web browser. Login with your Spotify credential and hit "AGREE" to authorize the app you just created with the scopes we defined. 
  
  ![Authorize app](/Spotify%20Demo/assets/authorize_app.png)

* Once the page is redirected, take a note of the "code" in the URL, which is needed to get the Spotify access token 
  
  ![Redirected Code](/Spotify%20Demo/assets/redirected_code.png)

#### Get Token
* Use Postman, or other preferred tool, to send a POST request to request for the access and refresh token. You will need to grab your app client_id and client_secret from earlier in the format of "client_id:client_secret", and encode it in Base64 format (https://www.base64encode.org/).
  - Method: `POST`
  - url: `https://accounts.spotify.com/api/token`
  - Headers: `{Authorization Basic Base64_encoded_app_clientId:secret}` (Make sure the clientId:secret is base64 encoded)
  - Body: {
      grant_type: `authorization_code`,
      code: (the code we got from the redirected URI in the previous step)
      redirect_uri: `https://google.com`
    }
    *note that the body needs to be `x-www-form-rulencoded` format.
  
  ![Postman Get Token](/Spotify%20Demo/assets/post_request_for_token.png)

* Take a note of the received refresh_token, as these will be needed in the custom tile settings.

### Create Custom Tile in SharpTools
* Navigate to [SharpTools User Page](https://sharptools.io/user), tap the user menu drop down and select "Developer Tools"
* Create a Custom Tile. Fill in the custom tile name, and choose `HTML` in the Type field.
* Hit "Add Settings" and add the following settings. (The key value must be exactly the same as shown in the screenshot.)

  ![Custom Tile Settings](/Spotify%20Demo/assets/configure_custom_tile.png)

* Set default dimension to `3 x 3`   
* Copy & paste the HTML code from `source.html` to the page and hit "SAVE"

### Deploy Custom Tile
* Go to the desired dashboard, Edit, and Add Item. Tap "Custom Tile" in the Other section. 

  ![Add Custom Tile](/Spotify%20Demo/assets/add_custom_tile.png)

* Edit the tile and fill-in the Refresh Token, App Client ID and Secret we got from previous steps. 

  ![Edit custom tile](/Spotify%20Demo/assets/edit_deployed_custom_tile_settings.png)

* Save

That's it. You can now choose an available device, select a playlist and play the music from this custom tile integrated with your Spotify account.  😀