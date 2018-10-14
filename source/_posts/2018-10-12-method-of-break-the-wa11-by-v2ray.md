---
title: Break the wa11 by v2ray
date: 2018-10-12 15:54:51
categoties: Network
tags: 
- note
- tools
- wa11
- v2ray
- privacy
---

# Check the time (VPS and your host)

```shell
date -r
```
Make sure the distance between them is smaller than 2 minutes. Different timezone is OK.
<!-- more -->

# install v2ray

There is no difference between servers and clients. They differ by configurations.

* Linux

```shell
bash <(curl -L -s https://install.direct/go.sh)
```

* macOS

```shell
brew tap v2ray/v2ray
brew install v2ray-core
brew info v2ray-core
```

# customize your configuration ([refer](https://yuan.ga/v2ray-complete-tutorial/))

```shell
vi /etc/v2ray/config.json
```

`jq` can verify the format of json.
Or just test the configuration by v2ray itself:

```shell
v2ray -test -config=./config.json
```

# Start your server

```shell
systemctl start v2ray
systemctl status v2ray
```

# Start your client

* macOS

```shell
v2ray -config=./config.json
```

Or use brew (auto restart at login) :
```shell
brew services start v2ray/v2ray/v2ray-core
```

* macOS GUI :v2rayX

```shell
brew cask install v2rayx
```

I suggest that configure the v2rayX manully, because the import method doesn't work on my machine.

V2rayX will configure the system proxy automaticly, so `SwitchOmega` is not necessary.

* Android: [v2rayNG](https://play.google.com/store/apps/details?id=com.v2ray.ang&hl=en_US)

# Enjoy

There is no worry any more about my study!!!😂