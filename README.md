# QtContainers
A collection of docker containers for building Qt. Use them as intermediate containers to build your own dev containers. The containers are currently in a quite hacky state. Naming conventions and locations for the compiled Qt versions are subject to change. 

Use them in your Dockerfiles like this:

```Dockerfile
FROM ubuntu:20.04

.... your stuff here

COPY --from=bernedom/qt_x86_64:5.15.2 /usr/local/Qt /usr/local/Qt/gcc_64
COPY --from=bernedom/qt_android:5.15.2 /usr/local/Qt /usr/local/Qt/android
COPY --from=bernedom/qt_android:5.15.2 /opt/android-sdk /opt/android-sdk
COPY --from=bernedom/qt_android:5.15.2 /opt/android-ndk /opt/android-ndk

```

## Containers available on docker hub

[bernedom/qt_x86_64:5.15.2](https://hub.docker.com/layers/bernedom/qt_x86_64/5.15.2/images/sha256-d69cb7ebd9e5e26c376c086c45eda727290ddcac4a1f9104e390c1b4075e2810?context=repo)
[bernedom/qt_android:5.15.2](https://hub.docker.com/layers/bernedom/qt_android/5.15.2/images/sha256-5167e875a3c7ea632edca0b8681c9ff6b302e2c156a384a106d3866f9a9d0838?context=repo)


## Building

```bash
docker build ./x86_64 -t bernedom/qt_x86_64:5.15.2
```
