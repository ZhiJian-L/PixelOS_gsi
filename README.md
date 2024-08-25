English | [Русский](README-RU.md)
### To get started with building PixelOS GSI,
You'll need to get familiar with [Git and Repo](https://source.android.com/source/using-repo.html) as well as [How to build a GSI](https://github.com/phhusson/treble_experimentations/wiki/How-to-build-a-GSI%3F).


### Create the directories

As a first step, you'll have to create and enter a folder with the appropriate name
To do that, run these commands:

```bash
mkdir PixelOS
cd PixelOS
```

### To initialize your local repository, run this command:

```bash
repo init -u https://github.com/PixelOS-AOSP/manifest.git -b fourteen --git-lfs
```
 

### Clone the Manifest to add necessary dependencies for gsi:
 
    git clone https://github.com/MisterZtr/treble_manifest.git .repo/local_manifests  -b 14
  


### Afterwards, sync the source by running this command:

```bash
repo sync --force-sync --optimized-fetch --no-tags --no-clone-bundle --prune -j$(nproc --all)
```

### After synchronizing the source code, generate private keys to sign the build. Important: the keys must be generated without a password

```bash
subject='/C=SG/ST=State/L=City/O=Android/OU=Android/CN=Android/emailAddress=limzhijian99@gmail.com'
for x in releasekey platform shared media networkstack verity otakey testkey sdk_sandbox bluetooth nfc; do \
    ./development/tools/make_key vendor/aosp/signing/keys/$x "$subject"; \
done
```
Where:

C: Country code (e.g., US) ST: State name L: City name O: Organization name OU: Organizational Unit name CN: Common name emailAddress: Your email address


### Next, apply patches:

Copy the patches folder to rom folder and in rom folder

```
bash patches/apply-patches.sh .
```

## Generating Rom Makefile

 Clone this repository and then copy pixel.mk to device/phh/treble in rom folder. Then run the following commands:,
 
 ```
cd device/phh/treble
bash generate.sh pixel
 ```

### Turn on caching to speed up build

You can speed up subsequent builds by adding these lines to your ~/.bashrc OR ~/.zshrc file:

```
export USE_CCACHE=1
export CCACHE_COMPRESS=1
export CCACHE_MAXSIZE=50G # 50 GB
``` 

## Compilation 

In rom folder,

 ```
. build/envsetup.sh
ccache -M 50G -F 0
lunch treble_arm64_bN-ap2a-userdebug
make systemimage -j$(nproc --all)
 ```


## Compress

After compilation,
If you want to compress the build, i recommend use [7-zip](https://aur.archlinux.org/packages/7-zip), for a fast and safe way
In rom folder,

   ```
cd out/target/product/tdgsi_arm64_ab
7zz a system.img.xz "system.img"
   ```


## Create VNDK Lite variant

Copy the resulting system.img to the treble_adapter folder in rom
Then,

 ```
sudo bash lite-adapter.sh 64 system.img
 ```


## Troubleshoot
 
If you face any conflicts while applying patches, apply the patch manually



## Credits
These people have helped this project in some way or another, so they should be the ones who receive all the credit:
- [crDroid Team](https://github.com/crdroidandroid)
- [Phhusson](https://github.com/phhusson)
- [AndyYan](https://github.com/AndyCGYan)
- [Ponces](https://github.com/ponces)
- [Peter Cai](https://github.com/PeterCxy)
- [Iceows](https://github.com/Iceows)
- [ChonDoit](https://github.com/ChonDoit)
- [Nazim N ](https://github.com/naz664)
- [Ahnet](https://github.com/ahnet-69)
