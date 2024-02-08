This is a conceptual model for designing and describing networks that contains the following 7 layers from the most abstract one to the most specific :

- Application Layer
- Presentation Layer
- Session Layer
- Transport Layer
- Network Layer
- Data Link Layer
- Physical Layer

### 1. Physical Layer

Data transmission units : raw binary data in the form of electrical or optical signals, depending on the medium used

 Layer 1, also known as the physical layer, is the first and lowest layer in the OSI model. It is responsible for the physical transmission of data over a communication channel, such as a wire, cable, or wireless signal. This layer defines the physical characteristics of the communication medium, including voltage levels, cable types, and signaling methods, to ensure that data is transmitted accurately and reliably between devices.

However, the downsides of this layer are the fact that we can't uniquely identify devices. In a network with multiple devices and a switch, everything received by the switch is multiplexed to all other devices connected to it. We don't have the possibility to direct traffic to specific targets (be it valid successful transmission or collisions). Also, if 2 or more devices emit data, collisions appear as we don't have "media access control" or "collision detection". 

### 2. Data-Link Layer

Layer 2 of the OSI model, known as the Data Link layer, sits above the Physical layer and below the Network layer. It primarily deals with node-to-node communication, providing a reliable link between two directly connected nodes on a network. The Data Link layer performs several key functions:

1. **Framing**: The Data Link layer encapsulates network layer packets into frames for transmission over the physical medium. It adds header and trailer information to the data received from the Network layer, allowing for synchronization, error detection, and flow control.
    
2. **Addressing**: Each device on a network typically has a unique hardware address, such as a Media Access Control (MAC) address. The Data Link layer uses these addresses to identify the source and destination of frames within the same local network.
    
3. **Error Detection and Correction**: The Data Link layer ensures the integrity of data transmission by detecting and, in some cases, correcting errors that may occur during transmission over the physical medium. Common error detection techniques include checksums and cyclic redundancy checks (CRC).
    
4. **Flow Control**: The Data Link layer manages the flow of data between two connected devices, ensuring that the sender does not overwhelm the receiver with data. This helps in preventing packet loss and congestion on the network.
    
5. **Media Access Control (MAC)**: The Data Link layer also governs access to the physical medium by devices on the same network segment. Different protocols, such as Carrier Sense Multiple Access/Collision Detection (CSMA/CD) for Ethernet, manage how devices contend for access to the network.

We however do not have the ability to route packets globally ( to interconnected networks) by just using layer 2.

### 3. Network Layer

Layer 3 of the OSI model, known as the Network layer, is responsible for end-to-end communication and routing of data packets across interconnected networks. It operates above the Data Link layer and below the Transport layer. Layer 3 protocols deal with logical addressing, packet forwarding, and routing

1. **Logical Addressing**: Layer 3 assigns logical addresses, such as IP addresses, to devices on a network. These addresses are hierarchical and provide a structured way to identify devices and their locations within a network.
    
2. **Routing**: Layer 3 routers use logical addressing information to make forwarding decisions. They determine the optimal path for data packets to reach their destination across multiple interconnected networks. Routing protocols, such as OSPF (Open Shortest Path First) and BGP (Border Gateway Protocol), are used at this layer to exchange routing information and maintain routing tables.
    
3. **Internetwork Communication**: Layer 3 enables communication between devices that are not directly connected on the same local network. It facilitates end-to-end communication by routing packets across multiple intermediate networks, including local area networks (LANs), wide area networks (WANs), and the internet.
    
4. **Packet Switching**: Layer 3 employs packet switching techniques to transmit data packets efficiently across networks. Packets are forwarded based on their destination IP addresses, allowing for flexible and dynamic routing of data traffic.
    
5. **Quality of Service (QoS)**: Layer 3 protocols support Quality of Service mechanisms to prioritize certain types of traffic based on criteria such as packet size, delay sensitivity, or application requirements. This ensures that critical traffic, such as voice or video data, receives preferential treatment over less time-sensitive data.
    
6. **Network Segmentation**: Layer 3 facilitates network segmentation by dividing large networks into smaller subnetworks, or subnets. This helps in improving network performance, security, and manageability by limiting the scope of broadcast traffic and isolating network issues.
    

### 4. and 5. Transport layer & Session layer

#### Layer 4: Transport Layer

The Transport layer is responsible for end-to-end communication between hosts and provides mechanisms for error detection, flow control, and data segmentation. The primary protocols associated with this layer are TCP (Transmission Control Protocol) and UDP (User Datagram Protocol).

##### Why we need Layer 4:

1. **Segmentation and Reassembly**: The Transport layer breaks down data from the upper layers into manageable segments for transmission and reassembles them at the receiving end. This process helps in optimizing the use of network resources and handling varying network conditions.
    
2. **Reliability**: TCP, a connection-oriented protocol at Layer 4, ensures reliable delivery of data by implementing mechanisms such as acknowledgment, retransmission, and sequencing of data packets. This reliability is crucial for applications that require guaranteed delivery, such as web browsing, email, and file transfer.
    
3. **Flow Control**: Layer 4 protocols regulate the flow of data between sender and receiver to prevent the sender from overwhelming the receiver with data. TCP uses sliding window flow control to adjust the rate of data transmission based on the receiver's capacity, ensuring smooth and efficient data transfer.
    
4. **Error Detection and Correction**: TCP includes mechanisms for error detection through checksums and error correction through retransmission of lost or corrupted packets. This helps in maintaining data integrity and ensuring accurate delivery of information.
    
5. **Multiplexing and Demultiplexing**: Layer 4 protocols use port numbers to multiplex multiple communication streams onto a single network connection and demultiplex incoming data to the appropriate application or process running on the host. This enables concurrent communication between multiple applications on the same host.
    

### Layer 5: Session Layer

The Session layer establishes, maintains, and terminates communication sessions between applications. It handles session synchronization, checkpointing, and recovery mechanisms. While the OSI model includes this layer, many modern networking implementations merge its functionality into the Transport layer (Layer 4) or the Application layer (Layer 7).

#### Why we need Layer 5:

1. **Session Establishment**: The Session layer facilitates the establishment of communication sessions between applications by handling authentication, authorization, and session setup procedures. This ensures that the participating entities are authenticated and authorized to communicate with each other.
    
2. **Session Management**: Once a session is established, the Session layer manages the ongoing communication between applications, including session synchronization, data exchange, and flow control. It coordinates the exchange of data units (messages or segments) between the sender and receiver.
    
3. **Session Termination**: When the communication session is complete, the Session layer oversees the orderly termination of the session, ensuring that resources are released, connections are closed, and any necessary cleanup procedures are performed.
    

#### Example:

An example of the Session layer in action is during a web browsing session. When you visit a website, your web browser establishes a session with the web server to request and receive web pages. The Session layer handles the setup of this session, including authentication if necessary. During the session, the Session layer manages the exchange of data between the browser and the server, ensuring that web pages are delivered correctly. When you close the browser or navigate away from the website, the Session layer terminates the session gracefully, releasing resources and closing connections.


#networking 