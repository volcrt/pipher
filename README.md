# pipher
small python script to pipe and cipher encrypted/decrypted text

## install
```sh
git clone git@github.com:volcrt/pipher.git
chmod a+x pipher
cp pipher /usr/local/bin/
```

### usage

```
usage: pipher [-h] [-e] [-d] [-i IDENTITY] [-s SALT]

pipher decrypts or encrypts string from stdin and outputs the result

options:
  -h, --help            show this help message and exit
  -e, --encrypt         encrypts a string
  -d, --decrypt         decrypts a string
  -i IDENTITY, --identity IDENTITY
                        using public private key instead of password
  -s SALT, --salt SALT  some string as salt used for better protection against dictionary attacks
```



### Examples
using pipher to encrypt an environment variable you can simple pipe and cipher like so:

#### Prepare public / private keys:
pipher supports public and private keys in pem format
```sh
openssl genpkey -algorithm RSA -out private_key.pem -aes256
openssl rsa -pubout -in private_key.pem -out public_key.pem
```

#### Encrypt an environment variable
in this example we create an environment variable with an encrypted value



```sh
  # export permanent environment variable
  echo "GITHUB_TOKEN=$(pipher -e -i ~/public_key.pem)" >> ~/.profile
  # reload profile
  source ~/.profile
```

###### Pipher will first ask for the text you want to encrypt
$> hello world
###### if you haven't provided a public key pipher will ask you for a password
$> some top secret password

# otherwise pipher will use the public key to encrypt the text

#### Using an encrypted environment variable
in this example we use an encrypted environment variable that contains for example a
PersonalAccessToken for a docker registry
```sh
  echo $GITHUB_TOKEN | pipher -d -i ~/private_key.pem | docker login ghcr.io -u <USER> --password-stdin
```

first we pipe the encrypted version of the token to pipher which decrypts the token and pipes
