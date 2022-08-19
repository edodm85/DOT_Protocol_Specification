# DOT Protocol Specification


An DOT (Data Over TCP) packet consists of a packet header, payload and tail.

Compatible android applications:
* Camera WIFI: https://github.com/edodm85/CameraWIFI
* OV Grabber: soon

<br>

NB: Byte order for all packets are network order (BIG ENDIAN).


| Packet        | Size          | Value         | Description       |
| ------------- | ------------- | ------------- | ----------------- |
| Start         | 1Byte         | 0xAA          | Start Byte        |
| Version       | 1Byte         | 0x01          | Protocol Version  |
| Lenght        | 4Bytes        |               | Lenght (Type, Payload and Stop) |
| Type          | 1Byte         |               | Message type      |
| Payload       | N x 1Byte     |               | Payload           |
| Stop          | 1Byte         | 0x55          | End Byte          |

<br>

<img src=https://github.com/edodm85/DOT_Protocol_Specification/blob/master/Resources/flowchart_v2.jpg >


<br>

## 1. Start and Stop

The packet starts with a Start Byte (0xAA) and ends with a Stop Byte (0x55) for easy parsing.

<br>

## 2. Version

The second Byte is the protocol Version: 
* 0x01: first version

<br>

## 3. Lenght

The Length is the payload length plus two (1Byte Type + 1Byte Stop).

<br>

## 4. Type

The "Type" identifies the kind of the payload:

| Value         | Description      |
| ------------- | ---------------- |
| 0x01          | Command          |
| 0x02          | Command Response |
| 0x05          | Image            |
| 0x06          | Image ACK        |

<br>

## 5. Payload

### 5.1 Payload for "Type Command (0x01)"

Each table row describes a command (placed in the first byte of the payload):

| Function      	     | Size          | Payload              |
| ------------- 	     | ------------- | -------------        |
| Snap a Image  	     | 1Byte         | 0x10                 | 
| Start Grab    	     | 1Byte         | 0x11                 |
| Stop Grab     	     | 1Byte         | 0x12                 |
| Set Focus OFF        | 1Byte         | 0x20                 | 
| Set Focus ON         | 1Byte         | 0x21                 | 
| Get Focus status     | 1Byte         | 0x25                 | 
| Set Flash OFF        | 1Byte         | 0x30                 | 
| Set Flash ON         | 1Byte         | 0x31                 | 
| Get Flash status     | 1Byte         | 0x35                 |

<br>

### 5.2 Payload for "Type Command Response (0x02)"

The response payload is described in this way:

| Payload position        | Name             | Size          |
| -------------           | ---------------- | ------------- |
| 0                       | Command Echo     | 1Byte         | 
| 1                       | Response         | NBytes        |

ACK = 0xEE

<br>

### 5.3 Payload for "Type Image (0x05)"

The image payload is build in this way:

| Payload position      | Name             | Size          |
| -------------         | ---------------- | ------------- |
| 0                     | Width            | 2Bytes        | 
| 2                     | Height           | 2Bytes        | 
| 4                     | Image Format     | 1Byte         | 
| 5                     | Image Datas      | NBytes        | 

NB: An IMAGE ACK response is required when an image is received

<br>

### 5.4 Payload for "Type Image ACK (0x06)"

The response payload is described in this way:

| Payload position        | Name              | Size          |
| -------------           | ----------------  | ------------- |
| 0                       | 0xEE (ACK)        | 1Byte         | 


<br>

## 6. Examples

### 6.1 Set focus ON

Send focus ON command (PC --> ANDROID):

| Start [0]  | Version [1]   | Lenght [2-5]  | Type [6] | Payload [7]   | Stop [8]    |
| ---------- | ------------  | -----------   | -------- | ---------     | -------     |
| 0xAA       | 0x01          | 3 (dec)       | 0x01     | 0x21          | 0x55        |

Response ACK (ANDROID --> PC):

| Start [0]  | Version [1]   | Lenght [2-5]  | Type [6] | Payload [7-8]   | Stop [9]    |
| ---------- | ------------  | -----------   | -------- | ---------       | -------     |
| 0xAA       | 0x01          | 4 (dec)       | 0x02     | 0x21, 0xEE      | 0x55        |



<br>

### 6.2 Acquire one image

Send One image command (PC --> ANDROID):

| Start [0]  | Version [1]   | Lenght [2-5]  | Type [6] | Payload [7]   | Stop [8]    |
| ---------- | ------------  | -----------   | -------- | ---------     | -------     |
| 0xAA       | 0x01          | 3 (dec)       | 0x01     | 0x10          | 0x55        |

Response ACK (ANDROID --> PC):

| Start [0]  | Version [1]   | Lenght [2-5]  | Type [6] | Payload [7-8]   | Stop [9]    |
| ---------- | ------------  | -----------   | -------- | ---------       | -------     |
| 0xAA       | 0x01          | 4 (dec)       | 0x02     | 0x10, 0xEE      | 0x55        |

<br>

Image (ANDROID --> PC):

| Start [0]  | Version [1]   | Lenght [2-5]   | Type [6] | Payload [N Bytes]                | Stop [N Bytes + 1]     |
| ---------- | ------------  | -----------    | -------- | ---------                       | -------               |
| 0xAA       | 0x01          | N Bytes        | 0x05     | Witdt, Height, Format, Image    | 0x55                  |

Image ACK (PC --> ANDROID):

| Start [0]  | Version [1]   | Lenght [2-5]  | Type [6] | Payload [7]   | Stop [8]    |
| ---------- | ------------  | -----------   | -------- | ---------     | -------     |
| 0xAA       | 0x01          | 3 (dec)       | 0x06     | 0xEE          | 0x55        |

<br>

### 6.3 Acquire multiple image

Send Start Grab command (PC --> ANDROID):

| Start [0]  | Version [1]   | Lenght [2-5]  | Type [6] | Payload [7]   | Stop [8]    |
| ---------- | ------------  | -----------   | -------- | ---------     | -------     |
| 0xAA       | 0x01          | 3 (dec)       | 0x01     | 0x11          | 0x55        |

Response ACK (ANDROID --> PC):

| Start [0]  | Version [1]   | Lenght [2-5]  | Type [6] | Payload [7-8]   | Stop [9]    |
| ---------- | ------------  | -----------   | -------- | ---------       | -------     |
| 0xAA       | 0x01          | 4 (dec)       | 0x02     | 0x10, 0xEE      | 0x55        |

<br>

Image 1 (ANDROID --> PC):

| Start [0]  | Version [1]   | Lenght [2-5]   | Type [6] | Payload [N Bytes]                | Stop [N Bytes + 1]     |
| ---------- | ------------  | -----------    | -------- | ---------                       | -------               |
| 0xAA       | 0x01          | N Bytes        | 0x05     | Witdt, Height, Format, Image    | 0x55                  |

Image ACK (PC --> ANDROID):

| Start [0]  | Version [1]   | Lenght [2-5]  | Type [6] | Payload [7]   | Stop [8]    |
| ---------- | ------------  | -----------   | -------- | ---------     | -------     |
| 0xAA       | 0x01          | 3 (dec)       | 0x06     | 0xEE          | 0x55        |

<br>

....

<br>

Image N (ANDROID --> PC):

| Start [0]  | Version [1]   | Lenght [2-5]   | Type [6] | Payload [N Bytes]                | Stop [N Bytes + 1]     |
| ---------- | ------------  | -----------    | -------- | ---------                       | -------               |
| 0xAA       | 0x01          | N Bytes        | 0x05     | Witdt, Height, Format, Image    | 0x55                  |

Image ACK (PC --> ANDROID):

| Start [0]  | Version [1]   | Lenght [2-5]  | Type [6] | Payload [7]   | Stop [8]    |
| ---------- | ------------  | -----------   | -------- | ---------     | -------     |
| 0xAA       | 0x01          | 3 (dec)       | 0x06     | 0xEE          | 0x55        |

<br>

Send Stop Grab command (PC --> ANDROID):

| Start [0]  | Version [1]   | Lenght [2-5]  | Type [6] | Payload [7]   | Stop [8]    |
| ---------- | ------------  | -----------   | -------- | ---------     | -------     |
| 0xAA       | 0x01          | 3 (dec)       | 0x01     | 0x12          | 0x55        |

Response ACK (ANDROID --> PC):

| Start [0]  | Version [1]   | Lenght [2-5]  | Type [6] | Payload [7-8]   | Stop [9]    |
| ---------- | ------------  | -----------   | -------- | ---------       | -------     |
| 0xAA       | 0x01          | 4 (dec)       | 0x02     | 0x10, 0xEE      | 0x55        |



