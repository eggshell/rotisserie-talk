!SLIDE[bg=_images/backgrounds/white_bg.png

# OCR Microservice

* We previously used Tesseract for Object Character Recognition.
* Ran as a separate container, responded to RESTful requests.
* Had a roughly 30% success rate, which was OK for POC but bad for anything
  else.
* Transparency of box containing number of players alive threw off Tesseract.

!SLIDE[bg=_images/backgrounds/white_bg.png]

# OCR Microservice

* In comes TensorFlow and Python!
* Ryan Moe was able to train a neural net specifically on cropped images we
  had obtained from running our app and storing them  in a Kubernetes persistent
  volume.
* The pre-trained model is provided at build time for the OCR container.
* Flexibility of container deployments allows us to deliver updates to models
  as easily as any other part of the app.
* Allows us to tell whether a game is in the lobby or not.
* Has improved the accuracy of the OCR up to 99%.

!SLIDE[bg=_images/backgrounds/white_bg.png] code

# Passing a file to the OCR Microservice

    @@@ javascript
    function ocrCroppedShot(options) {
      return new Promise((resolve, reject) => {
        let formData = {
          image: fs.createReadStream(__dirname
                                     + options.cropsDir.replace(".", "")
                                     + options.streamName + ".png"),
        };

        let requestOptions = {
          url: "http://" + process.env.ROTISSERIE_OCR_SERVICE_HOST + ":" + process.env.ROTISSERIE_OCR_SERVICE_PORT + "/process_pubg",
          formData: formData,
        };

        request.post(requestOptions, function(err, httpResponse, body) {
          if (err) {
            console.error("upload failed");
            reject(err);
          } else {
            let parsed = JSON.parse(body);
            let object = {};
            object.name = options.streamName;
            object.alive = parsed.number;
            resolve(object);
          }
        });
      });
    }

    end

# Do it Again

* Process is repeated on a fixed interval.
* Each step in the process is not allowed to begin until its previous process
  has its promise resolved.
* Each iteration will update the array of stream names and sort by their
  associated number of players alive.
* The stream with the lowest number of players alive will be determined to be
  the “most interesting”, and some client-side Javascript will refresh the
  stream iframe.
