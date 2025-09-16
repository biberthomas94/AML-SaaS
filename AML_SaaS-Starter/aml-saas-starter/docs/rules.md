# Rules Configuration

Rules are stored as JSON documents and versioned. The engine must return an explainable rationale for every match.

## JSON Schema (informal)
```json
{
  "name": "string",
  "version": 1,
  "type": "threshold|structuring|velocity",
  "params": {
    "field": "fiat_amount",
    "operator": ">",
    "value": 10000,
    "window_hours": 24,
    "count": 3
  },
  "severity": "low|medium|high"
}
```

## Examples
### Threshold
```json
{
  "name": "Threshold_10k",
  "version": 1,
  "type": "threshold",
  "params": { "field": "fiat_amount", "operator": ">", "value": 10000 },
  "severity": "high"
}
```

### Structuring
```json
{
  "name": "Structuring_under_10k_24h",
  "version": 1,
  "type": "structuring",
  "params": { "field": "fiat_amount", "operator": "<", "value": 10000, "window_hours": 24, "count": 3 },
  "severity": "high"
}
```

### Velocity
```json
{
  "name": "Velocity_gt_5_in_2h",
  "version": 1,
  "type": "velocity",
  "params": { "window_hours": 2, "count": 5 },
  "severity": "medium"
}
```
