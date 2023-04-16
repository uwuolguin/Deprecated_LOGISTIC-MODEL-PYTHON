Problem Summary:

A company has to transfer merchandise from suppliers to retailers, whether or not they use the available cross-docking facilities they have.

For a network made of S suppliers, R retailers, and C cross-dock facilities, the objective is to find the best fleet dispatching and consolidation plan so the total transportation cost is minimized. This is done by determining the loads to be sent directly from the suppliers to the retailers and the amounts to be sent indirectly through the cross-docking facilities. The loads sent to the cross-dock facilities are consolidated and sent accordingly to their final destination.

The mathematical model adopted assumes the following:

-The loads to be sent from a supplier location S to a Retailer R are known.
-Trucks are always available when needed.
-All the trucks have the same capacity (C).
-The shipped loads are assumed to have the same cross sections that can utilize the whole frontal cross section of the truck. The shipped quantities are expressed by the length they need to utilize the overall length of the truck. This reduces the complexity of the shipped items from their three dimensional shapes into single dimensional shapes.
-The shipping costs are associated with both the traveled distances and traveling time among locations.


For situations where this problem has a low number of retailers, cross-dock facilities, and suppliers, you can even find the best solution manually, but for more complex scenarios, you need to build a system that can simulate the situation and lets you  find a good way to reduce costs.

Challenge:

1) Establish a Model that can adequately simulate the situation.

2) Establish a Data Model that allows us to properly create, read, update, and delete data.

3) Build a script that calculates the plan that minimizes transportation costs.

Solution:

1) We can see three main entities:

	a) Suppliers: They are going to have a name, social security number, and sector for this problem.
	b) Cross-Dock Facilities: They are going to have a name and a location for this problem.
	c) Retailers: They are going to have a name and social security number for this problem.


2) There are relationships between these entities, specifically:
	
	a) There is a cost of transportation from cross-dock facility C to Retailer R.
	b) There is a cost of transportation from Supplier S to the cross-dock facility C.
	c) There is a cost of transportation from Supplier S to Retailer R.
	d) There is an amount of merchandise to transport from Supplier S to Retailer R.

3) We can establish an average load capacity for the trucks, so the value its fixed for every truck.


4) With point 1 and 2, we can build a database in postgresql with the following Tables:

	a) crossdock, this table will contain 2 columns:

		i)  name: This column will be a 100 character varchar, unique and will represent the name of the cross-dock facility.
		ii) location: This column will be a varchar of 100 characters and will represent the location of the cross-dock facility.

	b) retailer, this table will contain 2 columns:

		i)  social security number: This column will be a varchar of 20 characters, unique and will represent the social security number of the 				    retailer.
		ii) name: This column will be a varchar of 100 characters and will represent the name of the retailer.

	c) supplier, this table will contain 3 columns:

 		i)   social security number: This column will be a varchar of 20 characters, unique and will represent the social security number of the 				     supplier.
		ii)  name: This column will be a varchar of 100 characters and will represent the name of the supplier.
		iii) sector: This column will be a varchar of 100 characters and will represent the sector of the supplier.

	d) crossdock_retailer, this table will contain 3 columns:

 		i)   r_key: This column will be the foreign key of the retailer, its social security number.
		ii)  c_key: this column will be the crossdock foreign key, its name.
		iii) cost_cr: This column will be of type int and will represent the cost of transportation from crossdock C to retailer R.

	e) supplier_crossdock, this table will contain 3 columns:

 		i)   s_key: This column will be the foreign key of the supplier, its social security number.
		ii)  c_key: this column will be the foreign key of the cross-dock, its name.
		iii) cost_sc: This column will be of type int and will represent the cost of transportation from supplier S to crossdock C.

	f) supplier_retailer, this table will contain 4 columns:

 		i)   s_key: This column will be the foreign key of the supplier, its social security number.
		ii)  r_key: This column will be the foreign key of the retailer, its social security number.
		iii) cost_sc: This column will be of type int and will represent the cost of transportation from supplier S to retailer R.
		iV)  q_sr: This column will be of type int, and will represent the amount of merchandise to be transported from supplier S to retailer R.


5) Optional reading: 

	Simulated annealing algorithm, is a stochastic global search optimization algorithm. The algorithm is inspired by annealing in metallurgy where metal is heated to a high temperature quickly, then cooled slowly, which increases its strength and makes it easier to work with. The annealing process works by first exciting the atoms in the material at a high temperature, allowing the atoms to move around a lot, then decreasing their excitement slowly, allowing the atoms to fall into a new, more stable configuration. The simulated annealing optimization algorithm can be thought of as a modified version of stochastic hill climbing. Stochastic hill climbing maintains a single candidate solution and takes steps of a random but constrained size from the candidate in the search space. If the new point is better than the current point, then the current point is replaced with the new point. This process continues for a fixed number of iterations. Simulated annealing executes the search in the same way. The main difference is that new points that are not as good as the current point (worse points) are accepted sometimes. A worse point is accepted probabilistically where the likelihood of accepting a solution worse than the current solution is a function of the temperature of the search and how much worse the solution is than the current solution. The initial temperature for the search is provided as a hyperparameter and decreases with the progress of the search. A number of different schemes (annealing schedules) may be used to decrease the temperature during the search from the initial value to a very low value, although it is common to calculate temperature as a function of the iteration number. A popular example for calculating temperature is the so-called “fast simulated annealing,” calculated as follows: 

	temperature = initial_temperature / (iteration_number + 1)

We add one to the iteration number in the case that iteration numbers start at zero, to avoid a divide by zero error.
The acceptance of worse solutions uses the temperature as well as the difference between the objective function evaluation of the worse solution and the current solution. A value is calculated between 0 and 1 using this information, indicating the likelihood of accepting the worse solution. This distribution is then sampled using a random number, which, if less than the value, means the worse solution is accepted.This is called the metropolis acceptance criterion and for minimization is calculated as follows:

	criterion = exp( -(objective(new) – objective(current)) / temperature)

Where exp() is e (the mathematical constant) raised to a power of the provided argument, and objective(new), and objective(current) are the objective function evaluation of the new (worse) and current candidate solutions.The effect is that poor solutions have more chances of being accepted early in the search and less likely of being accepted later in the search. The intent is that the high temperature at the beginning of the search will help the search locate the basin for the global optima and the low temperature later in the search will help the algorithm hone in on the global optima.

This algorithm will be implemented in the final solution!!!!!!!!!!!!!


6) Script:

	With points 1, 2, 3, 4 and 5 complete, we can build a script that:

		a) Uses the data from the Postgresql DB created for this particular problem.
		b) Allows you to define the loading capacity of the trucks.
		c) Allows you to define the parameters of the simulated annealing algorithm.
		d) Finds a good solution to the minimization problem.
		e) Gives you a detailed plan to reduce transportation costs.


Script Explanation:



Note: The script is written in Python, a very slow language, to use this repository in a standard complex situation, please translate the script to C, C++, or any other language with high performance.