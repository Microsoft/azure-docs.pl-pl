---
title: Tworzenie zależności wyzwalaczy okna tumbling
description: Dowiedz się, jak utworzyć zależność od wyzwalacza okna z okienka w usłudze Azure Data Factory.
services: data-factory
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/29/2019
ms.openlocfilehash: 39ea8dda0fd823d3061b2cb29e1c548f99281c82
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418800"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Tworzenie zależności wyzwalacza okna wirowania
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ten artykuł zawiera kroki, aby utworzyć zależność od wyzwalacza okna brzuszka. Aby uzyskać ogólne informacje na temat wyzwalaczy okna tumbling, zobacz [Jak utworzyć wyzwalacz okna tumbling](how-to-create-tumbling-window-trigger.md).

Aby utworzyć łańcuch zależności i upewnij się, że wyzwalacz jest wykonywany dopiero po pomyślnym wykonaniu innego wyzwalacza w fabryce danych, użyj tej zaawansowanej funkcji, aby utworzyć zależność okna tumbling.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Tworzenie zależności w interfejsie użytkownika fabryki danych

Aby utworzyć zależność od wyzwalacza, wybierz **opcję Wyzwalaj > Zaawansowane > Nowy**, a następnie wybierz wyzwalacz, od który ma zależeć przy odpowiednim przesunięciu i rozmiarze. Wybierz **zakończ** i opublikuj zmiany fabryczne danych dla zależności, które mają być skuteczne.

![Tworzenie zależności](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Tworzenie zależności")

## <a name="tumbling-window-dependency-properties"></a>Właściwości zależności okna tumbling

Wyzwalacz okna z zależnością ma następujące właściwości:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

Poniższa tabela zawiera listę atrybutów potrzebnych do zdefiniowania zależności okna tumbling.

| **Nazwa właściwości** | **Opis**  | **Typ** | **Wymagane** |
|---|---|---|---|
| type  | Wszystkie istniejące wyzwalacze okna tumbling są wyświetlane w tej listy rozwijanej. Wybierz wyzwalacz, na który ma być zależna.  | TumblingWindowTriggerDependencyReference lub SelfDependencyTumblingWindowTriggerReference | Tak |
| przesunięcie | Przesunięcie wyzwalacza zależności. Podaj wartość w formacie przedziału czasu i dopuszczalne są zarówno ujemne, jak i dodatnie przesunięcia. Ta właściwość jest obowiązkowe, jeśli wyzwalacz zależy od siebie i we wszystkich innych przypadkach jest opcjonalne. Współzależność własna powinna być zawsze przesunięciem ujemnym. Jeśli nie określono wartości, okno jest taka sama jak sam wyzwalacz. | Zakres czasu<br/>(hh:mm:ss) | Zależność własna: Tak<br/>Inny: Nie |
| size | Rozmiar okna tumbling zależności. Podaj dodatnią wartość timespan. Ta właściwość jest opcjonalna. | Zakres czasu<br/>(hh:mm:ss) | Nie  |

> [!NOTE]
> Wyzwalacz okna tumbling może zależeć od maksymalnie dwóch innych wyzwalaczy.

## <a name="tumbling-window-self-dependency-properties"></a>Właściwości samozależności okna

W scenariuszach, w których wyzwalacz nie należy przejść do następnego okna, dopóki poprzednie okno nie zostanie pomyślnie ukończone, skompiluj zależność samodzielną. Wyzwalacz współzależności, który jest zależny od powodzenia wcześniejszych przebiegów w ciągu poprzedniego hr będzie miał następujące właściwości:

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Scenariusze użycia i przykłady

Poniżej znajdują się ilustracje scenariuszy i użycia właściwości zależności okna tumbling.

### <a name="dependency-offset"></a>Przesunięcie zależności

![Przykład przesunięcia](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Przykład przesunięcia")

### <a name="dependency-size"></a>Rozmiar zależności

![Przykład rozmiaru](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Przykład rozmiaru")

### <a name="self-dependency"></a>Zależność własna

![Zależność własna](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Zależność własna")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Zależność od innego wyzwalacza okna

Zadanie przetwarzania danych telemetrycznych dziennie w zależności od innego dziennego zadania agregującego dane wyjściowe z ostatnich siedmiu dni i generuje siedmiodniowe strumienie okien rolowanych:

![Przykład zależności](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Przykład zależności")

### <a name="dependency-on-itself"></a>Zależność od siebie

Codzienne zadanie bez przerw w strumieniach wyjściowych zadania:

![Przykład zależności własnej](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Przykład zależności własnej")

Aby zademonstrować sposób tworzenia potoków zależnych w fabryce danych platformy Azure przy użyciu wyzwalacza okna włączania, obejrzyj następujący klip wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="monitor-dependencies"></a>Monitorowanie zależności

Można monitorować łańcuch zależności i odpowiednie okna ze strony monitorowania uruchamiania wyzwalacza. Przejdź do **monitorowania > uruchomień wyzwalacza**. W kolumnie akcje można ponownie uruchomić wyzwalacz lub wyświetlić jego zależności.

![Monitorowanie uruchomień wyzwalacza](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Monitorowanie uruchomień wyzwalacza")

Jeśli klikniesz na "Wyświetl zależności wyzwalania", możesz zobaczyć stan zależności. Jeśli jeden z wyzwalaczy zależności nie powiedzie się, należy pomyślnie uruchomić go ponownie, aby wyzwalacz zależny uruchomić. Wyzwalacz okna tumbling będzie czekać na zależności przez siedem dni przed limit czasu.

![Monitorowanie zależności](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Monitorowanie zależności")

Aby uzyskać bardziej wizualne, aby wyświetlić harmonogram zależności wyzwalacza, wybierz widok Gantta.

![Monitorowanie zależności](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Monitorowanie zależności")

## <a name="next-steps"></a>Następne kroki

* Przejrzyj [jak utworzyć wyzwalacz okna tumbling](how-to-create-tumbling-window-trigger.md)
