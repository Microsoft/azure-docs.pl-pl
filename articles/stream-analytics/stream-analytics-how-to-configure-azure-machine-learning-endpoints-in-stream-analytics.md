---
title: Używaj punktów końcowych Azure Machine Learning Studio (klasycznych) w Azure Stream Analytics
description: W tym artykule opisano sposób korzystania z funkcji zdefiniowanych przez użytkownika w języku maszynowym w programie Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/11/2019
ms.openlocfilehash: a36162d24e18371fdf6b19835e4748e3043d1f24
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012529"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Integracja Azure Machine Learning Studio (klasyczny) w Stream Analytics (wersja zapoznawcza)
Stream Analytics obsługuje funkcje zdefiniowane przez użytkownika, które wywołują Azure Machine Learning Studio (klasyczne) punkty końcowe. Obsługa interfejsu API REST dla tej funkcji jest szczegółowa w [bibliotece interfejsu API rest Stream Analytics](/rest/api/streamanalytics/). Ten artykuł zawiera dodatkowe informacje, które są odpowiednie do pomyślnej implementacji tej możliwości w Stream Analytics. Samouczek został również ogłoszony i jest dostępny w [tym miejscu](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Przegląd: Azure Machine Learning Studio (klasyczny) terminologia
Microsoft Azure Machine Learning Studio (klasyczny) to narzędzie do współpracy typu "przeciągnij i upuść", które służy do kompilowania, testowania i wdrażania rozwiązań analizy predykcyjnej na danych. To narzędzie jest nazywane *Azure Machine Learning Studio (klasyczne)*. Studio (klasyczny) służy do interakcji z zasobami uczenia maszynowego i łatwego kompilowania, testowania i iteracji w projekcie. Te zasoby i ich definicje są poniżej.

* **Obszar roboczy**: *obszar roboczy* to kontener, który zawiera wszystkie inne zasoby uczenia maszynowego w kontenerze do zarządzania i kontroli.
* **Eksperymentowanie**: *eksperymenty* są tworzone przez analityków danych do korzystania z zestawu danych i uczenia modelu uczenia maszynowego.
* **Punkt końcowy**: *punkty końcowe* to obiekt Studio (klasyczny) służący do podejmowania funkcji jako danych wejściowych, stosowania określonego modelu uczenia maszynowego i zwracania oceny danych wyjściowych.
* **Ocenianie** usługi sieci Web: usługa sieci *Web oceniania* jest kolekcją punktów końcowych, jak wspomniano powyżej.

Każdy punkt końcowy ma interfejsy API do wykonywania wsadowego i wykonywania synchronicznego. Stream Analytics używa wykonywania synchronicznego. Określona usługa nosi nazwę [usługi żądanie/odpowiedź](../machine-learning/classic/consume-web-services.md) w Azure Machine Learning Studio (klasyczny).

## <a name="studio-classic-resources-needed-for-stream-analytics-jobs"></a>Zasoby programu Studio (klasyczne) są niezbędne dla zadań Stream Analytics
Na potrzeby przetwarzania zadań Stream Analytics, punkt końcowy żądania/odpowiedzi, [apikey](../machine-learning/classic/consume-web-services.md)i Definicja struktury Swagger są niezbędne do pomyślnego wykonania. Stream Analytics ma dodatkowy punkt końcowy, który konstruuje adres URL dla punktu końcowego struktury Swagger, wyszukuje interfejs i zwraca domyślną definicję UDF do użytkownika.

## <a name="configure-a-stream-analytics-and-studio-classic-udf-via-rest-api"></a>Konfigurowanie Stream Analytics i Studio (klasycznego) UDF za pośrednictwem interfejsu API REST
Za pomocą interfejsów API REST można skonfigurować zadanie do wywoływania funkcji Studio (klasycznych). Kroki tego procesu są następujące:

1. Tworzenie zadania usługi Stream Analytics
2. Zdefiniuj dane wejściowe
3. Definiowanie danych wyjściowych
4. Tworzenie funkcji zdefiniowanej przez użytkownika (UDF)
5. Napisz transformację Stream Analytics, która wywołuje funkcję UDF
6. Uruchamianie zadania

## <a name="creating-a-udf-with-basic-properties"></a>Tworzenie elementu UDF z podstawowymi właściwościami
Na przykład następujący przykładowy kod tworzy skalarną UDF o nazwie *newudf* , która wiąże się z punktem końcowym Azure Machine Learning Studio (klasycznym). Należy pamiętać, że *punkt końcowy* (identyfikator URI usługi) można znaleźć na stronie pomocy interfejsu API dla wybranej usługi, a *apiKey* można znaleźć na stronie głównej usług.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Przykład treści żądania:

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Wywoływanie punktu końcowego RetrieveDefaultDefinition dla domyślnego formatu UDF
Po utworzeniu szkieletu UDF jest wymagana kompletna definicja UDF. Punkt końcowy RetrieveDefaultDefinition pomaga uzyskać definicję domyślną dla funkcji skalarnej, która jest powiązana z Azure Machine Learning Studio (klasyczny) punkt końcowy. Poniższy ładunek wymaga pobrania domyślnej definicji UDF dla funkcji skalarnej, która jest powiązana z punktem końcowym Studio (klasycznego). Nie określa faktycznego punktu końcowego, ponieważ został już podany podczas żądania PUT. Stream Analytics wywołuje punkt końcowy podany w żądaniu, jeśli został podany jawnie. W przeciwnym razie używa tego samego odwołania. W tym miejscu funkcja UDF przyjmuje jeden parametr ciągu (zdanie) i zwraca pojedyncze dane wyjściowe typu String, który wskazuje etykietę "tonacji" dla tego zdania.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Przykład treści żądania:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Przykładowe dane wyjściowe wyglądają poniżej.

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>Poprawka UDF z odpowiedzią
Teraz należy zastosować poprawki do formatu UDF z poprzednią odpowiedzią, jak pokazano poniżej.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Treść żądania (dane wyjściowe z RetrieveDefaultDefinition):

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Zaimplementuj transformację Stream Analytics, aby wywołać metodę UDF
Teraz wykonaj zapytanie dotyczące formatu UDF (tutaj o nazwie scoreTweet) dla każdego zdarzenia wejściowego i Napisz odpowiedź dla tego zdarzenia do danych wyjściowych.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Uzyskaj pomoc
Aby uzyskać dalszą pomoc, wypróbuj nasze [&stronie pytań na temat Azure Stream Analytics](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)