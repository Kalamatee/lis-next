obj-m	+= hv_vmbus.o
obj-m	+= hv_storvsc.o
obj-m	+= hv_netvsc.o
obj-m	+= hv_utils.o
obj-m	+= hid-hyperv.o
obj-m	+= hyperv_fb.o
obj-m	+= hv_balloon.o
obj-m	+= hyperv-keyboard.o
obj-m	+= hv_sock.o

# Get machine bit length
arch=$(shell getconf LONG_BIT)

# RDMA should only build on 64 bits arch
ifeq ($(arch), 64)
        obj-m += hv_network_direct.o
endif

# Get RHEL/CentOS version information
rhel_major=$(shell grep -Eoh [0-9]+\.[0-9]+ /etc/{issue,*release} | head -1 | awk -F'.' '{ print $$1 }')
rhel_minor=$(shell grep -Eoh [0-9]+\.[0-9]+ /etc/{issue,*release} | head -1 | awk -F'.' '{ print $$2 }')
rhel_release_code=$(shell echo $$(($(rhel_major) << 8 | $(rhel_minor))))

# Kernel support for the Hyper-V PCI driver was introduced in RHEL/CentOS 7.3
# Only build the pci-hyperv driver on RHEL 7.3 and newer.
# The RHEL_RELEASE_CODE for RHEL/CentOS 7.2 = 1794.

build_pci=$(shell test $(rhel_release_code) -gt 1794; echo $$?)
ifeq ($(build_pci),0)
        obj-m	+= pci-hyperv.o
endif


# UIO underlying function are not yet in rhel6.x so make it conditional for future release
buildrdma=$(shell test $(rhel_release_code) -gt 1795; echo $$?)
ifeq ($(buildrdma),0)
        ifeq ($(arch), 64)
                obj-m   += uio_hv_generic.o
         endif
endif

ccflags-y += -I$(M)/include/ -I$(M)/arch/$(ARCH)/include -I$(M)/arch/$(ARCH)/include/uapi

hv_vmbus-y := vmbus_drv.o \
		 hv.o connection.o channel.o \
		 channel_mgmt.o ring_buffer.o arch/$(ARCH)/hyperv/hv_init.o
hv_utils-y := hv_util.o hv_kvp.o hv_snapshot.o hv_fcopy.o hv_utils_transport.o

hv_storvsc-y := storvsc_drv.o
hv_netvsc-y := netvsc_drv.o netvsc.o rndis_filter.o
hyperv_keyboard-y := hyperv-keyboard.o
hv_network_direct-y := provider.o vmbus_rdma.o hvnd_addr.o
hv_sock-y := af_hvsock.o
hv_uio_generic-y := uio_hv_generic.o
