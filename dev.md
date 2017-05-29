# Aprenda Bitcoin e Criptomoedas

## Resumo

O curso se destina a programadores e interessados que querem aprender mais sobre as criptomoedas, opensource, um pouco de criptografia e o funcionamento de redes descentralizadas.

## Instalando Bitcoin Core

```shell
# adiciona o repositório do Bitcoin Core
sudo add-apt-repository ppa:bitcoin/bitcoin
sudo apt-get update

# instala o bitcoin-qt e bitcoind
sudo apt-get install bitcoin-qt
sudo apt-get install bitcoind
```

## Data Directory

Pasta de configuração e onde os arquivos do blockchain são baixados.

```shell
# data directory no Linux
 ~/.bitcoin
```

```shell
# altera o data directory e o arquivo de configuração
bitcoin-qt -datadir=c:/ -conf=coco.conf
```

## Criando bitcoin.conf

- Criar arquivo bitcoin.conf no data directory

```shell
# inicia o Bitcoin como servidor
server=1

# inicia o Bitcoin na testnet
testnet=1

# configura usuário e senha para JSON-RPC
rpcuser=bitcoinrpc
rpcpassword=p0ssw0rd
```

## Iniciando e Encerrando o Bitcoin

```shell
# inicia o Bitcoin de acordo com as configurações em bitcoin.conf
bitcoin-qt &

# verifica overview do servidor rodando
bitcoin-cli getinfo

# encerra o Bitcoin
bitcoin-cli stop
```

## JSON-RPC

JSON-RPC is lightweight remote procedure call protocol similar to XMLRPC. It's designed to be simple. The general mechanism consists of two peers establishing a data connection. During the lifetime of a connection, peers may invoke methods provided by the other peer. To invoke a remote method, a request object serialized as JSON is sent. Unless the request is a notification it must be replied to with a response object serialized as JSON.

A remote method is invoked by sending a request to a remote service using HTTP or a TCP/IP socket (starting with version 2.0). When using HTTP, the content-type may be defined as application/json.

All transfer types are a single object, serialized using JSON. A request object is a call to a specific method provided by a remote system. It must contain these three properties:

- method - A String with the name of the method to be invoked.
- params - An Array of objects to be passed as parameters to the defined method.
- id - A value of any type, which is used to match the response with the request that it is replying to.

The receiver of the request must reply with a valid response object to all received requests. The response must contain these three properties:

- result - The data returned by the invoked method. If an error occurred while invoking the method, this value must be null.
- error - A specified Error code if there was an error invoking the method, otherwise null.
- id - The id of the request it is responding to.

### Exemplos

- → {“methdod”: “sum”, “params”: [1,2], “id”: 0}
- ← {“result”: 3, “error”: null, “id”: 0}

- → {“method”: “getbalance”, “params”: [], “id”: 1}
- ← {“result”: 79.50000000, “error”: null, “id”: 1}

- → {“method”: “getnewaddress”, “params”: [], “id”: 2}
- ← {“result”: “mfxyDWEtjcuEPkNXGADaick4DiWbsiTENx”, “error”: null, “id”: 2}

## Instalando um Bitcoin client JavaSript JSON-RPC API [node-bitcoin](https://github.com/freewil/node-bitcoin)

```shell
# instala npm e node.js
sudo apt install npm
sudo apt install nodejs-legacy

# instala json-rpc api bitcoin client
npm install bitcoin

# open the node REPL
node
```

```js
// require the npm package bitcoin
var bitcoin = require(“bitcoin”)

// set-up a rpc connection with Bitcoin server
var client = new bitcoin.Client({
    user: “rpcuser”,
    pass: “rpcpass”,
    host: “127.0.0.1”,
    port: “18332”
});

// bitcoin-cli getinfo
client.getInfo(function(error, info){
    console.log(error ? error : info);
});
```

At the time of writing this paragraph, node-bitcoin doesn't support all Bitcoin RPC
methods ! It's supporting methods up to Bitcoin 0.8.0, however this isn't a
problem. Node-bitcoin has a method called cmd on the bitcoin.Client object, you
can use it to call any RPC method:

```js
// getunconfirmedbalance is supported by Bitcoin Core 0.9.0+
// not exposed as bitcon.Client method
client.cmd(“getunconfirmedbalance”, function(error, balance){
    if(error){
        console.log(error.code);
        console.log(error.message);
    } else {
        console.log(balance);
    }
});
```


### Ver também [Bitcore.io](https://bitcore.io/)



## Proper Money Handling

Bitcoin Core client store all balances and transaction values as 64-bit integers,
where 1 btc = 100,000,000 satoshi (One hundred million of the smallest possible
bitcoin value, named after Satoshi Nakamoto the pseudonym of the inventor of
Bitcoin). On the other hand in JSON-RPC API values are expressed as double
precision numbers, where 1 btc is expressed as 1.00000000

Bitcoin Core Client      |   JSON-RPC
100,000,000 satoshi      |   1.00000000 btc
195,000,000 satoshi      |   1.95000000 btc
5,500,000,000 satoshi    |   55.00000000 btc
78,412,546,600 satoshi   |   784.12546600 btc

You can implement this conversion in python like this:

```py
def amountToJSON(value):
    return float(value / 1e8)

def JSONToAmount(value):
    return long(value * 1e8)
```

The general rule to convert from double to 64-bit intger is to multiply by
100,000,000 and round to the nearest integer, and to convert from 64-bit integer
to double is to divide by 100,000,000.0 and make sure that the result has 8 or
more digits after the decimal point including padding zeros.


