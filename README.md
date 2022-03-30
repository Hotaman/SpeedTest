# SpeedTest
A simple way to add a connection speed display to your existing website for trouble shooting UE issues.

##Overview
This repo is based on HowSlow. It is intended to help K-12 Schools implement connection speed monitoring. Two unmodified files from the HowSlow repo are utilized in this solution.

Currently, this repo shows how to add a simple connection speed display to your existing websites. This output can help resolve user experience issues caused by slow network connections.

Additional work is planned to add telemetry, storage and analysis for verifying compliance. I am exploring methods to add this tool to SIS and other tools commonly used by schools.

##How do I?
###Step One
Copy forPage.js and forSW.js to the root of your website.

This places the service worker at the root path of your website. It will intercept all requests and save resource timings from the browser. This data is used to estimate the connection speed and round trip time (RTT).

###Step Two
Add the script tags to instantiate the service worker:

    <script src="forPage.js"></script>
    <script>
        window.howslow = new HowSlowForPage('forSW.js');
    </script>

These script tags are placed in the body of your index.html (or whatever it is named on your site).

###Step Three
Add the following line where you want the speed display to be rendered on the page. I suggest putting it in the footer area. Add styling/layout to match your site. You can change the ids if you like, just make sure they match in Step 4. Feel free to change the tags as needed for your site. The only requirement here is that the id'd tags must be able to hold text (default is the 'unknown' text below).

    <p>
      Estimated bandwidth: <b id="bwDisplay">unknown <b>KBps<br>
      Estimated RTT: <b id="rttDisplay">unknown </b>ms
    </p>

###Step Four
We now have a service worker monitoring all requests and a place to display results. We just need to update the display with the latest timings. Add the following script to enable a timer that will update the display twice per second.

    <script>
        const bwDisplay = document.getElementById('bwDisplay');
        const rttDisplay = document.getElementById('rttDisplay');
        setInterval(function() {
            if (window.howslow) {
                bwDisplay.innerHTML = window.howslow.bandwidth || 'unknown ';
                rttDisplay.innerHTML = window.howslow.rtt || 'unknown ';
            }
        }, 500);
    </script>

This script is placed below Step 3 html tags so that when it runs, the ids can be found (first two lines). The function is called every 500ms which will update the display if the service worker is present.

Please refer to the included index.html file for an example of what your finished file should look like after the above steps are completed. Don't expect this to work well at all on a local machine server (localhost). Fetches happen way too fast for the service worker to respond correctly. You can check for errors locally, just expect to see results of 'unknown'.

Have Fun!
