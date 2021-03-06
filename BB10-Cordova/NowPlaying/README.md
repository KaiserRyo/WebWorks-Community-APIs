# NowPlaying #

This BlackBerry 10 Cordova Plugin plays music in the background.


## Contents ##

* [Native Extension](plugin/src/blackberry10/native) - BlackBerry 10 Native SDK Project
* [Sample Application](sample) - WebWorks 2.0/Cordova Project


## Including the feature in your application ##

This API can be installed from source or from the [Cordova Plugin Registry](http://plugins.cordova.io/). Installation from the registry is done through the following:

	cordova plugin add com.blackberry.community.nowplaying

or,
	
	webworks plugin add com.blackberry.community.nowplaying

Installation from source is the same but instead of the id ("com.blackberry.community.nowplaying"), use the file system path to the source plugin folder.


## How to build your native Plugin ##

1.  Clone this repo to your local machine

2.  Ensure the [BlackBerry 10 Native SDK](https://developer.blackberry.com/native/download/sdk) is correctly installed

3.  Import the native extension project into momentics. It is located at `/plugin/src/blackberry10/native` within **this project**

4.  Build the native extension project for all architectures: Right click on the project and choose "Build Configurations" > "Build All"


## Using the Plugin in an Application ##

Run nowplaying_build.sh from that directory.


## Javascript API ##

The api exports a global module as blackberry.community.nowplaying, as shown in `/plugin/src/blackberry10/native/src/NowPlaying_ndk.cpp`

### Object Methods ###

#### NowPlayingRequestPlayback() ####
Sets up the app for playing music and binds callbacks to the app.

@param input: a json object with methods that callback to the app.
The callback methods that must be specified are:

	- play: Fired when the track is played.
	- pause: Fired when the track is paused.
	- stop: Fired when the track is stopped.
	- next: Fired when the next track is invoked. Callback must invoke com.blackberry.community.nowplaying.play()
			with arguments for the next track.
	- previous: Fired when the previous track is invoked. Callback must invoke com.blackberry.community.nowplaying.play()
				with arguments for the previous track.
	- error: Fired when an internal error occurs.

@returns String: whether playback was requested (set up) successfully.

Example:

	var requestPlaybackButtonClick = function() {
		var jsonData = {
			play: sampleAsyncCallback,
			pause: sampleAsyncCallback,
			stop: sampleAsyncCallback,
			next: nextCallback,
			previous: previousCallback,
			error: sampleAsyncCallback
		};

		com.blackberry.community.nowplaying.NowPlayingRequestPlayback(jsonData);
	};

	var sampleAsyncCallback = function(data) {
		if (data) {
			console.log(data);
		}
	};

	var nextCallback = function(data) {
		sampleAsyncCallback(data);

		// Move track pointer.
		if (myTrackPointer < myPlaylist.length - 1) {
			myTrackPointer++;

			// Play the next track.
			play(myPlaylist, myTrackPointer);
		} else {
			console.log("At last track: can't go next.");
		}
	};

	var previousCallback = function(data) {
		sampleAsyncCallback(data);

		// Move track pointer.
		if (myTrackPointer > 0) {
			myTrackPointer--;

			// Play the previous track.
			play(myPlaylist, myTrackPointer);
		} else {
			console.log("At first track: can't go previous.");
		}
	};

	/***********************************
	 * App logic variables and methods
	 ***********************************/

	// Pointer to current track
	var myTrackPointer = -1;

	// Playlist of tracks
	var myPlaylist = [
		{
			trackURL: "http://www.pch.gc.ca/DAMAssetPub/DAM-hymChs-antSgn/STAGING/audio-audio/o-canada_1359474460106_eng.MP3",
			iconURL: "http://flaglane.com/download/canadian-flag/canadian-flag-small.jpg",
			metadata: {
				Title: "O Canada",
				Artist: "Canada",
				Album: "Canada's Favorites"
			}
		},
		{
			trackURL: "sounds/highhat.mp3",
			iconURL: "img/Hi-hat.jpg",
			metadata: {
				Title: "High Hat",
				Artist: "Drum Kit",
				Album: "Instruments"
			}
		}
	];

	// Helper method to play a specified track in a given playlist.
	var play = function(playlist, trackPointer) {
		var jsonData = {
			trackURL: playlist[trackPointer].trackURL,
			iconURL: playlist[trackPointer].iconURL,
			metadata: playlist[trackPointer].metadata,
			nextEnabled: trackPointer < playlist.length - 1,
			prevEnabled: trackPointer > 0
		};

		// Play the track.
		com.blackberry.community.nowplaying.NowPlayingPlay(jsonData);
	};

#### NowPlayingPlay() ####
Plays a specified track in the background.
Executes the callback method given to NowPlayingRequestPlayback.
Use this method to play the track if it isn't automatically restarted after the app playback is no longer preempted.

@param input: a json object with track details.
The details that must be specified are:

	- trackURL: URL of the track to play.
	- iconURL: URL of the icon to display on the volume overlay.
	- metadata: a json object with metadata details to display on the volume overlay.
				Can include title, artist, and album.
	- nextEnabled: boolean used to set whether or not the Next button should be
				   enabled on the volume overlay.
	- previousEnabled: boolean used to set whether or not the Previous button should be
					   enabled on the volume overlay.

@returns String: whether the track played successfully.

Example:

	var jsonData = {
		trackURL: "http://www.pch.gc.ca/DAMAssetPub/DAM-hymChs-antSgn/STAGING/audio-audio/o-canada_1359474460106_eng.MP3",,
		iconURL: "http://flaglane.com/download/canadian-flag/canadian-flag-small.jpg",
		metadata: {
					  Title: "O Canada",
					  Artist: "Canada",
					  Album: "Canada's Favorites"
				  },
		nextEnabled: true,
		prevEnabled: false
	};

	// Play the track.
	com.blackberry.community.nowplaying.NowPlayingPlay(jsonData);

#### NowPlayingPause() ####
Pauses the track if there is any in the background.
Executes the callback method given to NowPlayingRequestPlayback.

@returns String: whether the track paused successfully.

Example:
	com.blackberry.community.nowplaying.NowPlayingPause();


#### NowPlayingResume() ####
Resumes the paused track if there is any in the background.
Executes the callback method given to NowPlayingRequestPlayback.

@returns String: whether the track resumed successfully.

Example:
	com.blackberry.community.nowplaying.NowPlayingResume();


#### NowPlayingStop() ####
Stops the track if there is any in the background.
Executes the callback method given to NowPlayingRequestPlayback.

@returns String: whether the track stopped successfully.

Example:
	com.blackberry.community.nowplaying.NowPlayingStop();


#### NowPlayingNext() ####
Plays the next track according to the callback method given to NowPlayingRequestPlayback.

@returns String: whether the track was changed to next one successfully.

Example:
	com.blackberry.community.nowplaying.NowPlayingNext();


#### NowPlayingPrevious() ####
Plays the previous track according to the callback method given to NowPlayingRequestPlayback.

@returns String: whether the track was changed to previous one successfully.

Example:
	com.blackberry.community.nowplaying.NowPlayingPrevious();



## TODO ##

- These are in order of importance.
- Sizing is roughly x hours to investigate, at most 1 hour (left) to finish implementing, at most 1 hour to test.

#### MUST-HAVES ###
#### NICE-TO-HAVES ####

1. Provide an optional parameter to suppress the callbacks from playSlot(), pauseSlot(), and stopSlot(), so that actions can be done to change the internal state of the app without externalization to the interface.

2. Give NowPlayingNDK::NowPlayingError signal/slot parameters to specify error information. Can create an enum of general errors that can occur. Use this throughout the class.
	(In `/plugin/src/blackberry10/native/src/NowPlaying_ndk.cpp`)

3. Give the sample app more interesting callbacks, e.g. the sample app [here](https://github.com/blackberry/Cascades-Samples/tree/master/nowplaying) uses images.

4. Metadata
	(In `/plugin/src/blackberry10/native/src/NowPlaying_ndk.cpp`)

	- Album isn't showing.
	- Investigate putting more than just the Title, Artist, Album properties, even though [this](http://developer.blackberry.com/native/reference/cascades/bb__multimedia__nowplayingconnection.html#comment-1134791487) says


	> Comment 2 years ago from Oct 30, 2015. Wes Barichak: "Currently, the only metadata properties that are available are MetaData::Album, MetaData::Artist, and MetaData::Title, while the rest of the MetaData properties are ignored. This will likely change in the future though."

5. Icon
	(In `/plugin/src/blackberry10/native/src/NowPlaying_ndk.cpp`)

	- Investigate setting icons based on URLs even though [this](http://developer.blackberry.com/native/reference/cascades/bb__multimedia__nowplayingconnection.html#function-play) says


	> Comment 2 years ago from Oct 30, 2015. Theodore Mavrakis: "How can we pass an http url to use for the icon of a NowPlayingConnection?" Wes Barichak: "Currently, this is not possible. But, we will be looking at adding this functionality in a future release."

6. Volume Overlay
	(In `/plugin/src/blackberry10/native/src/NowPlaying_ndk.cpp`)

	- Investigate setting appropriate size for media notification area even though Tim mentioned


	> I don't think there's any control to change the size of the media notification area - it's set by the OS and the hardware.


## Disclaimer ##

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

