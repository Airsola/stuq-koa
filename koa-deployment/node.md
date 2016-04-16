
## 安装nodejs

### 安装nvm

```
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.31.0/install.sh | bash
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  7766  100  7766    0     0  28614      0 --:--:-- --:--:-- --:--:-- 28656
=> Downloading nvm as script to '/home/sang/.nvm'

=> Appending source string to /home/sang/.bashrc
=> Close and reopen your terminal to start using nvm
$ source ~/.bashrc
$ nvm

Node Version Manager
```

安装nodejs lts版本

```
$ nvm install 4
Downloading https://nodejs.org/dist/v4.3.2/node-v4.3.2-linux-x64.tar.xz...
######################################################################## 100.0%
Now using node v4.3.2 (npm v2.14.12)
Creating default alias: default -> 4 (-> v4.3.2)
$ node -v
v4.3.2
```

使之成为默认

```
$  nvm alias default 4.3
default -> 4.3 (-> v4.3.2)
```

### 确认npm版本

```
$ npm -v
2.14.12
```

只要大于2.9.1即可，如不是，请`npm i -g npm@2.9.1`

### 安装nrm

```
$ npm i -g nrm
npm WARN deprecated npmconf@0.1.16: this package has been reintegrated into npm and is now out of date with respect to npm
/home/sang/.nvm/versions/node/v4.3.2/bin/nrm -> /home/sang/.nvm/versions/node/v4.3.2/lib/node_modules/nrm/cli.js
nrm@0.3.0 /home/sang/.nvm/versions/node/v4.3.2/lib/node_modules/nrm
├── ini@1.3.4
├── only@0.0.2
├── extend@1.3.0
├── async@0.7.0
├── open@0.0.5
├── commander@2.9.0 (graceful-readlink@1.0.1)
├── npmconf@0.1.16 (inherits@2.0.1, osenv@0.0.3, ini@1.1.0, semver@2.3.2, mkdirp@0.3.5, once@1.3.3, nopt@2.2.1, config-chain@1.1.10)
├── node-echo@0.0.6 (jistype@0.0.3, mkdirp@0.3.5, coffee-script@1.7.1)
└── request@2.69.0 (aws-sign2@0.6.0, forever-agent@0.6.1, tunnel-agent@0.4.2, oauth-sign@0.8.1, is-typedarray@1.0.0, caseless@0.11.0, stringstream@0.0.5, isstream@0.1.2, json-stringify-safe@5.0.1, extend@3.0.0, tough-cookie@2.2.1, node-uuid@1.4.7, qs@6.0.2, combined-stream@1.0.5, form-data@1.0.0-rc3, mime-types@2.1.10, aws4@1.3.2, hawk@3.1.3, bl@1.0.3, http-signature@1.1.1, har-validator@2.0.6)
```

测速

```
$ nrm test

* npm ---- 274ms
  cnpm --- 6868ms
  taobao - 716ms
  edunpm - 5598ms
  eu ----- Fetch Error
  au ----- Fetch Error
  sl ----- 1234ms
  nj ----- 2228ms
  pt ----- Fetch Error
```

切换源

```
$ nrm use npm

   Registry has been set to: https://registry.npmjs.org/

```

