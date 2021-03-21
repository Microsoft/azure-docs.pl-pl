---
title: Eksportuj & Usuń dane — LUIS
titleSuffix: Azure Cognitive Services
description: Masz pełną kontrolę nad przeglądaniem, eksportowaniem i usuwaniem swoich danych. Usuwanie danych klienta w celu zapewnienia prywatności i zgodności.
services: cognitive-services
manager: nitinme
ms.custom: seodec18, references_regions
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/10/2020
ms.openlocfilehash: 0a2d0ce683261ca3460c7aeaa0d7a42152b81a1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98680193"
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
  - Użytkownicy mogą wyłączyć rejestrowanie zapytań `&log=false` , dołączając do żądania szczegółowe dane [](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Usuwanie danych klienta
Użytkownicy LUIS mają pełną kontrolę w zakresie usuwania dowolnej zawartości użytkownika, za pomocą portalu sieci Web LUIS lub interfejsu API LUIS Authoring (nazywanego również programistycznymi). W poniższej tabeli przedstawiono linki pomagające w obu:

| | **Konto użytkownika** | **Aplikacja** | **Przykład wypowiedź** | **Zapytania użytkowników końcowych** |
| --- | --- | --- | --- | --- |
| **Portal** | [Link](luis-concept-data-storage.md#delete-an-account) | [Link](luis-how-to-start-new-app.md#delete-app) | [Link](luis-concept-data-storage.md#utterances-in-an-intent) | [Active Learning wyrażenia długości](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Wyrażenia długości zarejestrowane](luis-concept-data-storage.md#disable-logging-utterances) |
| **Interfejsy API** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Eksportowanie danych klienta
Użytkownicy LUIS mają pełną kontrolę w celu wyświetlania danych w portalu, ale muszą być wyeksportowane za pomocą interfejsów API tworzenia LUIS (nazywanych również programistycznymi). W poniższej tabeli przedstawiono linki pomagające w eksportowaniu danych za pośrednictwem interfejsów API LUIS Authoring (nazywanych również programistycznymi):

| | **Konto użytkownika** | **Aplikacja** | **Wypowiedź** | **Zapytania użytkowników końcowych** |
| --- | --- | --- | --- | --- |
| **Interfejsy API** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Lokalizacja aktywnego uczenia

Aby włączyć [aktywną naukę](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning), wyrażenia długości zalogowanych użytkowników w opublikowanej Luis punktach końcowych, są przechowywane w następującym lokalizacje geograficzne Azure:

* [Europa](#europe)
* [Australia](#australia)
* [Stany Zjednoczone](#united-states)

Z wyjątkiem aktywnych danych szkoleniowych (opisanych poniżej) LUIS są zgodne z [zasadami przechowywania danych dla usług regionalnych](https://azuredatacentermap.azurewebsites.net/).

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]


### <a name="europe"></a>Europa

Zasoby Europa (nazywane również interfejsami API programu programistycznego) są hostowane w obszarze geograficznym Europy platformy Azure i obsługują wdrażanie punktów końcowych w ramach następujących lokalizacje geograficzne platformy Azure:

* Europa
* Francja
* Zjednoczone Królestwo

W przypadku wdrażania w usłudze Azure lokalizacje geograficzne, wyrażenia długości odebrane przez punkt końcowy od użytkowników końcowych aplikacji będą przechowywane w obszarze geograficznym platformy Azure na potrzeby aktywnego uczenia się.

### <a name="australia"></a>Australia

Zasoby w Australii (nazywane również programistycznymi interfejsami API) są hostowane w obszarze geograficznym programu Australia platformy Azure i obsługują wdrażanie punktów końcowych w ramach następujących lokalizacje geograficzne Azure:

* Australia

Po wdrożeniu w usłudze Azure lokalizacje geograficzne, wyrażenia długości otrzymany przez punkt końcowy od użytkowników końcowych aplikacji będzie przechowywany w obszarze geograficznym Australii platformy Azure na potrzeby aktywnej nauki.

### <a name="united-states"></a>Stany Zjednoczone

Stany Zjednoczone tworzenia (nazywanego również interfejsami API programu programistycznego) są hostowane w Stany Zjednoczone geograficznym platformy Azure i obsługują wdrażanie punktów końcowych w ramach następujących lokalizacje geograficzne Azure:

* Lokalizacje geograficzne platformy Azure nie są obsługiwane przez regiony Europy lub Australii

W przypadku wdrażania w usłudze Azure lokalizacje geograficzne, wyrażenia długości odebrane przez punkt końcowy od użytkowników końcowych aplikacji będą przechowywane w Stany Zjednoczone geograficznym platformy Azure na potrzeby aktywnej nauki. 

## <a name="disable-active-learning"></a>Wyłącz aktywną naukę

Aby wyłączyć aktywną naukę, zobacz sekcję [disable Active Learning](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Aby zarządzać przechowywanymi wyrażenia długości, zobacz [delete wypowiedź](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Odwołania do regionów LUIS](./luis-reference-regions.md)
