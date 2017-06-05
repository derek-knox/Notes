# Introduction to Networking by Charles Severance.md

## Fundamentals
- *Internet* - internetworking of routers and computers
  1. Each computer connects phyically or wirelessly to a *local area networks* (LAN) via a *router*
  2. LANs are interconnected physically or wirelessly to a create *wide area networks* (WAN)
  3. The interconnection of these WANs results in the internet
- *Routers* - are specialized computers that quickly route *packet*s of data from a source computer to a destination computer
  - *packet* - a datum subset accompanied by its source address, destination address, and its "offset" value. The destination address recombines each datum, in the correct order, to form the original data.

## Network Architecture
- *Four-Layer TCP/IP Model* - Link > Internetwork > Transport > Application
- *link* - wired or wireless computer connections
  - Encode and send data across link 
    - wireless = agreed upon digital encoding in signal of agreed upon radio frequencies
    - wired (electric) = agreed upon speed of bits in signal of agreed upon wire voltage
    - wired (fiber optic) = agreed upon speed of light frequencies in signal of agreed upon light frequencies
  - Async sending to support multiple computers
    - *Carrier Sense Multiple Access w/Collision Detection* (CSMA/CD) - each computer simultanously sends packets while listening for the sent signal, where it continues if it "hears" itself. It pauses for a random interval and retries if it "hears" another signal (another computer on the network). This system ensures all computers on the network share access to the router.
- *internetwork* - internet protocol (IP) ...
- *transport* - transport control protocol (TCP) ...
- *application* - end user interaction interface (browser for example)
