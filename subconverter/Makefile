# SPDX-License-Identifier: GPL-3.0-only
#
# Copyright (C) 2021 ImmortalWrt.org

include $(TOPDIR)/rules.mk

PKG_NAME:=subconverter
PKG_RELEASE:=6

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/MetaCubeX/subconverter.git
PKG_SOURCE_DATE:=2026-1-21
PKG_SOURCE_VERSION:=3f50ea31ea40d47c771010701de71a48e1261b79
PKG_MIRROR_HASH:=be1c13c79ba6a309c938e8fbaf4d4fed744d333e9dba2beb6e600838c194ca3b
# 保持原始版本号
PKG_VERSION:=0.9.2~$(PKG_SOURCE_DATE)

PKG_LICENSE:=GPL-3.0
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Tianling Shen <cnsztl@immortalwrt.org>

PKG_BUILD_DEPENDS:=jpcre2 rapidjson toml11
PKG_BUILD_FLAGS:=gc-sections

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/subconverter
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=Utility to convert between various proxy subscription format
  URL:=https://github.com/tindy2013/subconverter
  DEPENDS:=+libatomic +libcron +libcurl +libevent2 +libpcre2 \
    +libquickjs +libstdcpp +libyaml-cpp
endef

CMAKE_OPTIONS+= -DCMAKE_EXE_LINKER_FLAGS="-latomic"

TARGET_CXXFLAGS := $(filter-out -O%,$(TARGET_CXXFLAGS)) -O3 -fno-lto
TARGET_CXXFLAGS += -Wall -Wextra

define Package/subconverter/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/subconverter $(1)/usr/bin/subconverter
	$(INSTALL_DIR) $(1)/etc/subconverter
	$(CP) $(PKG_BUILD_DIR)/base/* $(1)/etc/subconverter/

	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/subconverter.init $(1)/etc/init.d/subconverter
endef

# 关键：在 APK 打包前重写版本号
define Build/Compile
	$(call Build/Compile/Default)
	# 创建一个干净的版本号用于 APK
	$(eval APK_VERSION:=0.9.2.20260121)
	# 在构建目录中创建一个文件来存储干净的版本号
	echo "$(APK_VERSION)" > $(PKG_BUILD_DIR)/.apk_version
endef

# 覆盖 APK 打包过程
define BuildPackage/subconverter
	$(eval $(call Build/DefaultTargets,$(1)))
	
	# 获取干净的版本号
	APK_VERSION:=$(shell cat $(PKG_BUILD_DIR)/.apk_version 2>/dev/null || echo "0.9.2")
	
	# 重新生成 APK 包
	$(call Build/APK/Package,$(1),$(APK_VERSION))
endef

# 自定义 APK 打包函数
define Build/APK/Package
	@echo "Building APK package with version: $(2)"
	# 创建必要的目录
	mkdir -p $(PKG_BUILD_DIR)/apk-aarch64_cortex-a53/$(1)/
	mkdir -p $(PKG_BUILD_DIR)/ipkg-aarch64_cortex-a53/$(1)/lib/apk/packages/
	
	# 生成必要的脚本文件
	( echo "#!/bin/sh"; echo "[ \"\${IPKG_NO_SCRIPT}\" = \"1\" ] && exit 0"; echo "[ -s "\${IPKG_INSTROOT}/lib/functions.sh" ] || exit 0"; echo ". \${IPKG_INSTROOT}/lib/functions.sh"; echo 'export root="${IPKG_INSTROOT}"'; echo 'export pkgname="$(1)"'; echo "add_group_and_user"; echo "default_postinst"; ) > $(PKG_BUILD_DIR)/apk-aarch64_cortex-a53/$(1)/post-install;
	
	( echo "#!/bin/sh"; echo 'export PKG_UPGRADE=1'; [ ! -f $(PKG_BUILD_DIR)/apk-aarch64_cortex-a53/$(1)/post-install ] || sed '/^\s*#!/d' "$(PKG_BUILD_DIR)/apk-aarch64_cortex-a53/$(1)/post-install"; ) > $(PKG_BUILD_DIR)/apk-aarch64_cortex-a53/$(1)/post-upgrade;
	
	( echo "#!/bin/sh"; echo "[ -s "\${IPKG_INSTROOT}/lib/functions.sh" ] || exit 0"; echo ". \${IPKG_INSTROOT}/lib/functions.sh"; echo 'export root="${IPKG_INSTROOT}"'; echo 'export pkgname="$(1)"'; echo "default_prerm"; ) > $(PKG_BUILD_DIR)/apk-aarch64_cortex-a53/$(1)/pre-deinstall;
	
	# 生成文件列表
	(cd $(PKG_BUILD_DIR)/ipkg-aarch64_cortex-a53/$(1) && find . -type f,l -printf "/%P\n" | sort > $(PKG_BUILD_DIR)/ipkg-aarch64_cortex-a53/$(1)/lib/apk/packages/$(1).list)
	
	# 使用干净的版本号打包 APK
	$(STAGING_DIR_HOST)/bin/fakeroot $(STAGING_DIR_HOST)/bin/apk mkpkg \
		--info "name:$(1)" \
		--info "version:$(2)-r$(PKG_RELEASE)" \
		--info "description:Utility to convert between various proxy subscription format" \
		--info "arch:aarch64_cortex-a53" \
		--info "license:GPL-3.0" \
		--info "origin:feeds/base/subconverter" \
		--info "url:https://github.com/tindy2013/subconverter" \
		--info "maintainer:Tianling Shen <cnsztl@immortalwrt.org>" \
		--info "provides:$(1)-any" \
		--script "post-install:$(PKG_BUILD_DIR)/apk-aarch64_cortex-a53/$(1)/post-install" \
		--script "post-upgrade:$(PKG_BUILD_DIR)/apk-aarch64_cortex-a53/$(1)/post-upgrade" \
		--script "pre-deinstall:$(PKG_BUILD_DIR)/apk-aarch64_cortex-a53/$(1)/pre-deinstall" \
		--info "depends:libc libatomic1 libcron libcurl4 libevent2-7 libpcre2 libquickjs libstdcpp6 libyaml-cpp0.8" \
		--files "$(PKG_BUILD_DIR)/ipkg-aarch64_cortex-a53/$(1)" \
		$(if $(CONFIG_BUILD_SIGNED_PACKAGES),--sign $(TOPDIR)/private-key.pem,) \
		--output "$(BIN_DIR)/packages/aarch64_cortex-a53/base/$(1)-$(2)-r$(PKG_RELEASE).apk"
endef

# 使用我们自定义的 BuildPackage
$(eval $(call BuildPackage/subconverter,subconverter))
