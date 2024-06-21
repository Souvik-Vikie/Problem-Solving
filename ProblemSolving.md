

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