# Send a Tweet for each new plane in PlaneFence
This utility enables tweeting of new events. It consists of a BASH shell script that monitors today's planes as written by PlaneFence, and sends out a tweet for every new plane using [Twurl](https://github.com/twitter/twurl).

There are three major parts to install this. Each of these parts is described below.
- Twurl is dependent on Ruby version 2.5 or greater, which is a later version than comes standard for the Raspberry Pi.
- You must apply for your own Twitter Developer Account and create an app.
- You must configure the BASH shell script to send the tweets

## Prerequisites
These instructions assume that you already installed [PlaneFence](https://github.com/kx1t/planefence). If you haven't, click on the link and do that first.
You also should have `git` installed, but you already did this when you installed PlaneFence.

## Installing Ruby 2.6 and Twurl

### Ruby 2.6 installation
We will use [Tom Black](https://gist.github.com/blacktm)'s excellent installation script for Ruby 2.6.
On your Raspberry Pi's command line, type the following. Please note that this may take a LONG time to install, compile, etc.
Be patient and get a cup of coffee or tea :). Oh - in case you aren't familiar with Linux compiling - there's a lot of text scrolling over
your screen. Ignore everything including warnings. 
```
bash <(curl -s https://gist.githubusercontent.com/blacktm/8302741/raw/install_ruby_rpi.sh)
```  
If successful, something like this will be shown:
```
  Installed ruby-2.6.4 to /home/pi/.rbenv/versions/2.6.4
  Reload the current shell to get access to rbenv using:
    source ~/.bashrc
  Finished in 0 hr, 20 min, and 30 sec
```
Now execute `.bashrc`:
```
source ~/.bashrc
```
### Twurl installation
On your command line, type:
```
gem install twurl
```
If successful, something like this is shown:
```
Successfully installed twurl-0.9.5
1 gem installed
```

## Signing up for a Twitter Development Account and getting Twitter Credentials

You'll need a registered Twitter application. If you've never registered a Twitter application before, do the following:

- If you need help, [here's a webpage](https://elfsight.com/blog/2020/03/how-to-get-twitter-api-key/) with an excellent graphical walk-through of what you need to do. In short, this is the same as doing the following:

- Sign-in using your Twitter account and then fill out the short form at https://apps.twitter.com/app/new.
  - If you've previously registered a Twitter application, it should be listed at https://apps.twitter.com/.
  - Once you've registered an application, make sure to set your application's Access Level to "Read, Write and Access direct messages", otherwise you'll receive an error that looks like this: `Error processing your OAuth request: Read-only application cannot POST`

- A mobile phone number must be associated with your account in order to obtain write privileges. If your carrier is not supported by Twitter and you are unable to add a number, contact Twitter using https://support.twitter.com/forms/platform, selecting the last checkbox. Some users have reported success adding their number using the mobile site, https://mobile.twitter.com/settings, which seems to bypass the carrier check at the moment.

  - Keep the page with your `Consumer API keys` open - you will need them in the next step.

Now, you're ready to authorize a Twitter account with your application. To proceed, type the following command at the prompt and follow the instructions (replace the numbers with your `API Key` and `API Secret Key` from above):

```
twurl authorize --consumer-key 1234YourConsumerKey6789 --consumer-secret 9876YourConsumerSecret4321
```

This command will direct you to a URL where you can sign-in to Twitter, authorize the application, and then enter the returned (7 digit?) PIN back into the terminal. This is a LONG URL that you need to copy and paste into your browser
```
Go to https://api.twitter.com/oauth/authorize?oauth_consumer_key=xxxxx&oauth_nonce=yyyy&oauth_signature=pppp&oauth_signature_method=HMAC-SHA1&oauth_timestamp=1589303820&oauth_token=qqqq&oauth_version=1.0 and paste in the supplied PIN
```
If all works fine, you should see a final message: `Authorization successful`.

## Installing and configuring PlaneTweet

Let's assume that you already have installed PlaneFence.
Just in case you have an older version, let's refresh your local copy with the new one.
This should NOT affect your configuration or changes that you already made to PlaneFence
Give the following commands:
```
cd ~/git/planefence
git pull
```
Now, we install PlaneTweet. Some of these commands may give an error if you previously created the directory. Ignore those errors:
```
sudo mkdir /usr/share/planefence
sudo chmod a+rwx /usr/share/planefence
cp scripts/planetweet.sh /usr/share/planefence
chmod a+rwx /usr/share/planefence/planetweet.sh
```
In case your default configuration doesn't use the directory structure of dump1090-fa, make the following change:
```
nano /usr/share/planefence/planetweet.sh
```
Look for `HTMLDIR=/usr/share/dump1090-fa/html/planefence` and change this to the location of your PlaneFence directory
Once you are done, you can exit with `CTRL-o` (to save) `CTRL-x` (to exit)

## Running PlaneTweet as a Service using Systemd

If you want to run PlaneTweet as a Service on your Raspberry Pi, to be autostarted whenever
your Raspberry Pi reboot, then do the following:
```
sudo cp /home/pi/git/planefence/systemd/planetweet.service /lib/systemd/system
sudo systemctl daemon-reload
sudo systemctl enable planetweet
sudo systemctl start planetweet
```
As with every Systemd Service, you can control PlaneTweet with the following commands, whenever you feel like it:
```
systemctl status planetweet # shows the status of PlaneTweet. You can check if it's still running
sudo systemctl stop planetweet # stops the PlaneTweet Service. It will restart upon reboot
sudo systemctl disable planetweet # stops the PlaneTweet Service from starting upon reboot
sudo systemctl start planetweet # starts the PlaneTweet Service after you stopped it
sudo systemctl restart planetweet # stops and then restarts the PlaneTweet Service

```
# Summary of License Terms
This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <https://www.gnu.org/licenses/>.

