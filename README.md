# hyper-arm-builds
https://github.com/vercel/hyper

# ***NOTE: [DEPRECATED]***
- arm64 builds were fixed & armhf support wad added to upstream hyper in [this](https://github.com/vercel/hyper/commit/20de964f53c3a85969c89398915a69bf1cb6218c) commit.
- Use builds on upstream hyper releases [page](https://github.com/vercel/hyper/releases) (Will be availabe since v4.0.0).
- This repo will be up for historic purposes.

#   
Finished builds are available on the [Releases](https://github.com/Jai-JAP/hyper-arm-builds/releases) page.

Latest Hyper releases are automatically compiled for armv7l/arm64 using Github workflows.

## To compile Hyper on arm linux:
```
sudo apt update
sudo apt install -y libarchive-tools jq graphicsmagick icnsutils xz-utils ruby build-essentials git
#Install nodejs 16
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt install nodejs
sudo gem install fpm --no-document
            
git clone https://github.com/vercel/hyper -b $(curl --silent "https://api.github.com/repos/vercel/hyper/releases/latest" | jq -r '.tag_name') --single-branch
cd hyper

# remove x64 builds
sed -e '/},/{:a;N;/]/!ba};/        "target": "snap",/d' -i electron-builder.json 
sed -e '/          x64",/d' -i electron-builder.json 
sed -i 's/\ \&\& husky install//g' package.json
sed -i '/electron-mksnapshot/d' package.json
sed -i 's|yarn run v8-snapshot &&||g' package.json
sed -i '/cp-snapshot.js/d' electron-builder.json

# for armhf downgrade electron to 17.0.0 in package.json
sed -e '/"electron":/c\    "electron" : "^17.0.0",' -i package.json
# change arm64 to armv7l for armhf build
sed -e 's/          "arm64"/          "armv7l"/g' -i electron-builder.json

sudo npm i -g yarn
yarn --network-timeout 1000000
npm i app-builder-lib
USE_SYSTEM_FPM=true yarn run dist

#Build artifacts will be in dist subfolder
```
