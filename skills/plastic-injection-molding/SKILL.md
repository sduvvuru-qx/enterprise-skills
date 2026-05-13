---
name: Plastic Injection Molding
description: Use when designing, reviewing, or troubleshooting plastic injection-molded components for HV inverter applications. Covers material selection, wall thickness, ribs, draft angles, insert molding, gate design, tolerancing, process capability, and supplier qualification. Trigger on plastic design, insert molding, injection molding, molded busbar, DC bus block, Cpk Ppk molded, plastic standard, SASG plastic, rib design, draft angle, wall thickness plastic, gate position, insert shift, PBT PPS PA66, creepage clearance molded.
icon: 🧱
metadata:
  category: engineering/design
  family: astemo-engineering
  lifecycle: active
  canonical_slug: plastic-injection-molding
  crystallized_from: "2026-05-03 XP7G DC Bus Block insert molding deep review"
  programs: [XP7G, FH4S, FH4L, XY9H, future-BEV]
  sasg_references: [K11-01A, K10-01A, K11-03A, N41-02A, K11-12A, K22-03A]
---

# Plastic Injection Molding — HV Inverter Design Reference

## When to Use This Skill

- Designing plastic molded parts for HV inverters (AC mold, DC mold, brackets, covers)
- Reviewing insert-molded components (busbar position, insert nut concentricity)
- Evaluating supplier Cpk/Ppk for molded parts
- Writing or reviewing a plastic design standard (SASG format)
- Troubleshooting insert shift, warpage, sink marks, or dimensional instability
- Preparing DFMEA for plastic/insert-molded components
- Supplier qualification for injection molding

## Quick Reference Tables

### Material Selection

| Resin | GF% | Mold Temp | CTI | Key Use |
|-------|:---:|:---------:|:---:|---------|
| PBT-GF30 | 30 | 80–100°C | 250V | DC molds, covers |
| PPS-GF40 | 40 | 130–150°C | 175V | AC molds, busbars |
| PA66-GF30 | 30 | 80–100°C | 600V | Brackets, sensors |
| PPA-GF33 | 33 | 120–140°C | 500V | High-temp brackets |
| LCP-GF30 | 30 | 100–120°C | 150V | Thin-wall, high-flow |

Minimum: UL 94 V-0, CTI ≥ 175V, dielectric ≥ 20 kV/mm, continuous temp ≥ 140°C.

### Wall Thickness

| Material | Min | Max | Rule |
|----------|:---:|:---:|------|
| PC (amorphous) | 0.6 mm | 3.0 mm | Uniform Δt ≤ 10% |
| PBT-GF | 0.8 mm | 3.0 mm | Taper transitions ≥ 3:1 |
| PPS-GF | 1.0 mm | 3.0 mm | Same |
| PA66-GF | 0.8 mm | 3.0 mm | Same |

### Rib Design

| Parameter | Amorphous | Semi-crystalline |
|-----------|:---:|:---:|
| Rib thickness / wall | 0.5–0.7 | **0.25–0.35** |
| Draft per side | ≥ 0.5° | ≥ 1.0° |
| Root radius | R = 0.4–0.5 × S | R = 0.4–0.5 × S |

No rib crossings. Align with flow direction.

### Draft Angles

| Surface | Min | Recommended |
|---------|:---:|:---:|
| Polished, amorphous | 0.5° | 1° |
| Polished, semi-cryst. | 1.0° | 1.5° |
| Textured (VDI 24+) | +0.5° per grade | — |

### Insert Molding Rules

**Six mechanisms of insert shift (from Watanabe article + XP7G Ironwood):**

1. **Resin flow pressure** — asymmetric gate → net lateral force on insert
2. **Thermal expansion** — ΔL = α × L × ΔT (steel: ~12 μm per 8mm per 100°C)
3. **Flow balance asymmetry** — pressure differential pulls/pushes insert
4. **Mold temperature non-uniformity** — one-sided shrinkage
5. **Insert surface condition** — oil, oxide, roughness affects grip
6. **Process drift** — speed, temp, lot viscosity changes cause sudden shift

**Key rules:**
- Retention safety factor ≥ 2.0 (F_retention / F_flow)
- Gate position: balanced flow around insert, pressure differential ≤ 15%
- Boss OD ≥ 1.0 × insert OD
- Surface: Ra 3.2 ± 0.8 μm, no oil/oxide, ultrasonic wash
- PPS/LCP: electroless Ni plating 3–5 μm on inserts
- Thermal play: if ΔL > ½ positional tolerance → use tapered seat

**Cpk requirements:**
- Target: ≥ 1.33
- Min with containment: ≥ 1.00 + 100% inspection
- Sample size: n ≥ 30 (screening), n ≥ 50 (production qualification)
- P-side / N-side measured separately

### General Tolerances (SASG K22-03A)

| Range | Standard | Tight |
|:-----:|:---:|:---:|
| ≤ 6 mm | ± 0.1 | ± 0.05 |
| 6–30 mm | ± 0.2 | ± 0.1 |
| 30–120 mm | ± 0.3 | ± 0.15 |
| 120–400 mm | ± 0.5 | ± 0.25 |

## Supplier Qualification Checklist

### Design Phase
- [ ] Moldflow simulation — flow balance around inserts (ΔP ≤ 15%)
- [ ] Insert retention force > 2× flow force
- [ ] Thermal expansion calc < ½ positional tolerance
- [ ] Gate position optimized
- [ ] DFMEA with insert shift modes (RPN evaluated)

### Tool Qualification
- [ ] Mold temp mapping at insert zones (ΔT ≤ 5°C)
- [ ] n ≥ 50 parts, 3 runs, insert position measured
- [ ] Cpk ≥ 1.33 for insert position
- [ ] Worst-case conditions tested

### Production
- [ ] SPC on insert position
- [ ] Process parameter windows monitored
- [ ] Material lot change → 30-part confirmation
- [ ] Single controlled measurement document (per SASG N41-02A)
- [ ] Go/No-Go or 100% inspection if Cpk < 1.33

## SASG Cross-References

| Standard | Relevance |
|----------|-----------|
| K11-01A | Design Standards Manual — parent format |
| K10-01A | Design & Development Process — gate reviews |
| K10-01D | Advanced Development (PFS → FS → EP) |
| K11-03A | DFMEA — insert shift failure modes |
| K11-12A | Vital Point Notice — insert position as safety critical |
| N41-02A | Linkage Check — Vital Point → Control Plan |
| K22-03A | General Tolerances |
| K22-04A | Classification (Class A/B/C) |
| Z10-03A | Document numbering |

## Source Artifacts

These files contain the full engineering analysis behind this skill:

| Artifact | Location |
|----------|----------|
| Gap Analysis | `Dropbox/XP7G_INV_eAxle/08_Reports/2026-05-03_Plastic_Standard_Gap_Analysis.md` |
| Deep Engineering Report | `Dropbox/XP7G_INV_eAxle/08_Reports/2026-05-03_Insert_Molding_Deep_Engineering_Report.md` |
| SASG §7A Standard Section | `Dropbox/XP7G_INV_eAxle/08_Reports/2026-05-03_SASG_Section_7A_Insert_Molding_Design_Rules.md` |
| Cheat Sheet | `Dropbox/XP7G_INV_eAxle/08_Reports/2026-05-03_Plastic_Component_Design_Cheat_Sheet.md` |
| Pankaj Draft Standard | `ShadowArchive/.../design-drafts/plastic-component/DE-USXEV-Draft Plastic Molded Parts Standard.docx` |
| Pankaj Review | `ShadowArchive/.../design-drafts/plastic-component/REVIEW-DE-USXEV-Plastic-Standard.docx` |
| Watanabe Molding Tips | `ShadowArchive/80-reports/watanabe-molding-tips-bilingual-2026-04-22.md` |
| VEC Action Radar | `ShadowArchive/80-reports/vec-action-radar-2026-04-22.md` |
