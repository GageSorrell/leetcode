# 2 | Valid Parentheses

*Difficulty - Easy*

⚡ A trivial note — the singular form of parentheses is *parenthesis*.

## The Problem

Given a string, `s`, of characters `(`, `)`, `[`, `]`, and `{`, `}`, determine if the string is valid under these conditions:

I. Open brackets must be closed by the same type
II. Open brackets must be closed in the correct order

## Understanding The Problem

Without loss of generalization, let us work left-to-right across the string.  What are patterns we can expect to see in a valid string?

Walking across the string, we would *not* expect to see any closing brackets before an open bracket of the same type.  Additionally, for *n* open brackets of a given type, we must find exactly *n* closing brackets of the same type.

Now, we propose an intuitive way of framing the problem: the notion of *removing* brackets as we solve the problem.  Suppose `s = "((([[{{}}]])))")`.  We can begin testing the validity by composing a string that consists of brackets *not including* valid pairs that we have already found.

For example, `"((([[{{}}]])))"` ↦ `"((([[]])))"` ↦ `((()))` ↦ ` `.  Thus, a valid string is mapped (one way or another) to the empty string.

Condition II gives us one more thing to consider before arriving to the solution: from `s = "(([)])"`, we see that the method of matching closing brackets with leftward opening brackets works only when the matching brackets have no *not-yet-matched* brackets between them.  In other words, it does not only matter whether there exists a leftward opening bracket, but the order of leftward brackets also matters.


## Solving The Problem

This notion of *removal* and the above consideration leads us to the data structure which is most beneficial for solving this problem: **the stack**.  As we walk across the string, for each closing bracket we check for an opening bracket of the same type to the left.  If that opening bracket is found, we pop it off of the stack.  If there exists a closing bracket without an opening bracket at the top of the stack, then the string is invalid.

In addition to using a stack, we also need a hashmap.  This is far more trivial than arriving to the use of a stack; we simply need a way to define which brackets match up, and the easiest way is to create a map.  This clearly forms a bijection, so we need to choose a "direction" for our map.  Since we are going to match opening brackets with closing brackets, we will map them accordingly:

```
) ↦ (
] ↦ [
} ↦ {
```

Because we need to only walk the string once, we have $O(n)$ time- and space-complexity.

### C++ Details

C++ gives us a `std::stack`, which has these useful methods:

* `std::stack::top` allows us to peak at the top element (returns a reference)
* `std::stack::push` allows us to add to the stack
* `std::stack::pop` allows us to remove from the stack

And for the hashmap, we will use `std::unordered_map`.  This gives us on average $O(1)$ time-complexity, whereas `std::map` has $O(\operatorname{log} n)$ time-complexity.  This is not a serious consideration for a map with three elements, but since this is LeetCode, we consider this anyway.

* `std::unordered_map::count` can tell us whether a key exists by whether the value is non-zero.

Another small detail: `std::string`s can be iterated over via a range-based loop, `for(const char& Character : MyString)`.

### C++ Solution

```cpp
#include <stack>
#include <unordered_map>

class Solution
{
public:
    bool isValid(std::string Brackets)
    {
        std::stack<char> Stack;
        std::unordered_map<char, char> BracketsMap;
        
        BracketsMap[')'] = '(';
        BracketsMap[']'] = '[';
        BracketsMap['}'] = '{';

        for(const char& Bracket : Brackets)
        {
            const bool bClosingBracket = BracketsMap.count(Bracket) > 0;
            if(bClosingBracket)
            {
                const char OpeningBracket = BracketsMap.at(Bracket);
                if(!Stack.empty() && Stack.top() == OpeningBracket)
                {
                    Stack.pop();
                }
                else
                {
                    /* There exists no appropriate opening bracket. */
                    return false;
                }
            }
            else
            {
                Stack.push(Bracket);
            }
        }

        /* Return whether all characters have been removed (and thus the string was valid). */
        return Stack.empty();
    }
};
```