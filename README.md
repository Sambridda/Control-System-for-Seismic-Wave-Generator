# Controlled Actuation for Hydraulic Systems
### Feedforward–Feedback Trajectory Control for Proportional Valve Architectures

**Status:** 🚧 In Progress
**Organisation:** Mechatronics Engineering Pvt. Ltd. (MEPL), Kathmandu, Nepal
**My Role:** Control System Architect / Designer

---

## Overview

This repository documents my ongoing work on a PLC-supervised hydraulic control
system for driving a single-rod, asymmetric hydraulic cylinder through prescribed
displacement trajectories — specifically triangular and sinusoidal motion profiles —
using a proportional directional control valve (DCV) and a pressure control valve
(PCV), closed-loop compensated via PID at a target update rate of up to 1 kHz.

I was recommended for this role by our senior engineer, and I've approached it as an
opportunity to grow into the position of control system architect on a real
industrial actuation problem. This README gives a general overview of the technical
proposal I've been developing, the constraints that shaped it, and where the project
stands today.

The proposal is written as a technical document for internal review, and this
repository/README exists to track my engineering process alongside it — the
reasoning behind the equations, the trade-offs considered, and the work still ahead.

---

## Industrial Mandate & Constraints

The core mandate was to define, from first principles, a control methodology capable
of driving an asymmetric single-rod cylinder along two canonical motion profiles
(triangular and sinusoidal) with confidence that the resulting valve commands are
physically achievable and safe to deploy on real hardware. A few constraints shaped
every decision in the proposal:

- **Asymmetric actuator geometry.** Because the cap-end area exceeds the rod-end
  area on a single-rod cylinder, extension and retraction demand different flow
  rates for the same target velocity — this asymmetry had to be carried through
  every trajectory equation rather than assumed away.
- **Two possible pressure-control architectures.** The installed PCV could either be
  a simple fixed-setting relief valve (Configuration A) or an actively modulated
  proportional valve (Configuration B). Rather than assume one, I derived the full
  command-signal behaviour for both, since the final hardware decision may not be
  mine to make.
- **Normalised, hardware-agnostic signal convention.** All valve commands are
  expressed as dimensionless values (`udcv ∈ [−1, +1]`, `upcv ∈ [0, +1]`) so the
  underlying math stays valid regardless of whether the final interface is 0–10 V or
  4–20 mA.
- **Real-time computability.** Every equation had to be evaluable within a PLC
  control cycle — this became especially important when comparing candidate PLC
  hardware platforms, since a mathematically elegant solution is only useful if it
  fits inside the available scan time.
- **Budget-conscious hardware sourcing.** Part of the brief was to weigh
  cost-competitive PLC platforms available through regional distribution channels
  against the actual required control loop rate, rather than defaulting to the most
  capable (and expensive) option.

---

## My Role and Engineering Approach

As control system architect on this proposal, my responsibility was to take the
mandate above and build a complete, defensible control methodology from the ground
up — the governing physics, the trajectory synthesis, the feedback compensation
layer, and the hardware/I-O architecture needed to implement it.

My approach, broadly:

1. **Established the physical foundations first.** I built the system's variable
   set and derived the governing orifice flow relationship before touching
   trajectory shaping, so that every later equation is traceable back to a physical
   principle rather than an assumption.
2. **Derived feedforward control laws for both target trajectories**, under both
   possible PCV architectures, so the control software can be finalised once the
   actual installed hardware is confirmed, without re-deriving the math later.
3. **Layered a closed-loop PID compensator on top of the feedforward terms** to
   correct for effects the feedforward math can't capture on its own — oil
   compressibility, fluid viscosity drift with temperature, and valve deadband.
4. **Bounded the achievable operating frequency analytically**, rather than by
   trial and error, by identifying and comparing the four independent constraints
   that could limit trajectory tracking (hydraulic resonance, pump flow limit,
   inertial force limit, and valve bandwidth).
5. **Grounded hardware recommendations in the actual required loop rate**, comparing
   PLC platforms available in the Nepal market against the trajectory frequencies
   the system realistically needs to hit, instead of defaulting to the highest
   spec available.

Throughout, I leaned on established literature and standards to validate my
derivations rather than working from intuition alone — including classical hydraulic
control references (Merritt's *Hydraulic Control Systems*), modern servo-system
texts (Jelali & Kroll), PID tuning theory (Åström & Hägglund), and relevant ISO
standards for valve flow characterisation (ISO 4411, ISO 10770-1). I see this as an
important part of the job: proposing equations I can point back to a credible source
for, not just ones that "look right."

---

## What the Proposal Covers

For anyone reviewing the full technical document, here's a general map of what's
inside:

| Section | What it establishes |
|---|---|
| System variables & physical foundations | The orifice flow equation and how valve commands are derived from it |
| Pressure control architectures | Two configurations — fixed relief (A) vs. actively modulated (B) — and their respective trade-offs |
| Triangular trajectory synthesis | DCV/PCV command derivation for constant-velocity motion with abrupt reversals |
| Sinusoidal trajectory synthesis | DCV/PCV command derivation for continuously varying velocity/acceleration |
| Closed-loop feedback compensation | Cascaded feedforward + PID structure, and a staged tuning procedure |
| Operational frequency range | Four independent constraints on achievable trajectory frequency, and which dominate under which conditions |
| PLC implementation considerations | Control loop rate, anti-windup, deadband compensation, commissioning sequence, and a hardware comparison |
| I/O architecture | Minimum analog/digital I/O needed and the signal mapping to physical outputs |

*(Note: this repository intentionally does not reference specific instrument models
or vendor specifications from any separate equipment procurement documentation —
those are project-specific and subject to change independently of the control
methodology above.)*

---

## Contributions So Far

- Derived the governing orifice flow and normalised command equations from first
  principles.
- Developed full feedforward trajectory synthesis for both triangular and
  sinusoidal motion profiles, under both PCV architecture assumptions.
- Designed the cascaded feedforward + PID closed-loop structure, including
  anti-windup and deadband compensation strategy.
- Analytically derived and compared the four constraints bounding achievable
  operating frequency.
- Benchmarked candidate PLC hardware platforms against realistic loop-rate
  requirements, rather than the nominal 1 kHz specification, to support a more
  cost-effective hardware decision.
- Authored the technical proposal document currently under internal review.

---

## Future Plan

This is very much a project in progress. With the theoretical groundwork now
written up for review, my next steps are:

- [ ] **System Integration** — validating the derived control equations against
      the actual cylinder, valve, and PLC hardware once procurement is finalised.
- [ ] **Load Testing** — running the feedforward-only trajectory first (as laid out
      in the commissioning sequence) to characterise real position lag before any
      feedback correction is applied.
- [ ] **PID Tuning** — following the staged tuning procedure (Kp → Kd → Ki) with
      anti-windup verification at trajectory turning points before continuous
      operation.
- [ ] **Electrical Connections & Schematics** — finalising the I/O wiring between
      the PLC, DCV, PCV, and position sensors based on the confirmed hardware
      platform.
- [ ] **HMI Design** — building an operator interface for trajectory selection,
      live position/pressure monitoring, and safety interlocks (start/e-stop).

I'll keep this README updated as the project moves from proposal into
implementation.

---

## A Note of Thanks

I'm grateful to our senior engineer for recommending me for this project, and I've
tried to treat that trust seriously by grounding every equation in this proposal in
verifiable physics and established literature rather than shortcuts. I still have a
lot to learn on the implementation side, and I'm looking forward to that next phase.

---

*This README is a living document and will be updated as the project progresses
through integration, testing, and commissioning.*
