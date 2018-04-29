!SLIDE[bg=_images/backgrounds/white_bg.png]

# Taking a Screenshot

* Now that we have some footage of a stream, we take a screenshot in order to
  get a single frame.
* fluent-ffmpeg , an npm package which provides node.js bindings for ffpmeg,
  makes this easy.

!SLIDE[bg=_images/backgrounds/white_bg.png] code

# Taking a Screenshot

    @@@ javascript
    function takeScreenshot(options) {
      return new Promise((resolve, reject) => {
        if (fs.existsSync(options.clipsDir + options.streamName + ".mp4")) {
          log.info("taking screenshot of stream: " + options.streamName);
          new FFMpeg(options.clipsDir + options.streamName + ".mp4")
            .takeScreenshots({
              timestamps: [0],
              folder: options.thumbnailsDir,
              filename: options.streamName + ".png",
            })
            .on("end", function() {
              resolve(options);
            })
            .on("error", function(err) {
              fs.unlinkSync(options.clipsDir + options.streamName + ".mp4");
              log.info("Deleted " + options.clipsDir
                            + options.streamName + ".mp4");
              reject(new Error("An error occurred: " + err.message));
            });
        } else {
          reject(new Error("File " + options.clipsDir
            + options.streamName + ".mp4 not found."));
        }
      });
    }
    end

!SLIDE[bg=_images/backgrounds/white_bg.png]

# Taking a Screenshot

* So, now we have a bunch of pictures that look something like this:

![pubg_number](../_images/pubg_number.jpg)
