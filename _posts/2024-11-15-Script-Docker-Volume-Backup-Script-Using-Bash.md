---
title: 'Scripting : Docker Volume Backup Script Using Bash'
date: 2024-11-15
permalink: /posts/2024/11/Script-Docker-Volume-Backup-Script-Using-Bash/
tags:
  - Docker
  - Linux
  - Backup
  - Scripting
  - Rclone
---

This script was created to locate and backup docker volumes as well as stop and start the containers during the backup process

## Script Breakdown

### Variables
Here we set variables needed to run the script. Most point to directories that are involved with the backups.
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
```

## Logic : Check if directories exist
Pretty straight forward, this if statement ensures that the folder we are saving the backups to exists and creates the folder if it does not.
```
# Check if backup directory exists, create if not
if [ ! -d "$BACKUP_DIR" ]; then
  mkdir -p "$BACKUP_DIR"
fi

# Get a list of all running containers
CONTAINERS=$(docker ps --format "{{.Names}}")
```

## Logic : Listing containers
Another straight forward section, we use docker commands to list all containers in the system
```
# Get a list of all running containers
CONTAINERS=$(docker ps --format "{{.Names}}")
```

### Function : Backup Volumes
Inside this function a few variables are passed though that are used to create the backup zips.
Usage of this function looks like this: backup_volume $CONTAINER_NAME $VOLUME_PATH. 
- $CONTAINER_NAME : Name of container, used to create filename of backup zip
- $VOLUME_PATH : Path of volume to be backed up

We also have a few variables that are isolated to this function:
- $container_name : The name of the container to be backed up, used to create filename of backup zip
- $volume_path : Path of volume to be backed up
- $volume_name : The name of the folder is pulled from the full volume path. EX: "/portainer/volumes/tautulli/data" would result in "data"
- $backup_file : Combines the above vatiables to create the filename of the backup zip

```
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
```

### Container processing loop

```
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
```

## Cloud Upload

```

# Uploads any data in the backup root to backblaze
rclone copy --stats-one-line $BACKUP_ROOT $REMOTE_ROOT
echo "Upload Completed"
```

## Final Thoughts
Hopefully the breakdown helps you better understand how this script works and can be modified for your usecase

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
