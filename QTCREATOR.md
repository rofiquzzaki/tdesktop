##Build instructions for Qt Creator 3.1.2 under Ubuntu 14.04

###Prepare

* Install git by command **sudo apt-get install git** in Terminal
* Install g++ by command **sudo apt-get install g++** in Terminal
* Install Qt Creator from [**Downloads page**](https://www.qt.io/download/)

For 32 bit Ubuntu you need to install g++ version 4.8 manually by such commands

* sudo add-apt-repository ppa:ubuntu-toolchain-r/test
* sudo apt-get update
* sudo apt-get install gcc-4.8 g++-4.8
* sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 20
* sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-4.8 20

###Prepare folder

Choose a folder for the future build, for example **/home/user/TBuild** There you will have two folders, **Libraries** for third-party libs and **tdesktop** (or **tdesktop-master**) for the app.

###Clone source code

By git – in Terminal go to **/home/user/TBuild** and run

    git clone https://github.com/telegramdesktop/tdesktop.git

or download in ZIP and extract to **/home/user/TBuild** rename **tdesktop-master** to **tdesktop** to have **/home/user/TBuild/tdesktop/Telegram/Telegram.pro** project

###Prepare libraries

Install dev libraries

    sudo apt-get install libexif-dev liblzma-dev libz-dev libssl-dev libappindicator-dev libunity-dev

Install audio libraries

####Opus codec 1.1

Download [opus-1.1 sources](http://downloads.xiph.org/releases/opus/opus-1.1.tar.gz) from http://www.opus-codec.org/downloads, extract to **/home/user/TBuild/Libraries**, go to **/home/user/TBuild/Libraries/opus-1.1** and run

    ./configure
    make
    sudo make install

####FFmpeg

Download sources [ffmpeg-2.6.3.tar.bz2](http://ffmpeg.org/releases/ffmpeg-2.6.3.tar.bz2) from https://www.ffmpeg.org/download.html, extract to **/home/user/TBuild/Libraries** to have **/home/user/TBuild/Libraries/ffmpeg-2.6.3**, go to **/home/user/TBuild/Libraries/ffmpeg-2.6.3** and run

    sudo apt-get update
    sudo apt-get -y --force-yes install autoconf automake build-essential libass-dev libfreetype6-dev libgpac-dev libsdl1.2-dev libtheora-dev libtool libva-dev libvdpau-dev libvorbis-dev libxcb1-dev libxcb-shm0-dev libxcb-xfixes0-dev pkg-config texi2html zlib1g-dev
    sudo apt-get install yasm

    ./configure --prefix=/usr/local --disable-programs --disable-everything --enable-libopus --enable-decoder=aac --enable-decoder=aac_latm --enable-decoder=aasc --enable-decoder=mp1 --enable-decoder=mp1float --enable-decoder=mp2 --enable-decoder=mp2float --enable-decoder=mp3 --enable-decoder=mp3adu --enable-decoder=mp3adufloat --enable-decoder=mp3float --enable-decoder=mp3on4 --enable-decoder=mp3on4float --enable-decoder=wavpack --enable-decoder=opus --enable-decoder=vorbis --enable-decoder=wmalossless --enable-decoder=wmapro --enable-decoder=wmav1 --enable-decoder=wmav2 --enable-decoder=wmavoice --enable-decoder=flac --enable-encoder=libopus --enable-parser=aac --enable-parser=aac_latm --enable-parser=mpegaudio --enable-parser=opus --enable-parser=vorbis --enable-parser=flac --enable-demuxer=aac --enable-demuxer=wav --enable-demuxer=mp3 --enable-demuxer=ogg --enable-demuxer=mov --enable-demuxer=flac --enable-muxer=ogg --enable-muxer=opus

    make
    sudo make install

####PortAudio 19

[Download portaudio sources](http://www.portaudio.com/archives/pa_stable_v19_20140130.tgz) from **http://www.portaudio.com/download.html**, extract to **/home/user/TBuild/Libraries**, go to **/home/user/TBuild/Libraries/portaudio** and run

    ./configure
    make
    sudo make install

####OpenAL Soft

In Terminal go to **/home/user/TBuild/Libraries** and run

    git clone git://repo.or.cz/openal-soft.git

then go to **/home/user/TBuild/Libraries/openal-soft/build** and run

    sudo apt-get install cmake
    cmake -D LIBTYPE:STRING=STATIC ..
    make
    sudo make install

####libxkbcommon (required for Fcitx Qt plugin)

In Terminal go to **/home/user/TBuild/Libraries** and run

    sudo apt-get install xutils-dev bison python-xcbgen
    git clone https://github.com/xkbcommon/libxkbcommon.git

then go to **/home/user/TBuild/Libraries/libxkbcommon** and run

    ./autogen.sh --disable-x11
    make
    sudo make install

####Qt 5.5.0, slightly patched

http://download.qt-project.org/official_releases/qt/5.5/5.5.0/single/qt-everywhere-opensource-src-5.5.0.tar.gz

Extract to **/home/user/TBuild/Libraries**, rename **qt-everywhere-opensource-src-5.5.0** to **QtStatic** to have **/home/user/TBuild/Libraries/QtStatic/qtbase** folder

Apply patch:

* OR copy (with overwrite!) everything from **/home/user/TBuild/tdesktop/Telegram/\_qt\_5\_5\_0\_patch/** to **/home/user/TBuild/Libraries/QtStatic/**
* OR copy **/home/user/TBuild/tdesktop/Telegram/\_qt\_5\_5\_0\_patch.diff** to **/home/user/TBuild/Libraries/QtStatic/**, go there in Terminal and run

    git apply _qt_5_5_0_patch.diff

#####Building library

Install some packages for Qt (see **/home/user/TBuild/Libraries/QtStatic/qtbase/src/plugins/platforms/xcb/README**)

    sudo apt-get install libxcb1-dev libxcb-image0-dev libxcb-keysyms1-dev libxcb-icccm4-dev libxcb-render-util0-dev libxcb-util0-dev libxrender-dev libasound-dev libpulse-dev libxcb-sync0-dev libxcb-xfixes0-dev libxcb-randr0-dev libx11-xcb-dev libffi-dev

In Terminal go to **/home/user/TBuild/Libraries/QtStatic** and there run

    ./configure -release -opensource -confirm-license -qt-xcb -no-opengl -static -nomake examples -nomake tests -skip qtquick1 -skip qtdeclarative
    make module-qtbase module-qtimageformats
    sudo make module-qtbase-install_subtargets module-qtimageformats-install_subtargets

building (**make** command) will take really long time.

###Building Telegram Desktop

* Launch Qt Creator, all projects will be taken from **/home/user/TBuild/tdesktop/Telegram**
* Tools > Options > Build & Run > Qt Versions tab > Add > File System /usr/local/Qt-5.5.0/bin/qmake > **Qt 5.5.0 (Qt-5.5.0)** > Apply
* Tools > Options > Build & Run > Kits tab > Desktop (default) > change **Qt version** to **Qt 5.5.0 (Qt-5.5.0)** > Apply
* Open MetaStyle.pro, configure project with paths **/home/user/TBuild/tdesktop/Linux/DebugIntermediateStyle** and **/home/user/TBuild/tdesktop/Linux/ReleaseIntermediateStyle** and build for Debug
* Open MetaEmoji.pro, configure project with paths **/home/user/TBuild/tdesktop/Linux/DebugIntermediateEmoji** and **/home/user/TBuild/tdesktop/Linux/ReleaseIntermediateEmoji** and build for Debug
* Open MetaLang.pro, configure project with paths **/home/user/TBuild/tdesktop/Linux/DebugIntermediateLang** and **/home/user/TBuild/tdesktop/Linux/ReleaseIntermediateLang** and build for Debug
* Open Telegram.pro, configure project with paths **/home/user/TBuild/tdesktop/Linux/DebugIntermediate** and **/home/user/TBuild/tdesktop/Linux/ReleaseIntermediate** and build for Debug, if GeneratedFiles are not found click **Run qmake** from **Build** menu and try again
* Open Updater.pro, configure project with paths **/home/user/TBuild/tdesktop/Linux/DebugIntermediateUpdater** and **/home/user/TBuild/tdesktop/Linux/ReleaseIntermediateUpdater** and build for Debug
* Release Telegram build will require removing **CUSTOM_API_ID** definition in Telegram.pro project and may require changing paths in **/home/user/TBuild/tdesktop/Telegram/FixMake.sh** or **/home/user/TBuild/tdesktop/Telegram/FixMake32.sh** for static library linking fix, static linking applies only on second Release build (first uses old Makefile)
