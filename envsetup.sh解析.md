envsetup.sh -> buildroot/build/envsetup.sh



##### 代码分析

###### make()

SCRIPTS_DIR=~/user/buildroot/build

BUILDROOT_DIR=~/user/buildroot/

TOP_DIR=~/user/

BUILDROOT_BOARD_DIR=~/user/buildroot/output/.board



进入~/user/buildroot/configs，选择一个配置文件，文件名赋值给RK_BUILD_CONFIG

setup_board



###### setup_board()

BUILDROOT_OUTPUT_DIR=~/user/buildroot/output/$RK_BUILD_CONFIG

跟新~/user/buildroot/output/.board软链接

make -C "$BUILDROOT_DIR" "${RK_BUILD_CONFIG}_defconfig"



envsetup.sh最终的效果就是：融合生成配置文件：~/user/buildroot/output/$RK_BUILD_CONFIG/.config