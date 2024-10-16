# Campus Bikes
## https://leetcode.com/problems/campus-bikes

On a campus represented on the X-Y plane, there are n workers and m bikes, with n <= m.

You are given an array workers of length n where workers[i] = [xi, yi] is the position of the ith worker. You are also given an array bikes of length m where bikes[j] = [xj, yj] is the position of the jth bike. All the given positions are unique.

Assign a bike to each worker. Among the available bikes and workers, we choose the (workeri, bikej) pair with the shortest Manhattan distance between each other and assign the bike to that worker.

If there are multiple (workeri, bikej) pairs with the same shortest Manhattan distance, we choose the pair with the smallest worker index. If there are multiple ways to do that, we choose the pair with the smallest bike index. Repeat this process until there are no available workers.

Return an array answer of length n, where answer[i] is the index (0-indexed) of the bike that the ith worker is assigned to.

The Manhattan distance between two points p1 and p2 is Manhattan(p1, p2) = |p1.x - p2.x| + |p1.y - p2.y|.

```
Example 1:
Input: workers = [[0,0],[2,1]], bikes = [[1,2],[3,3]]
Output: [1,0]
Explanation: Worker 1 grabs Bike 0 as they are closest (without ties), and Worker 0 is assigned Bike 1. So the output is [1, 0].

Example 2:
Input: workers = [[0,0],[1,1],[2,0]], bikes = [[1,0],[2,2],[2,1]]
Output: [0,2,1]
Explanation: Worker 0 grabs Bike 0 at first. Worker 1 and Worker 2 share the same distance to Bike 2, thus Worker 1 is assigned to Bike 2, and Worker 2 will take Bike 1. So the output is [0,2,1].
``` 

## Constraints:

1. n == workers.length
2. m == bikes.length
3. 1 <= n <= m <= 1000
4. workers[i].length == bikes[j].length == 2
5. 0 <= xi, yi < 1000
6. 0 <= xj, yj < 1000
7. All worker and bike locations are unique.

## Implementation :
```java
class Solution {
    List<List<Pair<Integer, Integer>>> workerToBikeList = new ArrayList<>();
    int closestBikeIndex[] = new int[1001];
    class WorkerBikePair {
        int workerIndex;
        int bikeIndex;
        int distance;   
        WorkerBikePair(int workerIndex, int bikeIndex, int distance) {
            this.workerIndex = workerIndex;
            this.bikeIndex = bikeIndex;
            this.distance = distance;
        }
    }
    Comparator<WorkerBikePair> WorkerBikePairComparator 
        = new Comparator<WorkerBikePair>() {
        public int compare(WorkerBikePair a, WorkerBikePair b) {
            if (a.distance != b.distance) 
              return a.distance - b.distance;
            else if (a.workerIndex != b.workerIndex)
              return a.workerIndex - b.workerIndex;
            else     
              return a.bikeIndex - b.bikeIndex;
        }
    };

    int findDistance(int[] worker, int[] bike) {
        return Math.abs(worker[0] - bike[0]) + Math.abs(worker[1] - bike[1]);
    }
    void addClosestBikeToPq(PriorityQueue<WorkerBikePair> pq, int worker) {
        Pair<Integer, Integer> closestBike = workerToBikeList.get(worker)
            .get(closestBikeIndex[worker]);
        closestBikeIndex[worker]++;
        
        WorkerBikePair workerBikePair 
            = new WorkerBikePair(worker, closestBike.getValue(), closestBike.getKey());
        pq.add(workerBikePair);
    }
    
    public int[] assignBikes(int[][] workers, int[][] bikes) {
        PriorityQueue<WorkerBikePair> pq = new PriorityQueue<>(WorkerBikePairComparator);
        for (int worker = 0; worker < workers.length; worker++) {
            List<Pair<Integer, Integer>> bikeList = new ArrayList<>();
            for (int bike = 0; bike < bikes.length; bike++) {
                int distance = findDistance(workers[worker], bikes[bike]);
                bikeList.add(new Pair(distance, bike));
            }
            Collections.sort(bikeList, Comparator.comparing(Pair::getKey));
            workerToBikeList.add(bikeList);
            closestBikeIndex[worker] = 0;
            addClosestBikeToPq(pq, worker);    
        }
        
        boolean bikeStatus[] = new boolean[bikes.length];
        int workerStatus[] = new int[workers.length];
        Arrays.fill(workerStatus, -1);
        
        while (!pq.isEmpty()) {
            WorkerBikePair workerBikePair = pq.remove();
            int worker = workerBikePair.workerIndex;
            int bike = workerBikePair.bikeIndex;
            if (workerStatus[worker] == -1 && !bikeStatus[bike]) {
                bikeStatus[bike] = true;
                workerStatus[worker] = bike;
            } else {
                addClosestBikeToPq(pq, worker);
            }
        }
        return workerStatus;
    }
}
```
