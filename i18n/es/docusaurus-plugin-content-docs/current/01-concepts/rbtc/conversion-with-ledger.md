---
title: Conversión utilizando un monedero físico Ledger
sidebar_label: Libro mayor
tags:
  - rsk
  - portainjertos
  - rbtc
  - conversión
  - peg
  - clavija
  - peg-out
  - federación
  - Libro mayor
description: Cómo realizar el mecanismo Powpeg utilizando Ledger.
sidebar_position: 304
---

En esta sección, repasaremos los pasos para convertir BTC a RBTC utilizando el monedero hardware de Ledger, y viceversa en las redes Bitcoin y Rootstock (RSK).

## Requisitos generales

- Necesita un [Ledger](https://www.ledger.com/) con Bitcoin y
  Rootstock Apps instalados. Le recomendamos que tenga
  [Ledger Live](https://www.ledger.com/ledger-live)
  y revise este tutorial:
- You need to have [Electrum](https://electrum.org/).
  Install it and [configure it to be used with Ledger](https://support.ledger.com/article/115005161925-zd).
- Nodo >= 10.16.0

## Conversor de BTC a RBTC

Instrucciones sobre cómo hacer un peg-in en Mainnet.

### Obtener una dirección BTC con saldo

Recomendamos utilizar el monedero Electrum BTC para conectarse a
BTC Mainnet utilizando el monedero de hardware Ledger.

- Download the wallet from
  [Electrum Website](https://coingate.com/blog/post/setup-electrum-guide)
- Instalar Electrum
- Conecta y desbloquea tu dispositivo Ledger.
- Abrir la aplicación Bitcoin
- Inicio Electrum
- Una vez iniciado Electrum, crea o importa un monedero
- En la pantalla del almacén de claves, seleccione Utilizar un dispositivo de hardware y haga clic en Siguiente.
- Seleccione su dispositivo Ledger y haga clic en Siguiente.
- Elija la ruta de derivación adecuada para su cuenta y haga clic en Siguiente:
  - Legado para una cuenta que tiene direcciones que empiezan por 1
- Vaya a la tercera pestaña "Recibir". Verás una dirección Bitcoin.

:::info[Note]
El monedero Bitcoin debe ser heredado (no Segwit)
cuya clave pública empiece por `m` o `n`,
y la clave privada empiece por `p2pkh:`.
:::

### Encontrar una dirección BTC con saldo

Tendrá que encontrar la dirección BTC correspondiente derivada
de la ruta de derivación BTC en la pestaña "Recibir" de Electrum.

- Compruebe la ruta de derivación para BTC que se va a utilizar:
  - Mainnet: `44'/0'/0'/0/0`
    [BIP 44](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki) **Legacy**
- Desbloquea Ledger y abre la **App Bitcoin**.
- Para obtener la dirección BTC derivada de la ruta de derivación que ha especificado. Ejecute el siguiente script:
  ```js
  const Transport = require("@ledgerhq/hw-transport-node-hid").default;
  const AppBtc = require("@ledgerhq/hw-app-btc").default;

  const getBtcAddress = async (derivationPath = "44'/0'/0'/0/0") => {
    try{
        const transport = await Transport.create();
        const btc = new AppBtc(transport);
        const result = await btc.getWalletPublicKey(derivationPath);

        console.log('BTC Address');
        console.log(result.bitcoinAddress);
        console.log('Derivation Path: ' + derivationPath);
    }
    catch(err){
      console.log(err);
    }
  };

  (async () => {
    await getBtcAddress("44'/0'/0'/0/0");
  })();
  ```
- Después de eso usted debe obtener un resultado similar a:
  ```text
  Dirección BTC
  12dAR91ji1xqimzdTQYHDtY....ppSR
  Ruta de derivación: 44'/0'/0'/0/0
  ```

:::tip[Tip]
Esta es la dirección que tienes que utilizar para hacer la transferencia a la federación.
:::

### Enviar Bitcoin a la dirección de Rootstock Federation

:::warning[Warning]
Usted necesita enviar una cantidad mínima de 0,01 BTC o cantidad máxima,
no más de 10 BTC para la conversión.
:::

Para obtener la dirección de Rootstock Federation puede ejecutar el siguiente script:

```javascript
const Web3 = require('web3');
const precompiled = require('@rsksmart/rsk-precompiled-abis');

const getFederationAddress = async function(){
    const bridge = precompiled.bridge.build(new Web3('https://public-node.rsk.co'));
    const address = await bridge.methods.getFederationAddress().call();
    console.log('Dirección de la Federación:');
    console.log(address);
}

(async () => {
    await getFederationAddress();
})();
```

Una vez que tenga la dirección de la Federación Rootstock, puede enviarle Bitcoin desde su dirección Bitcoin.

Utiliza Electrum para enviar BTCs a la dirección de la federación Rootstock. Para ello:

- Abrir Electrum
- Ir a la pestaña Direcciones
- Haga clic con el botón derecho del ratón
- Seleccione la opción "Gastar desde":
  ![Gastar desde](/img/concepts/peg-ledger/electrumSpendFromOption.png)
- Por último, realice un pago a la dirección de la Federación RSK
  ![Sending Payment](/img/concepts/peg-ledger/electrumSpendFrom.png)

**4 Esperar confirmaciones de BTC**

To ensure the transaction, we need to wait for 100 BTC confirmations, be patient :

:::tip[Tip]
100 bloques \* 10 minutos/bloque = 1000 minutos = 16,667 horas aprox.
:::

**5 Get RBTC address from Ledger hardware wallet**

Get the corresponding RBTC address from your Ledger hardware wallet, by following these steps:

- Conecta y desbloquea tu dispositivo Ledger.
- Abra la aplicación RSK.
- Obtener la dirección derivada de RSK ejecutando este script:
  ```javascript
    const Transport = require("@ledgerhq/hw-transport-node-hid").default;
    const AppEth = require("@ledgerhq/hw-app-eth").default;

    const getRskAddress = async (derivationPath = "44'/0'/0'/0/0") => {
      try{
          const transport = await Transport.create();
          const eth = new AppEth(transport);
          const result = await eth.getAddress(derivationPath);

          console.log('RSK Address');
          console.log(result.address);
          console.log('Derivation Path: ' + derivationPath);
      }
      catch(err){
          console.log(err);
      }
    };

    (async () => {
      await getRskAddress("44'/0'/0'/0/0");
    })();

  ```
- Go to MyCrypto and connect to Ledger hardware wallet.
- Seleccione **Dirección personalizada** y ponga la ruta de derivación `m/44'/0'/0'/0`.
  A continuación, elija la dirección que obtuvo en el paso anterior.

**6 Comprobar saldo RBTC**

You can check balance of RBTC address on MyCrypto or MEW setting the corresponding derivation path and selecting the address.

:::info[Note]
You have to wait a minimum of 100 confirmations + a minimum of 5 minutes to check your RBTC balance
:::

## Conversion RBTC a BTC

Instrucciones sobre cómo hacer un peg-out de Mainnet.

1. Obtener dirección BTC con Ledger hardware wallet

If you forgot your BTC public address, you can check section **1**.
The important thing is that the receiving BTC address will be
the same that it was used to send to the federation.

2. Enviar RBTC a Rootstock Bridge Contract

Open MyCrypto or MEW.
Set the corresponding derivation path and select the address. \
This address has to be the same as that from section **6**.
Then do a transaction to the Bridge Contract.

> Dirección del contrato puente: `0x0000000000000000000000000000000001000006`

:::info\[Note]

- The minimum amount to send in a peg-out transaction must be greater than or equal to 0.004 **RBTC** for Mainnet and the minimum amount to send in a peg-in transaction must be greater than or equal to 0.005 **BTC** for Mainnet.
- El límite de gas de la transacción debe fijarse manualmente en 100.000 gas; de lo contrario, la transacción fallará.
- El precio del gas puede fijarse en 0,06 gwei.
  :::

![Personalizar Gas en Metamask antes de enviar transacción en Rootstock](/img/concepts/metamask-gas-limit.png)

3. Comprobar el saldo de la dirección BTC

Puede utilizar el monedero Electrum descargado anteriormente o
cualquier explorador de Bitcoin para comprobar el saldo.

:::info[Note]
El proceso de liberación en la red Bitcoin lleva 4000 confirmaciones de bloque RSK y al menos 10 minutos más.
:::
