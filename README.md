gitian.sigs.mdc
===============

Trusted Build Process signatures


This repository contains gitian signatures for Mediterraneancoin.  It should be updated on each release.
Random members of the public are encouraged to participate in this process in order to better secure the integrity of the Gitian builds.

## Generating Gitian Sigs

 Checkout mediterraneancoin, gitian-builder and gitian.sigs.mdc

	mkdir workdir
	cd workdir
	git clone https://github.com/devrandom/gitian-builder.git
	git clone https://github.com/mrtexaznl/mediterraneancoin
	git clone https://github.com/mrtexaznl/gitian.sigs.mdc.git

 Create Gitian Base VM Images

	cd ./gitian-builder
        sudo bin/make-base-vm --arch i386
        sudo bin/make-base-vm --arch amd64
        sudo bin/make-base-vm --arch amd64 --suite precise

 Fetch and build inputs: (first time, zor when dependency versions change)

	mkdir -p inputs; cd inputs/
	wget 'http://miniupnp.free.fr/files/download.php?file=miniupnpc-1.6.tar.gz' -O miniupnpc-1.6.tar.gz
	wget 'http://www.openssl.org/source/openssl-1.0.1c.tar.gz'
	wget 'http://download.oracle.com/berkeley-db/db-4.8.30.NC.tar.gz'
	wget 'http://downloads.sourceforge.net/project/libpng/zlib/1.2.6/zlib-1.2.6.tar.gz'
	wget 'ftp://ftp.simplesystems.org/pub/libpng/png/src/libpng-1.5.9.tar.gz'
	wget 'http://fukuchi.org/works/qrencode/qrencode-3.2.0.tar.bz2'
	wget 'http://downloads.sourceforge.net/project/boost/boost/1.54.0/boost_1_54_0.tar.bz2'
	wget 'https://svn.boost.org/trac/boost/raw-attachment/ticket/7262/boost-mingw.patch'
	mv boost-mingw.patch boost-mingw-gas-cross-compile-2013-03-03.patch
	wget 'http://releases.qt-project.org/qt4/source/qt-everywhere-opensource-src-4.8.3.tar.gz'
	cd ..
	./bin/gbuild ../mediterraneancoin/contrib/gitian-descriptors/boost-win32.yml
	mv build/out/inputs/boost-win32-1.54.0-gitian-r6.zip inputs/
	./bin/gbuild ../mediterraneancoin/contrib/gitian-descriptors/deps-win32.yml
	mv build/out/bitcoin-deps-win32-gitian-r9.zip inputs/
	./bin/gbuild ../mediterraneancoin/contrib/gitian-descriptors/qt-win32.yml
	mv build/out/qt-win32-4.8.3-gitian-r4.zip inputs/

 Choose your GPG identity and git tag VERSION that you wish to build.

	export SIGNER=(your gitian key, ie bluematt, sipa, etc)
	export VERSION=master-0-8

 Build mediterraneancoind and mediterraneancoin-qt on Linux32, Linux64, and Win32:

	sudo ./bin/gbuild --commit mediterraneancoin=${VERSION} ../mediterraneancoin/contrib/gitian-descriptors/gitian.yml
	sudo ./bin/gsign --signer $SIGNER --release ${VERSION} --destination ../gitian.sigs.mdc/ ../mediterraneancoin/contrib/gitian-descriptors/gitian.yml
	sudo ./bin/gbuild --commit mediterraneancoin=${VERSION} ../mediterraneancoin/contrib/gitian-descriptors/gitian-win32.yml
	sudo ./bin/gsign --signer $SIGNER --release ${VERSION}-win32 --destination ../gitian.sigs.mdc/ ../mediterraneancoin/contrib/gitian-descriptors/gitian-win32.yml

 It is customary to rename your directories in gitian.sigs.mdc to match your github username.  Submit a Pull Request against this repo.  If you are uncertain, please see previous commits for examples.
