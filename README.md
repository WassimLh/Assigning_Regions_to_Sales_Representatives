# Assigning Regions to Sales Representatives

  In this project, we assume that Pfizer, one of the world's biggest biopharmaceutical companies, sells its products to doctors throug Sales Representatives (SR). Hence, those SR need to canvass potential clients and maintain great relationships with them in order to improve the sales efficiency. 

  As our work is based on the following paper:   , we focus on Pfizer's implantation in Istanbul, Turkey. The company has in this city 4 Sales Representativs. Furthermore, Istanbul is divided into 22 elementary bricks, and each brick is assigned to one and only one SR. We also consider that each SR has his office in a Central Brick, from which he has to move to his other assigned bricks regularly to meet doctors.
  
  With a given assignment of SRs, Pfizer executives might want to improve this repartition, which means, finding an assignment with:
   - A minimal total distance covered by the SRs during their trips (from central brick to assigned bricks)
   - A more equitable workload
   - A minimal disruption (i.e. breaking a minimal number of doctor-SR relationships).
   
  As this problem has various constraints and multiple objective functions, we developped Linear Programming Methods to find optimal repartitions, using:
  - Linear combination
  - Epsilon constraint
  
  We also studied two different situations:
  - The first one has fixed central bricks
  - The second one has variable central bricks

