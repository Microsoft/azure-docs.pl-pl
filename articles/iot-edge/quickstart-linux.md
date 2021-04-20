---
title: 'Szybki start: tworzenie urządzenia usługi Azure IoT Edge w systemie Linux | Microsoft Docs'
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć urządzenie IoT Edge w systemie Linux, a następnie wdrożyć wstępnie utworzony kod zdalnie z Azure Portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cdc5dd2df0dc6ac682d37aea3328545fcb7e5ad2
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728612"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Szybki start: wdrażanie pierwszego modułu IoT Edge na wirtualnym urządzeniu z systemem Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Przetestuj Azure IoT Edge w tym przewodniku Szybki start, wdrażając konteneryzowany kod na wirtualnym urządzeniu IoT Edge Linux. IoT Edge umożliwia zdalne zarządzanie kodem na urządzeniach, dzięki czemu można wysyłać więcej obciążeń na urządzenia brzegowe. W tym przewodniku Szybki start zalecamy użycie maszyny wirtualnej platformy Azure dla urządzenia IoT Edge, która umożliwia szybkie utworzenie maszyny testowej, a następnie usunięcie jej po zakończeniu.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie centrum IoT Hub.
* Rejestrowanie urządzenia usługi IoT Edge w centrum IoT Hub.
* Zainstaluj i uruchom środowisko IoT Edge uruchomieniowego na urządzeniu wirtualnym.
* Zdalne wdrażanie modułu na urządzeniu usługi IoT Edge.

![Diagram — architektura przewodnika Szybki start dla urządzenia i chmury](./media/quickstart-linux/install-edge-full.png)

W tym przewodniku Szybki start otworzymy maszynę wirtualną z systemem Linux, która jest skonfigurowana jako IoT Edge urządzenia. Następnie wdrożysz moduł z Azure Portal na urządzeniu. Moduł używany w tym przewodniku Szybki start to symulowany czujnik, który generuje dane dotyczące temperatury, wilgotności i ciśnienia. Inne samouczki Azure IoT Edge są opierać się na pracy w tym miejscu przez wdrożenie dodatkowych modułów, które analizują symulowane dane na potrzeby szczegółowych informacji biznesowych.

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Wymagania wstępne

Przygotowywanie środowiska dla interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Zasoby w chmurze:

* Grupa zasobów do zarządzania wszystkimi zasobami używanymi w tym przewodniku Szybki start. W tym przewodniku Szybki start i w poniższych samouczkach używamy przykładowej nazwy grupy zasobów **IoTEdgeResources.**

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Rozpocznij pracę z przewodnikiem Szybki start od utworzenia centrum IoT za pomocą interfejsu wiersza polecenia platformy Azure.

![Diagram — tworzenie centrum IoT Hub w chmurze](./media/quickstart-linux/create-iot-hub.png)

W tym przewodniku Szybki start wystarcza warstwa bezpłatna usługi IoT Hub. Jeśli korzystasz z IoT Hub w przeszłości i masz już utworzone centrum, możesz użyć tego centrum IoT.

Poniższy kod tworzy bezpłatne centrum **F1** w grupie zasobów **IoTEdgeResources.** Zastąp `{hub_name}` nazwą unikatową centrum IoT Hub. Utworzenie aplikacji może potrwać kilka IoT Hub.

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

   Jeśli wystąpi błąd kluczy zasad iothubowner, upewnij się, że Cloud Shell jest uruchomiona najnowsza wersja rozszerzenia azure-iot.

2. Wyświetl ciąg połączenia urządzenia, który łączy urządzenie fizyczne z jego tożsamością w IoT Hub. Zawiera nazwę centrum IoT Hub, nazwę urządzenia, a następnie klucz współużytkowy, który uwierzytelnia połączenia między nimi. Ponownie będziemy odwoływać się do tych parametrów połączenia w następnej sekcji podczas IoT Edge urządzenia.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

   ![Wyświetlanie parametrów połączenia z danych wyjściowych interfejsu wiersza polecenia](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Konfigurowanie urządzenia usługi IoT Edge

Utwórz maszynę wirtualną z Azure IoT Edge uruchomieniowym.

![Diagram — uruchamianie środowiska uruchomieniowego na urządzeniu](./media/quickstart-linux/start-runtime.png)

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Składa się ono z trzech składników. Demon *IoT Edge zabezpieczeń* jest uruchamiany przy każdym uruchomieniu IoT Edge i uruchomieniu urządzenia przez uruchomienie IoT Edge agenta. Agent *IoT Edge ułatwia* wdrażanie i monitorowanie modułów na urządzeniu IoT Edge, w tym IoT Edge centrum danych. Centrum *IoT Edge zarządza* komunikacją między modułami na urządzeniu IoT Edge oraz między urządzeniem a IoT Hub.

Podczas konfigurowania środowiska uruchomieniowego należy podać parametry połączenia urządzenia. Jest to ciąg pobrany z interfejsu wiersza polecenia platformy Azure. Za pomocą tych parametrów urządzenie fizyczne jest kojarzone z tożsamością urządzenia usługi IoT Edge na platformie Azure.

### <a name="deploy-the-iot-edge-device"></a>Wdrażanie IoT Edge urządzenia

W tej sekcji użyto Azure Resource Manager do utworzenia nowej maszyny wirtualnej i zainstalowania na IoT Edge uruchomieniowego. Jeśli zamiast tego chcesz użyć własnego urządzenia z systemem Linux, możesz wykonać kroki instalacji opisane w te tematu Install the Azure IoT Edge runtime (Instalowanie środowiska uruchomieniowego usługi [Azure IoT Edge),](how-to-install-iot-edge.md)a następnie wrócić do tego przewodnika Szybki start.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Użyj następującego polecenia interfejsu wiersza polecenia, IoT Edge urządzenie oparte na wstępnie utworzonym [szablonie iotedge-vm-deploy.](https://github.com/Azure/iotedge-vm-deploy)

* W przypadku Cloud Shell bash lub Cloud Shell skopiuj następujące polecenie do edytora tekstów, zastąp tekst zastępczy swoimi informacjami, a następnie skopiuj do swojego okna bash lub Cloud Shell tekstowego:

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

* W przypadku użytkowników programu PowerShell skopiuj następujące polecenie do okna programu PowerShell, a następnie zastąp tekst zastępczy własnymi informacjami:

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

Użyj następującego polecenia interfejsu wiersza polecenia, IoT Edge urządzenie oparte na wstępnie utworzonym [szablonie iotedge-vm-deploy.](https://github.com/Azure/iotedge-vm-deploy/tree/1.2.0)

* W przypadku Cloud Shell bash lub Cloud Shell skopiuj następujące polecenie do edytora tekstów, zastąp tekst zastępczy swoimi informacjami, a następnie skopiuj do swojego okna bash lub Cloud Shell tekstowego:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* W przypadku użytkowników programu PowerShell skopiuj następujące polecenie do okna programu PowerShell, a następnie zastąp tekst zastępczy własnymi informacjami:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" `
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
| **grupa zasobów** | Grupa zasobów, w której zostaną utworzone zasoby. Użyj domyślnych **zasobów IoTEdgeResources,** których używaliśmy w tym artykule, lub podaj nazwę istniejącej grupy zasobów w subskrypcji. |
| **template-uri** | Wskaźnik do Resource Manager szablonu, z których korzystamy. |
| **dnsLabelPrefix** | Ciąg, który będzie używany do tworzenia nazwy hosta maszyny wirtualnej. Zastąp tekst zastępczy nazwą maszyny wirtualnej. |
| **adminUsername** | Nazwa użytkownika konta administratora maszyny wirtualnej. Użyj przykładu **azureUser** lub podaj nową nazwę użytkownika. |
| **deviceConnectionString** | Ciąg połączenia z tożsamości urządzenia w programie IoT Hub, który służy do konfigurowania IoT Edge uruchomieniowego na maszynie wirtualnej. Polecenie interfejsu wiersza polecenia w ramach tego parametru pobiera parametry połączenia. Zastąp tekst zastępczy nazwą centrum IoT. |
| **authenticationType** | Metoda uwierzytelniania dla konta administratora. W tym przewodniku Szybki start **jest używane** uwierzytelnianie za pomocą hasła, ale można również ustawić ten parametr na **wartość sshPublicKey.** |
| **adminPasswordOrKey** | Hasło lub wartość klucza SSH dla konta administratora. Zastąp tekst zastępczy bezpiecznym hasłem. Hasło musi mieć co najmniej 12 znaków i musi zawierać trzy z czterech z następujących znaków: małe litery, wielkie litery, cyfry i znaki specjalne. |

Po zakończeniu wdrażania w interfejsie wiersza polecenia powinny być odbierane dane wyjściowe w formacie JSON, które zawierają informacje OSH służące do nawiązywania połączenia z maszyną wirtualną. Skopiuj wartość publicznego **wpisu SSH** sekcji **outputs:**

   ![Pobieranie publicznej wartości SSH z danych wyjściowych](./media/quickstart-linux/outputs-public-ssh.png)

### <a name="view-the-iot-edge-runtime-status"></a>Wyświetlanie stanu środowiska uruchomieniowego usługi IoT Edge

Pozostałe polecenia w tym przewodniku Szybki start są wykonywane na samym urządzeniu usługi IoT Edge, dzięki czemu można zobaczyć, co dzieje się na urządzeniu. Jeśli używasz maszyny wirtualnej, połącz się z maszyną teraz przy użyciu nazwy użytkownika administratora, która została przez Ciebie ustawiona, oraz nazwy DNS, która została wyprowadzna przez polecenie wdrożenia. Nazwę DNS można również znaleźć na stronie przeglądu maszyny wirtualnej w Azure Portal. Użyj następującego polecenia, aby nawiązać połączenie z maszyną wirtualną. Zastąp `{admin username}` wartości `{DNS name}` i własnymi wartościami.

   ```console
   ssh {admin username}@{DNS name}
   ```

Po na połączeniu z maszyną wirtualną sprawdź, czy środowisko uruchomieniowe zostało pomyślnie zainstalowane i skonfigurowane IoT Edge urządzeniu.

<!--1.1 -->
:::moniker range="iotedge-2018-06"

1. Sprawdź, czy IoT Edge demon zabezpieczeń działa jako usługa systemowa.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Zobacz demona IoT Edge działającego jako usługa systemowa](./media/quickstart-linux/iotedged-running.png)

   >[!TIP]
   >Uruchomienie poleceń `iotedge` wymaga podniesionych uprawnień. Po wylogowaniu się z komputera i ponownym zalogowaniu się do niego po raz pierwszy od zainstalowania środowiska uruchomieniowego usługi IoT Edge Twoje uprawnienia zostaną automatycznie zaktualizowane. Do tego czasu użyj `sudo` polecenia przed poleceniami .

2. Jeśli potrzebujesz rozwiązać problem z usługą, pobierz jej dzienniki.

   ```bash
   journalctl -u iotedge
   ```

3. Wyświetl wszystkie moduły uruchomione na urządzeniu usługi IoT Edge. Ponieważ usługa została właśnie uruchomiona po raz pierwszy, tylko moduł **edgeAgent** powinien być widoczny jako uruchomiony. Moduł edgeAgent jest uruchamiany domyślnie i pomaga zainstalować i uruchomić wszelkie dodatkowe moduły wdrażane na urządzeniu.

   ```bash
   sudo iotedge list
   ```

   ![Wyświetlanie jednego modułu na urządzeniu](./media/quickstart-linux/iotedge-list-1.png)
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Sprawdź, czy IoT Edge działa. Następujące polecenie powinno zwrócić stan **OK,** jeśli IoT Edge uruchomiona, lub podać błędy usługi.

   ```bash
   sudo iotedge system status
   ```

   >[!TIP]
   >Uruchomienie poleceń `iotedge` wymaga podniesionych uprawnień. Po wylogowaniu się z komputera i ponownym zalogowaniu się do niego po raz pierwszy od zainstalowania środowiska uruchomieniowego usługi IoT Edge Twoje uprawnienia zostaną automatycznie zaktualizowane. Do tego czasu użyj `sudo` polecenia przed poleceniami .

2. Jeśli potrzebujesz rozwiązać problem z usługą, pobierz jej dzienniki.

   ```bash
   sudo iotedge system logs
   ```

3. Wyświetl wszystkie moduły uruchomione na urządzeniu usługi IoT Edge. Ponieważ usługa została właśnie uruchomiona po raz pierwszy, tylko moduł **edgeAgent** powinien być widoczny jako uruchomiony. Moduł edgeAgent jest uruchamiany domyślnie i pomaga zainstalować i uruchomić wszelkie dodatkowe moduły wdrażane na urządzeniu.

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

Jedną z kluczowych możliwości usługi Azure IoT Edge jest wdrażanie kodu na urządzeniach IoT Edge z chmury. *IoT Edge to pakiety* wykonywalne implementowane jako kontenery. W tej sekcji wdrożysz wstępnie sbudowaną moduł z sekcji [IoT Edge Modules](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) Azure Marketplace bezpośrednio z Azure IoT Hub.

Moduł, który wdrożysz w tej sekcji, symuluje czujnik i wysyła wygenerowane dane. Ten moduł jest przydatny, gdy rozpoczyna się pracę z usługą IoT Edge, ponieważ symulowane dane można wykorzystać przy programowaniu i testowaniu. Jeśli chcesz zobaczyć, co dokładnie robi ten moduł, możesz wyświetlić [kod źródłowy symulowanego czujnika temperatury](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Wykonaj następujące kroki, aby uruchomić kreatora **ustawianie modułów** w celu wdrożenia pierwszego modułu z Azure Marketplace.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT.

1. W menu po lewej stronie w obszarze **Automatyczne Zarządzanie urządzeniami** wybierz **pozycję IoT Edge.**

1. Wybierz identyfikator urządzenia docelowego z listy urządzeń.

1. Na górnym pasku wybierz pozycję **Ustaw moduły**.

   ![Zrzut ekranu przedstawiający wybieranie opcji Ustaw moduły.](./media/quickstart/select-set-modules.png)

### <a name="modules"></a>Moduły

Pierwszym krokiem kreatora jest wybranie modułów, które mają być uruchamiane na urządzeniu.

W **IoT Edge Modules (Moduły** witryny Marketplace) otwórz menu rozwijane **Add (Dodaj),** a następnie wybierz pozycję Marketplace **Module (Moduł witryny Marketplace).**

   ![Zrzut ekranu przedstawiający menu rozwijane Dodaj.](./media/quickstart/add-marketplace-module.png)

W **IoT Edge Module Marketplace** wyszukaj i wybierz `Simulated Temperature Sensor` moduł. Moduł zostanie dodany do sekcji IoT Edge Modules z żądanym **stanem** uruchomienia.

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Wybierz **pozycję Ustawienia środowiska** uruchomieniowego, aby otworzyć ustawienia dla modułów edgeHub i edgeAgent. W tej sekcji ustawień można zarządzać modułami środowiska uruchomieniowego, dodając zmienne środowiskowe lub zmieniając opcje tworzenia.

Zaktualizuj pole **Obraz** dla modułów edgeHub i edgeAgent, aby używać tagu wersji 1.2. Na przykład:

* `mcr.microsoft.com/azureiotedge-hub:1.2`
* `mcr.microsoft.com/azureiotedge-agent:1.2`

Wybierz **pozycję Zapisz,** aby zastosować zmiany do modułów środowiska uruchomieniowego.

:::moniker-end
<!--end 1.2-->

Wybierz **pozycję Dalej: Trasy,** aby kontynuować pracę kreatora.

   ![Zrzut ekranu przedstawiający kontynuowanie do następnego kroku po dodaniu modułu.](./media/quickstart/view-temperature-sensor-next-routes.png)

### <a name="routes"></a>Trasy

Na karcie **Trasy** usuń trasę domyślną, trasę , a następnie wybierz pozycję **Dalej:** Przeglądanie + tworzenie, aby kontynuować pracę kreatora.

   >[!Note]
   >Trasy są konstruowane przy użyciu par nazw i wartości. Na tej stronie powinny zostać wyświetlonych dwie trasy. Trasa domyślna, **route,** wysyła wszystkie komunikaty do IoT Hub (o nazwie `$upstream` ). Druga trasa, **SimulatedTemperatureSensorToIoTHub,** została utworzona automatycznie po dodaniu modułu z Azure Marketplace. Ta trasa wysyła wszystkie komunikaty z modułu symulowanej temperatury do IoT Hub. Możesz usunąć trasę domyślną, ponieważ w tym przypadku jest ona nadmiarowa.

   ![Zrzut ekranu przedstawiający usuwanie trasy domyślnej, a następnie przejście do następnego kroku.](./media/quickstart/delete-route-next-review-create.png)

### <a name="review-and-create"></a>Przegląd i tworzenie

Przejrzyj plik JSON, a następnie wybierz pozycję **Utwórz**. Plik JSON definiuje wszystkie moduły wdrażane na urządzeniu IoT Edge danych. Zobaczysz moduł **SimulatedTemperatureSensor** oraz dwa moduły środowiska uruchomieniowego: **edgeAgent** i **edgeHub.**

   >[!Note]
   >Podczas przesłania nowego wdrożenia na urządzenie usługi IoT Edge do urządzenia nie jest wypychane żadne powiadomienie. Nie jest to konieczne, ponieważ urządzenie regularnie wysyła do usługi IoT Hub zapytania w celu odebrania wszelkich nowych instrukcji. Jeśli urządzenie znajdzie zaktualizowany manifest wdrażania, użyje informacji o nowym wdrożeniu, aby ściągnąć obrazy modułów z chmury, a następnie zacznie uruchamiać moduły lokalnie. Ten proces może potrwać kilka minut.

Po utworzeniu szczegółów wdrożenia modułu kreator wróci do strony szczegółów urządzenia. Wyświetl stan wdrożenia na **karcie Moduły.**

Powinny zostać wyświetlony trzy moduły: **$edgeAgent**, **$edgeHub** i **SimulatedTemperatureSensor.** Jeśli co najmniej jeden moduł ma wartość **YES** (Tak) w obszarze **SPECIFIED IN DEPLOYMENT** (OKREŚLONE WE WDROŻENIU), ale nie w obszarze REPORTED BY **DEVICE**(ZGŁOSZONE PRZEZ URZĄDZENIE), urządzenie IoT Edge nadal je uruchamia. Poczekaj kilka minut, a następnie odśwież stronę.

   ![Zrzut ekranu przedstawiający symulowany czujnik temperatury na liście wdrożonych modułów.](./media/quickstart/view-deployed-modules.png)

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

W tym przewodniku Szybki start utworzono nowe urządzenie usługi IoT Edge i zainstalowano na nim środowisko uruchomieniowe usługi IoT Edge. Następnie użyto witryny Azure Portal do wdrożenia modułu usługi IoT Edge w celu uruchomienia go na urządzeniu bez konieczności wprowadzenia zmian na samym urządzeniu.

W tym przypadku wypchowany moduł generuje przykładowe dane środowiska, których można użyć do późniejszego testowania. Symulowany czujnik monitoruje maszynę i środowisko wokół maszyny. Na przykład ten czujnik może być umieszczony w serwerowni, w hali fabrycznej lub na turbinie wiatrowej. Komunikat zawiera temperaturę i wilgotność otoczenia, temperaturę maszyny, ciśnienie oraz znacznik czasu. W samouczkach usługi IoT Edge dane utworzone przez ten moduł są używane jako dane testowe do analizy.

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

Możesz również obserwować komunikaty odbierane do centrum IoT przy użyciu [rozszerzenia Azure IoT Hub dla usługi Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz przejść do samouczków dotyczących usługi IoT Edge, możesz użyć urządzenia, które zostało zarejestrowane i skonfigurowane w ramach tego przewodnika Szybki start. W przeciwnym razie możesz usunąć utworzone zasoby platformy Azure, aby uniknąć nalicznych opłat.

Jeśli maszyna wirtualna i centrum IoT Hub zostały utworzone w nowej grupie zasobów, możesz usunąć tę grupę i wszystkie powiązane zasoby. Sprawdź dokładnie zawartość grupy zasobów, aby się upewnić, że nie ma w niej żadnych elementów, które chcesz zachować. Jeśli nie chcesz usuwać całej grupy, możesz usunąć poszczególne zasoby.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne.

Usuń grupę **IoTEdgeResources**. Usunięcie grupy zasobów może potrwać kilka minut.

```azurecli-interactive
az group delete --name IoTEdgeResources --yes
```

Możesz potwierdzić, że grupa zasobów została usunięta, wyświetlając listę grup zasobów.

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono urządzenie usługi IoT Edge i wdrożono na nim kod przy użyciu interfejsu usługi Azure IoT Edge w chmurze. Masz teraz urządzenie testowe generujące dane pierwotne dotyczące jego otoczenia.

Następnym krokiem jest skonfigurowanie lokalnego środowiska projektowego, aby można było rozpocząć tworzenie modułów IoT Edge, w których działa logika biznesowa.

> [!div class="nextstepaction"]
> [Rozpoczynanie tworzenia IoT Edge modułów dla urządzeń z systemem Linux](tutorial-develop-for-linux.md)
