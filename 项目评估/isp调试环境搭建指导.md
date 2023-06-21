| 更新时间   | 更新内容   |
| ---------- | ---------- |
| 2023-05-18 | 第一次创建 |
|            |            |

此指导对应代码版本（这也是环境包中对应的版本）

- bsp版本：1032631（/data1/dongzhiliang/work/bsp/r318/r318_bsp_base/**branches**/bsp_support_isp） 

- dsp版本：1032941（/data1/dongzhiliang/work/dsp/r318/r318_base/**branch**/dsp_support_isp）

压缩包内容说明：

![image-20230523205558879](Y:\github_local\notebook\项目评估\.isp调试环境搭建指导.assets\image-20230523205558879.png)

| 文件名                                            | 作用                                  |
| ------------------------------------------------- | ------------------------------------- |
| RKISP_Tuner_v2.0.f_0826_test.rar                  | rkisp_tuner客户端安装文件             |
| Rockchip_IQ_Tools_Guide_ISP21_ISP30_CN_v2.0.4.pdf | rkisp_tuner的使用说明文件             |
| dsp_rkaiq_test                                    | 运行目录（所有运行资源的位置）        |
| isp调参有效性展示/                                | isp调参前后对比截图                   |
| isp调试环境搭建指导.pdf                           | isp调参环境启动的指导手册             |
| m00_f_sc130gs-5-0032.json                         | 下sensor的效果配置文件                |
| m01_b_sc130gs-4-0032.json                         | 上sensor的效果配置文件                |
| rkaiq_tool_server-aarch64-20221025_112250_RK3588  | rkisp_tuner客户端在板端运行的服务进程 |
| uboot_and_kernel                                  | uboot和kernel镜像                     |

## 修改内容介绍



![image-20230523171303454](Y:\github_local\notebook\项目评估\.isp调试环境搭建指导.assets\image-20230523171303454.png)

为了能正常使用isp调参功能，需要对bsp和dsp进行相应的更新。

bsp的根文件系统介绍：

- 根文件系统内容由三个来源：
  - 本地busybox编译
  - 本地库编译（有库的源码，每次编译根文件系统都会重新编译一次这些库）
  - 提前编译好的库的导入（没有库的源码）。

sns_entity_name介绍：

- sns_entity_name是一个字符串，由sensor驱动(sc130gs.c)的sc130gs_probe函数通过读取设备树信息来组合而成。模仿官方的sns_entity_name格式及内容，通过修改设备树和sensor驱动相关部分，我们将上下sesnor的sns_entity_name修改为了：
  - 上sensor："m01_b_sc130gs 4-0032"
  - 下sensor："m00_f_sc130gs 5-0032"

bsp对设备树、根文件系统、内核均有修改。**bsp修改点如下**：

- 根文件系统的修改：
  - 修改根文件系统的原因是librkaiq.so（启动isp调参模块的依赖）与现有根文件系统中的libc.so.6和libstdc++.so.6不兼容。librkaiq.so版本更新，有对后者更高版本的要求。所以我们需要整体替换一遍根文件系统。
  - 对根文件系统的修改
    - 本地busybox编译和本地库编译的工具链替换为更新的版本。
    - 提前编译好的库部分导入更新版本的库。

- sensor驱动(sc130gs.c)的修改（唯一目的：修改sns_entity_name）
- 对设备树的修改（唯一目的：sns_entity_name）

**dsp的修改点如下**：

- 导入新动态库librkaiq.so及其对应的头文件
- 调用librkaiq.so接口设计一个isp_module_init函数，然后在sensor启动流程中进行调用。



## 运行资源准备

- 编译dsp并拷贝生成物


```shell
#起始路径(服务器)：/data1/dongzhiliang/work/dsp/r318/r318_base/branch/dsp_support_isp
#编译
./zoo agv_dsp 1.0.0 all clean
./zoo agv_dsp 1.0.0 all
#拷贝生成物
cd zoos
cp -r dsp_process camera_ps libadsp.so libhk_mrse_r318.so libhk_mrse_r318_v3.so librockchip_mpp.so.1 librkaiq.so 运行目录
cd ../dsp/lib
cp realsense/libcjson.so 运行目录
cp nanomsg/libnanomsg.so 运行目录/libnanomsg.so.5
```

- 编译one_click_test并拷贝生成物

```shell
#起始路径(服务器)：/data1/dongzhiliang/work/dsp/r318/r318_base/branch/dsp_support_isp
#编译
cd demo/test/one_click_test
mkdir build; cd build
export PATH=/opt/rk3588_linux/buildroot/host/bin:$PATH
export LD_LIBRARY_PATH=/opt/rk3588_linux/buildroot/host/lib:$LD_LIBRARY_PATH
cmake ..; make
#拷贝生成物
cp one_click_test 运行目录
cp ../resource/1_dist.hkt 运行目录
```

- 编译bsp并拷贝生成物

```shell
#起始路径(服务器)：/data1/dongzhiliang/work/bsp/r318/r318_bsp_base/branches/bsp_support_isp
#编译
cd  ci/build; chmod 777 buildCCI.sh
rm -r zoos/agv_4rd/* 
./buildCCI.sh release package agv_4rd 1.0.0 all
#拷贝生成物
cd ../../zoos/agv_4rd/
cp u-boot-r318.bin tftp目录
cp uImage_noenc tftp目录/uImage
```

- 从压缩包中拷贝isp调参环境所需资源到运行目录
  - m00_f_sc130gs-5-0032.json
  - m01_b_sc130gs-4-0032.json
  - rkaiq_tool_server-aarch64-20221025_112250_RK3588
- 安装rkisp_tuner客户端
  - 安装MCR_R2016a（安装细节参照压缩包内《Rockchip_IQ_Tools_Guide_ISP21_ISP30_CN_v2.0.4.pdf》1.3和1.4小节）
  - 安装rkisp_tuner（压缩包内有其安装文件，名为RKISP_Tuner_v2.0.f_0826_test.rar）




## isp调参操作验证

1. 烧录uboot和kernel。

2. 将所有运行资源的文件夹(后续称之为dsp_rkaiq_test)放置在板端/mnt目录下。

3. 将json文件放置在板端/mnt/iqfiles目录下

   `mkdir -p /mnt/iqfiles; cp /mnt/dsp_rkaiq_test/m01_b_sc130gs_4-0032.json /mnt/dsp_rkaiq_test/m00_f_sc130gs_5-0032.json /mnt/iqfiles`

4. 与笔记本建立局域网。

5. 启动dsp_process

   `cd /mnt/dsp_rkaiq_test; cp *.so* /lib64; ./dsp_process`

6. 启动上sensor取流

   `cd /mnt/dsp_rkaiq_test; ./one_click_test 4 6`

7. 启动camera进程

   `cd /mnt/dsp_rkaiq_test; cp camera_ps/XML_Vision.zip /home; ./camera_ps/camera`

8. 在mvs客户端查看取流是否符合预期

9. 启动rkaiq_tool_server

   `./rkaiq_tool_server-aarch64-20221025_112250_RK3588`

10. rkisp_tuner客户端连接，尝试调参并在mvs客户端上确认调参效果。（具体步骤见[rkisp_tuner客户端操作说明](# rkisp_tuner客户端操作说明)第1、2条）

11. 将调参后的json文件保存到本地，并同步到板端。（具体步骤见[rkisp_tuner客户端操作说明](# rkisp_tuner客户端操作说明)第3、4条）

12. 重启板端，重新走3-10步，只是这次是为了调试下sensor，所以第6步需要执行`cd /mnt/dsp_rkaiq_test; ./one_click_test 4 7`，其他步骤基本相同。

13. 重启板端，重新走3-7步，只是这次是为了确认json文件修改有效，所以需要将上下sensor都启动取流，第6步需要执行`cd /mnt/dsp_rkaiq_test; ./one_click_test 4 5`，其他步骤基本相同。



## rkisp_tuner客户端操作说明

1. 载入json文件：File->Open->选择加载本地json文件->后续全部点击ok即可。
   - 调试上读码选择m01_b_sc130gs_4-0032.json(位置：rkisp_tuner安装文件夹/configs/RK3588)。
   - 调试下读码选择m00_f_sc130gs_5-0032.json(位置：rkisp_tuner安装文件夹/configs/RK3588)。
2. 调参（效果请看[isp调参有效性展示](# isp调参有效性展示)）
   1. 调整exposure time和exposure gain（AEC->CommCtrl）
      1. Param中的AecOpType切换为RK_AIQ_OP_MODE_MANUAL
      2. AecManualCtrl->LinearAE.TimeValue设置为0.0001（注：设置此值是为了增加噪点，后面调试nr降噪时效果更易观察）
      3. AecManualCtrl->LinearAE.GainValue设置为3000（注：设置此值是为了增加噪点，后面调试nr降噪时效果更易观察）

   2. 调试gamma（agamma_calib->GammaTuningPara->Edit Curve）
   3. 调试blc（ablc_calib->BlcTuningPara->enable）
   4. 调试nr（bayertnr_v2->Bayernr3D->enable）
3. 保存调参后的json文件：File->Save As->Save(注：一般是保存在rkisp_tuner客户端的configs/RK3588目录)
4. 将更新的json文件同步到板端/mnt/iqfiles目录下，可以使用rkisp_tuner客户端的Send File to Device功能：File Transfer->Send File To Device（注：不必关心其进度条不动没有显示传输完成，实际已经完成，可以直接去板端确认）



## isp调参有效性展示

### aec调参有效性展示

![aec前](Y:\github_local\notebook\项目评估\.isp调试环境搭建指导.assets\aec前.png)

![aec后](Y:\github_local\notebook\项目评估\.isp调试环境搭建指导.assets\aec后.png)

### gamma调参有效性展示

![gamma前](Y:\github_local\notebook\项目评估\.isp调试环境搭建指导.assets\gamma前.png)

![gamma后](Y:\github_local\notebook\项目评估\.isp调试环境搭建指导.assets\gamma后.png)

### blc调参有效性展示

![blc前](Y:\github_local\notebook\项目评估\.isp调试环境搭建指导.assets\blc前.png)

![blc后](Y:\github_local\notebook\项目评估\.isp调试环境搭建指导.assets\blc后.png)

### nr调参有效性展示

![nr前](Y:\github_local\notebook\项目评估\.isp调试环境搭建指导.assets\nr前.png)

![nr后](Y:\github_local\notebook\项目评估\.isp调试环境搭建指导.assets\nr后.png)