# Maintainer: Xilin Wu <sophon@radxa.com>
# Upstream: Qualcomm Adreno GPU OpenCL prebuilt binaries (gfx-adreno.le.0.0)

pkgname=qcom-adreno-opencl-bin
pkgver=1.887.7
pkgrel=1
pkgdesc="Qualcomm Adreno GPU OpenCL driver (prebuilt binary)"
arch=('aarch64')
url="https://softwarecenter.qualcomm.com"
license=('LicenseRef-Qualcomm-EULA')
depends=('gcc-libs' 'glib2' 'glibc' 'opencl-icd-loader' 'zlib')
provides=('opencl-driver')
options=('!strip')

_datestamp=261116

source=("https://qartifactory-edge.qualcomm.com/artifactory/qsc_releases/software/chip/component/gfx-adreno.le.0.0/${_datestamp}/prebuilt_yocto/qcom-adreno_${pkgver}_armv8a.tar.gz")
sha256sums=('6631d73be86069f3e410853ace7cc6c6574c13bf6c0c8d319f7582f83148a7ee')
noextract=("qcom-adreno_${pkgver}_armv8a.tar.gz")

prepare() {
    mkdir -p adreno
    tar xzf "$srcdir/qcom-adreno_${pkgver}_armv8a.tar.gz" -C adreno
}

package() {
    local _src="$srcdir/adreno"

    install -Dm644 "$_src/usr/include/CL/cl_ext_qcom.h" \
        "$pkgdir/usr/include/CL/cl_ext_qcom.h"

    # OpenCL ICD, runtime and dynamically loaded compiler components.
    # The ICD no longer links against libOpenCL.so.1, so the system
    # ICD loader (ocl-icd) is used instead of Qualcomm's.
    for _lib in libOpenCL_adreno.so.1 libgsl.so.1 libadreno_utils.so.1 \
                libCB.so.1 libq3dtools_adreno.so.1 libq3dtools_esx.so.1 \
                libQCGPUCompiler.so.1 libQCGPUCompilerCore.so.1 \
                libqclccompiler.so.1 libqclccompiler-lgc.so.1 \
                libqgpucompiler-lgc.so.1 libqgpucompilercore-lgc.so.1 \
                libkcl.so.1 libkernelmanager.so.1; do
        install -Dm755 "$_src/usr/lib/$_lib" "$pkgdir/usr/lib/$_lib"
    done

    # Qualcomm's compiler plugins dlopen this library without its SONAME.
    ln -s 'libQCGPUCompilerCore.so.1' "$pkgdir/usr/lib/libQCGPUCompilerCore.so"

    install -Dm644 "$_src/etc/OpenCL/vendors/adrenocl.icd" \
        "$pkgdir/etc/OpenCL/vendors/adrenocl.icd"

    ln -s 'libadreno_utils.so.1' "$pkgdir/usr/lib/libadreno_utils.so"
    install -dm755 "$pkgdir/usr/lib/pkgconfig"
    cat > "$pkgdir/usr/lib/pkgconfig/adreno_utils.pc" <<'PC'
prefix=/usr
includedir=${prefix}/include
libdir=${prefix}/lib

Name: AdrenoUtils
Description: AdrenoUtils package
Version: 1.0.0
Libs: -L${libdir} -ladreno_utils
Cflags: -I${includedir}
PC

    install -Dm644 "$_src/NOTICE" \
        "$pkgdir/usr/share/licenses/$pkgname/NOTICE"
    install -Dm644 "$_src/NO.LOGIN.BINARY.LICENSE.QTI.pdf" \
        "$pkgdir/usr/share/licenses/$pkgname/NO.LOGIN.BINARY.LICENSE.QTI.pdf"
}
