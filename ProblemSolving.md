

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

**Notes:**  target_sum.count(complement) is used to check if complement exists as a key in the target_sum unordered_map.


### 4. Anagram Groups

Given an array of strings strs, group all anagrams together into sublists. You may return the output in any order.

An anagram is a string that contains the exact same characters as another string, but the order of the characters can be different.

```cpp

class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> mp;
        for(auto x:strs)
        {
            string word = x;
            sort(word.begin(), word.end());
            mp[word].push_back(x);
        }
        vector<vector<string>> ans;
        for(auto x: mp){
            ans.push_back(x.second);
        }
        return ans;
    }
};

```

### 5. Top K Elements in List

Given an integer array nums and an integer k, return the k most frequent elements within the array.

The test cases are generated such that the answer is always unique.

```cpp

class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
      unordered_map<int, int> count;
        for (auto x : nums) {
            count[x]++;
        }

        // Step 2: Use a max heap to keep track of top k frequent elements
        priority_queue<pair<int, int>> maxHeap;
        for (auto& [num, freq] : count) {
            maxHeap.push({freq, num});
        }

        // Step 3: Extract the top k elements
        vector<int> ans;
        for (int i = 0; i < k; ++i) {
            ans.push_back(maxHeap.top().second);
            maxHeap.pop();
        }

        return ans;
    }
}
```

### 6. String Encode and Decode

Design an algorithm to encode a list of strings to a single string. The encoded string is then decoded back to the original list of strings.

Please implement encode and decode

```cpp

class Solution {
public:

    string encode(vector<string>& strs) {
        string result = "";
        for(int i=0; i<strs.size(); i++)
        {
            string str = strs[i] ;
            result += to_string(str.size()) + "#" + str ;
        }
        return result;
    }


    vector<string> decode(string s) {
        vector<string> result;

        int i=0 ;
        while( i< s.size())
        {
            int j = i;
            while(s[j]!= '#')
            {
                j++;
            }
            int length = stoi(s.substr(i,j-i));
            string str = s.substr(j+1, length);
            result.push_back(str);
            i = j+1+length;
        }
        return result;
    }
};
```

