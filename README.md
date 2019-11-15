## Developing React Apps using Windows Subsystem for Linux (WSL 1)

This guide is made to help set up a nice web develop environment using the WSL (Windows Subsystem for Linux).

*OBS: WSL 2 is currently in development and can be used on Windows Insider builds. It features full kernel access and much faster filesystem writing, but as for now the network between linux and windows is not as seamless as WSL 1,  so I wasn't  able to make android emulator integrate well enough . Microsoft is working on it. Meanwhile we're gonna use the current release of WSL as its limitations don't really impact the current web development workflow.*

This guide will be constantly updated.

Everything was tested using a stable release of Windows 10 Pro 64bits (version 1909).

# Enabling WSL

Firs you need to enable the feature and download Ubuntu from Windows Store:

From powershell:
```
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```
Or type "Turn Windows features on or off" on start menu and enable it.

### Install Ubuntu from Windows Store
![](https://docs.microsoft.com/pt-br/windows/wsl/media/store.png)

Open it and set up your unix username and password.

# A better terminal experience
[![hyper](https://i.imgur.com/q2EcZsv.png "hyper")](https://imgur.com/q2EcZsv "hyper")

To give you a better terminal emulation I recommend using [Hyper Terminal][Hyper Terminal] or [Windows Terminal][Windows Terminal].

#### Optional styling

- Install Dracula theme on Hyper:
```bash
$ hyper install hyper-dracula
```
- for Windows Terminal, include the following code on the "schemes" section of the profiles.json file and include `"colorScheme" : "Dracula"` on the profile section that mentions "Ubuntu".
```json
{
    "background" : "#282A36",
    "black" : "#21222C",
    "blue" : "#BD93F9",
    "brightBlack" : "#6272A4",
    "brightBlue" : "#D6ACFF",
    "brightCyan" : "#A4FFFF",
    "brightGreen" : "#69FF94",
    "brightPurple" : "#FF92DF",
    "brightRed" : "#FF6E6E",
    "brightWhite" : "#FFFFFF",
    "brightYellow" : "#FFFFA5",
    "cyan" : "#8BE9FD",
    "foreground" : "#F8F8F2",
    "green" : "#50FA7B",
    "name" : "Dracula",
    "purple" : "#FF79C6",
    "red" : "#FF5555",
    "white" : "#F8F8F2",
    "yellow" : "#F1FA8C"
}
```
*taken from : https://github.com/dracula/windows-terminal*

- Install  [Fira Code](https://github.com/tonsky/FiraCode "Fira Code") font and include it on the chosen terminal configuration file.

#### Enabling WSL on Hyper Terminal

Edit the preferences file and replace the following code:

```json
// Powershell on Windows
// - Example: `C:\\WINDOWS\\System32\\WindowsPowerShell\\v1.0\\powershell.exe`
shell: 'C:\\Windows\\System32\\wsl.exe',

// for setting shell arguments (i.e. for using interactive shellArgs: ['-i'])
// by default ['--login'] will be used
shellArgs: ['~'], 
```
OBS: Windows Terminal provides and tab button so you can choose which terminal to open.

#### ZSH and Oh My ZSH

Update distro:
```bash
sudo apt-get update
sudo apt-get upgrade
```
Install ZSH:
```bash
sudo apt-get install zsh
```
Install Oh My ZSH:
```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

Install Spaceship theme:

```bash
git clone https://github.com/denysdovhan/spaceship-prompt.git "$ZSH_CUSTOM/themes/spaceship-prompt"
```
Create a symbolic link on themes folder:
```bash
ln -s "$ZSH_CUSTOM/themes/spaceship-prompt/spaceship.zsh-theme" "$ZSH_CUSTOM/themes/spaceship.zsh-theme"
```
Now edit the **~/.zshrc** file `code ~/.zshrc` or `nano ~/.zshrc` and make the following edits:
```bash
ZSH_THEME="spaceship"
```
At the end of the file:
```json
SPACESHIP_PROMPT_ORDER=(
  user          # Username section
  dir           # Current directory section
  host          # Hostname section
  git           # Git section (git_branch + git_status)
  hg            # Mercurial section (hg_branch  + hg_status)
  exec_time     # Execution time
  line_sep      # Line break
  vi_mode       # Vi-mode indicator
  jobs          # Background jobs indicator
  exit_code     # Exit code section
  char          # Prompt character
)
SPACESHIP_USER_SHOW=always
SPACESHIP_PROMPT_ADD_NEWLINE=false
SPACESHIP_CHAR_SYMBOL="❯"
SPACESHIP_CHAR_SUFFIX=" "
```
Save, restart the terminal and install the plugins component:
```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/zdharma/zplugin/master/doc/install.sh)"
```
Edit the **~/.zshrc** file again and include the following code at the end of file:
```json
zplugin light zdharma/fast-syntax-highlighting
zplugin light zsh-users/zsh-autosuggestions
zplugin light zsh-users/zsh-completions
```
Restart the terminal one more time.

# React Native Dev Enviroment

*Obs: If you have Node and Yarn installed on Windows please uninstall to avoid potential path conflicts:*

Install Node.js

```bash
curl -sL https://deb.nodesource.com/setup_10.x | sudo bash -
sudo apt install nodejs
```

Install React Native CLI
```bash
sudo npm install -g react-native-cli
```

Install Yarn
```bash
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update && sudo apt install yarn
```
- Fix the following permissions to avoid yarn commands errors *(only apply to WSL 1)*:
```bash
sudo chown -R $USER:$GROUP ~/.npm
sudo chown -R $USER:$GROUP ~/.config
```

Install JDK:
```bash
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get update
sudo apt-get install openjdk-8-jdk
```

Install 32bit graphics libs:
```bash
sudo apt-get install gcc-multilib lib32z1 lib32stdc++6
```
### Android SDK and Emulator

Create the following folder structure `~/Android/Sdk` and inside the Sdk folder download the latest android sdk command line tools:
```bash
wget https://dl.google.com/android/repository/sdk-tools-linux-[go get it from google developers website].zip
```
Extract the file so you can get the` /tools` folder.

**Note: You can access the linux filesystem from Windows explorer using `\\wsl$`**

Edit the `~/.zshrc` file again and include the following code at the beggining of the file:
```bash
export ANDROID_HOME=~/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/platform-tools
```
Restart the terminal and execute the following code:
```bash
~/Android/Sdk/tools/bin/sdkmanager "platform-tools" "platforms;android-27" "build-tools;27.0.3"
```

#### Windows Side

Install the latest Android Studio, open AVD Manager and create a Android Device (I used a Pixel 2 running Android Pie)

- Type `path` on start menu and include the following **User Variable**:
```bash
Name: ANDROID_HOME
Value: C:\Users\[yourUser]\AppData\Local\Android\Sdk
```
- Now edit the PATH variable and include the following:
```bash
%ANDROID_HOME%\platform-tools
%ANDROID_HOME%\tools
```
Restart Windows to fully apply these changes.

## React-Native Run

To make this work you have to FIRST open AVD Manager, manually start your Android Device AND execute `adb devices` on a **powershell or cmd prompt**

This will start the server on the Windows Side and WSL will be able to see it.

If you see a return like this then you're  set:
```bash
List of devices attached
emulator-5554   device
```

Now on your WSL Terminal, you can execute `react-native start` and on another instance `react-native run-android`

If everything goes well you should see your App running on the Android Emulator.

## Microsoft Visual Studio Code

If you are using VS Code you can install the WSL Extension (Usually automatically installed when it detects WSL instalation) and from Terminal you can execute `code .` from your project folder and it will automatically start VS Code on that folder with build in terminal running inside WSL.

# Improve Performance

## Windows Defender

Windows Defender and other antivirus softwares can impact the performance of WSL.

To improve this, navigate to the folder `C:\Users\[yourUser]\AppData\Local\Packages\` and look for a folder named `CanonicalGroupLimited...` inside that folder navigate to `\LocalState\rootfs` and copy the full path adress to this folder.

Now go to  `Settings > Update & Security > Windows Defender > Open Windows Defender Secutiry Central > Protection Against Viruses & Threats > Advanced Config… > Exclusions > Add or Remove > Add > Folder` and finally: paste the previous copied path.

OBS: You can further improve performance by completely disabling Windows Defender realtime protection but its not very advisable as Windows is a highly targeted OS.


##### References
https://docs.rocketseat.dev/ambiente-react-native/android/linux
https://blog.rocketseat.com.br/terminal-com-oh-my-zsh-spaceship-dracula-e-mais/
https://fcbrossard.net/blog/wsl-ubuntu-zsh-hyper-terminal
https://stackoverflow.com/questions/49529696/yarn-error-eaccess-permission-denied-scandir-home-ubuntu-config-yarn-link
https://docs.microsoft.com/pt-br/windows/wsl/install-win10

[1]: hyper.is "Hyper Terminal"
[Hyper Terminal]: http://hyper.is "Hyper Terminal"
[Microsoft Terminal]: https://www.microsoft.com/en-us/p/windows-terminal-preview/9n0dx20hk701 "Microsoft Terminal"
[Windows Terminal]: https://www.microsoft.com/en-us/p/windows-terminal-preview/9n0dx20hk701 "Windows Terminal"
