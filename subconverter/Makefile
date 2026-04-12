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
# 修改这里：移除波浪线，用点号分隔
PKG_VERSION:=0.9.2.$(subst -,.,$(PKG_SOURCE_DATE))

PKG_LICENSE:=GPL-3.0
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Tianling Shen <cnsztl@immortalwrt.org>

PKG_BUILD_DEPENDS:=jpcre2 rapidjson toml11
# 修改这里：移除 lto
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

# 修改这里：添加 -fno-lto
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

$(eval $(call BuildPackage,subconverter))
