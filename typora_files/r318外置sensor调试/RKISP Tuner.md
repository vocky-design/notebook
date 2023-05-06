RKISP Tuner:

- Capture Tool
- Calibration Tool

用户确保ToolServer与RKISP Tuner版本匹配

运行Tuner之前应预先安装MCR_R2016a(9.0.1)的64位版本（仅支持此版本），下载地址：
https://ww2.mathworks.cn/products/compiler/matlab-runtime

IQ文件(.json格式):最后用户需要将该文件替换到固件或设备中相应位置并重启相机应用来确认最终的图像效
果。

也可以采集效果异常的场景，在仿真器中排查问题



Sensor配置

配置参数调试区域：该部分参数说明请参考Rockchip_Tuning_Guide_ISP21的4.1.2.5小节的描
述，用户应参考Sensor的Datasheet来填写（该部分建议驱动调试人员完成）