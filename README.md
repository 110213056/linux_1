# 樹莓神秘隱者

## Concept Development
<!-- Why does your team want to build this idea/project?  -->
想去瑟瑟的網站或做壞壞的事情又不想被發現身分

## Implementation Resources
<!-- e.g., How many Raspberry Pi? How much you spent on these resources? -->
| 名稱          | 數量 |
| ------------- | ---- |
| raspberry pi3 | 1    |
| SD卡        | 1    |
| 螢幕  | 1    |
| HDMI線        | 1    |
| zyxel路由器    | 1    |

## Existing Library/Software
<!-- Which libraries do you use while you implement the project -->
系統安裝 - Pi imager

作業系統 - ubuntu

VPN設定 - PiVPN


## Implementation Process
<!-- What kind of problems you encounter, and how did you resolve the issue? -->
大致流程圖
```mermaid
graph TD;

  A[幫樹梅派灌入系統] -->|安裝完成| B[設置固定內網IP];
  B -->|設置完成| C[設置固定外網IP or DDNS];
  C -->|設置完成| D[幫樹梅派灌入VPN軟體];
  D -->|安裝完成| E[產生伺服器SSL/使用者帳戶];
  E -->|設置完成| F[在欲連線裝置上下載VPN客戶端];
```
### 實作過程
先在Pi OS lite開啟SSH功能

![image](https://github.com/110213056/linux_1/assets/148735788/02571e8f-7061-4269-a29a-32fbb662c687)

取得樹梅派的內網IP
```
hostname -I
```
![image](https://github.com/110213056/linux_1/assets/148735788/c77b9cfd-85d0-4312-94e5-b9c58ad8f0be)

SSH到樹梅派
```
ssh <用戶名>@<內網IP>
```

路由器設定固定IP(帳號密碼可Google路由器機型取得)

![image](https://github.com/110213056/linux_1/assets/148735788/b5060b49-9375-4c04-a2cd-0b7e1e8ca186)

瀏覽器的gateway IP通常是192.168.1.1 or 192.168.1.0

![image](https://github.com/110213056/linux_1/assets/148735788/0d748e19-6ba5-4a4b-a7c8-1f408602ab9c)

到LAN setting裡設定static DCHP

![未命名](https://github.com/110213056/linux_1/assets/148735788/392c8820-9463-4bd2-ad15-5971223f5841)

輸入樹梅派的MAC和想要的內網IP

![image](https://github.com/110213056/linux_1/assets/148735788/021e0d2c-c029-4a7f-8726-f13cb9baf932)

重啟樹梅派，刷新IP
![image](https://github.com/110213056/linux_1/assets/148735788/2e0dd634-2a86-4368-99b3-a5bd72bf6d7d)

接著我們想做
![image](https://github.com/110213056/linux_1/assets/148735788/6619f931-b4e5-44e9-9169-4e987ca2b54e)

到NAT設定Port forwarding
![image](https://github.com/110213056/linux_1/assets/148735788/1625c3ac-993f-444c-a8a6-ecd0135a4f4f)


指定一個port 轉到樹梅派的內網IP上
![image](https://github.com/110213056/linux_1/assets/148735788/855b9307-f677-4c67-a127-50fd4a30ccec)

設定外網固定IP(固定IP已向網路供應商申請)
![image](https://github.com/110213056/linux_1/assets/148735788/87b6833c-80e3-4b39-a6be-0aecbae97127)

到WAN setting設定PPPoE的選項
![image](https://github.com/110213056/linux_1/assets/148735788/9ed91ac8-8cff-48df-949d-70e634b264b3)

接著上網查看自己外網IP是否正確即可

到NoIP申請一個域名
![image](https://github.com/110213056/linux_1/assets/148735788/3b9e82f9-7b79-4d7b-abc6-26ebbe5ca3bc)

用Ping檢查
![image](https://github.com/110213056/linux_1/assets/148735788/c3a50def-edb2-4e42-b5c6-085f01ed1e4a)

在樹梅派裝NoIP給的客戶端，定時更新DDNS
```
mkdir /home/<用戶名>/noip
cd /home/<用戶名>/noip
wget https://www.noip.com/client/linux/noip-duc-linux.tar.gz
```

下載好記得解壓縮，並且登入剛創的noip帳號
```
tar vzxf noip-duc-linux.tar.gz
cd /noip-2.1.9-1
sudo make
sudo make install
```
安裝PiVPN，按照以下設定
![image](https://github.com/110213056/linux_1/assets/148735788/7e2f6805-0c6f-46e6-8bf4-d7aba44c59c6)
![image](https://github.com/110213056/linux_1/assets/148735788/0924327e-7912-46f0-8620-12d10c3c9719)
![image](https://github.com/110213056/linux_1/assets/148735788/0e32747c-edd8-45fc-8478-fdc26d58fd98)

這邊選擇剛剛設定要用的port
![image](https://github.com/110213056/linux_1/assets/148735788/b81160b4-a543-459a-bae6-5184a0c685bf)
![image](https://github.com/110213056/linux_1/assets/148735788/b923711b-1ccb-4e02-abae-e171502b2000)
![image](https://github.com/110213056/linux_1/assets/148735788/dcbe9068-63e9-470a-8bef-4fbd211a3d2e)

開啟自動安全性更新
![image](https://github.com/110213056/linux_1/assets/148735788/2a5d868c-1f4b-4ff2-ac0c-885f2423af83)

輸入 pivpn -a 增加一個新的使用者，會生成一個使用者設定檔，在/home/<用戶名>/configs
![image](https://github.com/110213056/linux_1/assets/148735788/3506ad58-6825-467d-aea3-0d1b95ca9733)



## Knowledge from Lecture
<!-- What kind of knowledge did you use on this project? -->

### VPN
**原理：**
透過VPN伺服器做為跳板，相當於用別人的電腦上網，可以事先講好通訊協定

### 固態IP & DDNS
**原理：**
相對於動態IP，是固定不變的IP;
動態(Dynamic)DNS伺服器，定時更新DNS所對應IP

### LAN and WAN
**原理：**
LAN使用內網IP，WAN使用外網IP

### Port forwarding
**原理：**
將從一個網路中的特定port接收到的數據流量轉發到另一個網路中的指定port

## Installation
<!-- How do the user install with your project? -->
PiVPN
```sh
curl -L https://install.pivpn.io | bash
```

固定IP - 跟中華電信申請

![image](https://github.com/110213056/linux_1/assets/148735788/0b1c88cc-574b-432a-9840-601361b44f62)

註冊NoIP

![image](https://github.com/110213056/linux_1/assets/148735788/b9610e9e-3da4-4111-8df4-d64eb71527eb)


下載wireguard(或手機下載)
https://www.wireguard.com/install/

![image](https://github.com/110213056/linux_1/assets/148735788/1f953dc1-dd00-4d03-8b0a-9ab28f049359)

## Usage
<!-- How to use your project -->
用scp下載在樹梅派上的檔案
```
scp <用戶名>@<內網IP>:/home/<用戶名>/configs/<VPN用戶名>.conf <VPN用戶名>.conf
```

![image](https://github.com/110213056/linux_1/assets/148735788/99055d77-28d3-42ca-bb0f-996e590d9956)

在PC端使用(下載wireguard的VPN客戶端)

![image](https://github.com/110213056/linux_1/assets/148735788/9f5df1af-2ef8-4789-b33e-0d65c04ab500)

載安著手機端使用(下載 Wireguard VPN客戶端 > 匯入檔案)

![image](https://github.com/110213056/linux_1/assets/148735788/97c1947c-0c5e-4a69-ba35-e020ed100828)

使用VPN前後在SSH上的差別

![image](https://github.com/110213056/linux_1/assets/148735788/4cce8bc1-a4d7-4757-86f5-a2dcb16b589a)
## 未來展望
在烏俄戰爭之後，因為俄國的網路管控原因，導致俄國人必須要用vpn才連線到外國的網站，中國也是如此，為了讓小蝦迷戰勝大鯨魚，我們希望未來能將vpn作為一個反滲透武器來使用。


## Job Assignment

|  實做  | 查資料 | github |  PPT  |  報告 | 
| ----- | ------ | ------ | ----- | ----- |
| 吉凱聖 | 吉凱聖 | 廖承偉 | 王彥仁 | 王彥仁 |
| 廖承偉 | 王新友 | 吳哲岳 | 王新友 |   -   | 
| 吳哲岳 | 廖承偉 | 王新友 | 吉凱聖 |   -   |
| 王彥仁 | 吳哲岳 |   -   |   -   |   -   | 

## References
https://github.com/pivpn/pivpn
## 簡報連結
[https://docs.google.com/presentation/d/1KQSmEWhO1FUNpS2-b8gzUJlNwKOKJmb0z7OTwvtu_cU/edit?usp=sharing](https://docs.google.com/presentation/d/1Ym1DUYAKkZOn0Syi1hsnw0TzLAo4hQCPzZPYPD0_Avs/edit?usp=sharing)https://docs.google.com/presentation/d/1Ym1DUYAKkZOn0Syi1hsnw0TzLAo4hQCPzZPYPD0_Avs/edit?usp=sharing
