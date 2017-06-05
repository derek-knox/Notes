# Introduction to Networking by Charles Severance.md

## Fundamentals
- *Internet* - internetworking of routers and computers
  1. Each computer connects phyically or wirelessly to a *local area networks* (LAN) via a *router*
  2. LANs are interconnected physically or wirelessly to a create *wide area networks* (WAN)
  3. The interconnection of these WANs results in the internet
- *Routers* - are specialized computers that quickly route *packet*s of data from a source computer to a destination computer
  - *packet* - a datum subset accompanied by its source address, destination address, the data length, and the datum's "offset" value. The destination address recombines each datum, in the correct order, to form the complete original data against the data length value.

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
