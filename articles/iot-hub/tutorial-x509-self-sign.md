---
title: Samouczek — używanie programu OpenSSL do tworzenia certyfikatów z podpisem własnym dla Azure IoT Hub | Microsoft Docs
description: Samouczek — używanie programu OpenSSL do tworzenia certyfikatów X.509 z podpisem własnym dla Azure IoT Hub
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
ms.openlocfilehash: 982e402946cbd71d946bc1e316cef99621c536a3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378197"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Samouczek: tworzenie certyfikatów z podpisem własnym przy użyciu programu OpenSSL

Urządzenie można uwierzytelnić w aplikacji IoT Hub przy użyciu dwóch certyfikatów urządzeń z podpisem własnym. Jest to czasami nazywane uwierzytelnianiem odciskiem palca, ponieważ certyfikaty zawierają odciski palca (wartości skrótu) przesyłane do centrum IoT. Poniższe kroki informują o tym, jak utworzyć dwa certyfikaty z podpisem własnym.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>Krok 1. Tworzenie klucza dla pierwszego certyfikatu

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>Krok 2. Tworzenie żądania CSR dla pierwszego certyfikatu

Upewnij się, że po wyświetleniu monitu określono identyfikator urządzenia.

```bash
openssl req -new -key device1.key -out device1.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-3---check-the-csr"></a>Krok 3. Sprawdzanie csr

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>Krok 4. Certyfikat samodzielnego podpisywania 1

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>Krok 5. Tworzenie klucza dla certyfikatu 2

Po wyświetleniu monitu określ ten sam identyfikator urządzenia, który został użyty dla certyfikatu 1.

```bash
openssl req -new -key device2.key -out device2.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-6---self-sign-certificate-2"></a>Krok 6 . Certyfikat samodzielnego podpisywania 2

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-7---retrieve-the-thumbprint-for-certificate-1"></a>Krok 7. Pobieranie odcisku palca certyfikatu 1

```bash
openssl x509 -in device.crt -text -fingerprint
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-2"></a>Krok 8. Pobieranie odcisku palca certyfikatu 2

```bash
openssl x509 -in device2.crt -text -fingerprint
```

## <a name="step-9---create-a-new-iot-device"></a>Krok 9. Tworzenie nowego urządzenia IoT

Przejdź do swojego IoT Hub w Azure Portal i utwórz nową tożsamość urządzenia IoT o następujących właściwościach:

* Podaj **identyfikator urządzenia,** który odpowiada nazwie podmiotu dwóch certyfikatów.
* Wybierz typ **uwierzytelniania X.509 z podpisem** własnym.
* Wklej odciski palca ciągu w postaci hex skopiowane z certyfikatów podstawowych i pomocniczych urządzenia. Upewnij się, że ciągi hex nie mają ograniczników dwukropka.

## <a name="next-steps"></a>Następne kroki

Przejdź do [uwierzytelniania certyfikatu testowania,](tutorial-x509-test-certificate.md) aby określić, czy certyfikat może uwierzytelniać urządzenie w IoT Hub.
