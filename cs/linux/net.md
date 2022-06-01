- [Networking](#networking)

# Networking

```shell
# TCP Ping
timeout 2 bash -c -- "while true;do nc -z example.com 443 -v -w 3;done"
```
