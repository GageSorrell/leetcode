# 1 | Two Sum

*Difficulty - Easy*

For this problem, there exist a trivial solution and an optimal solution (*one-pass*).

## The Problem

Given an array of integers and a *target* integer, find the indices of two values whose sum is the target integer.

There are two constraints (one which helps us, and one which works against us),
    * The possible arrays form an injective map to the pair of desired integers (*i.e.*, there is exactly one solution for a given array)
    * No solutions may use the same element twice (even if the sum would be the target integer)

Example:
Given $[ 2, 7, 11, 15 ]$ and $Target = 9$, we observe that $2 + 7 = 9 = Target$, therefore the answer is indices $0$ and $1$.

## Trivial Solution

The trivial solution is to simply iterate over the array once, and for each integer, calculate the sum of this integer with every other integer in the array.

This could mean iterating over every element in the array for each element in the array, so there is a time complexity of $O(n^2)$.  This is not ideal.

### C++ Code

```cpp
class Solution {
public:
    std::vector<int> twoSum(std::vector<int>& nums, int target) {
        int i_FirstNumber = -1;
        int i_SecondNumber = -1;
        
        for(int i = 0; i < nums.size(); i++)
        {
            for(int j = 0; j < nums.size(); j++)
            {
                if(i == j)
                {
                    continue;
                }
                
                const int Sum = nums[i] + nums[j];
                
                if(Sum == target)
                {
                    i_FirstNumber = i;
                    i_SecondNumber = j; 
                }
            }
        }
        
        return { i_FirstNumber, i_SecondNumber };
    }
};
```

## Optimal Solution

The optimal solution uses a **hashmap**.  To understand why a hashmap is useful here, observe that for a given iteration over the array, you wish to find the difference between the Target and the current integer.

For example, if in the iteration your integer is $4$, and the Target is $3$, you are looking for an array value of $1$.  For every integer over which we iterate, we can simply check every previous value to see if there exists this difference in the array.  If we haven't found it yet, we move on to the next array element and search for the corresponding difference.

```cpp
class Solution {
public:
    std::vector<int> twoSum(std::vector<int>& Numbers, int Target) {
        std::unordered_map<int, int> Map;
        std::vector<int> Summand;

        for(int Index = 0; Index < Numbers.size(); ++Index)
        {
            const int Number = Numbers[Index];
            const int Difference = Target - Numbers[Index];

            if(Map.find(Difference) != Map.end())
            {
                Summand = { Map[Difference], Index};
                break;
            }
            else
            {
                Map[Number] = Index;
            }
        }

        return Summand;
    }
};
```

# Other Notes

There exists a solution with $O(n \operatorname{log} n)$ time complexity, which is described on this [StackOverflow post](https://stackoverflow.com/questions/8119911/on-logn-algorithm-that-checks-if-sum-of-2-numbers-in-a-int-given-number).