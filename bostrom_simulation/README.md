<!-- хаки рендеринга latex https://gist.github.com/a-rodin/fef3f543412d6e1ec5b6cf55bf197d7b#gistcomment-3523272 -->
# Bostrom network simulation

## Usage

0. Install Python3 if you have no
1. Go to `bostrom_simulation` folder
```bash
cd bostrom_simulation
```
2. Install requirements via pip3
```bash
pip3 install -r requirements.txt
```
3. Run
```bash
jupyter notebook
```
4. The notebook server should be running at `http://127.0.0.1:8888` 

5. Open [`simulation.ipynb`](simulation.ipynb)

6. Fill `Initial state` and `Params for simulating` sections

7. On the top bar `Kernel`->`Restart & Run All`

8. The simulation time depends on the simulation period you have set, f.e. for 7 years it approximately 1 hour

9. Look at the results and conclude.

## Goals

To optimize parameters for launching Bostrom.

An idea is to model the value of BOOT through the understanding of established network effects in Ethereum.
Then we can forecast claim dynamics and address growth based on approximated network effects. Assuming some demand for cyberlinks based on address growth we can adjust the supply of cyberlinks so that V price could grow. The given model also allows defining inflation parameters of BOOT to optimize investments into the hardware infrastructure.

## BOOT supply 

Simulate the ability of heroes to invest in infrastructure depending on different market conditions.  

The formula used in the excel model:

<img src="https://render.githubusercontent.com/render/math?math=\color{green}boot\_supply = start\_boot\_supply \cdot ( 1 %2B \frac{ 0.12}{1 %2B \frac{days}{365 \cdot 2}})^{\frac{days}{365}}">

### Simulation parameters

- *start_boot_supply* = `1e15`
- <img src="https://render.githubusercontent.com/render/math?math=\color{green}boot\_supply = start\_boot\_supply \cdot ( 1 %2B \frac{ 0.12}{1 %2B \frac{days}{365 \cdot 2}})^{\frac{days}{365}}">
- *boot_inflation_rate_change*
- *boot_inflation_max*
- *boot_inflation_min*
- *boot_bonded_target*  

## Activated BOOT Supply

- liquid supply (25%)
- claimed supply

## Gift claim dynamics
 
The addresses for gift are defined in the [research](https://github.com/Snedashkovsky/cybergift). This research [concludes](https://github.com/Snedashkovsky/cybergift#prize-to-be-the-first) 6M addresses for distribution of 70% of BOOT tokens. Further we need to model how this gifts can be claimed. 

We need to derive *claim_rate* (by formula to define).

Assumptions:
- 100,000 agents will claim our gift. After that the gift will be activated (*start_agents_count* = 100_000)
- it will take 100 days since genesis before the gift claiming process will be completed (*days_for_gift_activation* = 100)  

  *days_for_gift_activation* is needed to refine days for gift activation to reach *start_agents_count*.

After the modeling of claim dynamics, we can set baselines for adoption ("understanding network effects" chapter).

### Simulation parameters

- *start_agents_count* `(10'000, 50'000, 100'000)`
- *days_for_gift_activation* `(100, 150)`
- *days_for_gift_full_claim* `(0, 360)` 
- *claimed_at_activation_share* (`1, 0.5`)

## Understanding network effects

Modeling price of BOOT as a function of usage weighted on network effects.  

To model agents' growth dynamics of the Bostrom network, we did a regression analysis on ETH active agent dynamics
([excel spreadsheet online](https://needfordata-my.sharepoint.com/:x:/g/personal/max_needfordata_ru/EZWCgmE-VOBEsGJTg8lslpYBP2LQoBFdMC9LgXleJ3Dj_Q?e=PwmaIh)).
We calculated [ethereum active agents](eth_active_agents.ipynb) as addresses with a balance more than 0.01 ETH (the
balance sufficient to complete at least 1 transaction).

![](images/EthAgentsDynamics.png)

We have combined 2 trendlines and derived the following formula of agents count by days from ETH dynamics.

<img src="https://render.githubusercontent.com/render/math?math=\color{green}agents\_count = 2 \cdot days^{2} %2B 100 \cdot days %2B 8700">

Assuming that there are much more crypto-agents now than it was on  time of ETH launch we adjusted formula with
coeffients to expect more rapid growth.

<img src="https://render.githubusercontent.com/render/math?math=\color{green}agents\_count = 9 \cdot days^2 %2B 100 \cdot days %2B start\_agents\_count">

We decided to model total capitalization through *capitalization_per_user* metric derived from ETH capitalization in
BTC (from 100 day from start till 2160 days of network, as before 100 days ETH price in BTC had a lot of fluctuations).

![ETH dynamics](images/EthCapPerAgentActiveInBTC1.png)

We derived such formula:

<img src="https://render.githubusercontent.com/render/math?math=\color{green}capitalization\_per\_user = 60\,000 \cdot agents\_count^{-0,7}">


We assumed that dynamics of capitalization of BOOT token in ETH will resemble ETH token dynamics in BTC prices.
We adjusted the formula that our first day *capitalization_per_user* will be equal to 1 ETH
(*start_capitalization_per_user* = 1).

<img src="https://render.githubusercontent.com/render/math?math=\color{green}capitalization\_per\_user = start\_capitalization\_per\_user \cdot start\_agents\_count^{0.7} \cdot  agents\_count^{-0,7}">

### Simulation parameters

- <img src="https://render.githubusercontent.com/render/math?math=\color{green}agents\_count = 9 \cdot days^2 %2B 100 \cdot days %2B start\_agents\_count">
- <img src="https://render.githubusercontent.com/render/math?math=\color{green}start\_capitalization\_per\_user = 1">
- <img src="https://render.githubusercontent.com/render/math?math=\color{green}capitalization\_per\_user = start\_capitalization\_per\_user \cdot start\_agents\_count^{0.7} \cdot  agents\_count^{-0.7}">

## Modeling H supply

Agents will delegate 70% of BOOT Supply to heroes, and H will be minted in the corresponding amount (*boot_bonded* = 0.7).

<img src="https://render.githubusercontent.com/render/math?math=\color{green}h\_supply = boot\_supply \cdot boot\_bonded">

### Simulation parameters

- *boot_bonded* = `0.7`
- <img src="https://render.githubusercontent.com/render/math?math=\color{green}h\_supply = boot\_supply \cdot boot\_bonded">

## Predicting V demand

Simulate cyberlinks usage.  

We have derived base estimate of cyberlink per agent formula from ETH data:

![ETH Data](images/EthAgentsCountActive_vs_EthTransPerAgent.png)

We derived such formula:

<img src="https://render.githubusercontent.com/render/math?math=\color{green}transactions\_per\_agent = 9 \cdot agents\_count^{-0.3}">

And we adjust such formula with the number of extra links and guaranteed links. 

<img src="https://render.githubusercontent.com/render/math?math=\color{green}cyberlinks\_per\_agent = 9 \cdot agents\_count^{-0.3} %2B extra\_links %2B guaranteed\_links">

*Extra_links* count depends on UX specifics, such as name setting, following (proportion of agents) and extra:

<img src="https://render.githubusercontent.com/render/math?math=\color{green}extra\_links ~ f( agents\_count, name, following, extra)"> 

Also, the founding team envisions some basic stuff to cyberlink such as naming systems and tokens. So we can rely
on this demand also adding *guaranteed_links* count.

### Simulation parameters 

- *extra_links* - function to define
- *guaranteed_links* - function to define
- <img src="https://render.githubusercontent.com/render/math?math=\color{green}cyberlinks\_per\_agent = 9 \cdot agents\_count^{-0.3} %2B extra\_links %2B guaranteed\_links">

## Adjusting V and A supply

To model minting properties of V for the planning of GPU storage and maximization of price. As A reource do not have natural measure we decide to model A supply rules equal to V. 

Gboot * 1 day = 1 volt

*investmint_period* - is period of investminiting H token for selected agent. It is choosen by agent according to his understanding and priorities of maximisation his benefits. 


And it is limited by system setting of *investmint_max_period*, that has some dynamic formula to define and research (presumably it will become longer with the age of network - [3, 6, 9, 12 ... ] monthes but no longer than the time from network start). 
*investmint_amount* - is amount of H token investminted by agents for selected period of time.  
*halving_time* - time period to increase investmint amount twofold.
*a_v_proportion* - ?

### Simulation parameteres

Parameters to define for V and A:
- *base_investmint_period*
- *base_investmint_amount*
- *start_investmint_max_period* 90
- *horizont_step* 
- *halving_time*
- *a_v_proportion* = `0.5`

## Subtotal summary of simulation parameters

- *start_boot_supply* = `1e15`
- <img src="https://render.githubusercontent.com/render/math?math=\color{green}boot\_supply = start\_boot\_supply \cdot ( 1 %2B \frac{ 0.12}{1 %2B \frac{days}{365 \cdot 2}})^{\frac{days}{365}}">
- *boot_inflation_rate_change*
- *boot_inflation_max*
- *boot_inflation_min*
- *boot_bonded_target*  
- *start_agents_count* `(10'000, 50'000, 100'000)`
- *days_for_gift_activation* `(30, 100)`
- <img src="https://render.githubusercontent.com/render/math?math=\color{green}agents\_count = 9 \cdot days^2 %2B 100 \cdot days %2B start\_agents\_count">
- <img src="https://render.githubusercontent.com/render/math?math=\color{green}start\_capitalization\_per\_user = 1">
- <img src="https://render.githubusercontent.com/render/math?math=\color{green}capitalization\_per\_user = start\_capitalization\_per\_user \cdot start\_agents\_count^{0.7} \cdot  agents\_count^{-0,7}">
- *boot_bonded* = `0.7`
- <img src="https://render.githubusercontent.com/render/math?math=\color{green}h\_supply = boot\_supply \cdot boot\_bonded">
- *extra_links* - function to define
- *guaranteed_links* - function to define
- <img src="https://render.githubusercontent.com/render/math?math=\color{green}cyberlinks\_per\_agent = 9 \cdot agents\_count^{-0.3} %2B extra\_links %2B guaranteed\_links">
- *investmint_period*
- *investmint_amount*
- *halving_time*
- *investmint_max_period*
- *a_v_proportion*

## Investments into infrastructure

BOOT Supply
BOOT Staked
Comission = 10%
BOOT price
Validator count = 92

Target - revenue of 1 validator

## Further work

Model could work as prediction governance tool for working network based on acutal measurements.


## Legend

There are three network tokens:

* F - Unclaimed network token
* T - Liquid network token
* L - Frozen network token


and two resource tokens:

* A - Amper token
* V - Volt token

The initial state of all tokens should be defined below

* 1 **timestep** == 1 time unit

## Claim function

The function of claim frozen tokens is:


<img src="https://render.githubusercontent.com/render/math?math=\color{green}f(x) = 7 \cdot 10^{14} - \frac{5.6 \cdot 10^{14}}{3}x">

<img src='images/claim.png' />

where **x** is year. This function means 80% of frozen tokens are going to be claimed in 3 years linearly by ~187
TeraTokens per year

## Vesting and Unvesting

The vesting function is defined as the amount of locking tokens in the time unit assumed by all liquid tokens must be
locked in the lock timeframe.

The vesting function is defined as the amount of unlocking tokens in the time unit assumed by all locked tokens must
be unlocked in the unlock timeframe.

## Resource tokens

Amperes and Volts mints by the following formula:


<img src="https://render.githubusercontent.com/render/math?math=\color{green}f(x) = \frac{x \cdot \frac{maxLockTime}{cycle}}{initPrice} \cdot MR^r">

where: 
* **x** is number of vested tokens
* **lockTime** is the maximum time for locking tokens
* **cycle** is amount of blocks for rank and entropy calculation
* **initPrice** is the initial price of the minting
* **MR_r** is mint rate [100, 1]. It's different for all energy tokens



## Mint Rate (Amperes)

Mint rate is multiple coefficient for minting Amper tokens

It is halving every year

<img src="https://render.githubusercontent.com/render/math?math=\color{green}MR_a(x) = \frac{initMR^a}{2^{\lfloor{x}\rfloor}}">

where: 
* **x** is year
* **initMRa** is the initial value of MRa

f.e.

<img src='images/mr_a.png' />


## Mint Rate (Voltes)

Mint rate is multiple coefficient for minting Volt tokens

It is halving every amount of supply defined

<img src="https://render.githubusercontent.com/render/math?math=\color{green}MR_v(x) = \frac{initMR^v}{2^{\lfloor{x}\rfloor}}">

where: 
* **x** is defined supply
* **initMRv** is the initial value of MRv


## Inflation

The inflation function depends on the ratio between vested tokens and tokens supply. It the same as in cosmos-based
networks.

## Supply

Supply is the sum of liquid, vested and frozen tokens in each block.


## Utils

The function of using volts for cyberlinking. By the simulation the probability in a timestamp [0.01, 0.02, 0.03, 0.05, 0.08, 0.13, 0.21, 0.34] .


## Assumptions

1. All agents lock tokens for the maximum available period defined in params for simulating
2. All agents mint maximum **A** and **V** tokens in 50/50 ratio

## Mathematical Specification

### Differential Equations

* T - liquid network token
* L - locked(vested) network token
* F - not claimed(frozen) network token
* IRC_u - inflation rate change per time unit
* S - total network tokens supply
* I - inflation per time unit in network tokens
* Provision - per time unit token provision
* A - Amper token
* V - Volt token
* CL - cyberlinks
* Agents_count - the amount of the active agents
* 

<img src="https://render.githubusercontent.com/render/math?math=\color{green}T_u = T_{u-1} %2B {\Delta T} \tag{1}">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}L_u = V_{u-1} %2B {\Delta V} \tag{2}">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}F_u = F_{u-1} %2B {\Delta F} \tag{3}">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}A_u = A_{u-1} %2B {\Delta A} \tag{4}">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}V_u = V_{u-1} %2B {\Delta V} \tag{5}">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}MR^{a}_{u} = {\Delta MR^a} \tag{6}">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}MR^{v}_{u} = {\Delta MR^v} \tag{7}">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}CL_{u} = CL_{u-1} %2B {\Delta CL} \tag{8}">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}Agents = Agentes_{u-1} %2B {\Delta Agents} \tag{1}">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}S_u = T_u %2B L_u %2B F_u \tag{9}">  

where the rate of change (<img src="https://render.githubusercontent.com/render/math?math=\color{green}\Delta">) is:  

<img src="https://render.githubusercontent.com/render/math?math=\color{green}{\Delta F} = - \frac{5.6 \cdot 10^{14}}{3 \cdot unitsPerYear} \tag{10}">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}{\Delta L} = \frac{T_{u-1}}{\frac{unitsPerYear}{12} \cdot vestingSpeed} - {\Delta U} \tag{11} ">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}{\Delta T} = - {\Delta F} - {\Delta L} %2B I_{u-1} %2B {\Delta U}  \tag{12} ">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}{\Delta A} = \lfloor{\lfloor{\frac{\frac{1}{2} \cdot \Delta L}{initPrice} \cdot \frac{vestringTime_{max}}{baseVestingTime}}\rfloor \cdot MR^a}\rfloor  \tag{13} ">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}{\Delta V} = \lfloor{\lfloor{\frac{\frac{1}{2} \cdot \Delta L}{initPrice} \cdot \frac{vestringTime_{max}}{baseVestingTime}}\rfloor \cdot MR^v}\rfloor  \tag{14} ">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}{\Delta U} = \frac{V_{u-1}}{\frac{unitsPerYear}{12} \cdot unvestingSpeed} \tag{15} ">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}{\Delta MR^a} = \frac{MR^a_{init}}{2^{\lfloor{\frac{u}{unitsPerYear}}\rfloor}} \tag{16} ">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}{\Delta MR^v} = \frac{MR^v_{init}}{2^{\lfloor{\frac{V}{voltHalving}}\rfloor}} \tag{17} ">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}{\Delta Agents} = 18 \cdot u %2B 100 \tag{18} ">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}{\Delta CL} = \frac{6.3 \cdot agents}{agents^{0.3}}  \tag{18} ">  

where:

<img src="https://render.githubusercontent.com/render/math?math=\color{green}I_{u-1} = \frac{S_{u-1} \cdot IRC_{u-1}}{unitsPerYear} \tag{19} ">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}IRC_u = \frac{\left(1 - \frac{vestedRatio_{u-1}}{goalVested}\right) \cdot inflationRateChange}{unitsPerYear} \tag{20} ">  
<img src="https://render.githubusercontent.com/render/math?math=\color{green}{vestingTime_{max} = initVestingTime_{max} \cdot 2^{\lfloor{\frac{u}{unitsPerYear}}\rfloor}} \tag{21} ">  