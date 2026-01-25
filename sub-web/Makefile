#
# This is free software, licensed under the Apache License, Version 2.0 .
#

include $(TOPDIR)/rules.mk

PKG_NAME:=sub-web
PKG_VERSION:=20260126
PKG_RELEASE:=3

PKG_SOURCE:=$(PKG_NAME)-dist-$(PKG_VERSION).zip
PKG_SOURCE_URL:=https://github.com/Dawneng/sub-web/releases/download/$(PKG_VERSION)
PKG_HASH:=92a93a7021c202095c170ce557ca2a712cd81bccca5ccdb47fc2ca68b4b6df7c

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE

include $(INCLUDE_DIR)/package.mk

define Package/sub-web
  SECTION:=net
  CATEGORY:=Network
  URL:=https://github.com/Dawneng/sub-web
  TITLE:=A WebUI for generating proxy subscription url
  DEPENDS:=+subconverter
  PKGARCH:=all
endef

UNZIP_CMD:=unzip -q -d $(PKG_BUILD_DIR)/dist $(DL_DIR)/$(PKG_SOURCE)

define Package/sub-web/description
  Based on vue-cli and subconverter, for generating proxy subscription
  url automatically.
endef

define Build/Configure
endef

define Build/Compile
endef

define Package/sub-web/install
	$(INSTALL_DIR) $(1)/www/sub-web
	$(CP) $(PKG_BUILD_DIR)/dist/* $(1)/www/sub-web/
endef

$(eval $(call BuildPackage,sub-web))

