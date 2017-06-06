# Introduction to Networking by Charles Severance

## Fundamentals
- *Internet* - internetworking of routers and computers
  1. Each computer connects phyically or wirelessly to a *local area networks* (LAN) via a *router*
  2. LANs are interconnected physically or wirelessly to a create *wide area networks* (WAN)
  3. The interconnection of these WANs results in the internet
- *Routers* - are specialized computers that quickly route *packet*s of data from a source computer to a destination computer
  - *packet* - a datum subset accompanied by its source address, destination address, the data length, and the datum's "offset" value. The computer at the destination address recombines each datum, in the correct order, to form the complete original data against the data length value.

## Network Architecture
- *Four-Layer TCP/IP Model* - Link > Internetwork > Transport > Application
- *link* - wired or wireless computer connections
  - Encode and send data across link 
    - wireless = agreed upon digital encoding in signal of agreed upon radio frequencies
    - wired (electric) = agreed upon speed of bits in signal of agreed upon wire voltage
    - wired (fiber optic) = agreed upon speed of light frequencies in signal of agreed upon light frequencies
  - Async sending to support multiple computers
    - *Carrier Sense Multiple Access w/Collision Detection* (CSMA/CD) - each computer simultanously sends packets while listening for the sent signal, where it continues if it "hears" itself. It pauses for a random interval and retries if it "hears" another signal (another computer on the network). This system ensures all computers on the network share access to the router.
- *internetwork* - internet protocol (IP)
  - Routers read a packet's destination address and use a lookup table of other routers it is connected to. It then sends the packet to the next router closer to the destination address. Routers also communicate to determine if certain router connections are down to re-route packets correctly.
- *transport* - transport control protocol (TCP)
  - The destination computer notifies the source computer of what packet's it has recieved. This way, lost packets (blocked by firewalls, certain routes broke, etc.) can be re-sent. Depending on the speed of this source-destination communication, the *window size* (size of source packet) is increased or decreased to ensure, like the link layer, that all computers share the network.
- *application* - end user interaction interface (world wide web via browser for example)
  - Destination computers (servers) serve data, media, and documents to source computers (clients) that request it. Both servers and clients use an *application protocol* to exchange messages.

## Link Layer
- Each computer (including each router) has a media access control (MAC) address which uniquely identifies it.
- *base station* - the first router a computer links to
- *gateway* - a router that links a LAN to a WAN, such as the internet (base station and gateway can be same router)
- *WiFi* - a device emits a packet with its MAC address as the source and a special "broadcast" MAC address as the destination. If a gateway exists, it is listening for the broadcast address and responds with its own MAC address as the source and your MAC address as the destination. This is how the link from a device to a gateway router via WiFi works, the "handshake". The following packets have the actual destination address for data to be sent over the internet.
- *token* - an alternative to Carrier Sense Multiple Access w/Collision Detection (CSMA/CD) where only one device holds the token, and the sole right to send packets, at a time. The token is passed around so each device has equal opportunity to transmit.

## Internetworking Layer
- Since devices don't always connect to the same router, router's cannot assume where the same device will be at a given time on the network. IP Addresses are used in tandem to properly identify source and destination addresses for a packet.
- *IP address* - an address (IPv4 example is 210.10.8.12) broken in to two parts (210.10 and 8.12) where the first part is the *network number* and the second is the *host identifier*. Combined, the network number identifies the LAN or WAN on the internet to route to where the host identifier identifies which computer currently connected to that network.
- *routing table* - each router's mapping of IP addresses to outbound links where recursive routing queries (neighbors of neighbors) result in found destinations
- *time to live (TTL)* - special value sent with a packet, usually starting around 30, where each router that forwards the packet decrements it. This strategy prevents infinite loops where the router that zeros out the TTL notifies the source router, and drops the packet.
- *Dynamic Host Configuration Protocol (DHCP)* - after a computer successfully connects to a base station router, it sends another broadcast message in search of a gateway router. If one exists, the gateway router replies with a temporary IP address that the computer can use to connect to the internet.
- There are five Regional Internet Registries (RIRs) that allocate IP addresses around the world to Internet Service Providers (ISPs) which are responsible for further allocating IP addresses for a given area.

## Transport Layer
- Enables client/server applications to work by ensuring packets that arrive out of order are able to be reassembled (via packet offset and length values). Additionally, packets that never arrive can be resent via messaging between client and server. When a client receives packets it sends an acknowledgment (ACK) message notifiying which packets it has received. The transport layer may then remove the temporarily stored packets to free up space.
- A server's IP address in conjunction with its port assignments allow a client to access a specific application being served on a computer

## Application Layer
- The *HTTP* protocol is how client and server communicate.
- Headers in the messages between client and server are useful metadata that accompanies the message itself. These are example status code header ranges:
  - 2xx - success
  - 3xx - redirecting
  - 4xx - client did something wrong
  - 5xx - server did something wrong
- Common application layer protocols with port mappings:
  - FTP (21) - File Transfer
  - SSH (22) - Secure Login
  - HTTP (80) - World Wide Web
  - HTTPS (443) - Secure Web
  - IMAP (143/220/993) - Mail Retrieval

## Secure Transport Layer
- Since the Link/Internetworking/Transport layers were built for effeciency of sending packets, and not securing the data in those packets, a new partial layer (SSL/TLS) became responsible for securing data. Essentially, a client must opt-in to encrypting data before sending and the server must decrypt the data. For the case of WiFi for example anyone can sit and listen in a coffeeshop for all the various packets that are being sent over the radio waves. The packets can be read as well if the data is not encrypted.
- The *Secure Sockets Layer (SSL)* or *Transport Layer Security (TLS)* is a partial layer between the Transport layer and the Application layer that adds opt-in security to the Four-Layer TCP/IP Model
- *encrypt* - encoding plain text to cipher text
- *decrypt* - decoding cipher text to plain text
- Two types of secret keys:
  1. *shared keys* - both client and server know the *shared* key (password)
  2. *asymmetric keys* - the computer that will be receiving the encrypted data determines both the encryption key (public) and the decryption key (private) so the sender can encrypt and send the data where only the receiver can decrypt it
