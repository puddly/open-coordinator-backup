# Open Zigbee Network Backup Format
Primarily used for backing up coordinator information and storing Zigbee network information on-disk.

## Adoption
Versions of the Open Zigbee Network Backup Format have been adopted by several popular open source home automation projects, including [zigpy](https://github.com/zigpy/zigpy), and [zigbee-herdsman](https://github.com/Koenkk/zigbee-herdsman) (Zigbee library used by [Zigbee2MQTT](https://www.zigbee2mqtt.io/) and [IoBroker](https://www.iobroker.net/)).

## Backup Structure
The backup format is a JSON document with the following properties, aiming to be both machine- and human-readable:

### `version`
The document version, currently `2`.  
*Mandatory value: `2`*

### `backup_time`
An ISO 8601 formatted string representing the time the backup was created.  
*Example value: `"2024-10-17T18:29:58.409890+00:00"`*

### `network_info`
Top-level property holding information about the joined/formed Zigbee network.

#### `network_info.extended_pan_id`
The 64-bit Extended Personal Area Network Identifier (EPID).  
*Example value: `"00:21:2e:ff:ff:aa:bb:cc"`*

#### `network_info.pan_id`
The 16-bit Personal Area Network Identifier (PAN ID), big-endian encoded.  
*Example value: `"1234"`*

#### `network_info.nwk_update_id`
The network update identifier, a number between 0 and 255.  
*Example value: `1`*

#### `network_info.nwk_manager_id`
The 16-bit network address of the network manager, big-endian encoded. Usually just `"0000"`.  
*Example value: `"0000"`*

#### `network_info.channel`
The current logical channel of the network.  
*Example value: `11`*

#### `network_info.channel_mask`
The logical channels on which the network can exist. Coordinators do not utilize this information.  
*Example value: `[11, 15, 20]`*

#### `network_info.security_level`
The network security level. Should be `5` for practically all networks.  
*Example value: `5`*


#### `network_info.network_key`
An object containing the network key information.

##### `network_info.network_key.key`
The 128-bit network key.  
*Example value: `"01:23:45:67:89:ab:cd:ef:01:23:45:67:89:ab:cd:ef"`*

##### `network_info.network_key.tx_counter`
The network key frame counter, for transmit. Devices store this globally.  
*Example value: `10741222`*

##### `network_info.network_key.rx_counter`
The network key frame counter, for receive. Not all device types persist this value.  
*Example value: `10741222`*

#### `network_info.network_key.sequence`
The network key sequence number.  
*Example value: `0`*


#### `network_info.tc_link_key`
An object containing the Trust Center Link Key information.

##### `network_info.tc_link_key.key`
The 128-bit Trust Center Link Key. Almost always `ZigBeeAlliance09`.  
*Example value: `"5a:69:67:42:65:65:41:6c:6c:69:61:6e:63:65:30:39"`*

##### `network_info.tc_link_key.tx_counter`
The APS frame counter, for transmit. Devices store this globally.  
*Example value: `10741222`*

##### `network_info.tc_link_key.rx_counter`
The APS frame counter, for receive. Not all device types persist this value.  
*Example value: `10741222`*


#### `network_info.key_table[i]`
An array of objects containing known APS link keys for devices.

##### `network_info.key_table[i].key`
The 128-bit APS link key shared with a specific partner.  
*Example value: `"5a:69:67:42:65:65:41:6c:6c:69:61:6e:63:65:30:39"`*

##### `network_info.key_table[i].tx_counter`
The APS link key frame counter, for transmit.  
*Example value: `10741222`*

##### `network_info.key_table[i].rx_counter`
The APS link key frame counter, for receive.  
*Example value: `10741222`*

##### `network_info.key_table[i].partner_ieee`
The APS link key frame counter partner IEEE address.  
*Example value: `"00:11:22:33:44:55:66:77"`*


#### `network_info.__devices_comment`
This is a special value meant to warn users reading the JSON about missing devices being normal.  
*Example value: `It is completely normal for this section to be empty or contain only a few devices! The coordinator doesn't need to keep track of every device on a network.`*

#### `network_info.children[i]`
An array of objects containing the IEEE addresses of children of the current node.  
*Example value: `"00:11:22:33:44:55:66:77"`*

#### `network_info.nwk_addresses.<ieee>`
An object of known NWK addresses for devices on the network. Some coordinators have a NWK address cache that can be pre-populated during restore.  
*Example value: `"00:11:22:33:44:55:66:77": "1234"`*


### `stack_specific`
Object meant to store information specific to a particular Zigbee stack, information that cannot be exported into a common format.

#### `stack_specific.ezsp.hashed_tclk`
128-bit hashed Trust Center Link Key for the EmberZNet stack. Not all versions support it.  
*Example value: `"eb:1b:fc:f9:cb:33:d0:d6:09:c4:66:c7:a3:5d:f7:a7"`*

#### `stack_specific.ezsp.tclk_seek`
128-bit Trust Center Link Key seed for Texas Instruments Z-Stack. Not all versions support it.  
*Example value: `"eb:1b:fc:f9:cb:33:d0:d6:09:c4:66:c7:a3:5d:f7:a7"`*


### `metadata`
Object meant to store internal metadata for the generating application or library.
Consider it opaque.


### `source`
Information about the source of the backup.

#### `source.software`
Software generating the backup.  
*Example value: `"zigpy"`*

#### `source.version`
Version of the software generating the backup.  
*Example value: `"1.2.3.4"`*


### `node_info`
Information about the node itself.

#### `node_info.nwk`
The 16-bit network address of the node, big-endian encoded.  
*Example value: `"1234"`*

#### `node_info.ieee`
The 64-bit IEEE address of the node.  
*Example value: `"00:11:22:33:44:55:66:77"`*

#### `node_info.logical_type`
The logical type of the node. Must be one of `coordinator`, `router`, or `end_device`.  
*Example value: `"coordinator"`*

#### `node_info.model`
The model of the node.  
*Example value: `"Home Assistant Connect ZBT-1"`*

#### `node_info.manufacturer`
The manufacturer of the node.  
*Example value: `"Nabu Casa"`*

#### `node_info.version`
The software version of the node.  
*Example value: `"7.4.4.0 build 0"`*
