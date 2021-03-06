---
description: Quick Start For Dapps with Examples
---

# Dapps \(Browser\)

## Quick Start For Dapps \(Client SDK - browser\)

{% hint style="info" %}
You can use the **Mock Wallet** to test your integration at [test.walletconnect.org](https://test.walletconnect.org) \([Source code](https://github.com/WalletConnect/walletconnect-mock-wallet)\). Keep in mind that this is **not a secure wallet - Do not store funds**.
{% endhint %}

### Install

{% tabs %}
{% tab title="yarn" %}

```bash
yarn add @walletconnect/browser
```

{% endtab %}

{% tab title="npm" %}

```bash
npm install --save @walletconnect/browser
```

{% endtab %}
{% endtabs %}

{% hint style="success" %}
Syntax shown below is Javascript ES6 which requires bundling and transpiling to run in web browsers.
If unfamiliar we recommend setting up an environment using [Webpack Starter](https://github.com/wbkd/webpack-starter) or [Create React App](https://github.com/facebook/create-react-app)
{% endhint %}

### Initiate Connection

```javascript
import WalletConnect from "@walletconnect/browser";
import WalletConnectQRCodeModal from "@walletconnect/qrcode-modal";

/**
 *  Create a walletConnector
 */
const walletConnector = new WalletConnect({
  bridge: "https://bridge.walletconnect.org" // Required
});

/**
 *  Check if connection is already established
 */
if (!walletConnector.connected) {
  // create new session
  walletConnector.createSession().then(() => {
    // get uri for QR Code modal
    const uri = walletConnector.uri;
    // display QR Code modal
    WalletConnectQRCodeModal.open(uri, () => {
      console.log("QR Code Modal closed");
    });
  });
}

/**
 *  Subscribe to connection events
 */
walletConnector.on("connect", (error, payload) => {
  if (error) {
    throw error;
  }

  // close QR Code Modal
  WalletConnectQRCodeModal.close();

  // get provided accounts and chainId
  const { accounts, chainId } = payload.params[0];
});

walletConnector.on("session_update", (error, payload) => {
  if (error) {
    throw error;
  }

  // get updated accounts and chainId
  const { accounts, chainId } = payload.params[0];
});

walletConnector.on("disconnect", (error, payload) => {
  if (error) {
    throw error;
  }

  // delete walletConnector
});
```

### Send Transaction \(eth_sendTransaction\)

```javascript
/**
 *  Draft transaction
 */
const tx = {
  from: "0xbc28ea04101f03ea7a94c1379bc3ab32e65e62d3",
  to: "0x0000000000000000000000000000000000000000",
  nonce: 1,
  gas: 100000,
  value: 0,
  data: "0x0"
};

/**
 *  Send transaction
 */
walletConnector
  .sendTransaction(tx)
  .then(console.log)
  .catch(console.error);
```

### Sign Message \(eth_sign\)

```javascript
/**
 *  Draft Message Parameters
 */
const msgParams = [
  "0xbc28ea04101f03ea7a94c1379bc3ab32e65e62d3",
  "My email is john@doe.com - 1537836206101"
];

/**
 *  Sign message
 */
walletConnector
  .signMessage(msgParams)
  .then(console.log)
  .catch(console.error);
```

### Sign Personal Message \(personal_sign\)

```javascript
/**
 *  Draft Message Parameters
 */
const msgParams = [
  "0xbc28ea04101f03ea7a94c1379bc3ab32e65e62d3",
  "My email is john@doe.com - 1537836206101"
];

/**
 *  Sign personal message
 */
walletConnector
  .signPersonalMessage(msgParams)
  .then(console.log)
  .catch(console.error);
```

### Sign Typed Data \(eth_signTypedData\)

```javascript
/**
 *  Draft Typed Data
 */
const msgParams = [
  "0xbc28ea04101f03ea7a94c1379bc3ab32e65e62d3",
  {
    types: {
      EIP712Domain: [
        { name: "name", type: "string" },
        { name: "version", type: "string" },
        { name: "chainId", type: "uint256" },
        { name: "verifyingContract", type: "address" }
      ],
      Person: [
        { name: "name", type: "string" },
        { name: "account", type: "address" }
      ],
      Mail: [
        { name: "from", type: "Person" },
        { name: "to", type: "Person" },
        { name: "contents", type: "string" }
      ]
    },
    primaryType: "Mail",
    domain: {
      name: "Example Dapp",
      version: "1.0.0-beta",
      chainId: 1,
      verifyingContract: "0x0000000000000000000000000000000000000000"
    },
    message: {
      from: {
        name: "Alice",
        account: "0xaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"
      },
      to: {
        name: "Bob",
        account: "0xbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb"
      },
      contents: "Hey, Bob!"
    }
  }
];

/**
 *  Sign Typed Data
 */
walletConnector
  .signTypedData(msgParams)
  .then(console.log)
  .catch(console.error);
```

### Send Custom Request

```javascript
/**
 *  Draft Custom Request
 */
const customRequest = {
  id: 1,
  jsonrpc: "2.0",
  method: "eth_signTransaction",
  params: [
    {
      from: "0xbc28ea04101f03ea7a94c1379bc3ab32e65e62d3",
      to: "0x0000000000000000000000000000000000000000",
      nonce: 1,
      gas: 100000,
      value: 0,
      data: "0x0"
    }
  ]
};

/**
 *  Send Custom Request
 */
walletConnector
  .sendCustomRequest(customRequest)
  .then(console.log)
  .catch(console.error);
```
