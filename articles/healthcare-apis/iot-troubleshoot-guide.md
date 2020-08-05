---
title: Azure IoT Connector for FHIR (wersja zapoznawcza) — Przewodnik rozwiązywania problemów i instrukcje
description: Jak rozwiązywać problemy z typowym łącznikiem usługi Azure IoT for FHIR (wersja zapoznawcza) komunikaty o błędach i warunki i kopiować pliki mapowania
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: jasteppe
ms.openlocfilehash: b404fa5322d3afa8cbf71741382d44dd0433d49c
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553686"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów z usługą Azure IoT Connector for FHIR (wersja zapoznawcza)

W tym artykule przedstawiono procedurę rozwiązywania problemów z typowym łącznikiem usługi Azure IoT dla FHIR * komunikatów o błędach i warunków.  

Dowiesz się również, jak utworzyć kopie łącznika usługi Azure IoT dla plików mapowania FHIR JSON w celu edytowania i archiwizowania poza Azure Portal lub zapewnienia pomocy technicznej platformy Azure podczas otwierania biletu pomocy technicznej. 

## <a name="error-messages-and-fixes"></a>Komunikaty o błędach i poprawki

|Wiadomość   |Warunek  |Wprowadzanie poprawek         |
|----------|-----------|------------|
|Nieprawidłowa nazwa mapowania, nazwa mapowania powinna być urządzeniem lub FHIR|Dostarczony typ mapowania nie jest urządzeniem ani FHIR|Użyj jednego z dwóch obsługiwanych typów mapowania (na przykład: Device lub FHIR)|
|Nie zdefiniowano ponownie parametrów klucza|Żądanie ponownego wygenerowania klucza|Uwzględnij parametry w żądaniu klucza regeneracji|
|Osiągnięto maksymalną liczbę wystąpień łącznika IoT *, które mogą być obsługiwane w ramach tej subskrypcji|Osiągnięto limit przydziału subskrypcji łącznika usługi Azure IoT dla FHIR (wartość domyślna to 2) na subskrypcję)|Usuń jedno z istniejących wystąpień łącznika usługi Azure IoT dla programu FHIR, użyj innej subskrypcji, która nie osiągnęła limitu przydziału subskrypcji lub zażądaj zwiększenia limitu przydziału subskrypcji|
|Przenoszenie zasobu nie jest obsługiwane w przypadku usługi Azure API Enabled dla zasobu FHIR|Podjęto próbę wykonania operacji przenoszenia na interfejsie API platformy Azure dla zasobu FHIR z co najmniej jednym wystąpieniem łącznika usługi Azure IoT dla FHIR|Usuń istniejące wystąpienia łącznika usługi Azure IoT dla FHIR do wykonania i ukończenia operacji przenoszenia|
|Nie zainicjowano obsługi łącznika IoT|Podjęto próbę użycia usług podrzędnych (połączeń & mapowania), gdy nie zainicjowano obsługi administracyjnej elementu nadrzędnego (łącznik usługi Azure IoT dla FHIR)|Inicjowanie obsługi administracyjnej łącznika usługi Azure IoT dla FHIR|
|Żądanie nie jest obsługiwane|Określone żądanie interfejsu API nie jest obsługiwane|Użyj poprawnego żądania interfejsu API|
|Konto nie istnieje|Podjęto próbę dodania łącznika usługi Azure IoT dla programu FHIR, a interfejs API platformy Azure dla usługi FHIR nie istnieje|Utwórz zasób interfejsu API platformy Azure dla usługi FHIR, a następnie spróbuj ponownie wykonać operację.|
|Wersja usługi Azure API for FHIR Resource FHIR nie jest obsługiwana w przypadku łącznika IoT|Podjęto próbę użycia łącznika usługi Azure IoT dla FHIR z niezgodną wersją interfejsu API platformy Azure dla zasobu FHIR|Tworzenie nowego interfejsu API platformy Azure dla zasobu FHIR (wersja R4) lub korzystanie z istniejącego interfejsu API platformy Azure dla zasobu FHIR (wersja R4)

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-mapping-files"></a>Tworzenie kopii pliku mapowania łącznika usługi Azure IoT for FHIR (wersja zapoznawcza)
Kopiowanie łącznika usługi Azure IoT dla plików mapowania FHIR może być przydatne do edytowania i archiwizowania poza witryną sieci Web Azure Portal oraz do świadczenia pomocy technicznej platformy Azure podczas otwierania biletu pomocy technicznej.

> [!NOTE]
> KOD JSON jest jedynym obsługiwanym formatem plików mapowania urządzeń i FHIR.

> [!TIP]
> Dowiedz się więcej na temat łącznika usługi Azure IoT dla [urządzeń FHIR i mapowania FHIR plików JSON](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Wybierz pozycję **"IoT Connector (wersja zapoznawcza)"** w lewej dolnej części pulpitu nawigacyjnego zasobów usługi Azure API for FHIR w sekcji **"Dodatki"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Łącznik IoT" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Wybierz **"łącznik"** , z którego będą kopiowane pliki mapowania.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Łącznik IoT" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Ten proces może również służyć do kopiowania i zapisywania zawartości JSON **"Konfigurowanie mapowania FHIR"** .

3. Wybierz pozycję **"Konfiguruj mapowanie urządzenia"**.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Łącznik IoT" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Wybierz zawartość JSON i wykonaj operację kopiowania (na przykład: wybierz CTRL + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Łącznik IoT" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Wykonaj operację wklejania (na przykład: wybierz kombinację klawiszy CTRL + v) do nowego pliku w edytorze (na przykład: Visual Studio Code, Notatnik) i Zapisz plik z rozszerzeniem *. JSON.

> [!TIP]
> Jeśli otworzysz bilet [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/) dla łącznika usługi Azure IoT dla programu FHIR, upewnij się, że zostały dołączone kopie plików mapowania, aby ułatwić proces rozwiązywania problemów.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z często zadawanymi pytaniami dotyczącymi łącznika usługi Azure IoT dla FHIR.

>[!div class="nextstepaction"]
>[Łącznik usługi Azure IoT dla FHIR — często zadawane pytania](fhir-faq.md#azure-iot-connector-for-fhir-preview)

* W Azure Portal łącznik usługi Azure IoT dla FHIR jest określany jako łącznik IoT (wersja zapoznawcza).

FHIR to zastrzeżony znak towarowy firmy HL7 i jest używany za jej pozwoleniem.