# Two-pass Hash Table

문제출처: https://leetcode.com/problems/two-sum/solution/
TwoSum

```kotlin
fun twoSum(nums: IntArray, target: Int): IntArray? {
    val map: MutableMap<Int, Int> = HashMap()
    
    for (i in nums.indices) {
        map[nums[i]] = i
    }
    
    for (i in nums.indices) {
        val complement = target - nums[i]
        if (map.containsKey(complement) && map[complement] != i) {
            return intArrayOf(i, map[complement]!!)
        }
    }

    throw IllegalArgumentException("No two sum solution")
}
```

