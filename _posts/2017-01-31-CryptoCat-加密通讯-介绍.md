---
tags: Trans
---

> 译者注：这篇翻译缘起 Telegram 的[相关群组]()讨论到 CryptoCat这个加密通讯工具。虽然它好像不怎么名声远扬（[github仓库](https://github.com/cryptocat/cryptocat)上 star 不多），但笔者发现它的开发者「诚恳诚实」地在做这款软件，也有一些创新之处（请看稍后的[测评]()）。他们的这篇软件加密介绍便值得一学。

Source: https://crypto.cat/security.html

## Encryption Overview
## 加密技术总览

Cryptocat uses a Double Ratchet-based encryption protocol that combines a forward-secure ratchet with a zero round-trip authenticated key exchange. As a transport layer for encrypted messages, Cryptocat adopts the OMEMO Multi-End Message and Object Encryption standard, which also gives Cryptocat multi-device support and allows for offline messaging. In terms of the transport layer, Cryptocat uses XMPP over a long-standing, TLS-encrypted WebSockets connection.
Cryptocat 使用了「双棘轮」为本的加密协议，该协议结合了一个向前安全的“棘轮，及一个零巡回验证的密钥交换过程。在承载加密信息的传输层上，Cryptocat 采用了 [OMEMO 多端信息和对象加密标准](https://conversations.im/xeps/multi-end.html)，能提供多设备支持并允许离线消息。至于这传输层，Cryptocat使用通过一个长期性的TLS加密的 WebSockets连接的 XMPP（协议）。
<!--more-->

Every Cryptocat device owns a long-term identityKey pair which helps with the establishment of the initial authenticated key exchange. This key pair also serves to sign the device's signedPreKey, an ephemeral public key that also is mixed into the authenticated key exchange. The signedPreKey is also shipped with 100, unsigned, one-time-use preKeys, and is regenerated and re-signed every week.

Suppose Alice wanted to start a new session with Bob. Alice would then fetch Bob's current signedPreKey and his list of 100 preKeys from the Cryptocat network. Alice would then select a random preKey from the list. Alice then generates her own initKey pair, which is a keypair used only for the purposes of initializing a new session. Alice then performs the following computation in order to obtain the initial session secret S:

```
S = SHA256(
	X25519(AliceIdentityKey, BobSignedPreKey) ||
	X25519(AliceInitKey, BobIdentityKey)      ||
	X25519(AliceInitKey, BobSignedPreKey)     ||
	X25519(AliceInitKey, BobPreKey)
)
(
	AliceReceivingRootKey,
	AliceReceivingChainKey
) = HKDF(S, Constant1, Constant2)
```

Between messages, Cryptocat maintains a forward-secure ratcheting chain that creates a new ephemeral key pair for each message, and derives their chain of authenticity by mixing in a chain going back to S via a Hash-Based Key Derivation Function (HKDF). Here is an example occuring later in the conversation, after Bob had also derived a BobMessageEphemeralKey and other session state elements: 

```
AliceMessageEphemeralKey = X25519_NewKeyPair()
AliceSharedKey = X25519(
	AliceMessageEphemeralKey,
	BobMessageEphemeralKey
)
(
	AliceSendingRootKey,
	AliceSendingChainKey
) = HKDF(S, AliceReceivingRootKey, Constant2)
AliceMessageEncryptionKey = HKDF(
	HMAC(AliceSendingChainKey, Constant3),
	Constant1,
	Constant4
)
(
	AliceEncryptedMessage,
	AliceEncryptedMessageTag
) = AESGCM256(
	Key:AliceMessageEncryptionKey,
	Plaintext:AliceMessagePlaintext,
	AddedData:(
		AliceMessageEphemeralKeyPublic ||
		BobMessageEphemeralKeyPublic   ||
		AliceIdentityKeyPublic         ||
		BobIdentityKeyPublic
	)
)
```

Alice then sends (AliceMessageEphemeralKeyPublic, AliceEncryptedMessage, AliceEncryptedMessageTag). Constant1, Constant2 and Constant3 are some publicly known constant strings coded into the protocol implementation.

### Primitives
### 术语

- `AES` is used in Galois Counter Mode (GCM) for authenticated symmetric encryption with added data.
-  (一种对称加密算法）
- `X25519` is used for all Elliptic-Curve Diffie-Hellman (ECDH) operations. A four-way ECDH handshake is used for the initial authenticated key exchange and further ECDH operations are routinely performed for preKey generation and message ratcheting.
- (一种用于ECDH加密的算法。用于初期的已验证的密钥交换等）
- `ED25519` is used for generating the identityKey pair and for signing and verifying the signedPreKey, but is otherwise unused throughout the protocol.
- (用于生成 `identityKey` 密钥对，并用来签名和验证 `signedPreKey`）
- `SHA256` is used as the standard hashing function and as the underlying hash function for the HKDF.
- (用于标准哈希函数）


### Threat Model
### 威胁模式

Cryptocat makes the following assumptions:
我们有以下的（风险）预设：

- Untrusted network: We assume that an attacker controls the network and so can intercept, tamper with and inject network messages.
- 不信任的网络：我们预设攻击者控制着网络并可以截听、篡改和注入网络上的信息。

- Long-term key compromise: We assume that an attacker may at some point compromise the long-term identityKey pair of a participant.
- 长久的密钥失窃：我们假设攻击者或能在某一点 获得用户的那个长期 `identityKey` 密钥对。

### Security Goals
### 安全目标

Given our threat model, Cryptocat aims to accomplish the following security goals:
就以上的威胁模式，我们旨在达到以下安全目标：

- Secrecy: A message sent between buddies can only be decrypted between these buddies.
- 私密性：两个人之间的消息，能且只能被这两个人解密。

- Authenticity: If Alice receive an apparently valid message from Bob, then Bob must have sent this message to Alice.
- 身份真实性验证：如果 Alice 收到一条明显是 Bob发来的消息，那 Bob一定是发了这条消息给 Alice（即这条消息一定是从 Bob的设备发出的）。

- Indistinguishability: If Alice randomly chooses between two messages of the same size and sends only one to Bob, an attacker cannot determine which message was sent.
- 不可区分性：如果 Alice从两条一样大小的消息中随机选择一条发给 Bob，那么攻击者无法确定发出去的是哪一条。

- Forward secrecy: If Alice sends a message to Bob and Alice's key state at the time of this message is subsequently compromised, all previous messages retain their Secrecy property.
- 前向保密：如果 ALice 发了一条消息给 Bob，但接下来 Alice的密钥不安全了（比如被被盗被公开等），那他俩以前的消息将成为他俩永远的秘密（外人无法解密）。

- Future secrecy: If Alice sends a first message to Bob, receives a reply from Bob, and then sends a second message to Bob, Alice's second message remains secret even if her key state for the first message is compromised.
- 未来私密：如果 Alice给 Bob发了第一条消息，并得到 Bob一条回复，然后 Alice再发第二条消息给 Bob，那么如果 Alice发第一条消息时的密钥变不安全了的话，她的第二条（及以后）消息仍然可以保持私密。


## Authentication Overview
## （身份）验证技术总览

Cryptocat offers users the ability to verify the authenticity of their buddies' devices. In that way, they can ensure that a malicious party (including, potentially, the Cryptocat network itself) is not masquerading as the device of another individual. Device fingeprints are calculated thus:
我们可以让用户有能力验证他们的好友的设备的真实性（即该设备是不是该好友的）。这样，他们可以确保捣乱者（包括，可能的话，Cryptocat网络自己）没有拿别人的设备做伪装。设备指纹是这样计算的：

```
DeviceFingerprint = SHA256(
	deviceId                ||
	SHA256(
		username   ||
		deviceName ||
		deviceIcon
	)                       ||
	deviceIdentityKeyPublic
).HexString().First32Characters()
```

In the above example, deviceId is a random 32-byte device identifier that is generated upon device registration and that never changes. deviceName is a name that the user assigns to the device that also cannot be later modified. deviceIcon is one of three icons (0, representing a laptop, 1, representing an all-in-one desktop and 2, representing a PC) and also cannot be modified.

## File Sharing

Cryptocat software provides users with the ability to share documents, video recordings, photos and other such media. These are all threated （似乎是笔误，应该是treated）as the same type of plaintext (a "file") and are all handled as follows:
我们让用户可以传文档、影音记录、照片等。他们都被视作同类型的纯文本（作为一个“文件”），都按以下方式处理：

- A 256-bit AES-GCM fileKey is randomly chosen and used to symmetrically encrypt the file.
- 一个256位的 AES-GCM fileKey 被随机选择用来对称性加密文件。

- The encrypted file is then uploaded to a Cryptocat server where it is stored for 30 days. The Cryptocat server obtains the encrypted file and is able to determine its size, but is nevertheless not able to determine anything else about the file, including its type or file name.
- 被加密了的文件然后被上传到 Cryptocat 服务器，并保存30天。Cryptocat 服务器接收了文件并有能力判断文件的大小，但不能判定关于文件其他任何信息，包括文件类型和文件名。

- The sender then sends the fileKey and its corresponding AES-GCM fileNonce, fileTag to the recipient along with a fileUrl pointer to where the encrypted file can be fetched from the Cryptocat network and a fileName value indicating the name of the file. This information is communicated a specially formatted, but nevertheless regularly encrypted Cryptocat chat message.
- 发送者随后发送fileKey 和与之对应的 AES-GCM fileNonce, fileTag 给接收者，并附带上一个 fileUrl，指向服务器上的相应加密了的文件，还附带一个 fileName 值 指向文件名。这些信息是以一种特别格式的、但又是以常规加密的 Cryptocat消息的方式发送的。

- The recipient's client parses the chat message as one that is pointing to a file, downloads the ciphertext from fileUrl, decrypts it with fileKey and fileNonce and authenticates it with fileTag.
- 接收者的客户端解析这些“聊天”信息，指向一个文件，从 fileUrl 下载密文，用 fileKey and fileNonce 解密，用 fileTag 验证。

- If decryption and authentication are successful, the file is presented to the recipient for viewing or download, depending on what is appropriate for the file type.
- 如果解密和验证是成功的，文件得以呈现给接收者，可以查看或下载，取决于该文件的类型。

Note that fileUrl cannot be just any HTTP URI but is specially restricted for the purposes of Cryptocat file sharing.
需要留意的是 fileUrl 并不只是任何一种 HTTP URI，而是为文件分享的目的的特别严格的格式。


## Miscellaneous Security Features
## 其他安全特性

Aside of the message encryption protocol, Cryptocat adopts the following security features in order to provide a generally more robust experience across the client:

- Authenticated installers: Cryptocat client installation packages are authenticated using Authenticode on Windows, gpg2 on Linux and Apple code signing on Mac.
- 已验证的安装器：Cryptocat 客户端安装包可以被 Windows下的 Authenticode、Linux下的 gpg2、Mac下的 Apple code signing 验证。
- Signed updates: In addition to installer authentication, Cryptocat's built-in update downloader will run an independent signature verification on all downloads.
- 已签名的更新：除安装验证外，Cryptocat 的内置更新下载器将对所有下载进行独立签名验证。
- Certificate pinning: Cryptocat will check for a "known-good" TLS certificate before initiating a connection to the Cryptocat XMPP server.
- 证书固定：Cryptocat 会以「已知的就是好的」原则，在开启一个通往 Cryptocat XMPP 服务器的连接时，检测 TLS 证书。
- Device management: Users are given the option of independently verifying their buddies' device lists and are notified when a buddy's device list is modified.
- 设备管理：用户可以独立地验证他们的好友的设备清单，当好友的设备清单有变动的时候得到通知。


## 报告安全问题

（略）

<br />
<hr>
Copyleft

Initially translated by @mdrights

Released under CC-BY-SA 4.0


