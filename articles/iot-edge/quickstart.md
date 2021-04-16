---
title: 'Szybki start: tworzenie urządzenia Azure IoT Edge w systemie Windows | Microsoft Docs'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć urządzenie usługi IoT Edge, a następnie zdalnie wdrożyć wstępnie skompilowany kod z poziomu witryny Azure Portal.
author: rsameser
manager: kgremban
ms.author: riameser
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 9f0562d4471ac1129bf9bc7ecfee058cddac7c61
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533135"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Szybki start: wdrażanie pierwszego modułu IoT Edge na urządzeniu z systemem Windows (wersja zapoznawcza)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Wypróbuj te Azure IoT Edge tym przewodniku Szybki start, wdrażając konteneryzowany kod w systemie Linux na IoT Edge Windows. IoT Edge umożliwia zdalne zarządzanie kodem na urządzeniach w celu wysyłania większej liczby obciążeń na urządzenia brzegowe. W tym przewodniku Szybki start zalecamy użycie własnego urządzenia, aby zobaczyć, jak łatwo można używać Azure IoT Edge dla systemu Linux w systemie Windows.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Utwórz centrum IoT.
* Rejestrowanie urządzenia usługi IoT Edge w centrum IoT Hub.
* Zainstaluj i uruchom pakiet IoT Edge dla systemu Linux w środowisku uruchomieniowym systemu Windows na urządzeniu.
* Zdalne wdrażanie modułu na urządzeniu IoT Edge wysyłanie danych telemetrycznych.

![Diagram przedstawiający architekturę tego przewodnika Szybki start dla twojego urządzenia i chmury.](./media/quickstart/install-edge-full.png)

W tym przewodniku Szybki start opisano sposób Azure IoT Edge dla systemu Linux na urządzeniu z systemem Windows. Następnie wdrożysz moduł z Azure Portal na urządzeniu. Moduł, z których będziesz korzystać, to symulowany czujnik generujący dane dotyczące temperatury, wilgotności i ciśnienia. Inne Azure IoT Edge są kompilowane na podstawie pracy, która jest tutaj robisz, wdrażając moduły, które analizują symulowane dane na potrzeby szczegółowych informacji biznesowych.

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

>[!NOTE]
>IoT Edge dla systemu Linux w systemie Windows jest w publicznej [wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Wymagania wstępne

Przygotuj środowisko dla interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Utwórz grupę zasobów w chmurze, aby zarządzać wszystkimi zasobami, których będziesz używać w tym przewodniku Szybki start.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Upewnij się, IoT Edge spełnia następujące wymagania:

* Wersje
  * Windows 10 wersji 1809 lub nowszej; kompilacja 17763 lub nowszy
    * Professional, Enterprise, IoT Enterprise
  * Windows Server 2019 w wersji 17763 lub nowszej

* Wymagania sprzętowe
  * Minimalna ilość wolnej pamięci: 1 GB
  * Minimalna ilość wolnego miejsca na dysku: 10 GB

>[!NOTE]
>W tym przewodniku Szybki Windows Admin Center do utworzenia wdrożenia usługi IoT Edge dla systemu Linux w systemie Windows. Można również użyć programu PowerShell. Jeśli chcesz utworzyć wdrożenie przy użyciu programu PowerShell, wykonaj kroki opisane w przewodniku instalowania i aprowizowania aplikacji Azure IoT Edge dla systemu Linux na urządzeniu z [systemem Windows.](how-to-install-iot-edge-on-windows.md)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Rozpocznij od utworzenia centrum IoT przy użyciu interfejsu wiersza polecenia platformy Azure.

![Diagram przedstawiający krok tworzenia centrum T.](./media/quickstart/create-iot-hub.png)

Poziom bezpłatnych Azure IoT Hub działa w tym przewodniku Szybki start. Jeśli korzystasz IoT Hub w przeszłości i masz już utworzone centrum, możesz użyć tego centrum IoT.

Poniższy kod tworzy bezpłatne centrum **F1** w grupie zasobów `IoTEdgeResources` . Zastąp `{hub_name}` nazwą unikatową centrum IoT Hub. Tworzenie centrum IoT może potrwać kilka minut.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

Jeśli wystąpi błąd, ponieważ masz już jedno bezpłatne centrum w ramach subskrypcji, zmień wartość w sku `S1` na . Jeśli wystąpi błąd z komunikatem o tym, że nazwa centrum IoT jest niedostępny, ktoś inny ma już centrum o tej nazwie. Wypróbuj nową nazwę.

## <a name="register-an-iot-edge-device"></a>Rejestrowanie urządzenia usługi IoT Edge

Zarejestruj urządzenie usługi IoT Edge, korzystając z nowo utworzonego centrum IoT.

![Diagram przedstawiający krok rejestrowania urządzenia przy użyciu tożsamości centrum IoT.](./media/quickstart/register-device.png)

Utwórz tożsamość urządzenia symulowanego, aby umożliwić mu komunikowanie się z centrum IoT Hub. Tożsamość urządzenia jest przechowywana w chmurze, a w celu skojarzenia urządzenia fizycznego z tożsamością urządzenia używane są unikatowe parametry połączenia urządzenia.

IoT Edge urządzenia zachowują się i mogą być zarządzane inaczej niż typowe urządzenia IoT. Użyj `--edge-enabled` flagi , aby zadeklarować, że ta tożsamość dotyczy IoT Edge urządzenia.

1. W Azure Cloud Shell wprowadź następujące polecenie, aby utworzyć urządzenie o nazwie **myEdgeDevice** w centrum.

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     Jeśli wystąpi błąd kluczy zasad, upewnij się, że Cloud Shell jest uruchomiona najnowsza wersja rozszerzenia `iothubowner` usługi Azure IoT.

1. Wyświetl ciąg połączenia urządzenia, który łączy urządzenie fizyczne z jego tożsamością w IoT Hub. Zawiera nazwę centrum IoT, nazwę urządzenia i klucz współużytkowy, który uwierzytelnia połączenia między nimi.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. Skopiuj wartość klucza `connectionString` z danych wyjściowych JSON i zapisz ją. Ta wartość to parametry połączenia urządzenia. Użyjesz go do skonfigurowania środowiska uruchomieniowego IoT Edge w następnej sekcji.

     ![Zrzut ekranu przedstawiający dane wyjściowe connectionString w Cloud Shell.](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalowanie i uruchamianie środowiska uruchomieniowego usługi IoT Edge

Zainstaluj IoT Edge dla systemu Linux w systemie Windows na urządzeniu i skonfiguruj je przy użyciu parametrów połączenia urządzenia.

![Diagram przedstawiający krok uruchamiania środowiska IoT Edge uruchomieniowego.](./media/quickstart/start-runtime.png)

1. [Pobierz Windows Admin Center](https://aka.ms/wacdownload).

1. Postępuj zgodnie z monitami wyświetlanymi w kreatorze instalacji, aby Windows Admin Center na urządzeniu.

1. Otwórz Windows Admin Center.

1. Wybierz **ikonę koła zębatego** Ustawienia w prawym górnym rogu, a następnie wybierz **pozycję Rozszerzenia.**

1. Na karcie **Źródła danych** wybierz pozycję **Dodaj**.

1. Wprowadź `https://aka.ms/wac-insiders-feed` w polu tekstowym, a następnie wybierz pozycję **Dodaj**.

1. Po dodaniu kanału informacyjnego przejdź do karty **Dostępne rozszerzenia** i poczekaj na aktualizację listy rozszerzeń.

1. Z listy **Dostępne rozszerzenia wybierz** pozycję **Azure IoT Edge**.

1. Zainstaluj rozszerzenie.

1. Po zainstalowaniu rozszerzenia wybierz pozycję **Windows Admin Center** lewym górnym rogu, aby przejść do głównej strony pulpitu nawigacyjnego.

     Połączenie **hosta lokalnego** reprezentuje komputer, na którym działa Windows Admin Center.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Zrzut ekranu przedstawiający stronę startową administratora systemu Windows.":::

1. Wybierz pozycję **Dodaj**.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Zrzut ekranu przedstawiający wybieranie przycisku Dodaj w Windows Admin Center.":::

1. Na kafelku Azure IoT Edge wybierz pozycję **Utwórz nową,** aby uruchomić kreatora instalacji.

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Zrzut ekranu przedstawiający tworzenie nowego wdrożenia w Azure IoT Edge til.":::

1. Kontynuuj pracę kreatora instalacji, aby zaakceptować Postanowienia licencyjne dotyczące oprogramowania firmy Microsoft, a następnie wybierz przycisk **Dalej.**

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Zrzut ekranu przedstawiający wybieranie przycisku Dalej w celu kontynuowania pracy z kreatorem instalacji.":::

1. Wybierz **pozycję Opcjonalne dane diagnostyczne,** a następnie wybierz pozycję **Dalej: Wdrażanie.** Ten wybór zapewnia rozszerzone dane diagnostyczne, które pomagają firmie Microsoft monitorować i utrzymywać jakość usług.

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Zrzut ekranu przedstawiający opcje danych diagnostycznych.":::

1. Na **ekranie Wybierz urządzenie docelowe** wybierz żądane urządzenie docelowe, aby sprawdzić, czy spełnia ono minimalne wymagania. W tym przewodniku Szybki start instalujemy IoT Edge urządzeniu lokalnym, więc wybierz **połączenie localhost.** Jeśli urządzenie docelowe spełnia wymagania, wybierz przycisk **Dalej,** aby kontynuować.

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Zrzut ekranu przedstawiający listę Urządzenia docelowe.":::

1. Wybierz **przycisk Dalej,** aby zaakceptować ustawienia domyślne. Ekran wdrażania przedstawia proces pobierania pakietu, instalowania pakietu, konfigurowania hosta i końcowego konfigurowania maszyny wirtualnej z systemem Linux. Pomyślne wdrożenie wygląda następująco:

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Zrzut ekranu przedstawiający pomyślne wdrożenie.":::

1. Wybierz **pozycję Dalej: Połącz,** aby kontynuować pracę w celu Azure IoT Edge urządzenia z identyfikatorem urządzenia z wystąpienia centrum IoT.

1. Wklej wartości parametrów połączenia skopiowane [wcześniej w tym przewodniku Szybki start](#register-an-iot-edge-device) w polu Parametrów **połączenia** urządzenia. Następnie wybierz **pozycję Aprowizowanie przy użyciu wybranej metody**.

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Zrzut ekranu przedstawiający ciąg połączenia w polu Parametrów połączenia urządzenia.":::

1. Po zakończeniu aprowizowania wybierz pozycję **Zakończ,** aby zakończyć i wrócić Windows Admin Center ekranu startowego. Urządzenie powinno być wyświetlane jako IoT Edge urządzenie.

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Zrzut ekranu przedstawiający wszystkie połączenia w Windows Admin Center.":::

1. Wybierz urządzenie Azure IoT Edge, aby wyświetlić jego pulpit nawigacyjny. Zobaczysz, że obciążenia z bliźniaczej reprezentacji urządzenia Azure IoT Hub zostały wdrożone. Na **IoT Edge modułu powinien być** pokazywany jeden moduł z uruchomionym modułem **edgeAgent,** a stan IoT Edge powinien być aktywny **(uruchomiony).** 

Urządzenie usługi IoT Edge jest teraz skonfigurowane. Jest ono gotowe do uruchamiania modułów wdrożonych w chmurze.

## <a name="deploy-a-module"></a>Wdrażanie modułu

Zarządzając urządzeniem usługi Azure IoT Edge z chmury, wdróż moduł przesyłający dane telemetryczne do centrum IoT Hub.

![Diagram przedstawiający krok wdrażania modułu.](./media/quickstart/deploy-module.png)

<!--
[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2
-->

Jedną z kluczowych możliwości usługi Azure IoT Edge jest wdrażanie kodu na urządzeniach IoT Edge z chmury. *IoT Edge to pakiety* wykonywalne implementowane jako kontenery. W tej sekcji wdrożysz wstępnie sbudowaną moduł z sekcji [IoT Edge Modules](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) Azure Marketplace bezpośrednio z Azure IoT Hub.

Moduł, który wdrożysz w tej sekcji, symuluje czujnik i wysyła wygenerowane dane. Ten moduł jest przydatny, gdy rozpoczyna się pracę z usługą IoT Edge, ponieważ symulowane dane można wykorzystać przy programowaniu i testowaniu. Jeśli chcesz zobaczyć, co dokładnie robi ten moduł, możesz wyświetlić [kod źródłowy symulowanego czujnika temperatury](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Wykonaj następujące kroki, aby wdrożyć pierwszy moduł z Azure Marketplace.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.

1. W menu po lewej stronie w obszarze **Automatyczne Zarządzanie urządzeniami** wybierz **pozycję IoT Edge**.

1. Wybierz identyfikator urządzenia docelowego z listy urządzeń.

1. Na górnym pasku wybierz pozycję **Ustaw moduły**.

   ![Zrzut ekranu przedstawiający wybieranie opcji Ustaw moduły.](./media/quickstart/select-set-modules.png)

1. W **IoT Edge Modułów** otwórz  menu rozwijane Dodaj, a następnie wybierz pozycję Moduł **witryny Marketplace.**

   ![Zrzut ekranu przedstawiający menu rozwijane Dodaj.](./media/quickstart/add-marketplace-module.png)

1. W **IoT Edge Module Marketplace** wyszukaj i wybierz `Simulated Temperature Sensor` moduł.

   Moduł zostanie dodany do sekcji IoT Edge Modules z żądanym **stanem uruchomienia.**

1. Wybierz **pozycję Dalej: Trasy,** aby kontynuować pracę w następnym kroku kreatora.

   ![Zrzut ekranu przedstawiający kontynuowanie do następnego kroku po dodaniu modułu.](./media/quickstart/view-temperature-sensor-next-routes.png)

1. Na karcie **Trasy** usuń trasę domyślną, trasę , a następnie wybierz pozycję **Dalej:** Przeglądanie + tworzenie, aby kontynuować pracę kreatora.

   >[!Note]
   >Trasy są konstruowane przy użyciu par nazw i wartości. Na tej stronie powinny zostać wyświetlonych dwie trasy. Trasa domyślna, **route**, wysyła wszystkie komunikaty do IoT Hub (o nazwie `$upstream` ). Druga trasa, **SimulatedTemperatureSensorToIoTHub,** została utworzona automatycznie po dodaniu modułu z Azure Marketplace. Ta trasa wysyła wszystkie komunikaty z modułu symulowanej temperatury do IoT Hub. Możesz usunąć trasę domyślną, ponieważ w tym przypadku jest ona nadmiarowa.

   ![Zrzut ekranu przedstawiający usuwanie trasy domyślnej, a następnie przejście do następnego kroku.](./media/quickstart/delete-route-next-review-create.png)

1. Przejrzyj plik JSON, a następnie wybierz pozycję **Utwórz**. Plik JSON definiuje wszystkie moduły wdrażane na urządzeniu IoT Edge danych. Zobaczysz moduł **SimulatedTemperatureSensor** oraz dwa moduły środowiska uruchomieniowego: **edgeAgent** i **edgeHub.**

   >[!Note]
   >Podczas przesłania nowego wdrożenia na urządzenie usługi IoT Edge do urządzenia nie jest wypychane żadne powiadomienie. Nie jest to konieczne, ponieważ urządzenie regularnie wysyła do usługi IoT Hub zapytania w celu odebrania wszelkich nowych instrukcji. Jeśli urządzenie znajdzie zaktualizowany manifest wdrażania, użyje informacji o nowym wdrożeniu, aby ściągnąć obrazy modułów z chmury, a następnie zacznie uruchamiać moduły lokalnie. Ten proces może potrwać kilka minut.

1. Po utworzeniu szczegółów wdrożenia modułu kreator wróci do strony szczegółów urządzenia. Wyświetl stan wdrożenia na **karcie Moduły.**

   Powinny zostać wyświetlony trzy moduły: **$edgeAgent**, **$edgeHub** i **SimulatedTemperatureSensor.** Jeśli co najmniej jeden moduł ma wartość **YES** (Tak) w obszarze **SPECIFIED IN DEPLOYMENT** (OKREŚLONE WE WDROŻENIU), ale nie w obszarze REPORTED BY **DEVICE**(ZGŁOSZONE PRZEZ URZĄDZENIE), urządzenie IoT Edge nadal je uruchamia. Poczekaj kilka minut, a następnie odśwież stronę.

   ![Zrzut ekranu przedstawiający symulowany czujnik temperatury na liście wdrożonych modułów.](./media/quickstart/view-deployed-modules.png)

## <a name="view-the-generated-data"></a>Wyświetlanie wygenerowanych danych

W tym przewodniku Szybki start utworzono nowe urządzenie usługi IoT Edge i zainstalowano na nim środowisko uruchomieniowe usługi IoT Edge. Następnie za pomocą Azure Portal wdrożyliśmy moduł IoT Edge do uruchomienia na urządzeniu bez konieczności zmieniania samego urządzenia.

Wypchnięty moduł generuje przykładowe dane środowiska, których można użyć do późniejszego testowania. Symulowany czujnik monitoruje maszynę i środowisko wokół maszyny. Na przykład ten czujnik może być umieszczony w serwerowni, w hali fabrycznej lub na turbinie wiatrowej. Wysyłane komunikaty obejmują temperaturę i wilgotność otoczenia, temperaturę i ciśnienie maszyny oraz znacznik czasu. IoT Edge samouczków używają danych utworzonych w tym module jako danych testowych do analizy.

Z powłoki poleceń w Windows Admin Center upewnij się, że moduł wdrożony z chmury jest uruchomiony na IoT Edge urządzeniu.

1. Połącz się z nowo utworzonym IoT Edge urządzeniem.

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Zrzut ekranu przedstawiający wybieranie opcji Połącz w Windows Admin Center.":::

     Na **stronie Przegląd** zobaczysz listę modułów IoT Edge **i** **stan IoT Edge modułu.** Możesz wyświetlić wdrożone moduły i stan urządzenia.  

1. W **obszarze Narzędzia** wybierz pozycję **Powłoka poleceń.** Powłoka poleceń to terminal programu PowerShell, który automatycznie używa Secure Shell (SSH) do nawiązywania połączenia z maszyną wirtualną Azure IoT Edge z systemem Linux na komputerze z systemem Windows.

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Zrzut ekranu przedstawiający otwieranie powłoki poleceń.":::

1. Aby zweryfikować trzy moduły na urządzeniu, uruchom następujące polecenie powłoki Bash:

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Zrzut ekranu przedstawiający dane wyjściowe listy krawędzi T powłoki poleceń.":::

1. Wyświetl komunikaty wysyłane z modułu czujnika temperatury do chmury.

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >IoT Edge poleceniach wielkość liter jest w nich wrażliwa na nazwy modułów.

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Zrzut ekranu przedstawiający listę komunikatów wysyłanych z modułu do chmury.":::

Możesz również użyć rozszerzenia [usługi Azure IoT Hub,](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) aby Visual Studio Code, jak komunikaty docierają do centrum IoT.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz kontynuować pracę z samouczkami IoT Edge samouczków, pomiń ten krok. Możesz użyć urządzenia, które zostało zarejestrowane i ustawione w tym przewodniku Szybki start. W przeciwnym razie możesz usunąć utworzone zasoby platformy Azure, aby uniknąć nalicznych opłat.

Jeśli maszyna wirtualna i centrum IoT Hub zostały utworzone w nowej grupie zasobów, możesz usunąć tę grupę i wszystkie powiązane zasoby. Jeśli nie chcesz usuwać całej grupy, możesz usunąć poszczególne zasoby.

> [!IMPORTANT]
> Sprawdź zawartość grupy zasobów, aby upewnić się, że nie ma niczego, co chcesz zachować. Usunięcie grupy zasobów jest nieodwracalne.

Użyj następującego polecenia, aby usunąć grupę **IoTEdgeResources.** Usunięcie może potrwać kilka minut.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Aby potwierdzić, że grupa zasobów została usunięta, możesz użyć tego polecenia, aby wyświetlić listę grup zasobów.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>Usuwanie Azure IoT Edge dla systemu Linux w systemie Windows

Użyj rozszerzenia pulpitu nawigacyjnego w Windows Admin Center, aby odinstalować Azure IoT Edge dla systemu Linux w systemie Windows.

1. Połącz się z urządzeniem IoT Edge w Windows Admin Center. Ładowane jest rozszerzenie narzędzia pulpitu nawigacyjnego platformy Azure.

1. Wybierz pozycję **Odinstaluj**. Po Azure IoT Edge zostanie Windows Admin Center, Azure IoT Edge wpis połączenia urządzenia ze **strony startowej.**

>[!Note]
>Innym sposobem usunięcia Azure IoT Edge z systemu Windows jest wybranie opcji **Uruchom** ustawienia Aplikacje Azure IoT Edge  >    >    >    >  **Odinstaluj** na IoT Edge urządzeniu. Ta metoda usuwa Azure IoT Edge z IoT Edge, ale pozostawia połączenie w Windows Admin Center. Aby ukończyć usuwanie, odinstaluj Windows Admin Center **z** menu Ustawienia.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono urządzenie usługi IoT Edge i wdrożono na nim kod przy użyciu interfejsu usługi Azure IoT Edge w chmurze. Masz teraz urządzenie testowe generujące nieprzetworzone dane dotyczące jego środowiska.

Następnie skonfiguruj lokalne środowisko projektowe, aby rozpocząć tworzenie modułów IoT Edge, w których działa logika biznesowa.

> [!div class="nextstepaction"]
> [Rozpoczynanie tworzenia IoT Edge modułów](tutorial-develop-for-linux.md)
