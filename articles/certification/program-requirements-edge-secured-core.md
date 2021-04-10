---
title: Wymagania certyfikacji z certyfikatem Secure-Core
description: Wymagania programu certyfikacji z certyfikatem Secure-Core
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Secured-core Certification Requirements
ms.service: certification
ms.openlocfilehash: 5bb02f939bb63fd1c6365fd4570996f09119e958
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166908"
---
# <a name="azure-certified-device---edge-secured-core-preview"></a>Certyfikowane urządzenia z systemem Azure — Zabezpieczenia podstawowe (wersja zapoznawcza) #

## <a name="edge-secured-core-certification-requirements"></a>Wymagania dotyczące certyfikacji Secured-Core Edge ##

Ten dokument zawiera informacje o możliwościach i wymaganiach dotyczących urządzenia, które zostaną spełnione, aby można było ukończyć certyfikat i wyświetlić listę urządzeń w katalogu usługi Azure IoT za pomocą etykiety "zabezpieczenia w sieci".

### <a name="program-purpose"></a>Cel programu ###
Usługa Edge Secure-Core to przyrostowa Certyfikacja w programie Device Certified dla urządzeń IoT z pełnymi systemami operacyjnymi, takimi jak Linux lub Windows 10 IoT. ten program umożliwia partnerom urządzeń rozróżnianie swoich urządzeń przez spełnienie dodatkowego zestawu kryteriów zabezpieczeń. Urządzenia spełniające te kryteria włączają te niesie obietnice zwiększenia:
1. Tożsamość urządzenia oparta na sprzęcie 
2. Możliwość wymuszania integralności systemu 
3. Są aktualne i zarządzane zdalnie
4. Zapewnia ochronę danych w czasie spoczynku
5. Zapewnia ochronę danych w trakcie tranzytu
6. Wbudowane Agent zabezpieczeń i Ograniczanie funkcjonalności
### <a name="requirements"></a>Wymagania ###

---
|Nazwa|SecuredCore. wbudowane. Security|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem testu jest upewnienie się, że urządzenia mogą raportować informacje o zabezpieczeniach i zdarzenia przez wysyłanie danych do usługi Azure Defender dla IoT.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja |Urządzenie musi generować dzienniki zabezpieczeń i alerty. Dzienniki urządzeń i alerty do Azure Security Center.<ol><li>Pobieranie i wdrażanie agenta zabezpieczeń z usługi GitHub</li><li>Sprawdź poprawność komunikatu o alercie z usługi Azure Defender dla IoT.</li></ol>|
|Zasoby|[Azure docs IoT Defender dla IoT](../defender-for-iot/how-to-configure-agent-based-solution.md)|

---
|Nazwa|SecuredCore. Encryption. Storage|
|:---|:---|
|Stan|Wymagane|
|Opis|Przeznaczenie testu do zweryfikowania, czy poufne dane mogą być szyfrowane w magazynie nietrwałym.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja|Urządzenie do zweryfikowania za pomocą zestawu narzędzi, aby zapewnić włączenie szyfrowania magazynu, a algorytm domyślny to XTS-AES z długością klucza 128 bitów lub wyższą.|
|Zasoby||

---
|Nazwa|SecuredCore. Hardware. SecureEnclave|
|:---|:---|
|Stan|Opcjonalne|
|Opis|Przeznaczenie testu do zweryfikowania istnienia bezpiecznego enklawy oraz, że enklawy jest dostępny z bezpiecznego agenta.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja|Urządzenie do zweryfikowania za pomocą zestawu narzędzi, aby zapewnić, że Agent zabezpieczeń platformy Azure może komunikować się z bezpiecznym enklawy|
|Zasoby|https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md|

---
|Nazwa|SecuredCore. Hardware. Identity|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem testu jest zweryfikowanie, czy zidentyfikowanie urządzenia jest umieszczone w obszarze Sprzęt.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja|Urządzenie, które ma zostać zweryfikowane za pośrednictwem zestawu narzędzi, aby upewnić się, że urządzenie ma obecny moduł TPM i że może być obsługiwane za pośrednictwem IoT Hub przy użyciu klucza poręczenia modułu TPM.|
|Zasoby|[Skonfiguruj funkcję samoobsługowego udostępniania przy użyciu programu DPS](../iot-dps/quick-setup-auto-provision.md)|

---
|Nazwa|SecuredCore. Update|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem testu jest sprawdzenie, czy urządzenie może odbierać i aktualizować oprogramowanie układowe i oprogramowanie.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja|Potwierdzenie partnera, że mogły wysłać aktualizację do urządzenia za pomocą usługi Microsoft Update, aktualizacji urządzenia platformy Azure lub innych zatwierdzonych usług.|
|Zasoby|[Aktualizacja urządzenia dla IoT Hub](../iot-hub-device-update/index.yml)|

---
|Nazwa|SecuredCore.Manageability.Configwersja|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem testu jest zweryfikowanie, czy urządzenia obsługują zdalne zarządzanie zabezpieczeniami.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja|Urządzenie do zweryfikowania za pomocą zestawu narzędzi, aby upewnić się, że urządzenie obsługuje możliwość zdalnego zarządzania i konfiguracji zabezpieczeń. I stan jest raportowany z powrotem do IoT Hub/Azure Defender for IoT.|
|Zasoby||

---
|Nazwa|SecuredCore. manage. Reset|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem tego testu jest zweryfikowanie urządzenia w dwóch przypadkach użycia: a) możliwość wykonania resetowania (usuwania danych użytkownika, usunięcia konfiguracji użytkowników), b) przywrócenia urządzenia do ostatniego znanego dobrego w przypadku aktualizacji powodującej problemy.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja|Urządzenie do zweryfikowania za pomocą kombinacji zestawu narzędzi i przesłanej dokumentacji, która obsługuje tę funkcję. Producent urządzenia może określić, czy zaimplementować te funkcje do obsługi resetowania zdalnego, czy tylko resetowania lokalnego.|
|Zasoby||

---
|Nazwa|SecuredCore. Updates. Duration|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem tych zasad jest upewnienie się, że urządzenie pozostanie bezpieczne.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręcznie|
|Walidacja|Zobowiązanie od złożenia certyfikowanych urządzeń będzie wymagało Aktualności urządzeń przez 60 miesięcy od daty przesłania. Specyfikacje dostępne dla kupującego i urządzeń w jakiś sposób powinny wskazywać czas trwania aktualizacji oprogramowania.|
|Zasoby||

---
|Nazwa|SecuredCore. Policy. vuln. ujawnienie|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem tych zasad jest upewnienie się, że istnieje mechanizm zbierania i dystrybuowania raportów dotyczących luk w zabezpieczeniach produktu.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręcznie|
|Walidacja|Zostanie sprawdzona Dokumentacja procesu przesyłania i otrzymywania raportów dotyczących luk w zabezpieczeniach dla certyfikowanych urządzeń.|
|Zasoby||

---
|Nazwa|SecuredCore. Policy. vuln. fixs|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem tych zasad jest zapewnienie, że luki w zabezpieczeniach, które są wysokie/krytyczne (przy użyciu programu CVSS 3,0), są rozwiązywane w ciągu 180 dni od udostępnienia poprawki.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręcznie|
|Walidacja|Zostanie sprawdzona Dokumentacja procesu przesyłania i otrzymywania raportów dotyczących luk w zabezpieczeniach dla certyfikowanych urządzeń.|
|Zasoby||


---
|Nazwa|SecuredCore. Encryption. TLS|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem testu jest zweryfikowanie obsługi wymaganych wersji protokołu TLS i mechanizmów szyfrowania.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
Walidacja|Urządzenie do zweryfikowania za pomocą zestawu narzędzi, aby upewnić się, że urządzenie obsługuje minimalną wersję protokołu TLS 1,2 i obsługuje następujące wymagane mechanizmy szyfrowania TLS.<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|Zasoby| [Obsługa protokołu TLS w IoT Hub](../iot-hub/iot-hub-tls-support.md) <br /> [Mechanizmy szyfrowania TLS w systemie Windows 10](https://docs.microsoft.com/windows/win32/secauthn/tls-cipher-suites-in-windows-10-v1903) |

---
|Nazwa|SecuredCore. Protection. SignedUpdates|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem testu jest sprawdzenie, czy aktualizacje muszą być podpisane.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja|Urządzenie do zweryfikowania za pomocą zestawu narzędzi, aby mieć pewność, że aktualizacje systemu operacyjnego, sterowników, oprogramowania aplikacji, bibliotek, pakietów i oprogramowania układowego nie będą stosowane, chyba że zostanie prawidłowo podpisany i zweryfikowany.
|Zasoby||

---
|Nazwa|SecuredCore. firmware. SecureBoot|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem testu jest zweryfikowanie integralności rozruchowej urządzenia.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja|Urządzenie do zweryfikowania za pomocą zestawu narzędzi, aby upewnić się, że oprogramowanie układowe i podpisy jądra są sprawdzane przy każdym uruchomieniu urządzenia. <ul><li>Interfejs UEFI: Bezpieczny rozruch jest włączony</li><li>UBoot: zweryfikowano rozruch jest włączony</li></ul>|
|Zasoby||

---
|Nazwa|SecuredCore. Protection. funkcji CodeIntegrity|
|:---|:---|
|Stan|Wymagane|
|Opis|Ten test polega na sprawdzeniu, czy integralność kodu jest dostępna na tym urządzeniu.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja|Urządzenie do zweryfikowania za poorednictwem zestawu narzędzi, aby zapewnić, że integralność kodu jest włączona. </br> System Windows: zasady wymagające WYMUSZONEJ </br> Linux: DM-Verity i IMA|
|Zasoby||

---
|Nazwa|SecuredCore. Protection. NetworkService|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem testu jest sprawdzenie, czy aplikacje akceptujące dane wejściowe z sieci nie działają z podniesionymi uprawnieniami.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja|Urządzenie do zweryfikowania za pomocą zestawu narzędzi, aby upewnić się, że usługi akceptujące połączenia sieciowe nie działają z uprawnieniami SYSTEMowymi i głównymi.|
|Zasoby||

---
|Nazwa|SecuredCore. Protection. Baselines|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem testu jest sprawdzenie, czy system jest zgodny z podstawową konfiguracją zabezpieczeń.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja|Urządzenie do zweryfikowania za za poorednictwem zestawu narzędzi, aby upewnić się, że testy wydajności konfiguracji systemu IOT w usłudze Defender zostały uruchomione.|
|Zasoby| https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines <br> https://www.cisecurity.org/cis-benchmarks/ |

---
|Nazwa|SecuredCore. firmware. Protection|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem testu jest upewnienie się, że urządzenie ma odpowiednie środki zaradcze od zagrożeń bezpieczeństwa oprogramowania układowego.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja|Urządzenie do zweryfikowania za pośrednictwem zestawu narzędzi, aby upewnić się, że jest chronione przed zagrożeniami bezpieczeństwa oprogramowania układowego za pomocą jednego z następujących sposobów: <ul><li>DRTM + tryb zarządzania interfejsem UEFI — środki zaradcze</li><li>DRTM + tryb zarządzania interfejsem UEFI</li><li>Zatwierdzona PD, która robi SRTM + środowisko uruchomieniowe oprogramowania układowego</li></ul> |
|Zasoby| https://trustedcomputinggroup.org/ |

---
|Nazwa|SecuredCore. firmware. zaświadczanie|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem testu jest upewnienie się, że urządzenie może zdalnie zaświadczać usługę zaświadczania Microsoft Azure.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja|Urządzenie do zweryfikowania za pomocą zestawu narzędzi, aby upewnić się, że dzienniki rozruchowe platformy i pomiary aktywności rozruchu można zbierać i zdalnie zaświadczać do usługi zaświadczania Microsoft Azure.|
|Zasoby| [Usługa Microsoft Azure Attestation](../attestation/index.yml) |

---
|Nazwa|SecuredCore. Hardware. MemoryProtection|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem testu jest sprawdzenie, czy dostęp DMA nie jest włączony w portach dostępnych zewnętrznie.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja|Jeśli na urządzeniu istnieją porty zewnętrzne obsługujące dostęp DMA, zestaw narzędzi umożliwia sprawdzenie, czy jednostki IOMMU lub SMMU jest włączona i skonfigurowana dla tych portów.|
|Zasoby||

---
|Nazwa|SecuredCore. Protection. Debug|
|:---|:---|
|Stan|Wymagane|
|Opis|Celem testu jest sprawdzenie, czy funkcja debugowania na urządzeniu jest wyłączona.|
|Docelowa dostępność|2021|
|Dotyczy:|Dowolne urządzenie|
|System operacyjny|Bez znaczenia|
|Typ walidacji|Ręczne/narzędzia|
|Walidacja|Urządzenie do zweryfikowania za poorednictwem zestawu narzędzi, aby zapewnić, że funkcje debugowania wymagają autoryzacji do włączenia.|
|Zasoby||
