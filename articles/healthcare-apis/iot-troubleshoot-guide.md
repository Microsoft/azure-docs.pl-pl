---
title: Azure IoT Connector for FHIR (łącznik IoT) — Przewodnik rozwiązywania problemów i instrukcje
description: Jak rozwiązywać typowe komunikaty o błędach i warunki programu IoT Connector oraz kopiować pliki mapowania
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jasteppe
ms.openlocfilehash: e74e3475256858955a5ab0dc99e7b858548e2485
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099392"
---
# <a name="iot-connector-preview-troubleshooting-guide-and-how-to"></a>Przewodnik rozwiązywania problemów z usługą IoT Connector (wersja zapoznawcza) i instrukcje

W tym artykule dowiesz się, jak rozwiązywać problemy z typowymi komunikatami o błędach i postanowieniami programu IoT Connector (wersja zapoznawcza).

Dowiesz się również, jak utworzyć kopie mapowania JSON łącznika IoT (wersja zapoznawcza).

## <a name="error-messages-and-fixes"></a>Komunikaty o błędach i poprawki

|Komunikat   |Warunek  |Wprowadzanie poprawek         |
|----------|-----------|------------|
|Nieprawidłowa nazwa mapowania, nazwa mapowania powinna być urządzeniem lub FHIR|Dostarczony typ mapowania nie jest urządzeniem ani FHIR|Użyj jednego z dwóch obsługiwanych typów mapowania (na przykład: Device lub FHIR)|
|Nie zdefiniowano ponownie parametrów klucza|Żądanie ponownego wygenerowania klucza|Uwzględnij parametry w żądaniu klucza regeneracji|
|Osiągnięto maksymalną liczbę wystąpień łącznika usługi IoT, które mogą być obsługiwane w ramach tej subskrypcji|Osiągnięto limit przydziału subskrypcji łącznika usługi IoT (wartość domyślna to 2 na subskrypcję)|Usuń jedno z istniejących łączników, użyj innej subskrypcji, która nie osiągnęła (2) łączników na przydział subskrypcji lub zażądaj zwiększenia limitu przydziału subskrypcji|
|Przenoszenie zasobu nie jest obsługiwane w przypadku usługi Azure API Enabled dla zasobu FHIR|Podjęto próbę wykonania operacji przenoszenia na interfejsie API platformy Azure dla zasobu FHIR, który ma co najmniej jeden łącznik IoT|Usuń istniejące łączniki, aby wykonać/zakończyć operacje przenoszenia|
|W przypadku zasobu Azure API for FHIR jest włączone łącze prywatne.  Link prywatny nie jest obsługiwany w przypadku łącznika IoT|Podjęto próbę dodania łącznika IoT do zasobu usługi Azure API for FHIR z włączonym prywatnym linkiem|Wybierz lub Utwórz interfejs API platformy Azure dla zasobu FHIR (wersja R4), dla którego nie włączono linku prywatnego|
|Nie zainicjowano obsługi łącznika IoT|Podjęto próbę użycia usług podrzędnych (połączeń & mapowania), gdy nie zainicjowano obsługi administracyjnej elementu nadrzędnego (łącznik usługi IoT)|Inicjowanie obsługi administracyjnej łącznika IoT|
|Żądanie nie jest obsługiwane|Określone żądanie interfejsu API nie jest obsługiwane|Użyj poprawnego żądania interfejsu API|
|Konto nie istnieje|Podjęto próbę dodania łącznika IoT i zasobu Azure API for FHIR nie istnieje|Utwórz zasób interfejsu API platformy Azure dla usługi FHIR, a następnie spróbuj ponownie wykonać operację.|
|Wersja usługi Azure API for FHIR Resource FHIR nie jest obsługiwana w przypadku łącznika IoT|Podjęto próbę użycia łącznika IoT z niezgodną wersją zasobu usługi Azure API for FHIR|Tworzenie nowego interfejsu API platformy Azure dla zasobu FHIR (wersja R4) lub korzystanie z istniejącego interfejsu API platformy Azure dla zasobu FHIR (wersja R4)

## <a name="creating-copies-of-the-iot-connector-preview-mapping-files"></a>Tworzenie kopii plików mapowania łącznika IoT (wersja zapoznawcza)
> [!NOTE]
> KOD JSON jest jedynym obsługiwanym formatem plików mapowania urządzeń i FHIR.

> [!TIP]
> Kopiowanie plików mapowania łącznika IoT może być przydatne w przypadku edytowania i archiwizowania poza witryną sieci Web w witrynie Azure Portal oraz do świadczenia pomocy technicznej platformy Azure podczas otwierania biletu pomocy technicznej.
> 
> Dowiedz się więcej o urządzeniu łącznik IoT [i FHIR mapowania plików JSON](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Wybierz pozycję **"IoT Connector (wersja zapoznawcza)"** w lewej dolnej części pulpitu nawigacyjnego zasobów usługi Azure API for FHIR w sekcji **"Dodatki"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Łącznik IoT" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Wybierz **"łącznik"** , z którego będą kopiowane pliki mapowania.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Łącznik IoT" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

3. Wybierz pozycję **"Konfiguruj mapowanie urządzenia"**.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Łącznik IoT" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

> [!NOTE]
> Ten proces może również służyć do kopiowania/zapisywania zawartości JSON **"Konfigurowanie mapowania FHIR"** .

4. Wybierz zawartość JSON i wykonaj operację kopiowania (na przykład: wybierz CTRL + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Łącznik IoT" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Wykonaj operację wklejania (na przykład: wybierz kombinację klawiszy CTRL + v) do nowego pliku w edytorze (na przykład: Visual Studio Code, Notatnik) i Zapisz plik z rozszerzeniem *. JSON.

> [!TIP]
> W przypadku otwierania biletu pomocy technicznej [platformy Azure](https://azure.microsoft.com/support/create-ticket/) dla łącznika IoT upewnij się, że zostały dołączone kopie plików mapowania, aby ułatwić proces rozwiązywania problemów.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z często zadawanymi pytaniami dotyczącymi łącznika IoT

>[!div class="nextstepaction"]
>[Często zadawane pytania dotyczące łącznika IoT](fhir-faq.md#iot-connector-preview)


FHIR to zastrzeżony znak towarowy firmy HL7 i jest używany za jej pozwoleniem.