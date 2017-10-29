---
layout: page
title: Projects
subtitle: Little Programming Things
comments: true
---

## Kicker Managerspiel as a Knapsack Problem

Every year the German football magazine hosts a virtual manager game. Before the start of the Bundesliga season, you can select your team from all Bundesliga squads for the with a given budget (currently 42E6 €). Then each match day you get points based on how your players performed in the actual games. For example, you get points if a player scores a goal, your goalie keeps a clean sheet or in general gets a good rating.
So overall you want to select a team which gets a maximum number of points uder a given set of constraints.
The game only alows you to buy 22 players (3 goalies, 6 defenders, 8 midfielders, 5 strikers), a maximum of 4 players per team and only 3 players of one team in your starting eleven are allowed.
The whole thing boils down to a constrained knapsack problem.
This can be nicely modeled with the open-source Java-based framework [Opt4J](http://opt4j.sourceforge.net/) utilizing Evolutionary Algorithms (EA) and Boolean Satisfiability (SAT).

$$ \alpha \cdot \beta$$
```java   
Collection<Player> playerList = problem.getPlayers();
```

```java
Set<Constraint> constraints = new HashSet<Constraint>();
```

```java
torConstraint = new Constraint(Operator.EQ, 3);   
abwConstraint = new Constraint(Operator.EQ, 6);   
mitConstraint = new Constraint(Operator.EQ, 8);   
stuConstraint = new Constraint(Operator.EQ, 5);   
```


[Link to Github](https://github.com/weichslgartner/KickerManagerspiel)
