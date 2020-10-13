---
title: 'Szybki start: tworzenie urządzenia usługi Azure IoT Edge w systemie Windows | Microsoft Docs'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć urządzenie usługi IoT Edge, a następnie zdalnie wdrożyć wstępnie skompilowany kod z poziomu witryny Azure Portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/30/2020
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: eae7a44d77d63641ba9ad062c504aaf624582668
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969215"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-windows-device"></a>Szybki Start: wdrażanie pierwszego modułu IoT Edge na urządzeniu wirtualnym z systemem Windows

Wypróbuj Azure IoT Edge w tym przewodniku Szybki Start, wdrażając kod kontenerowy na wirtualnym urządzeniu z systemem Windows IoT Edge. IoT Edge umożliwia zdalne zarządzanie kodem na urządzeniach, dzięki czemu można wysyłać więcej obciążeń do krawędzi. W tym przewodniku szybki start zalecamy korzystanie z maszyny wirtualnej platformy Azure dla urządzenia IoT Edge. Za pomocą maszyny wirtualnej można szybko utworzyć maszynę testową, zainstalować wymagania wstępne, a następnie usunąć ją po zakończeniu.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Utwórz centrum IoT.
* Rejestrowanie urządzenia usługi IoT Edge w centrum IoT Hub.
* Zainstaluj i uruchom środowisko uruchomieniowe IoT Edge na urządzeniu wirtualnym.
* Zdalne wdrażanie modułu do urządzenia usługi IoT Edge i wysyłanie telemetrii do usługi IoT Hub.

![Diagram — architektura przewodnika Szybki start dla urządzenia i chmury](./media/quickstart/install-edge-full.png)

Ten przewodnik Szybki Start przeprowadzi Cię przez proces tworzenia maszyny wirtualnej z systemem Windows i skonfigurowania jej jako urządzenia IoT Edge. Następnie należy wdrożyć moduł z Azure Portal na urządzeniu. Moduł używany w tym przewodniku szybki start to symulowany czujnik, który generuje dane dotyczące temperatury, wilgotności i ciśnienia. Inne samouczki Azure IoT Edge kompilują się po pracy wykonywanej w tym miejscu przez wdrożenie dodatkowych modułów, które analizują symulowane dane dla szczegółowych informacji o firmie.

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Użyj interfejsu wiersza polecenia platformy Azure, aby wykonać wiele kroków z tego przewodnika Szybki Start. Usługa Azure IoT ma rozszerzenie umożliwiające włączenie dodatkowych funkcji.

Dodaj rozszerzenie Azure IoT do wystąpienia Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zasoby w chmurze:

* Grupa zasobów do zarządzania wszystkimi zasobami używanymi w tym przewodniku Szybki start.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Urządzenie usługi IoT Edge:

* Maszyna wirtualna z systemem Windows, która będzie działać jako urządzenie IoT Edge. Tę maszynę wirtualną można utworzyć przy użyciu następującego polecenia, zastępując je `{password}` bezpiecznym hasłem:

  ```azurecli-interactive
  az vm create --resource-group IoTEdgeResources --name EdgeVM --image MicrosoftWindowsDesktop:Windows-10:rs5-pro:latest --admin-username azureuser --admin-password {password} --size Standard_DS1_v2
  ```

  Utworzenie i uruchomienie nowej maszyny wirtualnej może potrwać kilka minut.

  Po uruchomieniu maszyny wirtualnej można pobrać plik RDP do użycia podczas nawiązywania połączenia z maszyną wirtualną:

  1. Przejdź do nowej maszyny wirtualnej z systemem Windows w Azure Portal.
  1. Wybierz pozycję **Połącz**.
  1. Na karcie **RDP** wybierz pozycję **Pobierz plik RDP**.

  Otwórz ten plik z Podłączanie pulpitu zdalnego, aby nawiązać połączenie z maszyną wirtualną z systemem Windows przy użyciu nazwy użytkownika i hasła podanego za pomocą `az vm create` polecenia.

> [!NOTE]
> Maszyna wirtualna z systemem Windows zaczyna się od systemu Windows w wersji 1809 (kompilacja 17763), czyli najnowszej [kompilacji długoterminowej pomocy technicznej systemu Windows](https://docs.microsoft.com/windows/release-information/). System Windows automatycznie sprawdza aktualizacje co 22 godziny. Po sprawdzeniu maszyny wirtualnej system Windows wypycha aktualizację wersji, która jest niezgodna z IoT Edge dla systemu Windows, co uniemożliwi dalsze korzystanie z IoT Edge funkcji systemu Windows. Zalecamy ograniczenie używania maszyny wirtualnej do 22 godzin lub [czasowo wstrzymanie aktualizacji systemu Windows](https://support.microsoft.com/help/4028233/windows-10-manage-updates).
>
> W tym przewodniku szybki start do uproszczenia jest stosowana maszyna wirtualna z systemem Windows. Informacje o tym, które systemy operacyjne Windows są ogólnie dostępne w scenariuszach produkcyjnych, można znaleźć w temacie [Azure IoT Edge obsługiwane systemy](support.md).
>
> Aby skonfigurować własne urządzenie z systemem Windows na potrzeby IoT Edge, wykonaj kroki opisane w temacie [Install the Azure IoT Edge Runtime](how-to-install-iot-edge.md).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

Rozpocznij pracę z przewodnikiem Szybki start od utworzenia centrum IoT za pomocą interfejsu wiersza polecenia platformy Azure.

![Diagram — tworzenie centrum IoT Hub w chmurze](./media/quickstart/create-iot-hub.png)

W tym przewodniku Szybki start wystarcza warstwa bezpłatna usługi IoT Hub. Jeśli w przeszłości użyto IoT Hub i masz już utworzone centrum, możesz użyć tego Centrum IoT.

Poniższy kod tworzy bezpłatny koncentrator **F1** w grupie zasobów `IoTEdgeResources` . Zamień na `{hub_name}` unikatową nazwę Centrum IoT. Utworzenie IoT Hub może potrwać kilka minut.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Jeśli wystąpi błąd, ponieważ w subskrypcji jest już jedno bezpłatne centrum, zmień jednostkę SKU na **S1**. Jeśli wystąpi błąd polegający na niedostępności nazwy centrum IoT Hub, oznacza to, że ktoś inny ma już centrum o takiej nazwie. Wypróbuj nową nazwę.

## <a name="register-an-iot-edge-device"></a>Rejestrowanie urządzenia usługi IoT Edge

Zarejestruj urządzenie usługi IoT Edge, korzystając z nowo utworzonego centrum IoT.
![Diagram — rejestrowanie urządzenia przy użyciu tożsamości usługi IoT Hub](./media/quickstart/register-device.png)

Utwórz tożsamość urządzenia symulowanego, aby umożliwić mu komunikowanie się z centrum IoT Hub. Tożsamość urządzenia jest przechowywana w chmurze, a w celu skojarzenia urządzenia fizycznego z tożsamością urządzenia używane są unikatowe parametry połączenia urządzenia.

Ponieważ urządzenia usługi IoT Edge zachowują się inaczej niż typowe urządzenia IoT, a także mogą być inaczej zarządzane, zadeklaruj tę tożsamość jako należącą do urządzenia usługi IoT Edge za pomocą flagi `--edge-enabled`.

1. W Azure Cloud Shell wprowadź następujące polecenie, aby utworzyć urządzenie o nazwie **myEdgeDevice** w centrum.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Jeśli wystąpi błąd dotyczący kluczy zasad iothubowner, upewnij się, że na Cloud Shell jest uruchomiona Najnowsza wersja rozszerzenia Azure-IoT.

2. Wyświetl parametry połączenia dla urządzenia, które łączy urządzenie fizyczne z jego tożsamością w IoT Hub. Zawiera nazwę Centrum IoT, nazwę urządzenia, a następnie klucz współużytkowany, który uwierzytelnia połączenia między nimi.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Skopiuj wartość klucza `connectionString` z danych wyjściowych JSON i zapisz ją. Ta wartość to parametry połączenia urządzenia. Za pomocą tych parametrów połączenia skonfigurujesz środowisko uruchomieniowe usługi IoT Edge w następnej sekcji.

   ![Pobieranie parametrów połączenia z danych wyjściowych interfejsu wiersza polecenia](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalowanie i uruchamianie środowiska uruchomieniowego usługi IoT Edge

Zainstaluj środowisko uruchomieniowe usługi Azure IoT Edge na urządzeniu usługi IoT Edge i skonfiguruj je przy użyciu parametrów połączenia urządzenia.
![Diagram — uruchamianie środowiska uruchomieniowego na urządzeniu](./media/quickstart/start-runtime.png)

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Składa się ono z trzech składników. *Demon IoT Edge Security* jest uruchamiany za każdym razem, gdy IoT Edge urządzenie zostanie uruchomione i rozpocznie rozruch urządzenia przez uruchomienie agenta IoT Edge. *Agent usługi IoT Edge* ułatwia wdrażanie i monitorowanie modułów na urządzeniu usługi IoT Edge, w tym centrum usługi IoT Edge. *Centrum usługi IoT Edge* obsługuje komunikację między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub.

Skrypt instalacji zawiera także aparat kontenera o nazwie Moby, który zarządza obrazami kontenerów na urządzeniu usługi IoT Edge.

Podczas instalowania środowiska uruchomieniowego pojawi się prośba o podanie parametrów połączenia urządzenia. Użyj parametrów pobranych za pomocą wiersza polecenia platformy Azure. Za pomocą tych parametrów urządzenie fizyczne jest kojarzone z tożsamością urządzenia usługi IoT Edge na platformie Azure.

### <a name="connect-to-your-iot-edge-device"></a>Nawiązywanie połączenia z urządzeniem usługi IoT Edge

Kroki opisane w tej sekcji odbywają się na urządzeniu IoT Edge, dlatego chcesz połączyć się z tą maszyną wirtualną teraz za pośrednictwem pulpitu zdalnego.

### <a name="install-and-configure-the-iot-edge-service"></a>Instalowanie i Konfigurowanie usługi IoT Edge

Pobierz i zainstaluj środowisko uruchomieniowe usługi IoT Edge za pomocą programu PowerShell. Do skonfigurowania urządzenia użyj parametrów połączenia urządzenia pobranych z usługi IoT Hub.

1. Na maszynie wirtualnej Uruchom program PowerShell jako administrator.

   >[!NOTE]
   >Użyj sesji AMD64 programu PowerShell, aby zainstalować IoT Edge, a nie PowerShell (x86). Jeśli nie masz pewności, który typ sesji jest używany, uruchom następujące polecenie:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

2. Polecenie **Deploy-IoTEdge** sprawdza, czy komputer z systemem Windows jest w obsługiwanej wersji, włącza funkcję Containers, pobiera środowisko uruchomieniowe Moby, a następnie pobiera środowisko uruchomieniowe IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Windows
   ```

3. Komputer może zostać automatycznie uruchomiony ponownie. Jeśli zostanie wyświetlony monit Deploy-IoTEdge polecenie w celu ponownego uruchomienia systemu, zrób to teraz.

4. Uruchom ponownie program PowerShell jako administrator.

5. Polecenie **Initialize-IoTEdge** konfiguruje środowisko uruchomieniowe IoT Edge na komputerze. Polecenie domyślnie umożliwia ręczne Inicjowanie obsługi przy użyciu kontenerów systemu Windows.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Windows
   ```

6. Po wyświetleniu prośby o podanie wartości **DeviceConnectionString**, wpisz parametry skopiowane w poprzedniej sekcji. Nie dołączaj znaków cudzysłowów otaczających parametry połączenia.

### <a name="view-the-iot-edge-runtime-status"></a>Wyświetlanie stanu środowiska uruchomieniowego usługi IoT Edge

Sprawdź, czy środowisko uruchomieniowe zostało pomyślnie zainstalowane i skonfigurowane. Ukończenie instalacji może potrwać kilka minut, a moduł agenta IoT Edge zostanie uruchomiony.

1. Sprawdź stan usługi IoT Edge.

   ```powershell
   Get-Service iotedge
   ```

2. Jeśli potrzebujesz rozwiązać problem z usługą, pobierz jej dzienniki.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

3. Wyświetl wszystkie moduły uruchomione na urządzeniu usługi IoT Edge. Ponieważ usługa została właśnie uruchomiona po raz pierwszy, tylko moduł **edgeAgent** powinien być widoczny jako uruchomiony. Moduł edgeAgent jest domyślnie uruchamiany i ułatwia Instalowanie i uruchamianie wszelkich dodatkowych modułów wdrażanych na urządzeniu.

    ```powershell
    iotedge list
    ```

   ![Wyświetlanie jednego modułu na urządzeniu](./media/quickstart/iotedge-list-1.png)

Urządzenie usługi IoT Edge jest teraz skonfigurowane. Jest ono gotowe do uruchamiania modułów wdrożonych w chmurze.

## <a name="deploy-a-module"></a>Wdrażanie modułu

Zarządzając urządzeniem usługi Azure IoT Edge z chmury, wdróż moduł przesyłający dane telemetryczne do centrum IoT Hub.

![Diagram — wdrażanie modułu z chmury do urządzenia](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

W tym przewodniku Szybki start utworzono nowe urządzenie usługi IoT Edge i zainstalowano na nim środowisko uruchomieniowe usługi IoT Edge. Następnie użyto witryny Azure Portal do wdrożenia modułu usługi IoT Edge w celu uruchomienia go na urządzeniu bez konieczności wprowadzenia zmian na samym urządzeniu.

W takim przypadku moduł, który wypychał, generuje przykładowe dane środowiska, których można użyć do testowania później. Symulowany czujnik monitoruje maszynę i środowisko wokół maszyny. Na przykład ten czujnik może być umieszczony w serwerowni, w hali fabrycznej lub na turbinie wiatrowej. Komunikat zawiera temperaturę i wilgotność otoczenia, temperaturę maszyny, ciśnienie oraz znacznik czasu. W samouczkach usługi IoT Edge dane utworzone przez ten moduł są używane jako dane testowe do analizy.

Upewnij się, że moduł wdrożony z chmury jest uruchomiony na urządzeniu usługi IoT Edge.

```powershell
iotedge list
```

   ![Wyświetlanie trzech modułów na urządzeniu](./media/quickstart/iotedge-list-2.png)

Wyświetl komunikaty wysyłane z modułu czujnika temperatury do chmury.

```powershell
iotedge logs SimulatedTemperatureSensor -f
```

   >[!TIP]
   >Przy odwoływaniu się do nazw modułów w poleceniach usługi IoT Edge jest rozróżniana wielkość liter.

   ![Wyświetlanie danych z modułu](./media/quickstart/iotedge-logs.png)

Możesz również obejrzeć komunikaty docierające do centrum IoT Hub przy użyciu [rozszerzenia IoT Hub platformy Azure dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz przejść do samouczków dotyczących usługi IoT Edge, możesz użyć urządzenia, które zostało zarejestrowane i skonfigurowane w ramach tego przewodnika Szybki start. W przeciwnym razie możesz usunąć utworzone zasoby platformy Azure, aby uniknąć naliczania opłat.

Jeśli maszyna wirtualna i centrum IoT Hub zostały utworzone w nowej grupie zasobów, możesz usunąć tę grupę i wszystkie powiązane zasoby. Sprawdź dokładnie zawartość grupy zasobów, aby się upewnić, że nie ma w niej żadnych elementów, które chcesz zachować. Jeśli nie chcesz usuwać całej grupy, możesz usunąć poszczególne zasoby.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne.

Usuń grupę **IoTEdgeResources**. Usunięcie grupy zasobów może potrwać kilka minut.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Można potwierdzić, że grupa zasobów zostanie usunięta, wyświetlając listę grup zasobów.

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono urządzenie usługi IoT Edge i wdrożono na nim kod przy użyciu interfejsu usługi Azure IoT Edge w chmurze. Masz teraz urządzenie testowe generujące dane pierwotne dotyczące jego otoczenia.

Następnym krokiem jest skonfigurowanie lokalnego środowiska programistycznego, aby można było rozpocząć tworzenie modułów IoT Edge, które uruchamiają logikę biznesową.

> [!div class="nextstepaction"]
> [Rozpocznij opracowywanie modułów IoT Edge dla urządzeń z systemem Windows](tutorial-develop-for-windows.md)
