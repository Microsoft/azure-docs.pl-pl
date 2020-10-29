---
title: Zestawy roszczeń zaświadczania platformy Azure
description: Zestawy roszczeń dotyczące zaświadczania platformy Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e17002534d35f477467f0c35833560a0267dd596
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909781"
---
# <a name="claim-sets"></a>Zestawy oświadczeń

Oświadczenia wygenerowane w procesie zaświadczania enclaves za pomocą zaświadczania Microsoft Azure mogą być podzielone na następujące kategorie:

- **Oświadczenia przychodzące** : oświadczenia wygenerowane przez Microsoft Azure zaświadczania po przeanalizowaniu dowodu zaświadczania.

- **Oświadczenia wychodzące** : oświadczenia utworzone jako dane wyjściowe przez zaświadczanie platformy Azure. Zawiera wszystkie oświadczenia, które powinny kończyć się tokenem zaświadczania.

- **Oświadczenia właściwości** : oświadczenia utworzone jako dane wyjściowe przez zaświadczanie platformy Azure. Zawiera wszystkie oświadczenia reprezentujące właściwości tokenu zaświadczania, takie jak kodowanie raportu, czas trwania okresu ważności raportu itd.

Poniżej oświadczeń, które są zdefiniowane przez RFC dla tokenu JWT i używane przez zaświadczanie platformy Azure w obiekcie Response:

- **"ISS" (wystawca)** : "ISS" (wystawca) identyfikuje podmiot zabezpieczeń, który wystawił token JWT. Przetwarzanie tego żądania jest zwykle specyficzne dla aplikacji. Wartość "ISS" jest ciągiem z uwzględnieniem wielkości liter, zawierającym wartość StringOrURI.
- **"IAT" (wystawiony w)** : "IAT" (wystawiony w), określa czas, w którym został wystawiony token JWT. Tego żądania można użyć do określenia wieku tokenu JWT. Wartość musi być liczbą zawierającą wartość NumericDate.
- **"EXP" (czas wygaśnięcia)** wartość żądania: "EXP" (czas wygaśnięcia) określa czas wygaśnięcia w dniu lub, po upływie którego nie można zaakceptować tokenu JWT do przetworzenia. Przetwarzanie roszczeń "EXP" wymaga, aby bieżąca data/godzina była wcześniejsza niż data/godzina wygaśnięcia określona w postawce "EXP".

  Uwaga: 5-minutowy Leeway jest dodawany do czasu problemu (IAT), aby obsłużyć pochylenie zegara.
- **"NBF" (nie wcześniej)** — wartość "NBF" (nie wcześniej) określa czas, po którym token JWT nie zostanie zaakceptowany do przetwarzania. Przetwarzanie roszczeń "NBF" wymaga, aby bieżąca data/godzina była późniejsza lub równa wartości daty/godziny niewymienionej w polu "NBF".
  Uwaga: 5-minutowy Leeway jest dodawany do czasu problemu (IAT), aby obsłużyć pochylenie zegara.

## <a name="claims-issued-by-azure-attestation-in-sgx-enclaves"></a>Oświadczenia wystawione przez zaświadczanie o platformie Azure w programie SGX enclaves

### <a name="incoming-claims"></a>Oświadczenia przychodzące 

- **$is-możliwością debugowania** : wartość logiczna, która wskazuje, czy enklawy ma włączone debugowanie
- **$SGX-mrsigner** : zakodowana wartość szesnastkowa pola "mrsigner" w ofercie
- **$SGX-mrenclave** : zakodowana wartość szesnastkowa pola "mrenclave" w ofercie
- **Identyfikator $product**
- **$SVN** : numer wersji zabezpieczeń zakodowany w ofercie 
- **$tee** : typ elementu enklawy 

### <a name="outgoing-claims"></a>Oświadczenia wychodzące

- **is-możliwością debugowania** : wartość logiczna, która wskazuje, czy enklawy ma włączone debugowanie
- **SGX-mrsigner** : zakodowana wartość szesnastkowa pola "mrsigner" w ofercie
- **SGX-mrenclave** : zakodowana wartość szesnastkowa pola "mrenclave" w ofercie
- **Identyfikator produktu**
- **SVN** : numer wersji zabezpieczeń zakodowany w ofercie 
- **tee** : typ enklawy 
- **Maa-EHD** : Base64Url zakodowana wersja "enklawy dane przechowywane" określone w żądaniu zaświadczania 
- **AAS-EHD** : Base64Url zakodowana wersja "enklawy przechowywanych danych" określona w żądaniu zaświadczania 

## <a name="claims-issued-by-azure-attestation-in-vbs-enclaves"></a>Oświadczenia wystawione przez zaświadczanie o platformie Azure w VBS enclaves

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>Oświadczenia przychodzące (mogą być również używane jako oświadczenia wychodzące)

- **aikValidated** : wartość logiczna zawierająca informacje o tym, czy certyfikat klucza zaświadczania tożsamości (AIK) został zweryfikowany.
- **aikPubHash** : ciąg zawierający algorytm Base64 (SHA256 (klucz publiczny AIK w formacie der)).
- **tpmVersion** : wartość całkowita zawierająca wersję główną moduł TPM (TPM).
- **secureBootEnabled** : wartość logiczna wskazująca, czy bezpieczny rozruch jest włączony.
- **iommuEnabled** : wartość logiczna wskazująca, czy jest włączona jednostka zarządzania pamięcią podwyjściową (jednostki IOMMU).
- **bootDebuggingDisabled** : wartość logiczna wskazująca, czy debugowanie rozruchowe jest wyłączone.
- **notSafeMode** : wartość logiczna określająca, czy system Windows nie działa w trybie awaryjnym.
- **notWinPE** : wartość logiczna wskazująca, czy system Windows nie działa w trybie WinPE.
- **vbsEnabled** : wartość logiczna wskazująca, czy funkcja vbs jest włączona.
- **vbsReportPresent** : wartość logiczna wskazująca, czy raport vbs enklawy jest dostępny.
- **enclaveAuthorId** : wartość ciągu zawierająca Base64Url wartość zakodowaną identyfikatora autora enklawy — identyfikator autora modułu podstawowego dla enklawy.
- **enclaveImageId** : wartość ciągu zawierająca Base64Url wartość zakodowaną w identyfikatorze obrazu enklawy — Identyfikator obrazu modułu podstawowego dla enklawy.
- **enclaveOwnerId** : wartość ciągu zawierająca Base64Url wartość zakodowaną identyfikatora właściciela enklawy — identyfikator właściciela dla enklawy.
- **enclaveFamilyId** : wartość ciągu zawierająca Base64Url wartość zakodowaną w identyfikatorze rodziny enklawy. Identyfikator rodziny modułu podstawowego dla enklawy.
- **enclaveSvn** : wartość całkowita zawierająca numer wersji zabezpieczeń modułu podstawowego dla enklawy.
- **enclavePlatformSvn** : wartość całkowita zawierająca numer wersji zabezpieczeń platformy obsługującej enklawy.
- **enclaveFlags** : enclaveFlags jest wartością całkowitą zawierającą flagi opisujące zasady czasu wykonywania dla enklawy.
  
### <a name="outgoing-claims"></a>Oświadczenia wychodzące

- **policy_hash** : wartość ciągu zawierająca Skrót SHA256 dla tekstu zasad obliczanego przez BASE64URL (SHA256 (BASE64URL ())).
- **policy_signer** : zawiera JWK z kluczem publicznym lub łańcuch certyfikatów obecny w podpisanym nagłówku zasad.
- **Ver (wersja)** : wartość ciągu zawierająca wersję raportu. Obecnie 1,0.
- **CNF (potwierdzenie)** : twierdzenie "CNF" służy do identyfikowania klucza będącego dowodem posiadania. Twierdzenie potwierdzające zgodnie z definicją w dokumencie RFC 7800 zawiera publiczną część zaświadczonego klucza enklawy reprezentowanego jako obiekt klucza internetowego JSON (JWK) (RFC 7517).
- **rp_data (dane jednostki uzależnionej)** : dane jednostki uzależnionej (jeśli istnieją) określone w żądaniu, używane przez jednostkę uzależnioną jako identyfikator jednorazowy w celu zagwarantowania Aktualności raportu.
- **"JTI" (identyfikator JWT)** : wartość żądania "JTI" (identyfikator JWT) zapewnia unikatowy identyfikator dla tokenu JWT. Wartość identyfikatora jest przypisywana w sposób, który gwarantuje, że istnieje niewielkie prawdopodobieństwo, że ta sama wartość zostanie przypadkowo przypisana do innego obiektu danych.

### <a name="property-claims"></a>Oświadczenia właściwości

- **report_validity_in_minutes** : wartość całkowita określająca czas ważności tokenu.
  - **Wartość domyślna (Time)** : jeden dzień w minutach.
  - **Wartość maksymalna (Time)** : jeden rok w minutach.
- **omit_x5c** : wartość logiczna określająca, czy zaświadczanie platformy Azure powinno pominąć certyfikat używany do zapewnienia potwierdzenia autentyczności usługi. Jeśli wartość jest równa true, x5t zostanie dodana do tokenu zaświadczania. Jeśli wartość jest równa false (domyślnie), x5c zostanie dodana do tokenu zaświadczania.

## <a name="next-steps"></a>Następne kroki
- [Tworzenie i podpisywanie zasad zaświadczania](author-sign-policy.md)
- [Konfigurowanie zaświadczania platformy Azure przy użyciu programu PowerShell](quickstart-powershell.md)
