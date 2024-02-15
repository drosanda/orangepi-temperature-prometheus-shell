
# Howto: Orange PI temperature into Prometheus

Sample code and instructions on how to push the Orange Pi internal temperature value into Prometheus. No external libraries are required, everything is performed through unix shell scripts.
This works only using Ubuntu Version from Orange PI official website.

## Requirements
 Please make sure you have installed and running:
 - tmux
 - Prometheus job
 - Node_Exporter job

## Implementation

Change to your root `sudo su` then save the codes below into `/root/rpitemp.sh`

```bash
#!/bin/sh

while true; do
    { echo -ne "HTTP/1.0 200 OK\r\nContent-Length: $(echo -n "rpitemp $(cat /sys/class/thermal/thermal_zone0/temp | sed 's/\([0-9]\{2\}\)/\1./')" | wc -c)\r\n\r\nrpitemp $(cat /sys/class/thermal/thermal_zone0/temp | sed 's/\([0-9]\{2\}\)/\1./')"; } | nc -l -p 7028
done
```

Change the file permission to make it executable `chmod +x /root/rpitemp.sh`.

Add this config on promotheus.yml

```
.
.
.
scrape_configs:
 .
 .
 .
  - job_name: "rpitemp"
    static_configs:
    - targets: ['localhost:7028']
.
.
.
```

To run this script, Create new tmux session

```bash
tmux new -s rpitemp
```
After moved to tmux session, we can run the script.

```bash
/root/rpitemp.sh
```

press `ctrl` with `b` at same time, and then press `d` to exit from tmux session.

and then restart the prometheus execution.

## Other way
you can executed with your own way with the same concept.
