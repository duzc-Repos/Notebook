# Week2 Synapse and Channel dynamics

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









































