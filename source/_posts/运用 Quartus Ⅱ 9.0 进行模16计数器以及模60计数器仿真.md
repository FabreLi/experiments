---
title: 运用 Quartus Ⅱ 9.0 进行模16计数器以及模60计数器仿真
date: 2017-12-01
tags:
    - 实验
    - EDA
cdn: header-off
header-img: https://i.loli.net/2017/10/23/59edea69588f9.jpg
---

# 实验目的

利用VHDL语言设计加法计数器的逻辑功能，通过仿真，进一步了解计数器的特性和功能，并掌握 Quartus Ⅱ 软件的使用。

# 实验内容
1.设计模16计数器；计数器有时钟输入端、使能端、异步清零端、输出端；仿真验证设计结果。
2.提高部分：设计模60计数器；计数器有时钟输入端、使能端、异步清零端、输出端；仿真验证设计结果。

## 模16计数器

```VHDL
-- Quartus II VHDL Template
-- Binary Counter

library ieee;
use ieee.std_logic_1164.all;
use ieee.std_logic_unsigned.all;

entity COUNTER16 is
	port
	(clk,clr,en:in std_logic;
	q1,q2,q3,q4:out std_logic);
end COUNTER16;		--注明输入端和输出端

architecture one of COUNTER16 is
	signal data:std_logic_vector (3 downto 0);		--模16只需4位(2^4=16)，估为3~0
begin
	q1<=data(0);
	q2<=data(1);
	q3<=data(2);
	q4<=data(3);
	process (clk,clr)
	begin
		if (en='1') then		--“en”为使能端，仅当en为高电平1时计数器工作
			if (clr='0') then		--“clr”为异步清零端，当clr为0时计数器清零
				data<="0000";
			elsif (clk'event and clk='1') then		--“clk”为时钟信号，含义为“仅当时钟信号发生变化且变成高电平1，即上升沿时”
				if (data="1111") then
					data<="0000";
				else
					data<=data+'1';	
				end if;
			end if;
		end if;
	end process;
end one;
```

仿真结果：
![模16仿真结果.png](https://i.loli.net/2017/12/01/5a213908ae49f.png)

## 模60计数器

模60计数器与模16计数器原理上并无区别，代码格式无区别，仅需要更改其中参数即可。

### 输出端

由于 2^5=32<60,2^6=64>60, 模60计数器的需要六个输出端口。

```VHDL
entity COUNTER60 is
	port
	(clk,clr,en:in std_logic;
	q1,q2,q3,q4,q5,q6:out std_logic);
end COUNTER60;
```
### 计数器终点限制

由于 2^6=64>60，故该计数器的终点不可为 “111111”（63） ，我们需要对其进行限制，终点为 “111011”（59），此时计数器的范围为 “0~59”，模为 60。

```VHDL
process (clk,clr)
	begin
		if (en='1') then
			if (clr='0') then
				data<="000000";
			elsif (clk'event and clk='1') then
				if (data="111011") then
					data<="000000";		--限制计数器的终点为 “111011”（59）
				else
					data<=data+'1';
				end if;
			end if;
		end if;
end process;
```

仿真结果：
![模60仿真结果.png](https://i.loli.net/2017/12/01/5a213908cf15a.png)

# 操作注意事项

## 输出端没有结果

![输出](https://i.loli.net/2017/12/01/5a213ee1cc3ae.png)

如果已经完成了仿真（已经 Start Simulation 并成功结束）但输出端仍然没有出现输出结果，这是vwf文件没有关联导致的。

点开 Assignment-Setting-Simulation Setting，将『Simulation Input』一项关联到此vwf文件。

![Setting](https://i.loli.net/2017/12/01/5a213ee15289b.png)

## 输出端的改变不跟随时钟信号上升沿

![输出](https://i.loli.net/2017/12/01/5a213ee19e249.png)

在程序没有问题的情况下，若输出端的改变不跟随时钟信号CLK上升沿而改变，很有可能是由于设置为时序仿真模型导致的，此时仿真的结果有延迟，输出端的变化稍晚于时钟信号CLK的上升沿，应将其换回功能仿真模型。

点开 Assignment-Setting-Simulation Setting，在『Simulation mode』里面选择『Fuctional』

![注意3.png](https://i.loli.net/2017/12/01/5a213ee184700.png)

还有注意的是，当选择了『Functional』模式的时候，直接点开『Start Simulation』会导致事先没有『Generate Functional Simulation Netlist』而出错，故应该先点击 Progress-Generate Functional Simulation Netlist 再 Start Simulation。

![注意5.png](https://i.loli.net/2017/12/01/5a213ee13c425.png)