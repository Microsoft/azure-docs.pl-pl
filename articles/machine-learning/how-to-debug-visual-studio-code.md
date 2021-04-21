---
title: Debugowanie interakcyjne za pomocą Visual Studio Code
titleSuffix: Azure Machine Learning
description: Interaktywne debugowanie Azure Machine Learning, potoków i wdrożeń przy użyciu Visual Studio Code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 09/30/2020
ms.openlocfilehash: 69a69afedbd86871987a8e62b55dfc070121cc78
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813870"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Debugowanie interakcyjne za pomocą Visual Studio Code



Dowiedz się, jak interaktywnie debugować eksperymenty Azure Machine Learning, potoki i wdrożenia przy użyciu Visual Studio Code (VS Code) i [debugpy.](https://github.com/microsoft/debugpy/)

## <a name="run-and-debug-experiments-locally"></a>Lokalne uruchamianie i debugowanie eksperymentów

Użyj rozszerzenia Azure Machine Learning, aby walidować, uruchamiać i debugować eksperymenty uczenia maszynowego przed przesłaniem ich do chmury.

### <a name="prerequisites"></a>Wymagania wstępne

* Azure Machine Learning VS Code (wersja zapoznawcza). Aby uzyskać więcej informacji, [zobacz Konfigurowanie Azure Machine Learning VS Code rozszerzenia](tutorial-setup-vscode-extension.md).
* [Docker](https://www.docker.com/get-started)
  * Program Docker Desktop dla komputerów Mac i systemu Windows
  * Aparat platformy Docker dla systemu Linux.
* [Python 3](https://www.python.org/downloads/)

> [!NOTE]
> W systemie Windows upewnij się, że [skonfigurowano aplikację Docker do używania kontenerów systemu Linux.](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

> [!TIP]
> W przypadku systemu Windows, chociaż nie jest to wymagane, zdecydowanie zaleca się używanie platformy Docker z [programem Podsystem Windows dla systemu Linux (WSL) 2.](/windows/wsl/tutorials/wsl-containers#install-docker-desktop)

> [!IMPORTANT]
> Przed uruchomieniem eksperymentu lokalnie upewnij się, że jest uruchomiona tania.

### <a name="debug-experiment-locally"></a>Lokalne debugowanie eksperymentu

1. W VS Code otwórz widok Azure Machine Learning rozszerzenia.
1. Rozwiń węzeł subskrypcji zawierający obszar roboczy. Jeśli jeszcze go nie masz, możesz utworzyć obszar roboczy Azure Machine Learning [przy](how-to-manage-resources-vscode.md#create-a-workspace) użyciu rozszerzenia.
1. Rozwiń węzeł obszaru roboczego.
1. Kliknij prawym przyciskiem myszy węzeł **Experiments (Eksperymenty)** i wybierz **pozycję Create experiment (Utwórz eksperyment).** Po wyświetleniu monitu podaj nazwę eksperymentu.
1. Rozwiń węzeł **Experiments** (Eksperymenty), kliknij prawym przyciskiem myszy eksperyment, który chcesz uruchomić, a następnie wybierz **pozycję Run Experiment (Uruchom eksperyment).**
1. Z listy opcji uruchamiania eksperymentu wybierz pozycję **Lokalnie.**
1. **Po raz pierwszy użyj tylko w systemie Windows.** Po wyświetleniu monitu o zezwalanie na udział plików wybierz pozycję **Tak.** Włączenie udziału plików umożliwia usłudze Docker zamontowanie katalogu zawierającego skrypt w kontenerze. Ponadto umożliwia również platformie Docker przechowywanie dzienników i danych wyjściowych z uruchomienia w katalogu tymczasowym w systemie.
1. Wybierz **pozycję Tak,** aby debugować eksperyment. W przeciwnym razie wybierz opcję **Nie**. Wybranie opcji nie spowoduje uruchomienie eksperymentu lokalnie bez dołączania do debugera.
1. Wybierz **pozycję Utwórz nową konfigurację uruchamiania,** aby utworzyć konfigurację uruchamiania. Konfiguracja uruchamiania definiuje skrypt, który chcesz uruchomić, zależności i używane zestawy danych. Alternatywnie, jeśli masz już jedną z nich, wybierz ją z listy rozwijanej.
    1. Wybierz środowisko. Możesz wybrać dowolny z tych Azure Machine Learning [lub](resource-curated-environments.md) utworzyć własny.
    1. Podaj nazwę skryptu, który chcesz uruchomić. Ścieżka jest względna do katalogu otwartego w VS Code.
    1. Wybierz, czy chcesz używać zestawu Azure Machine Learning danych. Za pomocą [rozszerzenia Azure Machine Learning tworzyć](how-to-manage-resources-vscode.md#create-dataset) zestawy danych.
    1. Debugowanie jest wymagane w celu dołączenia debugera do kontenera, w których działa eksperyment. Aby dodać debugpy jako zależność, wybierz **pozycję Dodaj debugpy**. W przeciwnym razie wybierz pozycję **Pomiń**. Nie dodawania debugpy jako zależności uruchamia eksperyment bez dołączania do debugera.
    1. W edytorze zostanie otwarty plik konfiguracji zawierający ustawienia konfiguracji uruchamiania. Jeśli ustawienia ci się podobają, wybierz pozycję **Prześlij eksperyment.** Alternatywnie możesz otworzyć paletę poleceń (**View > Command Palette**) z paska menu i wprowadzić polecenie w polu `Azure ML: Submit experiment` tekstowym.
1. Po przesłaniu eksperymentu zostanie utworzony obraz platformy Docker zawierający skrypt i konfiguracje określone w konfiguracji uruchamiania.

    Po rozpoczęciem procesu kompilacji obrazu platformy Docker zawartość strumienia plików do konsoli wyjściowej w `60_control_log.txt` VS Code.

    > [!NOTE]
    > Po pierwszym utworzeniu obrazu platformy Docker może to potrwać kilka minut.

1. Po s zbudowaniu obrazu zostanie wyświetlony monit o uruchomienie debugera. Ustaw punkty przerwania w skrypcie i wybierz pozycję **Uruchom debuger,** gdy wszystko będzie gotowe do rozpoczęcia debugowania. W ten sposób dołącza VS Code do kontenera, w który jest uruchomiony eksperyment. Alternatywnie w rozszerzeniu Azure Machine Learning umieść kursor nad węzłem bieżącego uruchomienia i wybierz ikonę odtwarzania, aby uruchomić debuger.

    > [!IMPORTANT]
    > Nie można mieć wielu sesji debugowania dla jednego eksperymentu. Można jednak debugować co najmniej dwa eksperymenty przy użyciu VS Code wystąpień.

W tym momencie powinno być możliwe krokowe debugowanie kodu przy użyciu VS Code.

Jeśli w dowolnym momencie chcesz anulować przebieg, kliknij prawym przyciskiem myszy węzeł uruchamiania i wybierz polecenie **Anuluj uruchamianie.**

Podobnie jak w przypadku zdalnych przebiegów eksperymentów, możesz rozwinąć węzeł uruchamiania, aby sprawdzić dzienniki i dane wyjściowe.

> [!TIP]
> Obrazy platformy Docker, które używają tych samych zależności zdefiniowanych w środowisku, są ponownie używane między przebiegami. Jeśli jednak uruchamiasz eksperyment przy użyciu nowego lub innego środowiska, tworzony jest nowy obraz. Ponieważ te obrazy są zapisywane w magazynie lokalnym, zaleca się usunięcie starych lub nieużywanych obrazów platformy Docker. Aby usunąć obrazy z systemu, użyj interfejsu wiersza polecenia platformy [Docker](https://docs.docker.com/engine/reference/commandline/rmi/) [lub VS Code platformy Docker.](https://code.visualstudio.com/docs/containers/overview)

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego

W niektórych przypadkach może być konieczne interaktywne debugowanie kodu języka Python używanego w potoku uczenia maszynowego. Za pomocą VS Code i debugpy można dołączyć do kodu podczas jego pracy w środowisku szkoleniowym.

### <a name="prerequisites"></a>Wymagania wstępne

* Obszar __Azure Machine Learning,__ który jest skonfigurowany do korzystania z usługi __Azure Virtual Network.__
* Potok __Azure Machine Learning,__ który używa skryptów języka Python w ramach kroków potoku. Na przykład PythonScriptStep.
* Klaster Azure Machine Learning compute, który znajduje się w sieci __wirtualnej__ i jest używany __przez potok__ do szkolenia .
* Środowisko __projektowe,__ które __znajduje się w sieci wirtualnej__. Środowisko projektowe może być jednym z następujących:

  * Maszyna wirtualna platformy Azure w sieci wirtualnej
  * Wystąpienie obliczeniowe maszyny wirtualnej notesu w sieci wirtualnej
  * Komputer kliencki, który ma łączność sieci prywatnej z siecią wirtualną za pośrednictwem sieci VPN lub expressRoute.

Aby uzyskać więcej informacji na temat korzystania z usługi Azure Virtual Network z usługą Azure Machine Learning, zobacz Omówienie izolacji i [prywatności sieci wirtualnej.](how-to-network-security-overview.md)

> [!TIP]
> Chociaż można pracować z Azure Machine Learning, które nie znajdują się za siecią wirtualną, zaleca się korzystanie z sieci wirtualnej.

### <a name="how-it-works"></a>Jak to działa

Kroki potoku uczenia maszynowego uruchamiają skrypty języka Python. Te skrypty są modyfikowane w celu wykonywania następujących akcji:

1. Rejestruj adres IP hosta, na których są uruchomione. Adres IP umożliwia połączenie debugera ze skryptem.

2. Uruchom składnik debugowania debugera i poczekaj na połączenie debugera.

3. W środowisku projektowym monitorujesz dzienniki utworzone w procesie trenowania, aby znaleźć adres IP, pod którym jest uruchomiony skrypt.

4. Należy VS Code adres IP, z który ma nawiązać połączenie debuger, przy użyciu `launch.json` pliku.

5. Debuger jest dołączany i interaktywnie przechodzi przez skrypt.

### <a name="configure-python-scripts"></a>Konfigurowanie skryptów języka Python

Aby włączyć debugowanie, należy wprowadzić następujące zmiany w skryptach języka Python używanych przez kroki w potoku uczenia maszynowego:

1. Dodaj następujące instrukcje importu:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Dodaj następujące argumenty. Te argumenty umożliwiają włączenie debugera zgodnie z potrzebami i ustawienie limitu czasu dołączania debugera:

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

1. Dodaj następujące instrukcje. Te instrukcje ładują bieżący kontekst uruchamiania, aby można było rejestrować adres IP węzła, na których działa kod:

    ```python
    global run
    run = Run.get_context()
    ```

1. Dodaj `if` instrukcje, która uruchamia debugpy i czeka na dołączenie debugera. Jeśli przed limitem czasu nie zostanie dołączenia żaden debuger, skrypt będzie kontynuować normalnie. Pamiętaj, aby zastąpić `HOST` `PORT` wartości i `listen` własnymi wartościami.

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

Poniższy przykład w języku Python przedstawia prosty `train.py` plik, który umożliwia debugowanie:

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

### <a name="configure-ml-pipeline"></a>Konfigurowanie potoku uczenia maszynowego

Aby udostępnić pakiety języka Python potrzebne do uruchomienia debugpy i uzyskania kontekstu uruchamiania, utwórz środowisko i ustaw `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` element . Zmień wersję zestawu SDK, aby dopasować wersję, która jest przez Ciebie używania. Poniższy fragment kodu przedstawia sposób tworzenia środowiska:

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

W sekcji [Konfigurowanie skryptów języka Python](#configure-python-scripts) do skryptów używanych przez kroki potoku uczenia maszynowego dodano nowe argumenty. Poniższy fragment kodu przedstawia sposób użycia tych argumentów w celu włączenia debugowania składnika i ustawienia limitu czasu. Pokazano w nim również sposób korzystania ze środowiska utworzonego wcześniej przez ustawienie wartości `runconfig=run_config` :

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

Po uruchomieniu potoku każdy krok tworzy przebieg podrzędny. Jeśli debugowanie jest włączone, zmodyfikowany skrypt rejestruje informacje podobne do następującego tekstu w pliku `70_driver_log.txt` dla podrzędnego uruchomienia:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Zapisz `ip_address` wartość. Jest on używany w następnej sekcji.

> [!TIP]
> Adres IP można również znaleźć w dziennikach uruchamiania dla podrzędnego uruchomienia dla tego kroku potoku. Aby uzyskać więcej informacji na temat wyświetlania tych informacji, zobacz Monitorowanie przebiegów i metryk eksperymentów usługi [Azure ML.](how-to-log-view-metrics.md)

### <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

1. Aby zainstalować debugpy w środowisku VS Code dewelopera, użyj następującego polecenia:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Aby uzyskać więcej informacji na temat używania debugpy z VS Code, zobacz [Debugowanie zdalne](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Aby skonfigurować VS Code do komunikowania się z Azure Machine Learning obliczeniową, na których jest uruchomiony debuger, utwórz nową konfigurację debugowania:

    1. Z VS Code menu Debug __(Debugowanie),__ a następnie wybierz pozycję __Open configurations (Otwórz konfiguracje).__ Zostanie otwarty __pliklaunch.jso__ nazwie .

    1. Wlaunch.js __pliku__ znajdź wiersz, który zawiera , i wstaw `"configurations": [` następujący tekst po nim. Zmień wpis `"host": "<IP-ADDRESS>"` na adres IP zwrócony w dziennikach z poprzedniej sekcji. Zmień wpis `"localRoot": "${workspaceFolder}/code/step"` na katalog lokalny, który zawiera kopię debugowanych skryptów:

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
        > Jeśli w sekcji konfiguracji znajdują się już inne wpisy, dodaj przecinek (,) po wstawionych kodach.

        > [!TIP]
        > Najlepszym rozwiązaniem, szczególnie w przypadku potoków, jest zachowanie zasobów dla skryptów w oddzielnych katalogach, tak aby kod był odpowiedni tylko dla każdego z kroków. W tym przykładzie `localRoot` przykładowa wartość odwołuje się do `/code/step1` .
        >
        > W przypadku debugowania wielu skryptów w różnych katalogach utwórz oddzielną sekcję konfiguracji dla każdego skryptu.

    1. Zapisz plik __launch.jspliku.__

### <a name="connect-the-debugger"></a>Łączenie debugera

1. Otwórz VS Code i otwórz lokalną kopię skryptu.
2. Ustaw punkty przerwania, w których skrypt ma zostać zatrzymany po dołączeniu.
3. Gdy proces podrzędny uruchamia skrypt, a element jest wyświetlany w dziennikach, użyj `Timeout for debug connection` klawisza F5 lub wybierz pozycję __Debuguj__. Po wyświetleniu monitu wybierz pozycję __Obliczenia Azure Machine Learning: konfiguracja debugowania__ zdalnego. Możesz również wybrać ikonę debugowania na pasku bocznym, wpis __Azure Machine Learning:__ debugowanie zdalne z menu rozwijanego Debugowanie, a następnie użyć zielonej strzałki, aby dołączyć debuger.

    W tym momencie VS Code debugpy w węźle obliczeniowym i zatrzymuje się w punkcie przerwania ustawionym wcześniej. Teraz możesz przechodzić przez kod podczas jego pracy, wyświetlać zmienne itp.

    > [!NOTE]
    > Jeśli w dzienniku jest wyświetlany wpis z informacją , limit czasu wygasł, a skrypt był `Debugger attached = False` kontynuowany bez debugera. Ponownie prześlij potok i połącz debuger po komunikacie `Timeout for debug connection` i przed upływem limitu czasu.

## <a name="debug-and-troubleshoot-deployments"></a>Debugowanie wdrożeń i rozwiązywanie problemów z wdrożeniami

W niektórych przypadkach może być konieczne interaktywne debugowanie kodu języka Python zawartego we wdrożeniu modelu. Jeśli na przykład skrypt wejściowy się nie popełni, a przyczyna nie może zostać określona przez dodatkowe rejestrowanie. Za pomocą VS Code i debugpy można dołączyć do kodu uruchomionego w kontenerze platformy Docker.

> [!IMPORTANT]
> Ta metoda debugowania nie działa w przypadku używania metod `Model.deploy()` i `LocalWebservice.deploy_configuration` do lokalnego wdrażania modelu. Zamiast tego należy utworzyć obraz przy użyciu [metody Model.package().](/python/api/azureml-core/azureml.core.model.model#package-workspace--models--inference-config-none--generate-dockerfile-false-)

Lokalne wdrożenia usług sieci Web wymagają roboczej instalacji platformy Docker w systemie lokalnym. Aby uzyskać więcej informacji na temat korzystania z platformy Docker, zobacz [dokumentację platformy Docker.](https://docs.docker.com/) Należy pamiętać, że podczas pracy z wystąpieniami obliczeniowymi program Docker jest już zainstalowany.

### <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

1. Aby zainstalować debugpy w lokalnym VS Code dewelopera, użyj następującego polecenia:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Aby uzyskać więcej informacji na temat używania debugpy z VS Code, zobacz [Debugowanie zdalne](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Aby skonfigurować VS Code do komunikowania się z obrazem platformy Docker, utwórz nową konfigurację debugowania:

    1. Z VS Code menu __Debug__ (Debugowanie) w zakresie __Run__ (Uruchom), a następnie wybierz pozycję __Open configurations (Otwórz konfiguracje).__ Zostanie otwarty __pliklaunch.jso__ nazwie .

    1. W __launch.jspliku__ znajdź element __"configurations"__ (wiersz, który zawiera ) i wstaw następujący `"configurations": [` tekst po nim. 

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
        Po wstawieniu __launch.jspliku__ powinien być podobny do następującego:
        ```json
        {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python: Current File",
                "type": "python",
                "request": "launch",
                "program": "${file}",
                "console": "integratedTerminal"
            },
            {
                "name": "Azure Machine Learning Deployment: Docker Debug",
                "type": "python",
                "request": "attach",
                "connect": {
                    "port": 5678,
                    "host": "0.0.0.0"
                    },
                "pathMappings": [
                    {
                        "localRoot": "${workspaceFolder}",
                        "remoteRoot": "/var/azureml-app"
                    }
                ]
            }
            ]
        }
        ```

        > [!IMPORTANT]
        > Jeśli w sekcji konfiguracji znajdują się już inne wpisy, dodaj przecinek ( __,__ ) po wstawionych kodach.

        Ta sekcja jest dołączana do kontenera platformy Docker przy użyciu portu __5678.__

    1. Zapisz plik __launch.jspliku.__

### <a name="create-an-image-that-includes-debugpy"></a>Tworzenie obrazu, który zawiera debugpy

1. Zmodyfikuj środowisko conda dla wdrożenia, tak aby zawierało debugpy. W poniższym przykładzie pokazano dodawanie go przy użyciu `pip_packages` parametru :

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

1. Aby rozpocząć debugowanie i poczekać na połączenie po uruchomieniu usługi, dodaj następujący kod na początku `score.py` pliku:

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Utwórz obraz na podstawie definicji środowiska i ściągnij obraz do rejestru lokalnego. 

    > [!NOTE]
    > W tym przykładzie założono, że wskazuje Azure Machine Learning obszaru roboczego i jest `ws` `model` to wdrażany model. Plik `myenv.yml` zawiera zależności conda utworzone w kroku 1.

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

    Po utworzeniu i pobraniu obrazu (ten proces może potrwać więcej niż 10 minut, więc czekaj patiently) ścieżka obrazu (obejmuje repozytorium, nazwę i tag, który w tym przypadku jest również jego skrótem) jest wyświetlana w komunikacie podobnym do następującego:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Aby ułatwić pracę z obrazem lokalnie, możesz użyć następującego polecenia, aby dodać tag dla tego obrazu. Zastąp `myimagepath` w poniższym poleceniu wartością lokalizacji z poprzedniego kroku.

    ```bash
    docker tag myimagepath debug:1
    ```

    W pozostałych krokach zamiast wartości pełnej ścieżki obrazu można odwoływać się do obrazu `debug:1` lokalnego.

### <a name="debug-the-service"></a>Debugowanie usługi

> [!TIP]
> Jeśli ustawisz limit czasu dla połączenia debugera w pliku, musisz nawiązać połączenie VS Code z sesją debugowania przed wygaśnięciem `score.py` limitu czasu. Uruchom VS Code, otwórz lokalną kopię pliku , ustaw punkt przerwania i przygotuj go do pracy przed rozpoczęciem pracy z krokami `score.py` w tej sekcji.
>
> Aby uzyskać więcej informacji na temat debugowania i ustawiania punktów przerwania, zobacz [Debugowanie](https://code.visualstudio.com/Docs/editor/debugging).

1. Aby uruchomić kontener platformy Docker przy użyciu obrazu, użyj następującego polecenia:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_local_path_to_score.py>:/var/azureml-app/score.py debug:1 /bin/bash
    ```

    To dołączy Twoją `score.py` lokalnie do tej w kontenerze. W związku z tym wszelkie zmiany wprowadzone w edytorze są automatycznie odzwierciedlane w kontenerze

2. Aby uzyskać lepsze środowisko pracy, możesz przejść do kontenera przy użyciu nowego interfejsu kodu programu VS. Wybierz zakres na pasku bocznym VS Code, znajdź utworzony kontener lokalny. W tej `Docker` dokumentacji jest to `debug:1` . Kliknij prawym przyciskiem myszy ten kontener i wybierz pozycję , a następnie nowy interfejs VS Code zostanie automatycznie otwarty, a ten interfejs będzie wyświetlać wewnątrz `"Attach Visual Studio Code"` utworzonego kontenera.

    ![Interfejs VS Code kontenera](./media/how-to-troubleshoot-deployment/container-interface.png)

3. W kontenerze uruchom następujące polecenie w powłoki

    ```bash
    runsvdir /var/runit
    ```
    Następnie w shell wewnątrz kontenera zobaczysz następujące dane wyjściowe:

    ![Dane wyjściowe konsoli uruchamiania kontenera](./media/how-to-troubleshoot-deployment/container-run.png)

4. Aby dołączyć VS Code debugowania wewnątrz kontenera, otwórz plik VS Code klawisz F5 lub wybierz pozycję __Debuguj.__ Po wyświetleniu monitu wybierz __konfigurację wdrażanie Azure Machine Learning: debugowanie platformy Docker.__ Możesz również wybrać  ikonę Uruchom zakres na pasku bocznym, wpis __Azure Machine Learning Deployment: Docker Debug__ z menu rozwijanego Debugowanie, a następnie użyć zielonej strzałki, aby dołączyć debuger.

    ![Ikona debugowania, przycisk Rozpocznij debugowanie i selektor konfiguracji](./media/how-to-troubleshoot-deployment/start-debugging.png)
    
    Po kliknięciu zielonej strzałki i dołączeniu debugera w kontenerze VS Code interfejsie można zobaczyć nowe informacje:
    
    ![Dołączone informacje debugera kontenera](./media/how-to-troubleshoot-deployment/debugger-attached.png)
    
    Ponadto w głównym interfejsie VS Code są następujące elementy:

    ![Punkt VS Code w score.py](./media/how-to-troubleshoot-deployment/local-debugger.png)

Teraz lokalna, która jest dołączona do kontenera, została już zatrzymana w `score.py` punktach przerwania, w których została ustawiona. W tym momencie program VS Code debugpy wewnątrz kontenera platformy Docker i zatrzymuje kontener platformy Docker w ustawionym wcześniej punkcie przerwania. Teraz możesz przechodzić przez kod podczas jego pracy, wyświetlać zmienne itp.

Aby uzyskać więcej informacji na temat używania VS Code debugowania języka Python, zobacz [Debugowanie kodu w języku Python](https://code.visualstudio.com/docs/python/debugging).

### <a name="stop-the-container"></a>Zatrzymywanie kontenera

Aby zatrzymać kontener, użyj następującego polecenia:

```bash
docker stop debug
```

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu usługi VS Code zdalnego, możesz użyć wystąpienia obliczeniowego jako zdalnego obliczenia z usługi VS Code do interaktywnego debugowania kodu. 

Dowiedz się więcej o rozwiązywaniu problemów:

* [Wdrażanie modelu lokalnego](how-to-troubleshoot-deployment-local.md)
* [Zdalne wdrażanie modelu](how-to-troubleshoot-deployment.md)
* [Potoki uczenia maszynowego](how-to-debug-pipelines.md)
* [ParallelRunStep](how-to-debug-parallel-run-step.md)

