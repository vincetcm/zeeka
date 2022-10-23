    IP=`wget -qO- eth0.me`
    DISCORD=DISCORD1234    #<<< Bu kısım değişecek, Discord adınızı yazın




**Değişkenleri bash dosyasına kaydediyoruz.**
```bash
echo "export IP="${IP}"" >> $HOME/.bash_profile
echo "export DISCORD="${DISCORD}"" >> $HOME/.bash_profile
source $HOME/.bash_profile

```

**Güncelleme ve gereksinimleri yüklüyoruz**

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install wget jq git libssl-dev cmake -y
sudo apt install curl build-essential gcc make -y
```


**Rustu Yüklüyoruz**
```bash
curl https://sh.rustup.rs -sSf | sh -s -- -y
source ~/.cargo/env
```


**Bazuka kurulumuna başlıyoruz**

```bash
cd # ana dizine çıktık
rm -r bazuka #varsa eski bazuka klasörünü siliyoruz
git clone https://github.com/zeeka-network/bazuka && cd bazuka
git pull origin master
cargo install --path .   #noktayı unutma
```



**Bazukayı init edelim**

```bash
cd
bazuka init --seed "BURAYA SENIN KELIMELERIN" --network chaos --node 127.0.0.1:8765

```



**Servis dosyası oluşturuyoruz, bu kodları tek seferde kopyalayın**

```bash
sudo tee <<EOF >/dev/null /etc/systemd/system/zeekad.service
[Unit]
Description=Zeeka node
After=network.target
[Service]
User=$USER
ExecStart=`RUST_LOG=info which bazuka` node --listen 0.0.0.0:8765 --external $IP:8765 --network chaos --db /root/.bazuka-chaos --bootstrap 195.201.222.205:8765 --bootstrap 34.132.176.19:8765 --discord-handle "$DISCORD"
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF






sudo systemctl daemon-reload
sudo systemctl enable zeekad
sudo systemctl restart zeekad

sudo journalctl -f -u zeekad

```
**Faydalı komutlar**

**Logları izlemek için bu komutu kullanın, işiniz bittiğinde CRTL+C ile çıkın**
```bash
sudo journalctl -f -u zeekad
```

**Versiyon, node sayısı ve block bilgileri için**
```bash
bazuka status
```
**Cuzdan bilgileriniz için**

```bash
bazuka wallet
```

**Güncelleme olduğunda alttaki kodları terminalinize yapıştırın**

```bash
sudo systemctl stop zeekad
cd $HOME/bazuka
git pull origin master
cargo update
cargo install --path .
rm -rf /root/.bazuka-chaos
sudo systemctl restart zeekad
sudo journalctl -f -u zeekad
```


