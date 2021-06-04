## Guitaraoke

I never liked karaoke. A single "performer" in front of a non-interested crowd.  
But I like to play guitar and optionally sing along. And I found a buddy guitarist and we can play and sing together.  
I even found some more friends that like to sing along (mostly when drunk).  
And now we have a problem. Nobody remembers the lyrics and the chords.  
I am a programmer and every problem looks like it has a software solutions.  
Enter `Guitaraoke`: it is like karaoke but with added guitar chords. 

## guitaraoke videos

First I created the videos. In the video editor `ShotCut` for Win10, I put together the audio of the song and copied the lyrics and chords I found on the internet. I tried to show the lyrics and chords a little bit earlier so we can prepare to sing-along and play-along guitar. It is great that I can now modify the chords and the lyrics if I like it different.  

## only mobile

When we have a party often we don't have a big TV for everybody to see the lyrics. But everybody have a smartphone.  
I would like that everybody easily opens the guitaraoke mp4 and very important that all the smartphones play it in sync.  

## ideas

My first idea was to have a web server (on the internet somewhere) with the songs. But the party is sometimes in the woods or mountains without a good or any internet connection. Even in places with a mobile-data internet connection it can cost money to play videos on some cell phone plans.  Let avoid this.  
When we sing/play we are always near to each other. We can make a local wifi network using the smartphone wifi hot-spot. The smartphones can then see each other and the connection is very fast and costs nothing.  
My second idea was to have a web server on my android smartphone. There are some ready made apps in the Google Play Store, but I wanted a web server I can program with some more functionality like having all smartphones in sync. I don't want to make a native android app. I just try to avoid it. You must use the Android Studio, a language I don't know (java or kotlin) and a IDE user interface that is strange to me.  
My third idea was to use Termux a "Linux terminal" for android. Then make a CLI program in Rust (for the web-server), compile it for the target armv7-linux-androideabi. But it was complicated and other users will not like to use it.  
My forth idea was to use webrtc for peer-to-peer communication. But it must have some sort of signaling server. So we are back on the "server on Android" problem.  

# GuitaraokeServer

My final solution is to make a Web+WebSocket server for Android. Exactly what I wanted to avoid. Eh, ironic.  
I don't plan to publish it on Google Play, but I will create an APK for die-hard fans of Guitaraoke.  
The client will be a simple html/css/javascript project for any browser.

## Based on WebSocketChat

The basics I got from <https://github.com/JCAguilera/WebSocketChat>  
It crates a web server on the port 8080 and a WebSocket server on port 3000.
It uses [NanoHttpd](https://github.com/NanoHttpd/nanohttpd) for the Webserver, and [Java-WebSocket](https://github.com/TooTallNate/Java-WebSocket) for the WebSocket Server.
It has a very simple layout and it's pretty easy to use.
I will simplify it as much as possible. The server will be pretty stupid. It will just serve files and broadcast messages to all attached clients.  
The server has no knowledge at all what is going on.  
That is useful for more than just Guitaraoke. Maybe I will use the same concept for other projects of mine.
For debugging purposes I will print on the server app messages and debugging info.  

## Android Studio

There is no escape to use Android Studio for Android development. I use it on Win10.  
I attached my Lenovo tablet over USB. On the tablet in `Settings`-`About tablet` I clicked 7 times on the `Build number`. That enables the `Developer options`. Then in `Developer Options` I enabled `Stay awake` and `USB debugging`. I needed to try with different USB cables to make it work.  
Finally it shows in Android Studio in the Toolbar in `Running devices` before the `Run` button.  
I use an old Lenovo tablet for my server: Android 6.0 (API level 23).  

## Local network

This server will work only on a local network like 192.168.x.y. My local network (wi-fi) is the hotspot from my smartphone. I have my Lenovo Win10+WSL2 notebook and a lenovo tablet connected.  
It is possible to open the html file on one ip address and use the WebSocket on another ip address. This makes developing the html app faster: there is no need to publish the server to my android device only because the html has changed. But in the release version both server will be on the same ip address.  
Just to mention a strange behavior when the hotspot smartphone has no mobile-internet connection, it cannot see the local network. All other connected devices work just fine with the local network wi-fi.  

## Guitaraoke Client

The client project is inside the server project in  `GuitaraokeServer\app\src\main\assets\guitaraoke_client\`.  
I can use VisualStudioCode to edit this, because I like it more than the android studio for html/css/javascript.  
The `assets` folder is distributed with the server installation.  

## icons online

I created the android icons with this online service:  
<https://romannurik.github.io/AndroidAssetStudio/icons-launcher.html#foreground.type=clipart&foreground.clipart=android&foreground.space.trim=1&foreground.space.pad=0.25&foreColor=rgba(96%2C%20125%2C%20139%2C%200)&backColor=rgb(68%2C%20138%2C%20255)&crop=0&backgroundShape=square&effects=none&name=ic_launcher>

## android assets and ExternalStorage

Files that are distributed with the server are called assets. They are read-only and accessible with the the object AssetManager.  
I want the video files to be downloadable. The Leader can download any  
`xxx - guitaraoke.mp4`  
file from the internet. It will be saved in the ExternalStorage. I used the DownLoadManager object for that.  
The Guitaraoke Leader page `leader.html` list the video filesstored in the android device ExternalStorage.  

 

## play-flow

1. All the smartphones must be connected to the same local wifi network. Because the streaming will be fast without interruptions.  
2. The leader opens the GuitaraokeServer android app. It shows the ip address and port that everybody must use.  
3. The leader opens the browser with the server local ip address and the leader.html page.  
4. the followers open the browser on the server ip address and index.html page.  
5. The leader see how many followers are connected.  
6. The leader chooses the song. It broadcasts a msg to all connected followers with the name of the song.  
7. The followers load this song from the GuitaraokeServer and send a msg that the song is loaded.  
8. The leader see that the followers have downloaded the song.
9. The leader press Play and it sends a msg to all connected followers to start playing he song.  

## development

I work in Visual Studio Code on Win10. I have most of my projects in WSL2 Debian. So I will have this too. No other reason.  
This will be only static html/css/javascript files.  
In Win10 I can open the HTML file just by double-clicking it. Don't need to have a web server.  
Inside the javascript it will connect to the Guitaraoke WebSocket Server. That must be running on a known ip address+port. In my case I will have it running on my lenovo tablet. When the tablet screen is go to sleep, the server stops working. I modified the settings to `Sleep after 30 minutes of inactivity" and it works fine for me. The ip address of the tablet keeps changing and I need to change it in my browser and in my development index.html and leader.html.

## Problem

The first time I connect to the WebServer no problem. But when I reload the page it does not connect to websocket. Why?  

## TODO

ScrollView+TextView better for debugging instead of list of msg.
for leader
Web server directory listing
mp4 load for client
upload mp4 for leader
