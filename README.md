# 高分辨率光谱提取恒星参数和元素丰度<br />Stellar Parameters Extraction & Abundance Analysis with Hi-Res. Spectra

2024.7.12-2024.7.16, 云南大学中国西南天文研究所 SWIFAR

> 持续更新中...

## 准备工作 Prerequist

- **辐射转移代码 Radiative Transfer Code**

    辐射转移代码是用于模拟和计算电磁辐射在星际和恒星大气等介质中的传播、吸收和散射的计算工具. 按照对局部热动平衡的假设, 可以将辐射转移代码分为局部热动平衡 (Local Thermodynamic Equilibrium, 或 LTE) 和非局部热动平衡 (Non-LTE, 或 NLTE) 两大类. LTE和NLTE代码的特点比较如下表所示.

  | <div style="width:100px"> | **LTE**<div style="width:120px"> | **NLTE**<div style="width:120px"> |
  | :-: | :-: | :-: |
  | **基本假设** | 各小区域与局部辐射场热平衡 | 考虑整体辐射场对局部气体状态的影响, 不假设局部平衡 |
  | **适用条件** | 适于高密度及低温环境, 辐射场普朗克且各向同性 | 适于低密度及高温环境, 辐射场可能非普朗克 |
  | **辐射过程** | 与局部热平衡相匹配 | 需处理辐射过程与局部热平衡不匹配的情况|
  | **碰撞过程** | 碰撞足以维持热平衡 | 在稀薄大气中碰撞不足以维持平衡 |
  | **计算复杂度** | 相对简单, 仅需考虑局部条件 | 计算复杂, 需处理各种辐射相互作用 |
  | **准确性** | 在高金属丰度星体中结果较准确, 但在贫金属星体中可能不准确 | 能更准确描述辐射与物质的相互作用, 即使在贫金属星体中也能有准确结果 |
  | **优点**| 计算简便 | 能精确描述复杂的辐射与物质相互作用 |
  | **缺点**| 在高辐射或低密度环境下可能不准确 | 需要更多计算资源, 模拟过程复杂 |

  常见的辐射转移代码和集成转移代码的工具有

  - **iSpec**
  
    iSpec 是一个用于光谱分析的开源框架, 能够创建光谱库并确定天体物理参数。它集成了多个辐射转移代码, 并支持 SAMP 协议以实现与其他天文软件的互操作性。
    详细的安装指南请参考[官方安装指南](https://www.blancocuaresma.com/s/iSpec/manual/introduction)或[iSpec安装](https://www.notion.so/iSpec-53cf364299a24bf0801d5bb1489fe919).

    iSpec is an open-source framework for spectral analysis, capable of creating spectral libraries and determining astrophysical parameters. 
    It integrates with various radiative transfer codes and supports the SAMP protocol for interoperability with astronomical software.
    For detailed installation guide, please refer to [the official installation guide](https://www.blancocuaresma.com/s/iSpec/manual/introduction) or [iSpec installation instruction (Chinese ONLY)](https://www.notion.so/iSpec-53cf364299a24bf0801d5bb1489fe919).

  - SME (Spectroscopy Made Easy) [[Valenti & Piskunov, 1996](https://ui.adsabs.harvard.edu/abs/1996A%26AS..118..595V/abstract) & [Piskunov & Valenti, 2017](https://ui.adsabs.harvard.edu/abs/2017A%26A...597A..16P)] 与 pySME [[Wehrhahn+2023]](https://ui.adsabs.harvard.edu/abs/2023A%26A...671A.171W/abstract)

    [SME](https://www.stsci.edu/~valenti/sme.html) 是一款基于IDL的光谱分析软件, 集成于iSpec中. 其具有如下特色:

    - **双模式丰度计算** SME既可以执行LTE计算, 也可以进行NLTE计算, 无需配置多套代码.
    - **多参数光谱拟合** 通过非线性最小二乘法, SME能够同时考虑恒星参数 (如有效温度、表面重力), 元素丰度, 旋转展宽等多个参数, 求解当前输入恒星光谱对应参数的最优解.
    - **广泛应用** SME在多个大型天文学项目中得到广泛应用, 如GALAH和APOGEE. 这些项目依赖于SME的高精度分析能力来研究恒星和银河系的化学演化.

    在此基础上, 学者们开发了[pySME](https://github.com/MingjieJian/SME). pySME重构了原始SME中基于商业软件IDL的代码, 同时保留了SME基于C++和FORTRAN的核心计算功能, 使研究人员可以更自由地访问和利用这一工具.
    在本次研讨班中, 我们使用pySME进行光谱分析.
    有关PySME的安装和使用, 请参阅[PySME快速上手指南](slides/ABDEC2024-pySME_quick_guide-Jian.pdf)或[PySME GitHub](https://github.com/MingjieJian/SME).

  - MOOG 与 pyMOOG
  
    [MOOG](https://www.as.utexas.edu/~chris/moog.html) 是一款基于Fortran和SM的光谱分析软件, 可以执行LTE计算, 同样集成于iSpec中. 
    [pyMOOG](https://github.com/MingjieJian/pymoog)是MOOG的Python封装, 其基于解耦了原始MOOG程序中用于绘图的商业代码SM的[MOOG_NOSM](https://github.com/MingjieJian/moog_nosm), 让使用者可以更自由地使用MOOG进行光谱分析. 此外, pyMOOG能够为单条谱线生成生长曲线 (curve-of-growth, cog), 有助于研究特定谱线的等效宽度在不同丰度水平下的变化.
    有关pyMOOG的安装和使用, 请参阅[pyMOOG文档](https://pymoog.readthedocs.io/en/latest/).

  除此之外, 还有一些其他的辐射转移代码, 此处不再一一详细介绍, 有兴趣的读者可以自行查阅.
  - [KORG](https://github.com/ajwheeler/Korg.jl)
    
    基于Julia实现的辐射转移代码, 支持LTE计算. 可以在[*Python中调用*](https://github.com/ajwheeler/Korg.jl/blob/main/misc/Tutorial%20notebooks/basics/Python%20Basics.ipynb). 同时, KORG支持VALD, Kurucz等线表.

  - [SPECTRUM / TURBOSPECTRUM](http://www.appstate.edu/~grayro/spectrum/spectrum.html) 

    iSpec内置的辐射转移代码之一, 支持LTE计算.
  
  - [Spectrum Investigation Utility (SIU)](https://nlte.mpia.de/)

    基于Fortran和IDL的光谱分析软件, 支持NLTE计算.


- **恒星大气模型与线表 Stellar Atmosphere Model & Line List**
  
  恒星大气模型是用于模拟恒星大气中辐射转移的理论模型, 通常包括恒星大气的温度、密度、压强等参数. 
  线表则是用于描述恒星大气中各种元素和分子的吸收线的位置和强度的数据表格. 通常线表中包含了各种元素和分子的吸收线的波长、强度等信息.

  常见的恒星大气模型有
  - [ATLAS](https://wwwuser.oats.inaf.it/castelli/grids.html)

  - [MARCS](https://marcs.astro.uu.se/)

  - [Kurucz](http://kurucz.harvard.edu/)
  
  - [PHOENIX](https://phoenix.ens-lyon.fr/Grids/)
  



  常见的线表有
  - [VALD (Vienna Atomic Line Database)](http://vald.astro.uu.se/~vald/php/vald.php)

    VALD是目前少有的仍在持续更新的线表. 其最大的特色在于用户能够一定程度上自定义线表的内容, 以满足不同的科学需求.
    下载VALD线表需要提前注册账号, 请访问[VALD注册界面](http://vald.astro.uu.se/~vald/php/vald.php?docpage=contact.html), 注册账号并下载.
    VALD线表的使用可参考[pySME 使用指南](slides/ABDEC2024-pySME_quick_guide-Jian.pdf)中与VALD相关的介绍.

  - [Kurucz](http://kurucz.harvard.edu/)

  - [NIST (National Institute of Standards and Technology)](https://www.nist.gov/pml/atomic-spectra-database)

  *拓展阅读* 
   - *[Accuracy and Precision of Industrial Stellar Abundances (Jofre+2019)](https://ui.adsabs.harvard.edu/abs/2019ARA%26A..57..571J/abstract)*
   - *[LTE or non-LTE, that is the question. The NLTE chemical evolution of strontium in extremely metal-poor stars (Hansen+2013)](https://ui.adsabs.harvard.edu/abs/2013A%26A...551A..57H/abstract)* 
   - *[NLTE-Basic concepts](https://home.ifa.hawaii.edu/users/kud/teaching_16/7_Non_LTE.pdf)* 
   

## 恒星参数估算与提取 Stellar Parameters Estimation & Extraction
恒星参数及其运动会影响其光谱的形状和位置.
当原子和分子吸收特定频率的能量时, 它们会跃迁到新的能级, 在光谱中形成特定波长的吸收线. 
这些吸收线的具体位置和强度, 既反映恒星的物理状态 (有效温度 $T_\mathrm{eff}$, 表面重力 $\log{g}$, 微观湍流速度 $\xi_t$ 等), 同时揭示了恒星大气中元素和分子的丰度 ($\mathrm{[M/H]}$, $\mathrm{A(X)}$ 等). 
除此之外, 恒星沿视线方向上的运动 (即视向速度 $v_r$), 自转 (对应投影旋转速度 $v\sin{i}$) 等会在光谱上产生多普勒效应, 进一步影响谱线的位置和形状. 
在从观测光谱中精确地提取恒星参数和元素丰度之前, 首先需要对这些参数进行初步估算. 恰当的初始参数估计能够缩小参数的搜索范围, 优化计算效率和结果的精确性.

总的来说, 大部分恒星的 $T_\mathrm{eff}$ 和 $\log{g}$ 可以通过Balmer线的等效宽度和形状来确定. 但是, 对于高温恒星, 由于氢原子的电离平衡性不再成立, 因此需要使用其他方法来确定 $T_\mathrm{eff}$ 和 $\log{g}$. 
金属丰度 $\mathrm{[M/H]}$ 可以通过各种金属元素的吸收线的等效宽度和形状来确定. 
微观湍流速度 $\xi_t$ 可以通过谱线的宽度来确定. 
视向速度 $v_r$ 和投影旋转速度 $v\sin{i}$ 可以通过谱线的中心位置和形状来确定.


### 有效温度 Effective Temperature $T_\mathrm{eff}$
有效温度指和某天体发出等量电磁辐射的黑体的温度. 
模型依赖的方法通常基于理论预测, 而非模型依赖的方法则直接从观测数据中提取信息.

- **通过绝对流量确定 $T_\mathrm{eff}$**(唯一非模型依赖方法)
- **通过色指数确定 $T_\mathrm{eff}$**
- **通过绝对热星等 $M_\mathrm{bol}$ 确定 $T_\mathrm{eff}$**
- **红外流量方法 IRF**


### 表面重力 Surface Gravity $\log{g}$


- **Line-Depth Ratio (LDR)**
- **利用电离平衡性确定 $\log{g}$**


### 金属丰度(化学丰度)与元素丰度 Metallicity and Abundance $\mathrm{[M/H]}$ & $\mathrm{A(X)}$

丰度是描述恒星和其他天体中元素组成的重要指标. 
金属丰度通常表示相对于太阳, 恒星中所有金属元素 (除 $\mathrm{H}$ 和 $\mathrm{He}$ 之外的元素) 的总量.
元素丰度则具体指某一元素相对于 $\mathrm{H}$ 的丰度.

按照质量比例(各元素按**质量**占的比例)标度, 记 $\mathrm{H}$ 的质量分数为 $X$, $\mathrm{He}$ 的质量分数为 $Y$, 其他元素(即金属)的质量分数为 $Z$, 可得

```math
X+Y+Z=1
```
其中, $X_\odot=0.7381, Y_\odot=0.2485, Z_\odot=0.0134$  [(Asplund+2009)](https://ui.adsabs.harvard.edu/abs/2009ARA&A..47..481A/abstract)

金属丰度 $\mathrm{[M/H]}$ 反映恒星中所有的金属元素与 $\mathrm{H}$ 的比例相对于太阳的对数差, 通常定义为

```math
\mathrm{[M/H]}=\log_{10}\left(\frac{\sum_{\mathrm{metal}}N_\mathrm{metal}}{N_\mathrm{H}}\right)_\star-\log_{10}\left(\frac{\sum_{\mathrm{metal}}N_\mathrm{metal}}{N_\mathrm{H}}\right)_\odot
```

由于 $\mathrm{Fe}$ 的谱线在光谱中容易辨识, 同时其也是核聚变能够产生的最重的元素, 因此也有学者使用 *铁氢比* $\mathrm{[Fe/H]}$ 作为金属丰度的指标. 即

```math
\mathrm{[Fe/H]}=\log_{10}\left(\frac{N_\mathrm{Fe}}{N_\mathrm{H}}\right)_\star-\log_{10}\left(\frac{N_\mathrm{Fe}}{N_\mathrm{H}}\right)_\odot
```

元素丰度 $\mathrm{A(X)}$ 表示恒星中某种元素 $\mathrm{X}$ 相对于 $\mathrm{H}$ 的原子数密度比的对数值. 定义为

```math
\mathrm{A(X)}=\log_{10}\left(\frac{N_\mathrm{X}}{N_\mathrm{H}}\right)_\star+12
```

:warning: 在生成理论光谱时, 务必留意所使用的模型或代码要求输入的“丰度”的定义.

<!-- ### 微观湍流速度 microturbulence $\xi_t$ -->

<!-- ### 宏观湍流速度 macroturbulence -->

<!-- ## 元素丰度分析 Abundance Analysis -->


## [相关文件 Related Documents](slides)

- 会前准备 [[2024.7.18]](slides/ABDEC2024-preparation.pdf)[[金山文档 KDocs]](https://kdocs.cn/l/cqvAuLQPTNFz)
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
