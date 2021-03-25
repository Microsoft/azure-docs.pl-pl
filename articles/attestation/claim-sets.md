---
title: Zestawy roszczeń zaświadczania platformy Azure
description: Zestawy roszczeń dotyczące zaświadczania platformy Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 0d6d5a08ea85ebb666acc0336f1e1d7ec5e097da
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044672"
---
# <a name="claim-sets"></a>Zestawy oświadczeń

Oświadczenia wygenerowane w procesie zaświadczania enclaves za pomocą zaświadczania Microsoft Azure mogą być podzielone na następujące kategorie:

- **Oświadczenia przychodzące**: oświadczenia wygenerowane przez Microsoft Azure zaświadczania po przeanalizowaniu dowodu zaświadczania i mogą być używane przez autorów zasad do definiowania reguł autoryzacji w zasadach niestandardowych

- **Oświadczenia wychodzące**: oświadczenia wygenerowane przez zaświadczanie platformy Azure i zawarte w tokenie zaświadczania

- **Oświadczenia właściwości**: oświadczenia utworzone jako dane wyjściowe przez zaświadczanie platformy Azure. Zawiera wszystkie oświadczenia reprezentujące właściwości tokenu zaświadczania, takie jak kodowanie raportu, czas trwania okresu ważności raportu itd.

## <a name="incoming-claims"></a>Oświadczenia przychodzące 

### <a name="sgx-attestation"></a>Zaświadczanie SGX

Oświadczenia, które mają być używane przez autorów zasad do definiowania reguł autoryzacji w zasadach zaświadczania SGX:

- **x-MS-SGX-is-możliwością debugowania**: wartość logiczna, która wskazuje, czy element enklawy ma włączone debugowanie
- **x-MS-SGX-Product-ID**: wartość identyfikatora produktu SGX enklawy 
- **x-MS-SGX-mrsigner**: zakodowana wartość szesnastkowa pola "mrsigner" w ofercie
- **x-MS-SGX-mrenclave**: zakodowana wartość szesnastkowa pola "mrenclave" w ofercie
- **x-MS-SGX-SVN**: numer wersji zabezpieczeń zakodowany w ofercie 

Poniższe oświadczenia są uważane za przestarzałe, ale są w pełni obsługiwane i nadal będą uwzględniane w przyszłości. Zaleca się użycie nieprzestarzałych nazw zgłoszeń.

Przestarzałe zgłoszenie | Zalecane zgłoszenie
--- | --- 
$is — możliwością debugowania | x-MS-SGX-is-możliwością debugowania
Identyfikator $product | x-MS-SGX-Product-ID
$sgx — mrsigner | x-MS-SGX-mrsigner
$sgx — mrenclave | x-MS-SGX-mrenclave
$svn | x-MS-SGX-SVN

### <a name="tpm-attestation"></a>Poświadczanie za pomocą modułu TPM

Oświadczenia, które mają być używane przez autorów zasad do definiowania reguł autoryzacji w zasadach zaświadczania modułu TPM:

- **aikValidated**: wartość logiczna zawierająca informacje o tym, czy certyfikat klucza zaświadczania tożsamości (AIK) został zweryfikowany
- **aikPubHash**: ciąg zawierający algorytm Base64 (SHA256 (klucz publiczny AIK w formacie der))
- **tpmVersion**: wartość całkowita zawierająca wersję główną Trusted Platform Module (TPM)
- **secureBootEnabled**: wartość logiczna wskazująca, czy włączono bezpieczny rozruch
- **iommuEnabled**: wartość logiczna wskazująca, czy jest włączona jednostka zarządzania pamięcią podwyjściową (jednostki IOMMU)
- **bootDebuggingDisabled**: wartość logiczna wskazująca, czy debugowanie rozruchowe jest wyłączone
- **notSafeMode**: wartość logiczna wskazująca, czy system Windows nie działa w trybie awaryjnym
- **notWinPE**: wartość logiczna wskazująca, czy system Windows nie działa w trybie WinPE
- **vbsEnabled**: wartość logiczna wskazująca, czy funkcja vbs jest włączona
- **vbsReportPresent**: wartość logiczna wskazująca, czy jest dostępny raport vbs enklawy

### <a name="vbs-attestation"></a>Zaświadczanie VBS

Oprócz OŚWIADCZEŃ zasad zaświadczania modułu TPM poniższe oświadczenia mogą być używane przez autorów zasad do definiowania reguł autoryzacji w zasadach zaświadczania VBS.

- **enclaveAuthorId**: wartość ciągu zawierająca Base64Url wartość zakodowaną identyfikatora autora enklawy — identyfikator autora modułu podstawowego dla enklawy
- **enclaveImageId**: wartość ciągu zawierająca Base64Url wartość zakodowaną w identyfikatorze obrazu enklawy — Identyfikator obrazu modułu podstawowego dla enklawy
- **enclaveOwnerId**: wartość ciągu zawierająca Base64Url wartość zakodowaną identyfikatora właściciela enklawy — identyfikator właściciela enklawy
- **enclaveFamilyId**: wartość ciągu zawierająca Base64Url wartość zakodowaną w identyfikatorze rodziny enklawy. Identyfikator rodziny modułu podstawowego dla enklawy
- **enclaveSvn**: wartość całkowita zawierająca numer wersji zabezpieczeń modułu podstawowego dla enklawy
- **enclavePlatformSvn**: wartość całkowita zawierająca numer wersji zabezpieczeń platformy, która hostuje enklawy
- **enclaveFlags**: enclaveFlags jest wartością całkowitą zawierającą flagi opisujące zasady czasu wykonywania dla enklawy

## <a name="outgoing-claims"></a>Oświadczenia wychodzące 

### <a name="common-for-all-attestation-types"></a>Wspólne dla wszystkich typów zaświadczania

Zaświadczanie platformy Azure obejmuje poniższe oświadczenia w tokenie zaświadczania dla wszystkich typów zaświadczania. 

- **x-MS-Ver**: wersja schematu JWT (oczekiwana wartość to "1,0")
- **x-MS-zaświadczanie-Type**: wartość ciągu reprezentująca typ zaświadczania 
- **x-MS-Policy-hash**: skrót zasad oceny zaświadczania platformy Azure obliczony jako BASE64URL (SHA256 (UTF8 (BASE64URL (utf8)))
- **x-MS-Policy-Signer**: obiekt JSON z elementem członkowskim "JWK" reprezentującym klucz, który Klient użył do podpisania zasad. Ma to zastosowanie w przypadku przekazywania przez klienta podpisanej zasady

Poniżej znajdują się nazwy zgłoszeń ze [specyfikacją JWT grupy IETF](https://tools.ietf.org/html/rfc7519)

- **"JTI" (identyfikator JWT)** — unikatowy identyfikator dla tokenu JWT
- **"ISS" (wystawca)** — podmiot, który wystawił token JWT 
- **"IAT" (wystawiony w)** — czas, w którym został wystawiony token JWT 
- czas wygaśnięcia **żądania "EXP" (czas wygaśnięcia)** , po upływie którego nie można zatwierdzić tokenu JWT do przetwarzania
- **"NBF" (nie wcześniej)** przed upływem czasu, przed upływem którego nie można zatwierdzić tokenu JWT do przetwarzania 

Wymienione poniżej nazwy zgłoszeń są używane w [specyfikacji projektu Eat](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)

- **"Wystąpienie nonce" (identyfikator jednorazowy)** — nieprzekształcona bezpośrednia kopia opcjonalnej wartości nonce dostarczonej przez klienta 

Poniższe oświadczenia są uważane za przestarzałe, ale są w pełni obsługiwane i nadal będą uwzględniane w przyszłości. Zaleca się użycie nieprzestarzałych nazw zgłoszeń.

Przestarzałe zgłoszenie | Zalecane zgłoszenie
--- | --- 
ver | x-MS-Ver
tee | x-MS-zaświadczanie-typ
policy_hash | x-MS-Policy-hash
maa-policyHash | x-MS-Policy-hash
policy_signer  | x-MS-Policy-Signer

### <a name="sgx-attestation"></a>Zaświadczanie SGX 

Poniższe oświadczenia są generowane i uwzględniane w tokenie zaświadczania przez usługę na potrzeby zaświadczania SGX.

- **x-MS-SGX-is-możliwością debugowania**: wartość logiczna, która wskazuje, czy element enklawy ma włączone debugowanie
- **x-MS-SGX-Product-ID**: wartość identyfikatora produktu SGX enklawy 
- **x-MS-SGX-mrsigner**: zakodowana wartość szesnastkowa pola "mrsigner" w ofercie
- **x-MS-SGX-mrenclave**: zakodowana wartość szesnastkowa pola "mrenclave" w ofercie
- **x-MS-SGX-SVN**: numer wersji zabezpieczeń zakodowany w ofercie 
- **x-MS-SGX-EHD**: enklawy przechowywane dane SFORMATOWANE jako BASE64URL (dane przechowywane enklawy)
- **x-MS-SGX-zabezpieczenia**: obiekt JSON opisujący zabezpieczenie używane do wykonywania zaświadczania. Wartość dla zgłoszenia x-MS-SGX-Value jest zagnieżdżonym obiektem JSON z następującymi parami klucz/wartość:
    - **qeidcertshash**: SHA256 wartość quota ENKLAWY (zapytań), które wystawiają certyfikaty
    - **qeidcrlhash**: SHA256 wartość z listy CRL certyfikatu tożsamości wystawiającej certyfikaty
    - **qeidhash**: SHA256 wartość zapytań Identity
    - **quotehash**: SHA256 wartość obliczonego cudzysłowu
    - **tcbinfocertshash**: SHA256 wartość certyfikatu wystawiającego informacje TCB
    - **tcbinfocrlhash**: SHA256 wartość listy CRL certyfikatów wystawiających informacje o TCB
    - **tcbinfohash**: SHA256 wartość informacji o ZABEZPIECZENIAch TCB

Poniższe oświadczenia są uważane za przestarzałe, ale są w pełni obsługiwane i nadal będą uwzględniane w przyszłości. Zaleca się użycie nieprzestarzałych nazw zgłoszeń.

Przestarzałe zgłoszenie | Zalecane zgłoszenie
--- | --- 
$is — możliwością debugowania | x-MS-SGX-is-możliwością debugowania
Identyfikator $product | x-MS-SGX-Product-ID
$sgx — mrsigner | x-MS-SGX-mrsigner
$sgx — mrenclave | x-MS-SGX-mrenclave
$svn | x-MS-SGX-SVN
$maa — EHD | x-MS-SGX-EHD
$aas — EHD | x-MS-SGX-EHD
$maa — attestationcollateral | x-MS-SGX-zabezpieczenia

### <a name="tpm-and-vbs-attestation"></a>Zaświadczanie modułu TPM i VBS

- **CNF (potwierdzenie)**: to twierdzenie "CNF" służy do identyfikowania klucza będącego dowodem posiadania. Twierdzenie potwierdzające zgodnie z definicją w dokumencie RFC 7800 zawiera publiczną część zaświadczonego klucza enklawy reprezentowanego jako obiekt klucza internetowego JSON (JWK) (RFC 7517)
- **rp_data (dane jednostki uzależnionej)**: dane jednostki uzależnionej (jeśli istnieją) określone w żądaniu, używane przez jednostkę uzależnioną jako identyfikator jednorazowy w celu zagwarantowania Aktualności raportu. rp_data jest dodawana tylko wtedy, gdy rp_data

## <a name="property-claims"></a>Oświadczenia właściwości

### <a name="tpm-and-vbs-attestation"></a>Zaświadczanie modułu TPM i VBS

- **report_validity_in_minutes**: liczba całkowita określająca czas ważności tokenu.
  - **Wartość domyślna (Time)**: jeden dzień w minutach.
  - **Wartość maksymalna (Time)**: jeden rok w minutach.
- **omit_x5c**: wartość logiczna określająca, czy zaświadczanie platformy Azure powinno pominąć certyfikat używany do zapewnienia potwierdzenia autentyczności usługi. Jeśli wartość jest równa true, x5t zostanie dodana do tokenu zaświadczania. Jeśli wartość jest równa false (domyślnie), x5c zostanie dodana do tokenu zaświadczania.

## <a name="next-steps"></a>Następne kroki
- [Tworzenie i podpisywanie zasad zaświadczania](author-sign-policy.md)
- [Konfigurowanie zaświadczania platformy Azure przy użyciu programu PowerShell](quickstart-powershell.md)
