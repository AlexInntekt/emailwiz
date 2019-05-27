# Email server setup script

*NOTE: If this line is here, I'm still testing this to even it out.*

I wrote this script during the grueling process of installing an setting up a web server.
It perfectly reproduces my successful steps to ensure the same setup time and time again.
Deviate from one command and everything might fall apart unless you know better!

I've linked this file on Github to a shorter more memorable address on my LARBS.xyz domain, so you can get it on your machine with this short command:

```
curl -LO larbs.xyz/mailserver.sh
```

Read this readme and peruse the script's comments before running it.
Expect it to fail.

## This script...

- Installs a dovecot/postfix mail server for your domain of choice
- Sets up sensible default mail boxes located in `~/Mail`
- Installs and sets up Spam Assassin
- Installs and sets up OpenDKIM which validates your emails so you can send to Google and other sites with picky spam filters

## This script does _not_

- ...install or use a mySQL databse, instead uses the traditional Unix/PAM/login system where every user can be an email address on the domain.
- ...set up a graphical interface for mail like Roundcube or Squirrel Mail. If you want that, you'll have to install it yourself. I just use [isync/msmtp/mutt-wizard](https://github.com/lukesmithxyz/mutt-wizard) to have an offline mirror of my email setup and I recommend the same. There are other ways of doing it though, like Thunderbird, etc.
- ...offer any frills. If you want to change something, open the script up and change some variables.

## Info

These are the main active ingredients:

- Postfix - SMTP server (that's for sending mail)
- Dovecot - IMAP server (that's for receiving/storing mail)
- Spam Assassin - does what it sounds like. You can configure the rules you want. Read the manual.
- OpenDKIM - This authenticates your server so big-name sites like Google won't consider _your_ mail spam.

## Requirements

- `apt purge` all your previous (failed) attempts to install and configure a mailserver. Get rid of _all_ your system settings for Postfix, Dovecot, OpenDKIM and everything else. This script builds off of a fresh install.
- A Debian *web server*. I suspect the script will run on Ubuntu as well prodided there aren't huge differences in the default setup.
- An *MX record* in your DNS settings that points to your own main domain/IP. Unless you have your own DNS server, you'll put this setting on your domain registrar's site. Look up their documentation on how to do this, but it's usually really easy.
- *SSL for your site's mail subdomain*, specifically for mail.yourdomain.tld with Let's Encrypt. the script will look to Let's Encrypt's generated configs. If you have some other SSL system, you can manually change the SSL locations in the script before running it and it should be fine.
- After the script runs, you'll have to add an *additional DNS TXT record* which involved OpenDKIM key that it generates during the script.

## Don't get offended by this section

My intention is to have this script working for me on my Debian web server which I have with Vultr.
Different VPS hosts or distros might have a startup config that's a little different and I'm sure as heck not going to make sure everything works on every possible machine out there, please do not even ask.
If a lot of people try this script and see that it werks as expected everywhere, then I might try to label it as such and try to make it universal, but think of this script as a script that works on my exact setup than has some educational comments for the uninitiated and only might work as intended.

Configuring an email server is a living nightmare and that's why I made this script so I wouldn't have to do it again.
Don't ask me to configure your email server unless you are paying me big bucks to do it.
With this script and the comments in it, I've given you way more than I owe you.

If something is broken, start a PR to fix it, but *do not* open Issues about some problem you have unless you have figured out a solution and are offering a way for me to add a small change that will make this script more robust.
Your mindset in running this script should be "oh, look, it puts all the commands in a bash-readable format, so I can run it and troubleshoot errors as they come up.".
The script works for me and if it works for you without a problem, be thankful and feel lucky because setting up email is usually just so much less forgiving.

If you decide to start a VPS, specifically Vultr since I made this script and have tempered it most on their default setup,
use [this referal link of mine](https://www.vultr.com/?ref=7914655-4F) because you get a free $50 credit, and if you stay on the site, eventually I get a kickback too.
I honestly have to really strong preference of Vultr over other VPS providers, but they're about as cheap and reliable as it gets and if we can get free money, lol whatever click the link 👏👏.

## Details

- A user's mail is in `~/Mail`. Want a new email address? Create a new user and just add them to the mail group. Now they can send and receive mail. Look up using aliases too if you want for more cool stuff. Dovecot should autocreate the directories as needed.
- All dovecot configuration is just in `/etc/dovecot/dovecot.conf` instead of a dozen little config files. You can read those in `/etc/dovecot/conf.d/` for more info, but they are not called by default after running this script and the needed settings are edited into the main config.
- Your IMAP/SMTP server will me `mail.yourdomain.tld` and your ports will be the typical ones: 993 for IMAP and 587 for SMTP.
- Using non-encypted ports is not allowed for safety! The login is with plaintext because that's simpler and more robust given SSL's security.
- As is, you will use your name, not full email to log in. E.g., for my `luke@lukesmith.xyz` address, `luke` is my login. It seems that `luke` has to be my From: address in `msmtp` too, which is weird (it appears as a full address once I send mail though). I might look into changing this. Tell me if you know what to change.
