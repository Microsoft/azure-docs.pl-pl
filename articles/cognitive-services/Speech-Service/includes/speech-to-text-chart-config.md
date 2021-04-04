---
title: Instalowanie kontenerów mowy
titleSuffix: Azure Cognitive Services
description: Szczegóły opcji konfiguracji wykresu Helm zamiany mowy na tekst.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/05/2020
ms.author: trbye
ms.openlocfilehash: 85c4e0641e1989ddea6c8aa8b8a8895a966a5ddb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002293"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Zamiana mowy na tekst (wykres podrzędny: wykresy/speechToText)

Aby zastąpić wykres "parasol", Dodaj prefiks `speechToText.` na dowolnym parametrze, aby uczynić go bardziej szczegółowym. Na przykład zastąpi odpowiedni parametr na przykład `speechToText.numberOfConcurrentRequest` zastąpień `numberOfConcurrentRequest` .

|Parametr|Opis|Domyślne|
| -- | -- | -- |
| `enabled` | Określa, czy jest włączona usługa **zamiany mowy na tekst** . | `false` |
| `numberOfConcurrentRequest` | Liczba równoczesnych żądań dla usługi **zamiany mowy na tekst** . Ten wykres automatycznie oblicza zasoby procesora i pamięci na podstawie tej wartości. | `2` |
| `optimizeForAudioFile`| Czy usługa musi zoptymalizować dane wejściowe audio za pośrednictwem plików audio. Jeśli `true` ten wykres przydzieli więcej zasobów procesora CPU do usługi. | `false` |
| `image.registry`| Rejestr **aparatu Docker zamiany mowy na tekst** . | `containerpreview.azurecr.io` |
| `image.repository` | Repozytorium obrazów **zamiany mowy na tekst** . | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Tag obrazu **zamiany mowy na tekst** . | `latest` |
| `image.pullSecrets` | Wpisy tajne obrazu służące do ściągania obrazu platformy Docker **zamiany mowy na tekst** . | |
| `image.pullByHash`| Czy obraz platformy Docker jest ściągany przez skrót. Jeśli `true` `image.hash` jest to wymagane. | `false` |
| `image.hash`| Wartość skrótu obrazu platformy Docker **zamiany mowy na tekst** . Używane tylko wtedy, gdy `image.pullByHash: true` .  | |
| `image.args.eula` potrzeb | Oznacza, że licencja została zaakceptowana. Jedyna prawidłowa wartość to `accept` | |
| `image.args.billing` potrzeb | Wartość identyfikatora URI punktu końcowego rozliczenia jest dostępna na stronie Przegląd mowy Azure Portal. | |
| `image.args.apikey` potrzeb | Służy do śledzenia informacji dotyczących rozliczeń. ||
| `service.type` | Typ usługi Kubernetes dla usługi **zamiany mowy na tekst** . Zapoznaj się z instrukcjami dotyczącymi [typów usługi Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) , aby uzyskać więcej informacji i zweryfikować obsługę dostawcy chmury. | `LoadBalancer` |
| `service.port`|  Port usługi **zamiany mowy na tekst** . | `80` |
| `service.annotations` | Adnotacja **zamiany mowy na tekst** dla metadanych usługi. Adnotacje to pary klucz-wartość. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Określa, czy jest włączona funkcja [automatycznego skalowania w poziomie](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . Jeśli `true` program `speech-to-text-autoscaler` zostanie wdrożony w klastrze Kubernetes. | `true` |
| `service.podDisruption.enabled` | Czy jest włączony budżet w przypadku [przerwania](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) działania. Jeśli `true` program `speech-to-text-poddisruptionbudget` zostanie wdrożony w klastrze Kubernetes. | `true` |

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>Analiza tonacji (wykres podrzędny: wykresy/speechToText)

Począwszy od wersji 2.2.0 kontenera zamiany mowy na tekst i v 0.2.0 wykresu Helm, następujące parametry są używane do analizy tonacji przy użyciu interfejs API analizy tekstu.

|Parametr|Opis|Wartości|Domyślne|
| --- | --- | --- | --- |
|`textanalytics.enabled`| Czy jest włączona usługa **analizy tekstu**| PRAWDA/FAŁSZ| `false`|
|`textanalytics.image.registry`| Rejestr obrazu platformy Docker **analizy tekstu**| prawidłowy rejestr obrazu platformy Docker| |
|`textanalytics.image.repository`| Repozytorium obrazów platformy Docker **analizy tekstu**| prawidłowe repozytorium obrazów platformy Docker| |
|`textanalytics.image.tag`| Tag obrazu platformy Docker **analizy tekstu**| prawidłowy tag obrazu platformy Docker| |
|`textanalytics.image.pullSecrets`| Wpisy tajne obrazu do ściągania obrazu platformy Docker **analizy tekstu**| prawidłowa nazwa wpisu tajnego| |
|`textanalytics.image.pullByHash`| Określa, czy ściąganie obrazu platformy Docker następuje przez mieszanie.  Jeśli `yes` `image.hash` jest również wymagany. Jeśli `no` , ustaw ją jako "false". Wartość domyślna to `false`.| PRAWDA/FAŁSZ| `false`|
|`textanalytics.image.hash`| Skrót obrazu platformy Docker **analizy tekstu** . Używać go tylko z `image.pullByHash:true` .| prawidłowy skrót obrazu platformy Docker | |
|`textanalytics.image.args.eula`| Jeden z argumentów wymaganych przez kontener **analizy tekstu** , który oznacza, że licencja została zaakceptowana. Wartość tej opcji musi być równa: `accept` .| `accept`, jeśli chcesz używać kontenera | |
|`textanalytics.image.args.billing`| Jeden z argumentów wymaganych przez kontener **analizy tekstu** , który określa identyfikator URI punktu końcowego rozliczenia. Wartość identyfikatora URI punktu końcowego rozliczenia jest dostępna na stronie Przegląd mowy Azure Portal.|prawidłowy identyfikator URI punktu końcowego rozliczenia||
|`textanalytics.image.args.apikey`| Jeden z argumentów wymaganych przez kontener **analizy tekstu** , który jest używany do śledzenia informacji dotyczących rozliczeń.| prawidłowy apikey||
|`textanalytics.cpuRequest`| Żądany procesor CPU dla kontenera **analizy tekstu**| int| `3000m`|
|`textanalytics.cpuLimit`| Ograniczony procesor CPU dla kontenera **analizy tekstu**| | `8000m`|
|`textanalytics.memoryRequest`| Żądana pamięć dla kontenera **analizy tekstu**| | `3Gi`|
|`textanalytics.memoryLimit`| Ograniczona ilość pamięci dla kontenera **analizy tekstu**| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| Sufiks identyfikatora URI analizy tonacji, cały identyfikator URI jest w formacie "http:// `<service>` : `<port>` / `<sentimentURISuffix>` ". | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| Typ usługi **analizy tekstu** w Kubernetes. Zobacz [typy usługi Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) | prawidłowy typ usługi Kubernetes | `LoadBalancer` |
|`textanalytics.service.port`| Port usługi **analizy tekstu**| int| `50085`|
|`textanalytics.service.annotations`| Adnotacje, które użytkownicy mogą dodawać do metadanych usługi **analizy tekstu** . Przykład:<br/> **adnotacj**<br/>`   `**część/annotation1: wartość1**<br/>`  `**część/annotation2: wartość2** | Adnotacje, jeden na każdy wiersz| |
|`textanalytics.serivce.autoScaler.enabled`| Określa, czy jest włączona funkcja [automatycznego skalowania w poziomie](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . Jeśli ta funkcja `text-analytics-autoscaler` jest włączona, zostanie wdrożona w klastrze Kubernetes | PRAWDA/FAŁSZ| `true`|
|`textanalytics.service.podDisruption.enabled`| Czy jest włączony [budżet z przerwy](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) w działaniu. Jeśli ta funkcja `text-analytics-poddisruptionbudget` jest włączona, zostanie wdrożona w klastrze Kubernetes| PRAWDA/FAŁSZ| `true`|
