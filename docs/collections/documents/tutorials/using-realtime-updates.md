---
sidebar_position: 4
title: Realtime Updates
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

This tutorial is about using Macrometa GDN as a realtime database with local latencies across the globe.

## Pre-requisite

Let's assume your

* Tenant name is `nemo@nautilus.com` and
* User password is `xxxxxx`.

## SDK download

<Tabs groupId="operating-systems">
<TabItem value="py" label="Python">

```py
  pyC8 requires Python 3.5+. Python 3.6 or higher is recommended

  To install pyC8, simply run

      $ pip3 install pyC8

  or, if you prefer to use conda:

      conda install -c conda-forge pyC8

  or pipenv:

      pipenv install --pre pyC8

  Once the installation process is finished, you can begin developing applications in Python.
```
</TabItem>
<TabItem value="js" label="Javascript">

```js
  With Yarn or NPM

      yarn add jsc8
      (or)
      npm install jsc8

  If you want to use the SDK outside of the current directory, you can also install it globally using the `--global` flag:

      npm install --global jsc8

  From source,

      git clone https://github.com/macrometacorp/jsc8.git
      cd jsC8
      npm install
      npm run dist
```
</TabItem>
</Tabs>  

## Code Sample

<Tabs groupId="operating-systems">
<TabItem value="py" label="Python">

```py
import time
import threading
import pprint
from c8 import C8Client

# Variables - URLs
GLOBAL_URL = "play.paas.macrometa.io"

# Variables - DB
EMAIL = "nemo@nautilus.com"
PASSWORD = "xxxxx"
FABRIC = "_system"
COLLECTION_NAME = "ddos"

# Variables - Data
data = [
    {"ip": "10.1.1.1", "action": "block", "rule": "blacklistA"},
    {"ip": "20.1.1.2", "action": "block", "rule": "blacklistA"},
    {"ip": "30.1.1.3", "action": "block", "rule": "blacklistB"},
    {"ip": "40.1.1.4", "action": "block", "rule": "blacklistA"},
    {"ip": "50.1.1.5", "action": "block", "rule": "blacklistB"},
]

pp = pprint.PrettyPrinter(indent=4)

if __name__ == '__main__':

    # Step1: Open connection to GDN. You will be routed to closest region.
    print(f"\n1. CONNECT: federation: {GLOBAL_URL},  user: {EMAIL}")
    client = C8Client(protocol='https', host=GLOBAL_URL, port=443,
                      email=EMAIL, password=PASSWORD, geofabric=FABRIC)

    # Step2: Create a collection if not exists
    print(f"\n2. CREATE_COLLECTION: region: {GLOBAL_URL},  collection: {COLLECTION_NAME}")
    if client.has_collection(COLLECTION_NAME):
        collection = client.collection(COLLECTION_NAME)
    else:
        collection = client.create_collection(COLLECTION_NAME, stream=True)


    # Events are received by subscriber when changes are made to collection
    def create_callback():
        def callback_fn(event):
            pp.pprint(event)

        client.on_change(COLLECTION_NAME, callback=callback_fn, timeout=15)


    # Step3: Subscribe to receive documents in realtime (PUSH model)
    print(f"\n3. SUBSCRIBE_COLLECTION: region: {GLOBAL_URL},  collection: {COLLECTION_NAME}")
    rt_thread = threading.Thread(target=create_callback)
    rt_thread.start()
    time.sleep(2)
    print(f"Callback registered for collection: {COLLECTION_NAME}")

    # Step4: Subscribe to receive documents in realtime (PUSH model)
    print(f"\n4. INSERT_DOCUMENTS: region: {GLOBAL_URL},  collection: {COLLECTION_NAME}")
    client.insert_document(COLLECTION_NAME, document=data)
    
    # Step5: Wait to close the callback.
    print("\n5. Waiting to close callback")
    rt_thread.join(timeout=1)

    print(f"\n6. DELETE_DATA: region: {GLOBAL_URL}, collection: {COLLECTION_NAME}")
    collection.truncate()
    client.delete_collection(COLLECTION_NAME)
```

</TabItem>
<TabItem value="js" label="Javascript">

```js
const jsc8 = require("jsc8");

// Constants - DB
const globalUrl = "https://play.paas.macrometa.io/";
const email = "nemo@nautilus.com";
const password = "XXXXXX";
const client = new jsc8(globalUrl);

// Variables
const collectionName = "ddos";
let listener;
const data = [
  { ip: "10.1.1.1", action: "block", rule: "blocklistA" },
  { ip: "20.1.1.2", action: "block", rule: "blocklistA" },
  { ip: "30.1.1.3", action: "block", rule: "blocklistB" },
  { ip: "40.1.1.4", action: "block", rule: "blocklistA" },
  { ip: "50.1.1.5", action: "block", rule: "blocklistB" }
];

const sleep = (milliseconds) => {
  return new Promise(resolve => setTimeout(resolve, milliseconds));
};

// Create an authenticated instance with token or API key

// const client = new jsc8({url: globalUrl, token: "XXXX", fabricName: '_system'});
// const client = new jsc8({url: globalUrl, apiKey: "XXXX", fabricName: '_system'});

// Or use email and password to authenticate client instance

async function createCollection () {
  console.log("\n1. Log in.");
  await client.login(email, password);
  console.log("\n2. Create collection.");
  try {
    console.log(`Creating the collection ${collectionName}...`);
    const existsColl = await client.hasCollection(collectionName);
    if (existsColl === false) {
      await client.createCollection(collectionName, { stream: true });
    }
    // Adding an onChange listner for collection
    listener = await client.onCollectionChange(collectionName);
    // Decode the message printed here in readable format
    listener.on("message", (msg) => {
      const receivedMsg = msg && JSON.parse(msg);
      console.log("message=>", Buffer.from(receivedMsg.payload, "base64").toString("ascii"))
    });
    listener.on("open", () => console.log("Connection open"));
    listener.on("close", () => console.log("Connection closed"));
  } catch (e) {
    await console.log("Collection creation did not succeed due to " + e);
  }
}
async function insertData () {
  console.log(`\n3. Insert data in region ${globalUrl}`);
  await client.insertDocumentMany(collectionName, data);
}
async function deleteData () {
  console.log("\n4. Delete data");
  await client.deleteCollection(collectionName);
}
(async function () {
  await createCollection();
  await sleep(2000);
  await insertData();
  await sleep(10000);
  await listener.close();
  await deleteData();
})();
```

</TabItem>
</Tabs>
