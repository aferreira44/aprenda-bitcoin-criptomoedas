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

# Send  trace and debug info to the console instead of debug.log
bitcoin-qt -printtoconsole

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

- → {"methdod": "sum", "params": [1,2], "id": 0}
- ← {"result": 3, "error": null, "id": 0}

- → {"method": "getbalance", "params": [], "id": 1}
- ← {"result": 79.50000000, "error": null, "id": 1}

- → {"method": "getnewaddress", "params": [], "id": 2}
- ← {"result": "mfxyDWEtjcuEPkNXGADaick4DiWbsiTENx", "error": null, "id": 2}

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
var bitcoin = require("bitcoin")

// set-up a rpc connection with Bitcoin server
var client = new bitcoin.Client({
    user: "rpcuser",
    pass: "rpcpass",
    host: "127.0.0.1",
    port: "18332"
});

// bitcoin-cli getinfo
client.getInfo(function(error, info){
    console.log(error ? error : info);
});
```

At the time of writing this paragraph, node-bitcoin doesn't support all Bitcoin RPC methods ! It's supporting methods up to Bitcoin 0.8.0, however this isn't a
problem. Node-bitcoin has a method called cmd on the bitcoin.Client object, you
can use it to call any RPC method:

```js
// getunconfirmedbalance is supported by Bitcoin Core 0.9.0+
// not exposed as bitcon.Client method
client.cmd("getunconfirmedbalance", function(error, balance){
    if(error){
        console.log(error.code);
        console.log(error.message);
    } else {
        console.log(balance);
    }
});
```

### Ver também [Bitcore.io](https://bitcore.io/)

## Testnet

Testnet is an alternate blockchain used mainly for developing and testing bitcoin applications without using real bitcoins and without touching the main bitcoin blockchain on the mainnet. You can obtain Testnet coins online for free as we’ll see in the next sections or you can mine them. Testnet bitcoins are useless, so don’t trade them or buy anything with them. Testnet genesis block may be reset anytime and if that happened you’ll lose all your testnet coins. At the time of writing this book we’re on Testnet 3.

Start `bitcoin-qt` or `bitcoind` with `-testnet` argument:

```shell
bitcoin-qt -testnet

bitcoind -testnet
```

or add

```shell
testnet=1
```

to the configuration file if you want to be on testnet every time you use Bitcoin Core client.

After starting bitcoin-qt you’ll see the splash screen with large green bitcoin logo instead of the orange one.

If you’re running bitcoind, call getinfo and you’ll see the testnet flag:

```shell
bitcoin-cli getinfo
```

will return something like this:

```json
{
    "version" : 90100,
    "protocolversion" : 70002,
    "walletversion" : 60000,
    "balance" : 0.11990000,
    "blocks" : 256257,
    "timeoffset" : 0,
    "connections" : 0,
    "proxy" : "",
    "difficulty" : 1088.10584687,
    "testnet" : true,
    "keypoololdest" : 1400704976,
    "keypoolsize" : 101,
    "paytxfee" : 0.00000000,
    "errors" : ""
}
```

Testnet differs from the mainnet in that:

- Testnet JSON-RPC connection port is 18332, mainnet is 8332.
- Testnet Bitcoin network protocol listens to port 18333, mainnet is 8333.
- Testnet addresses has version byte 0x6f, mainnet is 0x00.
- Testnet addresses usually start with m or n.
- Testnet has minimum mining difficulty of 1.0.
- Testnet has a different genesis block, you can find it here [http://blockexplorer.com/testnet/b/0](http://blockexplorer.com/testnet/b/0)
- The isStandard() check is disabled, so you can experiment with non standard transactions like funding/spending multi-signatures addresses, replacement transaction, contracts … etc.

## Obtaining Testnet bitcoins on [Testnet Faucet](https://testnet.manu.backend.hamburg/faucet)

Testnet bitcoins are available online for free from online faucets like TP’s Testnet Faucet http://tpfaucet.appspot.com/. If you want to get some, start bitcoin-qt on Testnet, goto “receive" tab if you"re using Bitcoin Core client up to version 0.8.6, or if you’re using Bitcoin Core client 0.9+ from File menu > “Receiving addresses", create new address from “+ new address" button and give it a label like “TP Faucet".

Wait for the client to synchronize with the network, open the TP’s Testnet Faucet website, paste your testnet bitcoin address in the website, write the captcha and press send. At the time of writing the book the website sends 9.5 bitcoins per request. When you’re done with developing and testing your kickass Bitcoin application send your Testnet bitcoins back to the address in the website.

## Mining on testnet

Verify mining by calling gethashespersec method which will return your hashing power:

bitcoin-cli getmininginfo

to get the difficulty of testnet:

bitcoin-cli getdifficulty

difficulty on testnet is increasing because testers use GPU and ASICS to mine on testnet, it’ll take a long time before your first bitcoins are generated, and generated coins will mature after 120 blocks ! In Bitcoin 0.9 generated coins mature at 101 blocks, so mining on testnet isn't practical.

### Regtest

Bitcoin Core client version 0.9 introduced `-regtest` mode, which is similar to testnet, but private with difficulty=0 and instant block generation.

Start `bitcoin-qt` or `bitcoind` with `-regtest` option:

`bitcoin-qt -regtest`

if your bitcoin.conf file contains testnet=1, you"ll be alerted of invalid combination of `-regtest` and `-testnet`:

remove testnet=1 from your bitcoin.conf file, or start bitcoin with `-notestnet` option:

`bitcoin-qt -regtest -notestnet`

Bitcoin-qt will start with private testnet (regtest) mode, you can generate new blocks any time you want and get 50 bitcoins as a block reward by calling setgenerate method:

```shell
bitcoin-cli setgenerate true
```

Generated coins will mature after 101 blocks, so if you want to obtain 50 * n
mature bitcoins , generate (n+100) blocks. Fortunately, in -regtest mode, the
second argument to setgenerate method controls how many blocks are generated
immediately, to generate 1500 mature bitcoins:

bitcoin-cli setgenerate true 130

130 blocks will generate (130-100) * 50 = 1500 mature bitcoins.

## Chapter 4: Accounts and Addresses

Bitcoin Core client has an interesting feature called "accounts", which you can use to group receiving addresses under different account names, and you can use it in your Bitcoin application to give each user an account, when an address receives some bitcoins in a transaction, funds (transaction output) will be associated with this address, and the balance of the account associated with this
address will be increase accordingly.

When you send bitcoins from an account using RPC method like sendfrom as we will see in Chapter 5: Transactions, Bitcoin Core client will select funds associated with its addresses, and account"s balance will be decreased accordingly. Think of it as a tree structure where Bitcoin Core client has several accounts, and each account has different addresses, and each address has funds from different transactions.

Wallet > account > address > fund (transaction output).

Note that information about accounts are not transmitted to the Bitcoin network,
and aren't recorded in the block chain.

### Default Account

Bitcoin comes with a default account called "" empty JSON string. Generated
coins of solo mining are credited to the default account, and sentoaddress method
debits from the default account as we will see in the discussion of sendtoaddress
method in Chapter 5: Transactions. In bitcoin-qt default account’s addresses has
no label, as indicated with (no label) in the next picture:

## Creating New Account

You can create a new account or check the address of an existing account using
getaccountaddress method, which takes one argument `account` the name of
the account.

getaccountaddress `account`

If you give getaccountaddress method a name of an account that doesn’t
previously exist it will create new account and associate a new receiving address
to this account. If you want to check the address of the default account:

bitcoin-cli getaccountaddress ""

if you want to create a new account:

bitcoin-cli getaccountaddress "farghaly"

this will create a new account called "farghaly" if it doesn’t exist, and will create a new receiving address, and will associate this address to the new account, so any bitcoins sent to this address will be credited “farghaly" account.

Account names are case-sensitive and can be any string except "*" because it’s
used as wildcard instead of account name as we will see later in the discussion of
getbalance method. If you tried to create account with name "*":

bitcoin-cli getaccountaddress "*"

you'll get "Invalid account name" error with code -11.
The next two method calls will generate two receiving addresses and associate
them with two different accounts due to account name case-sensitivity:

bitcoin-cli getaccountaddress foo
bitcoin-cli getaccountaddress FOO

getaccountaddress method will return the same address until bitcoins are received on that address, it will generate and return new address. why ? to improve your anonymity and make it a bit difficult for other people to track how many bitcoins you have and where you’re spending them. Here’s how it works

bitcoin-cli getaccountaddress foo → D1
bitcoin-cli getaccountaddress foo → D1

someone sent 5 bitcoins to address D1

bitcoin-cli getaccountaddress foo → D2

This doesn’t mean that addresses D1 will become useless afterwards. This
address will work as long as bitcoin network is up and running.

This method comes in handy in services like online wallet, where you display to
each user in his profile page his main bitcoin address, like www.blockchain.info
my wallet page as shown in this figure. When someone send you bitcoins to this
address it’ll change automatically the next time the site call getaccountaddress
method.

## Wallet

```shell
listaccounts

# retorna os endereços por conta ou cria um novo endereço para uma nova conta, caso o nome da conta não exista

getaccountaddress ""
getaccountaddress "account"

getaccount "address"

getaddressesbyaccount ""
getaddressesbyaccount "account"

getnewaddress ""
getnewaddress "account"

# Retorna a chave pública do endereço, caso o endereço seja válido
validateaddress "address"

# Altera a conta proprietária de um endereço
setaccount <address> <account>

```

## Technical Details of Bitcoin Address

- Bitcoin uses Elliptic Curve Digital Signature Algorithm “ECDSA" to generate private and public keys, and to sign transaction and verify signatures.
- Private key is a random 256-bit number. Public key is 512-bit number calculated from the private key, but the private key can’t be calculated from the public key.
- Bitcoin address is a 160-bit hash of the public key. Address can’t be calculated from public key.
- Private key is used to sign transactions spending funds received on an address and is kept secret in your wallet.dat file, if you lose it, funds received on its address are lost forever.
- Anyone who knows your public key can verify that the signature is valid and you’re the owner of bitcoins in these transactions and hence valid.

- Private key → public key → address.

## Steps to create a Bitcoin address

Given a random private key

18E14A7B6A307F426A94F8114701E7C8E774E7F9A47E2C2035DB29A206321725

• Take the corresponding public key derived from it

0450863AD64A87AE8A2FE83C1AF1A8403CB53F53E486D8511DAD8A04887E5B23522CD470243453A299FA9E77237716103ABC11A1DF38855ED6F2EE187E9C582BA6

• Perform SHA-256 on (2)

600FFE422B4E00731A59557A5CCA46CC183944191006324A447BDB2D98D4B408

• Perform RIPEMD-160 on (3)

010966776006953D5567439E5E39F86A0D273BEE

• Add network version bytes in front of (4), network bytes is 0x00 for mainnet and 0x6f for testnet3

00010966776006953D5567439E5E39F86A0D273BEE

• Perform SHA-256 on (5)

445C7A8007A93D8733188288BB320A8FE2DEBD2AE1B47F0F50BC10BAE845C094

• Perform SHA-256 again but this time on (6)

D61967F63C7DD183914A4AE452C9F6AD5D462CE3D277798075B107615C1A8A30

• Take the first four bytes of (7), this is the address checksum. Checksum is
used for address validation and detecting errors. as we"ll see in Chapter 7:
Keys

D61967F6

• Add this checksum to the end of (5)

00010966776006953D5567439E5E39F86A0D273BEED61967F6

• Perform Base58Check on (9)

16UwLL9Risc3QfPqBUvKofHmBQ7wMtjvM

Congratulations, now you have a valid mainnet bitcoin address, you can import its
private key to your bitcoin client using importprivkey method and use it right away. But if you tried to import our address’s ECDSA private key using importprivkey method you’ll get “Invalid private key" error with code -5, why ?
because importprivatekey need another format of the private key called WIF “wallet import format", for more information on how to get the WIF from ECDSA format, goto Chapter 7: Keys.

Note that Base58Check in setp (10) is a variant of the base58 encoding to eliminate visual ambiguity of similarly looking characters in some fonts like zero 0 and uppercase o “O", lowercase L “l" and Uppercase i “I".

## Creating New Addresses

Bitcoin addresses are free, you can generate tens of thousands of them at no cost,
you can generate these addresses offline ! with a probability that two users generate the same address of 1 in 2^160, this is like searching for an atom in our expanding universe, Bitcoin address space contains about 1,460,000,000,000,000,000,000,000,000,000,000,000,000,000,000,000 addresses !

If bitcoin address collision happened, users who have the private key of the address can spend funds received on this address if any, there’re chances that this address is abandoned or has zero funds.

Use getnewaddress method to generate new address, which takes one optional argument [account] the name of the account to associate the new address to
getnewaddress [account]
If you omit [account] the generated address will be associated to the default
account “".

```shell
# Generate a new address and associate it to the account named “farghaly".
bitcoin-cli getnewaddress "farghaly"

# Generate a new address and associate it to the default account “".
bitcoin-cli getnewaddress ""

# Generating new address for account named “*":
bitcoin-cli getnewaddress “*"
```

will return “Invalid account name" error with code -5.
You should give your application’s users the ability to create new addresses and
labeling them for different uses so they can remember what they’re intended to
be used for, for example on blockchain.info wallet i’ve created an address for
cloud mining, another address for this book for receiving tips … etc. Labeling
addresses isn’t a bitcoin feature, implement it yourself.

## Address’s Public key

If you want to get the public key of an address, use validateaddress method to
make sure that the address is valid. If the address is valid, this method will return an object with pubkey property containing the public key:

validateaddress `address`

Here’s an example:

```py
address = “mvCVuo5KcbuSWRCXgcFcnZAyKotoyoFbEw"
res = bitcoin.validateaddress(address)
valid = res[“isvalid"]
if valid:
print res[“pubkey"]
```

this method will return pubkey if the address is valid and in your local wallet.dat file, you can’t use it to get the public key of foreign address. Public key can’t be calculated form address because address is a 160-bit hash of the public key, validateaddress simply fetches pubkey value from your wallet.dat file.

### What"s the use of public key?

Public key is used to verify signatures created by its mathematically associated
private key.

## Address’s Private key

If you want to get the private key -in wallet import format- of an address, use
dumpprivkey method:

dumpprivkey `address`

Note that, this method require unlocked wallet. Use walletpassphrase method to
unlock your wallet and store the wallet decryption key in memory for <timeout>
seconds:

walletpassphrase <passphrase> <timeout>

for more information on working with wallets, check Chapter 8: Wallets.

Here’s an example:

```py
address = “mvCVuo5KcbuSWRCXgcFcnZAyKotoyoFbEw"
# unlock your wallet for 1 minute if it"s locked
bitcoin.walletpassphrase(“my password", 60)
#private key in wallet import format
privkey = bitcoin.dumpprivkey(privkey)
print privkey
```

### What"s the use of private key ?

Private key is used to sign data. Bitcoin Core client uses address"s private key to sign transactions spending funds received on this address as a proof of ownership. If you own the private key, you own the address and the funds received on this address. So keep it secret in an encrypted wallet.

## Account’s Receiving Addresses

As you’ve seen getaccountaddress method create new address every time the
previous address received bitcoins, and getnewaddress can associate arbitrary
number of addresses to any account. To get all receiving addresses associated
with an account, use getaddressesbyaccount method:

getaddressesbyaccount <account>

where <account> is the name of the account, it will return an empty list if there’s no such account name or if account is associated with no addresses in odd cases
as we will see in the discussion of move method the next chapter, and it will
return a list of all receiving addresses associated with <account> if it does exist:

bitcoin-cli getaddressesbyaccount “foo"

will return something like:
[“address1", “address2", ...]

## Getting Account of an Address

If you want to get the name of the account associated with an address use getaccount method:

getaccount <address>

where <address> is the bitcoin receiving address:

bitcoin-cli getaccount “mkwMkWEVTJDe6Qkb7SE3nLwYSfLChotwxj"

will return “" the name of the default account.
getaccount method like all other methods that deals with bitcoin addresses, if you
call it with invalid address like 123456 , it will return “Invalid Bitcoin address" error with code -5.

Another issue to keep in your mind is that calling getaccount on foreign address
that isn’t in your local wallet will return “" empty string! this is the name of the default account. The solution to this problem is to validate the address using
validateaddress method and making sure that it’s in your local wallet.

## Validate Bitcoin Address

You can check the validity of an address using validateaddress method:

validateaddress <address>

where <address> is the bitcoin address, it will return an object with these
properties:

1) address - the address you’re validating.
2) account - account associated with this address
3) isvalid - true if address is valid, false otherwise.
4) ismine - true if address is in your local wallet and you’ve its private key,
false if foreign one.
5) iscompressed – true if address"s public key is compressed.
6) isscript – if address is script hash, i.e: multi-sig address.
7) pubkey - the public key from which this address was derived.
8) redeemScript – if address is script hash, redeemScript will contain the
script needed to spend funds on this address.

Here"s an example:

```py
addr = "31uEbMgunupShBVTewXjtqbBv5MndwfXhb"
bitcoin.validateaddress(addr)
will return:
```

```json
{
    "isvalid" : true,
    "address" : "31uEbMgunupShBVTewXjtqbBv5MndwfXhb",
    "ismine" : false
}
```

As you can see in the previous output, address 31uEbMgunupShBVTewXjtqbBv5MndwfXhb is perfectly valid bitcoin address but isn’t mine.

Another example:

```shell
bitcoin-cli validateaddress 123456
```

will return:

```json
{ "isvalid" : false }
```

Another example:

```py
addr = “mvCVuo5KcbuSWRCXgcFcnZAyKotoyoFbEw"
bitcoin.validateaddress(addr)
```

will return:

```json
{
    "account": "",
    "address": "mvCVuo5KcbuSWRCXgcFcnZAyKotoyoFbEw",
    "iscompressed": true,
    "ismine": true,
    "isscript": false,
    "isvalid": true,
    "pubkey": "02c00edf56427eb69c9fd70886bd5554e6aa9bb4f105d414151096bdff6f1ec462"
}
```

Note that if you’re on testnet and trying to validate a valid address on the
mainnet, it will return that the address is invalid. In other words valid addresses on testnet are invalid on mainnet and vice versa.

## Changing Account-Address Association
Accounts and addresses are associated using getaccountaddress and getnewaddress methods as you’ve seen earlier. This bond can be dissociated and changed using setaccount method:

setaccount <address> <account>

where <address> is the bitcoin address, and <account> is the name of the account. Coins previously received on <address> will be debited from the previous account balance and will be credited to the address’s new account.

setaccount method will change accounts balance and the new account associated with the address can spend funds received on this address using sendfrom method.

Suppose that account A1 is associated with two addresses D1 & D2, account A2 is
associated with address D3. Address D1 received 15 bitcoins, and address D2 received 25 bitcoins.

Bitcoin-cli getbalance “A1"
bitcoin-cli getbalance “A2"

will return 40 and 0 respectively.

Bitcoin-cli getaddressesbyaccount “A1"
bitcoin-cli getaddressesbyaccount “A2"

will return:

[D1, D2]
[D3]

respectively.

Let’s associate address D2 to account A2 and see what will happen:

bitcoin-cli setaccount “D2" “A2"

let’s check balances and receiving addresses of each account:

bitcoin-cli getbalance “A1"
bitcoin-cli getbalance “A2"

will return 15 and 25 respectively.

Bitcoin-cli getaddressesbyaccount “A1"
bitcoin-cli getaddressesbyaccount “A2"

will return:

[D1]
[D2, D3]

respectively.

Calling setaccount method on an address and an account that are already associated to each other, will create a new address and associate it to that account. So for example if account A3 is associated to address D4:

bitcoin-cli setaccount “D4" “A3"

will create a new address D5 and associate it to A3.

bitcoin-cli getaddressesbyaccount A3

will return:

[D4, D5]

so be careful.

If your “receiving addresses" list contains 2 addresses only, one associated with the default account “", and one associated with account “Foo". If you associate default account's address to account Foo, a new address will be created and associated to the default account ! Why ? Because there should be at least 1 address associated with the default account, because sendtoaddress method debits the default account and coins generated from mining are credited to the default account.

Note that setaccount works on foreign addresses ! If you associate an account
with foreign address, address will be added to your receiving addresses list, but
you won't be able to spend funds received on this address because you don't have
its private key ! You can check if the address is yours using validateaddress
method before association.

## Listing All Accounts

If you want to get all account names and their balances, use listaccounts method:

listaccounts [minconf=1]

which will return an object that has account names as keys and account balances as value, set [minconf] to 6 to get the confirmed balance, for example:

bitcoin-cli listaccounts 6

will return something like:

```json
{
    "": 65.877,
    "foo": 12.9987,
    "bar": 20.448
}
```

## Checking Account’s Balance

You can check the balance of a specific account using getbalance method:

getbalance [account] [minconf=1]

where [account] is the name of the account. If [account] is not specified, it'll return the wallet's total available balance. If [account] is specified, returns the balance in the account. [minconf] is the number of the minimum confirmations,
set it to 6 to get the confirmed balance, set it to 0 to get the confirmed balance + unconfirmed balance, subtract to get the unconfirmed balance, [minconf]default value is 1 if omitted.

bitcoin-cli getbalance “" 6
bitcoin-cli getbalance “" 0

If you want to get the wallet’s total balance, omit [account]:

bitcoin-cli getbalance

if you want to get the wallet’s total confirmed balance, set [account] to the wildcard “*", and set [minconf] to 6:

bitcoin-cli getbalance “*" 6

If you call getbalance method with a name of an account that doesn’t exist, it’ll
return 0.0 not an error and won’t create new account.

Bitcoin Core client version 0.9.0 introduced getunconfirmedbalance which returns
the server's total unconfirmed balance.

bitcoin-cli getunconfirmedbalance

## Getting Received by Account and Address

Note that getbalance method returns the sum of bitcoin amount in all transaction
categories (including received, sent, immature, generated, and moved), on the other hand getreceivedbyaccount method returns the sum of bitcoin amount in received transactions category only:

getreceivedbyaccount <account> [minconf=1]

If the default account “" received 110 bitcoins, sent 20 bitcoins using sendfrom
method as we will discuss in the next chapter, got 25 generated bitcoins from mining:

bitcoin-cli getbalance “"

will return 115.0, this is the amount received minus the amount sent, on the other
hand:

bitcoin-cli getreceivedbyaccount ""

will return 110.0, this is the amount received, it doesn't has be the same as
account's balance.

Another method for checking balance is getreceivedbyaddress which return the
bitcoin amount received by a given address with minimum confirmations condition:

getreceivedbyaddress "address" [minconf=1]

A more detailed version is listreceivedbyaccount method which returns an array of
objects, each object contains three keys:

- account - name of the account of the receiving addresses.
- amount - total amount received by addresses associated with this account.
- confirmations - number of confirmations of the most recent transaction.

listreceivedbyaccount [minconf=1] [includeempty]

set [includeempty] to true to include accounts with 0 amount.

bitcoin-cli listreceivedbyaccount 0 true

will return something like this:
```json
[
    {
        "account": "",
        "amount": 89.5584,
        "confirmations": 12
    },
    {
        "account": "foo",
        "amount": 24.6844128,
        "confirmations": 6
    },
    {
        "account": "bar",
        "amount": 0.00000000,
        "confirmations": 0
    }
]
```

Another method is listreceivedbyaddress which is the same as listreceivedbyaccount except that the result is grouped by address:

listreceivedbyaddress [minconf=1] [includeempty=false]

set [includeempty] to true to include addresses with 0 amount:

bitcoin-cli listreceivedbyaddress 0 true

will return something like:

```json
[
{
"account": "foo",
"amount": 1,
"address": "address1...",
"confirmations": 12
},
{
"account": "foo",
"amount": 15,
"address": "address2...",
"confirmations": 75
},
{
"account": "bar",
"amount": 0.00000000,
"address": "address3...",
"confirmations": 0
},
{
"account": "bar",
"amount": 15.85475600,
"address": "address3..."
"confirmations": 8
}
]
```

Bitcoin Core client version 0.9.0 added transaction ids array to the output of listreceivedbyaddress method, to indicate the transactions in which each address
received funds:

```json
[
{
"address" : "mqwbK1tpnN2f8X6yZwW2ZSqMGKjD8fyhoy",
"account" : "Foo",
"amount" : 0.32000000,
"confirmations" : 1165,
"txids" : [
"c3ffb2732f4511f380010bd122a35fc6416101fc7c073a0ab
4a16a39cbb5b4ed"
]
},
{
"address" : "mveBRwjGDs5nQ4oofEhsb6y2W2bqmgeiSD",
"account" : "Foo",
"amount" : 2.00000000,
"confirmations" : 1165,
"txids" : [
"eb707d6144e5d75666bfdb94852da9d2d7b808ba0f44f3a93
6da6a761702b5d0"
]
}
]
```

you can inspect these transactions using gettransaction and getrawtransaction
methods as we will see in Chapter 5: Transactions.

## Proper Money Handling

Bitcoin Core client store all balances and transaction values as 64-bit integers,
where 1 btc = 100,000,000 satoshi (One hundred million of the smallest possible
bitcoin value, named after Satoshi Nakamoto the pseudonym of the inventor of Bitcoin). On the other hand in JSON-RPC API values are expressed as double precision numbers, where 1 btc is expressed as 1.00000000

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


