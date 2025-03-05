
- Grand Masters (GMs) The root timing reference
- Slave Clocks (SC)
- Time Of Day (TOD)
- Boundary Clock (BC) It has one or several Client ports where it can receive timing and one or several master ports where the BC delivers timing,
- Ordinary Clocks (OC)
- The Transparent Clock（TC） timestamps the ingress 1588 PTP traffic, then it correlates for the time the 1588 PTP traffic spent inside the node on the egress.
- the clocks discover and configure each other using announce messages.
- Sync, Delay_Req and Delay_Resp messages are used to measure the time delay on backward and forward direction and to convey the time and phase information to the clients.
- The G.8275.1 Full Timing Support (FTS) profile runs over Untagged ethernet and is always supported by SyncE. The profile uses multicast traffic with the reserved multicast address(s) and Ethertype **88F7**.