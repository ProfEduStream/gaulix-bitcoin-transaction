# Bitcoin Transaction over Mesh Network

## Overview

The sender splits the raw transaction into *x* parts and sends each part to the mesh network.

The receiver collects each part of the raw transaction and assembles them (even if they are not received in the same packet). Then it displays the raw transaction.

A checksum is used to verify whether the raw transaction is complete.

Finally, the raw transaction is broadcast to the Bitcoin Network manually — either with a Bitcoin wallet (like **Sparrow**) or with an API call to **mempool.space**.

---

## Python Requirements

> Tested with Anaconda / Jupyter Notebook

```bash
pip install ipywidgets
pip install pyserial
pip install meshtastic
```

---

## Sender Output Example

```
📨 Sent: BTC 382 fe75ebe 1/6 02000000000101fd2bcef118caa1f19079ce8bdf3cbc234cd05e384db07120e6f04324
📨 Sent: BTC 382 fe75ebe 2/6 8ce502ff0000000000fdffffff019185010000000000160014c0ecfb7c6ddf8882754b
📨 Sent: BTC 382 fe75ebe 3/6 023d59771ac05949157b0247304402207a2da7a66eec50f02a32e98ca60d6d4985e48d
📨 Sent: BTC 382 fe75ebe 4/6 884143b9744668072c331f8eb902205210183b15139213a4354d28aa7e9d83b514ce2f
📨 Sent: BTC 382 fe75ebe 5/6 f332cbea8df492911278e943012102b401438d5800f1aafd846195a830d73a32bb856c
📨 Sent: BTC 382 fe75ebe 6/6 efbac5b6d5ce16ddf5bc9a40d1a14600

✅ Every part has been sent
```

---

## Receiver Output Example

```
📡 Listening on COM (115200)
Message found: checksum fe75ebe - part 1/6
Message found: checksum fe75ebe - part 2/6
Message found: checksum fe75ebe - part 3/6
Message found: checksum fe75ebe - part 4/6
Message found: checksum fe75ebe - part 5/6
Message found: checksum fe75ebe - part 6/6

✅ Transaction Bitcoin:
02000000000101fd2bcef118caa1f19079ce8bdf3cbc234cd05e384db07120e6f043248ce502ff0000000000fdffffff019185010000000000160014c0ecfb7c6ddf8882754b023d59771ac05949157b0247304402207a2da7a66eec50f02a32e98ca60d6d4985e48d884143b9744668072c331f8eb902205210183b15139213a4354d28aa7e9d83b514ce2ff332cbea8df492911278e943012102b401438d5800f1aafd846195a830d73a32bb856cefbac5b6d5ce16ddf5bc9a40d1a14600
```

---

## Broadcasting the Transaction to the Bitcoin Network

You can use **mempool.space** or **Blockstream API** to broadcast your signed transaction.

### Example with Python

```python
import requests

# Your signed transaction (raw hex)
raw_hex = "02000000000101fd2bcef118caa1f19079ce8bdf3cbc234cd05e384db07120e6f043248ce502ff0000000000fdffffff019185010000000000160014c0ecfb7c6ddf8882754b023d59771ac05949157b0247304402207a2da7a66eec50f02a32e98ca60d6d4985e48d884143b9744668072c331f8eb902205210183b15139213a4354d28aa7e9d83b514ce2ff332cbea8df492911278e943012102b401438d5800f1aafd846195a830d73a32bb856cefbac5b6d5ce16ddf5bc9a40d1a14600"

# Endpoint for mempool.space Testnet
url = "https://mempool.space/testnet/api/tx"

# Broadcast
resp = requests.post(url, data=raw_hex, timeout=20)

if resp.status_code == 200:
    print("Transaction broadcasted")
    print("TXID:", resp.text.strip())
else:
    print("Error:", resp.status_code, resp.text)
```
