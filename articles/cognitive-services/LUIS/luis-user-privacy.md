---
title: Eksportuj & Usuń dane — LUIS
titleSuffix: Azure Cognitive Services
description: Masz pełną kontrolę nad przeglądaniem, eksportowaniem i usuwaniem swoich danych. Usuwanie danych klienta w celu zapewnienia prywatności i zgodności.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/02/2019
ms.openlocfilehash: 868443e52b7159e6ee9478f64b7b2f376a10f604
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541010"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Eksportuj i usuwaj dane klientów w Language Understanding (LUIS) w Cognitive Services

Usuwanie danych klienta w celu zapewnienia prywatności i zgodności.

## <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klienta
Language Understanding Intelligent Service (LUIS) zachowuje zawartość klienta w celu obsługi usługi, ale użytkownik LUIS ma pełną kontrolę nad przeglądaniem, eksportowaniem i usuwaniem danych. Można to zrobić za pomocą [portalu](luis-reference-regions.md) sieci Web Luis lub [interfejsów API tworzenia Luis (nazywanych także programistycznie)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Zawartość klienta jest zaszyfrowana w ramach regionalnej usługi Microsoft Azure Storage i obejmuje następujące elementy:

- Zawartość konta użytkownika zebrana podczas rejestracji
- Dane szkoleniowe wymagane do skompilowania modeli
- Zarejestrowane zapytania użytkownika używane przez [aktywną naukę](luis-concept-review-endpoint-utterances.md) w celu ulepszania modelu
  - Użytkownicy mogą wyłączyć rejestrowanie zapytań `&log=false` , dołączając do żądania szczegółowe dane [here](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Usuwanie danych klienta
Użytkownicy LUIS mają pełną kontrolę w zakresie usuwania dowolnej zawartości użytkownika, za pomocą portalu sieci Web LUIS lub interfejsu API LUIS Authoring (nazywanego również programistycznymi). W poniższej tabeli przedstawiono linki pomagające w obu:

| | **Konto użytkownika** | **Aplikacja** | **Przykład wypowiedź** | **Zapytania użytkowników końcowych** |
| --- | --- | --- | --- | --- |
| **Portal** | [Łącze](luis-concept-data-storage.md#delete-an-account) | [Łącze](luis-how-to-start-new-app.md#delete-app) | [Łącze](luis-concept-data-storage.md#utterances-in-an-intent) | [Active Learning wyrażenia długości](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Wyrażenia długości zarejestrowane](luis-concept-data-storage.md#disable-logging-utterances) |
| **Interfejsy API** | [Łącze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Łącze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Łącze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Łącze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Eksportowanie danych klienta
Użytkownicy LUIS mają pełną kontrolę w celu wyświetlania danych w portalu, ale muszą być wyeksportowane za pomocą interfejsów API tworzenia LUIS (nazywanych również programistycznymi). W poniższej tabeli przedstawiono linki pomagające w eksportowaniu danych za pośrednictwem interfejsów API LUIS Authoring (nazywanych również programistycznymi):

| | **Konto użytkownika** | **Aplikacja** | **Wypowiedź** | **Zapytania użytkowników końcowych** |
| --- | --- | --- | --- | --- |
| **Interfejsy API** | [Łącze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Łącze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Łącze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Łącze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Lokalizacja aktywnego uczenia

Aby włączyć [aktywną naukę](luis-how-to-review-endpoint-utterances.md#enable-active-learning), wyrażenia długości zalogowanych użytkowników w opublikowanej Luis punktach końcowych, są przechowywane w następującym lokalizacje geograficzne Azure:

* [Europa](#europe)
* [Australia](#australia)
* [Stany Zjednoczone](#united-states)

Z wyjątkiem aktywnych danych szkoleniowych (opisanych poniżej) LUIS są zgodne z [zasadami przechowywania danych dla usług regionalnych](https://azuredatacentermap.azurewebsites.net/).

### <a name="europe"></a>Europa

Portal [eu.Luis.AI](https://eu.luis.ai) i tworzenie do Europy (nazywane również programistycznymi interfejsami API) są hostowane w lokalizacji geograficznej platformy Azure. Portal eu.luis.ai i autorstwo Europe (nazywane również programistycznymi interfejsami API) obsługują wdrażanie punktów końcowych w ramach następujących lokalizacje geograficzne platformy Azure:

* Europa
* Francja
* Zjednoczone Królestwo

W przypadku wdrażania w usłudze Azure lokalizacje geograficzne, wyrażenia długości odebrane przez punkt końcowy od użytkowników końcowych aplikacji będą przechowywane w obszarze geograficznym platformy Azure na potrzeby aktywnego uczenia się. Aktywną naukę można wyłączyć, zobacz temat [wyłączanie aktywnego uczenia](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Aby zarządzać przechowywanymi wyrażenia długości, zobacz [delete wypowiedź](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="australia"></a>Australia

Portal [au.Luis.AI](https://au.luis.ai) i Australia (nazywane również programistycznymi interfejsami API) są hostowane w obszarze geograficznym Australii platformy Azure. Portal au.luis.ai i Australia Authoring (nazywane również programistycznymi interfejsami API) obsługują wdrażanie punktów końcowych w ramach następujących lokalizacje geograficzne platformy Azure:

* Australia

Po wdrożeniu w usłudze Azure lokalizacje geograficzne, wyrażenia długości otrzymany przez punkt końcowy od użytkowników końcowych aplikacji będzie przechowywany w obszarze geograficznym Australii platformy Azure na potrzeby aktywnej nauki. Aktywną naukę można wyłączyć, zobacz temat [wyłączanie aktywnego uczenia](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Aby zarządzać przechowywanymi wyrażenia długości, zobacz [delete wypowiedź](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="united-states"></a>Stany Zjednoczone

Portal [Luis.AI](https://www.luis.ai) i tworzenie Stany Zjednoczone (nazywane również programistycznymi interfejsami API) są hostowane w Stany Zjednoczone geograficznym platformy Azure. Portal luis.ai i Stany Zjednoczone tworzenie (znane także jako programowe interfejsy API) obsługują wdrażanie punktów końcowych w ramach następujących lokalizacje geograficzne platformy Azure:

* Lokalizacje geograficzne platformy Azure nie są obsługiwane przez regiony Europy lub Australii

W przypadku wdrażania w usłudze Azure lokalizacje geograficzne, wyrażenia długości odebrane przez punkt końcowy od użytkowników końcowych aplikacji będą przechowywane w Stany Zjednoczone geograficznym platformy Azure na potrzeby aktywnej nauki. Aktywną naukę można wyłączyć, zobacz temat [wyłączanie aktywnego uczenia](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Aby zarządzać przechowywanymi wyrażenia długości, zobacz [delete wypowiedź](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Odwołania do regionów LUIS](./luis-reference-regions.md)
