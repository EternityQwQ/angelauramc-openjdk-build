# angelauramc-openjdk-build 

**This branch is for OpenJDK 26 (iOS only, buildjre26).**

Based on `duy/buildjre17-21-25-ios` (OpenJDK 17/21/25 iOS port). All porting/build work runs in GitHub Actions, no local build.

JDK source: `git clone --depth 1 https://github.com/openjdk/jdk26u openjdk-26` (see `5_clonejdk.sh`). iOS patches baseline copied from `patches/jre_25/ios/` to `patches/jre_26/ios/` (`1_jdk26u_ios.diff`, `2_mirror_mapping.diff`) — adapt in Actions if `git apply` rejects.

Based on [Java for Android](http://openjdk.java.net/projects/mobile/android.html) and [the PojavLauncher variant](https://github.com/PojavLauncherTeam/android-openjdk-build-multiarch)

## Building 

### Setup
#### Android
**Note:** We use Ubuntu 24.04 LTS to build our JDKs. Adapt these dependencies to your distribution, it should build fine.
- Install `autoconf`, `python3`, `python-is-python3`, `unzip`, `zip`, `systemtap-sdt-dev`, `libxtst-dev`, `libasound2-dev`, `libelf-dev`, `libfontconfig1-dev`, `libx11-dev`, `libxext-dev`, `libxrandr-dev`, `libxrender-dev`, `libxtst-dev`, `libxt-dev`.
- If building JDK 17, install `openjdk-17-jdk`. For 21, `openjdk-21-jdk`.
- Install Android NDK r27b.

#### iOS
- Install latest Xcode on your Mac.
- Install JDK 26 (`/usr/libexec/java_home -v 26` must work, used as `--with-boot-jdk` in `6_buildjdk.sh`).
- iOS target: `TARGET=aarch64-apple-ios`, `BUILD_IOS=1`, `TARGET_VERSION=26`, runner `J316sAP` (see `.github/workflows/build.yml`). No local build — push to this branch triggers Actions.

### Platform and architecture specific environment variables
<table>
      <thead>
        <tr>
          <th></th>
          <th align="center" colspan="7">Environment variables</th>
        </tr>
        <tr>
          <th>Platform - Architecture</th>
          <th align="center">TARGET</th>
          <th align="center">TARGET_JDK</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>Android - armv8/aarch64</td>
          <td align="center">aarch64-linux-android</td>
          <td align="center">aarch64</td>
        </tr>
        <tr>
          <td>Android - armv7/aarch32</td>
          <td align="center">arm-linux-androideabi</td>
          <td align="center">arm</td>
        </tr>
        <tr>
          <td>Android - x86/i686</td>
          <td align="center">i686-linux-android</td>
          <td align="center">x86</td>
        </tr>
        <tr>
          <td>Android - x86_64/amd64</td>
          <td align="center">x86_64-linux-android</td>
          <td align="center">x86_64</td>
        </tr>
        <tr>
          <td>iOS/iPadOS - armv8/aarch64</td>
          <td align="center">aarch64-macos-ios</td>
          <td align="center">aarch64</td>
        </tr>
      </tbody>
	</table>

### Run in this directory:
```
export BUILD_IOS=1 # only when targeting iOS, default is 0 (target Android)

export BUILD_FREETYPE_VERSION=[2.6.2/.../2.10.4] # default: 2.10.4
export JDK_DEBUG_LEVEL=[release/fastdebug/debug] # default: release
export JVM_VARIANTS=[client/server] # default: client (aarch32), server (other architectures)

# Setup NDK, run once (Android only)
./extractndk.sh
./maketoolchain.sh 

# Get CUPS, Freetype and build Freetype
./getlibs.sh
./buildlibs.sh

# Clone JDK, run once
./clonejdk.sh

# Configure JDK and build, if no configuration is changed, run makejdkwithoutconfigure.sh instead
./buildjdk.sh

# Pack the built JDK
./removejdkdebuginfo.sh
./tarjdk.sh
```

