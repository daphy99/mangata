# Güncelleme ve docker kurulumu, komutları sırasıyla girebilirsiniz
sudo apt update -y && sudo apt upgrade -y
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# bu komut satırını toplu girebilirsiniz
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# docker güncelleme ve run
sudo apt update -y && sudo apt upgrade -y
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world

# Go kurulumu
cd $HOME
ver="1.20.2"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
EigenLayer CLI kurulumu
# eigen klonlayalım
git clone https://github.com/Layr-Labs/eigenlayer-cli.git
cd eigenlayer-cli
mkdir -p build
go build -o build/eigenlayer cmd/eigenlayer/main.go

# Binary dosyamızı PATH yoluna gönderiyoruz
cd
sudo cp eigenlayer-cli/build/eigenlayer /usr/local/bin/
Altta ki komutlar ile KEY oluşturuyoruz..

ecdsa KEY bize bir evm adresi, private key ve dosya path (yolu) verecek

bls KEY ise bir private key verecek. Hepsini kaydedin.

Kapalı parantez dahil, <key-ismi> değiştirin, <> parantezleri kaldırın..

Her komuttan sonra şifre oluşturmanızı isteyecek, şifre karmaşık olmalı.

Örnek şifre DenemeSifre123.,#%

eigenlayer operator keys create --key-type ecdsa <key-ismi>
eigenlayer operator keys create --key-type bls <key-ismi>
Listeleyerek keyleri ve dosya yollarını kontrol edin

eigenlayer operator keys list

eigenlayer operator config create
nano operator.yaml

eigenlayer operator register operator.yaml
# Loglar aksın başarılı bir şekilde sonlanana kadar müsade edin

# Durumunu kontrol etmek için
eigenlayer operator status operator.yaml

# metadata herhangi bir değişiklik yaptığınızda güncellemek için
eigenlayer operator update operator.yaml

git clone https://github.com/mangata-finance/avs-operator-setup.git
cd avs-operator-setup
chmod +x run.sh
nano .env

# Üst kısma dokunmuyorsunuz. SADECE: TODO yazan satırın altı bizi ilgilendiriyor.
ETH_RPC_URL= goerli eth rpc http linki
ETH_WS_URL= goerli eth wss linki
# bu linkleri infuradan almıştık, zorlanırsan rues chatten yardım talep edin.

# key yolundan kastım path, yukarıda öğrenmiştiniz.
ECDSA_KEY_FILE_HOST=/.eigenlayer/operator_keys/<key-name>.ecdsa.key.json
BLS_KEY_FILE_HOST=/.eigenlayer/operator_keys/<key-name>.bls.key.json

# yukarıda bir şifre belirlemiştik her yerde kullandığımız.
ECDSA_KEY_PASSWORD= eigen cli kurulumda belirlediğimiz karmaşık key şifresi
BLS_KEY_PASSWORD= eigen cli kurulumda belirlediğimiz karmaşık key şifresi

# ctrl + x + y yapıp kaydedip çıkıyoruz

# şimdi opt-in yapalım son hamleler..
./run.sh opt-in

# key yolunda, şifrede vs hata yoksa bu adımlardan sonra docker compose up yapıyoruz ve sona geliyoruz.
docker compose up -d

# bir sorun olup olmadığını kontrol etmek için docker ps ile container id alın
docker logs -f <container_id>
# container id başta olur ve kurduğunuz node'un isminden hangi id olduğunu anlayabilirsiniz.
