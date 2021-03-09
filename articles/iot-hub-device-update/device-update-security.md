---
title: Zabezpieczenia dla aktualizacji urządzenia dla platformy Azure IoT Hub | Microsoft Docs
description: Zapoznaj się z tematem, jak aktualizacja urządzenia IoT Hub zapewnia bezpieczne aktualizowanie urządzeń.
author: lichris
ms.author: lichris
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: 86b2dbe6a28d1440f93788eb40e133d9b62d3f0c
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489433"
---
# <a name="device-update-security-model"></a>Model zabezpieczeń aktualizacji urządzenia

Aktualizacja urządzenia dla IoT Hub oferuje bezpieczną metodę wdrażania aktualizacji dla oprogramowania układowego, obrazów i aplikacji urządzenia na urządzeniach IoT. Przepływ pracy zapewnia kompleksowy bezpieczny kanał z pełnym modelem, który może być używany przez urządzenie w celu potwierdzenia, że aktualizacja jest zaufana, niemodyfikowana i zamierzona.

Każdy krok w przepływie pracy aktualizacji urządzenia jest chroniony przez różne funkcje zabezpieczeń i procesy, aby upewnić się, że każdy krok w potoku wykonuje bezpieczne przekazanie do następnego. Klient aktualizacji urządzenia identyfikuje i prawidłowo zarządza wszelkimi żądaniami aktualizacji illegitimate. Klient sprawdza również każdy pobrany z nich, aby upewnić się, że zawartość jest zaufana, niezmodyfikowana i jest zacelowa.

## <a name="for-solution-operators"></a>Dla operatorów rozwiązania

Jako operatorzy rozwiązań importują aktualizacje do ich wystąpienia aktualizacji urządzeń, usługa przekazuje i sprawdza pliki binarne aktualizacji, aby upewnić się, że nie zostały one zmodyfikowane ani zamienione przez złośliwego użytkownika. Po zweryfikowaniu Usługa aktualizacji urządzenia generuje wewnętrzny [manifest aktualizacji](./update-manifest.md) z użyciem skrótów plików z manifestu importu i innych metadanych. Ten manifest aktualizacji jest następnie podpisywany przez usługę aktualizacji urządzenia.

Gdy operator rozwiązania żąda zaktualizowania urządzenia, podpisana wiadomość jest wysyłana za pośrednictwem chronionego kanału IoT Hub do urządzenia. Podpis żądania jest weryfikowany przez agenta aktualizacji urządzenia urządzenia jako autentyczny. 

Wszystkie powstające pobieranie plików binarnych są chronione za pomocą walidacji sygnatury manifestu aktualizacji. Manifest aktualizacji zawiera skróty plików binarnych, więc gdy manifest jest zaufany, Agent aktualizacji urządzenia ufa skrótom i dopasowuje je do plików binarnych. Po pobraniu i zweryfikowaniu pliku binarnego aktualizacji zostanie on przekazany do Instalatora na urządzeniu.

## <a name="for-device-builders"></a>Dla konstruktorów urządzeń

Aby upewnić się, że usługa aktualizacji urządzenia jest skalowana w dół do prostych urządzeń o niskiej wydajności, model zabezpieczeń używa nieprzetworzonych kluczy asymetrycznych i pierwotnych sygnatur. Korzystają one z formatów opartych na notacji JSON, takich jak tokeny sieci Web JSON & klucze sieci Web JSON.

### <a name="securing-update-content-via-the-update-manifest"></a>Zabezpieczanie aktualizacji zawartości za pomocą manifestu aktualizacji

Manifest aktualizacji jest weryfikowany przy użyciu dwóch sygnatur. Sygnatury są tworzone przy użyciu struktury składającej się z kluczy *podpisywania* i kluczy *głównych* .

Agent aktualizacji urządzeń ma osadzone klucze publiczne, które są używane dla wszystkich urządzeń zgodnych z aktualizacją urządzeń. Są to klucze *Główne* . Odpowiednie klucze prywatne są kontrolowane przez firmę Microsoft.

Firma Microsoft generuje również parę kluczy publicznych/prywatnych, która nie jest uwzględniona w agencie aktualizacji urządzeń ani nie jest przechowywana na urządzeniu. Jest to klucz *podpisywania* .

Gdy aktualizacja zostanie zaimportowana do aktualizacji urządzenia dla IoT Hub, a manifest aktualizacji jest generowany przez usługę, usługa podpisze manifest za pomocą klucza podpisywania i zawiera sam klucz podpisywania, który jest podpisany przez klucz główny. Po wysłaniu do urządzenia manifestu aktualizacji Agent aktualizacji urządzenia otrzymuje następujące dane podpisu:

1. Sama wartość podpisu.
2. Algorytm używany do generowania #1.
3. Informacje o kluczu publicznym klucza podpisywania używanego do generowania #1.
4. Podpis publicznego klucza podpisywania w #3.
5. Identyfikator klucza publicznego klucza głównego używanego do generowania #3.
6. Algorytm używany do generowania #4.

Agent aktualizacji urządzeń korzysta z informacji określonych powyżej, aby sprawdzić, czy podpis publicznego klucza podpisywania jest podpisany przez klucz główny. Agent aktualizacji urządzenia sprawdza, czy podpis manifestu aktualizacji jest podpisany przez klucz podpisywania. Jeśli wszystkie podpisy są poprawne, manifest aktualizacji jest traktowany jako zaufany przez agenta aktualizacji urządzenia. Ponieważ manifest aktualizacji zawiera skróty plików, które odpowiadają samym plikom aktualizacji, pliki aktualizacji mogą być również zaufane, jeśli skróty są zgodne.

Posiadanie kluczy głównych i podpisywania pozwala firmie Microsoft na okresowe wycofywanie klucza podpisywania, najlepszym rozwiązaniem w zakresie zabezpieczeń.

### <a name="json-web-signature-jws"></a>Podpis internetowy JSON (JWS)

`updateManifestSignature`Służy do upewnienia się, że informacje zawarte w programie `updateManifest` nie zostały naruszone. `updateManifestSignature`Jest tworzony przy użyciu podpisu internetowego JSON z kluczami sieci Web JSON, umożliwiając weryfikację źródła. Sygnatura jest zakodowanym ciągiem Base64Url z trzema sekcjami w postaci ".".  Zapoznaj się z [metodami pomocnika jws_util. h](https://github.com/Azure/iot-hub-device-update/tree/main/src/utils/jws_utils) na potrzeby analizowania i weryfikowania kluczy i tokenów JSON.

Podpis internetowy JSON to powszechnie używany [proponowany Standard IETF](https://tools.ietf.org/html/rfc7515) do podpisywania zawartości przy użyciu struktur danych opartych na notacji JSON. Jest to sposób zapewnienia integralności danych przez zweryfikowanie podpisu danych. Więcej informacji można znaleźć w [dokumencie 7515 RFC](https://www.rfc-editor.org/info/rfc7515)w sieci Web JWS (JSON).

### <a name="json-web-token"></a>Token sieci Web JSON

Tokeny sieci Web JSON to otwarta, [branżowa Metoda](https://tools.ietf.org/html/rfc7519) służąca do bezpiecznego reprezentowania oświadczeń między dwiema stronami.

### <a name="root-keys"></a>Klucze główne

Każde urządzenie aktualizacji urządzenia zawiera zestaw kluczy głównych. Klucze te są głównym zaufaniem dla wszystkich podpisów aktualizacji urządzenia. Wszystkie podpisy muszą być połączone za poorednictwem jednego z tych kluczy głównych, aby można je było uznać za wiarygodne.

Zestaw kluczy głównych zmieni się w miarę upływu czasu, ponieważ jest to odpowiednie do okresowego obracania kluczy podpisywania ze względów bezpieczeństwa. W związku z tym oprogramowanie agenta aktualizacji urządzenia musi sama zaktualizować się przy użyciu najnowszego zestawu kluczy głównych. 

### <a name="signatures"></a>Podpisy

Wszystkie podpisy będą uwzględniane za pomocą klucza podpisywania (publicznego) podpisanego przez jeden z kluczy głównych. Podpis zidentyfikuje klucz główny, który został użyty do podpisania klucza podpisywania. 

Agent aktualizacji urządzenia musi sprawdzić poprawność podpisów, sprawdzając najpierw, czy podpis klucza podpisywania (publicznego) jest poprawny, prawidłowy i podpisany przez jeden z zatwierdzonych kluczy głównych. Po pomyślnym zweryfikowaniu klucza podpisywania można sprawdzić poprawność podpisu za pomocą teraz zaufanego klucza publicznego podpisywania.

Klucze podpisywania są obracane na wiele szybszych erze niż klucze główne, dlatego należy oczekiwać, że komunikaty są podpisane przez różne różne klucze podpisywania. 

Odwoływanie klucza podpisywania jest zarządzane przez usługę aktualizacji urządzeń, dlatego użytkownicy nie powinni próbować podpisywać kluczy podpisywania. Zawsze używaj klucza podpisywania towarzyszącego sygnaturze.

### <a name="receiving-updates"></a>Otrzymywanie aktualizacji

Żądania aktualizacji odebrane przez agenta aktualizacji urządzenia będą zawierać podpisany dokument manifestu aktualizacji (UM). Agent musi sprawdzić, czy sygnatura UM jest właściwa i nienaruszona. W tym celu należy sprawdzić, czy klucz podpisywania podpisu UM został podpisany przez właściwy klucz główny. Po wykonaniu tej czynności Agent sprawdza poprawność podpisu UM przed kluczem podpisywania.

Po sprawdzeniu poprawności podpisu programu UM Agent aktualizacji urządzenia może go zaufać jako "Źródło prawdy". Wszystkie inne relacje zaufania zabezpieczeń pochodzą z tego źródła. 

Program UM zawiera adresy URL i skróty zawartości do pobrania i zainstalowania. Po pobraniu przez agenta pliku binarnego aktualizacji musi on sprawdzić poprawność aktualizacji do wartości skrótu plików znalezionej w UM. Zapewnia to przechodni model zaufania na potrzeby walidacji pobierania. Nie tylko gwarantuje, że zawartość jest nienaruszona (nie została zmodyfikowana), ale również potwierdza, że pobrane elementy były rzeczywiście przeznaczone do pobrania. 

### <a name="securing-the-device"></a>Zabezpieczanie urządzenia

Ważne jest, aby upewnić się, że zasoby zabezpieczeń dotyczące aktualizacji urządzeń są prawidłowo zabezpieczone i chronione na urządzeniu. Zasoby, takie jak klucze główne, muszą być chronione przed modyfikacją. Istnieją różne sposoby wykonywania takich czynności, jak używanie urządzeń zabezpieczeń (TPM, SGX, HSM, inne urządzenia zabezpieczające), a nawet ich twarde kodowanie w agencie aktualizacji urządzeń. Drugie wymaga, aby kod agenta aktualizacji urządzenia został podpisany cyfrowo, a obsługa integralności kodu systemu została włączona w celu ochrony przed złośliwymi modyfikacjami kodu agenta.

Mogą być uzasadnione dodatkowe środki bezpieczeństwa, takie jak upewnienie się, że przekazanie ze składnika do składnika jest wykonywane w sposób bezpieczny. Na przykład rejestrowanie określonego konta izolowanego w celu uruchomienia różnych składników programu. I ograniczanie komunikacji sieciowej (np. wywołań interfejsu API REST) tylko do hosta lokalnego.

**[Następny krok: Dowiedz się więcej o tym, jak aktualizacja urządzenia używa platformy Azure RBAC](.\device-update-control-access.md)**