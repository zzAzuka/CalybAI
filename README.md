## Repository File Structure

This repo documents the process of mapping **UI clicks / interactions** to the **API calls (GraphQL queries + mutations)** they trigger, along with key findings and a machine-readable workflow output.

### Files

- **`ideation.md`**
  - Contains raw exploration notes: manually inspected **queries and mutations**, trial runs, and initial discovery steps.

- **`approach.md`**
  - Describes the step-by-step **methodology for mapping UI clicks → API calls → responses**, including how to track and validate each interaction.

- **`archeology_log.md`**
  - A log of **gotchas, caveats, edge cases, and unexpected behaviors** discovered while digging through docs and real API behavior.

- **`workflow_map.json`**
  - The final **machine-readable workflow mapping** (structured JSON) that captures the full UI → API mapping in an automated/portable format.
