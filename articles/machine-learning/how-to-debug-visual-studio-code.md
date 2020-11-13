---
title: Interaktywny debugowanie za pomocą Visual Studio Code
titleSuffix: Azure Machine Learning
description: Interaktywnie Debuguj kod, potoki i wdrożenia Azure Machine Learning przy użyciu Visual Studio Code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 09/30/2020
ms.openlocfilehash: 12163419ad779acfa116f1dee66284623e2d45fb
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616114"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Interaktywny debugowanie za pomocą Visual Studio Code



Dowiedz się, jak interaktywnie debugować Azure Machine Learning eksperymenty, potoki i wdrożenia przy użyciu Visual Studio Code (VS Code) i [debugpy](https://github.com/microsoft/debugpy/).

## <a name="run-and-debug-experiments-locally"></a>Lokalne uruchamianie i debugowanie eksperymentów

Użyj rozszerzenia Azure Machine Learning, aby sprawdzić, uruchomić i debugować eksperymenty uczenia maszynowego przed przesłaniem ich do chmury.

### <a name="prerequisites"></a>Wymagania wstępne

* Rozszerzenie VS Code Azure Machine Learning (wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz [Konfigurowanie rozszerzenia Azure Machine Learning vs Code](tutorial-setup-vscode-extension.md).
* [Docker](https://www.docker.com/get-started)
  * Pulpit Docker dla komputerów Mac i Windows
  * Aparat platformy Docker dla systemu Linux.
* [Python 3](https://www.python.org/downloads/)

> [!NOTE]
> W systemie Windows pamiętaj, aby [skonfigurować platformę Docker do korzystania z kontenerów systemu Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

> [!TIP]
> W przypadku systemu Windows, chociaż nie jest to wymagane, zdecydowanie zaleca się [Używanie platformy Docker z podsystemem Windows dla systemu Linux (WSL) 2](/windows/wsl/tutorials/wsl-containers#install-docker-desktop).

> [!IMPORTANT]
> Przed uruchomieniem eksperymentu lokalnego upewnij się, że platforma Docker jest uruchomiona.

### <a name="debug-experiment-locally"></a>Debuguj lokalnie eksperyment

1. W VS Code Otwórz widok rozszerzenia Azure Machine Learning.
1. Rozwiń węzeł subskrypcji zawierający obszar roboczy. Jeśli jeszcze tego nie masz, możesz [utworzyć obszar roboczy Azure Machine Learning](how-to-manage-resources-vscode.md#create-a-workspace) przy użyciu rozszerzenia.
1. Rozwiń węzeł obszaru roboczego.
1. Kliknij prawym przyciskiem myszy węzeł **eksperymenty** , a następnie wybierz pozycję **Utwórz eksperyment**. Po wyświetleniu monitu podaj nazwę eksperymentu.
1. Rozwiń węzeł **eksperymenty** , kliknij prawym przyciskiem myszy eksperyment, który chcesz uruchomić, a następnie wybierz polecenie **Uruchom eksperyment**.
1. Z listy opcji do uruchamiania eksperymentu wybierz pozycję **lokalnie**.
1. **Pierwsze użycie tylko w systemie Windows**. Po wyświetleniu monitu o zezwolenie na udział plików wybierz pozycję **tak**. Włączenie udostępniania plików umożliwia platformie Docker zainstalowanie katalogu zawierającego skrypt do kontenera. Ponadto umożliwia także platformie Docker przechowywanie dzienników i danych wyjściowych z przebiegu w katalogu tymczasowym w systemie.
1. Wybierz pozycję **tak** , aby debugować eksperyment. W przeciwnym razie wybierz opcję **Nie**. Wybranie pozycji nie spowoduje uruchomienie eksperymentu lokalnie bez dołączania do debugera.
1. Wybierz pozycję **Utwórz nową konfigurację przebiegu** , aby utworzyć konfigurację uruchomieniową. Konfiguracja przebiegu definiuje skrypt, który ma zostać uruchomiony, zależności i używane zestawy danych. Alternatywnie, jeśli masz już jeden, wybierz go z listy rozwijanej.
    1. Wybierz środowisko. Możesz wybrać jedną z [Azure Machine Learning nadzorowanych](resource-curated-environments.md) lub utworzyć własne.
    1. Podaj nazwę skryptu, który chcesz uruchomić. Ścieżka jest określana względem katalogu otwartego w VS Code.
    1. Wybierz, czy chcesz użyć zestawu danych Azure Machine Learning, czy nie. [Zestawy danych Azure Machine Learning](how-to-manage-resources-vscode.md#create-dataset) można tworzyć przy użyciu rozszerzenia.
    1. Debugpy jest wymagany w celu dołączenia debugera do kontenera, w którym działa eksperyment. Aby dodać debugpy jako zależność, wybierz pozycję **Dodaj debugpy**. W przeciwnym razie wybierz pozycję **Pomiń**. Nie można dodać debugpy jako zależność uruchamia Twój eksperyment bez dołączania do debugera.
    1. Plik konfiguracji zawierający ustawienia konfiguracji uruchamiania zostanie otwarty w edytorze. Jeśli ustawienia są zadowalające, wybierz pozycję **Prześlij eksperyment**. Alternatywnie możesz otworzyć paletę poleceń ( **wyświetl > paletę poleceń** ) z paska menu i wprowadzić `Azure ML: Submit experiment` polecenie w polu tekstowym.
1. Po przesłaniu eksperymentu zostanie utworzony obraz platformy Docker zawierający skrypt i konfiguracje określone w konfiguracji przebiegu.

    Po rozpoczęciu procesu kompilacji obrazu platformy Docker zawartość `60_control_log.txt` strumienia pliku do konsoli wyjściowej w vs Code.

    > [!NOTE]
    > Podczas pierwszego tworzenia obrazu platformy Docker może upłynąć kilka minut.

1. Po skompilowaniu obrazu zostanie wyświetlony monit o uruchomienie debugera. Ustaw punkty przerwania w skrypcie i wybierz pozycję **Uruchom Debuger** , gdy wszystko będzie gotowe do rozpoczęcia debugowania. Spowoduje to dołączenie debugera VS Code do kontenera, na którym działa eksperyment. Alternatywnie, w rozszerzeniu Azure Machine Learning, umieść kursor nad węzłem bieżącego przebiegu i wybierz ikonę odtwarzania, aby uruchomić debuger.

    > [!IMPORTANT]
    > Dla jednego eksperymentu nie można mieć wielu sesji debugowania. Można jednak debugować dwa lub więcej eksperymentów przy użyciu wielu wystąpień VS Code.

W tym momencie powinno być możliwe przechodzenie do kroku i debugowanie kodu przy użyciu VS Code.

Jeśli w dowolnym momencie chcesz anulować przebieg, kliknij prawym przyciskiem myszy węzeł uruchamiania, a następnie wybierz polecenie **Anuluj przebieg**.

Podobnie jak w przypadku zdalnych eksperymentów, można rozszerzyć węzeł uruchamiania w celu sprawdzenia dzienników i danych wyjściowych.

> [!TIP]
> Obrazy platformy Docker, które używają tych samych zależności zdefiniowanych w danym środowisku, są ponownie używane między przebiegami. Jeśli jednak uruchomisz eksperyment przy użyciu nowego lub innego środowiska, zostanie utworzony nowy obraz. Ponieważ obrazy te są zapisywane w magazynie lokalnym, zaleca się usunięcie starych lub nieużywanych obrazów platformy Docker. Aby usunąć obrazy z systemu, użyj [interfejsu wiersza polecenia platformy Docker](https://docs.docker.com/engine/reference/commandline/rmi/) lub [rozszerzenia vs Code Docker](https://code.visualstudio.com/docs/containers/overview).

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego

W niektórych przypadkach może być konieczne interaktywne Debugowanie kodu w języku Python używanego w potoku. Za pomocą VS Code i debugpy, można dołączyć do kodu w trakcie jego działania w środowisku szkoleniowym.

### <a name="prerequisites"></a>Wymagania wstępne

* __Obszar roboczy Azure Machine Learning__ skonfigurowany do korzystania z __usługi Azure Virtual Network__.
* __Potok Azure Machine Learning__ , który używa skryptów języka Python w ramach kroków potoku. Na przykład PythonScriptStep.
* Azure Machine Learning klaster obliczeniowy, który znajduje się __w sieci wirtualnej__ i jest __używany przez potok do szkoleń__.
* __Środowisko programistyczne__ znajdujące się __w sieci wirtualnej__. Środowisko programistyczne może być jednym z następujących:

  * Maszyna wirtualna platformy Azure w sieci wirtualnej
  * Wystąpienie obliczeniowe maszyny wirtualnej notesu w sieci wirtualnej
  * Komputer kliencki, który ma łączność sieci prywatnej z siecią wirtualną, przez sieć VPN lub za pośrednictwem usługi ExpressRoute.

Aby uzyskać więcej informacji na temat używania Virtual Network platformy Azure z usługą Azure Machine Learning, zobacz [Omówienie izolacji i prywatności w sieci wirtualnej](how-to-network-security-overview.md).

> [!TIP]
> Chociaż można korzystać z zasobów Azure Machine Learning, które nie znajdują się poza siecią wirtualną, zaleca się użycie sieci wirtualnej.

### <a name="how-it-works"></a>Jak to działa

Kroki potoku w sieci Te skrypty są modyfikowane w celu wykonania następujących czynności:

1. Zarejestruj adres IP hosta, na którym są uruchomione. Użyj adresu IP, aby połączyć debuger ze skryptem.

2. Uruchom składnik debugowania debugpy i poczekaj, aż debuger nawiąże połączenie.

3. W środowisku deweloperskim Monitoruj dzienniki utworzone przez proces szkoleń, aby znaleźć adres IP, na którym skrypt jest uruchomiony.

4. Poinformujesz VS Code adres IP, aby połączyć debugera z programem przy użyciu `launch.json` pliku.

5. Debuger i interaktywny krok można dołączyć do skryptu.

### <a name="configure-python-scripts"></a>Konfigurowanie skryptów języka Python

Aby włączyć debugowanie, wprowadź następujące zmiany w skryptach języka Python, które są używane przez kroki w potoku:

1. Dodaj następujące instrukcje importu:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Dodaj następujące argumenty. Te argumenty umożliwiają włączenie debugera w razie potrzeby i ustawienie limitu czasu na potrzeby dołączania debugera:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. Dodaj następujące instrukcje. Te instrukcje ładują bieżący kontekst uruchomienia, aby można było zarejestrować adres IP węzła, na którym uruchomiono kod:

    ```python
    global run
    run = Run.get_context()
    ```

1. Dodaj `if` instrukcję, która uruchamia debugpy i czeka na dołączenie debugera. Jeśli żaden debuger nie zostanie dołączony przed upływem limitu czasu, skrypt będzie kontynuowany w normalny sposób. Pamiętaj, aby zamienić `HOST` `PORT` wartości i to `listen` Funkcja z własnymi.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

W poniższym przykładzie w języku Python przedstawiono prosty `train.py` plik, który umożliwia debugowanie:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
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
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Konfigurowanie potoku ML

Aby udostępnić pakiety języka Python, które są konieczne do uruchomienia programu debugpy i uzyskać kontekst uruchamiania, Utwórz środowisko i ustaw `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` . Zmień wersję zestawu SDK, aby była zgodna z tą, której używasz. Poniższy fragment kodu przedstawia sposób tworzenia środowiska:

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
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

W sekcji [Konfigurowanie skryptów języka Python](#configure-python-scripts) nowe argumenty zostały dodane do skryptów używanych przez etapy potoku. Poniższy fragment kodu ilustruje, jak używać tych argumentów, aby włączyć debugowanie dla składnika i ustawić limit czasu. Pokazano w nim także, jak używać środowiska utworzonego wcześniej przez ustawienie `runconfig=run_config` :

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
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

1. Aby zainstalować program debugpy w środowisku deweloperskim VS Code, użyj następującego polecenia:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Aby uzyskać więcej informacji na temat używania debugpy z VS Code, zobacz [debugowanie zdalne](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Aby skonfigurować VS Code do komunikowania się z Azure Machine Learning obliczeń, w których działa debuger, Utwórz nową konfigurację debugowania:

    1. W obszarze VS Code wybierz menu __Debuguj__ , a następnie wybierz pozycję __Otwórz konfiguracje__. Plik o nazwie __launch.jsprzy__ otwieraniu.

    1. W __launch.jsw__ pliku Znajdź wiersz, który zawiera `"configurations": [` , i Wstaw następujący tekst po nim. Zmień `"host": "<IP-ADDRESS>"` wpis na adres IP zwrócony w dziennikach z poprzedniej sekcji. Zmień `"localRoot": "${workspaceFolder}/code/step"` wpis na katalog lokalny, który zawiera kopię debugowanego skryptu:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
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
        > Najlepszym rozwiązaniem, szczególnie w przypadku potoków, jest utrzymywanie zasobów dla skryptów w oddzielnych katalogach, aby kod był istotny tylko dla każdego z tych kroków. W tym przykładzie `localRoot` przykładowe odwołania do wartości `/code/step1` .
        >
        > W przypadku debugowania wielu skryptów w różnych katalogach należy utworzyć osobną sekcję konfiguracyjną dla każdego skryptu.

    1. Zapisz __launch.js__ pliku.

### <a name="connect-the-debugger"></a>Podłącz debuger

1. Otwórz VS Code i Otwórz lokalną kopię skryptu.
2. Ustaw punkty przerwania, w których skrypt ma zostać zatrzymany po dołączeniu.
3. Podczas gdy proces podrzędny uruchamia skrypt, a w `Timeout for debug connection` dziennikach jest wyświetlany, użyj klawisza F5 lub wybierz opcję __Debuguj__. Po wyświetleniu monitu wybierz konfigurację __Azure Machine Learning COMPUTE: Remote Debug__ . Możesz również wybrać ikonę debugowania z paska bocznego, __Azure Machine Learning: zdalnego debugowania__ z menu rozwijanego debugowanie, a następnie użyć zieloną strzałkę, aby dołączyć debuger.

    W tym momencie VS Code nawiązuje połączenie z usługą debugpy w węźle obliczeniowym i zatrzyma się na ustawionym wcześniej punkcie przerwania. Teraz można przechodzić przez kod w trakcie jego uruchamiania, wyświetlać zmienne itp.

    > [!NOTE]
    > Jeśli w dzienniku zostanie wyświetlony wpis z informacją `Debugger attached = False` , upłynął limit czasu, a skrypt kontynuuje działanie bez debugera. Prześlij potok ponownie, a następnie podłącz debuger po `Timeout for debug connection` komunikacie i przed upływem limitu czasu.

## <a name="debug-and-troubleshoot-deployments"></a>Debugowanie i rozwiązywanie problemów z wdrożeniami

W niektórych przypadkach może być konieczne interaktywne Debugowanie kodu w języku Python zawartego we wdrożeniu modelu. Na przykład, jeśli skrypt wejścia kończy się niepowodzeniem i powód nie może być określony przez dodatkowe rejestrowanie. Za pomocą VS Code i debugpy można dołączyć do kodu działającego wewnątrz kontenera Docker.

> [!IMPORTANT]
> Ta metoda debugowania nie działa w przypadku `Model.deploy()` `LocalWebservice.deploy_configuration` lokalnego wdrażania modelu i. Zamiast tego należy utworzyć obraz przy użyciu metody [model. Package ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truepackage-workspace--models--inference-config-none--generate-dockerfile-false-) .

Lokalne wdrożenia usługi sieci Web wymagają pracy instalacji platformy Docker w systemie lokalnym. Aby uzyskać więcej informacji na temat korzystania z platformy Docker, zapoznaj się z [dokumentacją platformy Docker](https://docs.docker.com/). Należy pamiętać, że podczas pracy z wystąpieniami obliczeniowymi program Docker jest już zainstalowany.

### <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

1. Aby zainstalować program debugpy w lokalnym środowisku programistycznym VS Code, użyj następującego polecenia:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Aby uzyskać więcej informacji na temat używania debugpy z VS Code, zobacz [debugowanie zdalne](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Aby skonfigurować VS Code do komunikowania się z obrazem platformy Docker, Utwórz nową konfigurację debugowania:

    1. W obszarze VS Code wybierz menu __Debuguj__ , a następnie wybierz pozycję __Otwórz konfiguracje__. Plik o nazwie __launch.jsprzy__ otwieraniu.

    1. W __launch.js__ pliku Znajdź wiersz, który zawiera `"configurations": [` , i Wstaw następujący tekst po nim:

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Jeśli w sekcji konfiguracje znajdują się już inne wpisy, Dodaj przecinek (,) po wstawionym kodzie.

        Ta sekcja dołącza do kontenera Docker przy użyciu portu 5678.

    1. Zapisz __launch.js__ pliku.

### <a name="create-an-image-that-includes-debugpy"></a>Tworzenie obrazu zawierającego debugpy

1. Zmodyfikuj środowisko Conda dla danego wdrożenia, aby zawierało debugpy. Poniższy przykład demonstruje dodanie go przy użyciu `pip_packages` parametru:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Aby rozpocząć debugpy i poczekać na połączenie, gdy usługa zostanie uruchomiona, Dodaj następujący element na początku `score.py` pliku:

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Tworzenie obrazu na podstawie definicji środowiska i ściąganie obrazu do rejestru lokalnego. 

    > [!NOTE]
    > W tym przykładzie przyjęto założenie, że `ws` wskazuje obszar roboczy Azure Machine Learning i `model` jest to wdrażany model. `myenv.yml`Plik zawiera zależności Conda utworzone w kroku 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Po utworzeniu i pobraniu obrazu ścieżka obrazu (łącznie z repozytorium, nazwą i tagiem, która w tym przypadku jest również skrótem) zostanie wyświetlona w komunikacie podobnym do poniższego:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Aby ułatwić pracę z obrazem, użyj następującego polecenia, aby dodać tag. Zamień `myimagepath` na wartość lokalizacji z poprzedniego kroku.

    ```bash
    docker tag myimagepath debug:1
    ```

    W pozostałej części kroków można odwołać się do lokalnego obrazu jako `debug:1` zamiast pełnej wartości ścieżki obrazu.

### <a name="debug-the-service"></a>Debugowanie usługi

> [!TIP]
> Jeśli ustawisz limit czasu dla połączenia usługi debugpy w `score.py` pliku, musisz połączyć vs Code z sesją debugowania przed upływem limitu czasu. Rozpocznij VS Code, Otwórz lokalną kopię `score.py` , ustaw punkt przerwania i przygotuj się do użycia przed wykonaniem kroków opisanych w tej sekcji.
>
> Aby uzyskać więcej informacji na temat debugowania i ustawiania punktów przerwania, zobacz [debugowanie](https://code.visualstudio.com/Docs/editor/debugging).

1. Aby uruchomić kontener platformy Docker przy użyciu obrazu, użyj następującego polecenia:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_path_to_score.py>:/var/azureml-apps/score.py debug:1 /bin/bash
    ```

    Spowoduje to dołączenie `score.py` lokalnego do kontenera. W związku z tym wszelkie zmiany wprowadzone w edytorze są automatycznie odzwierciedlane w kontenerze.

1. W obrębie kontenera Uruchom następujące polecenie w powłoce

    ```bash
    runsvdir /var/runit
    ```

1. Aby dołączyć VS Code do debugpy wewnątrz kontenera, Otwórz VS Code i naciśnij klawisz F5 lub wybierz opcję __Debuguj__. Po wyświetleniu monitu wybierz pozycję __wdrożenie Azure Machine Learning: Konfiguracja debugowania platformy Docker__ . Możesz również wybrać ikonę debugowania z paska bocznego, __Azure Machine Learning wdrożenia: Docker Debug__ z menu rozwijanego debugowanie, a następnie użyć zielonej strzałki do dołączenia debugera.

    ![Ikona debugowania, przycisk Rozpocznij debugowanie i selektor konfiguracji](./media/how-to-troubleshoot-deployment/start-debugging.png)

W tym momencie VS Code nawiązuje połączenie z usługą debugpy wewnątrz kontenera Docker i zostanie zatrzymane na ustawionym wcześniej punkcie przerwania. Teraz można przechodzić przez kod w trakcie jego uruchamiania, wyświetlać zmienne itp.

Aby uzyskać więcej informacji na temat używania VS Code do debugowania języka Python, zobacz [Debugowanie kodu](https://code.visualstudio.com/docs/python/debugging)w języku Python.

### <a name="stop-the-container"></a>Zatrzymywanie kontenera

Aby zatrzymać kontener, użyj następującego polecenia:

```bash
docker stop debug
```

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu VS Code zdalnej, można użyć wystąpienia obliczeniowego jako zdalnego obliczenia z VS Code do interaktywnego debugowania kodu. 

[Samouczek: uczenie swojego pierwszego modelu ml](tutorial-1st-experiment-sdk-train.md) pokazuje, jak używać wystąpienia obliczeniowego z zintegrowanym notesem.
