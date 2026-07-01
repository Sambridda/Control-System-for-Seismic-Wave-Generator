# Controlled Actuation for Hydraulic Systems
### Feedforward–Feedback Trajectory Control for Proportional Valve Architectures

**Status:** 🚧 In Progress
**Organisation:** Mechatronics Engineering Pvt. Ltd. (MEPL), Kathmandu, Nepal
**Role:** Control System Architecture / Design

---

## Overview

This repository documents a PLC-supervised hydraulic control system for driving a
single-rod, asymmetric hydraulic cylinder through prescribed displacement
trajectories — specifically triangular and sinusoidal motion profiles — using a
proportional directional control valve (DCV) and a pressure control valve (PCV),
closed-loop compensated via PID at a target update rate of up to 1 kHz.

This README provides a general overview of the underlying technical proposal, the
constraints that shaped it, and the current status of the project.

The proposal itself is written as a technical document for internal review. This
repository exists alongside it to track the engineering process — the reasoning
behind the governing equations, the trade-offs considered, and the work still
ahead.

---

## Industrial Mandate & Constraints

The core mandate was to define, from first principles, a control methodology capable
of driving an asymmetric single-rod cylinder along two canonical motion profiles
(triangular and sinusoidal) with confidence that the resulting valve commands are
physically achievable and safe to deploy on real hardware. A few constraints shaped
every decision in the proposal:

- **Asymmetric actuator geometry.** Because the cap-end area exceeds the rod-end
  area on a single-rod cylinder, extension and retraction demand different flow
  rates for the same target velocity — this asymmetry is carried through every
  trajectory equation rather than assumed away.
- **Two possible pressure-control architectures.** The installed PCV could either be
  a simple fixed-setting relief valve (Configuration A) or an actively modulated
  proportional valve (Configuration B). Rather than assume one, the full
  command-signal behaviour is derived for both, since the final hardware decision
  may be made independently of this proposal.
- **Normalised, hardware-agnostic signal convention.** All valve commands are
  expressed as dimensionless values (`udcv ∈ [−1, +1]`, `upcv ∈ [0, +1]`) so the
  underlying math stays valid regardless of whether the final interface is 0–10 V or
  4–20 mA.
- **Real-time computability.** Every equation must be evaluable within a PLC control
  cycle — this became especially relevant when comparing candidate PLC hardware
  platforms, since a mathematically elegant solution is only useful if it fits
  inside the available scan time.
- **Budget-conscious hardware sourcing.** Part of the brief was to weigh
  cost-competitive PLC platforms available through regional distribution channels
  against the actual required control loop rate, rather than defaulting to the most
  capable (and expensive) option.

---

## Role and Engineering Approach

The scope of the control system architecture role on this proposal was to take the
mandate above and build a complete, defensible control methodology from the ground
up — the governing physics, the trajectory synthesis, the feedback compensation
layer, and the hardware/I-O architecture needed to implement it.

The approach, broadly:

1. **Physical foundations established first.** The system's variable set and the
   governing orifice flow relationship were defined before any trajectory shaping,
   so that every later equation is traceable back to a physical principle rather
   than an assumption.
2. **Feedforward control laws derived for both target trajectories**, under both
   possible PCV architectures, so the control software can be finalised once the
   actual installed hardware is confirmed, without re-deriving the math later.
3. **A closed-loop PID compensator layered on top of the feedforward terms** to
   correct for effects the feedforward math can't capture on its own — oil
   compressibility, fluid viscosity drift with temperature, and valve deadband.
4. **Achievable operating frequency bounded analytically**, rather than by trial
   and error, by identifying and comparing the four independent constraints that
   could limit trajectory tracking (hydraulic resonance, pump flow limit, inertial
   force limit, and valve bandwidth).
5. **Hardware recommendations grounded in the actual required loop rate**, comparing
   PLC platforms available in the Nepal market against the trajectory frequencies
   the system realistically needs to hit, instead of defaulting to the highest
   spec available.

Throughout, derivations are validated against established literature and standards
rather than intuition alone — including classical hydraulic control references
(Merritt's *Hydraulic Control Systems*), modern servo-system texts (Jelali & Kroll),
PID tuning theory (Åström & Hägglund), and relevant ISO standards for valve flow
characterisation (ISO 4411, ISO 10770-1). Anchoring each equation to a credible
source, rather than one that simply "looks right," was treated as a core part of
the work.

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
methodology described above.)*

---

## Contributions to Date

- Governing orifice flow and normalised command equations derived from first
  principles.
- Full feedforward trajectory synthesis developed for both triangular and
  sinusoidal motion profiles, under both PCV architecture assumptions.
- Cascaded feedforward + PID closed-loop structure designed, including
  anti-windup and deadband compensation strategy.
- Four constraints bounding achievable operating frequency analytically derived
  and compared.
- Candidate PLC hardware platforms benchmarked against realistic loop-rate
  requirements, rather than the nominal 1 kHz specification, to support a more
  cost-effective hardware decision.
- Technical proposal document authored and currently under internal review.

---

## Future Plan

This is a project in progress. With the theoretical groundwork now written up for
review, the next phases of work are:

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

This README will be updated as the project moves from proposal into
implementation.

---

*This README is a living document and will be updated as the project progresses
through integration, testing, and commissioning.*
