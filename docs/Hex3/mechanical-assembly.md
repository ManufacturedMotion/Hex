
## Print Recommendations

!!! note
    PETG can be used, but we recommend printing in ABS or ASA for better strength and heat resistance.
    
All structural parts are sliced in PrusaSlicer using the `0.2mm STRUCTURAL` print profile on a Prusa Core ONE.

| Parts | Walls | Infill | Supports |
|---|---|---|---|
| Default (most parts) | 4 | 30% triangles | As needed |
| `base_core` & toes | 12+ | 30% triangles | As needed |

- `base_core` see the highest structural loads, so it's printed with 12+ walls to make them effectively solid rather than relying on infill.
- The toes are also printed with these settings as they will wear down over time.
