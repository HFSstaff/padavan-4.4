SRC_NAME=json-c-json-c-0.17-20230812
SRC_URL=https://github.com/json-c/json-c/archive/json-c-0.17-20230812.tar.gz
ARCHIVE_NAME=json-c-0.17-20230812

all: download_test extract_test config_test
	$(MAKE) -j$(HOST_NCPU) -C $(SRC_NAME)

download_test:
	( if [ ! -f $(ARCHIVE_NAME).tar.gz ]; then \
		wget -t5 --timeout=20 --no-check-certificate $(SRC_URL); \
	fi )

extract_test:
	( if [ ! -d $(SRC_NAME) ]; then \
		tar zxf $(ARCHIVE_NAME).tar.gz; \
		patch -d $(SRC_NAME) -p1 -i ../json-c-0.17.patch ; \
	fi )

config_test:
	( if [ -f ./config_done ]; then \
		echo "the same configuration"; \
	else \
		make configure && touch config_done; \
	fi )

configure:
	( cd $(SRC_NAME) ; \
	sh autogen.sh; \
	sed -e 's/#undef malloc.*//g' -e 's/#undef realloc.*//g' -i config.h.in; \
	./configure \
		--prefix=$(STAGEDIR) \
		--enable-static \
		--enable-shared \
		--host=$(HOST_TARGET) \
		--build=$(HOST_BUILD) ; \
	)

clean:
	if [ -f $(SRC_NAME)/Makefile ] ; then \
		$(MAKE) -C $(SRC_NAME) distclean ; \
	fi ; \
	rm -f config_done

install:
	$(MAKE) -C $(SRC_NAME) install DESTDIR=""

romfs:
	cp -fP $(DESTDIR)/lib/libjson-c.so* $(ROMFSDIR)/lib
