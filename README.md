# DOT Protocol Specification


An DOT packet consists of a packet header, payload and tail.

Byte order for all packets are network order (BIG ENDIAN).


| Packet        | Size          | Value         | Description       |
| ------------- | ------------- | ------------- | ----------------- |
| Start         | 1Byte         | 0xAA          | Start Byte        |
| Version       | 1Byte         | 0x01          | Protocol Version  |
| Lenght        | 4Bytes        |               | Lenght (Type, Payload and End) |
| Type          | 1Byte         |               | Message type      |
| Payload       | N x 1Byte     |               | Payload           |
| Stop          | 1Byte         | 0x55          | End Byte          |

<br>

<img src=https://github.com/edodm85/DOT_Protocol_Specification/blob/master/Resources/flowchart_v1.jpg >

<br>


## 1. Start and Stop

The packet starts with a Start Byte (0xAA) and ends with a Stop Byte (0x55) for easy parsing.

<br>

## 2. Version

The second Byte is the protocol Version: 
* 0x01: first version

<br>

## 3. Lenght

The Length is the payload length plus two (type and stop Byte).

<br>

## 4. Type

The Type identifies if the data is a command or a response.

| Value         | Description      |
| ------------- | ---------------- |
| 0x01          | Command          |
| 0x02          | Command Response |
| 0x05          | Image Response   |

<br>

## 5. Payload

### 5.1 Payload Command 0x01

Each table row describes a command:

| Function      	     | Size          | Payload Byte 1       | Response Type | 
| ------------- 	     | ------------- | -------------        | ------------- |
| Snap a Image  	     | 1Byte         | 0x10                 | 0x5           |
| Start Grab    	     | 1Byte         | 0x11                 | 0x5           |
| Stop Grab     	     | 1Byte         | 0x12                 | 0x5           |
| Set Focus OFF        | 1Byte         | 0x20                 | 0x2           |
| Set Focus ON         | 1Byte         | 0x21                 | 0x2           |
| Get Focus status     | 1Byte         | 0x25                 | 0x2           |
| Set Flash OFF        | 1Byte         | 0x30                 | 0x2           |
| Set Flash ON         | 1Byte         | 0x31                 | 0x2           |
| Get Flash status     | 1Byte         | 0x35                 | 0x2           |

<br>

### 5.2.1 Payload Command Response 0x02

The response message is described in this way:

| Position      | Name             | Size          |
| ------------- | ---------------- | ------------- |
| Byte 1        | Command Echo     | 1Byte         | 
| Byte 2        | Response         | NBytes        |


AOK = 0xEE

<br>

### 5.2.2 Payload Image Response 0x05

The image response message is described in this way:

| Position      | Name             | Size          |
| ------------- | ---------------- | ------------- |
| Byte 1        | Width            | 2Bytes        | 
| Byte 3        | Height           | 2Bytes        | 
| Byte 5        | Image Format     | 1Byte         | 
| Byte 6        | Image Datas      | NBytes        | 




