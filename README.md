Vibe coded project to analyze and predict The 2026 World Cup in real time using the Monte Carlo method based on FIFA rankings, tournament form, history, economics, demographics, and luck. Live data from: https://raw.githubusercontent.com/openfootball/worldcup.json/master/2026/worldcup.json  

Website: https://worldcup.satnar.my.id/  

The algorithm blends six measurable factors into a single Strength Score for every nation, then runs a Monte Carlo simulation of the rest of the tournament — including unplayed group matches and the entire knockout bracket — thousands of times to produce a championship probability distribution.

Each simulation models match scorelines using a Poisson distribution, where each team's expected goals (λ) is derived from the Strength Score gap between the two sides. The luck factor is re-rolled on every simulation to represent football's natural variance.

## Factor Inputs
- FIFA Ranking
100/(1+(rank-1)/14) ± 8·(pts_norm-0.5)
40.0%
- Tournament Form
(PPG/3)×60 + clamp(15 + GD×2, 0, 40)
27.0%
- History
10 + Σ 28×0.5^(age/20) per title
8.0%
- Economy
ln(GDP per capita) / ln(100,000) × 100
2.5%
- Demographics
ln(population) / ln(350) × 100
2.5%
- Luck
N(μ=50, σ=22), re-rolled per simulation
20.0%

## Strength Score Formula
```
  Strength(team) =
  0.40 × RankFactor   (hybrid: ordinal rank + actual FIFA points nudge)
  0.27 × FormFactor   (tournament form, widened saturation window)
  0.08 × HistFactor   (titles, time-decayed: half-life 20 years)
  0.025× EconFactor   (GDP per capita, log-scaled, minor tie-breaker only)
  0.025× DemoFactor   (population, log-scaled, minor tie-breaker only)
  0.20 × LuckFactor   (re-rolled per simulation)

diff = (S_a - S_b) / 22
host edge: diff += 0.18 only if exactly one side is a 2026 co-host (USA/MEX/CAN)
λ_a = max(0.45, 1.35 + diff)
λ_b = max(0.45, 1.35 - diff)
Goals ~ Poisson(λ)
```
Poisson goal model — λ derived from the Strength Score gap between both teams

Latest revision: Economy and Demographics weights were cut sharply (from 20% combined to 5%) so wealthy-but-weak footballing nations no longer get an unfair boost. The FIFA Ranking factor is now more responsive (accounting for actual points, not just position), the History factor now decays over time (a 2022 title counts more than a 1966 one), and a host-nation edge only applies when one of the two teams is actually a tournament host (USA/Mexico/Canada).

Note: the demographic factor models talent-pool potential from population scale, not a direct performance prediction.
