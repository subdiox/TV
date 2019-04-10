# TV
How to setup TV-related software for Windows

# For Windows (x64)
## Requirements
### Hardware Requirements
- Tuner Board (PT3 Rev.A is highly recommended)

### Software Requirements
- Tuner driver and SDK ([PT3 Rev.A driver/SDK](https://earthsoft.jp/PT3/download_SHA-2.html))
- [Node.js](https://nodejs.org/ja/) (10.x LTS is recommended)
- [Git for Windows](https://gitforwindows.org/)
- [Visual Studio Community 2019](https://visualstudio.microsoft.com/ja/)

## Main Software Setup
### BonRecTest (Recorder)
1. Download [BonRecTest binary](https://github.com/rndomhack/BonRecTest/releases/latest) and extract it
2. Copy `x64\BonRecTest.exe` to `D:\TV\BonRecTest` directory (create directory)

### BonDriver (Driver for BonRecTest)
1. Download [BonDriver for PT3 binary](https://github.com/epgdatacapbon/BonDriver_PT3-ST/releases/latest) and extract it
2. Copy `BonDriver_*` and `x64\PTCtrl.exe` to `D:\TV\BonRecTest` directory
3. Duplicate `x64\BonDriver_PT-S.dll` to `D:\TV\BonRecTest\BonDriver_PT-S0.dll` and `D:\TV\BonRecTest\BonDriver_PT-S1.dll`
4. Duplicate `x64\BonDriver_PT-T.dll` to `D:\TV\BonRecTest\BonDriver_PT-T0.dll` and `D:\TV\BonRecTest\BonDriver_PT-T1.dll`
5. Edit `D:\TV\BonRecTest\BonDriver_PT3-ST.ini` with `UseLNB=1`

### B25Decoder (Decoder for B25 Encryption)
1. Download [B25Decoder source](https://github.com/epgdatacapbon/libaribb25) and extract it
2. Open `arib_std_b25.sln` with Visual Studio and build it with `Release` and `x64` settings
3. Copy `x64\Release\libaribb25.dll` to `D:\TV\BonRecTest\B25Decoder.dll` (rename needed)

### SoftCAS (B-CAS Emulator)
1. Download [SoftCAS](http://www.wazoku.net/softcas.html) and extract it (URL might be fake)
2. Copy `x64\*` to `D:\TV\BonRecTest`
3. Edit winscard.ini with some key

### Mirakurun (Tuner Server)
1. Open Windows PowerShell with administrator privilege from start button (right click)
2. `npm install winser@1.0.3 -g`
3. `npm install mirakurun@latest -g --production`
4. Download [channels.yml](https://github.com/subdiox/TV/blob/master/channels.yml) and replace `C:\Users\username\.Mirakurun\channels.yml`
5. Edit `C:\Users\username\.Mirakurun\tuners.yml` like this:
    ```yaml
    - name: PT3-S1
      types:
          - BS
          - CS
      command: D:\TV\BonRecTest\BonRecTest.exe --decoder D:\TV\BonRecTest\B25Decoder.dll --driver D:\TV\BonRecTest\BonDriver_PT3-S0.dll --output - --channel <channel>
      decoder: ~
      isDisabled: false

    - name: PT3-S2
      types:
          - BS
          - CS
      command: D:\TV\BonRecTest\BonRecTest.exe --decoder D:\TV\BonRecTest\B25Decoder.dll --driver D:\TV\BonRecTest\BonDriver_PT3-S1.dll --output - --channel <channel>
      decoder: ~
      isDisabled: false

    - name: PT3-T1
      types:
          - GR
      command: D:\TV\BonRecTest\BonRecTest.exe --decoder D:\TV\BonRecTest\B25Decoder.dll --driver D:\TV\BonRecTest\BonDriver_PT3-T0.dll --output - --channel <channel>
      decoder: ~
      isDisabled: false

    - name: PT3-T2
      types:
          - GR
      command: D:\TV\BonRecTest\BonRecTest.exe --decoder D:\TV\BonRecTest\B25Decoder.dll --driver D:\TV\BonRecTest\BonDriver_PT3-T1.dll --output - --channel <channel>
      decoder: ~
      isDisabled: false
    ```
6. `Start-Service mirakurun` / `Stop-Service mirakurun`

### [Optional] Mirakurun-UI (UserInterface for Mirakurun)
1. Download [Mirakurun-UI](https://github.com/Chinachu/Mirakurun-UI/releases/latest) and install it
2. Launch Mirakurun-UI and enter your private IP address to it (don't use 127.0.0.1, use 192.168.x.x)
3. Check whether mirakurun is working or not

### BonDriver_Mirakurun (BonDriver for Mirakurun using TVTest)
1. Download [BonRecTest_Mirakurun binary](https://github.com/Chinachu/BonDriver_Mirakurun/releases/latest) and extract it
2. Copy `x64\*` to `D:\TV\TVTest` (create directory)
3. Edit `C:\TV\TVTest\BonRecTest_Mirakurun.ini` with `SERVER_HOST="YOUR_PRIVATE_IP"`

### TVTest (TV Player)
1. Download [LibISDB](https://github.com/DBCTRADO/LibISDB) and extract it
2. Open `LibISDB-master\Projects\LibISDB.sln` with Visual Studio
3. Edit `Tests\libisdbtest.cpp` by removing `constexpr` keyword
4. Edit `LibISDB\Windows\Viewer\DirectShow\AudioDecoders\AC3Decoder.cpp` by removing `constexpr` keyword
5. Build project with `Release` and `x64` settings
6. Download [TVTest-develop](https://github.com/DBCTRADO/TVTest/tree/develop) and extract it
7. Copy `LibISDB-master` directory to `TVTest-develop\src\LibISDB`
8. Open `TVTest-develop\src\TVTest_All.sln` with Visual Studio and build it with `Release` and `x64` settings
9. Copy `TVTest-develop\src\x64\Release\TVTest.exe` and `TVTest-develop\src\x64\Release\TVTest_Image.dll` to `D:\TV\TVTest`
10. Open `TVTest-develop\sdk\Samples\Samples.VS2013.sln` with Visual Studio and build it with `Release` and `x64` settings
11. Copy `TVTest-develop\sdk\Samples\x64\Release\*.tvtp` and `TVTest-develop\sdk\Samples\x64\Release\HDUSRemocon_KeyHook.dll` to `D:\TV\TVTest\Plugins` (create directory)
12. Copy `TVTest-develop\data\*` except for `Data_x64` and `TVTest_Logo.bmp` to `D:\TV\TVTest`, and copy `Data_x64\TVTest_Logo.bmp` to `D:\TV\TVTest`

### CasProcessor (CAS Processing Plugin for TVTest)
1. Download [CasProcessor](https://github.com/logue/CasProcessor) and extract it
2. Open `CasProcessor.sln` with Visual Studio and build it with `Release` and `x64` settings
3. Copy `x64\Release\CasProcessor.tvtp` to `D:\TV\TVTest\Plugins`

### TvCas (Main CAS Processor called by CasProcessor)
1. Download [TvCas](https://github.com/logue/TvCas) and extract it
2. Open `TvCas.sln` with Visual Studio and build it with `Release` and `x64` settings
3. Copy `x64\Release\B25.tvcas` to `D:\TV\TVTest`

### LAVFilters (Video Decoder for TVTest)
1. Download [LAVFilters binary](https://github.com/Nevcairiel/LAVFilters/releases/latest) and install it

---
Here you can check whether TVTest is working or not.

### EPGStation (TV Recording/Streaming Server)
1. Open Windows PowerShell with administrator privilege from start button (right click)
2. `npm install windows-build-tools` (when freezing, Ctrl+C and retry)
3. `cd D:\TV`
4. `git clone https://github.com/l3tnun/EPGStation.git`
5. `cd EPGStation`
6. `npm install`
7. `npm run build`
8. `copy config\config.sample.json config\config.json`
9. `copy config\operatorLogConfig.sample.json config\operatorLogConfig.json`
10. `copy config\serviceLogConfig.sample.json config\serviceLogConfig.json`
11. Download [static FFmpeg binary](https://ffmpeg.zeranoe.com/builds/) and place them to `C:\\ffmpeg` for example
12. Edit `config\config.json` like this:
    ```json
    "mirakurunPath": "\\\\.\\pipe\\mirakurun",
    "ffmpeg": "C:\\ffmpeg\\ffmpeg.exe",
    "ffprobe": "C:\\ffmpeg\\ffprobe.exe",
    "encode": [
        {
            "name": "H264",
            "cmd": "C:\\PROGRA~1\\nodejs\\node.exe %ROOT%\\config\\enc.js main",
            "suffix": ".mp4",
            "default": true
        },
        {
            "name": "H264-sub",
            "cmd": "C:\\PROGRA~1\\nodejs\\node.exe %ROOT%\\config\\enc.js sub",
            "suffix": "-sub.mp4"
        }
    ]
    ```
13. `npm run install-win-service`
14. `net start epgstation` / `net stop epgstation`
