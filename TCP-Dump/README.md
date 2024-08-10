# Task 1. Identify network interfaces

`sudo ifconfig`
![image](https://github.com/user-attachments/assets/29ab435f-5c1a-432d-a26f-c05c05d88120)

`sudo tcpdump -D`
![image](https://github.com/user-attachments/assets/69e19888-c227-461b-a5c6-ce1965451948)

# Task 2. Inspect the network traffic of a network interface with tcpdump

`sudo tcpdump -i eth0 -v -c5`
This command will run `tcpdump` with the following options:

- `-i eth0`: Capture data specifically from the eth0 interface.
- `-v`: Display detailed packet data.
- `-c5`: Capture 5 packets of data.
![image](https://github.com/user-attachments/assets/a60bb3a6-08cc-44ee-826f-65904792ecae)

## Exploring network packet details
In this example, you’ll identify some of the properties that `tcpdump` outputs for the packet capture data you’ve just seen.

1. In the example data at the start of the packet output, `tcpdump` reported that it was listening on the `eth0` interface, and it provided information on the link type and the capture size in bytes:
`tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes`

2. On the next line, the first field is the packet's timestamp, followed by the protocol type, IP:
`12:12:45.114483 IP`

3. The verbose option, -v, has provided more details about the IP packet fields, such as `TOS`, `TTL`, `offset`, `flags`, `internal protocol type` (in this case, `TCP` (6)), and the `length` of the outer IP packet in bytes:
`(tos 0x0, ttl 64, id 24999, offset 0, flags [DF], proto TCP (6), length 114)`

4. In the next section, the data shows the systems that are communicating with each other:
`cec2e7d2c848.5000 > nginx-us-central1-c.c.qwiklabs-terminal-vms-prod-00.internal.47230:`

The direction of the arrow `(>)` indicates the direction of the traffic flow in this packet. Each system name includes a suffix with the port number (.5000 in the screenshot), which is used by the source and the destination systems for this packet.

5. The remaining data filters the header data for the inner `TCP` packet:
`Flags [P.], cksum 0x588e (incorrect -> 0xd5bf), seq 336334033:336334095, ack 3415922524, win 492, options [nop,nop,TS val 725078408 ecr 211995748], length 62`

# Task 3. Capture network traffic with tcpdump

1. Capture packet data into a file called capture.pcap:
`sudo tcpdump -i eth0 -nn -c9 port 80 -w capture.pcap &`

This command will run tcpdump in the background with the following options:

- `-i eth0`: Capture data from the eth0 interface.
- `-nn`: Do not attempt to resolve IP addresses or ports to names.This is best practice from a security perspective, as the lookup data may not be valid. It also prevents malicious actors from being alerted to an investigation.
- `-c9`: Capture 9 packets of data and then exit.
- `port 80`: Filter only port 80 traffic. This is the default HTTP port.
- `-w capture.pcap`: Save the captured data to the named file.
- `&`: This is an instruction to the Bash shell to run the command in the background.

![image](https://github.com/user-attachments/assets/2371a3e9-53bb-4169-82a5-a0c32d52983d)

2. Use curl to generate some HTTP (port 80) traffic:
`curl opensource.google.com`

**RESPONSE**
```
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="https://opensource.google/">here</A>.
</BODY></HTML>
```
![image](https://github.com/user-attachments/assets/06cd0929-0318-4aac-b80c-d72ece34c456)

3. Verify that packet data has been captured:
`ls -l capture.pcap`

![image](https://github.com/user-attachments/assets/b87f7e67-894d-43ba-b4b0-e324b5044c90)

# Task 4. Filter the captured packet data

1. Use the `tcpdump` command to filter the packet `header` data from the `capture.pcap` capture file:
`sudo tcpdump -nn -r capture.pcap -v`

This command will run tcpdump with the following options:

- `-nn`: Disable port and protocol name lookup.
- `-r`: Read capture data from the named file.
- `-v`: Display detailed packet data.


