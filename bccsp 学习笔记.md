<!-- TOC depthFrom:1 depthTo:4 withLinks:1 updateOnSave:1 orderedList:0 -->

- [BCCSP接口](#bccsp接口)
- [官方实现使用方法](#官方实现使用方法)
	- [创建与初始化](#创建与初始化)
	- [使用BCCSP接口](#使用bccsp接口)
- [源码简析](#源码简析)
	- [包结构](#包结构)
	- [SW实现简析](#sw实现简析)
		- [密码学函数接口](#密码学函数接口)
		- [初始化](#初始化)
		- [主要函数逻辑](#主要函数逻辑)
	- [工厂](#工厂)
		- [工厂接口BCCSPFactory](#工厂接口bccspfactory)
		- [工厂初始化函数`InitFactories()`](#工厂初始化函数initfactories)
		- [SW工厂实现](#sw工厂实现)
	- [文件KeyStore](#文件keystore)
		- [获取密钥](#获取密钥)
		- [保存密钥](#保存密钥)
- [兼容默认实现的BCCSP实现的一点思路](#兼容默认实现的bccsp实现的一点思路)
	- [工厂](#工厂)
	- [BCCSP接口实现](#bccsp接口实现)
	- [KeyStore实现](#keystore实现)

<!-- /TOC -->

# BCCSP接口

fabric的BCCSP全称为blockchain cryptographic service provider，区块链密码学服务提供方，是fabric用于提供密钥生成、加解密以及签名验证的服务接口，接口的定义如下。

###### github.com/hyperledger/fabric/bccsp/bccsp.go
```go

package bccsp

import (
	"crypto"
	"hash"
)

// Key represents a cryptographic key
// 密钥的一般定义
type Key interface {

	// Bytes converts this key to its byte representation,
	// if this operation is allowed.
  // 返回这个key的二进制表示
	Bytes() ([]byte, error)

	// SKI returns the subject key identifier of this key.
  // 标准定义的SKI是：The subject key identifier extension provides a means of
  // identifying certificates that contain a particular public key.
  // 就是用于寻找所有证书里面是否包含了特定的公钥。看BCCSP.GetKey方法，可以理解
  // 为用户id，根据该用户id，从BCCSP中获取公钥
	SKI() []byte

	// Symmetric returns true if this key is a symmetric key,
	// false is this key is asymmetric
  // 若是对称密钥则返回true
	Symmetric() bool

	// Private returns true if this key is a private key,
	// false otherwise.
  // 若是私钥，则返回true
	Private() bool

	// PublicKey returns the corresponding public key part of an asymmetric public/private key pair.
	// This method returns an error in symmetric key schemes.
  // 若本key是私钥，则返回对应的公钥。若是公钥则直接返回。
	PublicKey() (Key, error)
}

// KeyGenOpts contains options for key-generation with a CSP.
// 包含了提供个CSP用于生成密钥的选项，用在BCCSP的Keygen函数
// 下面的都类似，不再说明
type KeyGenOpts interface {

	// Algorithm returns the key generation algorithm identifier (to be used).
  // 指定使用什么算法
	Algorithm() string

	// Ephemeral returns true if the key to generate has to be ephemeral,
	// false otherwise.
  // 指定生成的密钥是否是暂时使用的
	Ephemeral() bool
}

// KeyDerivOpts contains options for key-derivation with a CSP.
type KeyDerivOpts interface {

	// Algorithm returns the key derivation algorithm identifier (to be used).
	Algorithm() string

	// Ephemeral returns true if the key to derived has to be ephemeral,
	// false otherwise.
	Ephemeral() bool
}

// KeyImportOpts contains options for importing the raw material of a key with a CSP.
type KeyImportOpts interface {

	// Algorithm returns the key importation algorithm identifier (to be used).
	Algorithm() string

	// Ephemeral returns true if the key generated has to be ephemeral,
	// false otherwise.
	Ephemeral() bool
}

// HashOpts contains options for hashing with a CSP.
type HashOpts interface {

	// Algorithm returns the hash algorithm identifier (to be used).
	Algorithm() string
}

// SignerOpts contains options for signing with a CSP.
type SignerOpts interface {
	crypto.SignerOpts
}

// EncrypterOpts contains options for encrypting with a CSP.
// 这是空接口，意味着我们可以使用自定义的加密参数类来实现这个，传入任意的参数。
type EncrypterOpts interface{}

// DecrypterOpts contains options for decrypting with a CSP.
type DecrypterOpts interface{}

// BCCSP is the blockchain cryptographic service provider that offers
// the implementation of cryptographic standards and algorithms.
// BCCSP全称区块链密码学服务提供方，提供了一系列密码学服务的接口
type BCCSP interface {

  // -----------------------------密钥管理-------------------------

	// KeyGen generates a key using opts.
  // 根据参数生成Key
	KeyGen(opts KeyGenOpts) (k Key, err error)

	// KeyDeriv derives a key from k using opts.
	// The opts argument should be appropriate for the primitive used.
  // 使用密钥k以及选项opts派生一个新的密钥
  // opts参数需要适合用于底层（Primitive）的加密
	KeyDeriv(k Key, opts KeyDerivOpts) (dk Key, err error)

	// KeyImport imports a key from its raw representation using opts.
	// The opts argument should be appropriate for the primitive used.
  // 将一个密钥的原始表示，使用选项opts来转换成接口所使用的Key类型
	KeyImport(raw interface{}, opts KeyImportOpts) (k Key, err error)

	// GetKey returns the key this CSP associates to
	// the Subject Key Identifier ski.
  // 从本CSP中，根据SKI获取该SKI的公钥
	GetKey(ski []byte) (k Key, err error)

  // -----------------------------哈希算法-----------------------------

	// Hash hashes messages msg using options opts.
	// If opts is nil, the default hash function will be used.
  // 求哈希值，若opts忽略，则使用默认哈希函数
	Hash(msg []byte, opts HashOpts) (hash []byte, err error)

	// GetHash returns and instance of hash.Hash using options opts.
	// If opts is nil, the default hash function will be returned.
  // 获取一个哈希接口的实现。若opts为空，则返回默认实现
	GetHash(opts HashOpts) (h hash.Hash, err error)

  // ------------------------------签名验证----------------------------

	// Sign signs digest using key k.
	// The opts argument should be appropriate for the algorithm used.
	//
	// Note that when a signature of a hash of a larger message is needed,
	// the caller is responsible for hashing the larger message and passing
	// the hash (as digest).
  // 使用密钥K签名。注意，当需要使用消息的哈希时，需要调用方先把消息使用哈希函数
  // 求得哈希之后，再调用本函数求签名。
	Sign(k Key, digest []byte, opts SignerOpts) (signature []byte, err error)

	// Verify verifies signature against key k and digest
	// The opts argument should be appropriate for the algorithm used.
  // 使用密钥K验证签名，也需要先哈希
	Verify(k Key, signature, digest []byte, opts SignerOpts) (valid bool, err error)

  // ----------------------------------加密解密-------------------------

	// Encrypt encrypts plaintext using key k.
	// The opts argument should be appropriate for the algorithm used.
  // 使用密钥k进行加密明文
	Encrypt(k Key, plaintext []byte, opts EncrypterOpts) (ciphertext []byte, err error)

	// Decrypt decrypts ciphertext using key k.
	// The opts argument should be appropriate for the algorithm used.
  // 使用密钥k解密密文
	Decrypt(k Key, ciphertext []byte, opts DecrypterOpts) (plaintext []byte, err error)
}

```

除了上述接口以外，为了保存密钥，还定义了一个密钥存储（keystore）的接口，如下

###### github.com/hyperledger/fabric/bccsp/keystore.go
```go

// KeyStore represents a storage system for cryptographic keys.
// It allows to store and retrieve bccsp.Key objects.
// The KeyStore can be read only, in that case StoreKey will return
// an error.
// Keystore代表一个存储密码学密钥的存储系统。允许存储与取得bccsp.Key类型的对象，
// Keystore可以为只读，这样，StoreKey将会返回一个error对象。
type KeyStore interface {

	// ReadOnly returns true if this KeyStore is read only, false otherwise.
	// If ReadOnly is true then StoreKey will fail.
  // 只读返回true
	ReadOnly() bool

	// GetKey returns a key object whose SKI is the one passed.
  // 使用SKI获取密钥
	GetKey(ski []byte) (k Key, err error)

	// StoreKey stores the key k in this KeyStore.
	// If this KeyStore is read only then the method will fail.
  // 保存密钥。若存储是只读的，则返回error
	StoreKey(k Key) (err error)
}

```



# 官方实现使用方法

## 创建与初始化

官方实现使用工厂进行创建工作，工厂类在`bccsp/factory`包下。factory包提供了一些创建函数。

```go
// ====下面两个函数，是具体实现的函数，存放在pkcs11.go和nopkcs11.go文件中====

// 在使用factory的所有东西之前，首先需要调用这个函数！使用FactoryOpts初始化所有
// 工厂，FactoryOpts可以为空，有默认的设置。
func InitFactories(config *FactoryOpts) error

// 使用FactoryOpts获取BCCSP，根据Opts的ProviderName来获取对应的BCCSP
func GetBCCSPFromOpts(config *FactoryOpts) (bccsp.BCCSP, error)

// ====下面两个函数是工厂通用函数，存放在factory.go文件中====

// 获取一个默认的BCCSP
func GetDefault() bccsp.BCCSP

// 根据名称获取BCCSP，在初始化之后，就可以使用SW、PKCS11、PLUGIN来获取了，就是三个
// Opts对应的三个BCCSP
func GetBCCSP(name string) (bccsp.BCCSP, error)
```

其中，`FactoryOpts`是定义工厂的参数，默认实现放在`pkcs11.go`和`nopkcs11.go`下面，如

###### github.com/hyperledger/fabric/bccsp/factory/pkcs11.go
```go
// FactoryOpts holds configuration information used to initialize factory implementations
// 保存了初始化factory实例的配置信息
type FactoryOpts struct {
	ProviderName string            
	SwOpts       *SwOpts           
	PluginOpts   *PluginOpts        
	Pkcs11Opts   *pkcs11.PKCS11Opts
}

```

该实现有三种工厂，对应三个Opts，`SWOpts`、`PluginOpts`、`PKCS11Opts`。`ProviderName`指定`GetDefault()`方法返回的默认工厂的类型。具体这些Opts就不再细说。

使用的方法就是

1. 创建`FactoryOpts`对象，设置参数
2. 使用`InitFactories()`函数初始化工厂，或者直接使用`GetBCCSPFromOpts()`获取BCCSP，就直接结束创建
3. 初始化完毕后，就可以使用名称，通过`GetBCCSP()`函数获取，亦或者使用`GetDefault()`函数获取默认的工厂。

## 使用BCCSP接口

BCCSP的函数已经介绍过了。官方SW实现提供了RSA、AES、ECDSA三种算法，那么有许多的Opts提供了，分别在`bccsp/aesopts.go`、`bccsp/ecdsaopts.go`、`bccsp/rsaopts.go`文件里面。根据所使用的算法，创建对应的Opts，再传入BCCSP的各个函数即可。

那些方法会根据传入的key类型，判断所使用的算法。需要确保opts和key两个是属于同一个密钥算法的。


# 源码简析

## 包结构

```
github.com/hyperledger/fabric/bccsp/
├── aesopts.go                      AES加密算法选项
├── bccsp.go                        BCCSP接口定义
├── ecdsaopts.go                    ECDSA加密算法选项
├── factory                         工厂包
│   ├── factory.go                 BCCSP工厂接口和通用函数定义
│   ├── nopkcs11.go                不包含PCKS11 BCCSP的工厂的选项，以及初始化工厂函数
│   ├── opts.go                    主要放置了默认选项
│   ├── pkcs11factory.go           PCKS11 BCCSP工厂定义
│   ├── pkcs11.go                  包含PCKS11 BCCSP的工厂的选项，以及初始化工厂的函数
│   ├── pluginfactory.go           PLUGIN BCCSP工厂定义
│   └── swfactory.go               Software-Based BCCSP工厂定义
├── hashopts.go                     哈希选项
├── keystore.go                     密钥存储库接口
├── mocks             
│   └── mocks.go                   测试用的
├── opts.go                         放置许多Opts结构定义的位置
├── pkcs11                          这是PKCS11 BCCSP接口的实现，与SW类似，请看下面
│   ├── conf.go
│   ├── ecdsa.go
│   ├── ecdsakey.go
│   ├── impl.go
│   └── pkcs11.go
├── rsaopts.go
├── signer
│   └── signer.go                  签名工具的定义。签名使用的接口是`crypto`库
├── sw                              Software-Based BCCSP库
│   ├── aes.go                     AES加解密算法实现
│   ├── aeskey.go                  AES密钥实现
│   ├── conf.go                    impl的config对象结构定义
│   ├── dummyks.go                 一个辣鸡的密钥存储库实现，当不持久化数据时使用
│   ├── ecdsa.go                   ECDSA加解密算法
│   ├── ecdsakey.go                ECDSA密钥实现
│   ├── fileks.go                  基于文件的密钥存储库实现
│   ├── hash.go                    哈希算法通用实现
│   ├── impl.go                    SW实现所在地，结构名为impl，包含了对BCCSP接口的实现方法
│   ├── internals.go               impl实现使用的一些接口定义
│   ├── keyderiv.go                密钥派生函数实现
│   ├── keygen.go                  密钥生成函数实现
│   ├── keyimport.go               密钥导入函数实现
│   ├── mocks                      
│   │   └── mocks.go              又一个辣鸡实现
│   ├── rsa.go                     rsa加解密算法实现
│   └── rsakey.go                  rsa密钥实现
└── utils                           就一些通用函数，不多说
    ├── ecdsa.go
    ├── errs.go
    ├── io.go
    ├── keys.go
    ├── slice.go
    └── x509.go
```

## SW实现简析

SW的基本结构是这个`impl`

```go
// SoftwareBasedBCCSP is the software-based implementation of the BCCSP.
type impl struct {
	conf *config
	ks   bccsp.KeyStore

	keyGenerators map[reflect.Type]KeyGenerator
	keyDerivers   map[reflect.Type]KeyDeriver
	keyImporters  map[reflect.Type]KeyImporter
	encryptors    map[reflect.Type]Encryptor
	decryptors    map[reflect.Type]Decryptor
	signers       map[reflect.Type]Signer
	verifiers     map[reflect.Type]Verifier
	hashers       map[reflect.Type]Hasher
}
```

可以看到，包含了一个配置结构，以及一个存储库，另外还有许多`map`，这些`map`的键是`Key`的类型，而值则是对应的函数实现类，如`encryptors`里面，保存了ECDSA、RSA、AES的加密接口实现。

### 密码学函数接口

可以看到`impl`文件里面有许多的`er`接口，定义全部在`internals.go`文件里面，如下

###### github.com/hyperledger/fabric/bccsp/sw/internals.go
```go
// 为简化，已省略注释，注释内容与BCCSP的类似
type KeyGenerator interface {
	KeyGen(opts bccsp.KeyGenOpts) (k bccsp.Key, err error)
}

type KeyDeriver interface {
	KeyDeriv(k bccsp.Key, opts bccsp.KeyDerivOpts) (dk bccsp.Key, err error)
}

type KeyImporter interface {
	KeyImport(raw interface{}, opts bccsp.KeyImportOpts) (k bccsp.Key, err error)
}

type Encryptor interface {
	Encrypt(k bccsp.Key, plaintext []byte, opts bccsp.EncrypterOpts) (ciphertext []byte, err error)
}

type Decryptor interface {
	Decrypt(k bccsp.Key, ciphertext []byte, opts bccsp.DecrypterOpts) (plaintext []byte, err error)
}

type Signer interface {
	Sign(k bccsp.Key, digest []byte, opts bccsp.SignerOpts) (signature []byte, err error)
}

type Verifier interface {
	Verify(k bccsp.Key, signature, digest []byte, opts bccsp.SignerOpts) (valid bool, err error)
}

type Hasher interface {
	Hash(msg []byte, opts bccsp.HashOpts) (hash []byte, err error)
	GetHash(opts bccsp.HashOpts) (h hash.Hash, err error)
}
```

这些接口的实现分散在了`ecdsa.go`、`rsa.go`、`aes.go`、`hash.go`等的文件里面，具体实现不再细说。

### 初始化

使用`impl.go`文件的`New()`等函数初始化本实现，函数签名如下

```go
func New(securityLevel int, hashFamily string, keyStore bccsp.KeyStore) (bccsp.BCCSP, error)
```

我们不需要直接调用该函数，该函数由工厂方法调用。我们关注的是里面创建了许多的`encryptor`等，初始化了`impl`的各个`map`。

只是，新版的`impl`，为了实现能够定制，大改了`New()`函数的逻辑，由原本在New函数里面创建各个函数接口的实现，到在`new.go`文件内创建，改到了`NewWithParameters()`函数中。Fabric-CA用的是老的实现。

### 主要函数逻辑

查看`impl`的`BCCSP`接口的函数实现可以发现，逻辑都是差不多的，首先判断传入的Key的类型，然后获取相应的函数接口实现，然后再调用该函数，同时会传入`Opts`对象，比如下面的签名函数

###### github.com/hyperledger/fabric/bccsp/sw/impl.go
```go
func (csp *CSP) Sign(k bccsp.Key, digest []byte, opts bccsp.SignerOpts) (signature []byte, err error) {
  // ...
	keyType := reflect.TypeOf(k)
	signer, found := csp.signers[keyType]
  // ...
	signature, err = signer.Sign(k, digest, opts)
  // ...
	return
}
```

## 工厂

`factory.go`是工厂的通用接口的定义，包括`GetDefaults()`等函数，也是通用的。具体的工厂，是由`pkcs11.go`以及`nopkcs11.go`提供的。这两个文件定义了初始化所有工厂的函数，以及直接创建BCCSP实例的函数，而初始化工厂函数`InitFactories()`会给`factory.go`文件内的`defaultBCCSP`、`bccspMap`等赋值，从而我们能够使用`factory.go`的函数。

下面会以`pkcs11.go`的工厂实现为例讲解。

### 工厂接口BCCSPFactory

工厂有一个通用的接口，`BCCSPFactory`，用于创建某个类型的BCCSP的。如下

###### github.com/hyperledger/fabric/bccsp/factory/factory.go
```go
// BCCSPFactory is used to get instances of the BCCSP interface.
// A Factory has name used to address it.
// 用于构造BCCSP实例，每个具体的工厂都有自己的名字。
type BCCSPFactory interface {

	// Name returns the name of this factory
	Name() string

	// Get returns an instance of BCCSP using opts.
  // 根据工厂选项FactoryOpts来创建BCCSP，注意，其实他用的是SWOpts等三个具体的Opts
	Get(opts *FactoryOpts) (bccsp.BCCSP, error)
}
```

默认三个工厂的实现则放置在了`swfactory.go`、`pluginfactory.go`以及`pkcs11factory.go`文件中。

这些具体的实现，使用的是`FactoryOpts`中属于自己的设置的那个结构，如`SWFactory`使用的是`SWOpts`，而不是整个`FactoryOpts`。

### 工厂初始化函数`InitFactories()`
- 注意，工厂的初始化在pkcs11.go中的InitFactories()

该函数调用`setFactories`函数，首先检查`FactoryOpts`，然后根据`FactoryOpts`创建工厂

```go
// Software-Based BCCSP
if config.SwOpts != nil {
  f := &SWFactory{}
  err := initBCCSP(f, config)
  if err != nil {
    factoriesInitError = errors.Wrap(err, "Failed initializing SW.BCCSP")
  }
}
```

其中会使用`factory.go`的`initBCCSP()`函数，该函数使用`BCCSPFactory`接口的`Get()`方法创建`BCCSP`，并存入`bccspMap`中，键为工厂的名称，值为`BCCSP`实现。

###### github.com/hyperledger/fabric/bccsp/factory/factory.go
```go
func initBCCSP(f BCCSPFactory, config *FactoryOpts) error {
	csp, err := f.Get(config)
	if err != nil {
		return errors.Errorf("Could not initialize BCCSP %s [%s]", f.Name(), err)
	}

	logger.Debugf("Initialize BCCSP [%s]", f.Name())
	bccspMap[f.Name()] = csp
	return nil
}

```

然后，设置`factory.go`中定义的`defaultBCCSP`，以实现`GetDefault()`函数的功能。

###### github.com/hyperledger/fabric/bccsp/factory/pkcs11.go
```go
var ok bool
defaultBCCSP, ok = bccspMap[config.ProviderName]
if !ok {
  factoriesInitError = errors.Errorf("%s\nCould not find default `%s` BCCSP", factoriesInitError, config.ProviderName)
}
```

初始化完成后，就能够通过`GetDefault()`获取默认实现，通过`GetBCCSP()`根据工厂名获取实现，等等了。

### SW工厂实现

`swfactory.go`文件中存放了SW的工厂实现，定义了`SWOpts`结构。`New()`函数则构造`Keystore`、解析`SWOpts`等，在此不再细讲。

## 文件KeyStore

这里不细讲，讲下大概思路，下面一段翻译自该实现的注释。

`fileBasedKeyStore`是一个基于文件夹的密钥存储库。每一个密钥存在一个文件当中，文件的命名使用密钥的SKI以及对应密钥类型的字符串。所有的密钥保存在初始化时指定的文件夹中。存储库支持使用密码创建，密码将会用于密钥的加密和解密。密钥存储库可设置为只读的，以防止错误的覆盖了密钥。

### 获取密钥

`GetKey()`函数使用SKI来获取密钥，在`searchKeystoreForSKI()`函数中，通过遍历文件名的方式，找到该SKI的文件，然后就能根据Key的类型获取了。

### 保存密钥

根据密钥的SKI以及类型，使用`getPathForAlias(alias string)`函数来获取文件名，alias就是ski转成的16进制字符串，然后使用`storePrivateKey()`、`storePublicKey()`以及`storeKey()`函数存储。

# 兼容默认实现的BCCSP实现的一点思路

## 工厂

首先需要写一个我们自己实现`BCCSPFactory`的实现，定义`Opts`结构，以及实现两个方法，其中`Get()`方法调用`BCCSP`实现的构造函数。

然后我们需要创建一个自己的`FactoryOpts`，可以使用内嵌结构，像下面，包含本来的Opts

```go
type FactoryOpts struct {
    *factory.FactoryOpts
    ABEOpts *ABEOpts `mapstructure:"ABE,omitempty" json:"ABE,omitempty" yaml:"ABE"`
}
```

改了`FactoryOpts`，也需要重新定义`BCCSPFactory`，只是因为`Get()`函数要用到`FactoryOpts`。

然后按照工厂库四大导出函数，写我们的函数。初始化时，初始化本来的工厂，然后初始化我们的。

```go
// 在使用BCCSPFactory接口之前，必须调用此函数，初始化
func InitFactories(config *FactoryOpts) error {
    factoriesInitOnce.Do(func() {
        // 调用本来的，以初始化本来的factory
        factoriesInitError = factory.InitFactories(config.FactoryOpts)
        if factoriesInitError != nil {
            return
        }
        factoriesInitError = setABEFactory(config)
    })

    return factoriesInitError
}
```

`GetDefault()`则返回本来的Default就行。

其他函数也挺好改的，照葫芦画瓢就可。根据传入的参数，判断是用我们的实现，还是用本来的实现。

## BCCSP接口实现

为了兼容本来的实现，判断`Key`类型是我们的Key的时候，则调用我们的实现函数，否则调用本来的。

## KeyStore实现

我们需要自己实现`Keystore`，因为默认实现是根据Key类型来存储Key的，不是一个较为通用的实现，无法用于我们自己的Key。也是照着本来的`Keystore`实现一个类似的即可。
