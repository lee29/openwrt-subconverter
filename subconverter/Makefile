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
# 恢复原始版本号
PKG_VERSION:=0.9.2~$(PKG_SOURCE_DATE)

PKG_LICENSE:=GPL-3.0
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Tianling Shen <cnsztl@immortalwrt.org>

PKG_BUILD_DEPENDS:=jpcre2 rapidjson toml11
# 移除了 lto
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

# mips(el) & glibc require it
CMAKE_OPTIONS+= -DCMAKE_EXE_LINKER_FLAGS="-latomic"

# 添加了 -fno-lto
TARGET_CXXFLAGS := $(filter-out -O%,$(TARGET_CXXFLAGS)) -O3 -fno-lto
TARGET_CXXFLAGS += -Wall -Wextra

# 添加这个函数来重写版本号
define Build/Configure
	# 调用父类的配置
	$(call Build/Configure/Default)
	# 在 CMake 配置中修改版本号
	$(SED) 's/SET(CPACK_PACKAGE_VERSION ".*")/SET(CPACK_PACKAGE_VERSION "0.9.2")/g' $(PKG_BUILD_DIR)/CMakeLists.txt || true
endef

define Package/subconverter/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/subconverter $(1)/usr/bin/subconverter
	$(INSTALL_DIR) $(1)/etc/subconverter
	$(CP) $(PKG_BUILD_DIR)/base/* $(1)/etc/subconverter/

	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/subconverter.init $(1)/etc/init.d/subconverter
endef

# 在打包前重写版本号
define Package/subconverter/preinst
#!/bin/sh
# 在安装前重写版本号
sed -i 's/0\.9\.2~2026-1-21/0.9.2.20260121/g' "$${IPKG_INSTROOT}/usr/bin/subconverter" 2>/dev/null || true
exit 0
endef

$(eval $(call BuildPackage,subconverter))
