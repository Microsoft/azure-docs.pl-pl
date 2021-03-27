---
title: Samouczek — używanie OpenSSL do tworzenia certyfikatów testów X. 509 dla platformy Azure IoT Hub | Microsoft Docs
description: Samouczek — używanie OpenSSL do tworzenia certyfikatów urzędu certyfikacji i urządzeń dla usługi Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 0d083d856138d7895a6e03f4d290ef3c4ddebd05
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630721"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Samouczek: Tworzenie certyfikatów testowych przy użyciu programu OpenSSL

Mimo że można zakupić certyfikaty X. 509 z zaufanego urzędu certyfikacji, utworzenie własnej hierarchii certyfikatów testowych lub użycie certyfikatów z podpisem własnym jest wystarczające do testowania uwierzytelniania urządzenia usługi IoT Hub. Poniższy przykład używa [OpenSSL](https://www.openssl.org/) i [OpenSSL Cookbook](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) do tworzenia urzędu certyfikacji, podrzędnego urzędu certyfikacji i certyfikatu urządzenia. Przykład następnie podpisuje podrzędny urząd certyfikacji i certyfikat urządzenia do hierarchii certyfikatów. Jest to przedstawione tylko do celów.

## <a name="step-1---create-the-root-ca-directory-structure"></a>Krok 1. Tworzenie struktury katalogu głównego urzędu certyfikacji

Utwórz strukturę katalogów dla urzędu certyfikacji.

* W katalogu **certyfikatów** są przechowywane nowe certyfikaty.
* Katalog **bazy danych jest** używany w przypadku certyfikatu.
* Katalog **prywatny** przechowuje klucz prywatny urzędu certyfikacji.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>Krok 2. Tworzenie pliku konfiguracji głównego urzędu certyfikacji

Przed utworzeniem urzędu certyfikacji Utwórz plik konfiguracji i Zapisz go jako `rootca.conf` katalog rootca.

```xml
[default]
name                     = rootca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Root CA"

[ca_default]
home                     = ../rootca 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = none
default_days             = 3650
default_crl_days         = 365
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>Krok 3 — Tworzenie głównego urzędu certyfikacji

Najpierw Wygeneruj klucz i żądanie podpisania certyfikatu (CSR) w katalogu rootca.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Następnie Utwórz certyfikat urzędu certyfikacji z podpisem własnym. Samopodpisywania jest odpowiednie do celów testowych. Określ ca_ext rozszerzenia pliku konfiguracji w wierszu polecenia. Wskazują one, że certyfikat jest przeznaczony dla głównego urzędu certyfikacji i może służyć do podpisywania certyfikatów i list odwołania certyfikatów (CRL). Podpisz certyfikat i przekaż go do bazy danych.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>Krok 4. Tworzenie struktury katalogu podrzędnego urzędu certyfikacji

Utwórz strukturę katalogów dla podrzędnego urzędu certyfikacji.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>Krok 5. Tworzenie podrzędnego pliku konfiguracji urzędu certyfikacji

Utwórz plik konfiguracji i Zapisz go jako SubCA. conf w `subca` katalogu.

```bash
[default]
name                     = subca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Subordinate CA"

[ca_default]
home                     = . 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = copy 
default_days           
default_crl_days         = 90 
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash
```

## <a name="step-6---create-a-subordinate-ca"></a>Krok 6. Tworzenie podrzędnego urzędu certyfikacji

Utwórz nowy numer seryjny w `rootca/db/serial` pliku dla certyfikatu podrzędnego urzędu certyfikacji.

```bash
  openssl rand -hex 16 > db/serial
```

>[!IMPORTANT]
>Należy utworzyć nowy numer seryjny dla każdego certyfikatu podrzędnego urzędu certyfikacji i każdego tworzonego certyfikatu urządzenia. Różne certyfikaty nie mogą mieć tego samego numeru seryjnego.

W tym przykładzie pokazano, jak utworzyć podwładnego lub urzędu certyfikacji z rejestracją. Ze względu na to, że można użyć głównego urzędu certyfikacji do podpisywania certyfikatów, utworzenie podrzędnego urzędu certyfikacji nie jest absolutnie konieczne. Mając podrzędny urząd certyfikacji, należy jednak naśladować rzeczywiste hierarchie certyfikatów, w których główny urząd certyfikacji jest przechowywany w trybie offline, a podrzędne urzędy certyfikacji wystawiają certyfikaty klientów.

Użyj pliku konfiguracji w celu wygenerowania klucza i żądania podpisania certyfikatu (CSR).

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

Prześlij CSR do głównego urzędu certyfikacji i użyj głównego urzędu certyfikacji do wystawiania i podpisywania certyfikatu podrzędnego urzędu certyfikacji. Określ sub_ca_ext dla przełącznika rozszerzeń w wierszu polecenia. Rozszerzenia wskazują, że certyfikat dotyczy urzędu certyfikacji, który może podpisywać certyfikaty i listy odwołania certyfikatów (CRL). Po wyświetleniu monitu Podpisz certyfikat i przekaż go do bazy danych.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>Krok 7. przedstawienie dowodu posiadania

Masz teraz zarówno certyfikat głównego urzędu certyfikacji, jak i podrzędny certyfikat urzędu certyfikacji. Możesz użyć jednej z nich do podpisywania certyfikatów urządzeń. Wybrany wybór musi zostać przekazany do IoT Hub. W poniższych krokach przyjęto założenie, że jest używany certyfikat podrzędnego urzędu certyfikacji. Aby przekazać i zarejestrować podrzędny certyfikat urzędu certyfikacji w IoT Hub:

1. W Azure Portal przejdź do IoTHub i wybierz pozycję **ustawienia > certyfikaty**.

1. Wybierz pozycję **Dodaj** , aby dodać nowy certyfikat podrzędnego urzędu certyfikacji.

1. Wprowadź nazwę wyświetlaną w polu **Nazwa certyfikatu** i wybierz utworzony wcześniej plik certyfikatu PEM.

1. Wybierz pozycję **Zapisz**. Certyfikat zostanie wyświetlony na liście certyfikatów z **niezweryfikowanym** stanem. Proces weryfikacji będzie udowodnić, że posiadasz certyfikat.

   
1. Wybierz certyfikat, aby wyświetlić okno dialogowe **Szczegóły certyfikatu** .

1. Wybierz pozycję **Generuj kod weryfikacyjny**. Aby uzyskać więcej informacji, zobacz potwierdzenie [posiadania certyfikatu urzędu certyfikacji](tutorial-x509-prove-possession.md).

1. Skopiuj kod weryfikacyjny do schowka. Kod weryfikacyjny należy ustawić jako podmiot certyfikatu. Na przykład jeśli kod weryfikacyjny to BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A, Dodaj go jako podmiot certyfikatu, jak pokazano w następnym kroku.

1. Wygeneruj klucz prywatny.

  ```bash
    $ openssl req -new -key pop.key -out pop.csr

    -----
    Country Name (2 letter code) [XX]:.
    State or Province Name (full name) []:.
    Locality Name (eg, city) [Default City]:.
    Organization Name (eg, company) [Default Company Ltd]:.
    Organizational Unit Name (eg, section) []:.
    Common Name (eg, your name or your server hostname) []:BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A
    Email Address []:

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
 
  ```

9. Utwórz certyfikat przy użyciu pliku konfiguracji głównego urzędu certyfikacji i CSR.

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

10. Wybierz nowy certyfikat w widoku **Szczegóły certyfikatu**

11. Po przeładowaniu certyfikatu wybierz pozycję **Weryfikuj**. Stan certyfikatu urzędu certyfikacji powinien zostać zmieniony na **zweryfikowane**.

## <a name="step-8---create-a-device-in-your-iot-hub"></a>Krok 8. Tworzenie urządzenia w IoT Hub

Przejdź do IoT Hub w Azure Portal i Utwórz nową tożsamość urządzenia IoT przy użyciu następujących wartości:

1. Podaj **Identyfikator urządzenia** , który jest zgodny z nazwą podmiotu certyfikatów urządzeń.

1. Wybierz typ uwierzytelniania **podpisanego przez urząd certyfikacji X. 509** .

1. Wybierz pozycję **Zapisz**.

## <a name="step-9---create-a-client-device-certificate"></a>Krok 9. Tworzenie certyfikatu urządzenia klienckiego

Aby wygenerować certyfikat klienta, należy najpierw wygenerować klucz prywatny. Następujące polecenie pokazuje, jak utworzyć klucz prywatny przy użyciu OpenSSL. Utwórz klucz w katalogu SubCA.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Utwórz żądanie podpisania certyfikatu (CSR) dla klucza. Nie trzeba wprowadzać hasła wyzwania ani opcjonalnej nazwy firmy. Należy jednak wprowadzić identyfikator urządzenia w polu Nazwa pospolita.

```bash
openssl req -new -key device.key -out device.csr

-----
Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server hostname) []:`<your device ID>`
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

Sprawdź, czy CSR jest oczekiwany.

```bash
openssl req -text -in device.csr -noout
```

Wyślij CSR do podrzędnego urzędu certyfikacji w celu zalogowania się do hierarchii certyfikatów. Określ `client_ext` w `-extensions` przełączniku. Zwróć uwagę, że `Basic Constraints` w wystawionym certyfikacie wskazuje, że ten certyfikat nie jest urzędem certyfikacji. W przypadku podpisywania wielu certyfikatów Pamiętaj o zaktualizowaniu numeru seryjnego przed wygenerowaniem każdego certyfikatu za pomocą polecenia OpenSSL `rand -hex 16 > db/serial` .

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Następne kroki

Przejdź do [testowania uwierzytelniania certyfikatu](tutorial-x509-test-certificate.md) , aby określić, czy certyfikat może uwierzytelniać urządzenie w IoT Hub.
