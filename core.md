# Core

> It's not an embedded Linux distribution – it creates a custom one for you. The Yocto Project is an open source collaboration project that provides templates, tools and methods to help you create custom Linux-based systems for embedded products regardless of the hardware architecture. It was founded in 2010 as a collaboration among many hardware manufacturers, open-source operating systems vendors, and electronics companies to bring some order to the chaos of embedded Linux development.
>
> The Yocto Project is a Linux Foundation workgroup whose goal is to produce tools and processes that will enable the creation of Linux distributions for embedded software that are independent of the underlying architecture of the embedded software itself.

* [Yocto Project Homepage](https://www.yoctoproject.org/)
* [Yocto Project Quick Start Guide](http://www.yoctoproject.org/docs/2.1/yocto-project-qs/yocto-project-qs.html)
* [Yocto Developer Manual](http://www.yoctoproject.org/docs/1.6.1/dev-manual/dev-manual.html)
* [Yocto Project Reference Manual](http://www.yoctoproject.org/docs/latest/ref-manual/ref-manual.html)
* [Yocto Kernel Developer Manual](http://www.yoctoproject.org/docs/1.6.1/kernel-dev/kernel-dev.html)
* [Yocto Project and Embedded OS Webinar](http://www.intel.com/content/www/us/en/education/university/galileo-university-curricula/yocto-project-and-embedded-os-webinar-replay.html#)
* [Yocto Manage a Private Opkg Repository](http://www.jumpnowtek.com/yocto/Managing-a-private-opkg-repository.html)
* [Code Project Adding 3rd Party Components to Yocto/OpenEmbedded Linux](http://www.codeproject.com/Articles/774826/Adding-rd-party-components-to-Yocto-OpenEmbedded-L)
* [Yocto Git Server](http://git.yoctoproject.org/)
* [Elizabeth Flanagan The Yocto Project](http://www.aosabook.org/en/yocto.html)

## Components

* BitBake

  > the build engine.

* OpenEmbedded-Core

  > a set of base layers

* Poky

  > the reference system. It is a collection of projects and tools, used to bootstrap a new distribution based on the Yocto Project

## Embedded Architecture Workflow

![](http://www.yoctoproject.org/docs/2.0/yocto-project-qs/figures/yocto-environment.png)

## Recipe Reporting System

* 
## Some Theory

* poky-7ca60ec8bff7656b4e52f5a4d238913e851da089\meta\conf\machine
* poky-7ca60ec8bff7656b4e52f5a4d238913e851da089\meta\recipes-kernel\oprofile\oprofile\_1.1.0.bb
* poky-7ca60ec8bff7656b4e52f5a4d238913e851da089\meta\recipes-kernel\oprofile\oprofile.inc

```bash
DEPENDS = "popt binutils"
RDEPENDS_${PN} = "binutils-symlinks"
RRECOMMENDS_${PN} = "kernel-vmlinux"
```

```bash
SRC_URI = "${SOURCEFORGE_MIRROR}/${BPN}/${BPN}-${PV}.tar.gz \
           file://acinclude.m4 \
           file://automake-foreign.patch \
           file://oprofile-cross-compile-tests.patch \
           file://run-ptest \
           file://root-home-dir.patch \
           file://0001-Add-rmb-definition-for-NIOS2-architecture.patch"
```

### Upstream-Status: Pending

```bash
Prevent running check tests on host if cross compiling

This patch enables running the 'make check' tests on the target
in a cross-compiled environment. If not cross-compiling, then 'make
 check' builds and executes the tests; no change from this patch.
In a cross-compiling environment, the make variable CROSS_COMPILE is
set which bypasses assiging tests to the makekfile variable TESTS.
Since TESTS is empty, the 'make check' process never tries to run the
tests on the hosts.  On the target, the tests must be run manually.

Also, in the libutil++ tests, a makefile variable SRCDIR is passed into
the compilation phase, pointing to the runtime location of the test
'file-manip-tests'.  The mechanism used for a host test, based on
'topdir' doesn't work.  Instead, if CROSS_COMPILE is set, the
makefile takes the path of SRCDIR from the build environment and not
from an expression based on the host path 'topdir'.

Upstream-Status: Pending

Signed-off-by: Dave Lerner <dave.lerner@windriver.com>

diff --git a/configure.ac b/configure.ac
index 41ece64..ce5a16f 100644
--- a/configure.ac
+++ b/configure.ac
@@ -392,6 +392,7 @@ AC_ARG_ENABLE(account-check,
     enable_account_check=$enableval, enable_account_check=yes)

 AM_CONDITIONAL(CHECK_ACCOUNT, test "x$enable_account_check" = "xyes")
+AM_CONDITIONAL(CROSS_COMPILE, test "x$cross_compiling" = "xyes")

 AC_SUBST(OP_CFLAGS)
 AC_SUBST(OP_CXXFLAGS)
diff --git a/libdb/tests/Makefile.am b/libdb/tests/Makefile.am
index 8a69003..d820090 100644
--- a/libdb/tests/Makefile.am
+++ b/libdb/tests/Makefile.am
@@ -13,4 +13,6 @@ check_PROGRAMS = db_test
 db_test_SOURCES = db_test.c
 db_test_LDADD = ../libodb.a ../../libutil/libutil.a

+if ! CROSS_COMPILE
 TESTS = ${check_PROGRAMS}
+endif
diff --git a/libop/tests/Makefile.am b/libop/tests/Makefile.am
index 8a79eb5..6d417c4 100644
--- a/libop/tests/Makefile.am
+++ b/libop/tests/Makefile.am
@@ -33,4 +33,6 @@ load_events_files_tests_LDADD = ${COMMON_LIBS}
 mangle_tests_SOURCES = mangle_tests.c
 mangle_tests_LDADD = ${COMMON_LIBS}

+if ! CROSS_COMPILE
 TESTS = ${check_PROGRAMS} utf8_checker.sh
+endif
diff --git a/libregex/tests/Makefile.am b/libregex/tests/Makefile.am
index 6f19838..1d176f9 100644
--- a/libregex/tests/Makefile.am
+++ b/libregex/tests/Makefile.am
@@ -18,4 +18,6 @@ java_test_LDADD = \

 EXTRA_DIST = mangled-name.in

+if ! CROSS_COMPILE
 TESTS = ${check_PROGRAMS}
+endif
diff --git a/libutil++/tests/Makefile.am b/libutil++/tests/Makefile.am
index 51af031..a01ea2d 100644
--- a/libutil++/tests/Makefile.am
+++ b/libutil++/tests/Makefile.am
@@ -1,7 +1,9 @@

 REALPATH= readlink -f

+if ! CROSS_COMPILE
 SRCDIR := $(shell $(REALPATH) $(topdir)/libutil++/tests/ )
+endif
...
```

* poky-7ca60ec8bff7656b4e52f5a4d238913e851da089\meta\conf\distro
* poky-7ca60ec8bff7656b4e52f5a4d238913e851da089\meta-poky\conf\distro

```bash
DISTRO = "poky"
DISTRO_NAME = "Poky (Yocto Project Reference Distro)"
DISTRO_VERSION = "2.1+snapshot-${DATE}"
DISTRO_CODENAME = "master"
SDK_VENDOR = "-pokysdk"
SDK_VERSION := "${@'${DISTRO_VERSION}'.replace('snapshot-${DATE}','snapshot')}"

MAINTAINER = "Poky <poky@yoctoproject.org>"

TARGET_VENDOR = "-poky"

LOCALCONF_VERSION = "1"
...
SANITY_TESTED_DISTROS ?= " \
            poky-1.7 \n \
            poky-1.8 \n \
            poky-2.0 \n \
            Ubuntu-14.04 \n \
            Ubuntu-14.10 \n \
            Ubuntu-15.04 \n \
            Ubuntu-15.10 \n \
            Fedora-21 \n \
            Fedora-22 \n \
            Fedora-23 \n \
            CentOS-6.* \n \
            CentOS-7.* \n \
            Debian-7.* \n \
            Debian-8.* \n \
            openSUSE-project-13.2 \n \
            "
```

## OpenEmbedded Layers and Recipes

[https://layers.openembedded.org/layerindex/branch/master/layers/](https://layers.openembedded.org/layerindex/branch/master/layers/)

> meta-intel
>
> > Intel board support common layer \(official\) Machine \(BSP\) git://git.yoctoproject.org/meta-intel
>
> meta-intel-edison-bsp
>
> > Intel Edison module support Machine \(BSP\) git://git.yoctoproject.org/meta-intel-edison
>
> meta-intel-quark
>
> > Intel Quark platform support Machine \(BSP\) git://git.yoctoproject.org/meta-intel-quark

