# HTB-Machines-Info-Scraper 🕵️‍♂️
A simple and handy Bash script to download and search for Hack The Box machine information from a public source.
## 🧠 What does this script do?
This script allows you to:

- Automatically download or update the `bundle.js` file containing HTB machine information.
- Search for specific machine details by name.
- Display a help panel with available options.
  ## 📦 Requirements
Make sure the following tools are installed on your system:

- `curl`
- `js-beautify`
- `md5sum`
- Bash (Linux/macOS)
## 🛑 Script Output
Uses color-coded messages for clarity
Handles Ctrl+C (SIGINT) gracefully
# 📝Code
```bash
#!/bin/bash
greenColour="\e[0;32m\033[1m"
endColour="\033[0m\e[0m"
redColour="\e[0;31m\033[1m"
blueColour="\e[0;34m\033[1m"
yellowColour="\e[0;33m\033[1m"
purpleColour="\e[0;35m\033[1m"
turquoiseColour="\e[0;36m\033[1m"
grayColour="\e[0;37m\033[1m"

function ctrl_c(){
  echo -e "\n\n${redColour}[!]${endColour} Exiting..\n"
  exit 1
}
#CTRL_C
trap ctrl_c INT

# Variables globales
main_url="https://htbmachines.github.io"

function helpPanel(){
   echo -e "\n\n ${blueColour}[+]${endColour} Use: "
   echo -e "\t${greenColour}u)${endColour} Download necesary files "
   echo -e "\t${turquoiseColour}m)${endColour} Search machine name"
   echo -e "\t${purpleColour}h)${endColour} Show help panel\n"

}

function updateFiles(){

     if [ ! -f bundle.js ]; then
echo -e "\n\n ${yellowColour}[+]${endColour} Downloading files..."
sleep 2
      curl -s $main_url > bundle.js
       js-beautify bundle.js -o bundle.js
echo -e "\n\n ${greenColour}[!]${endColour} All files have been downloaded."
     else
    echo -e "\n\n ${yellowColour}[*]${endColour} Checking updates.."
    sleep 2
curl -s $main_url > bundle_temp.js
js-beautify bundle_temp.js -o bundle_temp.js
md_temp_value=$(md5sum bundle_temp.js | awk '{print $1}')
md_original_value=$(md5sum bundle.js | awk '{print $1}')

if [ "$md_temp_value" == "$md_original_value" ]; then
           rm bundle_temp.js
    echo -e "\n\n${greenColour}[+]${endColour} There is no updates."
        else

echo -e "\n\n${redColour}[!]${endColour} There is one update."
                    rm bundle.js && mv bundle_temp.js bundle.js
echo -e "\n\n${greenColour}[+]${endColour} Update completed."    
fi

fi
}

searchMachine(){
machineName="$1"
echo -e "\n\n${greenColour}[+]${endColour} Listing machine information: $machineName\n"
    cat bundle.js | awk "/name: \Forge\"/,/resuelta:/" | grep -vE "id:|sku:resuelta" | tr -d ',' | sed 's/^*//'

}

#IDICATORS
declare -i parameter_counter=0

while getopts "m:hu" arg ; do
case $arg in
m) machineName=$OPTARG; let parameter_counter+=1;;
u) let parameter_counter+=2;;
       h);;
esac

done

if [ $parameter_counter -eq 1 ]; then
    searchMachine $machineName
elif [ $parameter_counter -eq 2 ]; then
updateFiles
else
helpPanel

fi


