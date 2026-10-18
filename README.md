# Aster MCA Nano

低成本、小型化的通用多道脉冲幅度分析器原型，包含 CH32X033F8P6 与 AT32F423 两种 PCB。当前采谱开发主线是 **AT32F423CCT7 + TLV9061**，接收探头已有放大／整形电路输出的负脉冲。

本仓库保存硬件工程、生产与手焊资料、固件、macOS 上位机源码，以及有明确采集条件的实验记录。它与 FPGA 版 [Aster MCA](https://github.com/pulsefoundry/Aster-MCA) 是两个项目。

## 当前检查点：2026-10-18

| 项目 | 当前状态 |
|---|---|
| AT32 硬件 | v0.3，AT32F423CCT7；R21 = 40.2 kΩ、R22 = 10 kΩ，运放标称增益 5.02 |
| 固件 | `firmware/at32-peak-scope`，v0.8.2 |
| 采谱 | 4 MS/s，12 位 ADC；板上取峰并累计 2048 道，到时停止后回传一次能谱 |
| 触发门限 | 当前改焊板用 **100 ADC 码**；原增益配置用 20 码 |
| 波形诊断 | 仅空闲时采 4096 点／1.024 ms，停止后上传；段间有采样空隙 |
| 桌面软件 | macOS 原生窗口 v0.6.1，源码位于 `firmware/at32-continuous/desktop` |
| 最新实测 | 改焊后铯源采集 60.000256 s，接受 17,611 事件，所有 58,594 个 DMA 块处理完成，无设备错误或 ADC 溢出 |

改焊前／后的各 60 秒总源谱，用同一高斯峰加线性连续本底模型拟合，相对峰宽为 **17.20%／16.07%**。峰位从 78.33 移到 408.79 ADC 码，后者按标称 3.3 V 参考换算约 0.329 V。

这是一次短时对照实验，不是探头本征分辨率或硬件极限。拟合窗口变化后，改焊后的结果约为 15.36%–16.96%；模型条件下的差值 95% 区间为 −2.50 至 +0.33 个百分点，仍包含零，**尚未证明显著改善**。未做多点能量标定。

![改焊前后拟合对照](outputs/Aster-MCA-Nano-AT32-增益5倍实测-20261018/gain-peak-fit-comparison.png)

完整状态及下一步见 [检查点](docs/2026-10-18-checkpoint.md)。当前板子采集已停止，后续上板操作需先重新确认实体接线与源状态。

## 目录

- `firmware/at32-peak-scope`：当前 v0.8.2，正常取峰谱与有限长度 RAW 诊断。
- `firmware/at32-peak`：v0.8.0 取峰基线。
- `firmware/at32-histogram`：v0.6.3 积分谱基线及共享谱通信校验代码。
- `firmware/at32-continuous`：上位机、共享模型与早期 1 MS/s 连续采样分支；其桌面界面也支持当前板上取峰谱。
- `firmware/ch32-selftest-v0.3`：CH32 历史模拟链／波形诊断源码，不是已验证的采谱固件。
- 其余 `firmware/at32-*`：采样、选窗和 USB 传输实验历史。v0.7.x 在真实脉冲下未通过持续处理验证，不作为当前稳定路径。
- `outputs`：命名的生产资料、焊接对照、固件镜像、原始数据、图与验证记录。
- `analysis/20261018`：使用仓库内已保存数据复现增益对照拟合。
- `work`：本机工具链、第三方 SDK、虚拟环境、实时记录与临时分析目录，不纳入 Git。

## 硬件资料

| 资料 | 路径 |
|---|---|
| AT32 单板工程、Gerber、BOM | [AT32 v0.3](outputs/Aster-MCA-Nano-AT32-v0.3-USB-A-LED) |
| CH32 单板工程、Gerber、BOM | [CH32 v0.3](outputs/Aster-MCA-Nano-CH32-v0.3-USB-A-LED) |
| AT32 手焊对照 | [焊接资料](outputs/Aster-MCA-Nano-AT32-v0.3-焊接对照) |
| 本次 R21/R22 改焊 | [位置图及步骤](outputs/Aster-MCA-Nano-AT32-增益改焊-20261018) |
| 历史开槽合板工程 | [v0.3.1](outputs/Aster-MCA-Nano-v0.3.1-SlotCut-Bridge2p5-32x38) |

单板 32 × 18 mm，USB-A 与 SMA 接口。实装板厚为 1.6 mm；本轮 SMA 使用外置插座与短线连接。历史合板曾因同文件包含两种设计被厂方拒单，保留作工程参考，不能视为已批准的生产文件。

AT32 原 BOM 为 R21 = 0 Ω、R22 不装；当前实测改焊配置覆盖这两个默认值。CH32 移除 C9 的历史诊断结果不直接套用于 AT32。两块板的焊接对照需分别使用。

## 本地运行与软件检查

上位机和本地 C 辅助库当前按 macOS 环境验证，需要 Python 3 与可用的 `cc`。下面的检查使用模拟串口或已保存数据，不访问实体板子：

```sh
python3 -m unittest discover -s firmware/at32-peak-scope -p 'test_*.py' -v
python3 -m unittest discover -s firmware/at32-continuous -p 'test_*gui.py' -v
```

部分长历史重放测试依赖本机 `work` 数据，新克隆缺少这些数据时会明确跳过。仓库保留了本次 500 段本底／铯源波形和前后各 60 秒谱，供独立复核。

浏览器开发入口：

```sh
python3 firmware/at32-continuous/gui.py --open
```

macOS 原生桌面打包需要 Xcode Command Line Tools、Python 与 PyInstaller：

```sh
python3 -m venv work/desktop-build-venv
work/desktop-build-venv/bin/python -m pip install -r requirements-desktop.txt
work/desktop-build-venv/bin/python firmware/at32-continuous/desktop/build_desktop.py
```

生成 `outputs/Aster Nano.app`。打包运行时不纳入源码仓库。当前界面重新连接时会回填默认门限 20，**改焊板每次重连后需确认门限为 100**。

## 构建当前固件

当前构建脚本支持 `ARM_GCC_BIN`、`AT32_SDK`；也会查找 PATH 上的 ARM GCC。原实验使用 ARM GNU Toolchain 14.2.Rel1。

SDK 来自 [ArteryTek/AT32F423_Firmware_Library](https://github.com/ArteryTek/AT32F423_Firmware_Library)，使用的提交为 `a60472b248c39034f111ac17e7b1286549594435`。

```sh
git clone https://github.com/ArteryTek/AT32F423_Firmware_Library.git work/at32-bringup-2026-10-14/sdk
git -C work/at32-bringup-2026-10-14/sdk checkout a60472b248c39034f111ac17e7b1286549594435
export ARM_GCC_BIN=/path/to/arm-gnu-toolchain/bin
python3 firmware/at32-peak-scope/build.py
```

固件使用 CPU 144 MHz、ADC 72 MHz、内部 LDO 1.3 V，与本轮实测配置一致。构建成功仅说明软件检查通过，上板仍须单独验证。

`flash.py` 是指定旧镜像到新镜像的受保护迁移工具，支持环境变量 `DFU_UTIL`，并执行全 Flash／选项字节备份和回读。默认仅接受冻结 v0.8.1 身份，`--restore-peak` 仅接受当前 v0.8.2 并回退到冻结 v0.8.0；它不是任意空白芯片下载器。固件镜像及 SHA256 位于对应 `outputs` 发布目录。v0.8.1 仅作为迁移身份参考保留，其全缓冲 RAW 模式已判失败。

## 离线复核

```sh
python3 -m venv work/analysis-venv
work/analysis-venv/bin/python -m pip install -r requirements-analysis.txt
work/analysis-venv/bin/python analysis/20261018/fit_gain_comparison.py
```

新结果写入忽略目录 `work/replayed-gain-comparison`，不覆盖已归档实验。原生 2048 道与显示重分箱分别记录，拟合不使用平滑数据。

也可对已保存的改焊后波形进行独立重放：

```sh
work/analysis-venv/bin/python firmware/at32-peak-scope/analyze_scope.py \
  outputs/Aster-MCA-Nano-AT32-增益5倍实测-20261018/raw-Cs-present \
  --out work/replayed-Cs-waveforms --threshold 100 --nominal-relative-gain 5.02
```

每段重新校验序号、长度、ADC 范围、CRC、统计量及停止时序；段间上传空隙不会作为连续样本拼接。正常谱只有最终直方图，不能凭它重做逐事件成形。

## 第三方材料

AT32 SDK 与完整工具链由使用者从其官方仓库获取。CH32 历史诊断中保留的 WCH 代码沿用原有版权声明及仅用于 WCH 微控制器的限制，见其 `SOURCE-NOTES.txt`。生产目录中的器件资料与 EDA 资源保留原来源。项目自身的开源许可证尚未指定。
