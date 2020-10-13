---
title: Migrowanie do kontenerów OCR odczytu v3. x
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić migrację do kontenerów OCR odczytu v3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 09/21/2020
ms.author: aahi
ms.openlocfilehash: 856e73181ee02fe2bb21c4317ec8c733e2536d53
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973126"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>Migrowanie do kontenerów OCR odczytu v3. x

Jeśli używasz wersji 2 przetwarzanie obrazów kontenera OCR do odczytu, Skorzystaj z tego artykułu, aby dowiedzieć się więcej na temat uaktualniania aplikacji do korzystania z wersji 3. x kontenera. 


## <a name="configuration-changes"></a>Zmiany konfiguracji

* `ReadEngineConfig:ResultExpirationPeriod` nie jest już obsługiwane. Kontener odczytu ma wbudowane zadanie firmy CRONUS, które usuwa wyniki i metadane skojarzone z żądaniem po 48 godzinach.
* `Cache:Redis:Configuration` nie jest już obsługiwane. Pamięć podręczna nie jest używana w kontenerach v3. x, więc nie trzeba jej ustawiać.

## <a name="api-changes"></a>Zmiany interfejsu API

Kontenery Read v3. x używają wersji 3 interfejs API przetwarzania obrazów i mają następujące punkty końcowe:

#### <a name="version-31-preview"></a>[Wersja 3,1-Preview](#tab/version-3-1)

* `/vision/v3.1-preview.2/read/analyzeResults/{operationId}`
* `/vision/v3.1-preview.2/read/analyze`
* `/vision/v3.1-preview.2/read/syncAnalyze`

#### <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

* `/vision/v3.0/read/analyzeResults/{operationId}`
* `/vision/v3.0/read/analyze`
* `/vision/v3.0/read/syncAnalyze`

---

Aby uzyskać szczegółowe informacje na temat aktualizowania aplikacji do korzystania z wersji 3 interfejsu API odczytu opartego na chmurze, zobacz [Przewodnik migracji interfejsu API REST przetwarzanie obrazów v3](https://docs.microsoft.com/azure/cognitive-services/computer-vision/upgrade-api-versions) . Te informacje odnoszą się również do kontenera. Należy zauważyć, że operacje synchronizacji są obsługiwane tylko w kontenerach.

## <a name="memory-requirements"></a>Wymagania dotyczące pamięci

Wymagania i zalecenia są oparte na testach porównawczych z pojedynczym żądaniem na sekundę przy użyciu obrazu 8 MB z zeskanowaną listą biznesową, która zawiera 29 wierszy i łącznie 803 znaków. W poniższej tabeli opisano minimalne i zalecane alokacje zasobów dla każdego kontenera odczytu.

|Kontener  |Minimum | Zalecane  |
|---------|---------|------|
|Przeczytaj 3,0 — wersja zapoznawcza     | 8 rdzeni, 16 GB pamięci         | 8 rdzeni, 24 GB pamięci
|Przeczytaj 3,1 — wersja zapoznawcza | 8 rdzeni, 16 GB pamięci         | 8 rdzeni, 24 GB pamięci

Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.

Rdzeń i pamięć odpowiadają `--cpus` `--memory` ustawieniom i, które są używane jako część polecenia Docker Run.

## <a name="storage-implementations"></a>Implementacje magazynu

>[!NOTE]
> MongoDB nie jest już obsługiwana w wersjach 3. x kontenera. Zamiast tego kontenery obsługują usługę Azure Storage i systemy plików trybu offline.

| Implementacja |    Wymagane argumenty środowiska uruchomieniowego |
|---------|---------|
|Poziom pliku (wartość domyślna)    | Nie są wymagane argumenty środowiska uruchomieniowego. `/share` zostanie użyty katalog. |
|Obiekt bob Azure    | `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## <a name="queue-implementations"></a>Implementacje kolejki

W wersji 3. x kontenera RabbitMQ nie jest obecnie obsługiwane. Obsługiwane są następujące implementacje implementacji:

| Implementacja | Argumenty środowiska uruchomieniowego | Zamierzone użycie |
|---------|---------|-------|
| W pamięci (domyślnie) | Nie są wymagane argumenty środowiska uruchomieniowego. | Tworzenie i testowanie |
| Azure Queues | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | Produkcja |
| RabbitMQ    | Niedostępny | Produkcja |

W przypadku dodanej nadmiarowości kontener Read v3. x używa czasomierza widoczności, aby zapewnić pomyślne przetworzenie żądań w przypadku awarii, podczas uruchamiania w konfiguracji wielokontenerowej. 

Ustaw czasomierz przy użyciu `Queue:Azure:QueueVisibilityTimeoutInMilliseconds` , który określa czas, przez który komunikat będzie niewidoczny podczas przetwarzania go przez inny proces roboczy. Aby uniknąć nadmiarowego przetwarzania stron, zalecamy ustawienie limitu czasu na 120 sekund. Wartość domyślna to 30 sekund.

| Wartość domyślna | Zalecana wartość |
|---------|---------|
| 30000 |    120000 |


## <a name="next-steps"></a>Następne kroki

* Przegląd [konfigurowania kontenerów](computer-vision-resource-container-config.md) dla ustawień konfiguracji
* Przejrzyj [przegląd przetwarzanie obrazów](overview.md) , aby dowiedzieć się więcej o rozpoznawaniu tekstu napisanego i odręcznego
* Zapoznaj się z [interfejs API przetwarzania obrazów](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) , aby uzyskać szczegółowe informacje na temat metod obsługiwanych przez kontener.
* Zapoznaj się z [często zadawanymi pytaniami](FAQ.md) , aby rozwiązać problemy związane z funkcjami przetwarzanie obrazów.
* Użyj więcej [kontenerów Cognitive Services](../cognitive-services-container-support.md)
