---
title: Zestawy roszczeń zaświadczania platformy Azure
description: Zestawy roszczeń dotyczące zaświadczania platformy Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: afe2cf288cd4a15091e8278309b3ecf74a2d35a4
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572752"
---
# <a name="claim-sets"></a>Zestawy oświadczeń

Oświadczenia wygenerowane w procesie zaświadczania enclaves za pomocą zaświadczania Microsoft Azure mogą być podzielone na następujące kategorie:

- **Oświadczenia przychodzące**: oświadczenia wygenerowane przez Microsoft Azure zaświadczania po przeanalizowaniu dowodu zaświadczania i mogą być używane przez autorów zasad do definiowania reguł autoryzacji w zasadach niestandardowych

- **Oświadczenia wychodzące**: oświadczenia wygenerowane przez zaświadczenie platformy Azure i zawiera wszystkie oświadczenia, które kończą się tokenem zaświadczania

- **Oświadczenia właściwości**: oświadczenia utworzone jako dane wyjściowe przez zaświadczanie platformy Azure. Zawiera wszystkie oświadczenia reprezentujące właściwości tokenu zaświadczania, takie jak kodowanie raportu, czas trwania okresu ważności raportu itd.

### <a name="common-incoming-claims-across-all-attestation-types"></a>Typowe oświadczenia przychodzące we wszystkich typach zaświadczania

Poniższe oświadczenia są generowane przez zaświadczenie platformy Azure i mogą być używane do definiowania reguł autoryzacji w zasadach niestandardowych:
- **x-MS-Ver**: wersja schematu JWT (oczekiwana wartość to "1,0")
- **x-MS-zaświadczanie-Type**: wartość ciągu reprezentująca typ zaświadczania 
- **x-MS-Policy-hash**: skrót zasad oceny zaświadczania platformy Azure obliczony jako BASE64URL (SHA256 (UTF8 (BASE64URL (utf8)))
- **x-MS-Policy-Signer**: obiekt JSON z członkiem "JWK" reprezentujący klucz, z którego korzysta klient do podpisywania zasad, gdy klient przekazuje podpisane zasady

Poniższe oświadczenia są uważane za przestarzałe, ale są w pełni obsługiwane. Zaleca się użycie nieprzestarzałych nazw zgłoszeń.

Przestarzałe zgłoszenie | Zalecane zgłoszenie 
--- | --- 
ver | x-MS-Ver
tee | x-MS-zaświadczanie-typ
maa-policyHash | x-MS-Policy-hash
policy_hash | x-MS-Policy-hash
policy_signer | x-MS-Policy-Signer

### <a name="common-outgoing-claims-across-all-attestation-types"></a>Typowe oświadczenia wychodzące we wszystkich typach zaświadczania

Poniżej oświadczeń, które są zdefiniowane przez tokeny w [organizacji IETF](https://tools.ietf.org/html/rfc7519) i używane przez zaświadczanie platformy Azure w obiekcie Response:

- **"JTI" (identyfikator JWT)**
- **"ISS" (wystawca) — stwierdzenie**
- **"IAT" (wystawiony w)**
- **"EXP" (czas wygaśnięcia)**
- **"NBF" (nie wcześniej)**

Poniżej oświadczeń, które są zdefiniowane przez [grupę IETF Eat](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9) i używane przez zaświadczanie platformy Azure w obiekcie Response:
- **"Żądania nonce" (identyfikator jednorazowy)**

## <a name="claims-specific-to-sgx-enclaves"></a>Oświadczenia specyficzne dla SGX enclaves

### <a name="incoming-claims-specific-to-sgx-attestation"></a>Oświadczenia przychodzące specyficzne dla zaświadczania SGX

Poniższe oświadczenia są generowane przez usługę do zaświadczania SGX i mogą być używane do definiowania reguł autoryzacji w zasadach niestandardowych:
- **x-MS-SGX-is-możliwością debugowania**: wartość logiczna, która wskazuje, czy element enklawy ma włączone debugowanie
- **x-MS-SGX-Product-ID**
- **x-MS-SGX-mrsigner**: zakodowana wartość szesnastkowa pola "mrsigner" w ofercie
- **x-MS-SGX-mrenclave**: zakodowana wartość szesnastkowa pola "mrenclave" w ofercie
- **x-MS-SGX-SVN**: numer wersji zabezpieczeń zakodowany w ofercie 

### <a name="outgoing-claims-specific-to-sgx-attestation"></a>Wychodzące oświadczenia specyficzne dla zaświadczania SGX

Poniższe oświadczenia są generowane przez usługę i zawarte w obiekcie odpowiedzi dla zaświadczania SGX:
- **x-MS-SGX-is-możliwością debugowania**: wartość logiczna, która wskazuje, czy element enklawy ma włączone debugowanie
- **x-MS-SGX-Product-ID**
- **x-MS-Ver**
- **x-MS-SGX-mrsigner**: zakodowana wartość szesnastkowa pola "mrsigner" w ofercie
- **x-MS-SGX-mrenclave**: zakodowana wartość szesnastkowa pola "mrenclave" w ofercie
- **x-MS-SGX-SVN**: numer wersji zabezpieczeń zakodowany w ofercie 
- **x-MS-SGX-EHD**: enklawy przechowywane dane SFORMATOWANE jako BASE64URL (dane przechowywane enklawy)
- **x-MS-SGX-zabezpieczenia**: obiekt JSON opisujący zabezpieczenie używane do wykonywania zaświadczania. Wartość dla zgłoszenia x-MS-SGX-Value jest zagnieżdżonym obiektem JSON z następującymi parami klucz/wartość:
    - **qeidcertshash**: SHA256 wartość certyfikatu tożsamości wystawiającej zapytań
    - **qeidcrlhash**: SHA256 wartość z listy CRL certyfikatu tożsamości wystawiającej certyfikaty
    - **qeidhash**: SHA256 wartość zapytań Identity
    - **quotehash**: SHA256 wartość obliczonego cudzysłowu
    - **tcbinfocertshash**: SHA256 wartość certyfikatu wystawiającego informacje TCB
    - **tcbinfocrlhash**: SHA256 wartość listy CRL certyfikatów wystawiających informacje o TCB
    - **tcbinfohash**: obiekt JSON opisujący zabezpieczenie używane do wykonywania zaświadczania

Poniższe oświadczenia są uważane za przestarzałe, ale są w pełni obsługiwane i nadal będą uwzględniane w przyszłości. Zaleca się użycie nieprzestarzałych nazw zgłoszeń.

Przestarzałe zgłoszenie | Zalecane zgłoszenie
--- | --- 
$is — możliwością debugowania | x-MS-SGX-is-możliwością debugowania
$sgx — mrsigner | x-MS-SGX-mrsigner
$sgx — mrenclave | x-MS-SGX-mrenclave
Identyfikator $product | x-MS-SGX-Product-ID
$svn | x-MS-SGX-SVN
$tee | x-MS-zaświadczanie-typ
maa-ehd | x-MS-SGX-EHD
AAS — EHD | x-MS-SGX-EHD
maa-attestationcollateral | x-MS-SGX-zabezpieczenia

## <a name="claims-issued-specific-to-trusted-platform-module-tpm-attestation"></a>Oświadczenia wystawione specyficzne dla zaświadczania moduł TPM (TPM)

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>Oświadczenia przychodzące (mogą być również używane jako oświadczenia wychodzące)

- **aikValidated**: wartość logiczna zawierająca informacje o tym, czy certyfikat klucza zaświadczania tożsamości (AIK) został zweryfikowany.
- **aikPubHash**: ciąg zawierający algorytm Base64 (SHA256 (klucz publiczny AIK w formacie der)).
- **tpmVersion**: wartość całkowita zawierająca wersję główną moduł TPM (TPM).
- **secureBootEnabled**: wartość logiczna wskazująca, czy bezpieczny rozruch jest włączony.
- **iommuEnabled**: wartość logiczna wskazująca, czy jest włączona jednostka zarządzania pamięcią podwyjściową (jednostki IOMMU).
- **bootDebuggingDisabled**: wartość logiczna wskazująca, czy debugowanie rozruchowe jest wyłączone.
- **notSafeMode**: wartość logiczna określająca, czy system Windows nie działa w trybie awaryjnym.
- **notWinPE**: wartość logiczna wskazująca, czy system Windows nie działa w trybie WinPE.
- **vbsEnabled**: wartość logiczna wskazująca, czy funkcja vbs jest włączona.
- **vbsReportPresent**: wartość logiczna wskazująca, czy raport vbs enklawy jest dostępny.
- **enclaveAuthorId**: wartość ciągu zawierająca Base64Url wartość zakodowaną identyfikatora autora enklawy — identyfikator autora modułu podstawowego dla enklawy.
- **enclaveImageId**: wartość ciągu zawierająca Base64Url wartość zakodowaną w identyfikatorze obrazu enklawy — Identyfikator obrazu modułu podstawowego dla enklawy.
- **enclaveOwnerId**: wartość ciągu zawierająca Base64Url wartość zakodowaną identyfikatora właściciela enklawy — identyfikator właściciela dla enklawy.
- **enclaveFamilyId**: wartość ciągu zawierająca Base64Url wartość zakodowaną w identyfikatorze rodziny enklawy. Identyfikator rodziny modułu podstawowego dla enklawy.
- **enclaveSvn**: wartość całkowita zawierająca numer wersji zabezpieczeń modułu podstawowego dla enklawy.
- **enclavePlatformSvn**: wartość całkowita zawierająca numer wersji zabezpieczeń platformy obsługującej enklawy.
- **enclaveFlags**: enclaveFlags jest wartością całkowitą zawierającą flagi opisujące zasady czasu wykonywania dla enklawy.
  
### <a name="outgoing-claims-specific-to-tpm-attestation"></a>Oświadczenia wychodzące specyficzne dla zaświadczania modułu TPM

- **policy_hash**: wartość ciągu zawierająca Skrót SHA256 dla tekstu zasad obliczanego przez BASE64URL (SHA256 (BASE64URL ())).
- **policy_signer**: zawiera JWK z kluczem publicznym lub łańcuch certyfikatów obecny w podpisanym nagłówku zasad.
- **Ver (wersja)**: wartość ciągu zawierająca wersję raportu. Obecnie 1,0.
- **CNF (potwierdzenie)**: twierdzenie "CNF" służy do identyfikowania klucza będącego dowodem posiadania. Oświadczenia potwierdzające zgodnie z definicją w dokumencie RFC 7800 zawiera publiczną część zaświadczonego klucza enklawy reprezentowanego jako obiekt klucza internetowego JSON (JWK) (RFC 7517).
- **rp_data (dane jednostki uzależnionej)**: dane jednostki uzależnionej (jeśli istnieją) określone w żądaniu, używane przez jednostkę uzależnioną jako identyfikator jednorazowy w celu zagwarantowania Aktualności raportu.
- **"JTI" (identyfikator JWT)**: wartość żądania "JTI" (identyfikator JWT) zapewnia unikatowy identyfikator dla tokenu JWT. Wartość identyfikatora jest przypisywana w sposób, który gwarantuje, że istnieje niewielkie prawdopodobieństwo, że ta sama wartość zostanie przypadkowo przypisana do innego obiektu danych.

### <a name="property-claims"></a>Oświadczenia właściwości

- **report_validity_in_minutes**: wartość całkowita określająca czas ważności tokenu.
  - **Wartość domyślna (Time)**: jeden dzień w minutach.
  - **Wartość maksymalna (Time)**: jeden rok w minutach.
- **omit_x5c**: wartość logiczna określająca, czy zaświadczanie platformy Azure powinno pominąć certyfikat używany do zapewnienia potwierdzenia autentyczności usługi. Jeśli wartość jest równa true, x5t zostanie dodana do tokenu zaświadczania. Jeśli wartość jest równa false (domyślnie), x5c zostanie dodana do tokenu zaświadczania.

## <a name="next-steps"></a>Następne kroki
- [Tworzenie i podpisywanie zasad zaświadczania](author-sign-policy.md)
- [Konfigurowanie zaświadczania platformy Azure przy użyciu programu PowerShell](quickstart-powershell.md)
