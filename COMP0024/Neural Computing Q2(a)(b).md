# COMP0024 Neural Computing 公式总结

## 1. 单个 Neuron 的 Activation

$$
a=\sum_{i=1}^{n}w_ix_i-s
$$

其中：

- $x_i$：第 $i$ 个输入
- $w_i$：对应权重
- $s$：threshold
- $a$：activation / net input

---

## 2. Linear Neuron 输出公式

$$
y=a
$$

---

## 3. McCullough-Pitts Neuron 输出公式

$$
y=\theta(a)
$$

$$
\theta(a)=
\begin{cases}
1, & a>0\\
0, & a\leq 0
\end{cases}
$$

---

## 4. Sigmoid Firing Function

$$
y=\sigma(a)=\frac{1}{1+e^{-a}}
$$

---

# Hopfield Network

## 5. Binary-to-Bipolar 转换

$$
z_i^{(p)}=2x_i^{(p)}-1
$$

$$
0\mapsto -1
$$

$$
1\mapsto +1
$$

---

## 6. Hopfield Weight Setting Rule

$$
w_{ij}=\sum_{p=1}^{P}z_i^{(p)}z_j^{(p)}
$$

等价写法：

$$
w_{ij}=\sum_{p=1}^{P}(2x_i^{(p)}-1)(2x_j^{(p)}-1)
$$

$$
w_{ii}=0
$$

---

## 7. Hopfield Weight Symmetry

$$
w_{ij}=w_{ji}
$$

---

## 8. Hopfield Threshold Setting Rule

$$
s_i=\frac{1}{2}\sum_{j\neq i}w_{ij}
$$

---

## 9. Hopfield Energy Function

$$
H(x)=-\sum_{i<j}w_{ij}x_ix_j+\sum_i s_ix_i
$$

三节点展开形式：

$$
H(x)=-w_{12}x_1x_2-w_{13}x_1x_3-w_{23}x_2x_3+s_1x_1+s_2x_2+s_3x_3
$$

---

## 10. Hopfield Update Rule

$$
x_i'=
\begin{cases}
1, & \sum_{j\neq i}w_{ij}x_j>s_i\\
0, & \sum_{j\neq i}w_{ij}x_j\leq s_i
\end{cases}
$$

随机异步更新时：

$$
P(\text{update }x_i)=\frac{1}{n}
$$

三节点时：

$$
P(\text{update }x_i)=\frac{1}{3}
$$

---

## 11. 单个 Neuron 的局部输入

更新第 $i$ 个 neuron 时：

$$
h_i=\sum_{j\neq i}w_{ij}x_j
$$

因此 update rule 也可写为：

$$
x_i'=
\begin{cases}
1, & h_i>s_i\\
0, & h_i\leq s_i
\end{cases}
$$

---

## 12. Energy Level 计算

对每个 state $x$：

$$
H(x)=-\sum_{i<j}w_{ij}x_ix_j+\sum_i s_ix_i
$$

---

## 13. Stable State / Fixed Point

若某状态 $x$ 满足所有 neuron 更新后都不改变，则：

$$
x'=x
$$

或：

$$
P(x\to x)=1
$$

---

## 14. Absorbing Transition

若一个状态是 absorbing state：

$$
x\to x,\quad P=1
$$

---

## 15. CAM 相关表达

CAM 的核心思想：

$$
\text{noisy/partial input}\rightarrow \text{stable stored pattern}
$$

或：

$$
x^{(0)}\rightarrow x^{*}
$$

其中：

$$
x^{*}=F(x^{*})
$$