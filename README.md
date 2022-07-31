#写在之前
项目中用到的函数简介：https://github.com/EveandBob/Introduction-to-some-functions-in-elliptic-curves-not-projects-

# 项目名称
ECMH

# 项目实现
ECMH整体思路是：先把集合里的元素映射成椭圆曲线上的点，然后利用椭圆曲线上的加法求解哈希值。

为达到相同的安全性，ECMH算法需要的密钥长度远远小于哈希求和算法，因而ECMH相较哈希求和算法更为安全。

将信息hash到曲线上的方法为：
![Screenshot 2022-07-31 131217](https://user-images.githubusercontent.com/104854836/182011157-5b2dc1ae-d757-405c-8705-5ca619ce0372.jpg)
1.首先先对消息进行hash

2.开始遍历从零到正无穷，并补充在hash之前得到hash2，对hash2做hash

3.令得到的hash值模p赋值给x

4.根据求二次剩余的算法得到y，如果有y就跳转到5，如果没有y就到2

5.由于二次剩余得到的y有两个，那么这样选择两个y中的一个，根据消息的编码，如果最后以个数字为0，选小一点的y，如果为1，选大一点的y

# 部分代码
```python
def hesh_msg_to_EC_point(msg):
    if type(msg)==type("str"):
        num=msg.encode()
    else:
        num=int_to_bytes(msg)
    temp = sm3.sm3_hash(list(num))
    x = int(temp, 16)
    num_i=bytes_to_int(num)
    x_bytes=int_to_bytes(x)
    i=0
    while True:
        temp=int_to_bytes(i)+x_bytes
        temp=sm3.sm3_hash(list(temp))
        x=int(temp,16)%p
        y1,y2=get_Qr(x,p)
        if y1 != 0:
            if num_i%2==0:
                y=y1
            else :
                y=y2
            return x,y
        else:
            i+=1
```

# 实验结果
我对（"123"）和（"456"）进行hash到曲线得到G1，G2，然后相加得到G3
![Screenshot 2022-07-31 131844](https://user-images.githubusercontent.com/104854836/182011359-2ff80f90-9c18-4a8a-af44-704a845e8d93.jpg)
