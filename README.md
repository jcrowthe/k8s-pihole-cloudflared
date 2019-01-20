# k8s-pihole-cloudflared
A Kubernetes deployment of PiHole with DNS traffic forwarded over DNS-over-HTTPS, using cloudflared's proxying capabilities.

# Usage
1. After modifying `deployment-pihole-cloudflared.yaml` to include a location for your persistent data, apply it to the kubernetes cluster as follows:

```
kubectl apply -f deployment-pihole-cloudflared.yaml
```

1. At this point, get the pihole service IP with the following:
```
$ kubectl get svc
pihole       NodePort    192.168.1.72    <none>        80:31173/TCP,443:32320/TCP,53:31795/TCP   79m
pihole-udp   NodePort    192.168.1.11    <none>        53:30482/UDP,67:31077/UDP                 79m
...
```

1. Set this IP in the pihole configuration file `deployment-pihole-cloudflared.yaml` and `kubectl apply -f deployment-pihole-cloudflared.yaml` it again for pihole to receive it's correct IP address.
1. Test that DNS resolves fine with the "Testing DNS" section below
Note you can login to the pihole web interface, by navigating a browser to the `pihole` kubernetes service IP.
    - The password will be found in the logs for your pihole instance. Run `kubectl logs <pihole-pod> -c pihole` to get this
1. Profit.




# Testing DNS
Test by hitting the `pihole-udp` service IP. Get the IP:
```
$ kubectl get svc
pihole       NodePort    192.168.1.72    <none>        80:31173/TCP,443:32320/TCP,53:31795/TCP   79m
pihole-udp   NodePort    192.168.1.11    <none>        53:30482/UDP,67:31077/UDP                 79m
...
```

The query the `pihole-udp` IP:
```
$ dig @192.168.1.11 google.com

; <<>> DiG 9.10.6 <<>> @192.168.100.144 google.com
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 58199
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1452
;; QUESTION SECTION:
;google.com.			IN	A

;; ANSWER SECTION:
google.com.		211	IN	A	216.58.192.14

;; Query time: 82 msec
;; SERVER: 192.168.100.144#53(192.168.100.144)
;; WHEN: Sat Jan 19 19:00:59 MST 2019
;; MSG SIZE  rcvd: 65
```
