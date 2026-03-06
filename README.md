# Convert-ZTAssessmentResultsToJson

The **Convert-ZTAssessmentResultsToJson** PowerShell script designed to enhabce Zero Trust Workshop delivery by allowing security experts to Zero Trust Assessment HTML test results and converts them into a structured JSON file grouped by pillar, suitable for import into the Zero Trust Workshop.

## Prerequisites

- PowerShell 5.1+ or PowerShell 7+
- An HTML file containing Zero Trust Assessment test results
- A `test-mapping.json` file mapping TestIds to TaskOverride keys (optional)

## Usage

```powershell
.\Convert-ZTAssessmentResultsToJson.ps1 -HtmlFilePath ".\ZeroTrustAssessmentReport.html"
```

### With all parameters

```powershell
.\Convert-ZTAssessmentResultsToJson.ps1 `
    -HtmlFilePath "C:\Reports\ZeroTrustAssessmentReport.html" `
    -MappingFilePath ".\test-mapping.json" `
    -OutputFilePath ".\output.json"
```

## Parameters

| Parameter | Required | Default | Description |
|---|---|---|---|
| `-HtmlFilePath` | Yes | — | Path to the input HTML file containing test results. |
| `-MappingFilePath` | No | `./test-mapping.json` | Path to TestId→TaskOverride JSON mapping file. Falls back to using TestId directly if not found. |
| `-OutputFilePath` | No | `./output.json` | Path for the generated JSON output. |
| `-KnownPillars` | No | `identity, devices, data, network, infrastructure, security-ops, ai` | List of pillars to always include in the output. |

## Mapping File

The mapping file (`test-mapping.json`) maps numeric TestIds from the HTML input to human-readable TaskOverride keys:

```json
{
  "21941": "RMI_001",
  "21892": "RMI_001",
  "21803": "RMI_052"
}
```

- Multiple TestIds can map to the same override key — their notes will be combined.
- If the mapping file is not found, the script falls back to using TestId values directly.
- Tests without a mapping entry are skipped when a mapping file is loaded.

## Output

The script produces a JSON file with the following structure:

- `metadata` — export information (version, timestamp, description)
- `configuration`
  - `applicationState` — current pillar and last modified timestamp
  - `pillars` — each pillar contains a `taskOverrides` object with entries like:
    ```json
    "RMI_001": {
      "status": "not-reviewed",
      "notes": "ZT Assessment result:\nTest result text.\n"
    }
    ```
  - `globalSettings` — application preferences
- `statistics` — counts of total, modified, and completed tasks

## How It Works

1. Reads the HTML file and extracts the `"Tests"` JSON array using bracket-balancing (handles nested brackets in string values).
2. Loads the mapping file to translate TestIds to TaskOverride keys.
3. For each test, extracts the first non-empty line from `TestResult` as the notes value.
4. Groups tests by pillar and combines notes when multiple tests map to the same override key.
5. Writes the full JSON output with metadata, configuration, and statistics.

## Files

| File | Description |
|---|---|
| `Convert-ZTAssessmentResultsToJson.ps1` | The main script. |
| `test-mapping.json` | TestId to TaskOverride mapping file. |
| `SPEC-ParseZTAssessResults.md` | Detailed specification document. |

## Submit feedback
To submit feedback, suggestions, or comments, please fill out [this form](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRz52p4S9AVBArxDJwc93Sh1UQzFSNFBRV0ZXTVVUU0xLWU9BWDZPWkpLMC4u).


