---
title: Debugowanie & Rozwiązywanie problemów z potokami ML
titleSuffix: Azure Machine Learning
description: Debuguj potoki Azure Machine Learning w języku Python. Poznaj typowe pułapeky dla tworzenia potoków oraz porady ułatwiające debugowanie skryptów przed i podczas wykonywania zdalnego. Dowiedz się, jak używać Visual Studio Code do interaktywnego debugowania potoków uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: troubleshooting, devx-track-python
ms.openlocfilehash: be68ad35deca754df70bb51e83929e73ff132ba6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91315409"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego

W tym artykule dowiesz się, jak debugować i rozwiązywać problemy z [potokami uczenia maszynowego](concept-ml-pipelines.md) w [zestawach SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) i [Azure Machine Learning Designer](https://docs.microsoft.com/azure/machine-learning/concept-designer). Informacje na ten temat można znaleźć w tematach:

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

Poniższa tabela zawiera typowe problemy podczas tworzenia potoku z potencjalnymi rozwiązaniami.

| Problem | Możliwe rozwiązanie |
|--|--|
| Nie można przekazać danych do `PipelineData` katalogu | Upewnij się, że utworzono katalog w skrypcie, który odnosi się do miejsca, w którym potok oczekuje danych wyjściowych kroku. W większości przypadków argument wejściowy określi katalog wyjściowy, a następnie utworzysz katalog jawnie. Użyj polecenia `os.makedirs(args.output_dir, exist_ok=True)` , aby utworzyć katalog wyjściowy. Zapoznaj się z [samouczkiem](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) przykładowego skryptu oceniania, który pokazuje ten Wzorzec projektowy. |
| Błędy zależności | Jeśli w potoku zdalnym widoczne są błędy zależności, które nie były wykonywane podczas lokalnego testowania, potwierdź zależności środowiska zdalnego i wersje zgodne z wymaganiami w środowisku testowym. (Zobacz [Tworzenie środowiska, buforowanie i ponowne używanie](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Niejednoznaczne błędy z obiektami docelowymi obliczeń | Spróbuj usunąć i ponownie utworzyć cele obliczeniowe. Ponowne tworzenie obiektów docelowych obliczeń jest szybkie i może rozwiązać pewne problemy przejściowe. |
| Potok nie wykorzystał się z kroków | Ponowne użycie kroku jest włączone domyślnie, ale upewnij się, że nie zostało wyłączone w kroku potoku. Jeśli ponowne użycie jest wyłączone, `allow_reuse` parametr w kroku zostanie ustawiony na `False` . |
| Potok jest niepotrzebny. | Aby zapewnić, że kroki mają zostać uruchomione ponownie tylko wtedy, gdy ich dane podstawowe lub skrypty zmienią się, należy rozdzielić katalogi kodu źródłowego dla każdego kroku. Jeśli używasz tego samego katalogu źródłowego dla wielu kroków, może wystąpić niepotrzebne wykonanie ponownie. Użyj `source_directory` parametru w obiekcie krok potoku, aby wskazać odizolowany katalog dla tego kroku, i upewnij się, że nie używasz tej samej `source_directory` ścieżki dla wielu kroków. |
| Krok spowalniają pracę nad epoką szkoleniową lub innym zachowaniem zapętlenia | Spróbuj przełączyć wszystkie zapisy plików, w tym rejestrowanie, z `as_mount()` do `as_upload()` . Tryb **instalacji** używa zdalnego zwirtualizowanego systemu plików i przekazuje cały plik przy każdym dołączeniu do niego. |

## <a name="debugging-techniques"></a>Techniki debugowania

Istnieją trzy główne techniki dla potoków debugowania: 

* Debuguj poszczególne etapy potoku na komputerze lokalnym
* Użyj rejestrowania i Application Insights, aby wyizolować i zdiagnozować źródło problemu
* Dołącz zdalny debuger do potoku uruchomionego na platformie Azure

### <a name="debug-scripts-locally"></a>Lokalne debugowanie skryptów

Jeden z najczęstszych błędów w potoku polega na tym, że skrypt domeny nie działa zgodnie z oczekiwaniami lub zawiera błędy środowiska uruchomieniowego w zdalnym kontekście obliczeniowym, które są trudne do debugowania.

Potoki same nie mogą być uruchamiane lokalnie, ale uruchamianie skryptów w izolacji na komputerze lokalnym pozwala na szybsze debugowanie, ponieważ nie trzeba czekać na proces tworzenia i kompilowania środowiska. Aby to zrobić, należy wykonać następujące czynności:

* Jeśli dane są w magazynie danych w chmurze, musisz pobrać dane i udostępnić je dla skryptu. Użycie małego przykładu danych jest dobrym sposobem na wycinanie w czasie wykonywania i szybkie uzyskanie opinii na temat zachowania skryptu
* Jeśli podjęto próbę symulowania pośredniego etapu potoku, może być konieczne ręczne skompilowanie typów obiektów, których konkretny skrypt oczekuje od poprzedniego kroku
* Należy również zdefiniować własne środowisko i zreplikować zależności zdefiniowane w zdalnym środowisku obliczeniowym

Po skonfigurowaniu skryptu do uruchomienia w środowisku lokalnym można znacznie łatwiej wykonywać zadania debugowania, takie jak:

* Dołączanie konfiguracji debugowania niestandardowego
* Wstrzymywanie wykonywania i sprawdzania stanu obiektu
* Typ przechwytywania lub błędy logiczne, które nie zostaną ujawnione do czasu wykonania

> [!TIP] 
> Po sprawdzeniu, czy skrypt działa zgodnie z oczekiwaniami, dobrym następnym krokiem jest uruchomienie skryptu w potoku jednoetapowym przed podjęciem próby uruchomienia go w potoku z wieloma krokami.

## <a name="configure-write-to-and-review-pipeline-logs"></a>Konfigurowanie, zapisywanie i przeglądanie dzienników potoku

Testowanie skryptów lokalnie to doskonały sposób na Debugowanie głównych fragmentów kodu i złożonej logiki przed rozpoczęciem tworzenia potoku, ale w pewnym momencie prawdopodobnie trzeba będzie debugować skrypty w trakcie rzeczywistego uruchomienia potoku, szczególnie podczas diagnozowania zachowań występujących podczas interakcji między krokami potoku. Zalecamy korzystanie z niezliberalizowanych `print()` instrukcji w skryptach kroków, dzięki czemu można zobaczyć stan obiektu i oczekiwane wartości podczas wykonywania zdalnego, podobnie jak w przypadku debugowania kodu JavaScript.

### <a name="logging-options-and-behavior"></a>Opcje rejestrowania i zachowanie

Poniższa tabela zawiera informacje dotyczące różnych opcji debugowania potoków. Nie jest to pełna lista, ponieważ istnieją inne opcje oprócz Azure Machine Learning, Python i OpenCensus.

| Biblioteka                    | Type   | Przykład                                                          | Element docelowy                                  | Zasoby                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metryka | `run.log(name, val)`                                             | Interfejs użytkownika portalu Azure Machine Learning             | [Jak śledzić eksperymenty](how-to-track-experiments.md)<br>[Azure. Core. Run — Klasa](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true)                                                                                                                                                 |
| Drukowanie w języku Python/rejestrowanie    | Log    | `print(val)`<br>`logging.info(message)`                          | Dzienniki sterowników, Azure Machine Learning Designer | [Jak śledzić eksperymenty](how-to-track-experiments.md)<br><br>[Rejestrowanie w języku Python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| Zestaw OpenCensus Python          | Log    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights — ślady                | [Debugowanie potoków w usłudze Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Eksporterzy biblioteki OpenCensus usługi Azure Monitor](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Rejestrowanie w języku Python Cookbook](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Przykład opcji rejestrowania

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="azure-machine-learning-designer"></a>Projektant usługi Azure Machine Learning

W przypadku potoków utworzonych w projektancie można znaleźć plik **70_driver_log** na stronie Tworzenie lub na stronie szczegółów uruchomienia potoku.

### <a name="enable-logging-for-real-time-endpoints"></a>Włączanie rejestrowania dla punktów końcowych w czasie rzeczywistym

W celu rozwiązywania problemów i debugowania punktów końcowych w czasie rzeczywistym w projektancie należy włączyć rejestrowanie w usłudze Application Insights przy użyciu zestawu SDK. Rejestrowanie umożliwia rozwiązywanie problemów i debugowanie modeli oraz problemów z użyciem. Aby uzyskać więcej informacji, zobacz [Rejestrowanie wdrożonych modeli](how-to-enable-logging.md#logging-for-deployed-models). 

### <a name="get-logs-from-the-authoring-page"></a>Pobieranie dzienników ze strony tworzenia

Po przesłaniu uruchomienia potoku i pozostawania na stronie Tworzenie można znaleźć pliki dziennika wygenerowane dla każdego modułu, ponieważ każdy moduł kończy działanie.

1. Wybierz moduł, który zakończył działanie na kanwie tworzenia.
1. W prawym okienku modułu przejdź do karty dane  **wyjściowe i dzienniki** .
1. Rozwiń okienko po prawej stronie i wybierz **70_driver_log.txt** , aby wyświetlić plik w przeglądarce. Możesz również pobrać dzienniki lokalnie.

    ![Rozwinięte okienko danych wyjściowych w projektancie](./media/how-to-debug-pipelines/designer-logs.png)? View = Azure-ml-PR&zachować-View = true)? View = Azure-ml-PR&Preserve-View = true)

### <a name="get-logs-from-pipeline-runs"></a>Pobierz dzienniki z przebiegów potoku

Pliki dziennika dla określonych przebiegów można również znaleźć na stronie szczegółów uruchomienia potoku, która znajduje się w sekcji **potoki** lub **eksperymenty** w programie Studio.

1. Wybierz uruchomienie potoku utworzone w projektancie.

    ![Strona uruchomienia potoku](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Wybierz moduł w okienku podglądu.
1. W prawym okienku modułu przejdź do karty dane  **wyjściowe i dzienniki** .
1. Rozwiń okienko po prawej stronie, aby wyświetlić plik **70_driver_log.txt** w przeglądarce, lub wybierz plik, aby pobrać dzienniki lokalnie.

> [!IMPORTANT]
> Aby zaktualizować potok na stronie szczegółów uruchomienia potoku, należy **sklonować** uruchomienie potoku do nowej wersji roboczej potoku. Uruchomienie potoku jest migawką potoku. Jest on podobny do pliku dziennika i nie można go zmienić. 

## <a name="application-insights"></a>Application Insights
Aby uzyskać więcej informacji na temat korzystania z biblioteki OpenCensus Python w ten sposób, zobacz ten przewodnik: [debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego w Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Interaktywny debugowanie za pomocą Visual Studio Code

W niektórych przypadkach może być konieczne interaktywne Debugowanie kodu w języku Python używanego w potoku. Korzystając z Visual Studio Code (VS Code) i debugpy, można dołączać do kodu w trakcie jego działania w środowisku szkoleniowym. Aby uzyskać więcej informacji, zobacz [interaktywny debugowanie w przewodniku vs Code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać pomoc dotyczącą pakietu [Azure-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) oraz pakietem [kroków dla potoków usługi Azure](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true) .

* Zapoznaj się z listą [wyjątków projektanta i kodów błędów](algorithm-module-reference/designer-error-codes.md).
