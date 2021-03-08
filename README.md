# AgeControlProtocolPlus

The next generation of networks must support billions of connected devices in the Internet-of-Things (IoT). To support IoT applications, sources sense and send their measurement updates over the Internet to a monitor (control station) for real-time monitoring and actuation. Ideally, these updates would be delivered at a high rate, only constrained by the sensing rate supported by the sources. However, given network constraints, such a rate may lead to delays in delivery of updates at the monitor that make the freshest update at the monitor unacceptably old for the application.

Age Control Protocol+ (ACP+) is a novel transport layer protocol,that enables timely delivery of such updates to monitors, in a network-transparent manner. ACP+ allows the source to adapt its rate of updates to dynamic network conditions such that the average age of the sensed information at the monitor is minimized.

ACP+ is published at IEEE INFOCOM AOI Workshop 2021

T. Shreedhar, S. K. Kaul and R. D. Yates, "An Empirical Study of Ageing in the Cloud," IEEE INFOCOM 2021 - IEEE Conference on Computer Communications Workshops (INFOCOM WKSHPS),.

# ACP+ Operation

The Age Control Protocol+ resides in the transport layer of the TCP/IP networking stack and operates only on the end hosts. ACP+ also uses the  user datagram protocol (UDP) for sending of updates generated by the sources, inline with the freshness requirements of the updates. 

The client appends a header to an update from a source. The header contains a timestamp field that stores the time the update was generated. The source ACP+ calculates the inter-update generation rates using the estimated network conditions over the end-to-end path to the monitor ACP+. For this, monitor sends an ACK which contains the timestamp of the update being acknowledged. The ACK(s) allow the source ACP+ to keep an estimate of the age of sensed information at the monitor. Out-of-order packets and ACK(s) are discarded from the calculations.

For establishing an ACP+ connection, the monitor/server ACP+ must be listening on a previously advertised UDP port. The ACP+ client/source establishes a UDP connection with the server, and sends an update and waits for an ACK or for a suitable timeout to occur, and repeats this process for a few times, with the goal of probing the network to set an initial update rate. ACP+ operation can be described by a sequence of control epochs. Please refer to the paper on details of how an update rate is set at a control epoch. An ACP+ end-to-end connection is closed when the source closes its corresponding UDP socket.

# How to run ACP+ 
Following are the steps to run the codes in this repository :

- Create executables of C++ source code
	``` g++ -std=gnu++11 client_acp.cpp -o client_acp -lpthread -lrt ```
	``` g++ -std=gnu++11 client_baseline.cpp -o client_baseline -lpthread -lrt ```
``` g++ -std=gnu++11 server.cpp -o server -lpthread -lrt ```

- Run **server** on a machine and pass a port number along with it
	``` ./server $port ```

 - Run **client** on a different machine and pass a port number along with it. You can run the ACP and baseline on a single machine. 
	``` ./client_acp $serverIP $packetSize $numPackets 2 $stepSize $port ```
	``` ./client_baseline $serverIP $packetSize $numPackets 2 $port ```

>Flags: 
>$serverIP&rarr; IP address of ACP+ server
>$packetSize &rarr;  Packet size of the UDP packet (bytes)
>$numPackets &rarr; Total number of packets that the client sends
>$stepSize &rarr; Step size used in the control block. Use 100 for step size 1. 
>$port &rarr; UDP port for ACP+ client server
