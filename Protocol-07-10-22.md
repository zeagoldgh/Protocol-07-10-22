# Protocol Friday 07/10/2022

Presence:

-   all present

<img width="1440" alt="Screen Shot 1401-07-15 at 09 41 16" src="https://user-images.githubusercontent.com/114474804/194716850-7b1fd1aa-ca9b-4d52-a64d-b4d361eedd9b.png">

Topics
- Monitoring System
- Meaning of JSON
- Basic Linux Scripting
# Monitoring System
The goals are to transform your processes to JSON.
## What is JSON
- JSON syntax is derived from(JavaScript Object Notation).
- JSON is a text format for storing and transporting data.
- JSON is a lightweight data-interchange format.
- JSON is plain text written in JavaScript object notation.
- JSON is used to send data between computers.
## Why use JSON
- The JSON format is syntactically similar to the code for creating JavaScript objects. Because of this, a JavaScript program can easily convert JSON data into JavaScript objects.
- Since the format is text only, JSON data can easily be sent between computers, and used by any programming language.
- JavaScript has a built in function for converting JSON strings into JavaScript objects:
  JSON.parse()
- JavaScript also has a built in function for converting an object into a JSON string:
  JSON.stringify()
- For more details on JSON here is the link: https://www.w3schools.com/js/js_json_syntax.asp
### Friday Challenge about Basic For Linux Scripting, JSON, Deployment
# Taks 1
- 1. We are to search and read the basics about JSON format and understand the following JSON file.
[
    {
        "pid":234,
        "cpu": 30,
        "user": "fabianschmauder"
    },
    {
        "pid":134,
        "cpu": 20,
        "user": "fabianschmauder"
    },
    {
        "pid":33,
        "cpu": 10,
        "user": "fabianschmauder"
    }
]
- 2. A script that writes 5 processes with the highest cpu consumption into a json file by using this command ps -eo pcpu,pid,user,args | sort -k 1 -r
### Solutions:
- First solution from Irene:  
#!/bin/bash
resultString=`ps -eo pcpu,pid,user | sort -k 1 -r | head -6 | grep -v '%'`
valuesArray=($resultString)
titlesArray=(cpu pid user)
resultValuesLength=$(( ( ${#valuesArray}+1 )*${#titlesArray} ))
echo '[' > jsonFile.json
for ((i=0; i<$resultValuesLength; i=((i+3))))
do
    echo '    {'  >> jsonFile.json
    for ((k=0; k<${#titlesArray}; k++))
    do
        echo '         "'${titlesArray[$k]}'": "'${valuesArray[(($i+$k))]}'",' >> jsonFile.json
    done
    sed -i '' '$s/,$//' jsonFile.json
    echo '    },' >> jsonFile.json
done
sed -i '' '$s/,$//' jsonFile.json
echo ']' >> jsonFile.json
- Another solution from Kostyantyn:  
#!/bin/bash
top_cpu_proc () {
    for line in {1..5}
        do
            cpusage=$(ps -eo pcpu,pid,user | sort -k 1 -r | egrep -v '(%)'| head -n 5 | sed -n "$line p" | awk '{print $1}')
            eval "cpusage${line}=${cpusage}"
            pidn=$(ps -eo pcpu,pid,user | sort -k 1 -r | egrep -v '(%)'| head -n 5 | sed -n "$line p" | awk '{print $2}')
            eval "pidn${line}=${pidn}"
            usern=$(ps -eo pcpu,pid,user | sort -k 1 -r | egrep -v '(%)'| head -n 5 | sed -n "$line p" | awk '{print $3}')
            eval "usern${line}=${usern}"
        done
    jq -n --arg pidn1 "$pidn1" --arg cpusage1 "$cpusage1" --arg usern1 "$usern1" --arg pidn2 "$pidn2" --arg cpusage2 "$cpusage2" --arg usern2 "$usern2" --arg pidn3 "$pidn3" --arg cpusage3 "$cpusage3" --arg usern3 "$usern3" --arg pidn4 "$pidn4" --arg cpusage4 "$cpusage4" --arg usern4 "$usern4" --arg pidn5 "$pidn5" --arg cpusage5 "$cpusage5" --arg usern5 "$usern5" ['{"pid":$pidn1,"cpu":$cpusage1,"username":$usern1}','{"pid":$pidn2,"cpu":$cpusage2,"username":$usern2}','{"pid":$pidn3,"cpu":$cpusage3,"username":$usern3}','{"pid":$pidn4,"cpu":$cpusage4,"username":$usern4}','{"pid":$pidn5,"cpu":$cpusage5,"username":$usern5}'] > monitoring-data.json
}
if [[ ! $(rpm -qa | grep jq) ]]
then
    sudo yum install -y jq
    top_cpu_proc
else
    top_cpu_proc
fi (edited)
## Task 2
- Monitorying ec2 instance
- 1. We are to upload our script to an ec2 instance
- 2. create a cron job that runs your script every 5 minutes



