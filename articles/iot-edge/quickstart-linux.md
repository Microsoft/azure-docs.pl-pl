---
title: 'Szybki start: tworzenie urządzenia usługi Azure IoT Edge w systemie Linux | Microsoft Docs'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć urządzenie IoT Edge w systemie Linux, a następnie wdrożyć wstępnie skompilowany kod zdalnie z Azure Portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/02/2020
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 1feabdfa5b4715427b63e0d419b62d4c41bd1ee5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572392"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Szybki Start: wdrażanie pierwszego modułu IoT Edge na wirtualnym urządzeniu z systemem Linux

Przetestuj Azure IoT Edge w tym przewodniku Szybki Start, wdrażając kod kontenerowy na wirtualnym urządzeniu z systemem Linux IoT Edge. IoT Edge umożliwia zdalne zarządzanie kodem na urządzeniach, dzięki czemu można wysyłać więcej obciążeń do krawędzi. W tym przewodniku szybki start zalecamy korzystanie z maszyny wirtualnej platformy Azure dla urządzenia IoT Edge, co pozwala na szybkie utworzenie maszyny testowej z zainstalowaną usługą IoT Edge, a następnie usunięcie jej po zakończeniu.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie centrum IoT Hub.
* Rejestrowanie urządzenia usługi IoT Edge w centrum IoT Hub.
* Zainstaluj i uruchom środowisko uruchomieniowe IoT Edge na urządzeniu wirtualnym.
* Zdalne wdrażanie modułu na urządzeniu usługi IoT Edge.

![Diagram — architektura przewodnika Szybki start dla urządzenia i chmury](./media/quickstart-linux/install-edge-full.png)

Ten przewodnik Szybki Start przeprowadzi Cię przez proces tworzenia maszyny wirtualnej z systemem Linux, która jest skonfigurowana jako urządzenie IoT Edge. Następnie należy wdrożyć moduł z Azure Portal na urządzeniu. Moduł używany w tym przewodniku szybki start to symulowany czujnik, który generuje dane dotyczące temperatury, wilgotności i ciśnienia. Inne samouczki Azure IoT Edge kompilują się po pracy wykonywanej w tym miejscu przez wdrożenie dodatkowych modułów, które analizują symulowane dane dla szczegółowych informacji o firmie.

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przygotuj środowisko dla interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Zasoby w chmurze:

- Grupa zasobów do zarządzania wszystkimi zasobami używanymi w tym przewodniku Szybki start. Korzystamy z przykładowej nazwy grupy zasobów **IoTEdgeResources** w tym przewodniku Szybki Start i poniższych samouczków.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Rozpocznij pracę z przewodnikiem Szybki start od utworzenia centrum IoT za pomocą interfejsu wiersza polecenia platformy Azure.

![Diagram — tworzenie centrum IoT Hub w chmurze](./media/quickstart-linux/create-iot-hub.png)

W tym przewodniku Szybki start wystarcza warstwa bezpłatna usługi IoT Hub. Jeśli w przeszłości użyto IoT Hub i masz już utworzone centrum, możesz użyć tego Centrum IoT.

Poniższy kod tworzy wolne centrum **F1** w grupie zasobów **IoTEdgeResources**. Zamień na `{hub_name}` unikatową nazwę Centrum IoT. Utworzenie IoT Hub może potrwać kilka minut.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Jeśli wystąpi błąd, ponieważ w subskrypcji jest już jedno bezpłatne centrum, zmień jednostkę SKU na **S1**. Każda subskrypcja może zawierać tylko jedno bezpłatne centrum IoT Hub. Jeśli wystąpi błąd polegający na niedostępności nazwy centrum IoT Hub, oznacza to, że ktoś inny ma już centrum o takiej nazwie. Wypróbuj nową nazwę.

## <a name="register-an-iot-edge-device"></a>Rejestrowanie urządzenia usługi IoT Edge

Zarejestruj urządzenie usługi IoT Edge, korzystając z nowo utworzonego centrum IoT.

![Diagram — rejestrowanie urządzenia przy użyciu tożsamości usługi IoT Hub](./media/quickstart-linux/register-device.png)

Utwórz tożsamość urządzenia dla urządzenia IoT Edge, aby umożliwić mu komunikowanie się z centrum IoT. Tożsamość urządzenia jest przechowywana w chmurze, a w celu skojarzenia urządzenia fizycznego z tożsamością urządzenia używane są unikatowe parametry połączenia urządzenia.

Ponieważ urządzenia usługi IoT Edge zachowują się inaczej niż typowe urządzenia IoT, a także mogą być inaczej zarządzane, zadeklaruj tę tożsamość jako należącą do urządzenia usługi IoT Edge za pomocą flagi `--edge-enabled`.

1. W Azure Cloud Shell wprowadź następujące polecenie, aby utworzyć urządzenie o nazwie **myEdgeDevice** w centrum.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Jeśli wystąpi błąd dotyczący kluczy zasad iothubowner, upewnij się, że na Cloud Shell jest uruchomiona Najnowsza wersja rozszerzenia Azure-IoT.

2. Wyświetl parametry połączenia dla urządzenia, które łączy urządzenie fizyczne z jego tożsamością w IoT Hub. Zawiera nazwę Centrum IoT, nazwę urządzenia, a następnie klucz współużytkowany, który uwierzytelnia połączenia między nimi. Będziemy ponownie używać tych parametrów połączenia w następnej sekcji podczas konfigurowania urządzenia IoT Edge.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

   ![Wyświetl parametry połączenia z danych wyjściowych interfejsu wiersza polecenia](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Konfigurowanie urządzenia usługi IoT Edge

Utwórz maszynę wirtualną, na której znajduje się Azure IoT Edge środowisko uruchomieniowe.

![Diagram — uruchamianie środowiska uruchomieniowego na urządzeniu](./media/quickstart-linux/start-runtime.png)

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Składa się ono z trzech składników. *Demon IoT Edge Security* jest uruchamiany za każdym razem, gdy IoT Edge urządzenie zostanie uruchomione i rozpocznie rozruch urządzenia przez uruchomienie agenta IoT Edge. *Agent IoT Edge* ułatwia wdrażanie i monitorowanie modułów na urządzeniu IoT Edge, w tym centrum IoT Edge. *IoT Edge Hub* zarządza komunikacją między modułami na urządzeniu IoT Edge i między urządzeniem a IoT Hub.

Podczas konfigurowania środowiska uruchomieniowego należy podać parametry połączenia urządzenia. Jest to ciąg pobrany z interfejsu wiersza polecenia platformy Azure. Za pomocą tych parametrów urządzenie fizyczne jest kojarzone z tożsamością urządzenia usługi IoT Edge na platformie Azure.

### <a name="deploy-the-iot-edge-device"></a>Wdrażanie urządzenia IoT Edge

Ta sekcja używa szablonu Azure Resource Manager, aby utworzyć nową maszynę wirtualną i zainstalować na niej IoT Edge środowisko uruchomieniowe. Jeśli zamiast tego chcesz użyć własnego urządzenia z systemem Linux, możesz wykonać kroki instalacji opisane w temacie [Instalowanie środowiska uruchomieniowego Azure IoT Edge](how-to-install-iot-edge.md), a następnie powrócić do tego przewodnika Szybki Start.

Poniższe polecenie interfejsu wiersza polecenia służy do tworzenia urządzenia IoT Edge na podstawie wstępnie skompilowanego szablonu [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) .

* W przypadku użytkowników bash lub Cloud Shell Skopiuj następujące polecenie do edytora tekstów, Zastąp tekst symbolu zastępczego informacjami, a następnie skopiuj do okna bash lub Cloud Shell:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name
   <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* W przypadku użytkowników programu PowerShell Skopiuj następujące polecenie do okna programu PowerShell, a następnie zastąp tekst zastępczy własnymi informacjami:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://aka.ms/iotedge-vm-deploy" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

Ten szablon przyjmuje następujące parametry:

| Parametr | Opis |
| --------- | ----------- |
| **Grupa zasobów** | Grupa zasobów, w której zostaną utworzone zasoby. Użyj domyślnej **IoTEdgeResources** , która została użyta w tym artykule, lub podaj nazwę istniejącej grupy zasobów w subskrypcji. |
| **szablon — identyfikator URI** | Wskaźnik do szablonu Menedżer zasobów, który jest używany. |
| **dnsLabelPrefix** | Ciąg, który zostanie użyty do utworzenia nazwy hosta maszyny wirtualnej. Zastąp tekst zastępczy nazwą swojej maszyny wirtualnej. |
| **adminUsername** | Nazwa użytkownika dla konta administratora maszyny wirtualnej. Użyj przykładu **azureUser** lub podaj nową nazwę użytkownika. |
| **deviceConnectionString** | Parametry połączenia z tożsamości urządzenia w IoT Hub, które służą do konfigurowania środowiska uruchomieniowego IoT Edge na maszynie wirtualnej. Polecenie interfejsu wiersza polecenia w tym parametrze korzysta z parametrów połączenia. Zastąp tekst zastępczy nazwą Centrum IoT Hub. |
| **authenticationType** | Metoda uwierzytelniania dla konta administratora. Ten przewodnik Szybki Start używa uwierzytelniania **hasła** , ale można również ustawić ten parametr na **sshPublicKey**. |
| **adminPasswordOrKey** | Hasło lub wartość klucza SSH dla konta administratora. Zastąp tekst zastępczy bezpiecznym hasłem. Hasło musi składać się z co najmniej 12 znaków i zawierać trzy z czterech z następujących znaków: małe litery, wielkie litery, cyfry i znaki specjalne. |

Po zakończeniu wdrażania należy odbierać dane wyjściowe w formacie JSON w interfejsie wiersza polecenia, który zawiera informacje dotyczące protokołu SSH do nawiązania połączenia z maszyną wirtualną. Skopiuj wartość **publicznego wpisu SSH** **sekcji Outputs** :

   ![Pobierz publiczną wartość SSH z danych wyjściowych](./media/quickstart-linux/outputs-public-ssh.png)

### <a name="view-the-iot-edge-runtime-status"></a>Wyświetlanie stanu środowiska uruchomieniowego usługi IoT Edge

Pozostałe polecenia w tym przewodniku Szybki start są wykonywane na samym urządzeniu usługi IoT Edge, dzięki czemu można zobaczyć, co dzieje się na urządzeniu. Jeśli używasz maszyny wirtualnej, Połącz się z tą maszyną teraz przy użyciu skonfigurowanej nazwy użytkownika administratora i nazwy DNS, która została wyprodukowana przez polecenie wdrożenia. Nazwę DNS można również znaleźć na stronie przeglądowej maszyny wirtualnej w Azure Portal. Użyj następującego polecenia, aby nawiązać połączenie z maszyną wirtualną. Zamień `{admin username}` i `{DNS name}` na własne wartości.

   ```console
   ssh {admin username}@{DNS name}
   ```

Po nawiązaniu połączenia z maszyną wirtualną Sprawdź, czy środowisko uruchomieniowe zostało pomyślnie zainstalowane i skonfigurowane na urządzeniu IoT Edge.

1. Sprawdź, czy demon IoT Edge Security jest uruchomiony jako usługa systemowa.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Zobacz demon IoT Edge uruchomiony jako usługa systemowa](./media/quickstart-linux/iotedged-running.png)

   >[!TIP]
   >Uruchomienie poleceń `iotedge` wymaga podniesionych uprawnień. Po wylogowaniu się z komputera i ponownym zalogowaniu się do niego po raz pierwszy od zainstalowania środowiska uruchomieniowego usługi IoT Edge Twoje uprawnienia zostaną automatycznie zaktualizowane. Do tego momentu należy użyć `sudo` przed poleceniami.

2. Jeśli potrzebujesz rozwiązać problem z usługą, pobierz jej dzienniki.

   ```bash
   journalctl -u iotedge
   ```

3. Wyświetl wszystkie moduły uruchomione na urządzeniu usługi IoT Edge. Ponieważ usługa została właśnie uruchomiona po raz pierwszy, tylko moduł **edgeAgent** powinien być widoczny jako uruchomiony. Moduł edgeAgent jest domyślnie uruchamiany i ułatwia Instalowanie i uruchamianie wszelkich dodatkowych modułów wdrażanych na urządzeniu.

   ```bash
   sudo iotedge list
   ```

   ![Wyświetlanie jednego modułu na urządzeniu](./media/quickstart-linux/iotedge-list-1.png)

Urządzenie usługi IoT Edge jest teraz skonfigurowane. Jest ono gotowe do uruchamiania modułów wdrożonych w chmurze.

## <a name="deploy-a-module"></a>Wdrażanie modułu

Zarządzając urządzeniem usługi Azure IoT Edge z chmury, wdróż moduł przesyłający dane telemetryczne do centrum IoT Hub.

![Diagram — wdrażanie modułu z chmury do urządzenia](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

W tym przewodniku Szybki start utworzono nowe urządzenie usługi IoT Edge i zainstalowano na nim środowisko uruchomieniowe usługi IoT Edge. Następnie użyto witryny Azure Portal do wdrożenia modułu usługi IoT Edge w celu uruchomienia go na urządzeniu bez konieczności wprowadzenia zmian na samym urządzeniu.

W takim przypadku moduł, który wypychał, generuje przykładowe dane środowiska, których można użyć do testowania później. Symulowany czujnik monitoruje maszynę i środowisko wokół maszyny. Na przykład ten czujnik może być umieszczony w serwerowni, w hali fabrycznej lub na turbinie wiatrowej. Komunikat zawiera temperaturę i wilgotność otoczenia, temperaturę maszyny, ciśnienie oraz znacznik czasu. W samouczkach usługi IoT Edge dane utworzone przez ten moduł są używane jako dane testowe do analizy.

Otwórz ponownie wiersz polecenia na urządzeniu usługi IoT Edge lub użyj połączenia SSH z interfejsu wiersza polecenia platformy Azure. Upewnij się, że moduł wdrożony z chmury jest uruchomiony na urządzeniu usługi IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Wyświetlanie trzech modułów na urządzeniu](./media/quickstart-linux/iotedge-list-2.png)

Wyświetl komunikaty wysyłane z modułu czujnika temperatury:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >Przy odwoływaniu się do nazw modułów w poleceniach usługi IoT Edge jest rozróżniana wielkość liter.

   ![Wyświetlanie danych z modułu](./media/quickstart-linux/iotedge-logs.png)

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
> [Rozpocznij opracowywanie modułów IoT Edge dla urządzeń z systemem Linux](tutorial-develop-for-linux.md)
