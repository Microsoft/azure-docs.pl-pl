---
title: Instalowanie kontenerów mowy
titleSuffix: Azure Cognitive Services
description: Szczegółowe opcje konfiguracji wykresu helm speech-to-text.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: trbye
ms.openlocfilehash: f7ca8fdaddab9757292939c4f7e658179d6e517c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422337"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Zamiana mowy na tekst (podobsce: wykresy/speechToText)

Aby zastąpić wykres "parasol", dodaj prefiks `speechToText.` do dowolnego parametru, aby był bardziej szczegółowy. Na przykład zastąpi odpowiedni parametr, `speechToText.numberOfConcurrentRequest` na przykład zastępuje `numberOfConcurrentRequest`.

|Parametr|Opis|Domyślne|
| -- | -- | -- |
| `enabled` | Czy usługa **zamiany mowy na tekst** jest włączona. | `false` |
| `numberOfConcurrentRequest` | Liczba równoczesnych żądań dla usługi **zamiany mowy na tekst.** Ten wykres automatycznie oblicza zasoby procesora CPU i pamięci na podstawie tej wartości. | `2` |
| `optimizeForAudioFile`| Czy usługa musi zoptymalizować dla wejścia audio za pośrednictwem plików audio. Jeśli `true`ten wykres przydzieli więcej zasobów procesora DO serwisu. | `false` |
| `image.registry`| Rejestr obrazów dokowane **zamiany mowy na tekst.** | `containerpreview.azurecr.io` |
| `image.repository` | Repozytorium obrazów dokowane **zamiany mowy na tekst.** | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Znacznik obrazu dokowane **zamiany mowy na tekst.** | `latest` |
| `image.pullSecrets` | Wpisy tajne obrazu do ciągnięcia obrazu dokowane **mowy do tekstu.** | |
| `image.pullByHash`| Czy obraz docker jest ściągnięty przez skrót. Jeśli `true` `image.hash` wymagane jest. | `false` |
| `image.hash`| Skrót obrazu dokowane **zamiany mowy na tekst.** Używane tylko `image.pullByHash: true`wtedy, gdy .  | |
| `image.args.eula`(wymagane) | Wskazuje, że licencja została zaakceptowana. Jedyną prawidłową wartością jest`accept` | |
| `image.args.billing`(wymagane) | Wartość identyfikatora URI punktu końcowego rozliczeń jest dostępna na stronie Omówienie mowy w portalu Azure. | |
| `image.args.apikey`(wymagane) | Służy do śledzenia informacji rozliczeniowych. ||
| `service.type` | Typ usługi Kubernetes usługi **zamiany mowy na tekst.** Zobacz [instrukcje typów usług Kubernetes,](https://kubernetes.io/docs/concepts/services-networking/service/) aby uzyskać więcej informacji i zweryfikować obsługę dostawcy chmury. | `LoadBalancer` |
| `service.port`|  Port usługi **zamiany mowy na tekst.** | `80` |
| `service.annotations` | Adnotacje **zamiany mowy na tekst** dla metadanych usługi. Adnotacje są parami kluczowych wartości. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Czy [skalowanie automatyczne podążek poziomych](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) jest włączone. Jeśli `true`zostanie `speech-to-text-autoscaler` wdrożony w klastrze Kubernetes. | `true` |
| `service.podDisruption.enabled` | Czy [budżet zakłócenia pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) jest włączona. Jeśli `true`zostanie `speech-to-text-poddisruptionbudget` wdrożony w klastrze Kubernetes. | `true` |