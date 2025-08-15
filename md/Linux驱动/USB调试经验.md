## 调试经验

如果USB电源负载较大或负载变化率大，应增加100uF大电容储能和滤波

USB3.0必须增加ESD器件

一般USB2.0信号上ESD器件的结电容不得超过3pF，USB3.0信号上ESD器件的结电容不得超过0.4pF



## RK3568 USB 及其phy简介

### 概述

**USB2.0 HOST (EHCI/OHCI) x 2**

**USB3.0 OTG x 1(DWC3/xHCI)**

**USB3.0 HOST x 1(DWC3/xHCI)**

- xHCI（eXtensible Host Controller Interface）：USB 3.0及以后版本的主机控制器标准，支持USB 1.x、2.0和3.x所有速度等级。
- EHCI（Enhanced Host Controller Interface）：专门用于USB 2.0高速（High-Speed，480 Mbps）设备的主机控制器接口标准。
- OHCI（Open Host Controller Interface）：主要用于USB 1.1全速（Full-Speed，12 Mbps）和低速（Low-Speed，1.5 Mbps）设备的主机控制器接口标准。
- UHCI（Universal Host Controller Interface）：也是USB 1.1的主机控制器标准，由Intel提出，功能类似OHCI，但实现方式不同。
- DWC3（DesignWare Core USB 3.0）：Synopsys公司设计的一款USB 3.0控制器IP核

### 框图结合代码介绍

![image-20250814175011206](S:\笔记文档\Linux_Rockchip\md\Linux驱动\USB调试经验.assets\image-20250814175011206.png)

#### USB 3.0 OTG 控制器与 SATA_0 控制器复用 USB3/SATA Combo PHY_0

##### Combo PHY_0

```bash
/* USB 3.0 OTG/SATA Combo PHY_0 */
combphy0_us: phy@fe820000 {
	compatible = "rockchip,rk3568-naneng-combphy";
	reg = <0x0 0xfe820000 0x0 0x100>;
	#phy-cells = <1>;
	clocks = <&pmucru CLK_PCIEPHY0_REF>, <&cru PCLK_PIPEPHY0>,
		 <&cru PCLK_PIPE>;
	clock-names = "refclk", "apbclk", "pipe_clk";
	assigned-clocks = <&pmucru CLK_PCIEPHY0_REF>;
	assigned-clock-rates = <100000000>;
	resets = <&cru SRST_P_PIPEPHY0>, <&cru SRST_PIPEPHY0>;
	reset-names = "combphy-apb", "combphy";
	rockchip,pipe-grf = <&pipegrf>;
	rockchip,pipe-phy-grf = <&pipe_phy_grf0>;
	status = "disabled";
};
```

##### USB 3.0 OTG 控制器

<img src="S:\笔记文档\Linux_Rockchip\md\Linux驱动\USB调试经验.assets\image-20250814175151849.png" alt="image-20250814175151849"  />

##### SATA_0 控制器

![image-20250814175333479](S:\笔记文档\Linux_Rockchip\md\Linux驱动\USB调试经验.assets\image-20250814175333479.png)

#### USB 3.0 Host_1 控制器与 SATA_1/QSGMII 控制器复用 USB3/SATA/QSGMII Combo PHY_1

##### Combo PHY_1

```bash
/* USB 3.0 Host/SATA/QSGMII Combo PHY_1 */
combphy1_usq: phy@fe830000 {
	compatible = "rockchip,rk3568-naneng-combphy";
	reg = <0x0 0xfe830000 0x0 0x100>;
	#phy-cells = <1>;
	clocks = <&pmucru CLK_PCIEPHY1_REF>, <&cru PCLK_PIPEPHY1>,
		 <&cru PCLK_PIPE>;
	clock-names = "refclk", "apbclk", "pipe_clk";
	assigned-clocks = <&pmucru CLK_PCIEPHY1_REF>;
	assigned-clock-rates = <100000000>;
	resets = <&cru SRST_P_PIPEPHY1>, <&cru SRST_PIPEPHY1>;
	reset-names = "combphy-apb", "combphy";
	rockchip,pipe-grf = <&pipegrf>;
	rockchip,pipe-phy-grf = <&pipe_phy_grf1>;
	status = "disabled";
};
```

##### USB 3.0 Host_1 控制器

![image-20250814175536106](S:\笔记文档\Linux_Rockchip\md\Linux驱动\USB调试经验.assets\image-20250814175536106.png)

##### SATA_1控制器

![image-20250814175549185](S:\笔记文档\Linux_Rockchip\md\Linux驱动\USB调试经验.assets\image-20250814175549185.png)

#### USB 3.0 OTG 控制器与 USB 3.0 Host_1 控制器分别使用 USB 2.0 Comb PHY_0 的 port0 和 port1；

##### USB 3.0 OTG 控制器

![image-20250814180244944](S:\笔记文档\Linux_Rockchip\md\Linux驱动\USB调试经验.assets\image-20250814180244944.png)

##### USB 2.0 Comb PHY_0 port0

![image-20250814180311773](S:\笔记文档\Linux_Rockchip\md\Linux驱动\USB调试经验.assets\image-20250814180311773.png)

#####  USB 3.0 Host_1 控制器

![image-20250814180401216](S:\笔记文档\Linux_Rockchip\md\Linux驱动\USB调试经验.assets\image-20250814180401216.png)

##### USB 2.0 Comb PHY_0 port1

![image-20250814180508462](S:\笔记文档\Linux_Rockchip\md\Linux驱动\USB调试经验.assets\image-20250814180508462.png)

#### USB 2.0 Host_2 控制器与 USB 2.0 Host_3 控制器分别使用 USB 2.0 Comb PHY_1 的 port0 和 port1

##### USB 2.0 Host_2 控制器

![image-20250814180742213](S:\笔记文档\Linux_Rockchip\md\Linux驱动\USB调试经验.assets\image-20250814180742213.png)

##### USB 2.0 Comb PHY_1 port 0

![image-20250814181233285](S:\笔记文档\Linux_Rockchip\md\Linux驱动\USB调试经验.assets\image-20250814181233285.png)

##### USB 2.0 Host_3 控制器

![image-20250814181258673](S:\笔记文档\Linux_Rockchip\md\Linux驱动\USB调试经验.assets\image-20250814181258673.png)

##### USB 2.0 Comb PHY_1 port 1

![image-20250814181324252](S:\笔记文档\Linux_Rockchip\md\Linux驱动\USB调试经验.assets\image-20250814181324252.png)

### 总结

#### 默认使用

正常使用时要把上述所有节点都使能

| 节点名称                       | 节点 DTS 命名                     | 备注                                                         |                               |
| ------------------------------ | --------------------------------- | ------------------------------------------------------------ | ----------------------------- |
| USB 2.0 Comb PHY_0 part1       | u2phy0_host                       | &u2phy0_host {<br/>	phy-supply = <&vcc5v0_host>;<br/>	status = "okay";<br/>}; |                               |
| USB 2.0 Comb PHY_0 part0       | u2phy0_otg                        | &u2phy0_otg {<br/>	vbus-supply = <&vcc5v0_otg>;<br/>	status = "okay";<br/>}; |                               |
| USB 2.0 Comb PHY_1 part1       | u2phy1_host                       | &u2phy1_host {<br/>	phy-supply = <&vcc5v0_host>;<br/>	status = "okay";<br/>}; |                               |
| USB 2.0 Comb PHY_1 part0       | u2phy1_otg                        | &u2phy1_otg {<br/>	phy-supply = <&vcc5v0_host>;<br/>	status = "okay";<br/>}; |                               |
| USB 2.0 Comb PHY_0             | usb2phy0                          | &usb2phy0 {<br/>	status = "okay";<br/>};                  | 可在用户态配置切换host/device |
| USB 2.0 Comb PHY_1             | usb2phy1                          | &usb2phy1 {<br/>	status = "okay";<br/>};                  |                               |
| USB 2.0 Host_2 控制器          | usb_host0_ehci<br/>usb_host0_ohci | &usb_host0_ehci {<br/>	status = "okay";<br/>};<br/>&usb_host0_ohci {<br/>	status = "okay";<br/>}; |                               |
| USB 2.0 Host_3 控制器          | usb_host1_ehci<br/>usb_host1_ohci | &usb_host1_ehci {<br/>	status = "okay";<br/>};<br/>&usb_host1_ohci {<br/>	status = "okay";<br/>}; |                               |
| USB 3.0 OTG 控制器<br/>IP核    | usbdrd_dwc3                       | &usbdrd_dwc3 {<br/>	dr_mode = "otg";<br/>	extcon = <&usb2phy0>;<br/>	status = "okay";<br/>}; |                               |
| USB 3.0 OTG 控制器             | usbdrd30                          | &usbdrd30 {<br/>	status = "okay";<br/>};                  |                               |
| USB 3.0 Host_1 控制器<br/>IP核 | usbhost_dwc3                      | &usbhost_dwc3 {<br/>	status = "okay";<br/>};              |                               |
| USB 3.0 Host_1 控制器          | usbhost30                         | &usbhost30 {<br/>	status = "okay";<br/>};                 |                               |
| USB3/SATA Combo PHY_0          | combphy0_us                       | &combphy0_us {<br/>	status = "okay";<br/>};               |                               |
| USB3/SATA Combo PHY_1          | combphy1_usq                      | &combphy1_usq {<br/>	status = "okay";<br/>};              |                               |

#### OTG自动检测使用

如果要实现OTG自动切换功能，需要增加USB Switch chip（如FUSB340）

[rk3568平台调试typec口实现uvc输出，网络共享等功能_rk3568 uvc-CSDN博客](https://blog.csdn.net/hxHardway/article/details/130530947)

更改USB 3.0 OTG 控制器的IP核（usbdrd_dwc3）与USB3/SATA Combo PHY_0（combphy0_us），使之extcon指向fusb0节点

同时USB 2.0 Comb PHY_0 part0不要通过软件控制vbus使能（因为他被USB 3.0 OTG控制器使用）

软件的vbus请给到fusb0

##### 节省功耗使用

如果产品上不需要使用所有的 USB 接口，建议在内核中，将未使用的 USB 控制器的 DTS 配置为

disable，以降低 USB 控制器的功耗。如下是 disable USB 2.0 Host_2 和 USB 2.0 Host_3 的方法：

```bash
&usb_host0_ehci {
	status = "disabled";
};
&usb_host0_ohci {
	status = "disabled";
};
&usb_host1_ehci {
	status = "disabled";
};
&usb_host1_ohci {
	status = "disabled";
};
```

