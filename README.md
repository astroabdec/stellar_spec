# 高分辨率光谱提取恒星参数和元素丰度<br />Stellar Parameters Extraction & Abundance Analysis with Hi-Res. Spectra


## 准备工作 Prerequist

- **光谱分析软件 Spectral Data Analysis Tools**
  - **iSpec**
  
    iSpec 是一个用于光谱分析的开源框架, 能够创建光谱库并确定天体物理参数。它集成了多个辐射转移代码, 并支持 SAMP 协议以实现与其他天文软件的互操作性。
    详细的安装指南请参考[官方安装指南](https://www.blancocuaresma.com/s/iSpec/manual/introduction)或[iSpec安装](https://www.notion.so/iSpec-53cf364299a24bf0801d5bb1489fe919).

    iSpec is an open-source framework for spectral analysis, capable of creating spectral libraries and determining astrophysical parameters. 
    It integrates with various radiative transfer codes and supports the SAMP protocol for interoperability with astronomical software.
    For detailed installation guide, please refer to [the official installation guide](https://www.blancocuaresma.com/s/iSpec/manual/introduction) or [iSpec installation instruction (Chinese ONLY)](https://www.notion.so/iSpec-53cf364299a24bf0801d5bb1489fe919).
  

- **辐射转移代码 Radiative Transfer Code**
  
  辐射转移代码是用于模拟和计算电磁辐射在星际和恒星大气等介质中的传播、吸收和散射的计算工具. 按照对局部热动平衡的假设, 可以将辐射转移代码分为局部热动平衡 (Local Thermodynamic Equilibrium, 或 LTE) 和非局部热动平衡 (Non-LTE, 或者 NLTE) 两大类. LTE和NLTE代码的特点如下表所示.

    | **方面**<div style="width:100px"> | **LTE**<div style="width:120px"> | **NLTE**<div style="width:120px"> |
    |:---:|:---:|:---:|
    | **基本假设** | 每个小区域与其局部辐射场处于热平衡 | 不假设局部平衡, 考虑整个大气中的辐射场对局部气体状态的影响 |
    | **适用条件** | 适用于低温和高密度环境, 辐射场是普朗克辐射且各向同性 | 可适用于高温和低密度环境, 辐射场可能非普朗克辐射 |
    | **辐射过程** | 辐射过程与局部热平衡相匹配 | 辐射过程可能不与局部热平衡相匹配, 需要更复杂的计算 |
    | **碰撞过程** | 碰撞过程足以建立局部热平衡 | 碰撞过程可能不足以建立平衡, 特别是在稀薄的大气中 |
    | **计算复杂度** | 简单, 仅考虑局部条件 | 复杂, 需考虑辐射相互作用 |
    | **准确性** | 在高金属丰度条件下结果准确, 但在处理贫金属恒星中可能不准确 | 即使是贫金属丰度也能准确再现光谱特征 |
    | **优点** | 计算简便 | 更精确地描述辐射与物质相互作用的过程 |
    | **缺点** | 在辐射通量较高或气体密度较低的条件下结果可能不准确 | 计算资源需求大, 模拟复杂 |

  *拓展阅读* 
   - *[Accuracy and Precision of Industrial Stellar Abundances (Jofre+2019)](https://ui.adsabs.harvard.edu/abs/2019ARA%26A..57..571J/abstract)*
   - *[LTE or non-LTE, that is the question. The NLTE chemical evolution of strontium in extremely metal-poor stars (Hansen+2013)](https://ui.adsabs.harvard.edu/abs/2013A%26A...551A..57H/abstract)* 
   - *[NLTE-Basic concepts](https://home.ifa.hawaii.edu/users/kud/teaching_16/7_Non_LTE.pdf)* 
   

  常见的辐射转移代码有
  - SME (Spectrum Made Easy) & pySME

  - MOOG & pyMOOG

  - SPECTRUM
  
  - TURBOSPECTRUM

- **恒星大气模型与线表 Stellar Atmosphere Model & Line List**
  
  恒星大气模型是用于模拟恒星大气中辐射转移的理论模型, 通常包括恒星大气的温度、密度、压强等参数. 
  线表则是用于描述恒星大气中各种元素和分子的吸收线的位置和强度的数据表格. 通常线表中包含了各种元素和分子的吸收线的波长、强度等信息.

  常见的恒星大气模型有
  - MARCS
  - ATLAS
  - PHOENIX
  - Kurucz

  常见的线表有
  - VALD
  - NIST
  - MOOG
  - Kurucz


## 恒星参数估算 Stellar Parameters Estimation
当原子和分子吸收特定频率的能量时, 会跃迁到新的能级. 这一跃迁在光谱中体现为特定波长的光被吸收, 形成吸收线. 通过对这些吸收线位置和强弱的细致分析, 我们能够鉴定恒星大气中各种元素和分子的丰度. 除此之外, 恒星沿视线方向上的运动、自转等运动会在光谱上产生多普勒效应, 影响谱线的位置和形状. 
因此, 在分析观测光谱前, 必须预先估计恒星大气模型和运动的相关参数, 包括有效温度 $T_\mathrm{eff}$, 表面重力 $\log{g}$, 金属丰度 $\mathrm{[M/H]}$, 微观湍流速度 $\xi_t$, 视向速度 $v_r$ 和投影旋转速度 $v\sin{i}$ 等.



### 有效温度 Effective Temperature $T_\mathrm{eff}$
有效温度指和某天体发出等量电磁辐射的黑体的温度. 
模型依赖的方法通常基于理论预测, 而非模型依赖的方法则直接从观测数据中提取信息.

- **通过绝对流量确定 $T_\mathrm{eff}$**(唯一非模型依赖方法)

- **通过色指数确定 $T_\mathrm{eff}$**
- **通过绝对热星等 $M_\mathrm{bol}$ 确定 $T_\mathrm{eff}$**
- **红外流量方法 IRF**


### 表面重力 Surface Gravity $\log{g}$


### 金属丰度 Metallicity $\mathrm{[M/H]}$
金属丰度指恒星内除氢和氦外其余化学元素的**所占的比例**, 可以通过 **质量比例** 或者 **化学丰度** 表示.

在质量比例标度中, 记H比例为X, He比例为Y, 剩下其他元素(统称为金属)比例为Z, 满足
$$
X+Y+Z=1
$$
其中 $X_\odot=0.7381, Y_\odot=0.2485, Z_\odot=0.0134$ [(Asplund et al. 2009)](https://ui.adsabs.harvard.edu/abs/2009ARA&A..47..481A/abstract)

化学丰度标度 $\mathrm{[X/H]}$ 是指将某恒星的铁元素 $X$ 的丰度与太阳中该元素的丰度比较, 取其比值的常用对数, 计算方式如下

$$
\left[\dfrac{X}{H}\right] = \log_{10}\left(\dfrac{N_X}{N_H}\right)_\star - \log_{10}\left(\dfrac{N_X}{N_H}\right)_\odot
$$

:warning: 在生成理论光谱时务必留意使用模型或代码所需“丰度”的定义.

<!-- ### 微观湍流速度 microturbulence $\xi_t$ -->

<!-- ### 宏观湍流速度 macroturbulence -->



## [相关文件 Related Documents](slides)

- 小组综合信息 [[2024.7.18]](slides/ABDEC2024-stellar_spec_intro-Shi.pdf)[[金山文档 KDocs]](https://www.kdocs.cn/l/cjs58rcICzKN)
- 光谱分析讲义 [[2024.7.18]](slides/ABDEC2024-stellar_spec_intro-Shi.pdf)
- pySME 快速上手指南 / pySME Quick Guide [[2024.7.18]](slides/ABDEC2024-pySME_quick_guide-Jian.pdf)[[金山文档 KDocs]](https://kdocs.cn/l/cmhZefQSzzg1)
- 恒星参数估计 / Stellar Parameters Estimation Slides [[2024.7.18]](slides/ABDEC2024-stellar_para_misc-Jian.pdf)[[金山文档 KDocs]](https://kdocs.cn/l/ceExgv48wkvg)
  
**延伸阅读 Further Reading** 
- [The Observation and Analysis of Stellar Photospheres](https://www.cambridge.org/highereducation/books/the-observation-and-analysis-of-stellar-photospheres/67B340445C56F4421BCBA0AFFAAFDEE0#overview)
  > Easy, Practical, No NLTE

  [Mingjie的读书笔记 / Mingjie's Reading Note](https://mk-cookbook.readthedocs.io/en/latest/OASP/OASP_note/OASP_note.html)
- [Radiative Transfer in Stellar Atmospheres](https://robrutten.nl/Radiative_Transfer.html)
  > Normal, Theory, NLTE

- [Theory of Stellar Atmospheres](https://press.princeton.edu/books/paperback/9780691163291/theory-of-stellar-atmospheres)
  > Dictionary, Wikipedia, Bible