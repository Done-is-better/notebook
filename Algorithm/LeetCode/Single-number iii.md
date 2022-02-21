```java
//异或运算查找数组中两个只出现一次的数
class Solution {
    public int[] singleNumber(int[] nums) {
        //声明两个数为a.b，nums中全体异或运算结果为eor
        int a = 0 ;
        int b = 0; 
        int eor = 0;
        for(int i=0; i<nums.length; i++){
            eor ^= nums[i];
        }
        //通过与运算，找出eor二进制中从右往左第一个1
        int div =1;
        while( (div & eor)== 0){
            div = div << 1;
        }
        //用div将数组nums分为两组
        for(int i=0; i<nums.length; i++){
            if((div & nums[i] )== 0){
                a ^= nums[i];
            }else{
                b ^= nums[i];
            }
        }
        return new int[]{a,b};
        
    }
  
}
```

