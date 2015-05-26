title: "floyd_warshall"
date: 2015-04-30 10:58:59
tags:
---



wiki 伪代码

## algorithm

	1 let dist be a |V| × |V| array of minimum distances initialized to ∞ (infinity)
	2 for each vertex v
	3    dist[v][v] ← 0
	4 for each edge (u,v)
	5    dist[u][v] ← w(u,v)  // the weight of the edge (u,v)
	6 for k from 1 to |V|
	7    for i from 1 to |V|
	8       for j from 1 to |V|
	9          if dist[i][j] > dist[i][k] + dist[k][j] 
	10             dist[i][j] ← dist[i][k] + dist[k][j]
	11         end if
	

## Path reconstruction

	let dist be a |V| × |V| array of minimum distances initialized to ∞ (infinity)
	let next be a |V| × |V| array of vertex indices initialized to null

	procedure FloydWarshallWithPathReconstruction ()
	   for each edge (u,v)
	      dist[u][v] ← w(u,v)  // the weight of the edge (u,v)
	      next[u][v] ← v
	   for k from 1 to |V| // standard Floyd-Warshall implementation
	      for i from 1 to |V|
	         for j from 1 to |V|
	            if dist[i][k] + dist[k][j] < dist[i][j] then
	               dist[i][j] ← dist[i][k] + dist[k][j]
	               next[i][j] ← next[i][k]

	procedure Path(u, v)
	   if next[u][v] = null then
	       return []
	   path = [u]
	   while u ≠ v
	       u ← next[u][v]
	       path.append(u)
	   return path