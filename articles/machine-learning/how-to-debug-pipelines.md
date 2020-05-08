---
title: Debugowanie & Rozwiązywanie problemów z potokami ML
titleSuffix: Azure Machine Learning
description: Debuguj potoki Azure Machine Learning w języku Python. Poznaj typowe pułapeky dla tworzenia potoków oraz porady ułatwiające debugowanie skryptów przed i podczas wykonywania zdalnego. Dowiedz się, jak używać Visual Studio Code do interaktywnego debugowania potoków uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: 4f0eb6aa92dd8999baed6868a159c86d5e7bd0c8
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594653"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak debugować i rozwiązywać problemy z [potokami uczenia maszynowego](concept-ml-pipelines.md) w [zestawach SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) i [Azure Machine Learning Designer (wersja zapoznawcza)](https://docs.microsoft.com/azure/machine-learning/concept-designer). Informacje na ten temat można znaleźć w tematach:

* Debugowanie przy użyciu zestawu SDK Azure Machine Learning
* Debugowanie za pomocą narzędzia Azure Machine Learning Designer
* Debuguj przy użyciu Application Insights
* Debuguj interaktywnie przy użyciu Visual Studio Code (VS Code) i Python Tools for Visual Studio (PTVSD)

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Debugowanie i rozwiązywanie problemów w zestawie Azure Machine Learning SDK
Poniższe sekcje zawierają omówienie typowych pułapek podczas kompilowania potoków oraz różnych strategii debugowania kodu działającego w potoku. Jeśli masz problemy z uruchamianiem potoku zgodnie z oczekiwaniami, Skorzystaj z poniższych wskazówek.

### <a name="testing-scripts-locally"></a>Lokalne testowanie skryptów

Jeden z najczęstszych błędów w potoku polega na tym, że dołączony skrypt (skrypt czyszczący dane, skrypt oceniania itp.) nie jest uruchomiony zgodnie z oczekiwaniami lub zawiera błędy środowiska uruchomieniowego w zdalnym kontekście obliczeniowym, które są trudne do debugowania w obszarze roboczym w Azure Machine Learning Studio. 

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

### <a name="debugging-scripts-from-remote-context"></a>Debugowanie skryptów z kontekstu zdalnego

Testowanie skryptów lokalnie to doskonały sposób na Debugowanie głównych fragmentów kodu i złożonej logiki przed rozpoczęciem tworzenia potoku, ale w pewnym momencie prawdopodobnie trzeba będzie debugować skrypty w trakcie rzeczywistego uruchomienia potoku, szczególnie podczas diagnozowania zachowań występujących podczas interakcji między krokami potoku. Zalecamy korzystanie z `print()` niezliberalizowanych instrukcji w skryptach kroków, dzięki czemu można zobaczyć stan obiektu i oczekiwane wartości podczas wykonywania zdalnego, podobnie jak w przypadku debugowania kodu JavaScript.

Plik `70_driver_log.txt` dziennika zawiera: 

* Wszystkie wydrukowane instrukcje podczas wykonywania skryptu
* Ślad stosu dla skryptu 

Aby znaleźć te i inne pliki dziennika w portalu, najpierw kliknij potok uruchomiony w obszarze roboczym.

![Strona listy uruchomień potoku](./media/how-to-debug-pipelines/pipelinerun-01.png)

Przejdź do strony szczegółów uruchomienia potoku.

![Strona szczegółów uruchomienia potoku](./media/how-to-debug-pipelines/pipelinerun-02.png)

Kliknij moduł dla określonego kroku. Przejdź do karty **dzienniki** . Inne dzienniki zawierają informacje o procesie kompilacji obrazu środowiska i skryptach przygotowania krokowego.

![Karta dziennika strony szczegółów uruchomienia potoku](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Przebiegi *opublikowanych potoków* można znaleźć na karcie **punkty końcowe** w obszarze roboczym. Przebiegi dla *nieopublikowanych potoków* można znaleźć w **eksperymentach** lub **potokach**.

### <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

Poniższa tabela zawiera typowe problemy podczas tworzenia potoku z potencjalnymi rozwiązaniami.

| Problem | Możliwe rozwiązanie |
|--|--|
| Nie można przekazać danych do `PipelineData` katalogu | Upewnij się, że utworzono katalog w skrypcie, który odnosi się do miejsca, w którym potok oczekuje danych wyjściowych kroku. W większości przypadków argument wejściowy określi katalog wyjściowy, a następnie utworzysz katalog jawnie. Użyj `os.makedirs(args.output_dir, exist_ok=True)` polecenia, aby utworzyć katalog wyjściowy. Zapoznaj się z [samouczkiem](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) przykładowego skryptu oceniania, który pokazuje ten Wzorzec projektowy. |
| Błędy zależności | Jeśli lokalnie opracowano i przetestowano skrypty, ale występują problemy z zależnościami podczas uruchamiania na zdalnym obliczeniu w potoku, upewnij się, że zależności środowiska obliczeniowego i wersje są zgodne ze środowiskiem testowym. (Zobacz [Tworzenie środowiska, buforowanie i ponowne używanie](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Niejednoznaczne błędy z obiektami docelowymi obliczeń | Usunięcie i ponowne utworzenie obiektów docelowych obliczeń może rozwiązać pewne problemy związane z obiektami docelowymi obliczeń. |
| Potok nie wykorzystał się z kroków | Ponowne użycie kroku jest włączone domyślnie, ale upewnij się, że nie zostało wyłączone w kroku potoku. Jeśli ponowne użycie jest wyłączone, `allow_reuse` parametr w kroku zostanie ustawiony na `False`. |
| Potok jest niepotrzebny. | Aby zapewnić, że kroki mają zostać uruchomione ponownie tylko wtedy, gdy ich dane podstawowe lub skrypty zmienią się, należy rozdzielić katalogi dla każdego kroku. Jeśli używasz tego samego katalogu źródłowego dla wielu kroków, może wystąpić niepotrzebne wykonanie ponownie. Użyj `source_directory` parametru w obiekcie krok potoku, aby wskazać odizolowany katalog dla tego kroku, i upewnij się, że nie używasz `source_directory` tej samej ścieżki dla wielu kroków. |

### <a name="logging-options-and-behavior"></a>Opcje rejestrowania i zachowanie

Poniższa tabela zawiera informacje dotyczące różnych opcji debugowania potoków. Nie jest to pełna lista, ponieważ istnieją inne opcje oprócz Azure Machine Learning, Python i OpenCensus.

| Biblioteka                    | Typ   | Przykład                                                          | Element docelowy                                  | Zasoby                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metryka | `run.log(name, val)`                                             | Interfejs użytkownika portalu Azure Machine Learning             | [Jak śledzić eksperymenty](how-to-track-experiments.md#available-metrics-to-track)<br>[Azure. Core. Run — Klasa](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Drukowanie w języku Python/rejestrowanie    | Log    | `print(val)`<br>`logging.info(message)`                          | Dzienniki sterowników, Azure Machine Learning Designer | [Jak śledzić eksperymenty](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Rejestrowanie w języku Python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Log    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights — ślady                | [Debugowanie potoków w usłudze Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Eksporterzy biblioteki OpenCensus usługi Azure Monitor](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Rejestrowanie w języku Python Cookbook](https://docs.python.org/3/howto/logging-cookbook.html) |

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

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Debugowanie i rozwiązywanie problemów w programie Azure Machine Learning Designer (wersja zapoznawcza)

Ta sekcja zawiera omówienie sposobu rozwiązywania problemów z potokami w projektancie. W przypadku potoków utworzonych w projektancie można znaleźć plik **70_driver_log** na stronie Tworzenie lub na stronie szczegółów uruchomienia potoku.

### <a name="get-logs-from-the-authoring-page"></a>Pobieranie dzienników ze strony tworzenia

Po przesłaniu uruchomienia potoku i pozostawania na stronie Tworzenie można znaleźć pliki dziennika wygenerowane dla każdego modułu, ponieważ każdy moduł kończy działanie.

1. Wybierz moduł, który zakończył działanie na kanwie tworzenia.
1. W prawym okienku modułu przejdź do karty dane **wyjściowe i dzienniki** .
1. Rozwiń okienko po prawej stronie i wybierz plik **70_driver_log. txt** , aby wyświetlić go w przeglądarce. Możesz również pobrać dzienniki lokalnie.

    ![Rozwinięte okienko danych wyjściowych w projektancie](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Pobierz dzienniki z przebiegów potoku

Pliki dziennika dla określonych przebiegów można również znaleźć na stronie szczegółów uruchomienia potoku, która znajduje się w sekcji **potoki** lub **eksperymenty** w programie Studio.

1. Wybierz uruchomienie potoku utworzone w projektancie.

    ![Strona uruchomienia potoku](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Wybierz moduł w okienku podglądu.
1. W prawym okienku modułu przejdź do karty dane **wyjściowe i dzienniki** .
1. Rozwiń okienko po prawej stronie, aby wyświetlić plik **70_driver_log. txt** w przeglądarce, lub wybierz plik, aby pobrać dzienniki lokalnie.

> [!IMPORTANT]
> Aby zaktualizować potok na stronie szczegółów uruchomienia potoku, należy **sklonować** uruchomienie potoku do nowej wersji roboczej potoku. Uruchomienie potoku jest migawką potoku. Jest on podobny do pliku dziennika i nie można go zmienić. 

## <a name="debug-and-troubleshoot-in-application-insights"></a>Debugowanie i rozwiązywanie problemów w Application Insights
Aby uzyskać więcej informacji na temat korzystania z biblioteki OpenCensus Python w ten sposób, zobacz ten przewodnik: [debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego w Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Debugowanie i rozwiązywanie problemów w Visual Studio Code

W niektórych przypadkach może być konieczne interaktywne Debugowanie kodu w języku Python używanego w potoku. Korzystając z Visual Studio Code (VS Code) i Python Tools for Visual Studio (PTVSD), możesz dołączyć kod w trakcie jego działania w środowisku szkoleniowym.

### <a name="prerequisites"></a>Wymagania wstępne

* __Obszar roboczy Azure Machine Learning__ skonfigurowany do korzystania z __usługi Azure Virtual Network__.
* __Potok Azure Machine Learning__ , który używa skryptów języka Python w ramach kroków potoku. Na przykład PythonScriptStep.
* Azure Machine Learning klaster obliczeniowy, który znajduje się __w sieci wirtualnej__ i jest __używany przez potok do szkoleń__.
* __Środowisko programistyczne__ znajdujące się __w sieci wirtualnej__. Środowisko programistyczne może być jednym z następujących:

    * Maszyna wirtualna platformy Azure w sieci wirtualnej
    * Wystąpienie obliczeniowe maszyny wirtualnej notesu w sieci wirtualnej
    * Komputer kliencki połączony z siecią wirtualną przez wirtualną sieć prywatną (VPN).

Aby uzyskać więcej informacji na temat używania Virtual Network platformy Azure z usługą Azure Machine Learning, zobacz [bezpieczne zadania eksperymentowania i wnioskowania usługi Azure ml w ramach Virtual Network platformy Azure](how-to-enable-virtual-network.md).

### <a name="how-it-works"></a>Jak to działa

Kroki potoku w sieci Te skrypty są modyfikowane w celu wykonania następujących czynności:
    
1. Zarejestruj adres IP hosta, na którym są uruchomione. Użyj adresu IP, aby połączyć debuger ze skryptem.

2. Uruchom składnik debugowania PTVSD i poczekaj, aż debuger nawiąże połączenie.

3. W środowisku deweloperskim Monitoruj dzienniki utworzone przez proces szkoleń, aby znaleźć adres IP, na którym skrypt jest uruchomiony.

4. Poinformujesz VS Code adres IP, aby połączyć debugera z programem przy użyciu `launch.json` pliku.

5. Debuger i interaktywny krok można dołączyć do skryptu.

### <a name="configure-python-scripts"></a>Konfigurowanie skryptów języka Python

Aby włączyć debugowanie, wprowadź następujące zmiany w skryptach języka Python, które są używane przez kroki w potoku:

1. Dodaj następujące instrukcje importu:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Dodaj następujące argumenty. Te argumenty umożliwiają włączenie debugera w razie potrzeby i ustawienie limitu czasu na potrzeby dołączania debugera:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Dodaj następujące instrukcje. Te instrukcje ładują bieżący kontekst uruchomienia, aby można było zarejestrować adres IP węzła, na którym uruchomiono kod:

    ```python
    global run
    run = Run.get_context()
    ```

1. Dodaj `if` instrukcję, która uruchamia PTVSD i czeka na dołączenie debugera. Jeśli żaden debuger nie zostanie dołączony przed upływem limitu czasu, skrypt będzie kontynuowany w normalny sposób.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        ip = socket.gethostbyname(socket.gethostname())
        print(f'ip_address: {ip}')
        ptvsd.enable_attach(address=('0.0.0.0', 5678),
                            redirect_output=True)
        # Wait for the timeout for debugger to attach
        ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
        print(f'Debugger attached = {ptvsd.is_attached()}')
    ```

W poniższym przykładzie w języku Python przedstawiono `train.py` podstawowy plik, który umożliwia debugowanie:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import ptvsd
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    ip = socket.gethostbyname(socket.gethostname())
    print(f'ip_address: {ip}')
    ptvsd.enable_attach(address=('0.0.0.0', 5678),
                        redirect_output=True)
    # Wait for the timeout for debugger to attach
    ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
    print(f'Debugger attached = {ptvsd.is_attached()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Konfigurowanie potoku ML

Aby udostępnić pakiety języka Python, które są konieczne do uruchomienia programu PTVSD i uzyskać kontekst uruchamiania, Utwórz środowisko `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`i ustaw. Zmień wersję zestawu SDK, aby była zgodna z tą, której używasz. Poniższy fragment kodu przedstawia sposób tworzenia środowiska:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['ptvsd', 'azureml-sdk==1.0.83'])
```

W sekcji [Konfigurowanie skryptów języka Python](#configure-python-scripts) dwa nowe argumenty zostały dodane do skryptów używanych przez etapy potoku. Poniższy fragment kodu ilustruje, jak używać tych argumentów, aby włączyć debugowanie dla składnika i ustawić limit czasu. Pokazano w nim także, jak używać środowiska utworzonego wcześniej przez ustawienie `runconfig=run_config`:

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300],
                         compute_target=aml_compute, 
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

Po uruchomieniu potoku każdy krok tworzy podrzędny przebieg. Jeśli debugowanie jest włączone, zmodyfikowany skrypt rejestruje informacje podobne do następującego tekstu w elemencie `70_driver_log.txt` dla uruchomienia podrzędnego:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Zapisz `ip_address` wartość. Jest on używany w następnej sekcji.

> [!TIP]
> Możesz również znaleźć adres IP z dzienników uruchamiania dla tego kroku potoku. Aby uzyskać więcej informacji o wyświetlaniu tych informacji, zobacz [monitorowanie przebiegów eksperymentów i metryk usługi Azure ml](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

1. Aby zainstalować Python Tools for Visual Studio (PTVSD) w VS Code środowisku deweloperskim, użyj następującego polecenia:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Aby uzyskać więcej informacji na temat używania PTVSD z VS Code, zobacz [debugowanie zdalne](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Aby skonfigurować VS Code do komunikowania się z Azure Machine Learning obliczeń, w których działa debuger, Utwórz nową konfigurację debugowania:

    1. W obszarze VS Code wybierz menu __Debuguj__ , a następnie wybierz pozycję __Otwórz konfiguracje__. Zostanie otwarty plik o nazwie __Launch. JSON__ .

    1. W pliku __Launch. JSON__ Znajdź wiersz, który zawiera `"configurations": [`, i Wstaw następujący tekst po nim. Zmień `"host": "10.3.0.5"` wpis na adres IP zwrócony w dziennikach z poprzedniej sekcji. Zmień `"localRoot": "${workspaceFolder}/code/step"` wpis na katalog lokalny, który zawiera kopię debugowanego skryptu:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "10.3.0.5",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Jeśli w sekcji konfiguracje znajdują się już inne wpisy, Dodaj przecinek (,) po wstawionym kodzie.

        > [!TIP]
        > Najlepszym rozwiązaniem jest utrzymywanie zasobów dla skryptów w oddzielnych katalogach, co oznacza, że przykładowe `localRoot` odwołania `/code/step1`do wartości.
        >
        > W przypadku debugowania wielu skryptów w różnych katalogach należy utworzyć osobną sekcję konfiguracyjną dla każdego skryptu.

    1. Zapisz plik __Launch. JSON__ .

### <a name="connect-the-debugger"></a>Podłącz debuger

1. Otwórz VS Code i Otwórz lokalną kopię skryptu.
2. Ustaw punkty przerwania, w których skrypt ma zostać zatrzymany po dołączeniu.
3. Podczas gdy proces podrzędny uruchamia skrypt, a w dziennikach `Timeout for debug connection` jest wyświetlany, użyj klawisza F5 lub wybierz opcję __Debuguj__. Po wyświetleniu monitu wybierz konfigurację __Azure Machine Learning COMPUTE: Remote Debug__ . Możesz również wybrać ikonę debugowania z paska bocznego, __Azure Machine Learning: zdalnego debugowania__ z menu rozwijanego debugowanie, a następnie użyć zieloną strzałkę, aby dołączyć debuger.

    W tym momencie VS Code nawiązuje połączenie z usługą PTVSD w węźle obliczeniowym i zatrzyma się na ustawionym wcześniej punkcie przerwania. Teraz można przechodzić przez kod w trakcie jego uruchamiania, wyświetlać zmienne itp.

    > [!NOTE]
    > Jeśli w dzienniku zostanie wyświetlony wpis z informacją `Debugger attached = False`, upłynął limit czasu, a skrypt kontynuuje działanie bez debugera. Prześlij potok ponownie, a następnie podłącz debuger po `Timeout for debug connection` komunikacie i przed upływem limitu czasu.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać pomoc dotyczącą pakietu [Azure-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) oraz pakietem [kroków dla potoków usługi Azure](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

* Zapoznaj się z listą [wyjątków projektanta i kodów błędów](algorithm-module-reference/designer-error-codes.md).
