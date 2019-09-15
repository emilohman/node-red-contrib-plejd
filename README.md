node-red-contrib-plejd
=======================

A set of Node-RED nodes for interacting with Plejd.

## Install

Run the following command in the user directory of your Node-RED install. This is
usually `~/.node-red`.

```
npm install node-red-contrib-plejd
```

## Gathering crypto and device information

Obtaining the crypto key and the device ids is a crucial step to get this
running, for this it is required to get the .site json file from the plejd app
on android or iOS.

### Steps for android:

1. Turn on USB debugging and connect the phone to a computer.
2. Extract a backup from the phone:
```
$ adb backup com.plejd.plejdapp
```
3. Unpack the backup:
```
$ dd if=backup.ab bs=1 skip=24 | python -c "import zlib,sys;sys.stdout.write(zlib.decompress(sys.stdin.read()))" | tar -xv
```
4. Recover the .site file:
```
$ cp apps/com.plejd.plejdapp/f/*/*.site site.json
```

### Steps for iOS:

1. Open a backup in iBackup viewer.
2. Select raw files, look for AppDomainGroup-group.com.plejd.consumer.light.
3. In AppDomainGroup-group.com.plejd.consumer.light/Documents there should be two folders.
4. The folder that isn't named ".config" contains the .site file.

### Gather cryto key and ids for devices

When the site.json file has been recovered the cryptokey and the output
addresses can be extracted:

1. Extract the cryptoKey:
```
$ cat site.json | jq '.PlejdMesh.CryptoKey' | sed 's/-//g'
```
2. Extract the outputAddresses:
```
$ cat site.json  | jq '.PlejdMesh.outputAdresses' | grep -v '\$type' | jq '.[][]'
```

Or just open site.json in your favorite editor and extract the crypto key and output addresses (IDs).

## Usage

### connection

All nodes needs to be configured with a Plejd connection. The only setting for the connection is the crypto key.

### Input Node

Return state changes of Plejd devices in **msg.payload**. Example input:
```
{id: 11, state: 'on', dim: 255}
```

### Output node

Change state of a Plejd device in the mesh by using **msg.payload**:

```
{id: 12, state: 'off', dim: 255}

{id: 13, state: 'on', dim: 100}
```
