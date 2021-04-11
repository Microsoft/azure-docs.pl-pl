---
title: Jak zainstalować i uruchomić kontener analizy przestrzennej — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Kontener analizy przestrzennej umożliwia wykrywanie osób i odległości.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 87076febd4597556fd2b28245f47442308cd6e6c
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108368"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>Instalowanie i uruchamianie kontenera analizy przestrzennej (wersja zapoznawcza)

Kontener analizy przestrzennej umożliwia analizowanie wideo przesyłania strumieniowego w czasie rzeczywistym w celu zrozumienia relacji przestrzennych między osobami, ich przenoszeniem i interakcją z obiektami w środowiskach fizycznych. Kontenery doskonale nadają się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" Utwórz zasób przetwarzanie obrazów "  target="_blank"> utwórz zasób przetwarzanie obrazów </a> dla standardowej warstwy S1 w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Potrzebny będzie klucz i punkt końcowy z zasobu tworzonego w celu uruchomienia kontenera analizy przestrzennej. Będziesz używać klucza i punktu końcowego później.


### <a name="spatial-analysis-container-requirements"></a>Wymagania kontenera analizy przestrzennej

Aby można było uruchomić kontener analizy przestrzennej, potrzebne jest urządzenie obliczeniowe z [procesorem GPU typu NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). Zalecamy używanie [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) z PRZYSPIESZENIEM procesora GPU, ale kontener jest uruchamiany na dowolnej innej maszynie stacjonarnej, która spełnia wymagania minimalne. Odwołujemy się do tego urządzenia jako komputera-hosta.

#### <a name="azure-stack-edge-device"></a>[Urządzenie brzegowe Azure Stack](#tab/azure-stack-edge)

Azure Stack Edge to rozwiązanie typu "sprzęt jako usługa" oraz urządzenie komputerowe z systemem brzegowym z funkcją transferu danych w sieci. Szczegółowe instrukcje dotyczące przygotowania i instalacji znajdują się w [dokumentacji Azure Stack Edge](../../databox-online/azure-stack-edge-deploy-prep.md).

#### <a name="desktop-machine"></a>[Komputer stacjonarny](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>Minimalne wymagania sprzętowe

* 4 GB pamięci RAM systemu
* 4 GB pamięci RAM procesora GPU
* 8-rdzeniowy procesor CPU
* 1 NVIDIA Tesla T4 GPU
* 20 GB miejsca na dysku twardym

#### <a name="recommended-hardware"></a>Zalecany sprzęt

* 32 GB pamięci RAM systemu
* 16 GB pamięci RAM procesora GPU
* 8-rdzeniowy procesor CPU
* 2 NVIDIA Tesla T4 GPU
* 50 GB przestrzeni dyskowej SSD

W tym artykule zostaną pobrane i zainstalowane następujące pakiety oprogramowania. Komputer hosta musi mieć możliwość uruchomienia następujących czynności (zobacz poniżej, aby uzyskać instrukcje):

* [Sterowniki grafiki NVIDIA](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) i [zestaw narzędzi NVIDIA CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* Konfiguracje dla [NVIDIA MP](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (usługa wieloprocesowa).
* [Docker ce](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) i [NVIDIA — Docker2](https://github.com/NVIDIA/nvidia-docker) 
* [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) środowiska uruchomieniowego.

#### <a name="azure-vm-with-gpu"></a>[Maszyna wirtualna platformy Azure z procesorem GPU](#tab/virtual-machine)
W naszym przykładzie będziemy używać [maszyny wirtualnej z serii NC](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , która ma jeden procesor GPU K80.

---

| Wymaganie | Opis |
|--|--|
| Aparat fotograficzny | Kontener analizy przestrzennej nie jest powiązany z konkretną marką aparatu. Urządzenie aparatu fotograficznego musi: obsługiwać kodowanie przy użyciu protokołu przesyłania strumieniowego (Real-Time streaming) i H. 264, dostępne dla komputera hosta oraz możliwość przesyłania strumieniowego przy użyciu rozdzielczości 15FPS i 1080p. |
| System operacyjny Linux | Na komputerze-hoście musi być zainstalowany [Ubuntu Desktop 18,04 LTS](http://releases.ubuntu.com/18.04/) .  |


## <a name="request-approval-to-run-the-container"></a>Żądaj zatwierdzenia do uruchomienia kontenera

Wypełnij i prześlij [formularz żądania](https://aka.ms/csgate) , aby zażądać zatwierdzenia do uruchomienia kontenera.

Formularz żąda informacji o użytkowniku, firmie i scenariuszu użytkownika, dla którego będziesz używać kontenera. Po przesłaniu formularza zespół usługi Azure Cognitive Services sprawdzi go i wyśle wiadomość e-mail z decyzją.

> [!IMPORTANT]
> * W formularzu musisz użyć adresu e-mail skojarzonego z IDENTYFIKATORem subskrypcji platformy Azure.
> * Zasób przetwarzanie obrazów używany do uruchamiania kontenera musi zostać utworzony przy użyciu zatwierdzonego identyfikatora subskrypcji platformy Azure.

Po zatwierdzeniu będzie można uruchomić kontener po jego pobraniu z Container Registry firmy Microsoft (MCR), opisanego w dalszej części artykułu.

Nie będzie można uruchomić kontenera, jeśli subskrypcja platformy Azure nie została zatwierdzona.

## <a name="set-up-the-host-computer"></a>Konfigurowanie komputera hosta

Zalecane jest używanie Azure Stack urządzenia brzegowego dla komputera hosta. Kliknij pozycję **komputer stacjonarny** , jeśli konfigurujesz inne urządzenie lub **maszynę wirtualną** , jeśli korzystasz z maszyny wirtualnej.

#### <a name="azure-stack-edge-device"></a>[Urządzenie brzegowe Azure Stack](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>Konfigurowanie obliczeń w portalu Azure Stack Edge 
 
Analiza przestrzenna korzysta z funkcji obliczeniowych Azure Stack Edge, aby uruchomić rozwiązanie AI. Aby włączyć funkcje obliczeniowe, należy się upewnić, że: 

* [Nawiązano połączenie i aktywowano](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md) urządzenie Azure Stack Edge. 
* Masz system klienta systemu Windows z uruchomionym programem PowerShell 5,0 lub nowszym, aby uzyskać dostęp do urządzenia.  
* Aby wdrożyć klaster Kubernetes, należy skonfigurować urządzenie brzegowe Azure Stack za pomocą **lokalnego interfejsu użytkownika** na [Azure Portal](https://portal.azure.com/): 
  1. Włącz funkcję COMPUTE na urządzeniu Azure Stack Edge. Aby włączyć obliczenia, przejdź na stronę **obliczenia** w interfejsie sieci Web urządzenia. 
  2. Wybierz interfejs sieciowy, który chcesz włączyć dla obliczeń, a następnie kliknij pozycję **Włącz**. Spowoduje to utworzenie przełącznika wirtualnego na urządzeniu, w tym interfejsie sieciowym.
  3. Pozostaw puste adresy IP węzła testowego Kubernetes i adresy IP usług zewnętrznych Kubernetes.
  4. Kliknij pozycję **Zastosuj**. Ta operacja może potrwać około dwie minuty. 

![Konfigurowanie obliczeń](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>Konfigurowanie roli obliczeniowej Edge i tworzenie zasobu IoT Hub

W [Azure Portal](https://portal.azure.com/)przejdź do zasobu Azure Stack Edge. Na stronie **Przegląd** lub na liście nawigacji kliknij przycisk **wprowadzenie** do obliczeń krawędzi. Na kafelku  **konfigurowania obliczeń brzegowych**   kliknij przycisk **Konfiguruj**. 

![Link](media/spatial-analysis/configure-edge-compute-tile.png)

Na stronie **Konfigurowanie obliczeń krawędzi**   Wybierz istniejący IoT Hub lub wybierz opcję utworzenia nowego. Domyślnie warstwa cenowa standardowa (S1) jest używana do tworzenia zasobów IoT Hub. Aby użyć zasobu warstwy Bezpłatna IoT Hub, utwórz go, a następnie wybierz. Zasób IoT Hub używa tej samej subskrypcji i grupy zasobów, która jest używana przez zasób Azure Stack Edge 

Kliknij pozycję **Utwórz**. Tworzenie zasobów IoT Hub może potrwać kilka minut. Po utworzeniu zasobu IoT Hub zostanie zaktualizowany kafelek **Konfiguruj zmiany krawędzi** , aby pokazać nową konfigurację. Aby upewnić się, że skonfigurowano rolę obliczeniową brzegową, wybierz pozycję **Wyświetl konfigurację** na kafelku **Konfiguruj obliczenia**   .

Po skonfigurowaniu roli funkcji obliczeniowej Edge na urządzeniu Edge tworzone są dwa urządzenia: urządzenie IoT i urządzenie IoT Edge. Oba urządzenia można wyświetlić w zasobie usługi IoT Hub. Środowisko uruchomieniowe Azure IoT Edge będzie już uruchomione na urządzeniu IoT Edge.

> [!NOTE]
> * Obecnie tylko platforma systemu Linux jest obsługiwana w przypadku urządzeń IoT Edge. Aby uzyskać pomoc w rozwiązywaniu problemów z urządzeniem brzegowym Azure Stack, zobacz artykuł [Rejestrowanie i rozwiązywanie problemów](spatial-analysis-logging.md) .
> * Aby dowiedzieć się więcej o konfigurowaniu IoT Edge urządzenia do komunikowania się za pomocą serwera proxy, zobacz [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](../../iot-edge/how-to-configure-proxy-support.md#azure-portal)

###  <a name="enable-mps-on-azure-stack-edge"></a>Włącz MP na Azure Stack Edge 

1. Uruchom sesję programu Windows PowerShell jako administrator. 

2. Upewnij się, że na kliencie jest uruchomiona usługa Windows Remote Management. W terminalu programu PowerShell Użyj następującego polecenia 
    
    ```powershell
    winrm quickconfig
    ```
    
    Jeśli widzisz ostrzeżenia dotyczące wyjątku zapory, Sprawdź typ połączenia sieciowego i zapoznaj się z dokumentacją [Windows Remote Management](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management) .

3. Przypisz zmienną do adresu IP urządzenia. 
    
    ```powershell
    $ip = "<device-IP-address>" 
    ```
    
4. Aby dodać adres IP urządzenia do listy zaufanych hostów klienta, użyj następującego polecenia: 
    
    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force 
    ```

5. Uruchom sesję programu Windows PowerShell na urządzeniu. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell 
    ```

6. Podaj hasło po wyświetleniu monitu. Użyj tego samego hasła, które jest używane do logowania się do lokalnego interfejsu użytkownika sieci Web. Domyślne hasło lokalnego interfejsu użytkownika sieci Web to `Password1` .

Wpisz, `Start-HcsGpuMPS` Aby uruchomić usługę MP na urządzeniu. 

Aby uzyskać pomoc w rozwiązywaniu problemów z urządzeniem brzegowym Azure Stack, zobacz [Rozwiązywanie problemów z urządzeniem brzegowym Azure Stack](spatial-analysis-logging.md#troubleshooting-the-azure-stack-edge-device) 

#### <a name="desktop-machine"></a>[Komputer stacjonarny](#tab/desktop-machine)

Postępuj zgodnie z tymi instrukcjami, jeśli komputer hosta nie jest urządzeniem Azure Stack Edge.

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>Zainstaluj narzędzia NVIDIA CUDA toolkit i sterowniki grafiki NVIDIA na komputerze hosta

Poniższy skrypt bash służy do instalowania wymaganych sterowników grafiki NVIDIA oraz zestawu narzędzi CUDA.

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

Wykonaj ponownie rozruch komputera i uruchom następujące polecenie.

```bash
nvidia-smi
```

Powinny zostać wyświetlone następujące dane wyjściowe.

![Wyjście sterownika NVIDIA](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>Zainstaluj platformę Docker CE i NVIDIA-docker2 na komputerze hosta

Zainstaluj program Docker CE na komputerze-hoście.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

Zainstaluj pakiet oprogramowania *NVIDIA-Docker-2* .

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>Włącz NVIDIA MP na komputerze hosta

> [!TIP]
> * Nie instaluj pakietu MP, jeśli możliwości obliczeniowe procesora GPU są mniejsze niż 7. x (pre Volta). Zobacz [cuda](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title) , aby uzyskać informacje o zgodności. 
> * Uruchom instrukcje MPS z okna terminalu na komputerze-hoście. Nie znajduje się wewnątrz wystąpienia kontenera Docker.

Aby uzyskać najlepszą wydajność i wykorzystanie, należy skonfigurować procesory GPU komputera hosta dla [usługi wieloprocesowej (MPS) firmy NVIDIA](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf). Uruchom instrukcje MPS z okna terminalu na komputerze-hoście.

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>Konfigurowanie Azure IoT Edge na komputerze hosta

Aby wdrożyć kontener analizy przestrzennej na komputerze hosta, Utwórz wystąpienie usługi [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) przy użyciu warstwy cenowej Standard (S1) lub Free (F0). 

Utwórz wystąpienie usługi Azure IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure. Zastąp parametry stosownie do potrzeb. Alternatywnie możesz utworzyć IoT Hub platformy Azure na [Azure Portal](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
Zobacz [obsługę](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) regionów dla dostępnych regionów.
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

Konieczne będzie zainstalowanie [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) w wersji 1.0.9. Wykonaj następujące kroki, aby pobrać poprawną wersję:

Ubuntu Server 18,04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Skopiuj wygenerowaną listę.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Zainstaluj klucz publiczny programu Microsoft GPG.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Aktualizowanie list pakietów na urządzeniu.

```bash
sudo apt-get update
```

Zainstaluj wersję 1.0.9:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Następnie zarejestruj komputer hosta jako urządzenie IoT Edge w wystąpieniu IoT Hub, używając [parametrów połączenia](../../iot-edge/how-to-register-device.md).

Musisz podłączyć urządzenie IoT Edge do IoT Hub platformy Azure. Należy skopiować parametry połączenia z utworzonego wcześniej urządzenia IoT Edge. Alternatywnie możesz uruchomić poniższe polecenie w interfejsie wiersza polecenia platformy Azure.

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

Na komputerze hosta otwartym  `/etc/iotedge/config.yaml` do edycji. Zamień na `ADD DEVICE CONNECTION STRING HERE` Parametry połączenia. Zapisz i zamknij plik. Uruchom to polecenie, aby ponownie uruchomić usługę IoT Edge na komputerze-hoście.

```bash
sudo systemctl restart iotedge
```

Wdróż kontener analizy przestrzennej jako moduł IoT na komputerze-hoście, z poziomu [Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) lub [interfejsu wiersza polecenia platformy Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows). Jeśli używasz portalu, ustaw identyfikator URI obrazu na lokalizację Azure Container Registry. 

Wykonaj poniższe kroki, aby wdrożyć kontener przy użyciu interfejsu wiersza polecenia platformy Azure.

#### <a name="azure-vm-with-gpu"></a>[Maszyna wirtualna platformy Azure z procesorem GPU](#tab/virtual-machine)

Maszyna wirtualna platformy Azure z procesorem GPU może być również używana do uruchamiania analizy przestrzennej. W poniższym przykładzie zostanie użyta maszyna wirtualna z [serii NC](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , która ma jeden procesor GPU K80.

#### <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Otwórz kreatora [tworzenia maszyny wirtualnej](https://ms.portal.azure.com/#create/Microsoft.VirtualMachine) w Azure Portal.

Nadaj nazwę maszynie wirtualnej i wybierz region, który ma być (USA) zachodnie stany USA 2. Upewnij się, że ustawiono wartość `Availability Options` "Brak wymaganej nadmiarowości infrastruktury". Zapoznaj się z poniższym rysunkiem, aby uzyskać pełną konfigurację i następny krok w celu ułatwienia lokalizowania prawidłowego rozmiaru maszyny wirtualnej. 

:::image type="content" source="media/spatial-analysis/virtual-machine-instance-details.jpg" alt-text="Szczegóły konfiguracji maszyny wirtualnej." lightbox="media/spatial-analysis/virtual-machine-instance-details.jpg":::

Aby zlokalizować rozmiar maszyny wirtualnej, wybierz pozycję "Zobacz wszystkie rozmiary", a następnie Wyświetl listę "rozmiary maszyn wirtualnych bez usługi Premium", jak pokazano poniżej.

:::image type="content" source="media/spatial-analysis/virtual-machine-sizes.png" alt-text="Rozmiary maszyn wirtualnych." lightbox="media/spatial-analysis/virtual-machine-sizes.png":::

Następnie wybierz opcję **NC6** lub **NC6_Promo**.

:::image type="content" source="media/spatial-analysis/promotional-selection.png" alt-text="wybór promocyjny" lightbox="media/spatial-analysis/promotional-selection.png":::

Następnie utwórz maszynę wirtualną. Po utworzeniu przejdź do zasobu maszyny wirtualnej w Azure Portal i wybierz polecenie `Extensions` z okienka po lewej stronie. Zostanie wyświetlone okno rozszerzenia ze wszystkimi dostępnymi rozszerzeniami. Wybierz pozycję `NVIDIA GPU Driver Extension` Utwórz, a następnie Zakończ pracę kreatora.

Po pomyślnym zastosowaniu rozszerzenia przejdź do strony głównej maszyny wirtualnej w Azure Portal i kliknij przycisk `Connect` . Do maszyny wirtualnej można uzyskać dostęp za pośrednictwem protokołu SSH lub RDP. Protokół RDP będzie przydatny do wyświetlania okna wizualizatora (wyjaśnionego później). Skonfiguruj dostęp do protokołu RDP, wykonując następujące [kroki](../../virtual-machines/linux/use-remote-desktop.md) i otwierając połączenie pulpitu zdalnego z maszyną wirtualną.

### <a name="verify-graphics-drivers-are-installed"></a>Sprawdź, czy sterowniki grafiki są zainstalowane

Uruchom następujące polecenie, aby sprawdzić, czy sterowniki grafiki zostały pomyślnie zainstalowane. 

```bash
nvidia-smi
```

Powinny zostać wyświetlone następujące dane wyjściowe.

![Wyjście sterownika NVIDIA](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-vm"></a>Zainstaluj platformę Docker CE i NVIDIA-docker2 na maszynie wirtualnej

Uruchom następujące polecenia po raz, aby zainstalować platformę Docker CE i NVIDIA-docker2 na maszynie wirtualnej.

Zainstaluj program Docker CE na komputerze-hoście.

```bash
sudo apt-get update
```
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```


Zainstaluj pakiet oprogramowania *NVIDIA-Docker-2* .

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce nvidia-docker2
```
```bash
sudo systemctl restart docker
```

Teraz, po skonfigurowaniu i skonfigurowaniu maszyny wirtualnej, wykonaj poniższe kroki, aby skonfigurować Azure IoT Edge. 

## <a name="configure-azure-iot-edge-on-the-vm"></a>Konfigurowanie Azure IoT Edge na maszynie wirtualnej

Aby wdrożyć kontener analizy przestrzennej na maszynie wirtualnej, Utwórz wystąpienie usługi [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) przy użyciu warstwy cenowej Standard (S1) lub Free (F0).

Utwórz wystąpienie usługi Azure IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure. Zastąp parametry stosownie do potrzeb. Alternatywnie możesz utworzyć IoT Hub platformy Azure na [Azure Portal](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
Zobacz [obsługę](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) regionów dla dostępnych regionów.
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

Konieczne będzie zainstalowanie [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) w wersji 1.0.9. Wykonaj następujące kroki, aby pobrać poprawną wersję:

Ubuntu Server 18,04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Skopiuj wygenerowaną listę.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Zainstaluj klucz publiczny programu Microsoft GPG.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Aktualizowanie list pakietów na urządzeniu.

```bash
sudo apt-get update
```

Zainstaluj wersję 1.0.9:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Następnie zarejestruj maszynę wirtualną jako urządzenie IoT Edge w wystąpieniu IoT Hub przy użyciu [parametrów połączenia](../../iot-edge/how-to-register-device.md).

Musisz podłączyć urządzenie IoT Edge do IoT Hub platformy Azure. Należy skopiować parametry połączenia z utworzonego wcześniej urządzenia IoT Edge. Alternatywnie możesz uruchomić poniższe polecenie w interfejsie wiersza polecenia platformy Azure.

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

Na maszynie wirtualnej Otwórz  `/etc/iotedge/config.yaml` do edycji. Zamień na `ADD DEVICE CONNECTION STRING HERE` Parametry połączenia. Zapisz i zamknij plik. Uruchom to polecenie, aby ponownie uruchomić usługę IoT Edge na maszynie wirtualnej.

```bash
sudo systemctl restart iotedge
```

Wdróż kontener analizy przestrzennej jako moduł IoT na maszynie wirtualnej przy użyciu [Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) lub [interfejsu wiersza polecenia platformy Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows). Jeśli używasz portalu, ustaw identyfikator URI obrazu na lokalizację Azure Container Registry. 

Wykonaj poniższe kroki, aby wdrożyć kontener przy użyciu interfejsu wiersza polecenia platformy Azure.

---

### <a name="iot-deployment-manifest"></a>Manifest wdrożenia IoT

Aby usprawnić wdrażanie kontenerów na wielu komputerach hostów, można utworzyć plik manifestu wdrożenia w celu określenia opcji tworzenia kontenera i zmiennych środowiskowych. Przykład manifestu wdrażania [dla Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [innych maszyn stacjonarnych](https://go.microsoft.com/fwlink/?linkid=2152270)i [maszyn wirtualnych platformy Azure z procesorem GPU](https://go.microsoft.com/fwlink/?linkid=2152189) w serwisie GitHub.

W poniższej tabeli przedstawiono różne zmienne środowiskowe używane przez moduł IoT Edge. Można je również ustawić w manifeście wdrożenia połączonym powyżej, używając `env` atrybutu w `spatialanalysis` :

| Nazwa ustawienia | Wartość | Opis|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | Informacje Pełne | Poziom rejestrowania, wybierz jedną z dwóch wartości|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | Nie Modyfikuj|
| ARCHON_PERF_MARKER| fałsz| Ustaw tę wartość na true w przypadku rejestrowania wydajności, w przeciwnym razie powinna to być wartość false.| 
| ARCHON_NODES_LOG_LEVEL | Informacje Pełne | Poziom rejestrowania, wybierz jedną z dwóch wartości|
| OMP_WAIT_POLICY | CHODZĄC | Nie Modyfikuj|
| QT_X11_NO_MITSHM | 1 | Nie Modyfikuj|
| APIKEY | Klucz interfejsu API| Zbierz tę wartość z Azure Portal z zasobu przetwarzanie obrazów. Można je znaleźć w sekcji **klucz i punkt końcowy** zasobu. |
| ROZLICZENIA | Identyfikator URI punktu końcowego| Zbierz tę wartość z Azure Portal z zasobu przetwarzanie obrazów. Można je znaleźć w sekcji **klucz i punkt końcowy** zasobu.|
| UMOWY | odebrać | Ta wartość musi być ustawiona na *Zaakceptuj* , aby można było uruchomić kontener |
| WYŚWIETLANY | : 1 | Ta wartość musi być taka sama jak w danych wyjściowych `echo $DISPLAY` na komputerze-hoście. Urządzenia brzegowe Azure Stack nie są wyświetlane. To ustawienie nie ma zastosowania|
| ARCHON_GRAPH_READY_TIMEOUT | 600 | Dodaj tę zmienną środowiskową, jeśli procesor GPU **nie** jest w technologii T4 ani NVIDIA 2080 TI|
| ORT_TENSORRT_ENGINE_CACHE_ENABLE | 0 | Dodaj tę zmienną środowiskową, jeśli procesor GPU **nie** jest w technologii T4 ani NVIDIA 2080 TI|
| KEY_ENV | Klucz szyfrowania środowiska ASE | Dodaj tę zmienną środowiskową, jeśli Video_URL jest ciągiem nieprzesłoniętym |
| IV_ENV | Wektor inicjalizacji | Dodaj tę zmienną środowiskową, jeśli Video_URL jest ciągiem nieprzesłoniętym|

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby można było uruchomić kontener, należy określić opcje, i. w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [rozliczenia](#billing).

Po zaktualizowaniu manifestu wdrożenia dla [Azure Stack urządzeń brzegowych](https://go.microsoft.com/fwlink/?linkid=2142179), [maszyny STACJONARNEJ](https://go.microsoft.com/fwlink/?linkid=2152270) lub [maszyny wirtualnej platformy Azure z procesorem GPU](https://go.microsoft.com/fwlink/?linkid=2152189) z własnymi ustawieniami i wyborem operacji, można użyć poniższego polecenia [platformy Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows) , aby wdrożyć kontener na komputerze hosta jako moduł IoT Edge.

```azurecli
sudo az login
sudo az extension add --name azure-iot
sudo az iot edge set-modules --hub-name "<iothub-name>" --device-id "<device-name>" --content DeploymentManifest.json --subscription "<name or ID of Azure Subscription>"
```

|Parametr  |Opis  |
|---------|---------|
| `--hub-name` | Nazwa IoT Hub platformy Azure. |
| `--content` | Nazwa pliku wdrożenia. |
| `--target-condition` | Nazwa urządzenia IoT Edgego dla komputera hosta. |
| `-–subscription` | Identyfikator lub nazwa subskrypcji. |

To polecenie spowoduje uruchomienie wdrożenia. Przejdź do strony wystąpienia usługi Azure IoT Hub w Azure Portal, aby wyświetlić stan wdrożenia. Stan może być wyświetlany jako *417 — konfiguracja wdrożenia urządzenia nie jest ustawiona* , dopóki nie zakończy się pobieranie obrazów kontenera i zacznie działać.

## <a name="validate-that-the-deployment-is-successful"></a>Sprawdź, czy wdrożenie zakończyło się pomyślnie

Istnieje kilka sposobów sprawdzenia, czy kontener jest uruchomiony. Znajdź *stan środowiska uruchomieniowego* w **IoT Edge ustawienia modułu** dla modułu analizy przestrzennej w wystąpieniu usługi Azure IoT Hub na Azure Portal. Sprawdź, czy **wymagana wartość** i **raportowana wartość** *stanu czasu wykonywania* jest *uruchomiona*.

![Przykładowa weryfikacja wdrożenia](./media/spatial-analysis/deployment-verification.png)

Po zakończeniu wdrażania i zakończeniu działania kontenera **komputer hosta** zacznie wysyłać zdarzenia do IoT Hub platformy Azure. Jeśli używana jest `.debug` wersja operacji, zobaczysz okno wizualizatora dla każdego aparatu skonfigurowanego w manifeście wdrożenia. Teraz można zdefiniować wiersze i strefy, które mają być monitorowane w manifeście wdrożenia, a następnie postępować zgodnie z instrukcjami w celu ponownego wdrożenia. 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>Skonfiguruj operacje wykonywane przez analizę przestrzenną

Musisz użyć [operacji analizy przestrzennej](spatial-analysis-operations.md) , aby skonfigurować kontener do używania podłączonych kamer, skonfigurować operacje i nie tylko. Dla każdego skonfigurowanego urządzenia z kamerą operacje związane z analizą przestrzenną spowodują wygenerowanie strumienia wyjściowego komunikatów JSON wysyłanych do wystąpienia usługi Azure IoT Hub.

## <a name="use-the-output-generated-by-the-container"></a>Użyj danych wyjściowych wygenerowanych przez kontener

Jeśli chcesz zacząć zużywać dane wyjściowe wygenerowane przez kontener, zobacz następujące artykuły:

*   Użyj zestawu SDK usługi Azure Event Hub dla wybranego języka programowania, aby nawiązać połączenie z punktem końcowym usługi Azure IoT Hub i odebrać zdarzenia. Aby uzyskać więcej informacji [, zobacz odczytywanie komunikatów z urządzenia do chmury z wbudowanego punktu końcowego](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) . 
*   Skonfiguruj Routing komunikatów na IoT Hub platformy Azure, aby wysyłać zdarzenia do innych punktów końcowych, lub Zapisz zdarzenia w usłudze Azure Blob Storage itd. Aby uzyskać więcej informacji, zobacz [IoT Hub Routing komunikatów](../../iot-hub/iot-hub-devguide-messages-d2c.md) . 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>Uruchamianie analizy przestrzennej z zarejestrowanym plikiem wideo

Możesz użyć analizy przestrzennej z zarówno zarejestrowanym, jak i wideo na żywo. Aby użyć analizy przestrzennej dla zarejestrowanego wideo, spróbuj nagrać plik wideo i zapisać go jako plik MP4. Utwórz konto magazynu obiektów BLOB na platformie Azure lub Użyj istniejącego. Następnie zaktualizuj następujące ustawienia magazynu obiektów BLOB w Azure Portal:
    1. Zmiana **bezpiecznego transferu wymaganego** do **wyłączenia**
    2. Zmień **Zezwalaj na dostęp publiczny do obiektu BLOB** 

Przejdź do sekcji **kontenera** , a następnie utwórz nowy kontener lub Użyj istniejącego. Następnie Przekaż plik wideo do kontenera. Rozwiń Ustawienia pliku dla przekazanego pliku, a następnie wybierz pozycję **Generuj sygnaturę dostępu współdzielonego**. Pamiętaj o ustawieniu **daty wygaśnięcia** wystarczająco długo, aby uwzględnić okres testowania. Ustaw **dozwolone protokoły** *http* (*https* nie jest obsługiwane).

Kliknij pozycję **Generuj token SAS i adres URL** , a następnie skopiuj adres URL sygnatury dostępu współdzielonego obiektu BLOB. Zastąp ciąg rozpoczynający `https` się od `http` i Przetestuj adres URL w przeglądarce, która obsługuje odtwarzanie wideo.

Zastąp `VIDEO_URL` w manifeście wdrożenia [Azure Stack urządzenia brzegowego](https://go.microsoft.com/fwlink/?linkid=2142179), [maszyny stacjonarnej](https://go.microsoft.com/fwlink/?linkid=2152270)lub [maszyny wirtualnej platformy Azure z procesorem GPU](https://go.microsoft.com/fwlink/?linkid=2152189) z utworzonym adresem URL dla wszystkich wykresów. Ustaw wartość `VIDEO_IS_LIVE` na `false` i Wdróż ponownie kontener analizy przestrzennej z zaktualizowanym manifestem. Zobacz przykład poniżej.

Moduł analizy przestrzennej zacznie zużywać plik wideo i ciągle powtarzać się.


```json
"zonecrossing": {
    "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "Replace http url here",
        "VIDEO_SOURCE_ID": "personcountgraph",
        "VIDEO_IS_LIVE": false,
      "VIDEO_DECODE_GPU_INDEX": 0,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"do_calibration\": true }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"events\": [{\"type\": \"zonecrossing\", \"config\": {\"threshold\": 16.0, \"focus\": \"footprint\"}}]}]}"
    }
   },

```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpią problemy podczas uruchamiania lub uruchamiania kontenera, zobacz [telemetrię i rozwiązywanie problemów](spatial-analysis-logging.md) dotyczących typowych problemów. Ten artykuł zawiera również informacje dotyczące generowania i zbierania dzienników oraz zbierania informacji o kondycji systemu.

## <a name="billing"></a>Rozliczenia

Kontener analizy przestrzennej wysyła informacje o rozliczeniach do platformy Azure przy użyciu zasobu przetwarzanie obrazów na koncie platformy Azure. Korzystanie z analizy przestrzennej w publicznej wersji zapoznawczej jest obecnie bezpłatne. 

Kontenery usługi Azure Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z punktem końcowym pomiaru/rozliczeń. Należy włączyć kontenery do przekazywania informacji rozliczeniowych za pomocą punktu końcowego rozliczeń. Kontenery Cognitive Services nie wysyłają danych klienta, takich jak film wideo lub obraz, który jest analizowany, do firmy Microsoft.


## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy służące do pobierania, instalowania i uruchamiania kontenera analizy przestrzennej. Podsumowanie:

* Analiza przestrzenna jest kontenerem systemu Linux dla platformy Docker.
* Obrazy kontenerów są pobierane z Container Registry Microsoft.
* Obrazy kontenerów są uruchamiane jako moduły IoT w Azure IoT Edge.
* Jak skonfigurować kontener i wdrożyć go na komputerze-hoście.

## <a name="next-steps"></a>Następne kroki

* [Wdróż aplikację sieci Web zliczania osób](spatial-analysis-web-app.md)
* [Konfigurowanie operacji analizy przestrzennej](spatial-analysis-operations.md)
* [Rejestrowanie i rozwiązywanie problemów](spatial-analysis-logging.md)
* [Przewodnik umieszczania w aparacie](spatial-analysis-camera-placement.md)
* [Przewodnik umieszczania strefy i linii](spatial-analysis-zone-line-placement.md)
