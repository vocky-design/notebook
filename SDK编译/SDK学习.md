build.sh实际地址为：device/rockchip/common/build.sh

.BoardConfig.mk实际地址为：device/rockchip/.target_product/具体选择的板级配置文件

alientek_rv1126_defconfig地址为：kernel/arch/arm/configs/alientek_rv1126_defconfig

.config的地址为：kernel/.config



build.sh调用流程:

build_select_board

​	choose_target_board

​	创建软链接.BoardConfig.mk



make arch=arm alientek_rv1126_defconfig



