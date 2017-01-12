# 1. Two Sum [Easy]
Given an array of integers, return indices of the two numbers such that they add up to a specific target.
You may assume that each input would have exactly one solution.
Example:
```
Given nums = [2, 7, 11, 15], target = 9,
Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

### 分析
方法1：暴力，复杂度O(n)，会超时
方法2：hash。用一个哈希表，存储每个数对应的下标，复杂度 O(n)
方法3：先排序，然后左右夹逼，排序O(nlog n)，左右夹逼 O(n) ，最终O(nlog n)。但是注意，这题需要返回的是下标，而不是数字本身

### C++
方法2：
```
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> mm; 	\\ unrodered_map比map快
        vector<int> ans(2);
        for(int i = 0; i < nums.size(); ++i){	\\ 一边检索数组，一边判断
            int t = target - nums[i];
            if(mm.find(t) != mm.end()){
                ans[0] = mm[t];
                ans[1] = i;
                break;
            }
            else{
                mm.insert(make_pair(nums[i], i));
            }
        }
        return ans;
    }
};
```
方法3：
```
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<array<int, 2>> mm(nums.size());	\\ 内层使用array比用vector快
        vector<int> ans(2);

        for(int i = 0; i < nums.size(); ++i){	\\ 内层array分别存放值和下标
            mm[i][0] = nums[i];
            mm[i][1] = i;
        }     
        sort(mm.begin(), mm.end(), [](array<int, 2> &arr1, array<int, 2> &arr2){return arr1[0] < arr2[0];});
        
        auto bi = mm.begin();
        auto ei = mm.end();
        --ei;
        while(bi < ei){
            int tmp = (*bi)[0] + (*ei)[0];
            if(tmp == target){
                ans[0] = (*bi)[1];
                ans[1] = (*ei)[1];
                if(ans[0] > ans[1]) swap(ans[0], ans[1]);
                break;
            }
            else if(tmp < target){
                ++bi;
            }
            else{
                --ei;
            }
        }
        return ans;
    }
};
```

### Python
方法2：
```
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        dic = {}
        for i, x in enumerate(nums):
            k = target - x
            if k in dic:
                return [dic[k], i]
            dic[x] = i
```
方法3：
```
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        anums = [(i+1, x) for i, x in enumerate(nums)]
        anums.sort(key=lambda t: t[1])
        start = 0
        end = len(anums) - 1
        while start < end:
            sum = anums[start][1] + anums[end][1]
            if sum > target:
                end -= 1
            elif sum < target:
                start += 1
            else:
                break
        s = anums[start][0]
        e = anums[end][0]
        return [s, e] if s < e else [e, s] 
```