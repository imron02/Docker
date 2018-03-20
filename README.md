# Docker

Docker ini digunakan untuk ci / cd (gitlab) untuk membuat apk ionic dan react native.
Repo ini terdiri dari 3 docker image.

1. [base_hybrid](https://github.com/imron02/Docker/tree/master/base_hybrid). Image ini sebagai base docker dari docker ionic.
2. [ionic](https://github.com/imron02/Docker/tree/master/ionic). Image ini digunakan untuk membuild aplikasi ionic. Baik aplikasi web, aplikasi android (dev) dan aplikasi android (prod).
3. [react_native](https://github.com/imron02/Docker/tree/master/react_native). Image ini digunakan untuk membuild aplikasi react native. Baik aplikasi android (dev) dan aplikasi android (prod).

> Jika ingin ikut berkontribusi, silahkan fork repo ini.
> Dockerfile ini berdasarkan dari docker [node:8.10.0](https://hub.docker.com/_/node/), menggunakan sistem operasi debian jessie

Docker yang saya buat berdasarkan beberapa tutorial basic di bawah ini:
1. [Install docker](https://docs.docker.com/install/)
2. [Docker get started](https://docs.docker.com/get-started/)

#### Cara pakai untuk ionic:

Buat project ionic di gitlab, kemudian buat file `.gitlab-ci.yml` di root project anda, lalu copy paste ini:

```yaml
image: imron02/ionic:1.0.1

stages:
  - build
  - compile

ionic_build:
  stage: build
  before_script:
    - npm install
  script: 
    - node --max-old-space-size=4096 ./node_modules/.bin/ionic-app-scripts build --prod
  artifacts:
    name: "www_$CI_COMMIT_REF_SLUG"
    paths:
      - www/
  only:
    - /^build_.*$/
    - master

compile_android:
  stage: compile
  before_script:
    - npm install
  script: 
    - ionic cordova platform add android
    - ionic cordova build android --prod
    - cp ./platforms/android/app/build/outputs/apk/debug/app-debug.apk ./build-apk/
    - ionic cordova build android --prod --release
    - cp ./platforms/android/app/build/outputs/apk/release/app-release-unsigned.apk ./build-apk/
    - jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore ./build/ionic_ci.jks -storepass $STORE_PASS ./build-apk/app-release-unsigned.apk ionic_ci
    - /opt/android-sdk-linux/build-tools/27.0.3/zipalign -v 4 ./build-apk/app-release-unsigned.apk ./build-apk/ionic_ci.apk
  artifacts:
    name: "apk_$CI_COMMIT_REF_SLUG"
    paths:
      - build-apk/*.apk
  only:
    - /^build_.*$/
    - master
```

#### Cara pakai untuk react native:

Buat project react native di gitlab, kemudian buat file `.gitlab-ci.yml` di root project anda, lalu copy paste ini:

```yaml
image: imron02/react-native:1.0.2

stages:
  - compile

compile_android:
  stage: compile
  before_script:
    - yarn install
    - chmod +x ./android/gradlew
  script: 
    - cd android
    - ./gradlew assembleDebug
    - ./gradlew assembleRelease
    - cd ..
    - cp ./android/app/build/outputs/apk/*.apk ./build-apk/
    - jarsigner --verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore ./builds/react_native.keystore -storepass $STORE_PASS ./build-apk/app-release-unsigned.apk react_native
    - /opt/android-sdk-linux/build-tools/27.0.3/zipalign -v 4 ./build-apk/app-release-unsigned.apk ./build-apk/react_native.apk
  artifacts:
    name: "apk_$CI_COMMIT_REF_SLUG"
    paths:
      - build-apk/*.apk
  only:
    - /^build_.*$/
    - master

```

#### Cara pakai untuk react native (CRNA / Expo):

> `.gitlab-ci.yml` ini digunakan untuk build aplikasi react native menggunakan expo.

Buat project react native di gitlab, kemudian buat file `.gitlab-ci.yml` di root project anda, lalu copy paste ini:

```yaml
image: imron02/react-native:1.0.2

stages:
  - publish
  - compile

publish_app:
  stage: publish
  cache:
    key: "$CI_PIPELINE_ID"
    paths:
      - node_modules
  before_script:
    - yarn install
  script:
    - exp --non-interactive login --username $PROJECT_USERNAME --password $PROJECT_PASSWORD
    - exp --non-interactive publish --clear --release-channel prod
  only:
    - /^build_.*$/
    - master

compile_android:
  stage: compile
  cache:
    key: "$CI_PIPELINE_ID"
    paths:
      - node_modules
  before_script:
    - yarn install
  script: 
    - exp --non-interactive login --username $PROJECT_USERNAME --password $PROJECT_PASSWORD
    - exp --non-interactive build:android --release-channel prod
    - exp --non-interactive build:status >> build-apk/build.txt
    - cd ./build-apk
    - wget -q $(awk '/https:\/\//{print $NF}' build.txt)
    - rm build.txt
    - cd ..
  artifacts:
    name: "apk_$CI_COMMIT_REF_SLUG"
    paths:
      - build-apk
  only:
    - /^build_.*$/
    - master

```

`.gitlab-ci.yml` yang saya buat berdasarkan beberapa tutorial basic di bawah ini:
1. [Getting started with gitlab ci](https://docs.gitlab.com/ce/ci/quick_start/README.html)
2. [Gitlab ci reference](https://docs.gitlab.com/ce/ci/yaml/)

> `.gitlab-ci.yml` di atas akan running pipeline jika ada tag dengan prefix build_ atau push commit ke master