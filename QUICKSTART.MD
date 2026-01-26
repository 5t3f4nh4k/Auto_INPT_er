# 🚀 QUICK START GUIDE

## Installation (5 minutes)

### 1. Install Dependencies
```bash
# Ubuntu/Debian/Kali
sudo apt-get update
sudo apt-get install -y nmap masscan python3

# Optional: For enhanced reports
pip install python-docx openpyxl
```

### 2. Download Scripts
```bash
# Make scripts executable
chmod +x pentest_automation.py generate_reports.py
```

### 3. Create Targets File
```bash
cat > targets.txt << EOF
192.168.1.0/24
10.0.0.0/24
EOF
```

## Basic Usage

### Run a Complete Scan
```bash
sudo python3 pentest_automation.py targets.txt
```

That's it! Results will be in the `scan_results/` directory.

### View Results
```bash
# Open HTML report in browser
firefox scan_results/report_*.html

# Or check JSON results
cat scan_results/scan_results_*.json
```

## Advanced Usage

### Adjust Speed (More Workers = Faster)
```bash
# Default: 10 workers
sudo python3 pentest_automation.py targets.txt

# Fast: 20 workers
sudo python3 pentest_automation.py targets.txt -w 20

# Very Fast: 30 workers (high resource usage)
sudo python3 pentest_automation.py targets.txt -w 30
```

### Custom Output Directory
```bash
sudo python3 pentest_automation.py targets.txt -o client_abc_pentest
```

### Generate Additional Reports (DOCX/XLSX)
```bash
# Generate DOCX report
python3 generate_reports.py scan_results/scan_results_*.json --docx

# Generate Excel report
python3 generate_reports.py scan_results/scan_results_*.json --xlsx

# Generate both
python3 generate_reports.py scan_results/scan_results_*.json --all
```

## Common Scenarios

### Scenario 1: Quick Internal Network Scan
```bash
# Single /24 network
echo "192.168.1.0/24" > targets.txt
sudo python3 pentest_automation.py targets.txt
```

### Scenario 2: Multiple Subnets
```bash
cat > targets.txt << EOF
10.10.1.0/24
10.10.2.0/24
10.10.3.0/24
172.16.0.0/24
EOF

sudo python3 pentest_automation.py targets.txt -w 15
```

### Scenario 3: Large Enterprise Network
```bash
# Scan a /16 (65,536 IPs)
echo "10.0.0.0/16" > targets.txt
sudo python3 pentest_automation.py targets.txt -w 30 -o enterprise_scan

# This will take several hours - perfect time for coffee ☕
```

## Understanding Output

### File Structure
```
scan_results/
├── scan_results_20240127_143022.json    # All data (machine-readable)
├── report_20240127_143022.html           # Pretty report (open in browser)
├── masscan_output_*.txt                  # Raw masscan results
├── nmap_ports_*.xml                      # Individual nmap scans
└── nmap_services_*.xml                   # Service detection scans
```

### What Gets Scanned

**Phase 1 - Host Discovery (Masscan)**
- Scans for: 80, 443, 22, 21, 23, 25, 3389, 445, 139, 8080, 8443
- Purpose: Find which IPs are alive
- Speed: Very fast (seconds per /24)

**Phase 2 - Port Discovery (Nmap)**
- Scans: All 65,535 ports
- Purpose: Find all open ports
- Speed: Fast (minutes per host)

**Phase 3 - Service Detection (Nmap)**
- Scans: Only the open ports found
- Purpose: Identify service versions
- Includes: Default nmap scripts (-sC)
- Speed: Moderate (depends on number of services)

## Troubleshooting

### "Permission denied" or scan fails
```bash
# Always use sudo for SYN scans
sudo python3 pentest_automation.py targets.txt
```

### "masscan not found"
```bash
# Install masscan
sudo apt-get install masscan

# Or let script use nmap fallback (automatic, but slower)
```

### Scan is too slow
```bash
# Increase workers
sudo python3 pentest_automation.py targets.txt -w 20

# Or reduce target scope
```

### No hosts found
```bash
# Verify network is reachable
ping 192.168.1.1

# Check targets file format
cat targets.txt

# Try manual nmap test
nmap -sn 192.168.1.0/24
```

## Tips & Tricks

### 1. Resume a Scan
If a scan is interrupted, you can't resume (by design for accuracy).
Instead, narrow down your targets:
```bash
# Only scan newly discovered hosts
cat scan_results/scan_results_*.json | grep "live_hosts"
```

### 2. Scan Specific Hosts
```bash
# Create targets file with individual IPs
cat > specific_hosts.txt << EOF
192.168.1.10
192.168.1.20
192.168.1.30
EOF

sudo python3 pentest_automation.py specific_hosts.txt
```

### 3. Compare Scans Over Time
```bash
# Run scans on different days
sudo python3 pentest_automation.py targets.txt -o scan_monday
sudo python3 pentest_automation.py targets.txt -o scan_friday

# Compare JSON files
diff scan_monday/scan_results_*.json scan_friday/scan_results_*.json
```

### 4. Parse JSON with jq
```bash
# Install jq
sudo apt-get install jq

# Extract all live hosts
cat scan_results/scan_results_*.json | jq '.live_hosts[]'

# Count services per host
cat scan_results/scan_results_*.json | jq '.detailed_scans | to_entries[] | {host: .key, services: .value.services | length}'

# Find all HTTP services
cat scan_results/scan_results_*.json | jq '.detailed_scans[] | .services[] | select(.service == "http")'
```

## Time Estimates

| Network Size | Hosts | Time (10 workers) | Time (20 workers) |
|--------------|-------|-------------------|-------------------|
| /24          | 256   | ~15 minutes       | ~10 minutes       |
| /23          | 512   | ~30 minutes       | ~20 minutes       |
| /22          | 1024  | ~1 hour           | ~40 minutes       |
| /20          | 4096  | ~3 hours          | ~2 hours          |
| /16          | 65536 | ~12 hours         | ~8 hours          |

*Times assume average network with ~10% live hosts and ~5 services per host*

## Security Reminder

⚠️ **CRITICAL**: Only scan networks you own or have written authorization to test!

Unauthorized scanning is:
- **Illegal** in most jurisdictions
- **Unethical**
- Can trigger **alarms and investigations**
- May result in **criminal charges**

Always get written permission before scanning!

## Next Steps

1. ✅ Run your first scan
2. ✅ Review the HTML report
3. ✅ Generate DOCX/XLSX for clients (if needed)
4. ✅ Customize the script for your workflow
5. ✅ Integrate into your pentesting methodology

## Getting Help

- Read the full README.md for detailed documentation
- Check nmap documentation: `man nmap`
- Check masscan documentation: `masscan --help`
- Review the script code for customization options

---

Happy Hunting! 🎯
