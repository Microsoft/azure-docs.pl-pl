---
title: Utwórz zależności wyzwalacza okna wirowania
description: Dowiedz się, jak utworzyć zależność dla wyzwalacza okna wirowania w Azure Data Factory.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418800"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Tworzenie zależności wyzwalacza okna wirowania
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono procedurę tworzenia zależności w wyzwalaczu okna wirowania. Aby uzyskać ogólne informacje na temat wyzwalaczy okna wirowania, zobacz [jak utworzyć wyzwalacz okna wirowania](how-to-create-tumbling-window-trigger.md).

W celu utworzenia łańcucha zależności i upewnienia się, że wyzwalacz jest wykonywany tylko po pomyślnym wykonaniu innego wyzwalacza w fabryce danych, Użyj tej zaawansowanej funkcji, aby utworzyć zależność okna wirowania.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Tworzenie zależności w interfejsie użytkownika Data Factory

Aby utworzyć zależność od wyzwalacza, wybierz kolejno pozycje **wyzwalacze > zaawansowane > nowe**, a następnie wybierz wyzwalacz, który będzie zależny od odpowiedniego przesunięcia i rozmiaru. Wybierz pozycję **Zakończ** i Opublikuj zmiany w usłudze Fabryka danych, aby zależności zostały zastosowane.

![Tworzenie zależności](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Tworzenie zależności")

## <a name="tumbling-window-dependency-properties"></a>Właściwości zależności okna wirowania

Wyzwalacz okna wirowania z zależnością ma następujące właściwości:

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

Poniższa tabela zawiera listę atrybutów wymaganych do zdefiniowania zależności okna wirowania.

| **Nazwa właściwości** | **Opis**  | **Typ** | **Wymagane** |
|---|---|---|---|
| type  | Wszystkie istniejące wyzwalacze okna wirowania są wyświetlane na liście rozwijanej. Wybierz wyzwalacz, od którego ma być zależne.  | TumblingWindowTriggerDependencyReference lub SelfDependencyTumblingWindowTriggerReference | Tak |
| przesunięcie | Przesunięcie wyzwalacza zależności. Podaj wartość w formacie przedziału czasu, a ujemne i pozytywne przesunięcia są dozwolone. Ta właściwość jest wymagana, jeśli wyzwalacz jest zależny od siebie i we wszystkich innych przypadkach jest opcjonalny. Samodzielna wartość powinna zawsze być ujemna. Jeśli żadna wartość nie zostanie określona, okno jest takie samo jak wyzwalacz. | Zakres czasu<br/>(hh: mm: SS) | Samozależność: tak<br/>Inne: nie |
| size | Rozmiar okna wirowania zależności. Podaj dodatnią wartość TimeSpan. Ta właściwość jest opcjonalna. | Zakres czasu<br/>(hh: mm: SS) | Nie  |

> [!NOTE]
> Wyzwalacz okna wirowania może zależeć od maksymalnie dwóch innych wyzwalaczy.

## <a name="tumbling-window-self-dependency-properties"></a>Właściwości samoobsługowego okna wirowania

W scenariuszach, w których wyzwalacz nie powinien przechodzić do następnego okna do momentu pomyślnego zakończenia poprzedniego okna, należy zbudować samodzielną zależność. Wyzwalacz samozależności, który jest zależny od sukcesu wcześniejszych przebiegów w ramach poprzedniego elementu HR, będzie miał następujące właściwości:

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

Poniżej przedstawiono ilustracje scenariuszy i użycia właściwości zależności okna wirowania.

### <a name="dependency-offset"></a>Przesunięcie zależności

![Przykład przesunięcia](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Przykład przesunięcia")

### <a name="dependency-size"></a>Rozmiar zależności

![Przykład rozmiaru](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Przykład rozmiaru")

### <a name="self-dependency"></a>Samozależność

![Samozależność](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Samozależność")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Zależność od innego wyzwalacza okna wirowania

Codzienne zadanie przetwarzania danych telemetrycznych w zależności od innego codziennego zadania agregowania danych wyjściowych z ostatnich siedmiu dni i generowania siedmiu codziennych strumieni okna kroczącego:

![Przykład zależności](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Przykład zależności")

### <a name="dependency-on-itself"></a>Zależność od samego siebie

Codzienne zadanie bez przerw w strumieniach wyjściowych zadania:

![Przykład samoobsługowy](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Przykład samoobsługowy")

Aby zapoznać się z prezentacją dotyczącą tworzenia potoków zależnych w Azure Data Factory przy użyciu wyzwalacza okna wirowania, Obejrzyj następujący film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="monitor-dependencies"></a>Monitorowanie zależności

Można monitorować łańcuch zależności i odpowiadające im okna na stronie monitorowania uruchomienia wyzwalacza. Przejdź do **monitorowania > uruchomienia wyzwalacza**. W kolumnie akcje można ponownie uruchomić wyzwalacz lub wyświetlić jego zależności.

![Monitorowanie uruchomień wyzwalacza](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Monitorowanie uruchomień wyzwalacza")

Kliknięcie przycisku "Wyświetl zależności wyzwalacza" umożliwia wyświetlenie stanu zależności. Jeśli jeden z wyzwalaczy zależności ulegnie awarii, należy uruchomić go ponownie w celu uruchomienia wyzwalacza zależnego. Wyzwalacz okna wirowania będzie oczekiwał na zależności przez siedem dni przed upływem limitu czasu.

![Monitorowanie zależności](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Monitorowanie zależności")

Aby uzyskać więcej wizualizacji w celu wyświetlenia harmonogramu zależności wyzwalacza, wybierz widok wykresu Gantta.

![Monitorowanie zależności](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Monitorowanie zależności")

## <a name="next-steps"></a>Następne kroki

* Zapoznaj [się z tematem jak utworzyć wyzwalacz okna wirowania](how-to-create-tumbling-window-trigger.md)
