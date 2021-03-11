# One-pass Hash Table

문제출처: https://leetcode.com/problems/two-sum/solution/TwoSum

```kotlin
    fun twoSum(nums: IntArray, target: Int): IntArray {
       val map = HashMap<Int, Int>()

        nums.forEachIndexed { index, num ->
            val complement = target - num

            if (map.containsKey(complement)) {
                return intArrayOf(map[complement]!!, index)
            }
            map[num] = index
        }

        return intArrayOf()
    }
```

