常用环境变量
==============

bash的伪随机数生成器

- $RANDOM=======生成0~32767之间的随机数
- $[RANDOM%num]===生成0~num之间的随机数；对算术表达式的值进行引用时需要使用[]

代码示例：通过脚本生成n个随机数(N>5),对这些随机数按从小到大排序

.. code-block:: sh

    #!/bin/bash
    declare -a arrynumber
    read -p "Enter a number:" opt
    opt=$[opt-1]
    for i in `seq 0 $opt`;do
      arrynumer[$i]=$[RANDOM%1000]
    done
    let length=${#arrynumer[@]}
    length=$[length-1]
    for i in `seq 0 $length`;do
      let j=i+1
      for j in `seq $j $length`; do
          if [ ${arrynumer[$j]} -lt ${arrynumer[$i]} ];then
            temp=${arrynumer[$j]}
            arrynumer[$j]=${arrynumer[$i]}
            arrynumer[$i]=$temp
          fi
      done
      echo ${arrynumer[$i]}
    done