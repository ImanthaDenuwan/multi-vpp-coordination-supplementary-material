# Coordinated Multi-VPP Dispatch — Parameter Settings and Input Data

Supplementary material for:

> I. D. Meegasthanne *et al.*, “Coordinated Optimization of Multiple Virtual Power
> Plants for Grid Performance Enhancement in DER-Rich Systems,” manuscript prepared
> for submission to *IEEE Transactions on Sustainable Energy*, 2026.

This repository holds the numerical parameter settings, the customer-partition
definitions and the input-data provenance for the case study reported in the
paper. It is referenced from Section V (Test System and Case Design).

---

## 1. Second-layer optimisation parameters

Both VPPs use identical DER specifications and cost coefficients. This is a
modelling choice made to isolate the effect of portfolio composition from
differences in aggregator assumptions — not a limitation of the framework.

### 1.1 System configuration

| Parameter | Symbol | Value |
|---|---|---|
| Number of VPP customers | \|A\| | 70 |
| Number of VPPs | \|J\| | 2 |
| Scheduling horizon | T | 24 h |
| Time-step duration | Δt | 1.0 h |

### 1.2 Solar PV

| Parameter | Symbol | Value |
|---|---|---|
| PV inverter nameplate | P<sup>pv,max</sup><sub>c</sub> | 7.0 kW |

### 1.3 Battery energy storage

| Parameter | Symbol | Value |
|---|---|---|
| Nameplate capacity | E<sup>nameplate</sup><sub>c</sub> | 20.0 kWh |
| Usable energy upper bound (90%) | E<sup>max</sup><sub>c</sub> | 18.0 kWh |
| Usable energy lower bound (20%) | E<sup>min</sup><sub>c</sub> | 4.0 kWh |
| Initial state of charge (20%) | E<sup>init</sup><sub>c</sub> | 4.0 kWh |
| Maximum charging power | P<sup>ch,max</sup><sub>c</sub> | 5.0 kW |
| Maximum discharging power | P<sup>dis,max</sup><sub>c</sub> | 5.0 kW |
| Charging / discharging efficiency | η<sub>ch</sub>, η<sub>ds</sub> | 0.95 |
| Terminal SOC constraint | — | E<sub>c,T+1</sub> = E<sub>c,1</sub> |

### 1.4 Prices and cost coefficients

| Parameter | Symbol | Value |
|---|---|---|
| Import / export price (flat) | c<sup>imp</sup><sub>t</sub>, c<sup>exp</sup><sub>t</sub> | $0.12/kWh |
| PV curtailment penalty | c<sup>pv</sup><sub>curt</sub> | $0.20/kWh |
| Battery degradation cost | c<sup>ES</sup><sub>deg</sub> | $0.03/kWh |
| Demand response | P<sup>DR</sup><sub>c,t</sub> | disabled |

The curtailment penalty is set above the energy price to discourage curtailment
of residential PV, consistent with the industry partner's preference. The
wholesale market energy constraint is not enforced in this configuration,
because the VPPs are dispatched for network service rather than against a
cleared position; the export that occurs is residual solar surplus, not a
contracted quantity.

### 1.5 Peak shaving and grid support

| Parameter | Symbol | Value |
|---|---|---|
| Maximum customer peak duration | N<sup>max</sup><sub>p</sub> | 5 h |
| Number of grid peak hours | \|G\| | 2 |
| Charging source | — | solar surplus only |
| Off-peak discharge | — | prohibited |

Removing price-driven arbitrage from the problem is deliberate: it makes battery
availability depend on the control policy and the solar resource alone, so the
seasonal sensitivity reported in the paper is attributable to physical resource
availability rather than to tariff design.

---

## 2. Customer partitions

VPP membership is assigned by a seeded random permutation rather than by
electrical location, so that customers on the same LV lateral routinely belong
to different VPPs. For seed *s* and target size *n₁*:

```matlab
rng(s, 'twister');
pi = randperm(70);
V1 = pi(1:n1);
V2 = pi(n1+1:70);
```

The fixed seed makes each partition reproducible.

| Partition | Seed | \|V₁\| | \|V₂\| |
|---|---|---|---|
| Split 1 | 1 | 37 | 33 |
| Split 2 | 2 | 34 | 36 |
| Split 3 | 5 | 20 | 50 |

Splits 1 and 2 give the aggregators comparable portfolio sizes; Split 3 is
deliberately asymmetric.

---

## 3. Input data sources

| Quantity | Source | Treatment |
|---|---|---|
| Hourly residential load, 70 customers | OEDI Commercial and Residential Hourly Load Profiles | One representative calendar day per scenario, same day for all customers, individual load shapes preserved |
| Feeder demand multipliers m<sub>t</sub> | AESO Alberta Internal Load, 2024 | Applied to all MV spot loads and LV base loads |
| Hourly PV availability | NASA POWER irradiance | Capped at the 7.0 kW inverter nameplate |

Five representative operating days are evaluated: extreme summer, extreme
winter, typical summer, typical winter and shoulder season.

### 3.1 Aggregate scenario conditions

| Quantity | Extreme summer | Extreme winter | Typical summer | Typical winter | Shoulder |
|---|---:|---:|---:|---:|---:|
| Peak demand (kW) | 700.2 | 362.0 | 275.8 | 259.4 | 208.5 |
| Peak hour | 17 | 19 | 21 | 20 | 21 |
| Daily demand (kWh) | 8955 | 5751 | 4439 | 3923 | 3042 |
| Customer peak, minimum (kW) | 3.14 | 3.68 | 2.51 | 3.00 | 2.09 |
| Customer peak, maximum (kW) | 14.88 | 22.90 | 8.62 | 5.67 | 3.69 |
| PV peak (kW) | 462.9 | 121.0 | 418.5 | 219.7 | 321.8 |
| Daily PV energy (kWh) | 4524 | 431 | 3587 | 1017 | 2182 |
| Customer PV peak, maximum (kW) | 6.98 | 1.93 | 6.38 | 3.46 | 4.96 |
| PV-to-demand ratio | 0.51 | 0.07 | 0.81 | 0.26 | 0.72 |

---

## 4. Test feeder

The IEEE 123-node test feeder is extended with seventeen appended low-voltage laterals to give a 226-node model. Each LV lateral carries VPP-enrolled
customers and preserves the phase unbalance of the underlying feeder. Every
enrolled customer has a 7.0 kW rooftop PV system and a 20 kWh BESS rated at
5 kW charge and discharge.

---

## 5. Citation

```bibtex
@misc{meegasthanne2026supplementary,
  author       = {Meegasthanne, Imantha Denuwan and Wang, Xiaoting and
                  Khan, Ozair and Wang, Alice and Kish, Gregory J.},
  title        = {Coordinated Multi-{VPP} Dispatch: Parameter Settings and Input Data},
  year         = {2026},
  howpublished = {\url{https://github.com/ImanthaDenuwan/multi-vpp-coordination-supplementary-material}}
}
```
