---
title: Samouczek — Omówienie certyfikatów kluczy publicznych X. 509 dla platformy Azure IoT Hub | Microsoft Docs
description: Samouczek — Omówienie certyfikatów kluczy publicznych X. 509 dla usługi Azure IoT Hub
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
ms.openlocfilehash: fada68ba395b959e557542eb8c230561aad84214
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384327"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Samouczek: zrozumienie certyfikatów kluczy publicznych X. 509

Certyfikaty X. 509 to dokumenty cyfrowe reprezentujące użytkownika, komputer, usługę lub urządzenie. Są wystawiane przez urząd certyfikacji (CA), podrzędny urząd certyfikacji lub urząd rejestrowania i zawierają klucz publiczny podmiotu certyfikatu. Nie zawierają one klucza prywatnego podmiotu, który musi być bezpiecznie przechowywany. Certyfikaty klucza publicznego są udokumentowane w [dokumencie RFC 5280](https://tools.ietf.org/html/rfc5280). Są one podpisane cyfrowo i ogólnie, zawierają następujące informacje:

* Informacje o podmiotu certyfikatu
* Klucz publiczny, który odpowiada kluczowi prywatnemu podmiotu
* Informacje o URZĘDzie wystawiającym certyfikaty
* Obsługiwane algorytmy szyfrowania i/lub podpisywania cyfrowego
* Informacje umożliwiające określenie stanu odwołania i ważności certyfikatu

## <a name="certificate-fields"></a>Pola certyfikatów

Z upływem czasu istnieją trzy wersje certyfikatów. Każda wersja dodaje pola do tych pól przed. Wersja 3 jest bieżąca i zawiera pola w wersji 1 i 2 oprócz pól w wersji 3. W wersji 1 zdefiniowano następujące pola:

* **Wersja**: wartość (1, 2 lub 3), która identyfikuje numer wersji certyfikatu
* **Numer seryjny**: unikatowy numer dla każdego certyfikatu wystawionego przez urząd certyfikacji
* **Algorytm podpisu urzędu certyfikacji**: Nazwa algorytmu używanego przez urząd certyfikacji do podpisywania zawartości certyfikatu
* **Nazwa wystawcy**: nazwa wyróżniająca (DN) wystawiającego certyfikat urzędu certyfikacji
* **Okres ważności**: okres, dla którego certyfikat jest uznawany za ważny
* **Nazwa podmiotu**: Nazwa jednostki reprezentowanej przez certyfikat
* **Informacje o kluczu publicznym podmiotu**: klucz publiczny należący do podmiotu certyfikatu

W wersji 2 dodano następujące pola zawierające informacje o wystawcy certyfikatu. Te pola są jednak rzadko używane.

* **Unikatowy identyfikator wystawcy**: unikatowy identyfikator urzędu wystawiającego certyfikaty zgodnie z definicją urzędu certyfikacji
* **Unikatowy identyfikator podmiotu**: unikatowy identyfikator podmiotu certyfikatu zdefiniowany przez urząd wystawiający certyfikaty.

W przypadku certyfikatów w wersji 3 dodano następujące rozszerzenia:

* **Identyfikator klucza urzędu certyfikacji**: może to być jedna z dwóch wartości:
  * Podmiot urzędu certyfikacji i numer seryjny certyfikatu urzędu certyfikacji, który wystawił ten certyfikat
  * Skrót klucza publicznego urzędu certyfikacji, który wystawił ten certyfikat
* **Identyfikator klucza podmiotu**: skrót klucza publicznego bieżącego certyfikatu
* **Użycie klucza** Definiuje usługę, dla której może być używany certyfikat. Może to być co najmniej jedna z następujących wartości:
  * **Podpis cyfrowy**
  * **Brak wyparcia**
  * **Szyfrowanie klucza**
  * **Szyfrowanie danych**
  * **Umowa kluczowa**
  * **Podpis klucza certyfikatu**
  * **Podpis listy CRL**
  * **Tylko szyfrowanie**
  * **Tylko odszyfrowywanie**
* **Okres użycia klucza prywatnego**: okres ważności części pary kluczy
* **Zasady certyfikatów**: zasady służące do weryfikacji podmiotu certyfikatu
* **Mapowania zasad**: mapuje zasady w jednej organizacji na zasady w innym
* **Alternatywna nazwa podmiotu**: Lista alternatywnych nazw podmiotu
* **Alternatywna nazwa wystawcy**: Lista alternatywnych nazw dla wystawiającego urzędu certyfikacji
* **Atrybut katalogu podmiotu**: atrybuty z katalogu X. 500 lub LDAP
* **Podstawowe ograniczenia**: umożliwia certyfikatowi określenie, czy jest wystawiony dla urzędu certyfikacji, czy dla użytkownika, komputera, urządzenia lub usługi. To rozszerzenie zawiera również ograniczenie długości ścieżki, które ogranicza liczbę podrzędnych urzędów certyfikacji, które mogą istnieć.
* **Ograniczenia nazw**: określa, które przestrzenie nazw są dozwolone w certyfikacie wystawionym przez urząd certyfikacji
* **Ograniczenia zasad**: mogą być używane do zakazywania mapowań zasad między urzędami certyfikacji
* **Rozszerzone użycie klucza**: wskazuje, w jaki sposób można używać klucza publicznego certyfikatu poza celami określonymi w rozszerzeniu **użycia klucza**
* **Punkty dystrybucji listy CRL**: zawiera jeden lub więcej adresów URL, w których publikowana jest podstawowa lista odwołania certyfikatów (CRL)
* **Wstrzymywanie anyPolicy**: uniemożliwia użycie **wszystkich zasad wystawiania** OID (2.5.29.32.0) w certyfikatach podrzędnych urzędów certyfikacji.
* **Najnowsza lista CRL**: zawiera jeden lub więcej adresów URL, w których publikowana jest różnicowa lista CRL urzędu wystawiającego certyfikaty
* **Dostęp do informacji o urzędach**: zawiera co najmniej jeden adres URL, pod którym publikowany jest certyfikat wystawiającego urzędu certyfikacji
* **Dostęp do informacji o podmiotach**: zawiera informacje dotyczące sposobu pobierania dodatkowych szczegółów dotyczących podmiotu certyfikatu

## <a name="certificate-formats"></a>Formaty certyfikatów

Certyfikaty mogą być zapisywane w różnych formatach. Uwierzytelnianie za pomocą usługi Azure IoT Hub zazwyczaj używa formatów PEM i PFX.

### <a name="binary-certificate"></a>Certyfikat binarny

Zawiera on certyfikat binarny w postaci surowej przy użyciu kodowania DER ASN. 1.

### <a name="ascii-pem-format"></a>Format PEM w formacie ASCII

Certyfikat PEM (rozszerzenie PEM) zawiera certyfikat szyfrowany algorytmem Base64, zaczynający się od-----BEGIN CERTIFICATE-----i kończący się na-----końcowego certyfikatu-----. Format PEM jest bardzo powszechny i jest wymagany przez IoT Hub podczas przekazywania pewnych certyfikatów.

### <a name="ascii-pem-key"></a>Klucz ASCII (PEM)

Zawiera zakodowany w formacie base64 klucz DER z potencjalnie dodatkowymi metadanymi o algorytmie używanym do ochrony hasłem.

### <a name="pkcs7-certificate"></a>Certyfikat PKCS # 7

Format przeznaczony do transportu zapisywanych lub zaszyfrowanych danych. Jest on zdefiniowany w [dokumencie RFC 2315](https://tools.ietf.org/html/rfc2315). Może obejmować cały łańcuch certyfikatów.

### <a name="pkcs8-key"></a>Klucz PKCS # 8

Format magazynu kluczy prywatnych zdefiniowany w [dokumencie RFC 5208](https://tools.ietf.org/html/rfc5208).

### <a name="pkcs12-key-and-certificate"></a>Certyfikat i klucz PKCS # 12

Skomplikowany format, który może przechowywać i chronić klucz oraz cały łańcuch certyfikatów. Jest on często używany z rozszerzeniem PFX. Format PKCS # 12 jest równoznaczny z formatem PFX.

## <a name="for-more-information"></a>Więcej informacji

Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Przewodnik po programie Layman dla certyfikatu X. 509 żargon](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [Omówienie pojęć związanych z certyfikatami urzędu certyfikacji X. 509 w branży IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept)

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz generować certyfikaty testowe, których można użyć do uwierzytelniania urządzeń na IoT Hub, zobacz następujące tematy:

* [Tworzenie certyfikatów testowych przy użyciu skryptów Microsoft-Supplied](tutorial-x509-scripts.md)
* [Tworzenie certyfikatów testowych przy użyciu OpenSSL](tutorial-x509-openssl.md)
* [Tworzenie Self-Signed certyfikatów testowych przy użyciu programu OpenSSL](tutorial-x509-self-sign.md)

Jeśli masz certyfikat urzędu certyfikacji lub podrzędnego urzędu certyfikacji i chcesz przekazać go do centrum IoT i udowodnić, że jesteś jego własnością, zobacz [dowód posiadania certyfikatu urzędu certyfikacji](tutorial-x509-prove-possession.md).
