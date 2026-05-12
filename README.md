# Bumblebee-Linux-Log-Analysis-SQLite-Forensics

## Objective
The objective of this investigation was to analyze Linux web server logs and a SQLite database to identify suspicious activity, trace the actions of an external contractor, and determine how administrative credentials were compromised in the Hack The Box “Bumblebee” Sherlock challenge.

### Skills Learned
- Linux log analysis (web server logs such as Apache/Nginx access logs)
- Identifying suspicious user activity in logs and databases
- Basic incident response and attack timeline reconstruction

### Tools Used
- DB Browser for SQLite — to explore and analyze the SQLite database dump
- Linux Terminal — for log analysis and command execution
- Web Server Logs (Apache/Nginx) — for tracking user and attacker activity
- grep — to search and filter relevant events from web server logs
- cut — to extract specific fields from log entries for easier analysis
## Steps
in this lab we will look for web server logs and sqlite3 database 

we will satrt by downlaod the Bumblebee files
<img width="1912" height="905" alt="{81EEA741-9DC7-4AFC-BEA4-36391A140113}" src="https://github.com/user-attachments/assets/2b731d25-b721-4bcf-8863-ea03fe60006a" />

after we download and extract the file we will get two files
- access.log
- phpbb.sqlite3
<img width="1920" height="947" alt="Screenshot_2026-05-11_18_24_08" src="https://github.com/user-attachments/assets/63f0d083-e1c7-4a59-8286-e2d5dfdf42bb" />

to analise phpbb.sqlite3 we should download DB Broswer for sql (i arredy download it :) )

now let satrt with access.log
since its server logs then the most importent request are GET,POST so we will look for both of that request
and we will start with POST
<img width="1920" height="947" alt="Screenshot_2026-05-11_18_29_52" src="https://github.com/user-attachments/assets/756252b6-c933-4fd9-be97-eb0d7ade3861" />
these are all the post request but it a little bit mess so we will use the cut command to get the files we are intersted in like (ip address,time,request..etc)

<img width="1920" height="947" alt="Screenshot_2026-05-11_18_33_53" src="https://github.com/user-attachments/assets/1871dc79-aff7-476d-8bcb-89612aee1dab" />

and here we have all the POST request contain all the filds that are inmportent in clear format

let take a look to that logs and see if there is any wired things 

we see that ip 10.255.254.2 is login to /adm/index.php (likely the admin) 
<img width="1920" height="947" alt="Screenshot_2026-05-11_18_33_53 legtimt access login" src="https://github.com/user-attachments/assets/76dfc2c1-acea-4880-8eaa-e3286f3abf82" />

it seem like its legtemit login since that ip login useing password and he login from the first time

if we keep looking we see that ip 10.10.0.78 is login in but this login is wired 
why???
becase if we look closely we see that there is no mood=login, its like this ip found way to go to admin page without using password

<img width="1920" height="947" alt="Screenshot_2026-05-11_18_33_53unligtemite lo" src="https://github.com/user-attachments/assets/c6393a03-7586-4361-9a86-b05c15345281" />

onther wired thing is that this ip POST to download database at 11:54:22

<img width="1920" height="947" alt="download database" src="https://github.com/user-attachments/assets/d4309fac-0964-43da-9313-c61156e3e4d3" />

so lets keep that in POST_logs.text
<img width="1920" height="947" alt="Screenshot_2026-05-11_19_04_51" src="https://github.com/user-attachments/assets/a53e557c-069b-4cf2-b956-9c208e7963f1" />


now let look for GET request

<img width="1920" height="947" alt="Screenshot_2026-05-11_19_06_23" src="https://github.com/user-attachments/assets/ea9fcd4b-f43c-4f3a-a80b-330e54321bd6" />

if we scroll down to the button at the time where the downlaod in POST request happend we see the download of the database

<img width="1920" height="947" alt="Screenshot_2026-05-12_04_44_42Backup" src="https://github.com/user-attachments/assets/830922f4-7268-4b38-814d-836009184f47" />

at 1 in image we see the first vist of the /adm/index.php?i=acp_database and at 2 at time 12:01:38 we see the dwonload of the database and the size of the database 

great so let is save this in GET_logs.text

<img width="1920" height="947" alt="Screenshot_2026-05-12_04_58_53" src="https://github.com/user-attachments/assets/5d43486a-b18b-4fcd-bce0-9d8651fe2971" />

now let collect some information from the database we have phpbb.sqlite3
<img width="1920" height="912" alt="Screenshot_2026-05-12_05_04_23folder" src="https://github.com/user-attachments/assets/033736ca-db61-4857-ba73-1399ef78566a" />

here we see some inmportent table like phpbb_config,phpbb_log..etc

now let look to the qoutions

<img width="527" height="826" alt="{F47E1A8B-71A4-4B96-99B5-739A85B24E97}" src="https://github.com/user-attachments/assets/90a075db-6fce-4250-bec5-773d4c677d1b" />
i alread answers some of them :) 
