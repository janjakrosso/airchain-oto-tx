# Airchain oto tx
>Herhangi bir sunucumuza girelim indirmeleri ve ayarları yapalım.
```console
mkdir oto-tx
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
source ~/.nvm/nvm.sh
nvm install 20
nvm use 20
cd oto-tx
# Burayı hepsine enter diyerek geçebiliriz.
npm init -y
npm install
npm install ethers
```
```console
touch index.js
nano index.js
```
>Burada aşağıda vereceğim dosyayı düzenleyerek yapıştıralım. 5 adet değişecek kısım var hepsinin yanına açıklamalarını yazdım.

```
const { ethers } = require('ethers');


const provider = new ethers.providers.JsonRpcProvider('http://SUNUCUIP:8545/'); // Sunucuip'yi düzenleyelim.

const account = 'MMAdresin'; // Burada içinde tEVMOS bulunan mm adresini yaz
const privateKey = 'privatekey'; // Buraya da bir üst satırda mm adresini yazdığımız cüzdanın private keyini yazmalısın.
const wallet = new ethers.Wallet(privateKey, provider);

const sendTransaction = async () => {
    try {
        const tx = {
            to: 'alıcımmadresi', // Buraya aynı cüzdana bir hesap daha ekleyip onun mm adresini yazabilirsin. Ayrıca tEVMOS'ların hepsi bu adrese geldiğinde tekrar elle asıl cüzdanımıza atıp scripti devam ettirelim.
            value: ethers.utils.parseEther('0.1'), // gönderilecek miktar (0.1 tEVMOS)
            gasLimit: 2000000, // gas limiti
            chainId: 1234 // buradaki chain id'yi kendinize göre değiştirin.
        };

        const transaction = await wallet.sendTransaction(tx);
        console.log('Transaction successful with hash:', transaction.hash);
    } catch (error) {
        console.error('Transaction failed:', error);
    }
};

const getRandomInterval = (min, max) => {
    return Math.floor(Math.random() * (max - min + 1) + min);
};

const startSendingTransactions = () => {
    const interval = getRandomInterval(5000, 15000); // 5 ile 15 saniye arasında rastgele bir süre. Burayı kendine göre düzenleyebilirsin.

    sendTransaction();

    setTimeout(startSendingTransactions, interval);
};

// Başlangıçta rastgele bir süre bekleyip işlemleri göndermeye başla
setTimeout(startSendingTransactions, getRandomInterval(5000, 15000));

provider.getNetwork().then((network) => {
    console.log(`Connected to network ${network.name}`);
}).catch((error) => {
    console.error('Error getting network:', error);
});

provider.on('network', (newNetwork, oldNetwork) => {
    if (oldNetwork) {
        console.log(`Network changed from ${oldNetwork.name} to ${newNetwork.name}`);
    }
    console.log(`${account} is ready. Please wait`);
});
```
```console
node index.js
```
>Eğer başarılı ise şu şekilde gözükecek.


![image](https://github.com/janjakrosso/airchain-oto-tx/assets/121451942/ea94c7ca-14b0-4f68-a1d8-90cdb1a4a234)


>Eğer ctrl+c yaparsanız script durur. Arka planda çalışması için şu komutu girelim. (Tabi arka planda çalıştırmak için elimizle durduralım. Ctrl+c)
```console
nohup node index.js &
```
>Bununla da çalışıp çalışmadığını kontrol edebiliriz.
```console
ps -ef | grep node
```
>Takıldığınız yer olursa dc: janjakrosso tg: validatorsupreme
