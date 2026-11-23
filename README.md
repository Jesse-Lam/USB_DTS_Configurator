<div align="center">

# Rockchip USB DTS Configurator

**Rockchip 系列 SoC 的 USB 设备树（DTS）配置生成器**

[![在线试用](https://img.shields.io/badge/在线试用-GitHub%20Pages-2ea44f)](https://jesse-lam.github.io/USB_DTS_Configurator/)
[![下载](https://img.shields.io/badge/下载-最新版本-blue)](https://github.com/Jesse-Lam/USB_DTS_Configurator/releases)
[![芯片](https://img.shields.io/badge/芯片-16%20款-orange)](https://github.com/Jesse-Lam/USB_DTS_Configurator/releases)
[![License](https://img.shields.io/badge/License-MIT-lightgrey)](#license)

</div>

## 这是什么

Rockchip 每颗 SoC 的 USB 配置分散在 SDK 的 `dtsi`、板级 `dts` 和官方文档里。写一份配置常常要来回翻好几处：PHY 节点怎么挂、VBUS 该用 `vbus-supply` 还是 GPIO、同一个属性在不同内核版本上有什么区别……

这个工具把这些内容整理成可以直接勾选的条目，选完就能拿到一份带注释的 DTS 片段：

- 覆盖 **16 款 SoC**、**28 份内核版本文档**，共 406 条可选配置
- 每条配置都带注释：为什么这么配、有哪些坑、哪些值必须按原理图改
- 勾选多个 USB 口自动拼接成一份 DTS，一键复制
- 纯前端单文件，不联网也能用，可以拷到内网机器

## 在线使用 / 离线下载

| 方式 | 地址 |
|---|---|
| **在线直接打开**（推荐） | https://jesse-lam.github.io/USB_DTS_Configurator/ |
| 离线单文件 | 到 [Releases](https://github.com/Jesse-Lam/USB_DTS_Configurator/releases) 下载 `USB_DTS_Configurator_Vx.y.z.html`，双击用 Chrome / Edge 打开 |

浏览器要求：Chrome / Edge 等现代浏览器，无需安装任何东西。

## 三步上手

1. **左侧选主控芯片**（支持搜索，如 `RK3568`、`RV1126B`）
2. **上方选内核版本**（同一芯片可能有 4.4 / 4.19 / 5.10 / 6.1 / 6.12 多个版本）
3. **勾选需要的 USB 口**（OTG / HOST / DEVICE / Type-C / Micro-USB …），右侧即生成 DTS，点「复制」

> 芯片名下方会列出该芯片的 USB 端口摘要（控制器类型），方便先判断要选哪个口。

## 支持的芯片

| 芯片 | 内核版本 | USB 端口 |
|---|---|---|
| RK312X | 4.4 | USBOTG(USB2.0): DWC2；HOST0(USB2.0): EHCI + OHCI |
| RK3288 | 4.4 | USBOTG(USB2.0): DWC2；HOST0(USB2.0): EHCI + OHCI；HOST1(USB2.0): DWC2 |
| RK3308 | 4.4 / 5.10 / 6.1 | USBOTG(USB2.0): DWC2；USBHOST(USB2.0): EHCI + OHCI |
| RK3326 | 4.4 / 5.10 / 6.1 | USBOTG(USB2.0): DWC2；USBHOST(USB2.0): EHCI + OHCI |
| RK3328 | 4.4 | OTG20(USB2.0): DWC2；HOST0(USB2.0): EHCI + OHCI；USB30(USB3.0): DWC3 |
| RK3399 | 4.4 / 4.19 / 5.10 | TYPEC0(USB3.1): DWC3；TYPEC1(USB3.1): DWC3；HOST0(USB2.0): EHCI + OHCI；HOST1(USB2.0): EHCI + OHCI |
| RK3506 | 6.1 | USBOTG0(USB2.0): DWC2；USBOTG1(USB2.0): DWC2 |
| RK3562 | 5.10 | OTG0(USB3.1): DWC3；HOST1(USB2.0): EHCI + OHCI |
| RK3568 | 4.19 / 5.10 / 6.1 | OTG0(USB3.1): DWC3；HOST1(USB3.1): DWC3；HOST2(USB2.0): EHCI + OHCI；HOST3(USB2.0): EHCI + OHCI |
| RK3572 | 6.12 | USBDRD0(USB3.1): DWC3；USBDRD1(USB3.1): DWC3 |
| RK3576 | 6.1 | TYPEC0(USB3.1): DWC3；TYPEC1(USB3.1): DWC3 |
| RK3588 | 5.10 / 6.1 / 6.12 | TYPEC0(USB3.1): DWC3；TYPEC1(USB3.1): DWC3；USBHOST30(USB3.1): DWC3；HOST0(USB2.0): EHCI + OHCI；HOST1(USB2.0): EHCI + OHCI |
| PX30 | 4.4 / 5.10 / 6.1 | USBOTG(USB2.0): DWC2；USBHOST(USB2.0): EHCI + OHCI |
| RV1126B | 6.1 | USBDRD(USB3.1): DWC3；USBHOST(USB2.0): EHCI + OHCI |
| RV1106B_RV1103B | 6.1 | USB(USB2.0): DWC3 |
| RV1106_RV1103 | 5.10 | USB(USB2.0): DWC3 |

## 生成的配置长什么样

以 RK3328 的 Micro-USB2.0 OTG 为例：

```c
/* Micro-USB 座子带 ID 脚，dr_mode = "otg" 按 ID 自动切换 Host / Device */

/* **注意：该设计如果有热插拔需求，vbus 需要通过分压电路接到主控端的 vbusdet 引脚，用于 Device 模式时识别连接/断开状态** */

/* USB2.0 PHY 父节点 */
&u2phy {
	status = "okay";
};

/* OTG PHY：vbus-supply 在 Host 模式打开 VBUS，Device 模式关闭 */
&u2phy_otg {
	status = "okay";
	vbus-supply = <&vcc5v0_otg>;
};

/* VBUS 5V regulator，请根据原理图修改，这里以 GPIO0_A2 为例 */
vcc5v0_otg: vcc5v0-otg-regulator {
	compatible = "regulator-fixed";
	...
};
```

## 常见问题

**Q：生成的 GPIO 编号能直接用吗？**
不能。文档里的引脚、regulator 名都是**示例值**，用来演示标准设计怎么写；必须对照自己板子的原理图改成实际引脚。

**Q：为什么某个芯片没有我要的内核版本？**
只收录 Rockchip 在该内核上实际提供过 dtsi、且配置经过核对验证的组合。没有的版本说明上游没有对应支持或未验证，可以用相邻版本的配置作参考，再对照该版本 `dtsi` 调整。

**Q：支持哪些接口类型？**
Type-A / Micro-USB / Type-C 三种座子，角色覆盖 OTG、HOST、DEVICE；Type-C 提供「无 CC 芯片」「FUSB302」「HUSB311」「ET7303」等方案，具体以该芯片下实际列出的选项为准。

**Q：可以一次勾多个 USB 口吗？**
可以，勾选多个口会自动拼接成一份 DTS。硬件上互斥的选项会自动禁用并提示。

**Q：能离线用吗？**
能，整个工具就是一个 HTML 文件，加载后不需要联网。

**Q：和 SDK 里现成的 dts 冲突吗？**
工具只给片段，需要合并进你的板级 dts；同名 label（如 `u2phy`、`vcc5v0_otg`）要注意别重复定义，工具里已有提示。

## 数据来源与免责声明

- 配置依据：Rockchip 官方文档（《Rockchip Linux USB 开发指南》RK-KF-YF-096 等）、公开 SDK 源码（`rk3xxx.dtsi`、板级 `dts`、PHY/DWC 驱动）、公开参考设计原理图
- 文档中的配置是**标准设计示例**，用于说明"这类接口应该怎么配"，**不是某块具体板子的成品配置**
- 引脚编号、regulator 名、`dr_mode` 等**必须按实际原理图核对**；量产前请自行验证，作者不对由此产生的硬件问题负责

## 反馈

- 缺少芯片 / 内核版本、配置与你的板子不符、生成结果有误 → 欢迎提 [Issue](https://github.com/Jesse-Lam/USB_DTS_Configurator/issues)
- 提 Issue 时请带上：芯片型号、内核版本、你选的接口、实际现象

## License

[MIT](LICENSE)
