#SERIALPORT is the port to communicate with the debugger, usually 0x3f8, on db's system it's 0xef00
SERIALPORT=0x3f8 #bochs
#SERIALPORT=0xbf00 #intel
#SERIALPORT=0xec00 #amd
#SERIALPORT=0


all:
	make -C vmm	all SERIALPORT=${SERIALPORT}
	make -C common all SERIALPORT=${SERIALPORT}
	make -C parsevmmmap all SERIALPORT=${SERIALPORT}
	parsevmmmap/parsevmmmap	
	make -C vmloader all SERIALPORT=${SERIALPORT}
	make -C parsevmloadermap all SERIALPORT=${SERIALPORT}
	parsevmloadermap/parsevmloadermap
	make -C bootsector all SERIALPORT=${SERIALPORT}
	make -C imagemaker all SERIALPORT=${SERIALPORT}

install: all
	imagemaker/imagemaker
	chmod 777 vmdisk.img
	chmod 777 vmdisk144.img
	sync	

usb: install
	dd if=vmdisk.img of=/dev/sdb
	sync
	
disk: install
	dd if=vmdisk.img of=/dev/fd0
	sync

net: install
	-rm -f /mnt/cebin/vmdisk.img
	cp vmdisk.img /mnt/cebin
	sync

cdimage: install
	-mkdir iso
	-mkdir iso/boot  
	cp vmdisk144.img iso/boot
	cd iso
	mkisofs -r -b boot/vmdisk144.img -c boot/boot.catalog -o vmcd.iso iso/
	

release: clean install cdimage
	-rm -rf release	
	mkdir release	
	cp vmcd.iso release
	cp vmdisk.img release
	cp docs/readme.txt release
	cp vmm/docs/vmcall.txt release
	rar a dbvm.rar release/*
	@echo "Your release file is ready"

clean:
	@make -C imagemaker clean
	@make -C parsevmloadermap clean
	@make -C parsevmmmap clean
	@make -C bootsector clean
	@make -C vmloader clean
	@make -C vmm clean
	@make -C common clean
	@-rm -f *.bin
	@-rm -f *.img
	@-rm -f *.map
	@-rm -f *~
	@-rm -rf iso
	@-rm -rf release
	@-rm -f vmcd.iso
	@-rm -f dbvm.rar
	
