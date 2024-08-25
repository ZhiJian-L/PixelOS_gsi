English | [Русский](README-RU.md)
### To get started with building PixelOS GSI,
You'll need to get familiar with [Git and Repo](https://source.android.com/source/using-repo.html) as well as [How to build a GSI](https://github.com/phhusson/treble_experimentations/wiki/How-to-build-a-GSI%3F).

## 1. Initializing
### Create the directories

```bash
mkdir -p PixelOS && cd PixelOS
```

### To initialize your local repository

```bash
repo init -u https://github.com/PixelOS-AOSP/manifest.git -b fourteen --git-lfs
```
 

### Add necessary dependencies for GSI, by cloning the Manifest below
 
    git clone https://github.com/MisterZtr/treble_manifest.git .repo/local_manifests  -b 14
  

## 2. Syncing
### Sync the source (Force Sync)

```bash
repo sync --force-sync --optimized-fetch --no-tags --no-clone-bundle --prune -j$(nproc --all)
```

### Generate Private Key (Run Once)
After synchronizing the source code, generate private keys to sign the build. Important: the keys must be generated without a password

```bash
subject='/C=SG/ST=State/L=City/O=Android/OU=Android/CN=Android/emailAddress=limzhijian99@gmail.com'
for x in releasekey platform shared media networkstack verity otakey testkey sdk_sandbox bluetooth nfc; do \
    ./development/tools/make_key vendor/aosp/signing/keys/$x "$subject"; \
done
```
Where:

C: Country code (e.g., US) ST: State name L: City name O: Organization name OU: Organizational Unit name CN: Common name emailAddress: Your email address


### Apply patches (Do this Everything after sync)
 
```
# copy required file
cd PixelOS_gsi/patch/
./copy_files.sh

# apply patch
cd PixelOS/
bash patches/apply-patches.sh .
```

## 3. Generating Rom Makefile
 
 ```
cd device/phh/treble

# Apply some changes for generating the AndroidProduct
git am generate.sh.patch 

# Then generate
bash generate.sh pixel
 ```

### Turn on caching to speed up build

You can speed up subsequent builds by adding these lines to your ~/.bashrc OR ~/.zshrc file:

```
export USE_CCACHE=1
export CCACHE_COMPRESS=1
export CCACHE_MAXSIZE=50G # 50 GB
``` 

## 4. Build and Compile

### Build

 ```
. build/envsetup.sh
ccache -M 50G -F 0
lunch treble_arm64_bN-ap2a-userdebug
make systemimage -j8

# Note: Increate system swap size if having issue.
# Note: Use -j8 or lower if having issue. Below is use all "16" for my system
#make systemimage -j$(nproc --all)
 ```


### Compress

After compilation,
If you want to compress the build, i recommend use [7-zip](https://aur.archlinux.org/packages/7-zip), for a fast and safe way
In rom folder,

   ```
cd out/target/product/tdgsi_arm64_ab
7zz a system.img.xz "system.img"
   ```


### Create VNDK Lite variant

Copy the resulting system.img to the treble_adapter folder in rom
Then,

 ```
sudo bash lite-adapter.sh 64 system.img
 ```


## 5. Troubleshoot
 
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
