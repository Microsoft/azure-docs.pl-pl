---
title: Samouczek — informacje na temat certyfikatów kluczy publicznych X.509 dla Azure IoT Hub| Microsoft Docs
description: Samouczek — informacje na temat certyfikatów kluczy publicznych X.509 dla Azure IoT Hub
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
ms.openlocfilehash: 5503f9ad57180146c25a01c133a27b34e643496c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378350"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Samouczek: opis certyfikatów kluczy publicznych X.509

Certyfikaty X.509 to dokumenty cyfrowe reprezentujące użytkownika, komputer, usługę lub urządzenie. Są one wystawiane przez urząd certyfikacji (CA), podrzędny urząd certyfikacji lub urząd rejestrowania i zawierają klucz publiczny podmiotu certyfikatu. Nie zawierają one klucza prywatnego podmiotu, który musi być przechowywany bezpiecznie. Certyfikaty kluczy publicznych są udokumentowane w dokumencie [RFC 5280.](https://tools.ietf.org/html/rfc5280) Są one podpisane cyfrowo i ogólnie zawierają następujące informacje:

* Informacje na temat podmiotu certyfikatu
* Klucz publiczny odpowiadający kluczowi prywatnemu podmiotu
* Informacje o wystawiającym urzędu certyfikacji
* Obsługiwane algorytmy szyfrowania i/lub podpisywania cyfrowego
* Informacje na temat określania stanu odwołania i ważności certyfikatu

## <a name="certificate-fields"></a>Pola certyfikatu

W czasie były trzy wersje certyfikatów. Każda wersja dodaje pola do wcześniejszego. Wersja 3 jest bieżąca i zawiera pola wersji 1 i wersji 2 oraz pola wersji 3. W wersji 1 zdefiniowano następujące pola:

* **Wersja:** wartość (1, 2 lub 3), która identyfikuje numer wersji certyfikatu
* **Numer seryjny:** unikatowy numer dla każdego certyfikatu wystawionego przez urząd certyfikacji
* **Algorytm podpisu urzędu certyfikacji:** nazwa algorytmu używanego przez urząd certyfikacji do podpisywania zawartości certyfikatu
* **Nazwa wystawcy:** nazwa wyróżniająca urzędu wystawiającego certyfikat
* **Okres ważności:** okres, dla którego certyfikat jest uznawany za ważny
* **Nazwa podmiotu:** nazwa jednostki reprezentowanej przez certyfikat
* **Informacje o kluczu publicznym podmiotu:** klucz publiczny należący do podmiotu certyfikatu

W wersji 2 dodano następujące pola zawierające informacje o wystawcy certyfikatu. Te pola są jednak rzadko używane.

* **Unikatowy identyfikator wystawcy:** unikatowy identyfikator wystawiającego urzędu certyfikacji zgodnie z definicją urzędu certyfikacji
* **Unikatowy identyfikator podmiotu:** unikatowy identyfikator podmiotu certyfikatu zdefiniowany przez wystawiający urząd certyfikacji

W certyfikatach w wersji 3 dodano następujące rozszerzenia:

* **Identyfikator klucza urzędu:** może to być jedna z dwóch wartości:
  * Podmiot urzędu certyfikacji i numer seryjny certyfikatu urzędu certyfikacji, który wystawił ten certyfikat
  * Skrót klucza publicznego urzędu certyfikacji, który wystawił ten certyfikat
* **Identyfikator klucza podmiotu:** skrót klucza publicznego bieżącego certyfikatu
* **Użycie klucza** Definiuje usługę, dla której można użyć certyfikatu. Może to być co najmniej jedna z następujących wartości:
  * **Podpis cyfrowy**
  * **Brak repudiation**
  * **Szyfrowanie klucza**
  * **Szyfrowanie danych**
  * **Umowa klucza**
  * **Znak certyfikatu klucza**
  * **Znak listy CRL**
  * **Tylko szyfrowanie**
  * **Tylko odszyfruj**
* **Okres użycia klucza prywatnego:** okres ważności dla części klucza prywatnego pary kluczy
* **Zasady certyfikatów:** zasady używane do weryfikowania podmiotu certyfikatu
* **Mapowania zasad:** mapuje zasady w jednej organizacji na zasady w innej
* **Alternatywna nazwa podmiotu:** lista alternatywnych nazw podmiotu
* **Alternatywna nazwa wystawcy:** lista alternatywnych nazw wystawiającego urzędu certyfikacji
* **Atrybut Dir podmiotu:** atrybuty z katalogu X.500 lub LDAP
* **Podstawowe ograniczenia:** umożliwia wyznaczanie, czy certyfikat jest wystawiany dla urzędu certyfikacji, czy dla użytkownika, komputera, urządzenia lub usługi. To rozszerzenie zawiera również ograniczenie długości ścieżki, które ogranicza liczbę podrzędnych urzędu buforowania, które mogą istnieć.
* **Ograniczenia nazw:** określa, które przestrzenie nazw są dozwolone w certyfikacie wystawionym przez urząd certyfikacji
* **Ograniczenia zasad:** mogą służyć do zabraniania mapowania zasad między relacyjnie
* **Rozszerzone użycie klucza:** wskazuje, jak można używać klucza publicznego certyfikatu poza celami wskazanymi w **rozszerzeniu Użycie** klucza
* **Punkty dystrybucji listy CRL:** zawiera co najmniej jeden adres URL, pod którym opublikowano podstawową listę odwołania certyfikatów (CRL)
* **Należy odszukać anyPolicy:** zapobiega użyciu wszystkich zasad wystawiania **OID** (2.5.29.32.0) w certyfikatach podrzędnych urzędu certyfikacji
* **Najświeższa listy CRL:** zawiera co najmniej jeden adres URL, pod którym opublikowano różnicowej listy CRL urzędu wystawiającego urzędu certyfikacji
* **Dostęp do informacji o urzędzie:** zawiera co najmniej jeden adres URL, pod którym opublikowano certyfikat urzędu wystawiającego certyfikaty
* **Dostęp do informacji o podmiotach:** zawiera informacje na temat pobierania dodatkowych szczegółów dla podmiotu certyfikatu

## <a name="certificate-formats"></a>Formaty certyfikatów

Certyfikaty można zapisywać w różnych formatach. Azure IoT Hub zazwyczaj używa formatów PEM i PFX.

### <a name="binary-certificate"></a>Certyfikat binarny

Zawiera on certyfikat binarny w postaci pierwotnej z zastosowaniem kodowania DER ASN.1.

### <a name="ascii-pem-format"></a>Format ASCII PEM

Certyfikat PEM (rozszerzenie PEM) zawiera certyfikat zakodowany w formacie base64, rozpoczynający się od -----BEGIN CERTIFICATE----- a kończąc na -----END CERTIFICATE-----. Format PEM jest bardzo powszechny i jest wymagany przez IoT Hub podczas przekazywania niektórych certyfikatów.

### <a name="ascii-pem-key"></a>Klucz ASCII (PEM)

Zawiera klucz DER zakodowany w formacie base64 z potencjalnie dodatkowymi metadanymi na temat algorytmu używanego do ochrony hasłem.

### <a name="pkcs7-certificate"></a>PKCS #7 certyfikatu

Format przeznaczony do transportu podpisanych lub zaszyfrowanych danych. Jest on definiowany przez [RFC 2315.](https://tools.ietf.org/html/rfc2315) Może zawierać cały łańcuch certyfikatów.

### <a name="pkcs8-key"></a>Klucz PKCS#8

Format magazynu kluczy prywatnych zdefiniowany przez [RFC 5208.](https://tools.ietf.org/html/rfc5208)

### <a name="pkcs12-key-and-certificate"></a>PKCS #12 i certyfikat

Złożony format, który może przechowywać i chronić klucz oraz cały łańcuch certyfikatów. Jest on często używany z rozszerzeniem pfx. PKCS #12 to synonim formatu PFX.

## <a name="for-more-information"></a>Więcej informacji

Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Przewodnik laika dotyczący żargonu certyfikatu X.509](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [Koncepcyjne zrozumienie certyfikatów X.509 urzędu certyfikacji w branży IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept)

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz wygenerować certyfikaty testowe, których można użyć do uwierzytelniania urządzeń IoT Hub, zobacz następujące tematy:

* [Tworzenie certyfikatów testowych za pomocą skryptów Microsoft-Supplied skryptów](tutorial-x509-scripts.md)
* [Tworzenie certyfikatów testowych przy użyciu programu OpenSSL](tutorial-x509-openssl.md)
* [Tworzenie certyfikatów testów Self-Signed OpenSSL](tutorial-x509-self-sign.md)

Jeśli masz certyfikat urzędu certyfikacji (CA) lub certyfikat podrzędnego urzędu certyfikacji i chcesz przekazać go do centrum IoT i udowodnić, że jesteś jego właścicielem, zobacz Dokument posiadania certyfikatu [urzędu certyfikacji](tutorial-x509-prove-possession.md).
