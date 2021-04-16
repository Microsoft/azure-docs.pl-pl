---
title: Samouczek — używanie programu OpenSSL do tworzenia certyfikatów testowych X.509 dla Azure IoT Hub| Microsoft Docs
description: Samouczek — używanie programu OpenSSL do tworzenia certyfikatów urzędu certyfikacji i urządzeń dla usługi Azure IoT Hub
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
ms.openlocfilehash: 0843e5d3a5e91cb4acdf18ad6bdf6f4f0c214f72
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378299"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Samouczek: tworzenie certyfikatów testowych przy użyciu programu OpenSSL

Chociaż certyfikaty X.509 można kupić od zaufanego urzędu certyfikacji, utworzenie własnej hierarchii certyfikatów testowych lub użycie certyfikatów z podpisem własnym jest odpowiednie do testowania uwierzytelniania urządzeń usługi IoT Hub. W poniższym przykładzie użyto [openssl](https://www.openssl.org/) i [podręcznika OpenSSL](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) do utworzenia urzędu certyfikacji (CA), podrzędnego urzędu certyfikacji i certyfikatu urządzenia. Następnie przykład podpisuje podrzędny urząd certyfikacji i certyfikat urządzenia w hierarchii certyfikatów. Jest to prezentowane tylko w celach przykładowych.

## <a name="step-1---create-the-root-ca-directory-structure"></a>Krok 1 . Tworzenie struktury katalogów głównego urzędu certyfikacji

Utwórz strukturę katalogów dla urzędu certyfikacji.

* W **katalogu certyfikatów** są przechowywane nowe certyfikaty.
* Katalog **db** jest używany dla bazy danych certyfikatów.
* W **katalogu prywatnym** jest przechowywane klucz prywatny urzędu certyfikacji.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>Krok 2 . Tworzenie pliku konfiguracji głównego urzędu certyfikacji

Przed utworzeniem urzędu certyfikacji utwórz plik konfiguracji i zapisz go jako `rootca.conf` w katalogu rootca.

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

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>Krok 3. Tworzenie głównego urzędu certyfikacji

Najpierw wygeneruj klucz i żądanie podpisania certyfikatu (CSR) w katalogu rootca.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Następnie utwórz certyfikat urzędu certyfikacji z podpisem własnym. Podpisywanie samodzielne jest odpowiednie do celów testowych. Określ ca_ext plików konfiguracji w wierszu polecenia. Wskazują one, że certyfikat jest dla głównego urzędu certyfikacji i może służyć do podpisywania certyfikatów i list odwołania certyfikatów (CRL). Podpisz certyfikat i zat zatwierdzanie go w bazie danych.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>Krok 4 . Tworzenie struktury katalogów podrzędnego urzędu certyfikacji

Utwórz strukturę katalogów dla podrzędnego urzędu certyfikacji.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>Krok 5 . Tworzenie pliku konfiguracji podrzędnego urzędu certyfikacji

Utwórz plik konfiguracji i zapisz go jako plik subca.conf w `subca` katalogu .

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

## <a name="step-6---create-a-subordinate-ca"></a>Krok 6 . Tworzenie podrzędnego urzędu certyfikacji

Utwórz nowy numer seryjny w pliku `rootca/db/serial` dla certyfikatu podrzędnego urzędu certyfikacji.

```bash
  openssl rand -hex 16 > db/serial
```

>[!IMPORTANT]
>Należy utworzyć nowy numer seryjny dla każdego certyfikatu podrzędnego urzędu certyfikacji i każdego certyfikatu urządzenia, który tworzysz. Różne certyfikaty nie mogą mieć tego samego numeru seryjnego.

W tym przykładzie pokazano, jak utworzyć podrzędny lub rejestracji urzędu certyfikacji. Ponieważ można użyć głównego urzędu certyfikacji do podpisywania certyfikatów, tworzenie podrzędnego urzędu certyfikacji nie jest ściśle konieczne. Jednak podrzędny urząd certyfikacji naśladuje prawdziwe hierarchie certyfikatów, w których główny urząd certyfikacji jest przechowywany w trybie offline, a podrzędne urzędu certyfikacji wystawiają certyfikaty klienta.

Użyj pliku konfiguracji, aby wygenerować klucz i żądanie podpisania certyfikatu (CSR).

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

Prześlij żądania CSR do głównego urzędu certyfikacji i użyj głównego urzędu certyfikacji do wystawienia i podpisania certyfikatu podrzędnego urzędu certyfikacji. Określ sub_ca_ext przełącznika rozszerzeń w wierszu polecenia. Rozszerzenia wskazują, że certyfikat jest dla urzędu certyfikacji, który może podpisywać certyfikaty i listy odwołania certyfikatów (CRL). Po wyświetleniu monitu podpisz certyfikat i zat zatwierdzanie go w bazie danych.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>Krok 7. Demonstruj dowód posiadania

Teraz masz zarówno certyfikat głównego urzędu certyfikacji, jak i certyfikat podrzędnego urzędu certyfikacji. Możesz użyć jednego z nich do podpisywania certyfikatów urządzeń. Ten, który wybierzesz, musi zostać przekazany do twojego IoT Hub. W poniższych krokach przyjęto założenie, że używasz certyfikatu podrzędnego urzędu certyfikacji. Aby przekazać i zarejestrować certyfikat podrzędnego urzędu certyfikacji do IoT Hub:

1. W witrynie Azure Portal do usługi IoTHub i wybierz pozycję **Ustawienia > certyfikatów.**

1. Wybierz **pozycję Dodaj,** aby dodać nowy certyfikat podrzędnego urzędu certyfikacji.

1. Wprowadź nazwę wyświetlaną w **polu Nazwa certyfikatu** i wybierz utworzony wcześniej plik certyfikatu PEM.

1. Wybierz pozycję **Zapisz**. Certyfikat jest wyświetlany na liście certyfikatów ze stanem **Niezweryfikowany.** Proces weryfikacji potwierdzi, że jesteś właścicielem certyfikatu.

   
1. Wybierz certyfikat, aby wyświetlić **okno dialogowe Szczegóły** certyfikatu.

1. Wybierz **pozycję Generuj kod weryfikacyjny.** Aby uzyskać więcej informacji, zobacz [Prove Possession of a CA certificate (Udowodnij posiadanie certyfikatu urzędu certyfikacji).](tutorial-x509-prove-possession.md)

1. Skopiuj kod weryfikacyjny do schowka. Należy ustawić kod weryfikacyjny jako podmiot certyfikatu. Jeśli na przykład kod weryfikacyjny to BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A, dodaj go jako podmiot certyfikatu, jak pokazano w kroku 9.

1. Wygeneruj klucz prywatny.

  ```bash
    $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
  ```

9. Wygeneruj żądanie podpisania certyfikatu (CSR) na podstawie klucza prywatnego. Dodaj kod weryfikacyjny jako podmiot certyfikatu.

  ```bash
  openssl req -new -key pop.key -out pop.csr
  
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

10. Utwórz certyfikat przy użyciu pliku konfiguracji głównego urzędu certyfikacji i żądania CSR w celu potwierdzenia posiadania certyfikatu.

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

11. Wybierz nowy certyfikat w widoku **Szczegóły** certyfikatu. Aby znaleźć plik PEM, przejdź do folderu certs.

12. Po zakończeniu przekazywania certyfikatu wybierz pozycję **Weryfikuj**. Stan certyfikatu urzędu certyfikacji powinien zmienić się na **Zweryfikowano**.

## <a name="step-8---create-a-device-in-your-iot-hub"></a>Krok 8. Tworzenie urządzenia w IoT Hub

Przejdź do swojego IoT Hub w Azure Portal i utwórz nową tożsamość urządzenia IoT z następującymi wartościami:

1. Podaj **identyfikator urządzenia,** który odpowiada nazwie podmiotu certyfikatów urządzenia.

1. Wybierz typ **uwierzytelniania X.509 urzędu certyfikacji ze podpisem.**

1. Wybierz pozycję **Zapisz**.

## <a name="step-9---create-a-client-device-certificate"></a>Krok 9. Tworzenie certyfikatu urządzenia klienckiego

Aby wygenerować certyfikat klienta, należy najpierw wygenerować klucz prywatny. Następujące polecenie pokazuje, jak utworzyć klucz prywatny za pomocą openssl. Utwórz klucz w katalogu podrzędnym.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Utwórz żądanie podpisania certyfikatu (CSR) dla klucza. Nie musisz wprowadzać hasła wyzwania ani opcjonalnej nazwy firmy. Należy jednak wprowadzić identyfikator urządzenia w polu nazwa pospolita.

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

Sprawdź, czy raport CSR jest tym, czego oczekujesz.

```bash
openssl req -text -in device.csr -noout
```

Wyślij żądania CSR do podrzędnego urzędu certyfikacji w celu zalogowania się do hierarchii certyfikatów. Określ `client_ext` w `-extensions` przełączniku . Należy zauważyć, `Basic Constraints` że w wystawionym certyfikacie wskazuje, że ten certyfikat nie jest dla urzędu certyfikacji. W przypadku podpisywania wielu certyfikatów należy zaktualizować numer seryjny przed wygenerowaniem każdego certyfikatu za pomocą polecenia `rand -hex 16 > db/serial` openssl.

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Następne kroki

Przejdź do [uwierzytelniania certyfikatu testowania,](tutorial-x509-test-certificate.md) aby określić, czy certyfikat może uwierzytelniać urządzenie w IoT Hub.
