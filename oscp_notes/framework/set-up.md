# Set Up
Make folder for the project - script 

Make folders for each IPs - script

touch creds.txt - script

start network diagram

```bash

#!/bin/bash
# project_build.sh
#####################################################################
# Variables Declaration
# Name of the project
project=""
# List of IPs for the initial scan and setup
ip_list=(
    "" 
    ""
    ""
)
#####################################################################
# Global Variables
PROJECT_DIR="$HOME/$project"
#####################################################################
# Make directories 
mkdir $PROJECT_DIR
for i in "${ip_list[@]}" 
do 
    mkdir -p "$PROJECT_DIR/$i" 
done
# app is dir is for code or exploits
mkdir -p "$PROJECT_DIR/apps"
# workspace is your junk drawer
mkdir -p "$PROJECT_DIR/workspace"
# share is for the directory used for sharing 
mkdir -p "$PROJECT_DIR/share"
# creds is for cracked creds
mkdir -p "$PROJECT_DIR/nmap"
# share is for the directory used for sharing 
touch "$PROJECT_DIR/creds.txt"
# Network layout
touch "$PROJECT_DIR/network_layout.txt"

```
