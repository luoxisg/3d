# Ability: STEP-OCP Gap Analysis with SolidWorks ASM Support

## Overview

<purpose>
A structured approach for agents to analyze STEP (3D CAD) files and SolidWorks ASM (Assembly) ZIP files, performing comprehensive gap analysis against Open Compute Project (OCP) Open Rack v3 specifications. This ability provides systematic methodology to identify compliance issues, dimensional deviations, assembly relationship misalignments, and design misalignments between actual hardware designs and OCP Open Rack v3 standards.
</purpose>

<application>
This ability is used when:
- Analyzing 3D hardware designs (servers, chassis, network equipment, cooling systems, etc.)
- Parsing SolidWorks assembly (ASM) ZIP files containing component relationships
- Comparing design specifications against OCP Open Rack v3 standardized requirements
- Identifying mechanical assembly compliance gaps, thermal issues, and improvement opportunities
- Validating Field Replaceable Units (FRUs) and modular component design
- Generating detailed gap reports for engineering review and remediation
</application>

## Entity Model

<description>
STEP-OCP and SolidWorks ASM gap analysis spans six key analytical domains. Each domain builds on previous findings to create a complete OCP Open Rack v3 compliance assessment.
</description>

### Domain 0: SolidWorks ASM ZIP File Extraction
**Priority**: Extract first for assembly files
**Purpose**: Parse SolidWorks assembly ZIP structure and extract component hierarchy.

#### Key Entities

| Entity | Fields | Extract Methods |
|--------|--------|-----------------|
| **ZIP Structure** | File listing, metadata, compression info | Read ZIP header, list contents, identify file types |
| **Assembly Hierarchy** | ASM tree, sub-assemblies, parts, constraints | Parse .asm XML structure, extract COMPONENT nodes |
| **Component List** | Part names, quantities, file references, assembly order | Extract BOM from .asm metadata, count instances |
| **Geometric Data** | Part dimensions, weight per component, material per part | Parse .sldprt files, aggregate from sub-assemblies |
| **Assembly Relationships** | Mate types (Fixed, Coincident, Concentric, Parallel), positioning | Parse MATE nodes in .asm, extract constraint vectors |
| **Part Properties** | Mass, material code, finish, custom properties | Extract from .sldprt CUSTPROPS, material library references |
| **Configuration Data** | Active configuration, suppressed components, design variants | Identify CONFIG settings in .asm file |

**Extraction Procedure**:
```
1. Unzip ASM ZIP file to temporary location
2. Locate root .asm file (usually matches ZIP folder name)
3. Parse .asm XML structure (ISO 10303-21 or SolidWorks native format)
4. Extract COMPONENT references and MATE definitions
5. For each referenced .sldprt file:
   - Extract geometry (PART_BODY)
   - Extract mass properties (MASS element)
   - Extract material and custom properties
6. Aggregate assembly-level properties (total mass, bounding box)
7. Map component positions and constraints
```

---

### Domain 1: OCP Open Rack v3 Specification Baseline
**Priority**: Load baseline first
**Purpose**: Extract and map OCP Open Rack v3 specific requirements.

#### OCP Open Rack v3 Key Specifications

| Category | OCP v3 Requirement | Compliance Item |
|----------|-------------------|-----------------|
| **Form Factor** | 600mm (W) × 1070mm (D) × 482.6mm (H per RU) | Module must fit within these bounds |
| **Rail System** | OCP v3 19.05mm rail spacing, EIA-standard | Mounting holes must align to ±2mm |
| **Weight** | Max 30 kg per RU (distributed evenly) | Total ASM weight must not exceed limit |
| **Cooling** | 5000 CFM intake airflow per RU, 5°C inlet temp | Obstruction analysis required |
| **Power** | 12V primary, 48V auxiliary, 3200W per RU max | Connector positions and PDU compatibility |
| **Front Bezel** | 0-48RU support, component placement constraints | Hot-swap modules must be forward-accessible |
| **Rear Cabling** | Cable routing channels, max 50mm depth behind rails | Cable management compliance |
| **FRU Definition** | Field Replaceable Units must tool-less hot-swap | Identify removable sub-assemblies |
| **Vibration** | 5-500Hz, 1.0G per MIL-STD-810H Method 514.8 | Mounting point stress analysis |
| **Temperature** | 5°C to 40°C operational; -40°C to 70°C storage | Thermal pathway analysis |
| **Material** | RoHS-compliant, no restricted substances | Material code verification |
| **Grounding** | Class D grounding per IEC 61939-1 | Ground plane continuity check |
| **Accessibility** | No tools required for field service, diagnostic LEDs visible | Access path analysis |

---

### Domain 2: Assembly Structure Analysis
**Priority**: Map assembly relationships
**Purpose**: Analyze SolidWorks assembly structure against OCP modular requirements.

#### Assembly Compliance Checks

| Check | OCP v3 Requirement | Analysis Method |
|-------|-------------------|-----------------|
| **Top-Level Assembly** | Single root ASM representing complete RU module | Verify one .asm at root, no orphaned parts |
| **Subassembly Organization** | Logical grouping (Power, Cooling, Compute, I/O) | Map ASM tree to functional blocks |
| **FRU Definition** | Each replaceable unit as distinct subassembly | Identify parts marked as "replaceable" property |
| **Constraint Count** | Sufficient constraints for structural integrity | Check MATE count per subassembly (min 3 per FRU) |
| **Material Consistency** | Same materials for same functions across modules | Check material codes in properties |
| **Cable Routing** | Cables routed through designated channels | Verify no interference with moving parts |
| **Thermal Paths** | Unobstructed heat flow from CPU to heatsink to chassis | Trace thermal interface materials (TIM) |
| **Modular Interfaces** | Standard connectors at subassembly boundaries | Verify connector part numbers match OCP standard |

---

### Domain 3: Dimensional & Geometric Analysis
**Priority**: Extract and validate all critical dimensions
**Purpose**: Compare SolidWorks geometry against OCP Open Rack v3 form factor requirements.

#### Critical Dimensions for OCP Open Rack v3

| Dimension | OCP Spec | Tolerance | Source |
|-----------|----------|-----------|--------|
| **Overall Width** | 600mm (nominal) | ±2mm | .sldprt bounding box |
| **Overall Depth** | 1070mm (nominal) | ±2mm | .sldprt bounding box |
| **Height per RU** | 482.6mm (nominal) | ±1mm | Height / RU count |
| **Rail Mounting** | 19.05mm × 19.05mm rail | ±1mm hole spacing | Mount hole coordinates |
| **Front Panel** | 570mm usable width | Tolerance ±3mm | Front face geometry |
| **Rear Clearance** | 50mm max behind rails | Not to exceed | Back panel depth |
| **Cable Tray** | 100mm wide channels | Tolerance ±5mm | Cable routing path |
| **Connector Cutouts** | Position per OCP standard | ±2mm | Connector hole coordinates |
| **Thermal Interface** | 40mm × 40mm × 5mm min | Check presence | CPU heatsink contact area |
| **Vibration Mounts** | 4-point isolation required | Check attachment points | Mount location verification |

**Extraction Procedure**:
```
For each part (.sldprt) in assembly:
1. Extract bounding box (min/max X, Y, Z coordinates)
2. Identify critical mounting holes (via HOLE feature)
3. Measure rail alignment holes (extract coordinate data)
4. Identify thermal interface surfaces (check custom properties)
5. Locate connector cutouts (check SKETCH references)
6. Measure cable routing channels (extract SKETCH profiles)
7. Aggregate assembly-level dimensions from components
8. Compare against OCP v3 specifications
```

---

### Domain 4: Weight & Mass Distribution Analysis
**Priority**: Critical for RU compliance
**Purpose**: Verify weight distribution and balance per OCP Open Rack v3.

#### Weight Analysis Checks

| Check | OCP v3 Requirement | Analysis Method |
|-------|-------------------|-----------------|
| **Total Weight** | ≤30 kg per RU | Sum MASS property from all parts |
| **Weight Distribution** | ±2kg variation front-to-rear | Calculate center of gravity (CG) |
| **Component Density** | Evenly distributed (avoid top-heavy) | Check mass per functional area |
| **Mounting Point Load** | ≤15 kg per rail mount | Distribute total weight by number of mounts |
| **Cable Tray Load** | ≤5 kg (cables + trays) | Verify wire gauge and support points |
| **Heatsink Mount** | Direct CPU-to-chassis contact, ≥50W/K | Check TIM area and material |

**Extraction Procedure**:
```
1. For each part in ASM:
   - Extract MASS property (kg)
   - Extract MATERIAL code (check density if available)
2. Aggregate total mass across all parts
3. Calculate center of gravity (CG):
   - CG_x = Σ(mass_i × x_i) / Σ(mass_i)
   - CG_y = Σ(mass_i × y_i) / Σ(mass_i)
   - CG_z = Σ(mass_i × z_i) / Σ(mass_i)
4. Verify CG is within ±50mm of geometric center
5. Check distribution across mounting points
```

---

### Domain 5: Thermal & Airflow Analysis
**Priority**: High for cooling-intensive modules
**Purpose**: Identify cooling system compliance with OCP Open Rack v3.

#### Thermal Checks for OCP Open Rack v3

| Check | OCP Spec | Verification |
|-------|----------|--------------|
| **Intake Airflow** | 5000 CFM per RU @ 5°C inlet | Check obstruction analysis |
| **Outlet Temp** | ≤40°C (nominal operation) | Verify thermal pathway design |
| **Thermal Interface** | CPU-to-heatsink, ≥0.4°C/W | Check TIM material and area |
| **Heatsink Area** | ≥300 cm² exposed surface | Calculate from 3D model |
| **Air Gap** | 8-10mm between heatsink and chassis | Verify clearance distances |
| **Airflow Blockage** | No obstructions in airflow path | Check for interfering cables/parts |
| **Fan Mounting** | 120mm fans standard, proper intake/exhaust | Verify fan housing geometry |
| **Coolant (if liquid)** | Ethylene glycol based, 30-50% concentration | Check material specs |

**Extraction Procedure**:
```
1. Identify heatsink components (check name = "*SINK*" or material = "Aluminum")
2. Extract heatsink dimensions:
   - Surface area from FACE polygons
   - Fin spacing from SKETCH profiles
   - Thermal resistance from custom property "R_th"
3. Identify CPU location (search for "CPU" or "Processor" part)
4. Calculate thermal path:
   - Distance CPU → TIM interface
   - Distance TIM → heatsink base
   - Distance heatsink → chassis
5. Identify airflow path:
   - Inlet area (front panel opening)
   - Obstruction zones (cables, components)
   - Outlet area (rear exhausts)
6. Check for clearance violations (< 8mm gaps)
```

---

### Domain 6: FRU (Field Replaceable Unit) Modularity Analysis
**Priority**: Core to OCP design philosophy
**Purpose**: Validate hot-swap capability and field serviceability.

#### FRU Compliance Checks

| Check | OCP v3 Requirement | Verification Method |
|-------|-------------------|-------------------|
| **FRU Definition** | Subassemblies marked as "replaceable" | Check CUSTPROPS: REPLACEABLE=TRUE |
| **Hot-Swap Connector** | OCP standard connector types (Molex, TE, etc.) | Extract connector part numbers |
| **Tool-Less Access** | No screws/bolts to remove FRU | Check for fastener constraints |
| **Cable Routing** | Quick-disconnect connectors | Verify connector type compatibility |
| **Unique Keying** | Prevents incorrect insertion | Check connector asymmetry in CAD |
| **Status Indicators** | LED for each FRU present/failed | Verify LED mounting locations |
| **Service Label** | Each FRU labeled for identification | Check for LABEL feature in CAD |
| **Spare Parts Availability** | Each FRU has assigned part number | Verify part number in BOM |
| **Removal Sequence** | Documented removal order | Check assembly tree order |
| **No Single Points of Failure** | Critical parts redundantly mounted | Verify constraint count |

**Extraction Procedure**:
```
1. Scan all subassemblies for REPLACEABLE property
2. For each replaceable subassembly:
   - Extract subassembly name and part number
   - Identify connector types (search for connector part names)
   - Count fasteners (search for SCREW, BOLT, NUT features)
   - Check for quick-disconnect properties
   - Verify LED mounting locations
   - Extract service label references
3. Generate FRU list:
   - Part number
   - Description
   - Connector type
   - Removal tool requirements (if any)
   - Spare parts availability status
4. Assess modularity score (% of parts in replaceable subassemblies)
```

---

### Domain 7: Gap Identification
**Priority**: Analysis core
**Purpose**: Systematically compare SolidWorks ASM geometry against OCP Open Rack v3 requirements.

#### Gap Types for OCP Open Rack v3

| Category | Gap Types | Examples |
|----------|-----------|----------|
| **Dimensional Gaps** | Out-of-Spec, Undersized, Misaligned | Width exceeds 600mm, depth > 1070mm, rail holes misaligned |
| **Assembly Gaps** | Missing constraints, insufficient mates | FRU not properly constrained, floating parts |
| **Weight Gaps** | Exceeds 30kg RU limit, CG imbalanced | Total > 30kg, CG offset > 50mm from center |
| **Thermal Gaps** | Insufficient cooling, blocked airflow, poor TIM | Heatsink area < 300cm², airflow obstructed |
| **FRU Gaps** | Non-replaceable critical parts, tool-required access | CPU soldered to board, requires screwdriver to remove |
| **Material Gaps** | RoHS violation, incompatible materials | Lead solder used, dissimilar metal corrosion risk |
| **Connector Gaps** | Non-standard connectors, insufficient keying | 2.54mm headers instead of OCP standard, reversible insertion |
| **Accessibility Gaps** | Service components not accessible, no diagnostic LEDs | CPU heatsink behind non-removable panel |

---

### Domain 8: Risk & Impact Assessment
**Priority**: Business context
**Purpose**: Evaluate severity and business implications of identified gaps.

#### Severity Scoring for OCP Open Rack v3

| Severity | Definition | Color | OCP v3 Examples |
|----------|-----------|-------|-----------------|
| **CRITICAL** | Prevents RU installation, safety risk, violates OCP spec | 🔴 Red | Width > 610mm, weight > 35kg, no hot-swap capability |
| **HIGH** | Major compliance issue, impacts cooling/reliability | 🟠 Orange | CG imbalanced (>100mm offset), airflow blocked, non-standard FRU |
| **MEDIUM** | Workaround possible, affects field serviceability | 🟡 Yellow | Heatsink < 250cm², requires 1 tool to remove, non-standard connector |
| **LOW** | Minor issue, optional improvements | 🟢 Green | Missing service label, cosmetic finish, documentation gap |

---

### Domain 9: Remediation Roadmap
**Priority**: Actionable guidance
**Purpose**: Generate prioritized, actionable remediation steps for OCP Open Rack v3 compliance.

#### Remediation Options (OCP v3 Specific)

For each identified gap, provide three options:

**Option A (Preferred) — CAD Design Change**
- Detailed SolidWorks modification specification
- BOM impact (add/remove/modify components)
- New drawings/assemblies required
- Verification method (CAD validation, thermal FEA, compliance check)
- Timeline and cost estimate
- Risk residual after fix

**Option B (Workaround) — Assembly Procedure Change**
- Modify assembly sequence
- Add shims or spacers
- Adjust cable routing
- Field retrofit procedure (if post-deployment)

**Option C (OCP Exception) — Specification Waiver**
- Technical justification for non-compliance
- Precedent in OCP approved designs
- OCP Architecture Review Board (ARB) approval process
- Risk acceptance statement and conditions

---

## Traversal Strategy

### Step 0: Clarify User Intent & File Type
**Instruction**: Determine file type and analysis scope.

#### Intent Types

| Intent | Description | File Type | Scope |
|--------|-------------|-----------|-------|
| **basic_compliance_check** | Is this design compliant with OCP v3? | STEP, ASM ZIP | Extract parameters, list requirements, identify obvious gaps |
| **detailed_gap_report** | Comprehensive gap analysis with remediation | STEP, ASM ZIP | All domains; prioritized gaps with severity and fixes |
| **pre_certification** | Find issues before OCP audit | ASM ZIP (preferred) | High-impact gaps; FRU validation; thermal analysis |
| **assembly_validation** | Validate assembly structure and relationships | ASM ZIP only | BOM analysis, constraint verification, FRU mapping |
| **fru_analysis** | Assess field replaceability and modularity | ASM ZIP only | Hot-swap validation, connector analysis, service procedures |

#### File Type Detection

| File Type | Detection | Processing |
|-----------|-----------|-----------|
| **.step / .stp** | Single file, ISO 10303 format | Domain 1: STEP extraction → Domains 3-9 |
| **ASM ZIP file** | ZIP archive containing .asm + .sldprt | Domain 0: ASM extraction → Domains 2-9 (enhanced) |

### Step 1: Acquire Design File
**Instruction**: Obtain the design file or ASM ZIP.

**Sources**:
- GitHub repository link to `.step`, `.stp`, or `.zip` file
- SolidWorks project zip (exported from SolidWorks)
- Design documentation (PDF, BOM spreadsheet)
- Architect's design intent description

**If ASM ZIP provided**, extract and verify:
```
1. Check ZIP integrity (no corruption)
2. Verify root .asm file exists
3. Check all referenced .sldprt files are present
4. Confirm no missing external references
5. Validate XML structure in .asm file
```

### Step 2: Identify OCP Baseline
**Instruction**: Confirm OCP Open Rack v3 as target specification.

**For OCP Rack v3**:
- Standard form factor: 600mm W × 1070mm D × 482.6mm H per RU
- Open Rack v3 specifications: https://www.opencompute.org/documents/ocp-open-rack-specification-3-0-pdf
- Key standards: IEC 61075 (19" rack), ISO 10303 (data exchange)

### Step 3: Extract Files & Parse Assembly (ASM ZIP Only)
**Instruction**: If ASM ZIP, extract and parse assembly structure.

**Procedure**:
```
1. Extract ZIP to temporary directory
2. Parse root .asm file (XML/text format)
3. Build component tree:
   - Identify all COMPONENT references
   - Map part file names to tree locations
   - Extract MATE constraints
4. For each referenced .sldprt:
   - Extract MASS property
   - Extract MATERIAL code
   - Extract CUSTPROPS (especially REPLACEABLE flag)
   - Extract dimensional bounds
5. Aggregate assembly-level properties
6. Generate component list (BOM)
```

**Output**:
```
ASM Structure Summary
|Component|Type|Quantity|Mass (kg)|Material|Replaceable|
|CPU Module|Subassembly|1|0.5|Aluminum|YES|
|...||||||
|TOTAL|Assembly|—|28.5|Mixed|—|
```

### Step 4: Extract Dimensions & Geometry
**Instruction**: Compile all critical dimensional parameters.

**Output**:
```
Dimensional Analysis (OCP Open Rack v3)
|Dimension|Value|OCP Spec|Tolerance|Status|
|Width|600.2mm|600mm ±2mm|±2mm|✓ PASS|
|Depth|1072mm|1070mm ±2mm|±2mm|✗ FAIL (+2mm)|
|Height|482.8mm|482.6mm ±1mm|±1mm|✓ PASS|
|Total Mass|29.8kg|≤30kg|±1kg|✓ PASS|
|CG Offset|45mm|≤50mm|±10mm|✓ PASS|
```

### Step 5: Validate Assembly Structure
**Instruction**: Check assembly relationships against OCP v3 modular design.

**Output**:
```
Assembly Validation (OCP Open Rack v3)
|Check|Result|Status|Notes|
|Top-level ASM|Single root.asm|✓ PASS|Properly organized|
|Subassemblies|4 functional groups|✓ PASS|Power, Cooling, Compute, I/O|
|FRU Count|3 replaceable|✓ PASS|CPU, PSU, Fan modules|
|Constraint Integrity|28 mates per FRU avg|✓ PASS|Sufficient structural stability|
|Tool-less Access|No screws on FRU|✓ PASS|All quick-disconnect|
```

### Step 6: Analyze FRUs & Modularity
**Instruction**: Validate field replaceability and hot-swap capability.

**Output**:
```
FRU Analysis (OCP Open Rack v3)
|FRU|Part #|Connector|Status|Removal Tools|Spare Available|
|CPU Module|ABC-001|OCP Std Molex|✓ OK|None|YES|
|PSU|XYZ-100|Anderson SB50|✓ OK|None|YES|
|Fan Array|FAN-001|Quick-Disc|✓ OK|None|YES|
```

### Step 7: Conduct Gap Analysis
**Instruction**: Compare all extracted data against OCP Open Rack v3 specs.

**Output**:
```
Gap Summary (OCP Open Rack v3)
|Gap ID|Category|Description|OCP Spec|Actual|Severity|Fix|
|GAP-001|Dimensional|Depth exceeds spec|1070mm|1072mm|HIGH|Reduce cable tray depth|
|GAP-002|Thermal|Heatsink area low|≥300cm²|285cm²|HIGH|Upgrade heatsink fins|
|GAP-003|FRU|Fan module requires tool|Tool-less|Requires 1 screw|MEDIUM|Use quick-release clip|
```

### Step 8: Risk & Impact Assessment
**Instruction**: Evaluate severity for OCP v3 certification readiness.

**Output**:
```
Compliance Status: CONDITIONAL GO — 1 blocker, 2 tracking items

Gap Priority Matrix:
|Severity|Count|Certification Impact|Timeline|
|CRITICAL|1|BLOCKS certification|URGENT (< 1 week)|
|HIGH|2|Delays certification|2-3 weeks|
|MEDIUM|3|Post-launch OK|Post-launch|
|LOW|2|Optional improvement|Backlog|
```

### Step 9: Remediation Roadmap
**Instruction**: Propose concrete remediation options for OCP v3 compliance.

**Output** (for each CRITICAL/HIGH gap):
```
Remediation Option A: CAD Redesign (RECOMMENDED)
- Modify cable tray depth: 50mm → 48mm
- SolidWorks change: Sketch depth constraint
- BOM impact: Cable tray bracket (reduce count by 2)
- Verification: CAD check + airflow FEA simulation
- Timeline: 1 week
- Cost: 2 eng-hours
- Risk residual: None

Remediation Option B: Workaround
- Use thinner cables (0.5mm flex insulation)
- Deploy with +2mm depth waiver
- Risk: Fails OCP certification

Remediation Option C: OCP Waiver
- Request exception for +2mm depth
- Justification: "Improves cooling airflow efficiency"
- OCP approval: ~50% likelihood
- Risk: Certification delayed
```

### Step 10: Generate Final OCP v3 Certification Report
**Instruction**: Compile findings into executive certification report.

**Report Structure**:

1. **Executive Summary** (1 page)
   - Design overview
   - Compliance status: GO/NO-GO/CONDITIONAL GO
   - Critical gaps (if any)
   - Certification readiness: READY / NEEDS WORK / NOT READY
   - Timeline to certification

2. **OCP v3 Specification Compliance Matrix** (detailed table)
   - All requirements vs. current design status

3. **Detailed Gap Analysis** (by severity)
   - CRITICAL gaps with immediate remediation
   - HIGH gaps with 2-3 week fix plan
   - MEDIUM gaps (post-launch tracking)
   - LOW gaps (nice-to-have improvements)

4. **FRU & Modularity Assessment**
   - FRU list with hot-swap validation
   - Service procedures documented
   - Spare parts availability

5. **Thermal & Mechanical Validation**
   - Weight distribution and CG analysis
   - Airflow and cooling capacity
   - Vibration mounting points

6. **Remediation Roadmap**
   - Gantt chart: Fix priority, timeline, owner
   - Blocking dependencies
   - Sign-off criteria for OCP certification

7. **Appendices**
   - ASM structure diagram (if ASM ZIP)
   - BOM (Bill of Materials)
   - OCP v3 specification excerpts
   - CAD data extraction tables

---

## Token Efficiency Guidelines

### ASM ZIP Processing Priority
1. **Extract ASM structure** (fast, high-value)
2. **Parse BOM** (needed for weight/cost analysis)
3. **Extract critical dimensions** (needed for compliance)
4. **Skip detailed geometry** (unless user asks)

### FRU Analysis Optimization
- Don't expand all sub-assemblies unless needed
- Prioritize replaceable subassemblies (marked property)
- Skip non-replaceable parts analysis (lower priority)

### Gap Reporting Strategy
- **CRITICAL gaps**: Full 4-option remediation analysis
- **HIGH gaps**: 2-3 options with cost/timeline trade-off
- **MEDIUM gaps**: Brief option + backlog recommendation
- **LOW gaps**: Acknowledge only, defer to v2.0

---

## Output Format

### Example 1: ASM ZIP Compliance Check (OCP v3)

```
**Compliance Status**: CONDITIONAL GO — Design ready with 2 fixes

**Quick Assessment**:
✓ Dimensions: Within 600×1070×482.6mm spec
✓ Weight: 29.8kg (within 30kg limit)
✗ Depth overrun: 1072mm vs. 1070mm spec (+2mm) — Exceeds tolerance
✗ Heatsink area: 285cm² vs. 300cm² spec — Insufficient cooling
✓ FRU modularity: 3 tool-less hot-swap modules
✓ Connectors: All OCP standard types

**Recommendation**: Fix dimensional/thermal gaps before certification. Both have straightforward CAD solutions (1-2 weeks).

**Next Steps**: Detail remediation roadmap?
```

### Example 2: Detailed OCP v3 Gap Report (ASM ZIP)

```
**OCP Open Rack v3 Certification Readiness**: CONDITIONAL GO

**Certification Timeline**: 3 weeks (1 critical, 2 high-priority gaps)

**Gap Analysis Matrix**:
|Gap ID|Domain|Description|Spec|Actual|Fix Timeline|Owner|
|GAP-001|Dimensional|Depth|1070mm|1072mm|Week 1|Mech Eng|
|GAP-002|Thermal|Heatsink Area|300cm²|285cm²|Week 1-2|Thermal Eng|
|GAP-003|FRU|Fan removal|Tool-less|1 screw|Week 2|Design Eng|

**Critical Path**: GAP-001 + GAP-002 fix in parallel, then certification testing (Week 3).

[See detailed remediation options below...]
```

---

## Edge Cases

### Case 1: ASM ZIP with Missing References
**Scenario**: ASM ZIP has broken .sldprt references (files missing).
**Approach**:
- List missing parts and estimate their mass/dimensions
- Flag as "Incomplete data — requires design review"
- Ask user for missing files or latest version

### Case 2: Multiple Configurations in ASM
**Scenario**: ASM has multiple configurations (e.g., 1RU vs 2RU variants).
**Approach**:
- Analyze active configuration by default
- Ask user which configuration(s) to analyze
- Generate separate gap reports per configuration if needed

### Case 3: OCP v3 Amendment or Custom Profile
**Scenario**: Customer uses modified OCP v3 spec (subset/superset).
**Approach**:
- Use OCP v3 as baseline
- Ask user for custom spec document
- Assess compliance against both standard and custom specs

### Case 4: Thermal Simulation Data Available
**Scenario**: User provides CFD/thermal FEA results with ASM.
**Approach**:
- Extract thermal data from simulation files (if available)
- Cross-check against ASM geometry
- Use simulation data to refine cooling analysis

### Case 5: Legacy Components in ASM
**Scenario**: ASM contains non-standard or obsolete connectors.
**Approach**:
- Flag as non-OCP compliant
- Propose pin-compatible OCP standard alternative
- Estimate retrofit cost and timeline

---

## References

### OCP Standards
- **OCP Open Rack Specification v3.0** — https://www.opencompute.org/documents/ocp-open-rack-specification-3-0-pdf
- **OCP Interim Server Power Supply Specification** — For 12V primary, 48V auxiliary rails
- **OCP Architecture Review Board (ARB)** — For specification waivers and exceptions

### Industry Standards
- **ISO 10303 (STEP)** — Product data exchange standard
- **IEC 61075** — 19" and 23" rack systems
- **IEEE 1101.1** — Dimensional specifications for 19" racks
- **MIL-STD-810H Method 514.8** — Vibration testing procedures
- **IEC 61939-1** — Grounding and earthing standards

### SolidWorks Files
- **.asm** — SolidWorks Assembly file (text-based XML structure)
- **.sldprt** — SolidWorks Part file (binary or XML format)
- **.sldasm** — Alternative assembly extension (same as .asm)

---

## Checklist for Users

### Before Gap Analysis
- [ ] ASM ZIP or STEP file accessible and intact
- [ ] Target: OCP Open Rack v3 confirmed
- [ ] SolidWorks version documented (for ASM compatibility)
- [ ] All .sldprt files included in ZIP (no missing references)
- [ ] Custom properties populated (esp. REPLACEABLE, MATERIAL, MASS)
- [ ] Design freeze status confirmed (no pending changes)

### For ASM ZIP Analysis
- [ ] Assembly hierarchy well-organized (logical grouping)
- [ ] All mates defined (no floating/unconstrained parts)
- [ ] Mass properties calculated for all parts
- [ ] Material codes assigned to all components
- [ ] FRUs marked with REPLACEABLE=TRUE property
- [ ] Connector part numbers verified

### After Gap Analysis
- [ ] All CRITICAL gaps have proposed design changes
- [ ] HIGH gaps prioritized on 2-3 week timeline
- [ ] MEDIUM/LOW gaps tracked or backlog-scheduled
- [ ] Remediation roadmap reviewed with engineering team
- [ ] FRU validation complete (hot-swap capability confirmed)
- [ ] Thermal/mechanical analysis reviewed
- [ ] OCP v3 certification readiness confirmed

### Pre-Certification Audit
- [ ] All CRITICAL and HIGH gaps remediated
- [ ] Design documentation (PDFs) finalized
- [ ] BOM and part numbers verified
- [ ] FRU service procedures documented
- [ ] Thermal and vibration analysis complete
- [ ] OCP v3 specification compliance matrix 100% signed off

---

**Ready to analyze a SolidWorks ASM ZIP file against OCP Open Rack v3? Provide the ZIP file link and I'll generate a comprehensive compliance report!** 🚀
