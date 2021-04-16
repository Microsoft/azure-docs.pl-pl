---
title: Zabezpieczenia aktualizacji urządzenia dla Azure IoT Hub | Microsoft Docs
description: Dowiedz się, w jaki sposób aktualizacja IoT Hub zapewnia bezpieczne aktualizowanie urządzeń.
author: lichris
ms.author: lichris
ms.date: 4/15/2021
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: b10049e03e26cfe8da2bd57cc9f69dd933af706b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567302"
---
# <a name="device-update-security-model"></a>Model zabezpieczeń aktualizacji urządzeń

Aktualizacja urządzenia dla IoT Hub oferuje bezpieczną metodę wdrażania aktualizacji oprogramowania układowego urządzenia, obrazów i aplikacji na urządzeniach IoT. Przepływ pracy zapewnia pełny bezpieczny kanał z pełnym modelem łańcucha zabezpieczeń, za pomocą których urządzenie może udowodnić, że aktualizacja jest zaufana, niezmodyfikowana i zamierzona.

Każdy krok przepływu pracy aktualizacji urządzenia jest chroniony za pomocą różnych funkcji zabezpieczeń i procesów, aby upewnić się, że każdy krok w potoku wykonuje bezpieczne odsuwu do następnego. Klient aktualizacji urządzenia identyfikuje i prawidłowo zarządza wszystkimi żądaniami aktualizacji. Klient sprawdza również każde pobranie, aby upewnić się, że zawartość jest zaufana, niezmodyfikowana i zamierzona.

## <a name="for-solution-operators"></a>Dla operatorów rozwiązań

Gdy operatorzy rozwiązań importują aktualizacje do swojego wystąpienia aktualizacji urządzenia, usługa przesyła i sprawdza pliki binarne aktualizacji, aby upewnić się, że nie zostały one zmodyfikowane ani zamienione przez złośliwego użytkownika. Po zweryfikowaniu usługa aktualizacji urządzeń generuje manifest aktualizacji [wewnętrznej](./update-manifest.md) z skrótami plików z manifestu importu i innych metadanych. Manifest aktualizacji jest następnie podpisany przez usługę Aktualizacji urządzeń.

Po pozyskaniu do usługi i zapisaniu na platformie Azure pliki binarne aktualizacji i skojarzone metadane klienta są automatycznie szyfrowane podczas magazynowania przez usługę Azure Storage. Usługa aktualizacji urządzeń nie zapewnia automatycznego dodatkowego szyfrowania, ale umożliwia deweloperom samo szyfrowanie zawartości, zanim zawartość dotrze do usługi aktualizacji urządzeń.

Gdy operator rozwiązania zażąda aktualizacji urządzenia, podpisany komunikat jest wysyłany za pośrednictwem chronionego IoT Hub kanału do urządzenia. Podpis żądania jest weryfikowany przez agenta aktualizacji urządzenia jako autentyczny. 

Wszelkie wynikowe pobieranie danych binarnych jest zabezpieczane przez weryfikację podpisu manifestu aktualizacji. Manifest aktualizacji zawiera skróty plików binarnych, więc gdy manifest jest zaufany, agent usługi Device Update ufa skrótom i dopasowuje je do plików binarnych. Po pobraniu i zweryfikowaniu pliku binarnego aktualizacji zostanie on bezpiecznie pobrany do instalatora na urządzeniu.

## <a name="for-device-builders"></a>Dla konstruktorów urządzeń

Aby upewnić się, że usługa Device Update Service jest skalowana w dół do prostych urządzeń o niskiej wydajności, model zabezpieczeń używa nieprzetworzonych kluczy asymetrycznych i nieprzetworzonych podpisów. Używają one formatów opartych na formacie JSON, takich jak tokeny internetowe JSON, & JSON Web Keys.

### <a name="securing-update-content-via-the-update-manifest"></a>Zabezpieczanie zawartości aktualizacji za pośrednictwem manifestu aktualizacji

Manifest aktualizacji jest weryfikowany przy użyciu dwóch sygnatur. Podpisy są tworzone przy użyciu struktury składającej się z *kluczy podpisywania* i *kluczy* głównych.

Agent aktualizacji urządzenia ma osadzone klucze publiczne, które są używane dla wszystkich urządzeń zgodnych z aktualizacją urządzenia. Są to *klucze* główne. Odpowiednie klucze prywatne są kontrolowane przez firmę Microsoft.

Firma Microsoft generuje również parę kluczy publiczny/prywatny, która nie jest uwzględniona w agencie aktualizacji urządzenia ani nie jest przechowywana na urządzeniu. Jest to klucz *podpisywania.*

Gdy aktualizacja jest importowana do usługi Device Update for IoT Hub, a manifest aktualizacji jest generowany przez usługę, usługa podpisuje manifest przy użyciu klucza podpisywania i zawiera sam klucz podpisywania, który jest podpisany przez klucz główny. Po wysłaniu manifestu aktualizacji do urządzenia agent aktualizacji urządzenia odbiera następujące dane podpisu:

1. Sama wartość sygnatury.
2. Algorytm używany do generowania #1.
3. Informacje o kluczu publicznym klucza podpisywania używane do generowania #1.
4. Podpis publicznego klucza podpisywania w #3.
5. Identyfikator klucza publicznego klucza głównego używany do generowania #3.
6. Algorytm używany do generowania #4.

Agent aktualizacji urządzenia korzysta z informacji zdefiniowanych powyżej, aby zweryfikować, czy podpis publicznego klucza podpisywania jest podpisany przez klucz główny. Następnie agent aktualizacji urządzeń weryfikuje, czy podpis manifestu aktualizacji jest podpisany przez klucz podpisywania. Jeśli wszystkie podpisy są poprawne, manifest aktualizacji jest zaufany przez agenta aktualizacji urządzeń. Ponieważ manifest aktualizacji zawiera skróty plików, które odpowiadają samym plikom aktualizacji, pliki aktualizacji mogą być również zaufane, jeśli skróty są zgodne.

Posiadanie klucza głównego i klucza podpisywania umożliwia firmie Microsoft okresowe wycofywanie klucza podpisywania, co jest najlepszym rozwiązaniem w zakresie zabezpieczeń.

### <a name="json-web-signature-jws"></a>JSON Web Signature (JWS)

Służy do zapewnienia, że informacje zawarte w programie `updateManifestSignature` `updateManifest` nie zostały naruszone. Kod `updateManifestSignature` jest wytwarzanych przy użyciu sygnatury internetowej JSON z kluczami sieci Web JSON, co umożliwia weryfikację źródła. Sygnatura jest ciągiem zakodowanym w formacie Base64Url z trzema sekcjami rozsyłanym literą ".".  Zapoznaj się z [metodami jws_util.h](https://github.com/Azure/iot-hub-device-update/tree/main/src/utils/jws_utils) w celu analizowania i weryfikowania kluczy i tokenów JSON.

JSON Web Signature to powszechnie używany proponowany standard [IETF](https://tools.ietf.org/html/rfc7515) do podpisywania zawartości przy użyciu struktur danych opartych na formacie JSON. Jest to sposób zapewnienia integralności danych przez zweryfikowanie ich podpisu. Więcej informacji można znaleźć w dokumencie [RFC 7515](https://www.rfc-editor.org/info/rfc7515)JSON Web Signature (JWS).

### <a name="json-web-token"></a>JSON Web Token

Tokeny internetowe JSON to otwarta, standardowa w [branży](https://tools.ietf.org/html/rfc7519) metoda bezpiecznego reprezentowania oświadczeń między dwiema stronami.

### <a name="root-keys"></a>Klucze główne

Każde urządzenie aktualizacji urządzenia zawiera zestaw kluczy głównych. Te klucze są głównymi kluczami zaufania dla wszystkich sygnatur aktualizacji urządzeń. Każdy podpis musi być w łańcuchu za pośrednictwem jednego z tych kluczy głównych, aby można go było uznać za legalny.

Zestaw kluczy głównych zmienia się wraz z czasem, ponieważ należy okresowo obracać klucze podpisywania ze względów bezpieczeństwa. W związku z tym oprogramowanie agenta aktualizacji urządzenia będzie musiało zaktualizować się przy użyciu najnowszego zestawu kluczy głównych. 

### <a name="signatures"></a>Podpisy

Wszystkie podpisy będą uwzględniane przez klucz podpisywania (publiczny) podpisany przez jeden z kluczy głównych. Podpis zidentyfikuje klucz główny użyty do podpisania klucza podpisywania. 

Agent aktualizacji urządzenia musi zweryfikować podpisy, najpierw weryfikując, czy podpis klucza podpisywania (publicznego) jest prawidłowy, prawidłowy i podpisany przez jeden z zatwierdzonych kluczy głównych. Po pomyślnym weryfikacji klucza podpisywania sam podpis może zostać zweryfikowany przy użyciu zaufanego klucza publicznego podpisywania.

Klucze podpisywania są obracane znacznie szybciej niż klucze główne, więc należy oczekiwać komunikatów podpisanych przez różne klucze podpisywania. 

Odwołanie klucza podpisywania jest zarządzane przez usługę Aktualizacji urządzeń, dlatego użytkownicy nie powinni próbować buforować kluczy podpisywania. Zawsze używaj klucza podpisywania towarzyszącego podpisowi.

### <a name="receiving-updates"></a>Otrzymywanie aktualizacji

Żądania aktualizacji odebrane przez agenta aktualizacji urządzenia będą zawierać podpisany dokument manifestu aktualizacji (UM). Agent musi sprawdzić, czy podpis UM jest prawidłowy i nienaruszony. Jest to wykonywane przez weryfikację, czy klucz podpisywania podpisu UM został podpisany przez odpowiedni klucz główny. Po zakończeniu agent weryfikuje podpis UM względem klucza podpisywania.

Po weryfikacji podpisu UM agent aktualizacji urządzenia może mu ufać jako "źródło prawdy". Całe dalsze zaufanie do zabezpieczeń wynika z tego źródła. 

Pakiet UM zawiera adresy URL i skróty plików zawartości do pobrania i zainstalowania. Po pobraniu pliku binarnego aktualizacji agent musi zweryfikować aktualizację względem wartości skrótu pliku znalezionego w UM. Zapewnia to przechyły model zaufania do weryfikacji pobierania. Zapewnia nie tylko, że zawartość jest nienaruszona (nie jest modyfikowana), ale również potwierdza, że to, co zostało pobrane, rzeczywiście było tym, co miało zostać pobrane. 

### <a name="securing-the-device"></a>Zabezpieczanie urządzenia

Należy upewnić się, że zasoby zabezpieczeń związane z aktualizacją urządzenia są prawidłowo zabezpieczone i chronione na urządzeniu. Zasoby, takie jak klucze główne, muszą być chronione przed modyfikacjami. Istnieją różne sposoby, aby to zrobić, na przykład przy użyciu urządzeń zabezpieczeń (TPM, SGX, HSM, innych urządzeń zabezpieczeń), a nawet kodowania ich w agencie aktualizacji urządzeń. Ta ostatnia wymaga podpisu cyfrowego kodu agenta aktualizacji urządzenia i zapewnienia obsługi integralności kodu systemu w celu ochrony przed złośliwymi modyfikacjami kodu agenta.

Mogą być uzasadnione dodatkowe środki bezpieczeństwa, takie jak zapewnienie bezpiecznego sposobu bezpiecznego przetwarzania danych między składnikami. Na przykład zarejestrowanie określonego izolowanego konta do uruchamiania różnych składników. Ograniczenie komunikacji sieciowej (np. wywołań interfejsu API REST) tylko do hosta lokalnego.

**[Następny krok: Dowiedz się więcej na temat sposobu, w jaki usługa Device Update korzysta z kontroli RBAC platformy Azure](.\device-update-control-access.md)**