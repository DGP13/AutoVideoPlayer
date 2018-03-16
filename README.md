# AutoVideoPlayer
Play/pause videos automatically in UITableview when an UITableViewCell is in focus, videos can be easily embedded in any UITableViewCell subclass.
Can be easily extended to support UICollectionView

* Easily implement video player in any UITableView subclass
* Easily pause/play any video
* Videos are cached in memory and will be removed when there is memory pressure
* Highly performant
* Works when the app comes again from background

It can also be used to play videos in any subclass of UIView.

## Demo
![](https://i.imgur.com/Q4ElIJt.gif)


## Download
Drag and drop the VideoPlayLibrary folder in your project
## Usage

#### Adopt ASAutoPlayVideoLayerContainer protocol in your UITableviewCell subclass like below.

```
var videoLayer: AVPlayerLayer = AVPlayerLayer()
    
var videoURL: String? {
    didSet{
        if videoURL != nil{
            ASVideoPlayerController.sharedVideoPlayer.setUpNewPlayerObjectForURL(url: videoURL!)
        }
        videoLayer.isHidden = videoURL == nil
    }
}
```
Implement following method to return the visible height of the UITableViewCell
```
func visibleVideoHeight() -> CGFloat {
  //return visible height of the Video Player layer
}
```

#### ViewController Code

Put following code in viewDidLoad
```
NotificationCenter.default.addObserver(self, selector:#selector(self.appEnteredFromBackground), name: NSNotification.Name.UIApplicationWillEnterForeground, object: nil)
```

Add following code to play/pause when view appears/disappears
```
override func viewWillDisappear(_ animated: Bool) {
    super.viewWillDisappear(animated)
}

override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)
    pausePlayeVideos()
}
```
Add following method

```
Implement following methods
@objc func appEnteredFromBackground() {
    ASVideoPlayerController.sharedVideoPlayer.pausePlayeVideosFor(tableView: tableView, appEnteredFromBackground: true)
}

func pausePlayeVideos(){
    ASVideoPlayerController.sharedVideoPlayer.pausePlayeVideosFor(tableView: tableView)
}
```

Add following code in UITableView delegate and datasource methods
```
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    //if cell adopts ASAutoPlayVideoLayerContainer protocol then
    //set videoURL if you want to show video or else nil
}

func tableView(_ tableView: UITableView, didEndDisplaying cell: UITableViewCell, forRowAt indexPath: IndexPath) {
    if let videoCell = cell as? ASAutoPlayVideoLayerContainer, let _ = videoCell.videoURL {
        ASVideoPlayerController.sharedVideoPlayer.removeLayerFor(cell: videoCell)
    }
}
```
Add following code to pause/play videos when scroll stops
```
func scrollViewDidEndDragging(_ scrollView: UIScrollView, willDecelerate decelerate: Bool) {
    if !decelerate {
        pausePlayeVideos()
    }
}
func scrollViewDidEndDecelerating(_ scrollView: UIScrollView) {
    pausePlayeVideos()
}
```
