# SPDX-License-Identifier: GPL-2.0-only
#
# Sony released these techpack overlays in the flat qcom/ directory, but the
# LineageOS build has to tell base device trees and techpack overlays apart:
# kernel.mk moves everything directly under arch/arm64/boot/dts/vendor/*/ into
# the "base" set and merge_dtbs.py folds whatever lies deeper into it. Keeping
# them one level down is also how the official sm6475-devicetrees is laid out.
#
# The .dtsi files they include stayed in the parent directory, as released.
DTC_INCLUDE += $(srctree)/$(src)/..

dtbo-$(CONFIG_ARCH_PARROT) += \
	parrot-vidc.dtbo

always-y	:= $(dtbo-y) $(dtb-y)
clean-files	:= *.dtb *.dtbo
