# ESP32_WROOM_32E_msd
## 作成・許諾 
Sugano Robocon Aチーム (益田隆太郎、大谷卓輝、須崎一眞、藤上晃成)  
## * このソフトウェアパッケージは、3条項BSDライセンスの下、再配布及び使用が許可されます
## @ 2024 Sugano Robocon A team
## 外観
![3Dviewr](https://github.com/Sugano-robocon-2024-A-group/ESP32_WROOM32E_PCB_mr/blob/main/pictures/3Dviewer.png)
![PCBViwer](https://github.com/Sugano-robocon-2024-A-group/ESP32_WROOM32E_PCB_mr/blob/main/pictures/PCB_editor.png)
![回路図](https://github.com/Sugano-robocon-2024-A-group/ESP32_WROOM32E_PCB_mr/blob/main/pictures/%E5%9B%9E%E8%B7%AF%E5%9B%B3_editor.png)
## 使用法
production→ESP32_WROOM_32E_msd.zipがガーバーファイルで、JLCPCBに送るとそのままPCBとして出してくれます。
## ピン配置
ESP32はESPマトリックス機能があるため、どのピンを使っても基本的に許されるが、PWMがはけるピンは決まっている。
[こちら](https://docs.sunfounder.com/projects/umsk/ja/latest/07_appendix/esp32_wroom_32e.html)を参照のこと
を参照のこと。
### 足回りピン配置
回路図の通りですが
RFはRightFront、LFはLeftFront、RBはRightBack、LBはLeftBackに対応しています。(例:RF_1とRF_2でモータドライバの正転逆転を制御、RF_PWMで角速度を制御)

GPIO22 RF_1  
GPIO23 RF_2  
GPIO16 RF_PWM  
GPIO18 LF_1  
GPIO19 LF_2  
GPIO21 LF_PWM  
GPIO32 RB_1  
GPIO33 RB_2  
GPIO04 RB_PWM  
GPIO25 LB_1  
GPIO14 LB_2  
GPIO13 LB_PWM  
足回りのモータドライバは[こちら](https://osoyoo.com/2022/02/25/osoyoo-model-y-4-channel-motor-driver/)の市販品を使用しました。

### CAN通信
いろんなサイトで26と27がRXとTXにアサインされていたので使用。うまく動きました。

GPIO26 CAN_RX  
GPIO27 CAN_TX  

### エンコーダピン配置
エンコーダで速度制御のフィードバックをかけたかったのですが、それだとピンが足りません。なのでマイコンを2個使って、CAN通信で情報をおくりあうことにしました。二つのマイコンの基板を共通化させたかったので、ピンを共有させました。
本番では速度のPID制御はあきらめてフィードフォワード制御に変えたので、このピンは使っていません。使いたかったら使うと良いと思います。
この場合、①RFとLFのモータを制御するマイコン②RBとLBのモータを制御するマイコン③上物を制御するマイコンの３つのマイコンでCAN通信をする必要があります。この時、CANバス内のノードが３つなので、終端抵抗を一つの基板だけOFFにする必要があります。SW1はそれ用のスイッチです。
また、ピンが共通になっているので、①のマイコンにおいて

GPIO22 RF_1  
GPIO23 RF_2  
GPIO16 RF_PWM  
GPIO18 LF_1  
GPIO19 LF_2  
GPIO21 LF_PWM  

をモータドライバに接続するときは、エンコーダに接続するピンは

GPIO32 RB_1  
GPIO33 RB_2  
GPIO25 LB_1  
GPIO14 LB_2  

であるという紛らわしさに注意する必要があります。②はその逆で、

GPIO32 RB_1  
GPIO33 RB_2  
GPIO04 RB_PWM  
GPIO25 LB_1  
GPIO14 LB_2  
GPIO13 LB_PWM  

をモータドライバに接続するので、エンコーダに接続するピンは

GPIO22 RF_1  
GPIO23 RF_2  
GPIO18 LF_1  
GPIO19 LF_2  

です。回路図の通りですね。
### 上物
投擲用のモータドライバは秋月で買える[TB67H450](https://akizukidenshi.com/catalog/g/g114753/)を使いました。
投擲のモータは速度フィードバックはかけず、フィードフォワードのみです。

GPIO16 RF_PWM 正転・逆転  
GPIO21 LF_PWM 正転・逆転  
GPIO04 RB_PWM PWM調整  

です。

GPIO13 LB_PWM  
GPIO05 Servo1  

を装填・仰角調整にアサインしました。
上物の電源電圧は2Sのリポバッテリー7.4Vを使用しました。XT30がコネクタです。





