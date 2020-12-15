---
title: Utwórz zależności wyzwalacza okna wirowania
description: Dowiedz się, jak utworzyć zależność dla wyzwalacza okna wirowania w Azure Data Factory.
services: data-factory
ms.author: chez
author: chez-charlie
manager: weetok
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/03/2020
ms.openlocfilehash: 221f8bd7908613812a728d420a68b747051e095b
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508802"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Tworzenie zależności wyzwalacza okna wirowania
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono procedurę tworzenia zależności w wyzwalaczu okna wirowania. Aby uzyskać ogólne informacje na temat wyzwalaczy okna wirowania, zobacz [jak utworzyć wyzwalacz okna wirowania](how-to-create-tumbling-window-trigger.md).

W celu utworzenia łańcucha zależności i upewnienia się, że wyzwalacz jest wykonywany tylko po pomyślnym wykonaniu innego wyzwalacza w fabryce danych, Użyj tej zaawansowanej funkcji, aby utworzyć zależność okna wirowania.

Aby zapoznać się z prezentacją dotyczącą tworzenia potoków zależnych w Azure Data Factory przy użyciu wyzwalacza okna wirowania, Obejrzyj następujący film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Tworzenie zależności w interfejsie użytkownika Data Factory

Aby utworzyć zależność od wyzwalacza, wybierz kolejno pozycje **wyzwalacze > zaawansowane > nowe**, a następnie wybierz wyzwalacz, który będzie zależny od odpowiedniego przesunięcia i rozmiaru. Wybierz pozycję **Zakończ** i Opublikuj zmiany w usłudze Fabryka danych, aby zależności zostały zastosowane.

![Tworzenie zależności](media/tumbling-window-trigger-dependency/tumbling-window-dependency-01.png "Tworzenie zależności")

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
| typ  | Wszystkie istniejące wyzwalacze okna wirowania są wyświetlane na liście rozwijanej. Wybierz wyzwalacz, od którego ma być zależne.  | TumblingWindowTriggerDependencyReference lub SelfDependencyTumblingWindowTriggerReference | Tak |
| przesunięcie | Przesunięcie wyzwalacza zależności. Podaj wartość w formacie przedziału czasu, a ujemne i pozytywne przesunięcia są dozwolone. Ta właściwość jest wymagana, jeśli wyzwalacz jest zależny od siebie i we wszystkich innych przypadkach jest opcjonalny. Samodzielna wartość powinna zawsze być ujemna. Jeśli żadna wartość nie zostanie określona, okno jest takie samo jak wyzwalacz. | Zakres czasu<br/>(hh: mm: SS) | Samozależność: tak<br/>Inne: nie |
| size | Rozmiar okna wirowania zależności. Podaj dodatnią wartość TimeSpan. Ta właściwość jest opcjonalna. | Zakres czasu<br/>(hh: mm: SS) | Nie  |

> [!NOTE]
> Wyzwalacz okna wirowania może zależeć od maksymalnie pięciu innych wyzwalaczy.

## <a name="tumbling-window-self-dependency-properties"></a>Właściwości samoobsługowego okna wirowania

W scenariuszach, w których wyzwalacz nie powinien przechodzić do następnego okna do momentu pomyślnego zakończenia poprzedniego okna, należy zbudować samodzielną zależność. Wyzwalacz samozależności, który zależy od sukcesu wcześniejszych przebiegów w ciągu poprzedniej godziny, będzie miał właściwości wskazane w poniższym kodzie.

> [!NOTE]
> Jeśli wyzwolony potok opiera się na danych wyjściowych potoków w wcześniej wyzwolonych oknach, zalecamy użycie tylko samoobsługowego wyzwalacza okna wirowania. Aby ograniczyć przebiegi wyzwalacza równoległego, ustaw współbieżność wyzwalacza maximimum.

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

![Przykład przesunięcia](media/tumbling-window-trigger-dependency/tumbling-window-dependency-02.png "Przykład przesunięcia")

### <a name="dependency-size"></a>Rozmiar zależności

![Przykład rozmiaru](media/tumbling-window-trigger-dependency/tumbling-window-dependency-03.png "Przykład rozmiaru")

### <a name="self-dependency"></a>Samozależność

![Samozależność](media/tumbling-window-trigger-dependency/tumbling-window-dependency-04.png "Samozależność")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Zależność od innego wyzwalacza okna wirowania

Codzienne zadanie przetwarzania danych telemetrycznych w zależności od innego codziennego zadania agregowania danych wyjściowych z ostatnich siedmiu dni i generowania siedmiu codziennych strumieni okna kroczącego:

![Przykład zależności](media/tumbling-window-trigger-dependency/tumbling-window-dependency-05.png "Przykład zależności")

### <a name="dependency-on-itself"></a>Zależność od samego siebie

Codzienne zadanie bez przerw w strumieniach wyjściowych zadania:

![Przykład samoobsługowy](media/tumbling-window-trigger-dependency/tumbling-window-dependency-06.png "Przykład samoobsługowy")

## <a name="monitor-dependencies"></a>Monitorowanie zależności

Można monitorować łańcuch zależności i odpowiadające im okna na stronie monitorowania uruchomienia wyzwalacza. Przejdź do  **monitorowania > uruchomienia wyzwalacza**. Jeśli wyzwalacz okna wirowania ma zależności, Nazwa wyzwalacza będzie zawierać hiperłącze do widoku monitorowanie zależności.  

![Monitorowanie uruchomień wyzwalacza](media/tumbling-window-trigger-dependency/tumbling-window-dependency-07.png "Monitorowanie uruchomień wyzwalacza — cały widok zależności okna wirowania")

Kliknij nazwę wyzwalacza, aby wyświetlić zależności wyzwalacza. Panel po prawej stronie zawiera szczegółowe informacje o uruchamianiu wyzwalacza, takie jak RunID, czas okna, stan itd.

![Widok listy zależności monitora](media/tumbling-window-trigger-dependency/tumbling-window-dependency-08.png "Widok listy zależności monitora")

Można zobaczyć stan zależności i systemu Windows dla każdego wyzwalacza zależnego. Jeśli jeden z wyzwalaczy zależności zakończy się niepowodzeniem, należy uruchomić go ponownie w celu uruchomienia wyzwalacza zależnego.

Wyzwalacz okna wirowania będzie oczekiwał na zależności przez _siedem dni_ przed upływem limitu czasu. Po upływie siedmiu dni uruchomienie wyzwalacza zakończy się niepowodzeniem.

Aby uzyskać więcej wizualizacji w celu wyświetlenia harmonogramu zależności wyzwalacza, wybierz widok wykresu Gantta.

![Wykres Gantta zależności monitora](media/tumbling-window-trigger-dependency/tumbling-window-dependency-09.png "Widok wykresu Gantta zależności monitora")

Pola przezroczyste pokazują okna zależności dla każdego wyłączonego wyzwalacza zależnego od strumienia, podczas gdy pełne kolorowe pola powyżej pokażą pojedyncze uruchomienia okna. Oto kilka porad dotyczących interpretacji widoku wykresu Gantta:

* Przezroczyste pole renderuje niebieską, gdy zależne okna są w stanie oczekiwania lub uruchamiania
* Gdy wszystkie okna zakończą się pomyślnie dla wyzwalacza zależnego, przezroczyste pole zmieni kolor na zielony
* Przezroczyste pole renderuje kolor czerwony, gdy okno zależne nie powiedzie się. Poszukaj pełnego czerwonego pola, aby zidentyfikować uruchomienie okna awarii

Aby ponownie uruchomić okno w widoku wykresu Gantta, zaznacz pole pełnego koloru dla okna, a panel akcji zostanie wyświetlony z informacjami i ponownymi uruchomieniami

## <a name="next-steps"></a>Następne kroki

* Zapoznaj [się z tematem jak utworzyć wyzwalacz okna wirowania](how-to-create-tumbling-window-trigger.md)
