# Bitcoin Transaction over Mesh Network

## Overview

In an ideal world, a mesh network could send messages containing thousands of bytes. However, that's not the case, meaning a signed Bitcoin transaction can't be sent in a single package over a mesh network.

That's why, in this project, the sender splits the raw transaction into *x* parts and sends each part to the mesh network.

The receiver collects each part of the raw transaction and assembles them (even if they are not received in the same packet). Then it displays the raw transaction.

A checksum is used to verify whether the raw transaction is complete.

Finally, the raw transaction is broadcast to the Bitcoin Network, either with a Bitcoin wallet (like **Sparrow**) or with an API call to **mempool.space**.

---

## How to setup your mesh node

The documentation and firmware are available here: https://gaulix.fr/gaulix-pour-les-noobs/

They are based on the open-source Meshtastic firmware: https://github.com/meshtastic/firmware?tab=readme-ov-file

---

## The "B on Lille" conference

### Overview

The "B on Lille" is a Bitcoin conference taking place on September 26th, 27th, and 28th, organized by the Bitcoin Lille nonprofit association. More details are available on our website: https://bitcoinlille.fr/b-on-lille/

### The Mission

#### September 19th–22nd
A Bitcoin Lille member will receive two mesh antennas.

#### September 23rd
Four Bitcoin Lille members will work together to deploy both antennas in Belgium and test the mesh network.

To do so:
- One bitcoiner will remain in Bailleul.

The other three will travel together and:
- One bitcoiner will deploy the first antenna at point A in Belgium. He will bring a computer, a LoRa device, and remain available by call throughout the mission.
- One bitcoiner will deploy the second antenna at point B in Belgium. He will also bring a computer, a LoRa device, and remain available by call.
- One bitcoiner will travel to Brussels with a computer and a LoRa device, staying available by call during the mission.

The second antenna (point B) should ideally cover at least half of Brussels.
The bitcoiner in Brussels will then broadcast a message - "Hi guys! I'm in Brussels and I'm reaching out to you, Bitcoin Lille!" - over the Gaulix Network using the Sender-code-with-Jupyter script. (The higher the broadcasting position, the better the coverage.)

Meanwhile, the bitcoiner in Bailleul will listen to the network using Receiver-code-with-Jupyter. If the message is successfully received, the mission is considered a success.

#### September 27th (1:30 PM – 3:00 PM GMT+1)
We need a Brussels-based bitcoiner to be available on Telegram with the "B on Lille" organizers. He will need a computer, a LoRa device, and must remain available by call during the session.

At 1:30 PM, before the conference, a "send & receive" test will be conducted to confirm that everything works properly.

During the conference (around 2:30 PM), when prompted by the organizer, the Brussels bitcoiner will broadcast the message: "Hi guys! I'm in Brussels and I'm reaching out to you, Bitcoin Lille!"
Simultaneously, the speaker in Lille will listen to the network using Receiver-code-with-Jupyter.

Once received, the organizer will ask the Brussels bitcoiner to broadcast a signed Bitcoin transaction from Brussels, which the speaker will also receive.

After confirming reception, the speaker will connect his computer to the internet and broadcast the transaction manually via the mempool.space API.

This will demonstrate that a Bitcoin transaction can be transmitted from one country to another (Belgium → France) via a mesh network — without using the internet.
This proves that, even if Belgium were cut off from the internet, Belgian bitcoiners could still send Bitcoin transactions through a mesh network to French bitcoiners, who could then broadcast them.

#### Resilience.

---

## Python Requirements

> Tested with Anaconda / Jupyter Notebook

```bash
!pip install ipywidgets
!pip install pyserial
!pip install meshtastic
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
