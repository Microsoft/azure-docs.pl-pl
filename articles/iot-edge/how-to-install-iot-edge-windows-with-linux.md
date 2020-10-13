---
title: Instalowanie Azure IoT Edge dla systemu Linux w systemie Windows | Microsoft Docs
description: Instrukcje dotyczące instalacji Azure IoT Edge dla kontenerów systemu Linux w systemach Windows 10, Windows Server i Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: c3a23e0c2546da55f977d589eb38607994d3902b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88611793"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Używanie IoT Edge w systemie Windows do uruchamiania kontenerów systemu Linux

Testy IoT Edge modułów dla urządzeń z systemem Linux przy użyciu maszyny z systemami Windows.

W scenariuszu produkcyjnym urządzenia z systemem Windows powinny uruchamiać tylko kontenery systemu Windows. Typowym scenariuszem programistycznym jest jednak użycie komputera z systemem Windows do kompilowania modułów IoT Edge dla urządzeń z systemem Linux. Środowisko uruchomieniowe IoT Edge dla systemu Windows umożliwia uruchamianie kontenerów z systemem Linux na potrzeby **testowania i programowania** .

W tym artykule przedstawiono procedurę instalowania środowiska uruchomieniowego Azure IoT Edge przy użyciu kontenerów systemu Linux w systemie Windows x64 (AMD/Intel). Aby dowiedzieć się więcej na temat instalatora środowiska uruchomieniowego IoT Edge, w tym szczegółowe informacje o wszystkich parametrach instalacji, zobacz [Instalowanie środowiska uruchomieniowego Azure IoT Edge w systemie Windows](how-to-install-iot-edge-windows.md).

Aby uzyskać informacje o tym, co zawiera Najnowsza wersja IoT Edge, zobacz [wersje Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Wymagania wstępne

Ta sekcja służy do sprawdzenia, czy urządzenie z systemem Windows może obsługiwać IoT Edge i przygotować je do aparatu kontenera przed instalacją.

### <a name="supported-windows-versions"></a>Obsługiwane wersje systemu Windows

Azure IoT Edge z kontenerami systemu Linux można uruchamiać w dowolnym systemie Windows, który spełnia [wymagania programu Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Jeśli chcesz zainstalować IoT Edge na maszynie wirtualnej, Włącz wirtualizację zagnieżdżoną i Przydziel co najmniej 2 GB pamięci. Sposób włączania wirtualizacji zagnieżdżonej różni się w zależności od używanej funkcji hypervisor. W przypadku maszyn wirtualnych funkcji Hyper-V maszyny wirtualne generacji 2 mają domyślnie włączoną wirtualizację zagnieżdżoną. W przypadku oprogramowania VMware istnieje przełącznik umożliwiający włączenie tej funkcji na maszynie wirtualnej.

### <a name="prepare-the-container-engine"></a>Przygotowywanie aparatu kontenera

Azure IoT Edge opiera się na aparacie kontenera [zgodnym ze sterownikiem OCI](https://www.opencontainers.org/) . Największą różnicą między uruchomionymi kontenerami systemów Windows i Linux na komputerze z systemem Windows jest to, że instalacja IoT Edge obejmuje środowisko uruchomieniowe kontenera systemu Windows, ale przed zainstalowaniem IoT Edge należy zapewnić własne środowisko uruchomieniowe dla kontenerów z systemem Linux.

Aby skonfigurować maszynę z systemem Windows do tworzenia i testowania kontenerów dla urządzeń z systemem Linux, można użyć programu [Docker Desktop](https://www.docker.com/docker-windows) jako aparatu kontenera. Przed zainstalowaniem IoT Edge należy zainstalować platformę Docker i skonfigurować ją do [korzystania z kontenerów systemu Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) .  

Jeśli urządzenie IoT Edge jest komputerem z systemem Windows, sprawdź, czy spełnia [wymagania systemowe](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) dla funkcji Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Instalowanie IoT Edge na nowym urządzeniu

>[!NOTE]
>Azure IoT Edge pakiety oprogramowania podlegają postanowieniom licencyjnym znajdującym się w pakietach (w katalogu licencji). Przeczytaj postanowienia licencyjne przed użyciem pakietu. Instalacja i użycie pakietu oznacza akceptację tych warunków. Jeśli nie akceptujesz postanowień licencyjnych, nie używaj pakietu.

Skrypt programu PowerShell pobiera i instaluje demona Security Azure IoT Edge. Następnie demon zabezpieczeń uruchamia pierwsze dwa moduły środowiska uruchomieniowego, Agent IoT Edge, który umożliwia zdalne wdrażanie innych modułów.

W przypadku instalowania środowiska uruchomieniowego IoT Edge po raz pierwszy na urządzeniu należy zainicjować obsługę administracyjną urządzenia z tożsamością usługi IoT Hub. Pojedyncze urządzenie IoT Edge może być inicjowane ręcznie przy użyciu parametrów połączenia urządzenia dostarczonych przez Centrum IoT Hub. Można też użyć usługi Device Provisioning, aby automatycznie zainicjować obsługę administracyjną urządzeń, co jest przydatne, gdy istnieje wiele urządzeń do skonfigurowania.

Więcej informacji na temat różnych opcji instalacji i parametrów można znaleźć w artykule [Instalowanie środowiska uruchomieniowego Azure IoT Edge w systemie Windows](how-to-install-iot-edge-windows.md). Po zainstalowaniu i skonfigurowaniu programu Docker Desktop dla kontenerów systemu Linux główna różnica instalacji deklaruje system Linux z parametrem **-ContainerOs** . Na przykład:

1. Jeśli jeszcze tego nie zrobiono, Zarejestruj nowe urządzenie IoT Edge i Pobierz parametry połączenia urządzenia. Skopiuj parametry połączenia, aby użyć ich w dalszej części tej sekcji. Ten krok można wykonać przy użyciu następujących narzędzi:

   * [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Interfejs wiersza polecenia platformy Azure](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Uruchom program PowerShell jako administrator.

   >[!NOTE]
   >Użyj sesji AMD64 programu PowerShell, aby zainstalować IoT Edge, a nie PowerShell (x86). Jeśli nie masz pewności, który typ sesji jest używany, uruchom następujące polecenie:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Polecenie **Deploy-IoTEdge** sprawdza, czy komputer z systemem Windows jest w obsługiwanej wersji, włącza funkcję Containers, a następnie pobiera środowisko uruchomieniowe Moby (które nie jest używane dla kontenerów systemu Linux) i środowisko uruchomieniowe IoT Edge. Polecenie domyślnie jest kontenerami systemu Windows, więc należy zadeklarować system Linux jako żądany systemu operacyjnego kontenera.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. W tym momencie urządzenia IoT Core mogą zostać automatycznie uruchomione ponownie. Inne urządzenia z systemem Windows 10 lub Windows Server mogą monitować o ponowne uruchomienie. Jeśli tak, ponownie uruchom urządzenie teraz. Gdy urządzenie jest gotowe, uruchom ponownie program PowerShell jako administrator.

5. Polecenie **Initialize-IoTEdge** konfiguruje środowisko uruchomieniowe IoT Edge na komputerze. Polecenie domyślnie umożliwia ręczne Inicjowanie obsługi przy użyciu parametrów połączenia urządzenia. Należy ponownie zadeklarować system Linux jako żądany systemu operacyjnego kontenera.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Po wyświetleniu monitu podaj parametry połączenia urządzenia pobrane w kroku 1. Parametry połączenia urządzenia kojarzą urządzenie fizyczne z IDENTYFIKATORem urządzenia w IoT Hub.

   Parametry połączenia urządzenia mają następujący format i nie powinny zawierać znaków cudzysłowu: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Weryfikuj pomyślną instalację

Sprawdź stan usługi IoT Edge:

```powershell
Get-Service iotedge
```

Przeanalizuj dzienniki usług z ostatnich 5 minut:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Uruchom automatyczne sprawdzanie najbardziej typowych błędów konfiguracji i sieci:

```powershell
iotedge check
```

Wyświetl listę uruchomionych modułów. Po nowej instalacji jedynym modułem, który powinien zostać uruchomiony, jest **edgeAgent**. Po [wdrożeniu modułów IoT Edge](how-to-deploy-modules-portal.md) po raz pierwszy na urządzeniu zostanie uruchomiony inny moduł systemowy **edgeHub**.

```powershell
iotedge list
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz zainstalowaną IoT Edge urządzenie z zainstalowanym środowiskiem uruchomieniowym, możesz [wdrożyć moduły IoT Edge](how-to-deploy-modules-portal.md).

Jeśli masz problemy z instalacją IoT Edge prawidłowo, zapoznaj się ze stroną [rozwiązywania problemów](troubleshoot.md) .

Aby zaktualizować istniejącą instalację do najnowszej wersji IoT Edge, zobacz [Aktualizacja demona IoT Edge Security daemon i środowisko uruchomieniowe](how-to-update-iot-edge.md).
