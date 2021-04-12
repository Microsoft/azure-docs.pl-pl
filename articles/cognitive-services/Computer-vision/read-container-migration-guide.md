---
title: Migrowanie do kontenerów Read v3. x
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić migrację do kontenerów OCR odczytu v3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/29/2021
ms.author: aahi
ms.openlocfilehash: 1cc17306265e6e8ba2e7fb3f570d0017b006b84f
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284689"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>Migrowanie do kontenerów OCR odczytu v3. x

Jeśli używasz wersji 2 przetwarzanie obrazów kontenera OCR do odczytu, Skorzystaj z tego artykułu, aby dowiedzieć się więcej na temat uaktualniania aplikacji do korzystania z wersji 3. x kontenera. 


## <a name="configuration-changes"></a>Zmiany konfiguracji

* `ReadEngineConfig:ResultExpirationPeriod` nie jest już obsługiwane. Kontener OCR odczytu ma wbudowane zadanie firmy CRONUS, które usuwa wyniki i metadane skojarzone z żądaniem po 48 godzinach.
* `Cache:Redis:Configuration` nie jest już obsługiwane. Pamięć podręczna nie jest używana w kontenerach v3. x, więc nie trzeba jej ustawiać.

## <a name="api-changes"></a>Zmiany interfejsu API

Kontener Read v 3.2 używa wersji 3 interfejs API przetwarzania obrazów i ma następujące punkty końcowe:

* `/vision/v3.2-preview.1/read/analyzeResults/{operationId}`
* `/vision/v3.2-preview.1/read/analyze`
* `/vision/v3.2-preview.1/read/syncAnalyze`

Aby uzyskać szczegółowe informacje na temat aktualizowania aplikacji do korzystania z wersji 3 interfejsu API odczytu opartego na chmurze, zobacz [Przewodnik migracji interfejsu API REST przetwarzanie obrazów v3](./upgrade-api-versions.md) . Te informacje odnoszą się również do kontenera. Należy zauważyć, że operacje synchronizacji są obsługiwane tylko w kontenerach.

## <a name="memory-requirements"></a>Wymagania dotyczące pamięci

Wymagania i zalecenia są oparte na testach porównawczych z pojedynczym żądaniem na sekundę przy użyciu obrazu 8 MB z zeskanowaną listą biznesową, która zawiera 29 wierszy i łącznie 803 znaków. W poniższej tabeli opisano minimalną i zalecaną alokację zasobów dla każdego kontenera OCR odczytu.

|Kontener  |Minimum | Zalecane  |
|---------|---------|------|
|Przeczytaj 3,2 — wersja zapoznawcza | 8 rdzeni, 16 GB pamięci         | 8 rdzeni, 24 GB pamięci |

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
* Przejrzyj [Omówienie OCR](overview-ocr.md) , aby dowiedzieć się więcej o rozpoznawaniu tekstu napisanego i odręcznego
* Aby uzyskać szczegółowe informacje na temat metod obsługiwanych przez kontener, zapoznaj się z [interfejsem API odczytu](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .
* Zapoznaj się z [często zadawanymi pytaniami](FAQ.md) , aby rozwiązać problemy związane z funkcjami przetwarzanie obrazów.
* Użyj więcej [kontenerów Cognitive Services](../cognitive-services-container-support.md)