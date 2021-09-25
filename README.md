<br/>

<p align="center">
    <img src="https://raw.githubusercontent.com/SvenTiigi/YouTubePlayerKit/gh-pages/readme-assets/logo.png?token=ACZQQFS3DO5PMLZUDKA3VT3BKHYS4" width="30%" alt="logo">
</p>

<h1 align="center">YouTubePlayerKit</h1>

<p align="center">
    A Swift Package to easily play YouTube videos
</p>

<p align="center">
   <a href="https://github.com/SvenTiigi/YouTubePlayerKit/actions/workflows/ci.yml">
       <img src="https://github.com/SvenTiigi/YouTubePlayerKit/actions/workflows/ci.yml/badge.svg" alt="CI status">
   </a>
   <a href="https://sventiigi.github.io/YouTubePlayerKit">
      <img src="https://github.com/SvenTiigi/YouTubePlayerKit/blob/gh-pages/badge.svg" alt="Documentation">
   </a>
   <img src="https://img.shields.io/badge/platform-iOS%20%7C%20macOS-F05138" alt="Platform">
   <a href="https://twitter.com/SvenTiigi/">
      <img src="https://img.shields.io/badge/Twitter-@SvenTiigi-blue.svg?style=flat" alt="Twitter">
   </a>
</p>

<img align="right" width="307" src="https://raw.githubusercontent.com/SvenTiigi/YouTubePlayerKit/gh-pages/readme-assets/example-app.png?token=ACZQQFQCQOW4BAQ5BX3Q4Q3BKLRI2" alt="Example application">

```swift
import SwiftUI
import YouTubePlayerKit

struct ContentView: View {
    
    var body: some View {
        //  WWDC 2019 Keynote
        YouTubePlayerView(
            "https://youtube.com/watch?v=psL_5RIBqnY"
        )
    }
    
}
```

## Features

- [x] Play YouTube videos with just one line of code 📺
- [x] YouTube [Terms of Service](https://youtube.com/t/terms) compliant implementation ✅
- [x] Access to all native YouTube iFrame [APIs](https://developers.google.com/youtube/iframe_api_reference) 👩‍💻👨‍💻
- [x] Support for SwiftUI and UIKit 🧑‍🎨
- [x] Runs on iOS and macOS 📱 🖥
- [x] `async/await` support ⛓

## Example

Check out the example application to see YouTubePlayerKit in action. Simply open the `Example/Example.xcodeproj` and run the "Example" scheme.

## Installation

### Swift Package Manager

To integrate using Apple's [Swift Package Manager](https://swift.org/package-manager/), add the following as a dependency to your `Package.swift`:

```swift
dependencies: [
    .package(url: "https://github.com/SvenTiigi/YouTubePlayerKit.git", from: "1.0.0")
]
```

Or navigate to your Xcode project then select `Swift Packages`, click the “+” icon and search for `YouTubePlayerKit`.

## Usage

A YouTube player can be easily rendered when using `SwiftUI` by declaring a `YouTubePlayerView`.

```swift
import SwiftUI
import YouTubePlayerKit

struct ContentView: View {

    let youTubePlayer: YouTubePlayer = "https://youtube.com/watch?v=psL_5RIBqnY"

    var body: some View {
        YouTubePlayerView(self.youTubePlayer) { state in
            // Overlay ViewBuilder closure to place an overlay View
            // for the current `YouTubePlayer.State`
            switch state {
            case .idle:
                ProgressView()
            case .ready:
                EmptyView()
            case .error(let error):
                Text(verbatim: "YouTube player couldn't be loaded")
            }
        }
    }

}
```
> Check out the additional [`YouTubePlayerView`](https://github.com/SvenTiigi/YouTubePlayerKit/blob/main/Sources/View/YouTubePlayerView%2BInit.swift) initializer to place an overlay for a given state.

When using `UIKit` or `AppKit` you can make use of the `YouTubePlayerViewController`.

```swift
import UIKit
import YouTubePlayerKit

// Initialize a YouTubePlayer
let youTubePlayer = YouTubePlayer(
    source: .video(id: "psL_5RIBqnY")
)
// Initialize a YouTubePlayerViewController
let youTubePlayerViewController = YouTubePlayerViewController(
    player: youTubePlayer
)

// Example: Access the underlying iFrame API via the `YouTubePlayer` instance
youTubePlayer.getPlaybackMetadata { result in
    switch result {
    case .success(let metadata):
        print("Video title", metadata.title)
    case .failure(let error):
        print("Failed to retrieve metadata", error)
    }
}

// Present YouTubePlayerViewController
self.present(youTubePlayerViewController, animated: true)
```

## YouTubePlayer

A `YouTubePlayer` is the central object which needs to be passed to every YouTubePlayerView or YouTubePlayerViewController in order to play a certain YouTube video and interact with the underlying YouTube iFrame API. 

Therefore, you can easily initialize a `YouTubePlayer` by using a string literal as seen in the previous examples.

```swift
let youTubePlayer: YouTubePlayer = "https://youtube.com/watch?v=psL_5RIBqnY"
```

A `YouTubePlayer` generally consist of a `YouTubePlayer.Source` and a `YouTubePlayer.Configuration`.

```swift
let youTubePlayer = YouTubePlayer(
    source: .video(id: "psL_5RIBqnY"),
    configuration: .init(
        autoPlay: true
    )
)
```

### Source

The `YouTubePlayer.Source` is a simple enum which allows you to specify which YouTube source should be loaded.

```swift
// YouTubePlayer Video Source
let videoSource: YouTubePlayer.Source = .video(id: "psL_5RIBqnY")

// YouTubePlayer Playlist Source
let playlistSource: YouTubePlayer.Source = .playlist(id: "PLHFlHpPjgk72Si7r1kLGt1_aD3aJDu092")

// YouTubePlayer Channel Source
let channelSource: YouTubePlayer.Source = .channel(name: "iJustine")
```

Additionally, you can use a URL to initialize a `YouTubePlayer.Source`

```swift
let urlSource: YouTubePlayer.Source? = .url("https://youtube.com/watch?v=psL_5RIBqnY")
```
> When using a URL the `YouTubePlayer.Source` will be optional

### Configuration

The `YouTubePlayer.Configuration` allows you to configure various [parameters](https://developers.google.com/youtube/player_parameters) of the underlying YouTube iFrame player.

```swift
let configuration = YouTubePlayer.Configuration(
    // Disable user interaction
    isUserInteractionEnabled: false,
    // Enable auto play
    autoPlay: true,
    // Hide controls
    showControls: false,
    // Enable loop
    loopEnabled: true
)

let youTubePlayer = YouTubePlayer(
    source: "https://youtube.com/watch?v=psL_5RIBqnY",
    configuration: configuration
)
```
> Check out the [`YouTubePlayer.Configuration`](https://github.com/SvenTiigi/YouTubePlayerKit/blob/main/Sources/Configuration/YouTubePlayer%2BConfiguration.swift) to get a list of all available parameters.

### API

Additionally, a `YouTubePlayer` allows you to access the underlying YouTube player iFrame API in order to play, pause, seek or retrieve information like the current playback quality or title of the video that is currently playing.

> Check out the [`YouTubePlayerAPI`](https://github.com/SvenTiigi/YouTubePlayerKit/blob/main/Sources/API/YouTubePlayerAPI.swift) protocol to get a list of all available functions and properties.

#### Async/Await

Asynchronous functions for example `getPlaybackMetadata` can be invoked via a completion closure.
Therefore the `YouTubePlayerKit` exposes an `async/await` equivalent for each asynchronous function.

```swift
// Retrieve the current PlaybackMetadata via a completion closure
youTubePlayer.getPlaybackMetadata { result in
    switch result {
    case .success(let playbackMetadata):
        print(
            "Title", playbackMetadata.title,
            "Author", playbackMetadata.author
        )
    case .failure(let youTubePlayerAPIError):
        print("Error", youTubePlayerAPIError)
    }
}

// On iOS >= 15.0 and macOS >= 12.0 use await
let playbackMetadata = try await youTubePlayer.getPlaybackMetadata()
```

#### Playback controls and player settings

```swift
// Play video
youTubePlayer.play()

// Pause video
youTubePlayer.pause()

// Stop video
youTubePlayer.stop()

// Seek to 60 seconds
youTubePlayer.seek(to: 60, allowSeekAhead: false)
```

#### Events

```swift
// A Publisher that emits the current YouTubePlayer State
youTubePlayer.statePublisher

// A Publisher that emits the current YouTubePlayer PlaybackState
youTubePlayer.playbackStatePublisher

// A Publisher that emits the current YouTubePlayer PlaybackQuality
youTubePlayer.playbackQualityPublisher

// A Publisher that emits the current YouTubePlayer PlaybackRate
youTubePlayer.playbackRatePublisher
```

#### Playback status

```swift
// Retrieve a number between 0 and 1 that specifies the percentage of the video that the player shows as buffered
youTubePlayer.getVideoLoadedFraction { _ in }

// Retrieve the PlaybackState of the player video
youTubePlayer.getPlaybackState { _ in }

// Retrieve the elapsed time in seconds since the video started playing
youTubePlayer.getCurrentTime { _ in }

// Retrieve the current PlaybackMetadata
youTubePlayer.getPlaybackMetadata { _ in }
```

#### Load video

```swift
// Load a new video from source
youTubePlayer.load(source: .url("https://youtube.com/watch?v=psL_5RIBqnY"))
```

#### Update Configuration

```swift
// Update the YouTubePlayer Configuration
youTubePlayer.update(
    configuration: .init(
        showControls: false
    )
)
```
> Note: updating the `YouTubePlayer.Configuration` will result in a reload of the entire YouTubePlayer

#### Changing the player volume

```swift
// Mutes the player
youTubePlayer.mute()

// Unmutes the player
youTubePlayer.unmute()

// Retrieve Bool value if the player is muted
youTubePlayer.isMuted { _ in }

// Retrieve the player's current volume, an integer between 0 and 100
youTubePlayer.getVolume { _ in }

// Sets the volume
youTubePlayer.set(volume: 50)
```

#### Retrieving video information

```swift
// Retrieve the duration in seconds of the currently playing video
youTubePlayer.getDuration { _ in }

// Retrieve the YouTube.com URL for the currently loaded/playing video
youTubePlayer.getVideoURL { _ in }

// Retrieve the embed code for the currently loaded/playing video
youTubePlayer.getVideoEmbedCode { _ in }
```

#### Playing a video in a playlist

```swift
// This function loads and plays the next video in the playlist
youTubePlayer.nextVideo()

// This function loads and plays the previous video in the playlist
youTubePlayer.previousVideo()

// This function loads and plays the specified video in the playlist
youTubePlayer.playVideo(at: 3)

// This function indicates whether the video player should continuously play a playlist
youTubePlayer.setLoop(enabled: true)

// This function indicates whether a playlist's videos should be shuffled
youTubePlayer.setShuffle(enabled: true)

// This function returns an array of the video IDs in the playlist as they are currently ordered
youTubePlayer.getPlaylist { _ in }

// This function returns the index of the playlist video that is currently playing
youTubePlayer.getPlaylistIndex { _ in }
```

#### Controlling playback of 360° videos

```swift
// Retrieves properties that describe the viewer's current perspective
youTubePlayer.get360DegreePerspective { _ in }

// Sets the video orientation for playback of a 360° video
youTubePlayer.set(
    perspective360Degree: .init(
        yaw: 50,
        pitch: 20,
        roll: 60,
        fov: 10
    )
)
```

#### Setting the playback rate

```swift
// This function retrieves the playback rate of the currently playing video
youTubePlayer.getPlaybackRate { _ in }

// This function sets the suggested playback rate for the current video
youTubePlayer.set(playbackRate: 1.5)

// This function returns the set of playback rates in which the current video is available
youTubePlayer.getAvailablePlaybackRates { _ in }
```

## Credits
- [youtube/youtube-ios-player-helper](https://github.com/youtube/youtube-ios-player-helper)

## License

```
YouTubePlayerKit
Copyright (c) 2021 Sven Tiigi sven.tiigi@gmail.com

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
```
