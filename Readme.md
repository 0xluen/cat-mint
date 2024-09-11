
### CAT Protocol Token Mintleme Adımları

#### Gereksinimler:
- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/en/download)
- [Docker](https://docker.com/products/docker-desktop)

**Not:** Ubuntu veya diğer Linux dağıtımlarını kullanmanız gerekmektedir. (Alternatif olarak Windows'a Ubuntu terminali kurabilirsiniz)

---

#### Başlamadan Önce Yapılması Gerekenler:

1. **Sistem güncellemesi ve npm kurulumu:**
   ```bash
   sudo apt update
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
   export NVM_DIR="$HOME/.nvm"
   [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
   [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
   source ~/.bashrc
   nvm install 20
   nvm use 20
   ```

2. **Node.js sürüm yöneticisi 'n' yükleme ve Node.js güncelleme:**
   ```bash
   npm install n -g
   n stable
   ```

3. **Yarn yükleme:**
   ```bash
   npm i -g yarn
   ```

4. **Docker kurulumu:**
   ```bash
   sudo apt-get install docker.io -y
   ```

5. **Docker Compose'un en son sürümünü yükleyin:**
   ```bash
   VERSION=$(curl --silent https://api.github.com/repos/docker/compose/releases/latest | grep -Po '"tag_name": "\K.*\d')

   DESTINATION=/usr/local/bin/docker-compose
   sudo curl -L https://github.com/docker/compose/releases/download/${VERSION}/docker-compose-$(uname -s)-$(uname -m) -o $DESTINATION
   sudo chmod 755 $DESTINATION
   ```

---

#### Adımlar:

1. **Yarn'ı global olarak yükleyin:**
   ```bash
   npm install -g yarn
   ```

2. **CAT Token Box deposunu klonlayın:**
   ```bash
   git clone https://github.com/CATProtocol/cat-token-box.git
   ```

3. **cat-token-box dizinine gidin:**
   ```bash
   cd cat-token-box
   ```

4. **Gerekli bağımlılıkları yükleyin ve projeyi derleyin:**
   ```bash
   yarn install
   yarn build
   ```

5. **Tracker dizinine gidin:**
   ```bash
   cd packages/tracker
   ```

6. **Tracker için bağımlılıkları yükleyin ve derleyin:**
   ```bash
   yarn install
   yarn build
   ```

7. **Docker container'ı çalıştırın:**
   ```bash
   sudo chmod 777 docker/data
   sudo chmod 777 docker/pgdata
   docker-compose up -d
   ```

8. **Ana dizine geri dönün ve Docker image oluşturun:**
   ```bash
   cd ../../ && docker build -t tracker:latest .
   ```

9. **Tracker container'ını başlatın:**
   ```bash
   docker run -d \
   --name tracker \
   --add-host="host.docker.internal:host-gateway" \
   -e DATABASE_HOST="host.docker.internal" \
   -e RPC_HOST="host.docker.internal" \
   -p 3000:3000 \
   tracker:latest
   ```

10. **Tracker'ın fractal node ile senkronize olmasını bekleyin:**
    - Senkronizasyonu şu adresten takip edebilirsiniz: [http://127.0.0.1:3000/api](http://127.0.0.1:3000/api)
11. **Senkronizasyon Durumunu kontrol etmek için:**
   ```bash
   curl http://127.0.0.1:3000/api
   ```   

---

#### CLI Kullanarak Cüzdan ve Mintleme Adımları:

1. **CLI dizinine gidin:**
   ```bash
   cd packages/cli
   ```

2. **CLI için gerekli bağımlılıkları yükleyin ve derleyin:**
   ```bash
   yarn install
   yarn build
   ```

3. **Yeni bir cüzdan oluşturun ve adresinizi alın:**
   ```bash
   yarn cli wallet create
   yarn cli wallet address
   ```
   *Not: Bu adımda seed'i ve cüzdan adresinizi kaydetmeyi unutmayın. Cüzdanınıza FB gönderin.*

4. **CAT mintleyin:**
   *fee-rate değerini 600 olarak kullanıyorum. Çok düşük fee ile gönderirseniz mintlemeyebilir. 
   ```bash
   yarn cli mint -i 45ee725c2c5993b3e4d308842d87e973bf1951f5f7a804b21e4dd964ecd12d6b_0 5 --fee-rate 3000
   ```

   *Bu komutu her çalıştırdığınızda, seferde 5 adet CAT mintlenir.*

   ```bash
    command="yarn cli mint -i 45ee725c2c5993b3e4d308842d87e973bf1951f5f7a804b21e4dd964ecd12d6b_0 5 --fee-rate 3000"
    while true; do
      $command

        if [ $? -ne 0 ]; then
           echo "error"
          exit 1
        fi

        sleep 1
    done
   ```
    *Bu komutu her çalıştırdığınızda, sürekli olarak 5 adet CAT mintlenir.*
---



#### Artık CAT tokenları mintlemek için hazırsınız!
