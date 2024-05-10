include $(TOPDIR)/rules.mk

PKG_NAME:=homebox
PKG_VERSION:=0.0.0-dev.2024050301
PKG_RELEASE:=1

PKG_SOURCE_URL_FILE:=v$(PKG_VERSION).tar.gz
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_GIT_URL:=github.com/ghostnup/homebox-ssl
PKG_GIT_REF:=v$(PKG_VERSION)
PKG_SOURCE_URL:=https://codeload.$(PKG_GIT_URL)/tar.gz/$(PKG_GIT_REF)?
PKG_HASH:=a6cf5339b6a44b5c8c156c124ece802e1e40cf2656ed160cd561bf64fd46707b

PKG_BUILD_DEPENDS:=golang/host homebox/host
PKG_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0

HOST_BUILD_DIR:=$(BUILD_DIR_HOST)/${PKG_NAME}

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk
include $(TOPDIR)/feeds/packages/lang/golang/golang-package.mk

define Package/$(PKG_NAME)
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Web Servers/Proxies
  TITLE:=A Toolbox for Home Local Networks
  URL:=https://github.com/ghostnup/homebox-ssl
  DEPENDS:=$(GO_ARCH_DEPENDS)
  MENU:=1
endef

define Package/$(PKG_NAME)/description
  A Toolbox for Home Local Networks Speed Test
endef

GO_PKG_BUILD_VARS += GO111MODULE=auto
TAR_OPTIONS:=--strip-components 1 $(TAR_OPTIONS)
TAR_CMD=$(HOST_TAR) -C $(1) $(TAR_OPTIONS)

define Build/Configure
	( \
		cd $(PKG_BUILD_DIR)/server; \
		$(GO_PKG_VARS) \
		go get -d -modcacherw; \
	)
	( \
		cd $(PKG_BUILD_DIR)/server; \
		GOPATH=$(PKG_BUILD_DIR)/.go_work/build \
		go install -modcacherw github.com/go-bindata/go-bindata/...@latest; \
	)
endef

define Build/Compile
	rm -rf $(PKG_BUILD_DIR)/build/static
	mkdir -p $(PKG_BUILD_DIR)/build
	$(CP) $(HOST_BUILD_DIR)/build/static $(PKG_BUILD_DIR)/build/
	( \
		cd $(PKG_BUILD_DIR); \
		$(GO_PKG_VARS) PATH=$(PKG_BUILD_DIR)/.go_work/build/bin:$$$$PATH \
		$(MAKE) build-server; \
	)
endef

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/build/server $(1)/usr/bin/homebox
endef

define Package/$(PKG_NAME)/conffiles
/etc/config/homebox
endef

define Host/Configure
	cd $(HOST_BUILD_DIR)/web && rm -f package-lock.json && npm --prefer-offline install
endef

define Host/Compile
	cd $(HOST_BUILD_DIR) && $(MAKE) build-web
endef

define Host/Install
endef

define Host/Clean
	rm -f $(HOST_BUILD_DIR)/build/static
endef

$(eval $(call HostBuild))
$(eval $(call BuildPackage,homebox))
