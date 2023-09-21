## 步骤一：安装Node.js
首先需要准备Node.js的安装包。可以在Node.js的官网（https://nodejs.org/en/download/）下载最新版的Node.js安装包。
下载完成后，通过终端进入下载文件夹，并解压文件。在解压后的文件夹内，输入以下命令进行安装：
```bash
$ ./configure 
$ make 
$ sudo make install
```
该命令将执行以下操作：

- configure：用于生成Makefile文件，以便后续的编译操作。
- make：编译Node.js源码。
- sudo make install：将编译好的Node.js安装到系统中。

安装完成后，可以通过以下命令查看Node.js版本：
```bash
$ node -v
```
如果输出了Node.js的版本号，则说明Node.js已经安装成功。
## 步骤二：安装npm
npm是Node.js的默认包管理器，用于安装、升级和管理Node.js模块。在安装Node.js时，npm也会被一起安装。如果需要单独安装npm，则可以通过以下命令完成：
```bash
$ sudo apt-get install npm
```
安装完成后，可以通过以下命令查看npm版本：
```bash
$ npm -v
```
如果输出了npm的版本号，则说明npm已经安装成功。
使用淘宝镜像加速
```bash
$ npm config set registry https://registry.npmmirror.com
```
## 使用npm安装模块
安装完Node.js和npm之后，就可以通过npm安装和管理Node.js模块了。在终端中输入以下命令：
```bash
$ npm install <module-name>
```
其中，<module-name>是需要安装的模块名称。例如，要安装Express模块，则可以输入以下命令：
```bash
$ npm install express
```
npm会自动从npm仓库中下载并安装Express模块及其依赖项。
## 升级Node.js和npm
Node.js和npm都有定期的更新版本，可以通过以下命令进行升级：
```bash
$ sudo npm install -g n 
$ sudo n latest 
$ sudo npm install -g npm
```
该命令将执行以下操作：

- npm install -g n：安装n模块，用于管理Node.js的版本。
- n latest：将Node.js升级到最新版本。
- npm install -g npm：将npm升级到最新版本。

升级完成后，可以通过以下命令查看当前版本号：
```bash
$ node -v 
$ npm -v
```
