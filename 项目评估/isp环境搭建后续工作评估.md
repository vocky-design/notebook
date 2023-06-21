后续的可能工作：

- 代码和资源固化，代码优化
  - dsp_branch和bsp_branch
  - rkisp代码优化，及librkaiq.so调用流程优化（低可能性）
- 文档输出
  - isp环境搭建文档
  - branch分支修改文档
- 继续跟进
  - sensor_information是否需要更新
  - 是否某些isp模块某些选项不好用
- 本轮还剩余需要确认的
  - 确定rkaiq_tool可以选择不同sensor进行调焦
  - 确定调焦后iq文件后取流进程重新运行确实有效果



bsp的修改：

- 根文件系统的修改（替换库）

   ![image-20230519105241288](Y:\github_local\notebook\项目评估\.isp环境搭建后续工作评估.assets\image-20230519105241288.png)ls 

  ![image-20230519105421058](Y:\github_local\notebook\项目评估\.isp环境搭建后续工作评估.assets\image-20230519105421058.png)

  还缺少的：

  - libcrypto_extern.so



![image-20230519111219352](Y:\github_local\notebook\项目评估\.isp环境搭建后续工作评估.assets\image-20230519111219352.png)



![image-20230519111236902](Y:\github_local\notebook\项目评估\.isp环境搭建后续工作评估.assets\image-20230519111236902.png)



缺失的库：

- 没有缺失，多加了一个librkaiq.so库（后续要从根文件系统删掉）

------

![image-20230519111731894](Y:\github_local\notebook\项目评估\.isp环境搭建后续工作评估.assets\image-20230519111731894.png)





- probe中的sensor_entity_name修改
- 设备数中dev_id的修改







dsp的修改部分：

- libmpp的编译链替换、libmpp_demo的编译链替换
