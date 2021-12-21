# Week1 Basic Neuronal Models

## Part1 Introduction

## Part2 Membrane potential and Nernst Equation

* Membrane
  * ion channels ($Na^+, K^+, Cl^-, Ca^{2+}$):
    * 主要类别：
      * active ion pump: 耗能
      * Passive ion channel: 到达条件（电压门控，离子门控、压力等）后打开
        * 影响因素：内外离子浓度差(concentration gradients)，内外膜电位差
    * 主要作用：维持静息态膜电位（内负外正）
* Diffusion：当离子通道打开，离子会通过通道
  * 离子的动能$E \propto k_BT$
  * 离子的电势能$ {-}zqV_m$
  * 比较动能和电势能的大小，可以确定合适离子是否可以通过通道

* **Boltzmann distribution**
  * 微观尺度下，离子运动达到热平衡下，每个离子的动能都有所不同，这一群离子的动能满足的分布就是boltzmann distribution $P(E) = A\exp(-E/k_BT)$
  * 则能通过通道的离子比例为$\int_{-zqV_m}^\infty P(E) {\rm d}E = \exp(\frac{zqVm}{k_BT})$
* **Nernst Equation**
  * 在平衡状态，可以找到一个特定的膜电势使得特定内外离子浓度保持平衡：$[ion]_{out} = [ion]_{in}\exp(\frac{zqVm}{k_BT}) = [ion]_{in}\exp(\frac{zFVe}{RT})$
  * 整理上式可得Nernst Equation $V_e = \frac{RT}{zF}\ln\left( \frac{[ion]_{out}}{[ion]_{in}} \right)$

## Part3 Goldman Equation and Leaky Integrate-and-Fire(LIF) Model

* Goldman Equation：当有多个离子时，对Nernst Equation的修正

  * $V_e = \frac{RT}{zF} \ln \left( \frac{p_{K^+}[K^+]_{out} + p_{Na^+}[Na^+]_{out} + p_{Cl^-}[Cl^-]_{out}}{p_{K^+}[K^+]_{in} + p_{Na^+}[Na^+]_{in} + p_{Cl^+}[Cl^-]_{in}} \right)$
  * $p_{[ion]}$: 通透率，表示离子通道打开的程度

* LIF Model

  * Ohm law: $V = IR \longrightarrow I = V/R \longrightarrow I = gV$

    * $g$ is the conductance, 在计算神经科学中常用

  * 特定通道引起的电流$I_{ion} = g_{ion}(V - E_k)$

    * capacitor $C = \frac{Q}{V} \rightarrow CV=Q \rightarrow C \frac{{\rm d}V}{{\rm d}t} = I$

  * LIF Model

    * $C\frac{{\rm d}V}{{\rm d}t} = -\sum g_i(V-E_i) + I$

      * $I$ 是其他电流，外加电流，或者上游传导到的电流
      * $E_i$是某个离子的平衡电位

    * Let $g_{tot} = \sum g_i$, 当没有外加电流时（去掉$I$），$\sum g_iE_i/\sum g_i$近似于静息态膜电位$V_{rest}$

      * $g_{tot}$ or $g_{L}$ : leaky conductance

    * 如果某个离子的conductance $g_i$特别大，则$V_{rest} \approx E_i$
      $$
      C\frac{{\rm d}V}{{\rm d}t} = -g_{L}(V - V_{rest}) + I \approx -g_{L}(V - E_i) + I
      $$

      * 这是一个更常用的形式

    * 简单推导 $C\frac{{\rm d}V}{{\rm d}t} = -g_{L}(V - V_{rest}) + I = -g_L(V - (V_{rest} + \frac{I}{g_L}))$

      * **外加电流就像是改变了静息电位，给不同的电流就会抵达新的平衡态**

    * 在所有能产生spike的神经元模型中，LIF是最简单的

* 为什么要维持离子浓度？
  * 想象为什么要有水塔：反应迅速，水压稳定，比较鲁棒
  * 所以神经细胞反应非常迅速，尽管非常耗能

* LIF model 分析

  * 当没有外加电流时$C\frac{{\rm d}V}{{\rm d}t} = -g_{L}(V - V_{rest})$

    * $ V \sim \frac{{\rm d}V}{{\rm d}t}$的关系可以看出

      * 外部扰动使得$V < V_{rest} \Rightarrow \frac{{\rm d}V}{{\rm d}t}>0 \Rightarrow V$增加到$V_{rest}$
      * 外部扰动使得$V > V_{rest} \Rightarrow \frac{{\rm d}V}{{\rm d}t}<0 \Rightarrow V$减少到$V_{rest}$
      * 外部的轻微扰动可以使膜电位重新回到平衡态，所以说静息态的膜电位是**稳定的**

    * 当偏离时，重新回到平衡态的速度是怎样的？

      * Suppose $V(t=0) = V_0$ to $V(t=t') = V'$
        $$
        \begin{aligned}
        \Rightarrow & \frac{{\rm d}V}{(V - V_{rest})} = -\frac{g_L}{C}{\rm d}t \\
        \Rightarrow & \int_V^{V'} \frac{{\rm d}V}{(V - V_{rest})} = \int_0^{t'} -\frac{g_L}{C}{\rm d}t \\
        \Rightarrow & \ln(V-V_{rest})\mid_{V_0}^{V'} = -\frac{g_L}{C} \mid_{0}^{t'} \\
        \Rightarrow & \ln\frac{V' - V_{rest}}{V_0 - V{rest}} = -\frac{g_L}{C}t' \\
        \Rightarrow & V' = V_{rest} + (V_0-V_{rest})\exp(-\frac{g_L}{C}t') \\
        \Rightarrow & V(t) = V_{rest} + (V_0-V_{rest})\exp(-\frac{t}{\tau_m})\ \ \ (\tau_m =  \frac{g_L}{C})
        \end{aligned}
        $$

        * $\tau_m$被称为**时间常数**（time constant），表示回复到静息电位的速度
        * 当$t \rightarrow \infty$时，$V(t) \rightarrow V_{rest}$，这是一个指数衰减

  * 当有外加电流时$C\frac{{\rm d}V}{{\rm d}t} = -g_{L}(V - V_{rest}) + I = -g_L(V - (V_{rest} + \frac{I}{g_L}))$, $V_{rest}' = (V_{rest} + \frac{I}{g_L})$

    * $ V \sim \frac{{\rm d}V}{{\rm d}t}$的关系可以看出，此时静息态仍然是稳定的
    * 不同外加电流的影响是什么？
      * $V(t) = V_{rest}' + (V_0 - V_{rest}')\exp(-\frac{t}{\tau_m}) = (V_{rest} + \frac{I}{g_L}) + (V_0 - (V_{rest} + \frac{I}{g_L}))\exp(-\frac{t}{\tau_m})$
        * 弱的外加电流，$V_{rest}' < Thr$
        * 弱的外加电流，$V_{rest}' > Thr$
      * 在实际模拟的时候，我们需要人为设置阈值的大小，并且设置reset potential以及refractory period(1~2 ms)，这些并不能在模型中体现。

  * 缺点：

    * 并没有实际产生spike，只是人为设定一个高的值

* Firing rate

  * **膜电位**对该神经元以外的神经元是没有意义的，更关注**动作电位**

  * 当由恒定电流注入时
    $$
    \begin{aligned}
     & \begin{cases}
    \Delta V = V_{thr} - V_{reset} \\ 
    V(t) =  (V_{rest} + \frac{I}{g_L}) + (V_0 - (V_{rest} + \frac{I}{g_L}))\exp(-\frac{t}{\tau_m})
    \end{cases} \\
    \Rightarrow & V_{thr} = (V_{rest} + \frac{I}{g_L}) + (V_0 - (V_{rest} + \frac{I}{g_L}))\exp(-\frac{t_{isi}}{\tau_m}) \\
    
    \Rightarrow &r_{isi} = \frac{1}{t_{isi}} = \left( \tau_m\ln(\frac{V_{rest} + \frac{I}{g_L} - V_{reset} }{V_{rest} + \frac{I}{g_L} - V_{thr}}) \right)^{-1}
    
    \end{aligned}
    $$
    Let $Z = \frac{V_{thr} - V_{reset}}{V_{rest}' - V_{thr}}$, then$r_{isi} = (\tau_m\ln(1+Z))^{-1}$

    当外加电流比较大时，$Z$可以充分小，由$\ln(1+z) \approx z$则有
    $$
    r_{isi} \approx (\tau_mZ)^{-1} = \frac{V_{rest}' - V_{thr}}{\tau_m(V_{thr} - V_{reset})}
    $$

    * $t_{isi}$表示相邻两个spike之间的时间间隔
    * $r_{isi}$即放电频率，当电流比较大时与$V_{rest}'$成正比

  * 现在考虑refractory period
    $$
    r = \frac{1}{t_{isi} + t_{refrac}} = \frac{V_{rest}' + V_{thr}}{\tau_m(V_{thr} - V_{reset}) + (V_{rest}' - V_{thr})t_{refrac}}
    $$
    当施加大的外加电流时$V_{rest}'$较大，则有$r \approx \frac{1}{t_{refrac}}$

    * 从生物角度考虑，神经元的放电频率不会无限大，
    * 从LIF model来解释可以看到考虑refractory period之后，放电频率会逐渐收敛到$\frac{1}{t_{refrac}}$
      * 一般refractory period为1~2 ms，从测量上得到的

  * Variant of IaF model

    * no leaky term: $C\frac{{\rm d}V}{{\rm d}t} = I $
    * Leaky IaF: $C\frac{{\rm d}V}{{\rm d}t} = -g_{L}(V - V_{rest}) + I $

  * 思考：

    * **spike的分布是怎样的？**
      * 所以firing rate对神经活动的描述非常粗略，对具体问题，还需要考虑其具体空间分布！！！

  

------

<center><h3>The Next: How to model Synaptic input?





# Week2 Synapse and channel dynamics

* delta currents for synaptic input: $I = g_s \delta(t-t')$
  * 太过简化，不够精确

## Part 6 Basics

*   突出主要种类：
    *   Electronic synapse: 又称gap junction，两边的神经元通道是接触的
        *   当前后神经元有点位差时，电流会直接沿离子通道流过，就产生信号，不一定需要动作电位产生
    *   Chemical synapse：之间有间隙(synaptic cleft)
        *   必须要动作电位产生，才会有囊泡（vesicle）释放神经递质，依靠扩散传递信号
        *   作用是有时间性的，如果短期有大量的动作点位产生，可能造成神经递质释放完，来不及产生新的
        *   通道主要种类：
            *   Ligand-gated ion channels (配体门控通道): 神经递质结合引发构想改变
            *   速度快，不耗能
            *   G-protein-coupled receptors(G蛋白偶联受体):
                *   速度慢（10~20倍），耗能
*   与动作电位产生有关的离子通道：
    *   Persistent channel
        *   当发生构象改变后，机会一直打开
    *   transient channel
        *   相比于persistent channel，在膜内多了一个inactivation gate
        *   Deactivated -> activated -> inactivated，所以不会打开太长时间



## Part 7 Gating variable

*   描述膜电位的微分方程$C\frac{{\rm d}V}{{\rm d}t} = -g_{L}(V - V_{rest}) + I$

    *   本节主要关注$I = I_{syn}$的情况，

        *   由于突出上也是一种通道，所以形式上与描述膜电位类似，即$I_{s} = -g_s(V-V_s)$

        *   另一方面，通道有打开与不打开的情况，所以$g_s = \overline{g}_s* p$，$p$是通道打开的比例，$\overline{g}_s$是所有通道都打开时的最大电导率，所以$I_s = -\overline{g}_sp(V-V_s)$
            $$
            C\frac{{\rm d}V}{{\rm d}t} = -g_{L}(V - V_{rest}) -\overline{g}_sp(V-V_s)
            $$

*   如何描述$p$随时间的变化

    *   p受两方面因素的影响：1）突触前神经元释放神经递质的概率$p_{rel}$；2）离子通道激活的概率$p_s$（群体的概念，单独看一个通道是二元的 ，很多的通道一起，形成在0~1连续的$p_s$）
        $$
        p = p_s \times p_{rel}
        $$

    *   下面看$p_s$的变化
        $$
        \begin{matrix}
        \frac{{\rm d}p_s}{{\rm d}t} = \alpha_s(1-p_s)-\beta p_s & or& \frac{{\rm d}p_s}{{\rm d}t} = \frac{1-p_s}{\tau_{\alpha_s}} - \frac{p_s}{\tau_{\beta_s}}
        \end{matrix}
        $$

        *   $\alpha_s$: activation rate, 关闭的通道有概率打开
        *   $\beta_s$: de-activation rate, 打开的通道有概率关闭

    *   当有神经递质释放时(假设是$T$时间内一个方波)，$\alpha_s$会急剧上升，则有$\frac{{\rm d}p_s}{{\rm d}t} \approx \alpha_s(1-p_s)$
        $$
        p_s(t) = 1 + (p_s(0)-1)\exp(-\frac{t}{\tau_{\alpha_s}})\ \ \ \ (0 \leq t \leq T)
        $$

        *   $\tau_{\alpha_s}$: 越小，代表上升的越快，e.g 1 ms

    *   当没有神经递质时，此时$\alpha_s \approx 0$, 则有$\frac{{\rm d}p_s}{{\rm d}t} \approx -\beta_sp_s$
        $$
        p_s(t) = p_s(T)\exp(-\frac{t-T}{\tau_{\beta_s}})\ \ \ (t \geq 0)
        $$

        *   $\tau_{\beta_{s}}$: 衰减时间常数，e.g 6.67 ms

        *   $T$: peak time

    *   次处$p_s$实际上就是一个**gating variable**，在这个模型中是由3个常数控制$\tau_{\alpha_s}, \tau_{\beta_s}, T$

    *   More elegant expression: **two-exponential function**
        $$
        p_s(t) = B(\exp(\frac{t}{-\tau_1}) - \exp(-\frac{t}{\tau_2}))\ \ \ \ (\tau_1 >> \tau_2)
        $$

        *   $B$: 常数，使曲线下面积为1
        *   $\tau_r = \frac{\tau_1\tau_2}{\tau_1 - \tau_2}$: rise time, similar to $\tau_{\alpha_s}$
        *   $\tau_1$: Fall time, similar to $\tau_{\beta_s}$
        *   $\tau_r\ln(\tau_1/\tau_2)$: peak time, similar to $T$
        *   变化的曲线是非常平滑的，不会出现原来模型中出现的在$T$时刻可能的突变，这与实际观察也是相符合的。

    *   Further simplified: **alpha function**
        $$
        p_s(t) = \frac{t}{\tau_s}\exp(1 - \frac{t}{\tau_s})\ \ \ \ (\tau_1 = \tau_2 = \tau_s)
        $$

        *   失去了同时控制rise time和fall time的能力
        *   计算上更方便

*   实际的例子

    | 突出后端的受体     | AMPA                | $GABA_A$       | NDMA                |
    | ------------------ | ------------------- | -------------- | ------------------- |
    | 通透离子           | $Na^{+}, K^+$       | $Cl^-$         | $Ca^{2+}$           |
    | Rise time          | --                  | 0.3 ms         | 1.5 ms              |
    | Fall time          | 5.26 ms             | 5.6 ms         | 152 ms              |
    | 突出前端释放的递质 | Glutamate（兴奋性） | GABA（抑制性） | Glutamate（兴奋性） |
    | reversal potential | 0 mV                | -60 mV         | 0 mV                |

    *   NDMA：电压依赖的门控通道，被$Mg^{2+}$封堵，当膜电位比较高时，会被去除，通道被激活
        $$
        g_{NDMA} = \left(1 + \frac{[Mg^{2+}]}{3.57 {\rm mM}}\exp(-\frac{V}{16.13 {\rm mV}}) \right)^{-1}
        $$

*   Summary:

    *   3种描述方式，不论如何化简，都一定要保留**指数衰减的特性**



## Part 8 Synaptic input

LIF model: $C\frac{{\rm d}V}{{\rm d}t} = -g_{L}(V - V_{rest}) + I$

|                | current-based                                                | conductance-based                                            |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Delta          | $I = \overline{g}_s \delta(t-t')$                            | $I = \overline{g}_s(V-V_s) \delta(t-t')$                     |
| Single exp     | $I = \overline{g}_s \exp(\frac{t-t'}{\tau})$                 | $I = \overline{g}_s(V-V_s) \exp(\frac{t-t'}{\tau})$          |
| Two exp        | $I = \overline{g}_s B(\exp(\frac{t}{-\tau_1}) - \exp(-\frac{t}{\tau_2}))$ | $I = \overline{g}_s(V-V_s) B(\exp(\frac{t}{-\tau_1}) - \exp(-\frac{t}{\tau_2}))$ |
| Alpha function | $I = \overline{g}_s \frac{t}{\tau_s}\exp(1 - \frac{t}{\tau_s})$ | $I = \overline{g}_s(V-V_s) \frac{t}{\tau_s}\exp(1 - \frac{t}{\tau_s})$ |

*   ways to express the synaptic dynamics

    *   $$
        C\frac{{\rm d}V_i}{{\rm d}t} = -g_{L}(V_i - V_{rest}) + \sum_\limits{j} I_{ij} = -g_{L}(V_i - V_{rest}) + \sum_\limits{j} -g_{ij}^s s_{ij}(V_i - V_s)
        $$

        *   single exponential decay
            $$
            \frac{{\rm d}s_{ij}}{{\rm d}t} = -\frac{s_{ij}}{\tau_{decay}} + \sum_k \delta(t - t_j^k)
            $$

        *   two exponential decay (rise + decay)
            $$
            \begin{cases}
            \frac{{\rm d}s_{ij}}{{\rm d}t} = -\frac{s_{ij}}{\tau_{decay}} + \alpha x_j(t) \\
            \frac{{\rm d}x_{j}(t)}{{\rm d}t} = -\frac{x_{j}(t)}{\tau_{rise}} + \sum_\limits{k} \delta(t - t_j^k)
            
            
            \end{cases}
            $$



## Part 9 Hodgkin-Huxley model (HH model)

*   对动作电位产生以及膜电位控制具有里程碑式的意义

    *   阐述了动作**电位产生的原理**！！！

*   如何描述ion channel?

    *   相关概念：

        *   $n$: 通道蛋白的一个**subunit被打开**的概率，其与膜电位是有关的
        *   $p_k = n^K$: 一个有k个subunit的通道蛋白被打开的概率

    *   如何对$n$进行建模？
        $$
        \frac{{\rm d}n}{{\rm d}t} = \alpha_n(V)(1-n) - \beta_n(V)n
        $$

        *   $\alpha_n(V)$: 在膜电位为$V$的条件下，subunit从关闭到打开的rate
        *   $\beta_n(V)$: 在膜电位为$V$的条件下，subunit从打开到关闭的rate

*   比较前面的synaptic input（用神经递质描述动作电位），HH model描述了膜电位的变化

    *   The basic equation
        $$
        C\frac{{\rm d}V}{{\rm d}t} = -g_{L}(V - V_{rest}) -g_Kn^4(V - V_K) - g_{Na}m^3h(V - V_{Na}) + I \\
        
        \begin{aligned}
        \frac{{\rm d}n}{{\rm d}t} &= \alpha_n(V)(1-n) - \beta_n(V)n &   \frac{{\rm d}n}{{\rm d}t} &= \frac{n_{\infty}(V) - n}{\tau_n}\\
        \frac{{\rm d}m}{{\rm d}t} &= \alpha_m(V)(1-m) - \beta_m(V)m & \frac{{\rm d}m}{{\rm d}t} &= \frac{m_{\infty}(V) - m}{\tau_m}\\
        \frac{{\rm d}h}{{\rm d}t} &= \alpha_h(V)(1-h) - \beta_h(V)h & \frac{{\rm d}h}{{\rm d}t} &= \frac{h_{\infty}(V) - h}{\tau_h}\\
        \end{aligned}
        $$

        *   $K^+$是persistent channel
        *   $Na^+$是transient channel，$m, h$分别描述activation gate和inactivate gate的开关，$m$仍然是4个subunit，但是3次方拟合更好
        *   不仅要看开和关，还要看**开关的速度**
            *   通过速度的不同，transient channel的过程可以被实现

*   解释动作电位的产生

    *   deploarization ($g_{Na^{+}}$增大) -> $g_{k^+}增大$ -> repoloarization

*   正回馈例子

    *   麦克风与音响
    *   化学爆炸



# Week3 Signal propagation in single neurons

## Part 11 Cable equation

* 解释：电信号在电缆中的传递

* 信号如何在树突或轴突中传播？

  * 取出树突或者轴突的一截，可以近似成圆柱状，由于其仍然是膜结构，所以HH model的基本形式仍然保持，但是多了另外的电流项：即从圆柱两侧进出的电流
    $$
    \begin{aligned}
    C\frac{{\rm d}V}{{\rm d}t} &= -g_L(V - V_{rest}) -g_{K^+}n^4(V-V_{K^+}) - g_{{Na}^+}m^3h(V-V_{{Na}^+}) \\
    &= I_{L|left} + I_{L|right} + I
    \end{aligned}
    $$

  * 有文章表明：在轴方向上的刺激的传播是会衰减的，并且幅度很大（树突上）

    * 既然存在$I_L$，则表明在圆柱两端有电位差，即
      $$
      \Delta V = -R_LI_L
      $$

      * 规定向右为正方向，电位差为右减左

    * 圆柱导体的电阻$R_L = \frac{r_L \Delta x}{\pi a^2}$ （基本的电学公式）

      * $r_L$: 单位体积的电阻
      * $\Delta x$: 圆柱的长
      * $a$：圆柱的半径

    * 由此可得
      $$
      I_L = -\frac{\pi a^2 \Delta V}{r_L \Delta x} \Rightarrow 
      
      I_L = -\frac{\pi a^2 }{r_L} \frac{\part V}{\part x}
      $$

      * 由于膜中间是存在电流的，所以理论上$I_{L|left} + I_{L|right}$

  * **Linear cable equation**

    * 令$-g_L(V - V_{rest}) -g_{K^+}n^4(V-V_{K^+}) - g_{{Na}^+}m^3h(V-V_{{Na}^+}) = -g_m\Delta x(V - V_{rest})$ , 其中$g_m$是单位长度膜的电导率，同理对$C$也进行单位化，即$C = 2\pi a \dot\ \Delta xc_m$，其中$c_m$是单位面积膜的电导率，由此可做如下推导

    $$
    \begin{aligned}
    &\Rightarrow &2\pi a \dot\ \Delta xc_m\frac{\part V}{\part t} &= -g_m\Delta x(V - V_{rest}) - (\frac{\pi a^2 }{r_L} \frac{\part V}{\part x})_{left} + (\frac{\pi a^2 }{r_L} \frac{\part V}{\part x})_{right} \\
    &\Rightarrow &c_m \frac{\part V}{\part t} &= -\frac{g_m}{2\pi a}(V - V_{rest}) + \frac{a}{2r_L} \dot\ \frac{1}{\Delta x}\left[ - ( \frac{\part V}{\part x})_{left} + (\frac{\part V}{\part x})_{right} \right] \\
    &\Rightarrow &c_m \frac{\part V}{\part t} &= -\frac{g_m}{2\pi a}(V - V_{rest}) + \frac{a}{2r_L} \frac{\part^2 V}{\part x^2}
    \end{aligned}
    $$

    * 记$r_m = \frac{2\pi a}{g_m}$是单位面积上的电阻，则有
      $$
      \begin{aligned}
      c_m \frac{\part V}{\part t} &=  \frac{a}{2r_L} \frac{\part^2 V}{\part x^2} -\frac{(V - V_{rest})}{r_m} \\
      c_m r_m \frac{\part V}{\part t} &= \frac{ar_m}{2r_L} \frac{\part^2 V}{\part x^2} -(V - V_{rest})
      \end{aligned}
      $$

    * 令$\tau_m = c_m r_m, \lambda=\sqrt{\frac{ar_m}{2r_L}}$, 则有
      $$
      \tau_m \frac{\part V}{\part t} = \lambda^2 \frac{\part^2 V}{\part x^2} -(V - V_{rest})
      $$
      
      * $\tau_m$: 时间常数
      * $D_u = \frac{\lambda^2}{\tau_m} = \frac{a}{2r_Lc_m}$
      * 比较一维上的diffusion equation $\frac{\part \phi}{\part t} = D\frac{\part^2 \phi}{\part x^2}$, 
        * 非常相似，说明电压信号的在膜上的传播跟扩散现象很相似，
        * 传播速率与$\frac{\lambda^2}{\tau_m}$成正比
        * $V - V_{rest}$是从leaky得来的，说明在传播过程中由于leaky会发生衰减



## Part 12 propagation in dentrites

* 在树突上的传播，信号的损失是很大的，

* 当在树突注入电流时，电压如何变化

  * constant injecting: 长时间($t \rightarrow \infty$)后会达到稳定
    $$
    V(x) = \frac{I_eR_\lambda}{2}\exp(-\frac{|x|}{\lambda})
    $$

    * $R_\lambda = \frac{r_m}{2\pi a\lambda} = \frac{r_L \lambda}{\pi a^2}$
    * 只是一个假设，真是生物上很少有这种情况

  * current pulse: 
    $$
    V(x, t) = \frac{I_eR_\lambda}{\sqrt{4\pi t/\tau_m}} \exp(-\frac{\tau_mx^2}{4\lambda^2t})\exp(-\frac{t}{\tau_m})
    $$

    * 任意固定时间，沿$x$方向，$\exp(-\frac{\tau_mx^2}{4\lambda^2t})$实际上很接近高斯分布; 另一方面，这个峰的宽度也随着时间变大
    * $\exp(-\frac{t}{\tau_m})$这一项表明，高度随着时间指数衰减，
    * 上述两项的叠加，就会使信号衰减得更快
    * 对某个局部区域，观察到得实际上是一个wave，那么在某个某个位置，什么时刻会有peak
      * 令$\frac{\part V(x)}{\part t} = 0$， 则有$t = \frac{\tau_m}{4} (\sqrt{1+4(x/\lambda)^2}-1) \approx \frac{\tau_m}{4} \dot\ \frac{2x}{\lambda} = x\tau_m/2\lambda$
        * 说明在距离远得地方peak到的时间基本与距离成正比

* Compartment model

  * 可以用cable equation去模拟每个树突，但是偏微分方程在计算上存在一些问题

  * 可以分成一段一段的，每一段上都可以用LIF近似，但是存在相邻的compartment有电流的注入，所以
    $$
    c_m\frac{{\rm d}V}{{\rm d}t} = -g_L(V - V_{rest}) + g_{i,i+1}(V_{i+1} - V_i) + g_{i, i-1}(V_{i-1} - V_i)
    $$

    * 对于branch的情况，多添加几个compartment即可

      

## Part 13 propagation in axon

* 负责把动作电位传递下去
  * 轴突上有大量的$Na^+, K^+$离子泵
  * 动作电位传到轴突起始位置时，$Na^+$离子通道打开，大量$Na^+$进入
  * $Na+$会顺着axon两侧移动，动作电位传导到下游
  * 下游$Na^+$离子通道也打开，
  * 而原本位置电位升高，使$K^+$通道打开，外流$K^+$使电位急速下降，并进入**静息状态**
    * 静息状态保证了不会动作电位不会回传，但是$Na^+$会有回流，具有一定生理意义
  * 上述动作不断重复，信号得到传递
* 动作电位传播速率
  * 通过之前的cable model可知传播速度$v \propto \frac{x}{t_{max}} \propto \frac{\lambda}{\tau_m}\propto \sqrt{\frac{a}{2c_m^2r_Lr_m}} $, 
    * 这是一个非常慢的速度
  * 如何加速这个过程？ ———— **髓鞘**
    * 髓鞘就像一个绝缘体，但不是从头到尾，而是隔一段就会露出来，这可以产生动作电位
    * 好处：
      * 加快信号传递的速度
      * 保障信号的传播

* 定量描述髓鞘的优点

  * 髓鞘相当于一层厚厚的绝缘体，这就会影响到之前提到的cable equation

    * 电容部分

      * 非常薄的一层电容为$c_i = c_m2\pi aL \dot\ (d_m/\Delta a)$

        * $d_m$: 一层细胞的厚度
        * $c_m$: 单位面积的电容

      * 多层相当于电容的串联，则有：
        $$
        \begin{aligned}
        \frac{1}{C_m'} &= \frac{1}{c_1} + \frac{1}{c_2} + \frac{1}{c_3} + \dots \\
        &= \frac{1}{c_m2\pi Ld_m} \frac{\Delta a}{a_1} + \frac{1}{c_m2\pi Ld_m} \frac{\Delta a}{a_2} + \frac{1}{c_m2\pi Ld_m} \frac{\Delta a}{a_3} + \dots \\
        &= \frac{1}{c_m2\pi Ld_m} \int_{a_1}^{a_2} \frac{1}{a} {\rm d}a \\
        &= \frac{1}{c_m2\pi Ld_m} \ln (\frac{a_2}{a_1})
        \end{aligned}
        $$

    * cable equation转化为整个clinder的电容， 且$r_m \rightarrow \infty$
      $$
      \begin{aligned}
      & & c_m \frac{\part V}{\part t} &=  \frac{a_1}{2r_L} \frac{\part^2 V}{\part x^2} -\frac{(V - V_{rest})}{r_m} \\
      &\Rightarrow & 2\pi a_1 \dot\ c_m \frac{\part V}{\part t} &=  \frac{\pi a_1^2}{r_L} \frac{\part^2 V}{\part x^2} -\frac{2\pi a}{r_m}(V - V_{rest}) \\
      &\Rightarrow & \frac{C_m}{L} \frac{\part V}{\part t} &= \frac{\pi a_1^2}{r_L} \frac{\part^2 V}{\part x^2}
      
      \end{aligned}
      $$

    * 带入$C_m$得
      $$
      \frac{\part V}{\part t} = \frac{a_1^2 \ln(a_2/a_1)}{2c_mr_Ld_m} \frac{\part^2 V}{\part x^2} \\
      \Rightarrow \ \ \ \ \ \frac{\part V}{\part t} = D_m\frac{\part^2 V}{\part x^2}
      $$

      * $D_m = \frac{a_1^2 \ln(a_2/a_1)}{2c_mr_Ld_m}$, 扩散常数

    * 与没有髓鞘时得cable equation比较
      $$
      \frac{D_m}{D_u} = \frac{\frac{a_1^2 \ln(a_2/a_1)}{2c_mr_Ld_m}}{\frac{a_1}{2r_Lc_m}} = \frac{a_1\ln k}{d_m}
      $$

      * 一般来讲，有髓鞘传播速度会更快

  * 合适能有最快得速率

    * 假定$a_2$的厚度是固定的，调整$\frac{a_2}{a_1}$来使$D_m$达到最大
      $$
      \begin{aligned}
      
      \frac{\part D_m}{\part a_1} &= 0 \\
      \frac{1}{c_m2\pi Ld_m}(2a_1\ln(a_1/a_2) - a_1^2\frac{1}{a_2/a_1}\frac{a_2}{a_1^2} ) &= 0 \\
      \ln(a_2/a_1) &= 1/2 \\
      a_1/a_2 &= 0.6
      
      \end{aligned}
      $$

    * 0.6也是在大多数神经细胞观察到的



## Summary

* 动作电位每一点上重新产生，是一个主动传递过程。与波动的传递过程不同，其是被动的，会有能量的耗散
* 鞭炮的引信 与 神经信号的传递很类似



# Week 4 Neural network simulatiors (python nest package)

## Part 14 Neural network simulatiors

* Neural network models: Neuron model + synapse model

  * Neuron model: e.g LIF model
    $$
    C \frac{{\rm d} V_i}{{\rm d}t} = -g_L(V_i - V_L) + I_{syn}
    $$

  * Synapase model: e.g. single exponential, conductance based model
    $$
    \begin{aligned}
    I_{syn} &= \sum_\limits{j} \overline{g}_s(V_i - V_s)S_{ij}(t) \\
    \frac{{\rm d}S_{ij}(t)}{{\rm d}t} &= -S_{ij}/\tau + \sum_\limits{k} \delta(t-t_k)
    
    \end{aligned}
    $$

* 如何设计一个simulator？

  * Typical workflow
    * **Date input**
      * 参数设置
      * 实验设置：模拟时间（10s,20s，达到某个条件），外部刺激
      * 是否需要图形界面
    * Model setup: $C, g_L, V_L$, 神经元个数，等等
    * One simulation step, 对每一个神经元计算 (time step, 非常短，小于1ms)
      * 收集每个neuron的input: 来源于上一个time step
      * **解**synapse equation: 前对后的影响
      * **解**neuron equation: 
      * **更**新状态，发射spike
    * **输出模拟结果**
      * spike train, firing rate等

  * 其他要考虑的方面：
    * 生物的细节：选择适合的细节程度
    * Flexibility

* Data input如何选择
  * 编程语言
  * 参数文件

* 如何做simulation
  * 如何解微分方程



# Week 5 Basics of dynamical systems

## Part 15 Stability

* **稳定性**以及在**不同的稳定态之间转换**是动态系统最重要的内容

* 回到HH model: 4维的动态系统$V, n, m, h$，非常复杂

* 为了学习的目的，只能进行简化：假设研究系统如何发展出动作电位

* 动作电位产生主要是$Na^+$, 并且$h$这一项变换比较慢，可暂时忽略$h$这一项，变成一个二维系统；

  * 与此同时，假设恒定电流，($p$就是$m$)

  $$
  \begin{aligned}
  C\frac{{\rm d}V}{{\rm d}t} &= -g_L(V - V_L) - gp(V-E) + I \\
  \dot p &= (p_\infty(V) - p)/\tau(V)
  
  \end{aligned}
  $$
  ​	假定：$p$的动态非常快，即$\tau(V) \rightarrow 0$, 则$p \rightarrow p_\infty$, 所以
  $$
  C\frac{{\rm d}V}{{\rm d}t} = -g_L(V - E_L) - gp_\infty(V)(V-E_p)
  $$

  * 此时是一个一维的系统

* 四种可能的快速电流

  * Inward: $E_p > E_L$
  * Outward：$E_p < E_L$
  * Activation：通道打开的机率随通道上升
  * Inactivation

  |              | inward       | outward   |
  | ------------ | ------------ | --------- |
  | activation   | $I_{Na, P}$, | $I_K$     |
  | inactivation | $I_h$        | $I_{Kir}$ |

  * 负的conductance，放大电流，是一个不稳定的行为
  * 正的conductance，下降电流，是一个稳定行为

* 具体分析

  * **inward activation** persistent $Na$ current
    $$
    C\frac{{\rm d}V}{{\rm d}t} = -g_L(V - E_L) - g_{Na^+}m_\infty(V)(V-E_p) + I \\
    m_\infty(V) = \frac{1}{1+\exp((V_{1/2} - V)/k)}
    $$

    * Leaky term整个就是一条直线（恒定电流）
    * Bistability and monostability
      * $\dot V = 0$可以得到3个交点：两个稳定点，一个不稳定点（位于两个点之间）
        * 解释：这个不稳定点相当于一个threshold，在这个地点之下，系统会逼近这个点之下的平衡态，超过这个值，系统会逼近这个点之上的值
        * 关于reset的说明：由于此处只考虑了$Na^+$的情况
        * 可以解释动作电位上升的部分
      * 系统有两个平衡状态（静息态和激活态），两个稳定态之间的转移需要外加电流，否则只能维持在稳定态

  * **phase portraits** ($\dot V = F(V)$) and topological equivalence

    * 区别shape和topological 等价的区别

  * Geometrical analysis

    * 在一维系统中，两个稳定的状态之间必有一个不稳定状态

  * 有外加current的情况时，会有一些很有趣的现象

    * 负的电流，$F(V)$整体下移，可能变成只有一个平衡点
    * bistability：
    * hysteresis：迟滞现象，回忆磁化现象



## Part 16 Bifurcation

* 如何分析一个复杂系统？
* 基本原则：
  * 找出$\dot V = 0$的解 （fix points）
  * **Hartman-Grobman theorem** (local equivalence): 看平衡点附近的状态就可以知道这个点的性质
    * 是一个线性化理论，即$\dot V = \lambda(V - V_{eq}) \Rightarrow V(t) = V_{eq} + (V_0 - V_{eq})\exp(\lambda t)$
      * $\lambda < 0$: $V_0 \rightarrow V_{eq}$，说明是一个稳定系统
      * $\lambda > 0$: 发散，没有平衡点
      * $\lambda = 0$: **tangent point (bifurcation)**

* saddle-node bifurcation (fold bifurcation)
  * 如何找到这个点？：
    * $\dot V = F(V, I)$有平衡点$(V_{sn}, I_{sn})$, 系统发生saddle-node bifurcation等价于以下三个条件同时满足：
      * Non-hyperbolicity: $\frac{d}{dV}F(V, I_{sn}) \mid_{V_{sn}} = 0$
      * Non-degeneracy: $\frac{d^2}{dV^2}F(V, I_{sn}) \mid_{V_{sn}} \not= 0$
      * Transersality: $\frac{d}{dV}F(V, I_{sn}) \mid_{V_{sn}} \not= 0$
  * slow transition (ghost of the ruined attractor == 稳定平衡点)
    * 系统上当有bifurcation发生时，变化的非常慢
    * **bifurcation diagram**：$V \sim I$
      * $I$相当于是外加的参数
    * 神经元的动态系统会发生两个bifurcation

* Quadratic LIF model $\dot V = I + V^2$, 如果$V > V_{peak}$，则$V \leftarrow V_{reset}$
  * 会产生一个真正的动作电位



# Week 6 Firing rate model and network

## Part 18 Firing rate model, binary model and small networks

* 描述overall activity，而不是单个spikes
  $$
  \tau_r \frac{{\rm d}r}{{\rm d}t} = -r + F(h)
  $$

  * $r$ is firing rate
  * $\tau_r$: 时间常数
  * $F(h)$是外加输入：$h$是来自上游神经元的输入，即真正的输入
    * 可能的形式$ReLU$等
  * 简单性质
    * $F(h) = 0 \Rightarrow r \rightarrow 0$
    * $F(h) \not=0 \Rightarrow r \rightarrow F(h)$
  * 这种模型是描述大规模network的重要模型，但也有缺陷

* 考虑一个简单的网络
  $$
  \tau_r \frac{{\rm d}r}{{\rm d}t} = -r + F(h) \\
  h = \vec g \dot\ \vec r
  $$

  * $g = [g_{12}, g_{13}, g_{14}, \cdots]$

* 3个neuron相互连接形成的network
  $$
  \tau_{r1} \frac{{\rm d}r_1}{{\rm d}t} = -r_1 + F(g_{1*} \dot\ r)
  \tau_{r2} \frac{{\rm d}r_2}{{\rm d}t} = -r_2 + F(g_{2*} \dot\ r)
  \tau_{r3} \frac{{\rm d}r_3}{{\rm d}t} = -r_3 + F(g_{3*} \dot\ r)
  $$
  简化形式
  $$
  \frac{{d}\vec r}{{\rm d}t} = - \vec{r} + \vec F(G\vec r)
  $$

* 对于时间非常缓慢的形况（e.g NDMA通道），可以先写出突触电流对于input的反应
  $$
  \tau_{syn}\frac{{\rm d}I_{syn}}{{\rm d}t} = -I_{syn} + \vec{w}\dot\ \vec{u}
  $$

  * Formulation 1 $\tau_s >> \tau_r$
    $$
    v = F(I_s) \\
    $$
    
  * Formulation 2 $\tau_r >> \tau_s$
    $$
    \tau_r \frac{{\rm d}r}{{\rm d}t} = -r + F(I_s(t)) = -r + F(\vec{w}\dot\ \vec{u})
    $$
  
* 主要的两类network

  * feedforward network: 只是单向传播，不考虑回传，早期使用比较多，但是分析比较容易
    $$
    \tau_r \frac{{\rm d}v}{{\rm d}t} = -v + F(W \dot\ u)
    $$

    * 主要是工业上，或者机器学习使用比较多
    * 并且在使用时主要用的时**binary neuron**

  * Recurrent/feedback network
    $$
    \tau_r \frac{{\rm d}v}{{\rm d}t} = -v + F(W \dot\ u + M\dot\ v)
    $$

    * 

## Part 19 Stability in two-dimensional dynamical system

* 2-d system$\frac{{\rm d}\vec{x}}{{\rm d}t} = \vec F(\vec{x})$
  $$
  \frac{{\rm d}x}{{\rm d}t} = f(x, y) \\
  \frac{{\rm d}y}{{\rm d}t} = g(x, y) \\
  $$

  * fix point $F(x_0) = 0$

  * Taylor's expansion
    $$
    \frac{{\rm d}\vec{x}}{{\rm d}t} = F(x) = F(x_0) + DF(x_0)(x-x_0) + o(DF(x_0)(x-x_0)) \approx DF(x_0)(x-x_0)
    $$

    * $D$ is Jacobian matrix
      $$
      \begin{bmatrix}
      \frac{\part f(x_0, y_0)}{\part x} & \frac{\part f(x_0, y_0)}{\part y} \\
      \frac{\part g(x_0, y_0)}{\part x} & \frac{\part g(x_0, y_0)}{\part y}
      
      \end{bmatrix}
      $$

    * 进一步化简 $u = x - x_0, w = y-y_0$
      $$
      \begin{cases}
      \dot u = au + bw \\
      \dot w = cu + dw
      \end{cases} \Rightarrow \dot v = Lv
      $$

    * 记$L$的特征值和特征向量为$\lambda_1, \lambda_2, v_1, v_2$, $v = c_1v_1 + c_2v_2$，则有
      $$
      \dot v = Lv = L(c_1v_1 + c_2v_2) = c_1\lambda_1v_1 + c_2\lambda_2v_2
      $$
      由此可以得到
      $$
      \begin{bmatrix}
      u(t) \\ w(t)
      \end{bmatrix} = c_1\exp(\lambda_1t)v_1 + c_2\exp(\lambda_2t)v_2
      $$



## Part20 Recurrent Network

$$
\tau_r \frac{{\rm d}v}{{\rm d}t} = -v + F(W \dot\ u + M\dot\ v) = -v + h + M\dot\ v = (M-I)v + h
$$

* $M$表示output之间的连接
* 重要假设
  * $F()$是一个线性函数，$h = W\dot\ u$,
  * $M$是对称矩阵（有助于后续分析），记其特征值与特征向量为$ {\lambda}_\mu, e_\mu$
    * 由其对称性可知$e_\mu \dot\ e_v = 0$
    * 任意向量可以写成特征向量的线性组合

* 由上述假设可知$v(t) = \sum c_\mu(t)e_\mu$
  $$
  \begin{aligned}
  \Rightarrow & \tau_r \frac{{\rm d}}{{\rm d}t}(\sum c_\mu(t)e_\mu) = (M-I)(\sum c_\mu(t)e_\mu) + h \\
  \Rightarrow & \tau_r\sum \frac{{\rm d} c_\mu(t)}{{\rm d}t} e_\mu = -\sum(\lambda_\mu-1)c_\mu(t)e_\mu + h
  
  \end{aligned}
  $$

  * 等式两边同时乘以$e_\nu$，且假设输入是一个常数
    $$
    \begin{aligned}
    & \tau \frac{{\rm d} c_\nu(t)}{{\rm d}t} = -(1-\lambda_\nu)c_\nu(t) + e_\nu \dot\ h \\
    \Rightarrow & \int_{c(0)}(\frac{1}{-(1-\lambda_\nu)c_\nu(t) + e_\nu \dot\ h}) {\rm d}c_\nu = \int_{t_0}1/t {\rm d}t \\
    \Rightarrow & \ln \frac{-(1-\lambda_\nu)c_\nu(t) + e_\nu \dot\ h}{-(1-\lambda_\nu)c_\nu(0) + e_\nu \dot\ h} = -\frac{(1-\lambda_\nu)t}{\tau} \\
    \Rightarrow & -(1-\lambda_\nu)c_\nu(t) + e_\nu \dot\ h = \left(-(1-\lambda_\nu)c_\nu(0) + e_\nu \dot\ h \right)\exp(-\frac{(1-\lambda_\nu)t}{\tau})
    
    
    
    \end{aligned}
    $$

    * 解有一个指数衰减

  * 最终解为
    $$
    c_\nu(t) = \frac{e_\nu \dot\ h}{1-\lambda_\nu} \left( 1-\exp(-\frac{(1-\lambda_\nu)t}{\tau}) \right) + c_\nu(0)\exp(-\frac{(1-\lambda_\nu)t}{\tau})
    $$

    * 第一项与输入有关
    * 第二项与起始条件有关

* 动态系统讨论

  * $\lambda_\nu > 1$: 不稳定

  * $\lambda_\nu < 1$: 稳定，最终收敛到$\frac{e_\nu \dot\ h}{1-\lambda_\nu}$, **时间常数**近似与$\tau_r/(1-\lambda_\nu)$

    * ${0 <} \lambda_\nu < 1$: 输入会放大
    * $\lambda_\nu < 0$: 输入会衰减
    * 写成向量的形式则有$v_\infty = \sum\frac{e_\nu \dot\ h}{1-\lambda_\nu} e_\nu$
      * 如果$\lambda_1 \rightarrow 1$, 其他远小于1，则$v_\infty = \frac{e_\nu \dot\ h}{1-\lambda_\nu} e_1$, 会选择放大
      * 实例：神经元只对某一种反应特别强烈，称为tuning

  * $\lambda_1=1$, 其他等于小于1，则需要回到原始的方程式$ \tau \frac{{\rm d} c_\nu(t)}{{\rm d}t} = -(1-\lambda_\nu)c_\nu(t) + e_\nu \dot\ h \\$分析, 此时有
    $$
    \begin{aligned}
    \tau_r \frac{{\rm d} c_\nu(t)}{{\rm d}t} =  e_\nu \dot\ h \\
    \Rightarrow c_1(t) = c_1(0) + \frac{1}{\tau_r}\int_0 e_1 \dot\ ht' {\rm d}t
    
    \end{aligned}
    $$

    * 是一个积分器
    * 生物的例子：眼动，integrator neuron

* 必须清楚做出的假设是什么！！！！！！



# Week 7 Memory and plasticity

## Part 21 Memory

* 主要关注长期记忆，形成包括3个阶段

  * 编码

  * 固化
  * 提取
  * 每一个阶段都有相应的理论去解释

* 固化与提取（associative memory）

  * 假设记忆与神经元活动有关：不同的事件对应不同群的神经激活，但是也可能有一些是重复的

  * Landscape角度：每个波谷都是一个稳定状态

  * 回忆是一个recurrent network
    $$
    \tau_r \frac{{\rm d}v}{{\rm d}t} = (M-I)v + h
    $$

    * 当$\lambda \not= 1$, $c_\nu(t) = \frac{e_\nu \dot\ h}{1-\lambda_\nu} \left( 1-\exp(-\frac{(1-\lambda_\nu)t}{\tau}) \right) + c_\nu(0)\exp(-\frac{(1-\lambda_\nu)t}{\tau})$
    * 当$\lambda=1$, $c_1(t) = c_1(0) + \frac{1}{\tau_r}\int_0 e_1 \dot\ ht' {\rm d}t$

  * 上述模型并不能直接描述记忆：假设特征向量是已知的（连接矩阵是已知的）

    * Eigenvalue degeneracy（简并性）：矩阵有相同的特征值

    * 假设：有N个神经元，每个需要记忆的时间需要$\alpha N$个神经元（0.1），每个神经元需要激活的水平为$c$ (firing rate到达c)
      $$
      \tau_r \frac{{\rm d}v}{{\rm d}t} = -v + F(h + Mv) 
      $$

      * 则每个记忆事件$m$的稳定状态为$v^m = F(Mv^m)$

