# bitbucket-meteor-headless-browsers

A docker container for headless browser tests in bitbucket.

Currently only Firefox and Chrome are supported.

This is how the `bitbucket-pipeline.yml` could look for you:

    image: simonsimcity/bitbucket-meteor-headless-browsers:latest

    pipelines:
      default:
        - step:
            script:
              # Import all needed NPM packages
              - meteor npm install

              # Test the meteor-installation
              - meteor --version

              # Prepare for acceptance-tests
              - meteor reset
              - meteor npm run test-start > daemon.log &
              - meteor=$!

              # Start Xvfb
              - Xvfb :99 -ac -screen 0 1280x720x16 -nolisten tcp &
              - xvfb=$!
              - export DISPLAY=:99

              # Run all acceptance-tests in the different browsers
              - meteor npm run test-chimp-chrome-once
              - meteor npm run test-chimp-firefox-once

              # Kill the services needed for acceptance-tests
              - kill $xvfb
              - kill $meteor
