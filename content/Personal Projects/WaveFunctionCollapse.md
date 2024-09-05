+++
title = 'Generic Wave Function Collapse - Unreal Engine 5 - C++ Implementation'
date = 2024-09-05T11:57:26+01:00
+++


I knew that I wanted to implement a procedural algorithm for my latest project, and decided on creating a wave function collapse algorithm as it seemed like a good starting point for learning more advanced procedural algorithms. To add more challenge to this, I wasn't using it for generating maps, or textures but instead wanted it to interact with choosing which items to spawn into the map based on a variety of factors that were not known when starting the implementation.

Since I wanted this to be as extensible and useful as possible, I decided to implement it using an interface and the subsystem would hold an array of registered objects. Each UObject would handle how they would receive the propagation from the system, how they define their entropy and how they collapse. 

## Basic Algorithm

Each Node has an **entropy** value, this represents how many possible states that Node has. Whilst there is a Node in the graph that has an entropy greater than 1, we select a **Node** to collapse and then **propagate that change** to it's neighbours. These neighbours receive this change, compare it to it's own **states** and remove the states that are no longer possible with it's **constraints**. If a Node has an entropy of 0, then it no longer has any possible states. Depending on how you want to deal with this, you could either remove the Node or reset the wave function and try again.

After a Neighbour Node has received the change and compared the states, the algorithm will calculate the difference in entropy and if it is greater than 1, it will be added to the queue to propagate. This is because if a state gets removed from a Node, it might have been the only state that made another Node's state possible. Therefore, it will need to be notify it's neighbours that the state has been removed. 

## Interface

The IWaveFunctionNodeInterface forms the foundation for the Node based workflow that I intended this implementation to follow, being able to make any UObject influenced by the system, and not just Actors, opens up a lot more potential. As you could see from my other work, I really like to work in more generic terms that can be applied to a lot more different types of use cases. 

Here are the main functions in the interface

```c++
public:

	UFUNCTION(BlueprintNativeEvent, BlueprintAuthorityOnly, BlueprintCallable, Category = "Wave Function Collapse")
	void GetNeighbourNodes(TArray<UObject*>& OutNeighbourNodes) const;

	UFUNCTION(BlueprintNativeEvent, BlueprintAuthorityOnly, BlueprintCallable, Category = "Wave Function Collapse")
	int GetEntropy() const;
	
	UFUNCTION(BlueprintNativeEvent, BlueprintAuthorityOnly, BlueprintCallable, Category = "Wave Function Collapse")
	void GetPossibleStates(TArray<TSoftObjectPtr<UWFC_StatePrimaryDataAsset>>& OutPossibleStates) const;

	UFUNCTION(BlueprintNativeEvent, BlueprintAuthorityOnly, Category = "Wave Function Collapse")
	void ReceiveNodePropagation(const UObject* PropagatedNode);

	UFUNCTION(BlueprintNativeEvent, BlueprintAuthorityOnly, Category = "Wave Function Collapse")
	void CollapseNode();

	UFUNCTION(BlueprintNativeEvent, BlueprintAuthorityOnly, Category = "Wave Function Collapse")
	void PostWaveFunctionCollapse();

	UFUNCTION(BlueprintNativeEvent, BlueprintAuthorityOnly, Category = "Wave Function Collapse")
	void ResetNode();
};

```

## Subsystem 

I decided on a World Subsystem to form the basis for the implementation, because it made it the most sense for the intended use. Being able to register actors as Nodes to collapse, as well as being able to spawn in actors and managed lifetime it was the best choice. I could have gone for an Info Actor (similar to a Game Mode, or State) but I wanted to be able to access it from any actor without performing a GetActorOfClass operation. 

After all of the Nodes have been registered, we can trigger then collapse:

```c++

void UWaveFunctionCollapseSubsystem::CollapseWaveFunction()
{
	// Reset the wave function
	ResetWaveFunction();
	
	UE_LOG(LogTemp, Warning, TEXT("Collapsing Wave Function"));
	// While there are nodes to collapse
	int Iteration = 0;
	while(Iteration < WFC_MAX_COLLAPSE_ITERATIONS &&
	RegisteredNodes.ContainsByPredicate([](const FWFC_Node& RegisteredNode)
	{
		return IWaveFunctionNodeInterface::Execute_GetEntropy(RegisteredNode.Node) > 1;
	}))

```
For the conditions for the `while loop`, I needed to first ensure that this wouldn't result in an infinite loop so a basic Iteration counter with a hard coded Macro seemed like a good solution for experimenting with this concept. The second condition checks if any of the Nodes have an Entropy greater than 1, but it will return on the first instance of this. A potential optimisation here would be to cache the value when calculated in main body of the algorithm.


In the first part of the loop, we first retrieve the Node with the lowest entropy. We want to this one because we are less likely to pick a state that will invalidate more states when propagated. If the index is not a valid index, we break out of the while loop.

```c++
	{
		UE_LOG(LogTemp, Warning, TEXT("Iteration: [%d]"), Iteration);
		// Select a random node to collapse
		const int NodeIndex = GetLowestEntropyNodeIndex();
		if(!RegisteredNodes.IsValidIndex(NodeIndex))
		{
			UE_LOG(LogTemp, Warning, TEXT("Invalid Node Index: %d"), NodeIndex);
			break;
		}
		
```
To get the Node with the lowest entropy, it is a simple iteration over the RegisteredNodes array. I filtered out Nodes
 that have an entropy greater than 1. If it had 0-1, then it either is completely invalidated or has collapsed to one state, 
 so not valid candidates.
```c++ int UWaveFunctionCollapseSubsystem::GetLowestEntropyNodeIndex() const
{
	int LowestEntropyNodeIndex = -1;
	int LowestEntropy = TNumericLimits<int>::Max();

	for(int i = 0; i < RegisteredNodes.Num(); i++)
	{
		const int& Entropy = IWaveFunctionNodeInterface::Execute_GetEntropy(RegisteredNodes[i].Node);
		if(Entropy < LowestEntropy && Entropy > 1)
		{
			LowestEntropy = Entropy;
			LowestEntropyNodeIndex = i;
		}
	}

	return LowestEntropyNodeIndex;
}
```

Continuing on with the main algorithm, it is rather simple. Collapsing the Node by the Index that was just retrieved, 
and then create a new Queue of Node Indexes and propagate the change. 
```c++
		UE_LOG(LogTemp, Warning, TEXT("Selected Node: %s"), *GetNameSafe(RegisteredNodes[NodeIndex].Node));
		// Collapse the node
		if(CollapseNode(NodeIndex))
        {
            TQueue<int> PropagationQueue;
            // Propagate the wave function
            UE_LOG(LogTemp, Warning, TEXT("Propagating Wave Function"));
            PropagateWaveFunction(NodeIndex, PropagationQueue);
        }
		++Iteration;
	}
```

Collapsing the Node is quite easy, thanks to the interface each Node can be responsible for their own collapse.
To check if the collapse was successful, there is verification that the entropy is within expected ranges.
```c++
bool UWaveFunctionCollapseSubsystem::CollapseNode(int NodeIndex)
{
	UE_LOG(LogTemp, Warning, TEXT("Collapsing Node: %s"), *GetNameSafe(RegisteredNodes[NodeIndex].Node));

	// Collapse the node
	IWaveFunctionNodeInterface::Execute_CollapseNode(RegisteredNodes[NodeIndex].Node);
	UE_LOG(LogTemp, Warning, TEXT("Node Collapsed: %s"), *GetNameSafe(RegisteredNodes[NodeIndex].Node));
	return IWaveFunctionNodeInterface::Execute_GetEntropy(RegisteredNodes[NodeIndex].Node) <= 1;
	
}
```


The propagation has a lot of checks but at the basic level, it retrieves the Neighbour Nodes, performs 
some checks on them, propagates the Node to them and then calculate the difference in entropy. Only if the Neighbour Node
has successfully passed all of these checks, will they then be added to the Propagation Queue. 
```c++
void UWaveFunctionCollapseSubsystem::PropagateWaveFunction(int NodeIndex, TQueue<int>& PropagationQueue)
{
	// Retrieve the node's neighbours
	TArray<UObject*> Neighbours;
	IWaveFunctionNodeInterface::Execute_GetNeighbourNodes(RegisteredNodes[NodeIndex].Node, Neighbours);
	UE_LOG(LogTemp, Warning, TEXT("Retrieved Neighbours: %d"), Neighbours.Num());
	
	// Iterate through the neighbours
	for(int i = 0; i < Neighbours.Num(); i++)
	{
		// Check if the Object is valid
		if(!IsValid(Neighbours[i]))
		{
			UE_LOG(LogTemp, Warning, TEXT("Invalid Neighbour: %s"), *GetNameSafe(Neighbours[i]));
			continue;
		}
		const int& NeighbourEntropy = IWaveFunctionNodeInterface::Execute_GetEntropy(Neighbours[i]);

		// Check if they have been collapsed already
		if(NeighbourEntropy <= 1)
		{
			UE_LOG(LogTemp, Warning, TEXT("Neighbour already collapsed: %s"), *GetNameSafe(Neighbours[i]));
			continue;
		}

		// Propagate the wave function
		IWaveFunctionNodeInterface::Execute_ReceiveNodePropagation(Neighbours[i], RegisteredNodes[NodeIndex].Node);
		UE_LOG(LogTemp, Warning, TEXT("Propagating Wave Function to Neighbour: %s"), *GetNameSafe(Neighbours[i]));

		// If the Entropy is the same, then we can safely skip this node
		if(NeighbourEntropy == IWaveFunctionNodeInterface::Execute_GetEntropy(Neighbours[i]))
		{
			UE_LOG(LogTemp, Warning, TEXT("Neighbour Entropy is the same: %s"), *GetNameSafe(Neighbours[i]));
			continue;
		}
		
		// Add the neighbour to the propagation queue
		PropagationQueue.Enqueue(RegisteredNodes.Find(FWFC_Node(Neighbours[i])));
	}
	

	// Recursively propagate the wave function
	while(PropagationQueue.Dequeue(NodeIndex))
	{
		UE_LOG(LogTemp, Warning, TEXT("Propagating Wave Function: %s"), *GetNameSafe(RegisteredNodes[NodeIndex].Node));
		PropagateWaveFunction(NodeIndex, PropagationQueue);
		UE_LOG(LogTemp, Warning, TEXT("Wave Function Propagated: %s"), *GetNameSafe(RegisteredNodes[NodeIndex].Node));
		++PropagationCount;
	}
}

```


Finally, after the main while loop has completed, there is one final iteration through the
Registered Nodes. This is to perform any cleanup that the Nodes will need to do, as well as ensure that each Node
can receive a notification when the collapse has completed. 

```c++

	for(int i = 0; i < RegisteredNodes.Num(); i++)
	{
		IWaveFunctionNodeInterface::Execute_PostWaveFunctionCollapse(RegisteredNodes[i].Node);
	}

```



## State and Constraints

To represent the State, I used a Primary Data Asset as my base. The base class just contains an array of constraint objects.

```c++

	UPROPERTY(EditAnywhere, BlueprintReadOnly, Instanced, Category = "Wave Function Collapse")
	TArray<UWFC_ConstraintObject*> Constraints;
```
They also hold two functions, to validate themselves against other states 
```c++

bool UWFC_StatePrimaryDataAsset::IsStatePossible(const UWFC_StatePrimaryDataAsset* OtherState,
	const UObject* SourceNode, const UObject* TargetNode) const
{
	// Need to see if any of the constraints are violated
	for (const UWFC_ConstraintObject* Constraint : Constraints)
	{
		if (!Constraint->AreStatesCompatible(this, OtherState))
		{
			UE_LOG(LogTemp, Warning, TEXT("State [%s] is not possible with [%s]"), *GetNameSafe(this), *GetNameSafe(OtherState));
			return false;
		}
		if(!Constraint->AreNodeStatesCompatible(this, OtherState, SourceNode, TargetNode))
		{
			UE_LOG(LogTemp, Warning, TEXT("Node State [%s] is not possible with [%s]"), *GetNameSafe(this), *GetNameSafe(OtherState));
			return false;
		}
	}
	return true;
}

bool UWFC_StatePrimaryDataAsset::IsStatePossibleWithOthers(const TArray<TSoftObjectPtr<UWFC_StatePrimaryDataAsset>>& OtherStates,
	const UObject* SourceNode, const UObject* TargetNode) const
{
	UE_LOG(LogTemp, Warning, TEXT("Checking if [%s] is possible with others"), *GetNameSafe(this));

	// Need to see if any of the other states are possible
	for (const TSoftObjectPtr<UWFC_StatePrimaryDataAsset>& OtherState : OtherStates)
	{
		const UWFC_StatePrimaryDataAsset* OtherStatePtr = OtherState.LoadSynchronous();
		
		if(IsStatePossible(OtherStatePtr, SourceNode, TargetNode))
		{
			UE_LOG(LogTemp, Warning, TEXT("State [%s] is possible with [%s]"), *GetNameSafe(this), *GetNameSafe(OtherState.Get()));
			return true;
		}
	}
	UE_LOG(LogTemp, Warning, TEXT("No possible states found"));
	return false;
}
```
This was a pitfall that I ran into, where I was checking to see if any states violated the current state in the `IsStatePossibleWithOthers` function. That wasn't the intended purpose of this function, as it controlled if the state was removed from the valid possibilities on the Node. I realised I needed to check if it was possible with any of the other States on a Node rather than with all of them.



Constraints have a single purpose which is to validate if two states are compatible. Some states can be dependent on traits only accessed via the Node that they are 
associated with such as their world location. 
```c++

	UFUNCTION(BlueprintNativeEvent, BlueprintAuthorityOnly, BlueprintCallable, Category = "Wave Function Collapse")
	bool AreStatesCompatible(const UWFC_StatePrimaryDataAsset* StateA, const UWFC_StatePrimaryDataAsset* StateB) const;

	UFUNCTION(BlueprintNativeEvent, BlueprintAuthorityOnly, BlueprintCallable, Category = "Wave Function Collapse")
	bool AreNodeStatesCompatible(const UWFC_StatePrimaryDataAsset* StateA, const UWFC_StatePrimaryDataAsset* StateB, const UObject* NodeA, const UObject* NodeB) const;
```
