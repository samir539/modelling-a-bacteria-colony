# Bacteria population on coated and non-coated strips (2D)
//A collaborative project with Fengyi Li
<div style="text-align: justify"> In this simulation, the bacteria are allocated into different colonies with a synergistic or competitive relationship in a fixed environment made of two types of materials. Bacteria can move, die, duplicate, eat and fight, and these actions depend on characterizations of the bacteria. The aim of this simulation is to find the best combination of two strips with their widths and numbers used. </div> 

## Environment

The environment of the simulation is made of two types of strips aligned one by one. 
1. **Coated strips** 
* Constrain the movement of bacteria only in the current coated region
* Have **d** as the width of the strip

2. **non-coated strips** 
* Bacteria can move to coated strips or stay in the current region. 
* Have **d2** as the width of the strip

The shape of the environment is a square in this simulation, but it can be flexibly changed by tuning the parameters.

  

## Colony

A colony is a group where bacteria live with the same relationship with others. The region of the colony is defined by the [Convex hull](https://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.ConvexHull.html) based on the bacteria at the outermost positions. There are two kinds of colonies:

1. **Synergistic**: Colonies merge into one integrity if their 
convex hulls overlap.

2. **Competitive**: Bacteria in the colonies fight following a way until the convex hulls do not overlap.

At the beginning of the simulation, all colonies can have a regular shape such as circle as the initial shape, and then the bacteria can be randomly generated in the colonies.

## Bacteria

Several properties are associated with the bacteria:

1. **Position**: A pair of coordinates that describes the position of the bacteria in the environment.

2. **Neighbours**: The number of neighrbours of a bacteria is equal to the number of [Voronoi cell](https://docs.scipy.org/doc/scipy-0.18.1/reference/generated/scipy.spatial.Voronoi.html) generated by the bacteria in the same colony as it.

3. **Death_rate**: It is calculated by the equation where *DR(n)* is the death rate for a bacteria with *n* number of neighbours and *DR(0)* is the death rate when there are no neighbours.

<p align="center">
  <img width="130" height="40" src="https://github.com/FengyiLi1102/bacteria_simulation/blob/master/Eq_death_rate.gif">
</p>

4. **Duplicate_rate**: The rate of duplication for the bacteria and the new duplicated bacteria will be allocated at a random position close to the original one in a circle region.

5. **Move_rate**: The rate of movement for the bacteria and the bacteria will move with a pair of x and y directions based on the normal distribution.

6. **Strength**: The strength of the bacteria is defined in the following equation where *A* is the number of unit time for a bacteria to be alive, *C* is the number of colonies and *S* is the number of unit time after the bacteria took the food. The behaviour of fight between two bacteria will generate a surviour based on the strengths for each bacteria.

<p align="center">
  <img width="220" height="45" src="https://github.com/FengyiLi1102/bacteria_simulation/blob/master/Eq_strength.gif">
</p>


## Problems
While developing a solution to this challenge a few key underlying facts were concluded and outlined below:

- **Simulation in a synergistic mode**

It was noted regardless of the combination of strips chosen the number of bacteria never reached half of its starting value and as a result no value for the half life could be obtained. This indicates that in a synergistic mode colony growth and bacteria reproduction is favoured regardless of the strips and their associated parameters. The growth of the total bacteria population follows an exponential pattern and after 30 or so steps the time required to complete a step became unreasonable to work with.


- **Simulation in a competitive mode**

In the competitive mode of this simulation the total population of bacteria dropped to half their initial value very rapidly (in many cases within the first step). In most situations, the colonies initially set up will overlap with each other with the number of three or even four. This causes a rapid drop of bacteria due to random selection of bacteria from each colony and almost same strength at the first step. Food was rapidly eaten, and the populations of the bacteria would completely die out very rapidly. This meant it was very difficult to interpolate half life data and find a minimum or to find the standard deviations of the half life to within 0.2(T_1/2).


- **Note on convex hull**

In the competitive mode, as a result of the rapid death of the majority of the bacteria, there arose situations where only two bacteria were left after fighting with another colony. Since a convex hull requires at minimum three points a line remained and an alternative algorithm was introduced (found in overlap_checking.py). Also, if there are three or fewer than three bacteria in one colony, this colony cannot attend fight because if it overlaps with more than three colonies it does not have enough bacteria to fight with other colonies.


- **Note on duplication and Voronoi cells**

In the case that the duplicate of a bacteria inhabited the same point in space as the parent bacteria the two were treated as one in the construction of the Voronoi cell and when calculated the neighbours around certain bacteria. Therefore, a small modification was made in ???duplicate??? method in Colony class that duplicated bacteria will be randomly placed around the original bacteria within an enough small region to make the Voronoi function work properly. The new position will not be out of the playground or from coated region to uncoated one and the effect of this modification on the total result is very small.


