# Evidence Analysis & Chain-of-Custody
**Tools:** Velociraptor, FTK Imager, sha256sum  

---

## 5.1 Host-Based Network Analysis (Velociraptor)
**Objective:** Identify active command-and-control (C2) beacons or unauthorized data exfiltration tunnels using live endpoint forensics.

### Execution Steps:
1. **Collector Deployment:** Accessed the Windows Server-Z VM via the Velociraptor dashboard.
2. **VQL Artifact Execution:** Ran the `Windows.Network.Netstat` artifact to pull all active sockets.
3. **Query Logic:** ```sql
   SELECT LocalIP, LocalPort, RemoteIP, RemotePort, State, Pid, 
          { SELECT Name FROM proc_info(pid=Pid) } AS ProcessName
   FROM Out
   WHERE RemoteIP != "0.0.0.0" AND RemoteIP != "127.0.0.1"
