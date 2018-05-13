---
title: "Developing StarCraft AI with Evolution"
layout: post
date: 2018-05-12
headerImage: false
projects: true
tag:
- c++
- evolution-strategies
- AI
- RTS
category: project
description: Using evolution strategies to play micro-battles in StarCraft
---

![starcraft-2](/assets/images/evocraft/starcraft-2.jpg)

# Introduction

Real-time strategy video games have presented many interesting problems to artificial intelligence research. This genre of game requires players not only to control their units and engage in battles against their opponents, but also to gather resources and build infrastructures in order to produce additional units and ensure victory. On top of that, all these decisions on unit control, resource gathering, and base building are made almost instantly, hence the name "real-time strategy." Finding an optimal solution in this case is especially challenging since there is a substantial amount of possibilities to be considered within the time limit. For traditional search-based algorithms, which have been shown to do well in board games like Go and chess, a large branching factor in RTS games will, on the other hand, decrease the performance of the algorithms. While the average branching factor of chess is 35 and of Go is 250, this number can easily go beyond a trillion in an RTS scenario with eighteen units, each of which can have ten actions. The order will also increase exponentially as the number of units multiply in large combat battles.

<i>StarCraft</i>, released by Blizzard in 1998 and still popular nowadays, is a classic example of a real-time strategy game as it highlights many characteristics and constraints of the RTS genre. The game models a partially observable universe, in which players do not know their opponents' troop movement or building construction. Therefore, the players have to make decisions with imperfect information. Noted that this property contrasts with perfect-information games such as chess or Go, in which the players are aware of all moves and pieces on the board. Successful players usually master two aspects: macro- and micro-management. 

Macro is the production of units, construction of buildings, and gathering of resources. 

![macro](/assets/images/evocraft/macro.jpg)

On the other hand, micro is the ability to individually control units for scouting and engaging in battles.

![micro](/assets/images/evocraft/micro.jpg)

It is extremely hard and almost impossible to optimize both micro and macro at the same time, so in a realistic scenario, players have to make trade-offs. The simplest <i>StarCraft</i> AI is based on a fixed strategy, which involve scripts that instruct units how to behave in different cases. These scripts have been effective in the commercial and AI competitions; however, the obvious disadvantage is that such strategy does not adapt to the opponents and thus tends to do extremely well in one scenario but badly in another. Therefore, efforts have been made to develop more versatile strategies.

---

# Method

There have been many innovative ideas in developing AI systems, as listed in the Reference section below. In this project, I am particularly interested in developing an AI script with versatile movement strategy. Currently, an effective yet fixed movement strategy is to simulate the kiting behavior, which makes the enemy chase your unit and thus not be able to attack as much. Instead of hard-coding the kiting behavior, I break down the unit movement into a few parameters that will be used to determine the direction in which the unit will move. The values <b>*X*</b> for these parameters are obtained from the game state, and the weights <b>*W*</b> (how much a parameter should be accounted) are what to be evolved. Eventually, the direction with the highest score, <b>*V*</b>, which is the dot product of <b>*X*</b> and <b>*W*</b>, will be chosen. This simple operation can also be replaced with a ReLU function to evolve more complicated movement. Anyway, because of the more flexible movement strategy, this script is called <b>*MvmtEvo*</b>.

The complete source code can be found on my GitHub: [https://github.com/hieusydo/SparCraft-Evo](https://github.com/hieusydo/SparCraft-Evo)

---

# Results

<b>*MvmtEvo*</b> was run with the configuration below:

<ul>
    <li>Population size: 16</li>
    <li>Mu: 8</li>
    <li>Lambda: 8</li>
    <li>Number of generations: 100</li>
    <li>Number of experiment trials: 100</li>
    <li>Army of each player: 30 Dragoons</li>
</ul>

<p style="font-size: 10pt; font-style: italic;">All benchmarks were run in the SparCraft framework, courtesy of David Churchill.</p>

The offline evolution took approximately 30 minutes. The fitness scores over 100 generations: 

![mvmtEvoScores](/assets/images/evocraft/mvmtEvoScores.png)

<b>*MvmtEvo*</b> was benchmarked against two fixed scripts: <b>*AttackClosest*</b> and <b>*Kiter*</b>. The result meets the expectation. The evolved script adapted to the fixed ones and found a winning strategy!

<style>
table {
    border-collapse: collapse;
    border-spacing: 5px;
    border: 1px solid grey;
}

th, td {
    padding: 5px;
    border: 1px solid grey;
}    
</style>

| Opponent      | Win rate | Average score|
| ------------- |:--------:|:------------:|
| AttackClosest | 100      | 506          |
| Kiter         | 91.84    | 209          |

---

# How does the evolutionary script win? 

With a very interesting method...

<b>*MvmtEvo*</b> tends to first retreat to the edge of the battlefield and group up its units, as shown in the screenshot below with <b>*MvmtEvo*</b> on the left in red and <b>*Kiter*</b> on the right in green.

![Capture1](/assets/images/evocraft/Capture1.PNG)


Using the kiting strategy, the script will chase after <b>*MvmtEvo*</b> while trying to lure its enemy into its range.

![Capture2](/assets/images/evocraft/Capture2.PNG)


However, as soon as the first unit of <b>*Kiter*</b> get into <b>*MvmtEvo*</b>'s regrouped army, it gets shot by all of <b>*MvmtEvo*</b> army. Notice that the bars on the top right side of the screen show the hitpoints of the two armies, each bar for a unit, stacked on top of each other representing the army HP. As soon as the enemy units comes into range of <b>*MvmtEvo*</b>, the green stack (<b>*Kiter*</b>'s army HP) immediately loses two HP bars since two of its units got killed.

![Capture3](/assets/images/evocraft/Capture3.PNG)

<br>

<p style="text-align: center; font-style: italic; font-size: 16pt;">Pretty cool eh?</p>

---

#### Reference

<sub>Blizzard Entertainment, Starcraft, 1998.</sub>

<sub>Mitchell A. Potter and Kenneth A. De Jong, “Cooperative Coevolution: An Architec- ture for Evolving Coadapted Subcomponents,” Evolutionary Computation, no. 1, pp. 1–29, 2001.</sub>

<sub>Chern Han Yong and Risto Miikkulainen, “Cooperative Coevolution of Multi-Agent Sys- tems,” no. AI07-338, p. 15, 2001.</sub>

<sub>Niels Justesen, Tobias Mahlmann, and Julian Togelius, “Online Evolution for Multi-Action Adversarial Games,” in Applications of Evolutionary Computation, Giovanni Squillero and Paolo Burelli, Eds., Cham: Springer International Publishing, 2016, pp. 590–603, 978-3-319-31204-0.</sub>

<sub>David Churchill and Michael Buro, “Portfolio Greedy Search and Simulation for Large-Scale Combat in StarCraft” pp. 1–8, Aug. 2013, 2325-4270. 10.1109/CIG.2013. 6633643.</sub>

<sub>Che Wang, Pan Chen, Yuanda Li, Christoffer Holmgard, and Julian Togelius, “Portfo- lio Online Evolution in StarCraft,” Proceedings, The Twelth AAAI Conference on Artificial Intelligence and Interactive Digital Entertainment, 2016.</sub>

<sub>Diego Perez, Spyridon Samothrakis, Simon M. Lucas, and Philipp Rohlfshagen, “Rolling Horizon Evolution versus Tree Search for Navigation in Single-Player Real-Time Games,” GECCO, 2013.</sub>

<sub>Michal Certicky and Dave Churchill, The Current State of StarCraft AI Competitions and Bots, 2017.</sub>

<sub>David Churchill, SparCraft: Open Source StarCraft Combat Simulation, https://code.google. com/archive/p/sparcraft/, 2013.</sub>
