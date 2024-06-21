

## Problem Solving

## Arrays
---

### 1. Contains Duplicate

Given an integer array nums, return true if any value appears more than once in the array, otherwise return false.

```cpp

class Solution {
public:
    bool hasDuplicate(vector<int>& nums) {
        unordered_map<int,int> mp;
        for (int i = 0; i < nums.size(); i++) {
            mp[nums[i]]++;
        }
        for (auto it = mp.begin(); it != mp.end(); it++) {
            if (it->second > 1) {
                return true;
            }
        }
        return false;
    }
};

```

### 2. Is Anagram 

 Given two strings s and t, return true if the two strings are anagrams of each other, otherwise return false

```cpp

class Solution {
public:

    bool isAnagram(string s, string t) {

        if(s.size()!=t.size())
        {
            return false;
        }
        unordered_map<char,int>count;

        for(char c :s)
        {
            count[c]++;
        }
        
        for(char c : t)
        {
            if( count[c]==0)
            {
                return false;
            }
            count[c]--;
        }
        return true;
        
    }
};

```

### 3. Two Integer Sum

Given an array of integers nums and an integer target, return the indices i and j such that nums[i] + nums[j] == target and i != j.

You may assume that every input has exactly one pair of indices i and j that satisfy the condition.

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> target_sum;
        for(int i=0; i<nums.size(); i++)
        {
            target_sum[nums[i]] = i;
        }

        for(int i=0 ; i< nums.size(); i++)
        {
            int complement = target - nums[i] ;
            if(target_sum.count(complement) && target_sum[complement]!=i)
            {
                return {i, target_sum[complement] };
            }
        }
        return {};
    }
};


```
