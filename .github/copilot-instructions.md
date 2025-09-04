# VATSIM-SSA FASA Sector File Repository

VATSIM-SSA Sector File Development for South Africa (FASA) - Cape Town FIR (FACA) and Johannesburg FIR (FAJA). This repository contains EuroScope sector file configurations, plugin settings, and electronic AIP (eAIP) data for use in VATSIM flight simulation.

**Always reference these instructions first and fallback to search or bash commands only when you encounter unexpected information that does not match the info here.**

## Repository Overview

This is NOT a traditional software development repository. It contains:
- **EuroScope Configuration Files**: ASR (radar screen) files for air traffic control simulation
- **Plugin Configurations**: JSON configuration files for various EuroScope plugins  
- **eAIP Data**: Electronic Aeronautical Information Publication data for airports
- **Settings**: EuroScope application settings and profiles

The repository covers airspace for South Africa, Lesotho, and Eswatini with major airports FACT (Cape Town) and FAOR (Johannesburg).

## Working Effectively

### Repository Validation
**ALWAYS validate changes before committing:**
```bash
# Validate all JSON plugin configuration files
find . -name "*.json" -type f -exec python3 -c "import json; json.load(open('{}', 'r', encoding='utf-8-sig')); print('Valid: {}')" \;

# Check repository structure
ls -la ASR/ Plugins/ Settings/ eAIP/ .github/

# Validate eAIP content structure  
find eAIP -type f -name "*.txt" | head -5 | xargs -I {} sh -c 'echo "Checking {}:" && head -2 "{}"'
```

### No Traditional Build Process
**Do NOT attempt to:**
- Run `npm install`, `make`, `gradle build`, or similar build commands
- Look for compilation or traditional testing frameworks
- Search for `package.json`, `Makefile`, `pom.xml`, or similar build configuration files

### GitHub Actions Workflow
**eAIP Deployment** (deploy-eaip.yml):
- Triggers on changes to `eAIP/**` files pushed to main branch
- Deploys eAIP content to FTP server via SamKirkland/FTP-Deploy-Action
- **Deployment time: ~30 seconds for 79 files (408KB)**. NEVER CANCEL. Set timeout to 120+ seconds.
- Uses encrypted FTP credentials stored in repository secrets

## Key Directories and Files

### Repository Structure
```
/
├── .github/              # GitHub workflows and templates
├── ASR/                  # EuroScope radar screen configurations (.asr files)
├── Plugins/              # Plugin configurations (JSON files)
│   ├── TopSky/          # TopSky plugin (2.4.1) - radar display enhancement
│   ├── GroundRadar/     # Ground Radar plugin (1.5) - airport ground operations
│   ├── vATIS Profile/   # vATIS airport information configurations
│   ├── vFPC/            # Flight Plan Checker plugin (2.1.1)
│   └── [other plugins]  # Various EuroScope enhancement plugins
├── Settings/             # EuroScope application settings
│   ├── Common/          # Shared settings
│   ├── Local/           # Local configuration files
│   └── VCCS.txt         # Voice communication channel settings
├── eAIP/                # Electronic AIP data
│   ├── airport/         # Airport-specific information
│   ├── airspace/        # Airspace definitions
│   └── country/         # Country-specific data
└── .gitignore           # Excludes .sct, .ese, .rwy, .prf files (generated sector files)
```

### Critical Files to Check When Making Changes
- **JSON Plugin Files**: Always validate syntax after editing any `.json` file in `Plugins/`
- **ASR Files**: Radar screen configurations - handle with care as they control air traffic control displays
- **eAIP Content**: When modifying `eAIP/` files, ensure proper text formatting and links remain valid
- **VCCS.txt**: Voice communication settings - verify channel configurations are correct

## Validation and Testing

### JSON Validation (Required for Plugin Changes)
```bash
# Validate specific JSON file
python3 -c "import json; json.load(open('Plugins/TopSky/ICAO_Aircraft.json', 'r', encoding='utf-8-sig'))" && echo "Valid" || echo "Invalid"

# Validate all JSON files at once
find . -name "*.json" -type f -exec python3 -c "import json; json.load(open('{}', 'r', encoding='utf-8-sig')); print('Valid: {}')" \;
```

### eAIP Content Validation
```bash
# Check eAIP file structure and headers
find eAIP -name "*.txt" | head -3 | xargs -I {} sh -c 'echo "Checking {}:" && head -2 "{}"'

# Verify airport data completeness
ls eAIP/airport/
# Should show: fact, faor, and other airport codes
```

### Manual Testing Scenarios
**Always perform these checks after making changes:**

1. **Plugin Configuration Changes**: Load the modified JSON files in a JSON validator to ensure proper syntax
2. **ASR File Changes**: Verify ASR files maintain proper EuroScope format (DisplayTypeName, SECTORFILE, etc.)
3. **eAIP Changes**: Check that:
   - Airport information follows the established format (===== AIRPORT Information =====)
   - Links to external sources (Wikipedia, Chartfox) remain valid
   - Coordinate formats are consistent
   - Runway and frequency information is properly formatted

## Common Tasks

### Adding New Airport to eAIP
```bash
# Create new airport directory
mkdir -p eAIP/airport/[ICAO_CODE]
cd eAIP/airport/[ICAO_CODE]

# Create required files (following FACT template structure)
touch about.txt approach.txt briefing.txt charts.txt coordination.txt delivery.txt departure.txt ground.txt home.txt scenery.txt tower.txt wx.txt
```

### Updating Plugin Configurations
```bash
# Before editing JSON files
cp Plugins/[plugin]/[file].json Plugins/[plugin]/[file].json.backup

# After editing, validate
python3 -c "import json; json.load(open('Plugins/[plugin]/[file].json', 'r', encoding='utf-8-sig'))" && echo "Valid JSON" || echo "Invalid JSON - check syntax"
```

### Monitoring eAIP Deployment
```bash
# Check if eAIP deployment is triggered by viewing Actions tab in GitHub
# Deployment typically completes in 30-60 seconds
# Monitor: https://github.com/VATSIM-SSA/sectorfile-fasa/actions
```

## Plugin Versions and Dependencies

Current plugin versions (as indicated in README badges):
- TopSky: 2.4.1
- Ground Radar: 1.5  
- PushbackFlorian: 1.1.0
- vCH: 0.8.4
- vFPC: 2.1.1

**When updating plugin configurations:**
- Ensure JSON format compatibility with plugin versions
- Test ICAO aircraft codes against current databases
- Verify airport codes match current VATSIM sector file standards

## Important Notes

### File Exclusions (per .gitignore)
The following files are excluded and should NOT be committed:
- `*.prf` - EuroScope profile files (user-specific)
- `*.ese` - EuroScope settings files (generated)
- `*.sct` - Sector files (generated from external sources)
- `*.rwy` - Runway files (generated)
- Files in `FASA/`, `Alias/`, `NavData/`, `ICAO/`, `Sounds/` directories

### VATSIM-Specific Context
- This repository is for **flight simulation only** - not real-world aviation use
- Follows VATSIM network standards and procedures
- Coordinates with VATSIM Sub-Saharan Africa (SSA) division
- References real-world South African airspace but adapted for simulation

### Contributing Guidelines
- Refer to [VATSSA Sector File Development Overview](https://github.com/VATSIM-SSA/sectorfile-overview/wiki/How-to-Contribute)
- Use the provided PR template in `.github/PULL_REQUEST_TEMPLATE.md`
- Test changes locally before submitting
- Follow the established eAIP content format and structure

## Troubleshooting

### Common Issues
1. **JSON Syntax Errors**: Use the validation commands above to identify and fix syntax issues
2. **eAIP Deployment Failures**: Check GitHub Actions logs and verify FTP connectivity
3. **Missing Files**: Ensure required directories and files exist before referencing them
4. **Encoding Issues**: JSON files use UTF-8 with BOM - use `encoding='utf-8-sig'` when reading

### When Things Go Wrong
- Always validate JSON syntax before committing plugin changes
- Check GitHub Actions status for deployment issues
- Refer to recent commit history for working configurations
- Contact VATSIM-SSA team for airspace or procedure questions