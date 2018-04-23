+++
Categories =["code"]
Tags = ["code"]
date = "2017-04-10T21:28:38+08:00"
title = "输入一个正整数数组，将他们拼接成一个数，输出拼接出所有数字中最小的一个"
+++

#### 输入一个正整数数组，将他们拼接成一个数，输出拼接出所有数字中最小的一个
* 记录一道笔试题


```
    import java.util.*;
    public class JoinNumber {
       /**
     * 编程题：输入一个正整数数组，将他们拼接成一个数，输出拼接出所有数字中最小的一个
     */
     public static void main(String[] args){
           int[] arr = {20,321,21,10,8};
           StringBuffer sb = new StringBuffer();
           for(int i=0;i<arr.length;i++){
                if(i == (arr.length-1)){
                     sb.append(arr[i]); //最后一个不加空格
                }else{
                     sb.append(arr[i] + " "); //拼接时加上空格，一边正则split的使用
                }
           }
           System.out.println(sb);
           String[] str = sb.toString().split(" ");  //利用正则split根据空格，将字符串转成字符串数组
           Arrays.sort(str); //利用Arrays.sort()进行数组的排序，升序
           StringBuffer sb1 = new StringBuffer();  //排序之后再拼接成字符串，即可得到结果
           for(int i= 0;i<str.length;i++){
            //降序的(int i=str.length-1;i>=0;i--;)
                sb1.append(str[i]);
           }
           System.out.println(sb1);
     }
    }
```

#### 我的GitHub：

* https://github.com/pugongyingbo