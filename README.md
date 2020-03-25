# Build Tools for Iosevka Custom

This repository contain custom build plans and tools to run Iosevka Custom Build. Such as otfcc library, premake library, and private build plans TOML files.

Here some snippet of step by step in building Iosevka Customs, from  Adam Kruszewski original blog [https://adam.kruszewski.name/articles/2019-10-27-build-custom-iosevka-font/](https://adam.kruszewski.name/articles/2019-10-27-build-custom-iosevka-font/)

---

Step by step guide in compiling a custom Iosevka font on Ubuntu/Debian
Published October 27, 2019 #iosevka, #font, #emacs

My last post on how to build a custom Iosevka font got completely out of date as Iosevka changed the build system and moved away from Makefiles. I thought I’ll write an update with additional step-by-step guide on how to make it work on more popular GNU/Linux distribution - Ubuntu (it is pretty straightforward on Arch or Gentoo as every dependency can be installed by distro’s package/port manager).

---

Through the whole guide I assume we’ll clone most of the repositories used inside ~/Src/opensource/ directory.

## Install dependencies

There are three major dependencies for building Iosevka, NodeJS/NPM and ttfautohint are available as binaries in Ubuntu/Debian repositories so it is quite easy to install them running the following from terminal:

```sh
sudo apt install nodejs npm ttfautohint libttfautohint-dev
```

## Build and compile premake5 for otfcc

Otfcc requires premake5 build system to build and we need to compile it from sources:

```sh
cd ~/Src/opensource/
```

```sh
git clone https://github.com/premake/premake-core
cd premake-core
make -f Bootstrap.mak linux
```

## Build otfcc

We’ll not install premake system-wide, just execute it from otfcc’s source directory and build otfcc’s binaries.

```sh
cd ~/Src/opensource/
```

```sh
git clone https://github.com/caryll/otfcc
cd otfcc
../premake-core/bin/release/premake5 gmake
cd build/gmake
make config=release_x64
```

## Clone Iosevka repository

Now we can finally clone Iosevka’s source repository.

```sh
cd ~/Src/opensource/

git clone https://github.com/be5invis/Iosevka -o iosevka
```

## Install nodeJs dependencies

Node package manager will take care of last set of dependencies needed.

```sh
cd iosevka
npm install
```

## Create a custom build plan for Iosevka font

Now create a “private-build-plans.toml” file with the contents below inside Iosevka’s source directory. You can tweak look for each of the available options - please refer to official Iosevka readme to see what options are available. The ones below are what I’m using and reflect what you could see on the picture at the beginning of this post.

## Build the iosevka font

Finally we can build the font. Just for the build step run we’ll add otfcc’s binary path to the $PATH variable, for npm to find it. Go grab some coffee as it might take a while.

```sh
env PATH="$PATH:../otfcc/bin/release-x64/" npm run build -- contents::iosevka-custom
```

or other example

```sh
env PATH="$PATH:/home/{your linux username}/apps/Iosevkabuilds/otfcc/bin/release-x64" yarn run build -- contents::iosevka-mayukai
```

## Install the font

The last step is to install the font, a shortcut from using your desktop’s font manager is just to copy the fonts where font-config can find it and invalidate it’s cache.

```sh
cp build/iosevka-custom/*.ttf ~/.fonts
fc-cache -f
```

That’s all, custom Iosevka build should be available as “Iosevka Custom” now. Happy hacking!
