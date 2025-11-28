# CSV Server Setup Guide

This guide explains how to set up the environment and run the CSVServer project on Ubuntu using Docker. It includes steps from VM setup to fetching the server output.

1. Virtual Machine Setup

Installed VMware Workstation / Player.

Downloaded Ubuntu ISO.

Created a virtual machine and installed Ubuntu.

2. System Update

Update the package lists and upgrade existing packages:

sudo apt update
sudo apt upgrade -y

3. Docker Installation

Install Docker using Snap and configure permissions:

sudo snap install docker
docker --version


Enable Docker to start at boot:

sudo systemctl enable docker
sudo systemctl status docker


Adjust permissions to allow the current user to run Docker without sudo:

sudo chown root:docker /var/run/docker.sock
sudo chmod 660 /var/run/docker.sock
sudo usermod -aG docker $USER


Log out and back in or run newgrp docker to apply the group changes.

4. Git and Project Setup

Install Git and clone the CSVServer repository:

sudo apt install git -y
git --version

cd ~/Downloads
git clone https://github.com/infracloudio/csvserver.git
cd csvserver/solution
ls

5. Generate CSV Input File

Create a script gencsv.sh to generate input data:

#!/bin/bash
start=$1
end=$2
> inputFile
for ((i=start; i<=end; i++))
do
    echo "$i, $(( RANDOM % 300 ))" >> inputFile
done


Make it executable and generate data:

chmod +x gencsv.sh
./gencsv.sh 2 8
cat inputFile


Example output:

2, 203
3, 293
4, 169
5, 44
6, 214
7, 267
8, 162

6. Run CSVServer Docker Container

Run the container and mount the input file:

sudo docker run -d -v $(pwd)/inputFile:/csvserver/inputdata infracloudio/csvserver:latest


Advanced run with environment variable and port mapping:

sudo docker run -d \
  -v $(pwd)/inputFile:/csvserver/inputdata \
  -e CSVSERVER_BORDER=Orange \
  -p 9393:9300 \
  infracloudio/csvserver:latest


Check running containers:

sudo docker ps

7. Fetch CSVServer Output

Download and view the raw output:

curl -o ./part-1-output http://localhost:9393/raw
cat part-1-output


Save the run command for reference:

echo "sudo docker run -d -v \$(pwd)/inputFile:/csvserver/inputdata -e CSVSERVER_BORDER=Orange -p 9393:9393 infracloudio/csvserver:latest" > part-1-cmd


Save container logs:

sudo docker logs $(sudo docker ps -q --filter ancestor=infracloudio/csvserver:latest) >& part-1-logs

8. Directory Structure

After setup, your working directory should look like this:

solution/
├─ READme.md
├─ gencsv.sh
├─ inputFile
├─ part-1-output
├─ part-1-cmd
├─ part-1-logs

9. Status

✔ Docker container running and serving CSV data
✔ Input file generated via gencsv.sh
✔ Output saved in part-1-output
✔ Logs saved in part-1-logs
✔ Run command saved in part-1-cmd

refrence screenshot:

task 1:
<img width="1280" height="369" alt="Screenshot From 2025-11-28 22-11-13" src="https://github.com/user-attachments/assets/6da6ca08-ee8d-461c-9256-3081e4fc5736" />

task2:
<img width="1280" height="800" alt="Screenshot From 2025-11-28 22-12-24" src="https://github.com/user-attachments/assets/70c42485-0a87-4d2d-b849-a906478bf9ec" />

port assigned as mentioned in task description
<img width="1267" height="171" alt="Screenshot From 2025-11-28 22-13-22" src="https://github.com/user-attachments/assets/ee014c4a-d04b-4cde-9342-2dcfb8241020" />

file structure:
<img width="1280" height="503" alt="Screenshot From 2025-11-28 23-02-02" src="https://github.com/user-attachments/assets/d75f6b28-54ac-4530-9c1a-d97e224b055f" />

task3:
Docker compose and promithius

-------

# Note and screenshot for refrence:
