---
title: 'Szybki start: tworzenie urządzenia usługi Azure IoT Edge w systemie Linux | Microsoft Docs'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć urządzenie IoT Edge w systemie Linux, a następnie wdrożyć wstępnie skompilowany kod zdalnie z Azure Portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 10a073914a79d29ae4b1c1d90ae5be624e7d7673
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303942"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Szybki Start: wdrażanie pierwszego modułu IoT Edge na wirtualnym urządzeniu z systemem Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Przetestuj Azure IoT Edge w tym przewodniku Szybki Start, wdrażając kod kontenerowy na wirtualnym urządzeniu z systemem Linux IoT Edge. IoT Edge umożliwia zdalne zarządzanie kodem na urządzeniach, dzięki czemu można wysyłać więcej obciążeń do krawędzi. W tym przewodniku szybki start zalecamy korzystanie z maszyny wirtualnej platformy Azure dla urządzenia IoT Edge, co pozwala na szybkie utworzenie maszyny testowej, a następnie usunięcie jej po zakończeniu.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie centrum IoT Hub.
* Rejestrowanie urządzenia usługi IoT Edge w centrum IoT Hub.
* Zainstaluj i uruchom środowisko uruchomieniowe IoT Edge na urządzeniu wirtualnym.
* Zdalne wdrażanie modułu na urządzeniu usługi IoT Edge.

![Diagram — architektura przewodnika Szybki start dla urządzenia i chmury](./media/quickstart-linux/install-edge-full.png)

Ten przewodnik Szybki Start przeprowadzi Cię przez proces tworzenia maszyny wirtualnej z systemem Linux, która jest skonfigurowana jako urządzenie IoT Edge. Następnie należy wdrożyć moduł z Azure Portal na urządzeniu. Moduł używany w tym przewodniku szybki start to symulowany czujnik, który generuje dane dotyczące temperatury, wilgotności i ciśnienia. Inne samouczki Azure IoT Edge kompilują się po pracy wykonywanej w tym miejscu przez wdrożenie dodatkowych modułów, które analizują symulowane dane dla szczegółowych informacji o firmie.

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Wymagania wstępne

Przygotuj środowisko dla interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Zasoby w chmurze:

* Grupa zasobów do zarządzania wszystkimi zasobami używanymi w tym przewodniku Szybki start. Korzystamy z przykładowej nazwy grupy zasobów **IoTEdgeResources** w tym przewodniku Szybki Start i poniższych samouczków.

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

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Poniższe polecenie interfejsu wiersza polecenia służy do tworzenia urządzenia IoT Edge na podstawie wstępnie skompilowanego szablonu [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) .

* W przypadku użytkowników bash lub Cloud Shell Skopiuj następujące polecenie do edytora tekstów, Zastąp tekst symbolu zastępczego informacjami, a następnie skopiuj do okna bash lub Cloud Shell:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
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

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Poniższe polecenie interfejsu wiersza polecenia służy do tworzenia urządzenia IoT Edge na podstawie wstępnie skompilowanego szablonu [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy/tree/1.2.0-rc4) .

* W przypadku użytkowników bash lub Cloud Shell Skopiuj następujące polecenie do edytora tekstów, Zastąp tekst symbolu zastępczego informacjami, a następnie skopiuj do okna bash lub Cloud Shell:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0-rc4/edgeDeploy.json" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* W przypadku użytkowników programu PowerShell Skopiuj następujące polecenie do okna programu PowerShell, a następnie zastąp tekst zastępczy własnymi informacjami:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0-rc4/edgeDeploy.json" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```
:::moniker-end
<!-- end 1.2 -->

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

<!--1.1 -->
:::moniker range="iotedge-2018-06"

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
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Sprawdź, czy IoT Edge jest uruchomiony. Następujące polecenie powinno zwrócić stan **OK** , jeśli IoT Edge jest uruchomiona, lub podać błędy usługi.

   ```bash
   sudo iotedge system status
   ```

   >[!TIP]
   >Uruchomienie poleceń `iotedge` wymaga podniesionych uprawnień. Po wylogowaniu się z komputera i ponownym zalogowaniu się do niego po raz pierwszy od zainstalowania środowiska uruchomieniowego usługi IoT Edge Twoje uprawnienia zostaną automatycznie zaktualizowane. Do tego momentu należy użyć `sudo` przed poleceniami.

2. Jeśli potrzebujesz rozwiązać problem z usługą, pobierz jej dzienniki.

   ```bash
   sudo iotedge system logs
   ```

3. Wyświetl wszystkie moduły uruchomione na urządzeniu usługi IoT Edge. Ponieważ usługa została właśnie uruchomiona po raz pierwszy, tylko moduł **edgeAgent** powinien być widoczny jako uruchomiony. Moduł edgeAgent jest domyślnie uruchamiany i ułatwia Instalowanie i uruchamianie wszelkich dodatkowych modułów wdrażanych na urządzeniu.

   ```bash
   sudo iotedge list
   ```

:::moniker-end
<!-- end 1.2 -->

Urządzenie usługi IoT Edge jest teraz skonfigurowane. Jest ono gotowe do uruchamiania modułów wdrożonych w chmurze.

## <a name="deploy-a-module"></a>Wdrażanie modułu

Zarządzając urządzeniem usługi Azure IoT Edge z chmury, wdróż moduł przesyłający dane telemetryczne do centrum IoT Hub.

![Diagram — wdrażanie modułu z chmury do urządzenia](./media/quickstart-linux/deploy-module.png)

<!-- [!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2 -->

Jedną z kluczowych możliwości Azure IoT Edge jest wdrożenie kodu na urządzeniach IoT Edge z chmury. *Moduły IoT Edge* są pakietami wykonywalnymi wdrożonymi jako kontenery. Ta sekcja obejmuje wdrożenie wstępnie skompilowanego modułu z [sekcji modułów IoT Edge w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) bezpośrednio z usługi Azure IoT Hub.

Moduł, który wdrożysz w tej sekcji, symuluje czujnik i wysyła wygenerowane dane. Ten moduł jest przydatny, gdy rozpoczyna się pracę z usługą IoT Edge, ponieważ symulowane dane można wykorzystać przy programowaniu i testowaniu. Jeśli chcesz zobaczyć, co dokładnie robi ten moduł, możesz wyświetlić [kod źródłowy symulowanego czujnika temperatury](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Wykonaj następujące kroki, aby uruchomić kreatora **ustawiania modułów** w celu wdrożenia pierwszego modułu z witryny Azure Marketplace.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.

1. Z menu po lewej stronie w obszarze **Automatyczne zarządzanie urządzeniami** wybierz pozycję **IoT Edge**.

1. Wybierz identyfikator urządzenia docelowego z listy urządzeń.

1. Na górnym pasku wybierz pozycję **Ustaw moduły**.

   ![Zrzut ekranu pokazujący Wybieranie modułów zestawu.](./media/quickstart/select-set-modules.png)

### <a name="modules"></a>Moduły

Pierwszym krokiem kreatora jest wybranie modułów, które mają być uruchamiane na urządzeniu.

W obszarze **IoT Edge modules** Otwórz menu rozwijane **Dodaj** , a następnie wybierz pozycję **moduł witryny Marketplace**.

   ![Zrzut ekranu pokazujący menu rozwijane Dodaj.](./media/quickstart/add-marketplace-module.png)

W **witrynie IoT Edge Portal Marketplace** Wyszukaj i wybierz `Simulated Temperature Sensor` moduł. Moduł zostanie dodany do sekcji modułów IoT Edge z żądanym stanem **uruchomienia** .

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Wybierz pozycję **Ustawienia środowiska uruchomieniowego** , aby otworzyć ustawienia modułów EdgeHub i edgeAgent. Ta sekcja ustawień służy do zarządzania modułami uruchomieniowymi przez dodanie zmiennych środowiskowych lub zmianę opcji tworzenia.

Zaktualizuj pole **obrazu** dla modułów EdgeHub i edgeAgent, aby używało znacznika wersji 1,2. Na przykład:

* `mcr.microsoft.com/azureiotedge-hub:1.2`
* `mcr.microsoft.com/azureiotedge-agent:1.2`

Wybierz pozycję **Zapisz** , aby zastosować zmiany do modułów czasu wykonywania.

:::moniker-end
<!--end 1.2-->

Wybierz pozycję **Dalej: trasy** , aby przejść do następnego kroku kreatora.

   ![Zrzut ekranu pokazujący przejście do następnego kroku po dodaniu modułu.](./media/quickstart/view-temperature-sensor-next-routes.png)

### <a name="routes"></a>Trasy

Na karcie **trasy** Usuń trasę **domyślną trasy,** a następnie wybierz kolejno pozycje **Dalej: przegląd + Utwórz** , aby przejść do następnego kroku kreatora.

   >[!Note]
   >Trasy są konstruowane przy użyciu par nazw i wartości. Na tej stronie powinny być widoczne dwie trasy. Trasa domyślna ( **Route**) wysyła wszystkie komunikaty do IoT Hub (nazywanego `$upstream` ). Druga trasa, **SimulatedTemperatureSensorToIoTHub**, została utworzona automatycznie podczas dodawania modułu z witryny Azure Marketplace. Ta trasa wysyła wszystkie komunikaty z modułu symulowanej temperatury do IoT Hub. Trasy domyślnej można usunąć, ponieważ jest ona nadmiarowa w tym przypadku.

   ![Zrzut ekranu pokazujący usunięcie trasy domyślnej, a następnie przejście do następnego kroku.](./media/quickstart/delete-route-next-review-create.png)

### <a name="review-and-create"></a>Przegląd i tworzenie

Przejrzyj plik JSON, a następnie wybierz pozycję **Utwórz**. Plik JSON definiuje wszystkie moduły, które są wdrażane na urządzeniu IoT Edge. Zobaczysz moduł **SimulatedTemperatureSensor** oraz dwa moduły uruchomieniowe, **edgeAgent** i **edgeHub**.

   >[!Note]
   >Podczas przesłania nowego wdrożenia na urządzenie usługi IoT Edge do urządzenia nie jest wypychane żadne powiadomienie. Nie jest to konieczne, ponieważ urządzenie regularnie wysyła do usługi IoT Hub zapytania w celu odebrania wszelkich nowych instrukcji. Jeśli urządzenie znajdzie zaktualizowany manifest wdrażania, użyje informacji o nowym wdrożeniu, aby ściągnąć obrazy modułów z chmury, a następnie zacznie uruchamiać moduły lokalnie. Ten proces może potrwać kilka minut.

Po utworzeniu szczegółów wdrożenia modułu Kreator powróci do strony szczegółów urządzenia. Wyświetl stan wdrożenia na karcie **moduły** .

Powinny być widoczne trzy moduły: **$edgeAgent**, **$edgeHub** i **SimulatedTemperatureSensor**. Jeśli co najmniej jeden z modułów ma **wartość tak** w obszarze **wdrożenie** , ale nie w obszarze **zgłoszone przez urządzenie**, urządzenie IoT Edge nadal je uruchamia. Zaczekaj kilka minut, a następnie Odśwież stronę.

   ![Zrzut ekranu pokazujący symulowany czujnik temperatury na liście wdrożonych modułów.](./media/quickstart/view-deployed-modules.png)

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

W tym przewodniku Szybki start utworzono nowe urządzenie usługi IoT Edge i zainstalowano na nim środowisko uruchomieniowe usługi IoT Edge. Następnie użyto witryny Azure Portal do wdrożenia modułu usługi IoT Edge w celu uruchomienia go na urządzeniu bez konieczności wprowadzenia zmian na samym urządzeniu.

W takim przypadku moduł, który wypychał, generuje przykładowe dane środowiska, których można użyć do testowania później. Symulowany czujnik monitoruje maszynę i środowisko wokół maszyny. Na przykład ten czujnik może być umieszczony w serwerowni, w hali fabrycznej lub na turbinie wiatrowej. Komunikat zawiera temperaturę i wilgotność otoczenia, temperaturę maszyny, ciśnienie oraz znacznik czasu. W samouczkach usługi IoT Edge dane utworzone przez ten moduł są używane jako dane testowe do analizy.

Otwórz ponownie wiersz polecenia na urządzeniu usługi IoT Edge lub użyj połączenia SSH z interfejsu wiersza polecenia platformy Azure. Upewnij się, że moduł wdrożony z chmury jest uruchomiony na urządzeniu usługi IoT Edge:

   ```bash
   sudo iotedge list
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
   ![Wyświetlanie trzech modułów na urządzeniu](./media/quickstart-linux/iotedge-list-2-version-201806.png)
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
   ![Wyświetlanie trzech modułów na urządzeniu](./media/quickstart-linux/iotedge-list-2-version-202011.png)
:::moniker-end

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
az group delete --name IoTEdgeResources --yes
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
