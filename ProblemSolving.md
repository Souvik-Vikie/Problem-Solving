

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

### 7. Products of Array Discluding Self

Given an integer array nums, return an array output where output[i] is the product of all the elements of nums except nums[i].

Each product is guaranteed to fit in a 32-bit integer.

Follow-up: Could you solve it in O(n) time without using the division operation?

```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size() ;
        vector<int> result(n,1) ;

        int prefix =1;
        for(int i=0; i<n; i++)
        {
            result[i] = prefix;
            prefix *= nums[i] ; 
        }

        int postfix = 1;

        for(int i=n-1; i>= 0 ; i-- )
        {
            result[i] = result[i] * postfix ;
            postfix *= nums[i] ; 
        }
        return result; 
    }
};

```

### 8. Valid Sudoku

You are given a a 9 x 9 Sudoku board board. A Sudoku board is valid if the following rules are followed:

Each row must contain the digits 1-9 without duplicates.
Each column must contain the digits 1-9 without duplicates.
Each of the nine 3 x 3 sub-boxes of the grid must contain the digits 1-9 without duplicates.
Return true if the Sudoku board is valid, otherwise return false

Note: A board does not need to be full or be solvable to be valid.

```cpp
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        unordered_map<int, unordered_set<char>>rows;
        unordered_map<int, unordered_set<char>> cols;
        unordered_map<int, unordered_set<char>> squares ;

        for(int r=0 ; r<9 ; r++)
        {
            for(int c=0; c<9; c++)
            {
                int cell = board[r][c] ;
                if(cell == '.'){
                    continue;
                }
                if(rows[r].count(cell) || cols[c].count(cell) || 
                squares[(r/3)*3 + c/3].count(cell) ){
                    return false;
                }
                cols[c].insert(cell);
                rows[r].insert(cell);
                squares[(r/3) *3 + c/3 ].insert(cell) ;
            }
        }
        return true;
    }
};

```

### 9. Longest Consecutive Sequence

Given an array of integers nums, return the length of the longest consecutive sequence of elements.

A consecutive sequence is a sequence of elements in which each element is exactly 1 greater than the previous element.

You must write an algorithm that runs in O(n) time.

```cpp

class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
       unordered_set<int>numSet(nums.begin(), nums.end());
       int longest = 0;
       for(int n: numSet){
        if(numSet.find(n-1)==numSet.end())
        {
            int length =1;
            while(numSet.find(n + length)!= numSet.end())
            {
                length++ ;
            }
            longest = max(longest, length) ;
        }
       }
       return longest;
    }
};

```

## Two Pointers
---

### 1.Is Palindrome

Given a string s, return true if it is a palindrome, otherwise return false.

A palindrome is a string that reads the same forward and backward. It is also case-insensitive and ignores all non-alphanumeric characters.

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
      int l = 0;
      int r = s.size() - 1 ;
      while(l<r)
      {
        while(!isalnum(s[l]) && l<r)
        {
            l++;
        }
        while(!isalnum(s[r]) && l<r)
        {
            r--;
        }
        if(tolower(s[l]) != tolower(s[r]))
        {
            return false;
        }
        l++;
        r--;
      } 
      return true; 
    }
};

```

### 2. Two Integer Sum II

Given an array of integers numbers that is sorted in non-decreasing order.

Return the indices (1-indexed) of two numbers, [index1, index2], such that they add up to a given target number target and index1 < index2. Note that index1 and index2 cannot be equal, therefore you may not use the same element twice.

There will always be exactly one valid solution.

Your solution must use O(1) additional space.

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
     int i=0;
     int j= numbers.size() - 1;
     vector<int> ans;
     while(i < j)
     {
        if(numbers[i] + numbers[j] > target )
        {
            j--;
        }
        else if(numbers[i] + numbers[j] < target)
        {
            i++ ;
        }
        else {
            ans.push_back(i+1);
            ans.push_back(j+1);
            return ans;
        }
     }
     return {};
    }
};

```

### 3. Three Integer Sum

Given an integer array nums, return all the triplets [nums[i], nums[j], nums[k]] where nums[i] + nums[j] + nums[k] == 0, and the indices i, j and k are all distinct.

The output should not contain any duplicate triplets. You may return the output and the triplets in any order.

```cpp

class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
      sort(nums.begin(), nums.end());
        vector<vector<int>> res;
        for(int i= 0; i< nums.size(); i++)
        {
            int target = -nums[i];
            int front = i+1 ;
            int back = nums.size() - 1 ;
            while(front < back)
            {
                int sum = nums[front] + nums[back] ;
                if(sum > target)
                {
                    back --;
                }
                else if( sum < target)
                {
                    front++ ;
                }
                else{
                    vector<int> triplet = {nums[i], nums[front], nums[ back]} ;
                    res.push_back(triplet);

                    while(front< back && nums[front]== triplet[1])
                    {
                        front++;
                    }
                     while(front< back && nums[back]== triplet[2])
                    {
                        back--;
                    }
                }

                while(i+1 < nums.size() && nums[i+1]== nums[i]) 
                {
                    i++ ;
                }
            }
        }
        return res;   
    }
};

```

### 4. Max Water Container

You are given an integer array heights where heights[i] represents the height of the bar.

You may choose any two bars to form a container. Return the maximum amount of water a container can store.

```cpp
class Solution {
public:
    int maxArea(vector<int>& heights) {
        int l=0;
        int h= heights.size() -1;
        int maxValue = 0;
        while(l< h)
        {
            int rh= min(heights[l], heights[h] );
            int rb = h-l;
            int area = rh * rb ;
            maxValue = max(area , maxValue ) ;
            if (heights[l] < heights[h]) {
                l++;
            } else {
                h--;
            }
        }
        return maxValue;
    }
};

```

### 5.Trapping Rain Water

You are given an array non-negative integers heights which represent an elevation map. Each value heights[i] represents the height of a bar, which has a width of 1.

Return the maximum area of water that can be trapped between the bars.

```cpp
class Solution {
public:
    int trap(vector<int>& height) {
        if(height.empty())
        {
            return 0;
        }
        int res = 0;
        int l = 0, r = height.size() - 1;
        int leftMax= height[l];
        int rightMax = height[r];
        while(l<r)
        {
            if(leftMax< rightMax)
            {
                l++;
                leftMax = max(leftMax, height[l]);
                res = res + (leftMax - height[l]) ;
            }
            else{
                r--;
                rightMax = max(rightMax , height[r]) ;
                res = res + (rightMax - height[r]);
            }
        }
        return res;
    }
};

```

## Binary Search
---

### 1. Binary Search

You are given an array of distinct integers nums, sorted in ascending order, and an integer target.

Implement a function to search for target within nums. If it exists, then return its index, otherwise, return -1.

Your solution must run in O(logn) time.

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int l =0;
        int h = nums.size() -1 ;
        while ( l <= h)
        {
            int mid = (l+h)/2 ;
            if(nums[mid]> target )
            {
                h = mid -1;
            }
            else if( nums[mid] < target )
            {
                l =mid+1 ;
            }
            else 
            return mid;
        }
        return (-1) ;
    }
};

```