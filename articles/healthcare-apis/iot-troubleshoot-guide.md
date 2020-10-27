---
title: Azure IoT Connector for FHIR (wersja zapoznawcza) — Przewodnik rozwiązywania problemów i instrukcje
description: Jak rozwiązywać problemy z typowym łącznikiem usługi Azure IoT for FHIR (wersja zapoznawcza) komunikaty o błędach i warunki i kopiowanie plików mapowania
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 09/16/2020
ms.author: jasteppe
ms.openlocfilehash: 4a1a23ca2d0b30a192c30b331c588d13a2a261a7
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558520"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów z usługą Azure IoT Connector for FHIR (wersja zapoznawcza)

W tym artykule przedstawiono procedurę rozwiązywania problemów z typowym łącznikiem usługi Azure IoT dla FHIR * komunikatów o błędach i warunków.  

Dowiesz się również, jak utworzyć kopie łącznika usługi Azure IoT dla FHIR JSON mapowania konwersji (na przykład: Device i FHIR).  

Za pomocą kopii w formacie JSON mapowania konwersji można edytować i archiwizowania poza Azure Portal.  

> [!TIP]
> W przypadku otwierania biletu pomocy technicznej [platformy Azure](https://azure.microsoft.com/support/create-ticket/) dla łącznika usługi Azure IoT dla FHIR upewnij się, że zostały dołączone kopie JSON mapowania konwersji, aby ułatwić proces rozwiązywania problemów.

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>Walidacja szablonu JSON mapowania urządzeń i FHIR dla łącznika usługi Azure IoT dla FHIR (wersja zapoznawcza)
W tej sekcji dowiesz się, jak proces sprawdzania poprawności, który usługa Azure IoT Connector for FHIR wykonuje, aby sprawdzić poprawność szablonów JSON mapowania urządzeń i FHIR przed umożliwieniem ich zapisania do użycia.  Te elementy są wymagane w notacji JSON mapowania FHIR urządzeń i konwersji.

**Mapowanie urządzeń**

|Element|Wymagane|
|:-------|:------|
|TypeName|Prawda|
|TypeMatchExpression|Prawda|
|DeviceIdExpression|Prawda|
|TimestampExpression|Prawda|
|Wartości []. Pełna|Prawda|
|Wartości []. ValueExpression|Prawda|

> [!NOTE]
> Wartości []. ValueName i wartości []. ValueExpression
>
> Te elementy są wymagane tylko wtedy, gdy w tablicy znajduje się wpis wartości — jest on prawidłowy dla nie mapowanych wartości. Ta wartość jest używana, gdy wysyłane dane telemetryczne są zdarzeniem. Na przykład: gdy urządzenie wearable IoMT jest włączone lub usunięte. Element (y) nie ma żadnych wartości z wyjątkiem nazwy, którą łącznik usługi Azure IoT dla FHIR dopasowuje i emituje. W przypadku konwersji FHIR łącznik usługi Azure IoT dla FHIR mapuje go na koncepcję z możliwością kodu opartą na typie semantycznym — nie są wypełnione wartości rzeczywiste.

**Mapowanie FHIR**

|Element|Wymagane|
|:------|:-------|
|TypeName|Prawda|

> [!NOTE]
> Jest to jedyny wymagany element mapowania FHIR.

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>Komunikaty o błędach i poprawki dla łącznika usługi Azure IoT dla FHIR (wersja zapoznawcza)

|Wiadomość|Stawia|Warunek|Wprowadzanie poprawek| 
|-------|---------|---------|---|
|Nieprawidłowa nazwa mapowania; nazwa mapowania powinna mieć nazwę Device lub FHIR.|Interfejs API|Podany typ mapowania nie jest urządzeniem ani FHIR.|Użyj jednego z dwóch obsługiwanych typów mapowania (na przykład: Device lub FHIR).|
|Walidacja nie powiodła się. Brak wymaganych informacji lub są one nieprawidłowe.|Interfejs API i Azure Portal|Podjęto próbę zapisania w mapowaniu konwersji braku potrzebnych informacji lub elementu.|Dodaj brakujące informacje lub element mapowania konwersji i spróbuj ponownie zapisać mapowanie konwersji.|
|Nie zdefiniowano ponownie parametrów klucza.|Interfejs API|Ponownie Wygeneruj żądanie klucza.|Uwzględnij parametry w żądaniu klucza regeneracji.|
|Osiągnięto maksymalną liczbę wystąpień łącznika usługi IoT, które mogą być obsługiwane w tej subskrypcji.|Interfejs API i Azure Portal|Osiągnięto limit przydziału subskrypcji łącznika usługi Azure IoT dla FHIR (wartość domyślna to 2) na subskrypcję.|Usuń jedno z istniejących wystąpień łącznika usługi Azure IoT dla FHIR.  Użyj innej subskrypcji, która nie osiągnęła limitu przydziału subskrypcji.  Poproś o zwiększenie limitu przydziału subskrypcji.|
|Przenoszenie zasobu jest nieobsługiwane w przypadku włączenia usługi Azure API dla zasobu FHIR.|Interfejs API i Azure Portal|Podjęto próbę wykonania operacji przenoszenia na interfejsie API platformy Azure dla zasobu FHIR z co najmniej jednym wystąpieniem łącznika usługi Azure IoT dla FHIR.|Usuń istniejące wystąpienia łącznika usługi Azure IoT dla programu FHIR, aby wykonać operację przenoszenia.|
|Nie zainicjowano obsługi administracyjnej łącznika IoT.|Interfejs API|Podjęto próbę użycia usług podrzędnych (połączeń & mapowania), gdy nie zainicjowano obsługi administracyjnej elementu nadrzędnego (łącznik usługi Azure IoT dla FHIR).|Zainicjuj obsługę łącznika usługi Azure IoT dla FHIR.|
|Żądanie nie jest obsługiwane.|Interfejs API|Określone żądanie interfejsu API nie jest obsługiwane.|Użyj poprawnego żądania interfejsu API.|
|Konto nie istnieje.|Interfejs API|Podjęto próbę dodania łącznika usługi Azure IoT dla programu FHIR, a interfejs API platformy Azure dla usługi FHIR nie istnieje.|Utwórz zasób interfejsu API platformy Azure dla usługi FHIR, a następnie spróbuj ponownie wykonać operację.|
|Wersja usługi Azure API for FHIR Resource FHIR nie jest obsługiwana w przypadku łącznika IoT.|Interfejs API|Podjęto próbę użycia łącznika usługi Azure IoT dla FHIR z niezgodną wersją interfejsu API platformy Azure dla zasobu FHIR.|Utwórz nowy zasób Azure API for FHIR (wersja R4) lub Użyj istniejącego interfejsu API platformy Azure dla zasobu FHIR (wersja R4).

##  <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>Dlaczego mój łącznik usługi Azure IoT for FHIR (wersja zapoznawcza) nie jest wyświetlany w usłudze Azure API for FHIR?

|Potencjalne problemy|Poprawki|
|----------------|-----|
|Dane są nadal przetwarzane.|Dane są egressed do interfejsu API platformy Azure dla FHIR w partiach (co ~ 15 minut).  Istnieje możliwość, że dane są nadal przetwarzane i konieczne jest dodatkowy czas utrwalania danych w interfejsie API platformy Azure dla FHIR.|
|KOD JSON mapowania urządzeń nie został skonfigurowany.|Skonfiguruj i Zapisz plik JSON mapowania zgodnej konwersji urządzeń.|
|Plik JSON mapowania FHIR konwersji nie został skonfigurowany.|Skonfiguruj i Zapisz plik JSON mapowania FHIR konwersji.|
|Komunikat urządzenia nie zawiera oczekiwanego wyrażenia zdefiniowanego w mapowaniu urządzenia.|Sprawdź, czy wyrażenia wykryto zdefiniowane w tokenach dopasowania mapowania urządzeń zdefiniowane w komunikacie urządzenia.|
|Zasób urządzenia nie został utworzony w interfejsie API platformy Azure dla FHIR (typ rozwiązania: tylko wyszukiwanie) *.|Utwórz prawidłowy zasób urządzenia w interfejsie API platformy Azure dla FHIR. Upewnij się, że zasób urządzenia zawiera identyfikator pasujący do identyfikatora urządzenia podanego w komunikacie przychodzącym.|
|Zasób pacjenta nie został utworzony w interfejsie API platformy Azure dla FHIR (typ rozwiązania: tylko odnośnik) *.|Utwórz prawidłowy zasób pacjenta w interfejsie API platformy Azure dla FHIR.|
|Nie ustawiono odwołania do urządzenia. pacjent lub odwołanie jest nieprawidłowe (typ rozwiązania: odnośnik) *.|Upewnij się, że zasób urządzenia zawiera prawidłowe [odwołanie](https://www.hl7.org/fhir/device-definitions.html#Device.patient) do zasobu pacjenta.| 

* Dokumentacja [szybkiego startu: wdrażanie łącznika usługi Azure IoT (wersja zapoznawcza) przy użyciu Azure Portal](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview) , aby uzyskać funkcjonalny opis łącznika usługi Azure IoT dla typów rozpoznawania FHIR (na przykład: Lookup lub CREATE).

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>Tworzenie kopii pliku JSON mapowania łącznika usługi Azure IoT for FHIR (wersja zapoznawcza)
Kopiowanie łącznika usługi Azure IoT dla plików mapowania FHIR może być przydatne do edytowania i archiwizowania poza witryną sieci Web Azure Portal.

Podczas otwierania biletu pomocy technicznej w celu ułatwienia rozwiązywania problemów należy dostarczyć kopie plików mapowania do pomocy technicznej platformy Azure.

> [!NOTE]
> KOD JSON jest jedynym obsługiwanym formatem plików mapowania urządzeń i FHIR.

> [!TIP]
> Dowiedz się więcej o łączniku usługi Azure IoT dla [urządzeń FHIR i pliku JSON mapowania konwersji FHIR](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Wybierz pozycję **"IoT Connector (wersja zapoznawcza)"** w lewej dolnej części pulpitu nawigacyjnego zasobów usługi Azure API for FHIR w sekcji **"Dodatki"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT Connector1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Wybierz **"łącznik"** , z którego będzie KOPIOWANY kod JSON mapowania konwersji.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT Connector1" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Ten proces może również służyć do kopiowania i zapisywania zawartości JSON **"Konfigurowanie mapowania FHIR"** .

3. Wybierz pozycję **"Konfiguruj mapowanie urządzenia"** .

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT Connector1" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Wybierz zawartość JSON i wykonaj operację kopiowania (na przykład: wybierz CTRL + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT Connector1" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Wykonaj operację wklejania (na przykład: wybierz kombinację klawiszy CTRL + v) do nowego pliku w edytorze (na przykład: Visual Studio Code, Notatnik) i Zapisz plik z rozszerzeniem *. JSON.

> [!TIP]
> W przypadku otwierania biletu pomocy technicznej [platformy Azure](https://azure.microsoft.com/support/create-ticket/) dla łącznika usługi Azure IoT dla FHIR upewnij się, że zostały dołączone kopie JSON mapowania konwersji, aby ułatwić proces rozwiązywania problemów.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z często zadawanymi pytaniami dotyczącymi łącznika usługi Azure IoT dla FHIR.

>[!div class="nextstepaction"]
>[Łącznik usługi Azure IoT dla FHIR — często zadawane pytania](fhir-faq.md)

* W Azure Portal łącznik usługi Azure IoT dla FHIR jest określany jako łącznik IoT (wersja zapoznawcza).

FHIR to zastrzeżony znak towarowy firmy HL7 i jest używany za jej pozwoleniem.
