
# Just NoiZy
                          
# Auto_INPT_er
# 🔍 Pentesting Automation Toolkit

A comprehensive Python-based automation tool for penetration testers to scan networks, discover live hosts, identify open ports, detect services, and generate professional reports.

## Features

✅ **Fast Host Discovery** - Uses masscan for rapid host discovery across large networks  
✅ **Comprehensive Port Scanning** - Scans all 65535 ports on discovered hosts  
✅ **Service Detection** - Identifies services and versions using nmap's -sCV flags  
✅ **Parallel Processing** - Multi-threaded scanning for maximum speed  
✅ **Multiple Report Formats** - Generates JSON and HTML reports  
✅ **Professional Output** - Clean, organized reports ready for client delivery  

## Requirements

### System Tools
```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install nmap masscan python3

# Red Hat/CentOS
sudo yum install nmap masscan python3

# macOS
brew install nmap masscan python3
```

### Python
- Python 3.6 or higher (uses only standard library)

## Installation

1. Clone or download the script:
```bash
chmod +x pentest_automation.py
```

2. Create your targets file (see format below)

## Usage

### Basic Usage
```bash
sudo python3 pentest_automation.py targets.txt
```

### Advanced Options
```bash
# Specify custom output directory
sudo python3 pentest_automation.py targets.txt -o my_scan_results

# Adjust number of parallel workers (default: 10)
sudo python3 pentest_automation.py targets.txt --workers 20

# Full example
sudo python3 pentest_automation.py targets.txt -o client_scan_2024 -w 15
```

### Command Line Arguments
```
positional arguments:
  targets_file          File containing target IP ranges (CIDR notation)

optional arguments:
  -h, --help            Show help message
  -o, --output DIR      Output directory for results (default: scan_results)
  -w, --workers NUM     Number of parallel workers (default: 10)
```

## Targets File Format

Create a text file with one CIDR range per line:

```text
# Pentesting Targets File
# Lines starting with # are comments

192.168.1.0/24
10.0.0.0/24
172.16.0.0/16

# Add as many ranges as needed
```

## How It Works

### Phase 1: Host Discovery (Masscan)
- Rapidly scans all target ranges for live hosts
- Uses masscan on common ports (80, 443, 22, 21, 23, 25, 3389, 445, 139, 8080, 8443)
- Falls back to nmap if masscan is unavailable
- **Speed**: Can scan entire /16 networks in seconds

### Phase 2: Port Discovery (Nmap)
- Scans all 65535 ports on discovered hosts
- Uses SYN scanning (-sS) for speed and stealth
- Multi-threaded across multiple hosts
- **Speed**: Aggressive timing with min-rate 1000

### Phase 3: Service Detection (Nmap)
- Detailed service and version detection on open ports
- Runs default nmap scripts (-sC)
- Version detection (-sV)
- Extracts detailed service information

### Phase 4: Report Generation
- JSON format (machine-readable, for automation)
- HTML format (human-readable, professional presentation)
- Organized by host and service

## Output Structure

```
scan_results/
├── scan_results_20240127_143022.json    # Raw JSON data
├── report_20240127_143022.html           # HTML report
├── masscan_output_20240127_143022.txt    # Raw masscan output
├── nmap_ports_192.168.1.1_20240127_143022.xml    # Individual host scans
└── nmap_services_192.168.1.1_20240127_143022.xml
```

## Report Formats

### JSON Report
Complete scan data in structured format:
```json
{
  "scan_date": "2024-01-27T14:30:22",
  "targets": ["192.168.1.0/24"],
  "live_hosts": ["192.168.1.1", "192.168.1.10"],
  "detailed_scans": {
    "192.168.1.1": {
      "host": "192.168.1.1",
      "services": [
        {
          "port": "80",
          "protocol": "tcp",
          "state": "open",
          "service": "http",
          "version": "Apache httpd 2.4.41",
          "scripts": [...]
        }
      ]
    }
  }
}
```

### HTML Report
Professional, client-ready report with:
- Executive summary
- List of all live hosts
- Detailed service information per host
- Color-coded and organized layout
- Script output and findings

## Performance Tips

### For Small Networks (<256 hosts)
```bash
sudo python3 pentest_automation.py targets.txt -w 10
```

### For Medium Networks (256-4096 hosts)
```bash
sudo python3 pentest_automation.py targets.txt -w 20
```

### For Large Networks (>4096 hosts)
```bash
sudo python3 pentest_automation.py targets.txt -w 30
```

**Note**: More workers = faster scans but higher resource usage

## Scan Time Estimates

| Network Size | Host Discovery | Port Scan | Service Scan | Total |
|--------------|----------------|-----------|--------------|-------|
| /24 (256)    | ~10 seconds    | ~5 min    | ~10 min      | ~15 min |
| /20 (4096)   | ~30 seconds    | ~30 min   | ~60 min      | ~90 min |
| /16 (65536)  | ~2 minutes     | ~4 hours  | ~8 hours     | ~12 hours |

*Estimates based on 10 workers, typical network conditions*

## Security Considerations

⚠️ **Important Notes:**
- **Authorization Required**: Only scan networks you have explicit permission to test
- **Root/Sudo Required**: SYN scanning requires root privileges
- **Network Impact**: Aggressive scanning can trigger IDS/IPS systems
- **Rate Limiting**: Adjust worker count to avoid overwhelming targets

## Troubleshooting

### "masscan not found"
Install masscan or the script will automatically fall back to nmap (slower)
```bash
sudo apt-get install masscan
```

### "This script requires root privileges"
Run with sudo for SYN scanning:
```bash
sudo python3 pentest_automation.py targets.txt
```

### Scans timing out
Reduce the number of workers:
```bash
sudo python3 pentest_automation.py targets.txt -w 5
```

### No hosts discovered
- Verify target ranges are correct
- Check network connectivity
- Ensure firewall isn't blocking scans
- Try reducing scan rate in the script

## Customization

### Modify Masscan Ports
Edit line ~70 in the script:
```python
"-p", "80,443,22,21,23,25,3389,445,139,8080,8443",  # Add/remove ports
```

### Adjust Scan Timing
Modify nmap timing flags (lines ~115, ~148):
```python
"-T4",  # Change to T3 (slower) or T5 (faster)
"--min-rate", "1000",  # Adjust rate
```

### Extend Timeouts
Increase timeout values for slow networks:
```python
timeout=600  # Change to 900 or 1200
```

## Extending the Script

### Adding Database Storage
Integrate SQLite or PostgreSQL:
```python
import sqlite3
# Add database connection and storage methods
```

### Adding Excel Reports
Install openpyxl and add generation method:
```python
from openpyxl import Workbook
# Implement generate_excel_report()
```

### Adding DOCX Reports
Install python-docx and add generation method:
```python
from docx import Document
# Implement generate_docx_report()
```

## Example Workflow

```bash
# 1. Create targets file
cat > my_targets.txt << EOF
10.10.0.0/24
10.20.0.0/24
EOF

# 2. Run scan
sudo python3 pentest_automation.py my_targets.txt -o client_abc_scan

# 3. View results
firefox scan_results/report_*.html

# 4. Process JSON for automation
python3 parse_results.py scan_results/scan_results_*.json
```

## License

This tool is provided for authorized security testing only. Always obtain proper authorization before scanning any network.

## Credits

Built with:
- **nmap** - Network exploration and security auditing
- **masscan** - Fast port scanner
- **Python** - Automation and reporting

## Support

For issues or questions:
1. Check the troubleshooting section
2. Review nmap/masscan documentation
3. Verify you have proper permissions and setup

---

**Remember**: Only scan networks you have explicit authorization to test!
