本仓库是对源仓库进行中文解说,而不是进行翻译.使用机器翻译成中文的效果基本精确了




# Emulator Detection Bypass steps
Download and setup emulator with any SDK version 12 or below with playstore of x86_64 arch.

# Magisk image flashing[^1]   
* Download and extract rootAVD repo from this [Link](https://github.com/newbit1/rootAVD)
* Open terminal on your pc and navigate to above extracted dir.
* trigger command as
  ```
  ./rootAVD.sh
  ```
* This will list you commands that you need to trigger.
* Trigger command
  ```
  ./rootAVD.sh ListAllAVDs
  ```
  and select the image matching your emulators matching arch type.
* Once flashing is done it should reboot your emulator.
* Trigger command
  ```
  ./rootAVD.sh InstallApps
  ```
* Once done your emulator will have magisk installed in it,start the application it will ask permission which will then reboot your device.
* Once device has rebooted go to magisk settings and enable zygisk setting and reboot your device from magisk options.
* You are now done flashing/rooting your deivce.


  这一段是利用了另一个github仓库对安卓进行root,该仓库的作用是 使用脚本快速对 android studio中自带的安卓模拟器进行root.对于一般的安卓模拟器,一般自带开启root的功能.对于真实机器,该仓库不能直接使用.如果安卓系统已经root了,这一段可以跳过

# Setting up termux on device.
* Download latest apk from termux repo release section based on your emulator arch type [Link](https://github.com/termux/termux-app/releases)
* Open the app and enter command
  ```
  pkg update
  ```
* Once above is done enter command
  ```
  pkg upgrade
  ```
* Enter command
  ```
  termux-setup-storage
  ```
  and grant storage permission.
* Your termux is not ready to use.


  这一段的作用是 更新软件包索引( pkg update)和升级软件包( pkg upgrade) ,让 termux请求 获取存储权限(  termux-setup-storage )
  
  
# Additional Apps/tools that are needed
* Download and install root explorer apk.

下载使用root权限的文件浏览器. root explorer 是使用root的文件浏览器.这不是必须的,只是为了方便用户操作.便于修改和浏览文件

# Setting up frida and other packages in termux[^2]
* Enter command
  ```
  pkg install build-essential python python-pip git wget binutils openssl
  ```
*  Download Frida Core DevKit according to device architecture from [Link](https://github.com/frida/frida/releases)
*  Extract the zip and move the fils in your emulator (there will be 4 files)
*  Open root explorer and navigate to root->sdcard->downloads you will be able to locate your files that you had recently copied to this device.
*  Move these files to root->sdcard->devkit(make a new dir).
*  Open termux and enter command
  ```
export FRIDA_CORE_DEVKIT=/sdcard/devkit/
```
   if you extracted it on some other location, use that path.make sure you run above command without root/su.
* Install frida and its commandline tools from pip
  ```
  pip install frida frida-tools
  ```
  ![image](https://user-images.githubusercontent.com/27184655/218310575-89d7d2c0-028d-4942-a5ea-edc96461d55f.jpg)
  Now Frida is available to use from commandline.you can check version by typeing ``` frida-ps --version ```

下载frida的frida所需文件,这里必须严格按照步骤.从该链接https://github.com/frida/frida/releases 下载对应的文件,解压,然后放在安卓系统的/sdcard/devkit/ 目录 ,之后执行 命令(pip install frida frida-tools)

易错点:
1. 源仓库是2023 年8月13日更新的,所以安装下载的文件的发布时期,最好在2023 年8月13日之前,比如pip install  frida==16.0.9  pip install  frida-tools==12.1.0,寻找frida-core文件的时候,也需要注意发布日期,
2. 需要把 下载后解压后的文件放在/sdcard/devkit/.建议直接在termux中下载和解压
3. 按照该步骤执行的话,无法持久化.即每次打开termux都要运行一次 export FRIDA_CORE_DEVKIT=/sdcard/devkit/
  
# Optional steps if with above frida is giving error.
```
pkg install root-repo
```
```
pkg install frida-python
```
这一段的意思是,如果按照上面的步骤出现错误,可以试一试执行 以上的两个命令.但其实只是可能会成功,是一个不完整的诊断步骤.








# Frida server installation flow
* Download latest version of frida-server based on device arc type(file name containes server and android) [Link](https://github.com/frida/frida/releases)
* Extract the zip and move the file in you emulator by draging and droping.
* Open root explorer and navigate to root->sdcard->download where you will be able to locate your file.
* Move the file to root->data->local->tmp.
* Rename to file to frida-server
* Long press on the file and select additional options from the top left and select permissions and enable execution rights to all user types.
  
  <img src="https://github.com/Ms-dev3/EmulatorByPass/assets/111139550/643e5ac9-8fb7-4273-b580-131e19bba2a5" alt="image" width="500" height="550">
* open termux and enter folling commands (When run su command, If App ask for the super permission allow it with press on Grant)
* Goto your app permissions->Additional permmisions->allow Run command in termux terminal(Check below screenshots)
<img src="https://i.imgur.com/OHC7pO5.jpg" alt="Additional permissions" width="230" height="500"> <img src="https://i.imgur.com/J6rcGnQ.jpg" alt="permissions image" width="230" height="500">
* Now to start the server entering following commands.
  ```
  su
  ```
  ```
  cd /data/local/tmp
  ```
  ```
  ls (It will shows you a dir and files of the tmp dir)
  ```
  ```
  ./frida-server -l 127.0.0.1
  ```
  once above command is entered your cursor will move to next line and continue blinking.Leave this session of termux as it is and dont close it.
  To open a new session in termux swipe from the left edge to open a draw and select new session.
* Move emulator bypass js file to root->data->local->tmp
* Now to check if you are able to connect with the server from your new termux session enter below command to list all device process
  ```
  frida-ps -H 127.0.0.1
  ```
  or you can run acutal bypass command as
  ```
  frida -H 127.0.0.1 -f your.packagename -l /data/local/tmp/bypass.js
  ```
  
* If working that our termux setup is complete.

根据 https://github.com/frida/frida/releases链接,下载对应文件,放到 /data/local/tmp 目录下 , 然后termux中执行   chmod 777 /data/local/tmp/frida-server; /data/local/tmp/frida-server -l 127.0.0.1 
意思是给这个文件相应的权限

另外,这一段它讲了如何使用 刚刚安装的软件
如果你已经拥有了 基本frida的模拟器检测绕过脚本,比如该脚本的文件位置是/data/local/tmp/bypass.js ,想要禁止某个app(该app的名字是your.packagename )模拟器检测, 那可以在termux中执行 
 frida -H 127.0.0.1 -f your.packagename -l /data/local/tmp/bypass.js

难点:
1. 执行   /data/local/tmp/frida-server -l 127.0.0.1  ,后termux会无法响应用户后续操作,这时候需要打开一个新的termux会话,具体可谷歌搜索"termux打开新会话".这里简单说一下在termux左上角向中间滑动,可弹出 "会话管理"界面
   

# Termux setup to accept external commands.  
* Making termux accept external commands[^3].
  ```
  value="true"; key="allow-external-apps"; file="/data/data/com.termux/files/home/.termux/termux.properties"; mkdir -p "$(dirname "$file")"; chmod 700 "$(dirname "$file")"; if ! grep -E '^'"$key"'=.*' $file &>/dev/null; then [[ -s "$file" && ! -z "$(tail -c 1 "$file")" ]] && newline=$'\n' || newline=""; echo "$newline$key=$value" >> "$file"; else sed -i'' -E 's/^'"$key"'=.*/'"$key=$value"'/' $file; fi
  ```
* Before your app can transmit commands to termux you will have to enable certain permission and make the user grant them.
  ```
  <uses-permission android:name="com.termux.permission.RUN_COMMAND"/>
  ```
  ```
  <queries>
        <package android:name="com.termux" />
        <intent>
            <action android:name="android.intent.action.MAIN" />
        </intent>
  </queries>
  ```
* Sending commands from your app to termux[^4].
  ```
  intent.setClassName("com.termux", "com.termux.app.RunCommandService")
  intent.action = "com.termux.RUN_COMMAND"
  intent.putExtra("com.termux.RUN_COMMAND_PATH", "/data/data/com.termux/files/usr/bin/frida")
  intent.putExtra(
            "com.termux.RUN_COMMAND_ARGUMENTS",
            arrayOf("-H", "127.0.0.1", "-f", "your app package name", "-l", "/data/local/tmp/bypass.js")
  )
  intent.putExtra("com.termux.RUN_COMMAND_BACKGROUND", true)
  intent.putExtra("com.termux.RUN_COMMAND_SESSION_ACTION", "4")
  startService(intent)
  ```
# Additional step
* In case you encounter error while running frida command as below
  ```
  File "/data/data/com.termux/files/usr/lib/python3.11/re/_parser.py", line 455, in _parse_sub itemsappend(_parse(source, state, verbose, nested + 1
  ```
  you can enter following command as mentioned here [Link](https://github.com/frida/frida/issues/2372#issuecomment-1374208979)
  > silverbullet-herr commented Jan 7, 2023
  > 
  > try this:
  > pip uninstall pygments && pip install pygments
  > to install the latest version instead of the required version that's how it worked for me

这一段是对于开发者而言的,告诉开发者可以怎么写代码,给出了一个demo.除非你自己写代码,否则这一段不用看








*References*
[^1]: [Rooting emulator](https://avicoder.me/2021/09/02/Root-AVD-and-install-Magisk/)
[^2]: [Frida setup on termux](https://github.com/frida/frida/discussions/2411)
[^3]: [termux property edit](https://github.com/termux/termux-tasker#allow-external-apps-property-optional)
[^4]: [Frida run commands](https://github.com/termux/termux-app/wiki/RUN_COMMAND-Intent)


