# Python日常使用总结

## 创建虚拟环境

为了实现python的环境隔离，python允许对某个具体的项目生成一个虚拟环境，在虚拟环境中安装该项目所依赖的第三方模块可以有效的解决跨平台移植问题

```bash
python3 -m venv venv
```



## 导出当前项目所需要的第三方模块

使用`pip freeze > requirements.txt`命令可以将当前项目所依赖的第三方模块导出到一个名为`requirements.txt`（约定俗成）的文件中，一般此功能结合虚拟环境使用，方便项目移植到不同的平台

```bash
pip3 freeze > requirements.txt
```



## 离线安装第三方模块

使用`pip downloand`命令可以下载某个模块以及其依赖包，然后使用`pip install` 命令可以安装python离线包，以下载`pymysql`为例：

```bash
pip3 downlaod pymysql
# 会将pymysql.wheel文件下载到当前目录，也可指定具体目录，语法为 pip3 download <dirPath> moduleName

# 下载到/root/lib目录下
pip3 download /root/lib pymysql
```

也可指定下载多个文件

```bash
# 语法为 pip3 download <dirPath> -r requirements.txt
# 例如，requirements.txt文件内容如下：
certifi==2021.5.30
elasticsearch==7.13.2
PyMySQL==1.0.2
PyYAML==5.4.1
urllib3==1.26.5

# 下载这些包到/root/lib目录
pip3 download /root/lib -r requirements.txt
```



## Python进行AES加解密

```python
import base64
import hashlib
from Crypto.Cipher import AES as _AES


class AES:

    def __init__(self, key: str):
        """Init aes object used by encrypt or decrypt.
        AES/ECB/PKCS5Padding  same as aes in java default.
        """

        self.aes = _AES.new(self.get_sha1prng_key(key), _AES.MODE_ECB)

    @staticmethod
    def get_sha1prng_key(key: str) -> bytes:
        """encrypt key with SHA1PRNG.
        same as java AES crypto key generator SHA1PRNG.
        SecureRandom secureRandom = SecureRandom.getInstance("SHA1PRNG" );
        secureRandom.setSeed(decryptKey.getBytes());
        keygen.init(128, secureRandom);
        :param string key: original key.
        :return bytes: encrypt key with SHA1PRNG, 128 bits or 16 long bytes.
        """

        signature: bytes = hashlib.sha1(key.encode()).digest()
        signature: bytes = hashlib.sha1(signature).digest()
        return signature[:16]

    @staticmethod
    def padding(s: str) -> str:
        """Padding PKCS5"""

        pad_num: int = 16 - len(s) % 16
        return s + pad_num * chr(pad_num)

    @staticmethod
    def unpadding(s):
        """Unpadding PKCS5"""

        padding_num: int = ord(s[-1])
        return s[: -padding_num]

    def encrypt_to_bytes(self, content_str):
        """From string encrypt to bytes ciphertext.
        """

        content_bytes = self.padding(content_str).encode()
        ciphertext_bytes = self.aes.encrypt(content_bytes)
        return ciphertext_bytes

    def encrypt_to_base64(self, content_str):
        """From string encrypt to base64 ciphertext.
        """

        ciphertext_bytes = self.encrypt_to_bytes(content_str)
        ciphertext_bs64 = base64.b64encode(ciphertext_bytes).decode()
        return ciphertext_bs64

    def decrypt_from_bytes(self, ciphertext_bytes):
        """From bytes ciphertext decrypt to string.
        """

        content_bytes = self.aes.decrypt(ciphertext_bytes)
        content_str = self.unpadding(content_bytes.decode())
        return content_str

    def decrypt_from_base64(self, ciphertext_bs64):
        """From base64 ciphertext decrypt to string.
        """

        ciphertext_bytes = base64.b64decode(ciphertext_bs64)
        content_str = self.decrypt_from_bytes(ciphertext_bytes)
        return content_str


def encrypt_to_bytes(content_str, encrypt_key: str):
    """From string encrypt to bytes ciphertext.
    """

    aes: AES = AES(encrypt_key)
    ciphertext_bytes = aes.encrypt_to_bytes(content_str)
    return ciphertext_bytes


def encrypt_to_base64(content_str, encrypt_key: str) -> str:
    """From string encrypt to base64 ciphertext.
    """

    aes: AES = AES(encrypt_key)
    ciphertext_bs64 = aes.encrypt_to_base64(content_str)
    return ciphertext_bs64


def decrypt_from_bytes(ciphertext_bytes, decrypt_key: str) -> str:
    """From bytes ciphertext decrypt to string.
    """

    aes: AES = AES(decrypt_key)
    content_str = aes.decrypt_from_bytes(ciphertext_bytes)
    return content_str


def decrypt_from_base64(ciphertext_bs64, decrypt_key: str) -> str:
    """From base64 ciphertext decrypt to string.
    """

    aes: AES = AES(decrypt_key)
    content_str = aes.decrypt_from_base64(ciphertext_bs64)
    return content_str
```

