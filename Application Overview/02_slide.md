!SLIDE[bg=_images/backgrounds/white_bg.png]

# Recording a Stream

* Unfortunately, there is no good solution in node.js for recording raw twitch
  streams (that I am aware of; If you know of one, let me know!).
* Luckily, there is a python package called Livestreamer which makes this
  pretty easy.
* For each stream, we concurrently spawn a livestreamer process to record a
  second or two of footage, wait a few seconds, then kill it.
* Yes, this could be done better (workerpool comes to mind).
* Which reminds me of a certain quote...

!SLIDE[bg=_images/backgrounds/black_bg.png]

.blockwhite We write some hacks, and some of them become standards. This is the history of software engineering.

.blockteal Andrey Sibirev (@kobolog) - Dropbox

!SLIDE[bg=_images/backgrounds/white_bg.png] code

# Recording a Stream
    @@@ javascript
    function recordStream(options) {
      return new Promise((resolve, reject) => {
        log.info("recording clip of stream: " + options.streamName);
        const child = spawn("livestreamer", ["--yes-run-as-root", "-Q", "-f",
          "--twitch-oauth-token", process.env.token,
          "twitch.tv/" + options.streamName, "720p", "-o",
          options.clipsDir + options.streamName + ".mp4"]);
        setTimeout(function() {
          child.kill("SIGINT");
          log.info("recorded stream: " + options.streamName);
          resolve(options);
        }, 4000);
      });
    }
    end
