---
title: 基于Matlab画出电偶极子电场与电位的空间分布
date: 2017-12-01
tags:
	- 大作业
	- 电磁场
cdn: header-off
header-img: https://i.loli.net/2017/10/23/59edea69588f9.jpg
---
# 公式推导

电偶极子（electric dipole）是两个等量异号[点电荷](https://baike.baidu.com/item/%E7%82%B9%E7%94%B5%E8%8D%B7)组成的系统。电偶极子的特征用电偶极矩 $p=q*l$ 描述，其中 $l$ 是两点电荷之间的距离，$l$ 和 $p$ 的方向规定由 $－q$ 指向 $q$ 。

![timg](https://i.loli.net/2017/12/01/5a20f4aa4bdf7.gif)

如上图所示，此电偶极子在场点 P 处产生的电位等于两个点电荷在该点的电位之和，即$$\phi =\frac{q}{4\pi\epsilon\_{0}r\_{+}}-\frac{q}{4\pi\epsilon\_{0}r\_{-}}$$

由性质可得，电场强度的表达式为：$$E=-\nabla V$$

# 程序编制

```matlab
q=2e-6;k=9e9;
a=0;b=2;
x=-6:0.3:6;y=x;
[X,Y]=meshgrid(x,y);     % 设置基本参数和坐标网点 k 为 1/(4*pi*ε)

rp=sqrt((X).^2+(Y-b).^2);
rm=sqrt((X).^2+(Y+b).^2);
V=q*k*(1./rp-1./rm); 	 % 计算电势 

[Ex,Ey]=gradient(-V);    % 计算场强  gradient(-V)函数求的是（-V）矩阵中数值上的梯度，即场强方向
AE=sqrt(Ex.^2+Ey.^2);    % 场强的模
Ex=Ex./AE;Ey=Ey./AE;     % 场强归一化，使箭头等长

figure(1)                % 开始画图
contourf(X,Y,V,cv,'k-'); % 用黑实线画填色等位线图
title('电偶极子的场 ');
hold on;
quiver(X,Y,Ex,Ey,0.5); 
plot(a,b,'wo',a,b,'w+'); % 用白线画正电荷位置 
plot(-a,-b,'wo',-a,-b,'w-'); % 用白线画负电荷位置 
hold off;
```

# 最终图形

![untitled](https://i.loli.net/2017/12/01/5a20f4f44729d.jpg)