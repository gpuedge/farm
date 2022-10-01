# Farm

Metal at the edge.  
  
Provider  
![image](https://user-images.githubusercontent.com/3028982/167263971-6c9f15f0-b25e-4c1d-9ca2-9168a0ef7705.png)
  
Consumer  
![image](https://user-images.githubusercontent.com/3028982/167263995-9afa96de-7d96-4568-9d54-650aabb46d35.png)
![image](https://user-images.githubusercontent.com/3028982/167264063-fbc572d7-8d69-45f0-9e74-7dd5c4c62ff3.png)

## Changelog
v1.0.1 - Sept 30, 2022
  - WebSSH Added

v1.0.0 - Sept 29, 2022
First Point Release
  - A lot of code cleanup
  - Robust Storage APIs added
  - Improved real-time notification system

v0.0.10 - Jul 15, 2022
First Ready Release
  - Payment settlement through (https://github.com/gpuedge/wallet)
  - Rewrote webserver (https://github.com/vans163/photon)
  - Fixed throughput issues (30mbps 4k video ingestion)
  - Reduced binary size by 80%
  - Misc fixes all around

v0.0.9 - May 7th, 2022
```
Major reworking
 - Preparation for first point release
 - Nodes are independent providers
 - All job requests go to nodes directly
 - Added revamped dashboard native to running node
 - Added implicient subtype for jobs (javascript of dashboard handles)
   - blender
   - juypter
   - livebook
 - Added native storage called GPUX (hope to replace private IPFS)
 - Added folder mounts (for persistent storage between container runs)
 - Removed offchain endpoint
   - Removed autoupdate
   - Removed global job queuing
   - Removed job payment settlement for now ($0 jobs)
 - Removed IPFS (not working well)
 
Status
Docker (working)
Transcode (not working)
Live (not working)
```

v0.0.7 - February 9rd, 2022
```
IPFS support
```

v0.0.6 - February 3rd, 2022
```
Added better NVENC support detection
Fix cgroupsv2 detection
Fix Tesla GPU detection
Fix estimated price calculation
Add GPU price table support
Add Podman Args
Require kernel 5.11 now
```

v0.0.5 - January 12th, 2022
```
Added auto update support
```

v0.0.4 - January 11th, 2022
```
Added compute GPU support
Added trex for eth ( ETHADDR=0x.. )
```

v0.0.3 - December 31st, 2021
```
Added video transcode and live streaming
```

## Introduction to Compute
The GPUX Farm runs any code that can be containerized. You are not restricted to running WASM binaries or other derivatives. Using GPUs you have the following caps `NVIDIA_DRIVER_CAPABILITIES=utility,compute,graphics,video`, let us know if your usecase requires the `display` cap.
  
## Features
  - [X] GPUX API (API exposed via /api tcp-unix-socket inside container)
  - [X] docker containers /w GPU (podman)
  - [ ] transcode video (ffmpeg)
  - [ ] live streaming (basic features)
  - [ ] live streaming recording
  - [ ] encrypted_vm (AMD SEV) jobs are not supported yet.
  - [ ] encrypted_container (SGX) jobs are not supported yet.

## GPUX File Management
GPUX API is exposed inside the container as a tcp-unix domain socket at `/api`.  
(currently GPUX File Management is node local, if you change nodes your files and persistent storage will change)  
  
Inside container
```
#Download a file
curl --unix-socket /api "http://dontcare/gpux/v0/cat/sha1hash" --output myfile.png

#Upload a file and get sha1
curl -s --unix-socket /api -X POST --data-binary @myfile.png "http://dontcare/gpux/v0/add" | jq -r '.sha1'
```

Outside container
```
#Download a file
http://{node_ip_port}/download_file/sha1

#Upload a file to GPUX and get sha1
curl -d @myfile.png http://{node_ip_port}/upload_file | jq -r '.sha1'
```

Persistent folder inside Container bound to your ed25519 identity
```
/persist
```

## How to run jobs on the edge
Visit https://github.com/gpuedge/examples to see examples of running workloads like blender, machine learning, and more.  

## How to join the edge
Download the release and run ./farm, then ping on discord to get added to the beta node list.  

## Environmental Variables

```
//Farm
WORKFOLDER - Path to store misc items, disk images and non podman storage (default ~/.cache/gpux)
LOG_LEVEL - How verbose to log 1 | 2 | 3 (default 1)

//Network Interfaces
HTTP_IPV4 - IP interface/alias to bind on (default "0.0.0.0")
HTTP_PORT - Port (default 80)

(SSL optional)
HTTPS_DOMAIN - Domain name pointing to your node
HTTPS_IPV4 - IP interface/alias to bind on (default "0.0.0.0")
HTTPS_PORT - Port (default 443)
HTTPS_CERT - HTTPS Certificate path (default $WORKFOLDER/fullchain.pem)
HTTPS_KEY - HTTPS Certificate path (default $WORKFOLDER/privkey.pem)
```
