# 动态规划--DP

[背包问题九讲](https://www.kancloud.cn/kancloud/pack/70125)

[背包问题讲解](http://www.wutianqi.com/?p=539)

### 1.01背包问题

> 注意:每个物品最多只能装1个
>
> 输入: 1.包能装的最大重量weight  2.所有物品的重量及其价值，用capacity value，分别表示。
>
> 输出:该背包能装的最大价值



```php
function package($weight,$capacity,$value)
{
    //建立dp数组
    $dp=[];
    for($i=0;$i<=$weight;$i++){
        $dp[$i]=0;
    }

    $amount=count($capacity);
    for($i=0;$i<$amount;$i++){
        //注意这里的倒序，可以防止同一物品多次装入，这是和完全背包的核心差别
        for($j=$weight;$j>=$capacity[$i];$j--){
            $dp[$j]=max($dp[$j],$dp[$j-$capacity[$i]]+$value[$i]);
        }
    }
    return max($dp);
}
```



### 2.完全背包问题

> 完全背包(CompletePack): 有N种物品和一个容量为V的背包，每种物品都有无限件可用。第i种物品的费用是c[i]，价值是w[i]。求解将哪些物品装入背包可使这些物品的费用总和不超过背包容量，且价值总和最大



**注意与01背包的区别，在于内循环，01是倒序，完全是顺序，这个区别要思考一下**

```php
//完全背包问题
function totalPackage($weight,$capacity,$value)
{
    //建立dp数组
    $dp=[];
    for($i=0;$i<=$weight;$i++){
        $dp[$i]=0;
    }

    $amount=count($capacity);
    for ($i=0;$i<$amount;$i++){
        for($j=0;$j<=$weight;$j++){
            if ($j-$capacity[$i]>=0){
                $dp[$j]=max($dp[$j],$dp[$j-$capacity[$i]]+$value[$i]);
            }
        }
    }

    return max($dp);
}
```



### 3.多重背包问题

> 多重背包(MultiplePack): 有N种物品和一个容量为V的背包。第i种物品最多有n[i]件可用，每件费用是c[i]，价值是w[i]。求解将哪些物品装入背包可使这些物品的费用总和不超过背包容量，且价值总和最大。 



> f\[i][v]=max{f\[i-1][v-k\*c[i]]+k*w[i]|0<=k<=n[i]}





### 4.跳跃游戏

给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个位置。

**示例 1:**

```
输入: [2,3,1,1,4]
输出: true
解释: 从位置 0 到 1 跳 1 步, 然后跳 3 步到达最后一个位置。
```



```php
function canJump($nums)
{
    $len=count($nums);
    $dp=[];
    for($i=0;$i<=$len;$i++){
        $dp[$i]=0;
    }
    $dp[0]=1;

    for($i=0;$i<$len;$i++){
        if($dp[$i]==1){
            for($j=$i+1;$j<=$i+$nums[$i];$j++){
                if($j<$len){
                    $dp[$j]=1;
                }
            }
        }
    }

    return (boolean)$dp[$len-1];
}
```



### 5.不同路径



