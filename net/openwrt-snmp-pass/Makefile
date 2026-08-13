#
# Copyright (C) 2026 Kamil Bienkiewicz
#
# This is free software, licensed under the GNU General Public License v2.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=openwrt-snmp-pass
PKG_VERSION:=1.0.0
PKG_RELEASE:=1
PKG_MAINTAINER:=Kamil Bienkiewicz <kamil.bienkiewicz@gmail.com>
PKG_LICENSE:=GPL-2.0-only

include $(INCLUDE_DIR)/package.mk

define Package/openwrt-snmp-pass/Default
  SECTION:=net
  CATEGORY:=Network
  MAINTAINER:=Kamil Bienkiewicz <kamil.bienkiewicz@gmail.com>
  PKGARCH:=all
endef

define Package/openwrt-snmp-wireless
  $(call Package/openwrt-snmp-pass/Default)
  TITLE:=SNMP pass_persist handler for wireless metrics
  DEPENDS:=+snmpd +iwinfo +ubus +jsonfilter +iw
endef

define Package/openwrt-snmp-wireless/description
 A net-snmp pass_persist handler exporting per-VAP 802.11 metrics under
 OPENWRT-WIRELESS-MIB (.1.3.6.1.4.1.66510.1.10, OpenWrt's IANA enterprise
 number): associated client counts, negotiated tx/rx rate min/avg/max, SNR
 min/avg/max, noise floor, channel utilization, frequency and tx power.

 Radios and VAPs are discovered live at request time from ubus and iwinfo,
 so no per-interface configuration is needed. Table rows are indexed by the
 kernel ifIndex, so they join directly onto ifTable/ifXTable entries for the
 same interface.

 The MIB modules (OPENWRT-MIB, OPENWRT-WIRELESS-MIB) are installed to
 /usr/share/snmp/mibs.
endef

define Package/openwrt-snmp-sensors
  $(call Package/openwrt-snmp-pass/Default)
  TITLE:=SNMP pass_persist handler for temperature and fan sensors
  DEPENDS:=+snmpd
endef

define Package/openwrt-snmp-sensors/description
 A net-snmp pass_persist handler exporting thermal and fan readings under
 LM-SENSORS-MIB (.1.3.6.1.4.1.2021.13.16), so that generic SNMP managers
 pick them up without device-specific configuration.

 Temperatures come from /sys/devices/virtual/thermal/thermal_zone*, fan
 speeds from /sys/class/hwmon/*/fan*_input. A tachometer input is required
 for fan readings (e.g. kmod-hwmon-pwmfan); PWM-only fans with no readback
 expose nothing here.

 This package reads sysfs only and has no dependencies beyond snmpd.
endef

define Build/Prepare
endef

define Build/Configure
endef

define Build/Compile
endef

define Package/openwrt-snmp-wireless/install
	$(INSTALL_DIR) $(1)/usr/libexec/openwrt-snmp
	$(INSTALL_BIN) ./files/openwrt-snmp-pass.sh $(1)/usr/libexec/openwrt-snmp/
	$(INSTALL_BIN) ./files/wlClients.sh $(1)/usr/libexec/openwrt-snmp/
	$(INSTALL_BIN) ./files/wlFrequency.sh $(1)/usr/libexec/openwrt-snmp/
	$(INSTALL_BIN) ./files/wlInterfaces.sh $(1)/usr/libexec/openwrt-snmp/
	$(INSTALL_BIN) ./files/wlNoiseFloor.sh $(1)/usr/libexec/openwrt-snmp/
	$(INSTALL_BIN) ./files/wlRate.sh $(1)/usr/libexec/openwrt-snmp/
	$(INSTALL_BIN) ./files/wlSNR.sh $(1)/usr/libexec/openwrt-snmp/
	$(INSTALL_DIR) $(1)/usr/share/snmp/mibs
	$(INSTALL_DATA) ./files/OPENWRT-MIB $(1)/usr/share/snmp/mibs/
	$(INSTALL_DATA) ./files/OPENWRT-WIRELESS-MIB $(1)/usr/share/snmp/mibs/
	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_BIN) ./files/openwrt-snmp-wireless.defaults $(1)/etc/uci-defaults/openwrt-snmp-wireless
endef

define Package/openwrt-snmp-sensors/install
	$(INSTALL_DIR) $(1)/usr/libexec/openwrt-snmp
	$(INSTALL_BIN) ./files/lm-sensors-pass.sh $(1)/usr/libexec/openwrt-snmp/
	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_BIN) ./files/openwrt-snmp-sensors.defaults $(1)/etc/uci-defaults/openwrt-snmp-sensors
endef

define Package/openwrt-snmp-wireless/postrm
#!/bin/sh
[ -n "$$IPKG_INSTROOT" ] && exit 0
uci -q delete snmpd.openwrt_wireless && uci commit snmpd
/etc/init.d/snmpd running >/dev/null 2>&1 && /etc/init.d/snmpd restart
exit 0
endef

define Package/openwrt-snmp-sensors/postrm
#!/bin/sh
[ -n "$$IPKG_INSTROOT" ] && exit 0
uci -q delete snmpd.openwrt_sensors && uci commit snmpd
/etc/init.d/snmpd running >/dev/null 2>&1 && /etc/init.d/snmpd restart
exit 0
endef

$(eval $(call BuildPackage,openwrt-snmp-wireless))
$(eval $(call BuildPackage,openwrt-snmp-sensors))
