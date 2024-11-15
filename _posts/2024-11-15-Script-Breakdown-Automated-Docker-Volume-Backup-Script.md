---
title: 'Script Breakdown: Automated Docker Volume Backup Script'
date: 2024-11-15
permalink: /posts/2024/11/Script-Breakdown-Automated-Docker-Volume-Backup-Script/
tags:
  - Docker
  - Linux
  - Backup
  - Scripting
  - Rclone
---

This script automates the process of backing up Docker container volumes and uploading the backups to a remote location using rclone. 

The script performs the following steps:
1. Creates a backup directory for the current day.
2. Iterates through all running Docker containers.
3. Stops each container to ensure consistent backups.
4. Identifies and compresses eligible volumes for each container.
5. Restarts the container after backup completion.
6. Uploads the backup files to a remote storage location.


## Script Breakdown

### Variables
The script begins by defining variables for paths, timestamps, and remote storage details.

`DOCKER_VOLUMES`
- Path where Docker volumes are stored (e.g., /portainer/volumes).

`BACKUP_ROOT`
- Root directory where backup files will be stored (e.g., /portainer/backups).

`BACKUP_DIR`
- Directory for today's backups, generated based on the current date (e.g., /portainer/backups/2024-11-15).

`TIMESTAMP`
- Current timestamp for naming backup files (e.g., 2024-11-15_10-30-00).

`REMOTE_ROOT`
- Remote location for backups using rclone (e.g., b2-kolakloud:/kolakloud/docker.kolakloud.com/volumes).


### Directory Check and Creation
- Checks if the directory for today's backups exists.
- Creates the directory if it does not.
```
if [ ! -d "$BACKUP_DIR" ]; then
  mkdir -p "$BACKUP_DIR"
fi
```


### Container List
- Retrieves a list of all running Docker containers by their names.
```
CONTAINERS=$(docker ps --format "{{.Names}}")
```


### Function: `backup_volume`
#### Parameters:
- container_name: Name of the container being processed.
- volume_path: Path to the Docker volume.
#### Process:
- Generates a unique backup file name based on the container name, volume name, and timestamp.
- Compresses the volume contents into a .zip file.
- Logs the success or failure of the operation.
```
backup_volume() {
  local container_name=$1
  local volume_path=$2
  local volume_name=$(basename "$volume_path")
  local backup_file="${BACKUP_DIR}/${container_name}_${volume_name}_${TIMESTAMP}.zip"

  echo "Backing up volume at $volume_path for container $container_name..."
  zip -pr "$backup_file" "$volume_path"
  
  if [ $? -eq 0 ]; then
    echo "Backup for $volume_path completed successfully: $backup_file"
  else
    echo "Backup for $volume_path failed."
  fi
}
```


### Main Loop: Container Processing

#### Steps for Each Container:
1. Get Volumes: Fetches attached volumes for the container.
2. Stop Container: Stops the container to ensure data consistency during backup.
3. Backup Volumes: Iterates through volumes and backs up those matching the DOCKER_VOLUMES path.
4. Restart Container: Starts the container after backup completion.
5. Status Output: Displays the completion of the backup process for the container.
```
for CONTAINER_NAME in $CONTAINERS; do
  echo "Processing container: $CONTAINER_NAME"
  VOLUMES=$(docker inspect --format='{{ range .Mounts }}{{ .Source }} {{ end }}' "$CONTAINER_NAME")

  echo "Stopping container $CONTAINER_NAME..."
  docker stop "$CONTAINER_NAME"
  
  for VOLUME_PATH in $VOLUMES; do
    if [[ "$VOLUME_PATH" == $DOCKER_VOLUMES/* ]]; then
      backup_volume "$CONTAINER_NAME" "$VOLUME_PATH"
    else
      echo "Skipping non-eligible volume $VOLUME_PATH for container $CONTAINER_NAME."
    fi
  done

  echo "Starting container $CONTAINER_NAME..."
  docker start "$CONTAINER_NAME"
  echo "Backup process for container $CONTAINER_NAME completed."
done
```

### Remote Upload
- rclone copy: Uploads the backup files from the local backup root directory to the defined remote location.
- Logs the completion of the upload process.
```
rclone copy --stats-one-line $BACKUP_ROOT $REMOTE_ROOT
echo "Upload Completed"
```

### Key Features
1. Volume Filtering:
   - Only backs up volumes within the DOCKER_VOLUMES directory.
2. Data Consistency:
   - Containers are stopped before their volumes are backed up.
3. Automated Upload:
   - Uses rclone to securely transfer backups to a remote storage location.

## Usage
- Place this script on the host server.
- Modify the variables (DOCKER_VOLUMES, BACKUP_ROOT, REMOTE_ROOT) to match your environment.
- Ensure zip and rclone are installed and configured.
- Run the script with appropriate permissions:
```
./backup_script.sh
```

## Requirements
- Docker CLI
- zip utility
- rclone configured for the desired remote storage

## Final Thoughts 
I hope that this detailed breakdown should help the reader understand each step of the script and its purpose. 

# Complete Script
```
#!/bin/bash

# --Variables Start--

# Location Docker volumes are stored
DOCKER_VOLUMES="/portainer/volumes"

# Location of to place backups
BACKUP_ROOT="/portainer/backups"
BACKUP_DIR="$BACKUP_ROOT/$(date +%Y-%m-%d)"  # Directory where you want to save the backups

# Timestamp
TIMESTAMP=$(date +%Y-%m-%d_%H-%M-%S)     # Current timestamp for backup files

# Rclone remote to store backups
REMOTE_ROOT="b2-kolakloud:/kolakloud/docker.kolakloud.com/volumes"

# -- Variables End--

# Check if backup directory exists, create if not
if [ ! -d "$BACKUP_DIR" ]; then
  mkdir -p "$BACKUP_DIR"
fi

# Get a list of all running containers
CONTAINERS=$(docker ps --format "{{.Names}}")

# Function to back up a volume
backup_volume() {
  local container_name=$1
  local volume_path=$2
  local volume_name=$(basename "$volume_path")
  local backup_file="${BACKUP_DIR}/${container_name}_${volume_name}_${TIMESTAMP}.zip"

  #Zips files in docker volume, to see output, change -pr to -r
  echo "Backing up volume at $volume_path for container $container_name..."
  zip -pr "$backup_file" "$volume_path"

  if [ $? -eq 0 ]; then
    echo "Backup for $volume_path completed successfully: $backup_file"
  else
    echo "Backup for $volume_path failed."
  fi
}

# Loop through each container and check its volumes
for CONTAINER_NAME in $CONTAINERS; do
  echo "Processing container: $CONTAINER_NAME"

  # Get a list of all attached volumes for the container
  VOLUMES=$(docker inspect --format='{{ range .Mounts }}{{ .Source }} {{ end }}' "$CONTAINER_NAME")

  # Stop the Docker container
  echo "Stopping container $CONTAINER_NAME..."
  docker stop "$CONTAINER_NAME"

  # Backup only volumes within $DOCKER_VOLUMES
  for VOLUME_PATH in $VOLUMES; do

    #loops though volumes and zips them
    if [[ "$VOLUME_PATH" == $DOCKER_VOLUMES/* ]]; then
      backup_volume "$CONTAINER_NAME" "$VOLUME_PATH"
    else
      echo "Skipping non-eligible volume $VOLUME_PATH for container $CONTAINER_NAME."
    fi
  done #-End of volume processing loop

  # Restart the Docker container
  echo "Starting container $CONTAINER_NAME..."
  docker start "$CONTAINER_NAME"

  echo "Backup process for container $CONTAINER_NAME completed."
done #-End of Container loop

echo "All containers processed, starting upload"

# Uploads any data in the backup root to backblaze
rclone copy --stats-one-line $BACKUP_ROOT $REMOTE_ROOT
echo "Upload Completed"
```
