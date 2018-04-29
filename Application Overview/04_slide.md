!SLIDE[bg=_images/backgrounds/white_bg.png]

# Cropping a Screenshot

* Now, we can utilize ImageMagick to crop down the screenshot to just
  the number of players alive.
* Rotisserie uses gm, an npm package which provides node.js bindings for
  GraphicsMagick and ImageMagick, makes this easy!

!SLIDE[bg=_images/backgrounds/white_bg.png] code

# Cropping a Screenshot

    @@@ javascript
    function cropScreenshot(options) {
      return new Promise((resolve, reject) => {
        log.info("cropping screenshot of stream: " + options.streamName);
        if (fs.existsSync(options.thumbnailsDir + options.streamName + ".png")) {
          gm(options.thumbnailsDir + options.streamName + ".png")
            .crop(22, 22, 1190, 20)
            .type("Grayscale")
            .write(options.cropsDir + options.streamName + ".png", function(err) {
              resolve(options);
              if (err) reject(err);
            });
          log.info("cropped screenshot of: " + options.streamName);
        } else {
          reject(new Error(options.streamName + ": input file not found"));
        }
      });
    }
    end

!SLIDE[bg=_images/backgrounds/white_bg.png]

# Cropping a Screenshot

* So, now we have a bunch of pictures that look something like this:

![number_alive](../_images/number_alive.jpg)
