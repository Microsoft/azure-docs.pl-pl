---
title: Przekaż informacje o wyzwalaczu do potoku
description: Dowiedz się, jak odwoływać się do metadanych wyzwalacza w potoku
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: ''
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 50a9f9cd59ebeecae89580c878442eb20788f462
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593649"
---
# <a name="reference-trigger-metadata-in-pipeline-runs"></a>Odwołuje się do metadanych wyzwalacza w uruchomieniach potoków

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób, w jaki metadane wyzwalacza, takie jak godzina uruchomienia wyzwalacza, mogą być używane w ramach uruchomienia potoku.

Potok czasami musi zrozumieć i odczytuje metadane z wyzwalacza, który go wywołuje. Na przykład w przypadku uruchomienia wyzwalacza okna wirowania na podstawie czasu rozpoczęcia i zakończenia okna potok przetworzy różne wycinki danych lub foldery. W Azure Data Factory używany jest parametryzacja i [zmienna systemowa](control-flow-system-variables.md) do przekazywania metadanych z wyzwalacza do potoku.

Ten wzorzec jest szczególnie przydatny dla [wyzwalacza okna wirowania](how-to-create-tumbling-window-trigger.md), gdzie wyzwalacz udostępnia czas rozpoczęcia i zakończenia okna oraz [wyzwalacz zdarzenia niestandardowego](how-to-create-custom-event-trigger.md), gdzie wyzwalacz analizuje i przetwarza wartości w [polu _dane_ niestandardowe zdefiniowane](../event-grid/event-schema.md).

> [!NOTE]
> Inny typ wyzwalacza zawiera różne informacje meta data. Aby uzyskać więcej informacji, zobacz [zmienna systemowa](control-flow-system-variables.md)

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory

W tej sekcji pokazano, jak przekazać informacje meta z wyzwalacza do potoku w interfejsie użytkownika Azure Data Factory.

1. Przejdź do **kanwy tworzenia** i edytuj potok

1. Kliknij pustą kanwę, aby wyświetlić ustawienia potoku. Nie wybieraj żadnego działania. Może być konieczne wyciągnięcie panelu ustawień u dołu kanwy, ponieważ mogło ono zostać zwinięte.

1. Wybierz sekcję **Parametry** i wybierz pozycję **+ Nowy** , aby dodać parametry

    :::image type="content" source="media/how-to-use-trigger-parameterization/01-create-parameter.png" alt-text="Zrzut ekranu przedstawiający ustawienie potoku pokazujące sposób definiowania parametrów w potoku.":::

1. Dodaj wyzwalacze do potoku, klikając pozycję **+ wyzwalacz**.

1. Utwórz lub Dołącz wyzwalacz do potoku, a następnie kliknij przycisk **OK** .

1. Na poniższej stronie wypełnij metadane wyzwalacza dla każdego parametru. Użyj formatu zdefiniowanego w [zmiennej systemowej](control-flow-system-variables.md) , aby pobrać informacje o wyzwalaczu. Nie musisz wprowadzać informacji dla wszystkich parametrów, tylko te, które będą przyjmować wartości metadanych wyzwalacza. Na przykład w tym miejscu przypiszemy czas rozpoczęcia uruchomienia wyzwalacza do *parameter_1*.

    :::image type="content" source="media/how-to-use-trigger-parameterization/02-pass-in-system-variable.png" alt-text="Zrzut ekranu przedstawiający stronę definicji wyzwalacza przedstawiającą sposób przekazywania informacji o wyzwalaczu do parametrów potoku.":::

1. Aby użyć wartości w potoku, użyj parametrów _@pipeline (). Parameters. ParameterName_, a __nie__ zmiennej systemowej w definicjach potoku. Na przykład w naszym przypadku, aby odczytać czas rozpoczęcia wyzwalacza, będziemy odwoływać się do @pipeline () .Parameters.parameter_1.

## <a name="json-schema"></a>Schemat JSON

Aby przekazać informacje o wyzwalaczu do uruchomień potoków, należy zaktualizować wyzwalacz i kod JSON potoku przy użyciu sekcji _Parametry_ .

### <a name="pipeline-definition"></a>Definicja potoku

W sekcji **Właściwości** Dodaj definicje parametrów do sekcji **Parameters**

```json
{
    "name": "demo_pipeline",
    "properties": {
        "activities": [
            {
                "name": "demo_activity",
                "type": "WebActivity",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "url": {
                        "value": "@pipeline().parameters.parameter_2",
                        "type": "Expression"
                    },
                    "method": "GET"
                }
            }
        ],
        "parameters": {
            "parameter_1": {
                "type": "string"
            },
            "parameter_2": {
                "type": "string"
            },
            "parameter_3": {
                "type": "string"
            },
            "parameter_4": {
                "type": "string"
            },
            "parameter_5": {
                "type": "string"
            }
        },
        "annotations": [],
        "lastPublishTime": "2021-02-24T03:06:23Z"
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

### <a name="trigger-definition"></a>Definicja wyzwalacza

W sekcji **potoki** Przypisz wartości parametrów w sekcji **Parametry** . Nie musisz wprowadzać informacji dla wszystkich parametrów, tylko te, które będą przyjmować wartości metadanych wyzwalacza.

```json
{
    "name": "trigger1",
    "properties": {
        "annotations": [],
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "demo_pipeline",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "parameter_1": "@trigger().startTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Minute",
                "interval": 15,
                "startTime": "2021-03-03T04:38:00Z",
                "timeZone": "UTC"
            }
        }
    }
}
```

### <a name="use-trigger-information-in-pipeline"></a>Użyj informacji o wyzwalaczu w potoku

Aby użyć wartości w potoku, użyj parametrów _@pipeline (). Parameters. ParameterName_, a __nie__ zmiennej systemowej w definicjach potoku.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje na temat wyzwalaczy, zobacz [wykonywanie i wyzwalacze potoku](concepts-pipeline-execution-triggers.md#trigger-execution).
