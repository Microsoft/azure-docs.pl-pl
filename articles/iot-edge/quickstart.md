---
title: 'Szybki start: tworzenie urządzenia usługi Azure IoT Edge w systemie Windows | Microsoft Docs'
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
ms.openlocfilehash: 71e38059aceb7da63f3545610b9acfe48c5d3150
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663217"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Szybki Start: wdrażanie pierwszego modułu IoT Edge na urządzeniu z systemem Windows (wersja zapoznawcza)

Wypróbuj Azure IoT Edge w tym przewodniku Szybki Start, wdrażając kod kontenerowy w systemie Linux na urządzeniu z systemem Windows IoT Edge. IoT Edge umożliwia zdalne zarządzanie kodem na urządzeniach, dzięki czemu można wysyłać więcej obciążeń do krawędzi. W tym przewodniku szybki start zalecamy użycie własnego urządzenia, aby sprawdzić, jak łatwo jest używać Azure IoT Edge dla systemu Linux w systemie Windows.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Utwórz centrum IoT.
* Rejestrowanie urządzenia usługi IoT Edge w centrum IoT Hub.
* Zainstaluj i uruchom IoT Edge dla systemu Linux w środowisku uruchomieniowym Windows na urządzeniu.
* Zdalne wdrażanie modułu na urządzeniu IoT Edge i wysyłanie danych telemetrycznych.

![Diagram — architektura przewodnika Szybki start dla urządzenia i chmury](./media/quickstart/install-edge-full.png)

Ten przewodnik Szybki Start przeprowadzi Cię przez proces konfigurowania Azure IoT Edge dla systemu Linux na urządzeniu z systemem Windows. Następnie należy wdrożyć moduł z Azure Portal na urządzeniu. Moduł używany w tym przewodniku szybki start to symulowany czujnik, który generuje dane dotyczące temperatury, wilgotności i ciśnienia. Inne samouczki Azure IoT Edge kompilują się po pracy wykonywanej w tym miejscu przez wdrożenie dodatkowych modułów, które analizują symulowane dane dla szczegółowych informacji o firmie.

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

>[!NOTE]
>IoT Edge dla systemu Linux w systemie Windows jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Przygotuj środowisko dla interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Zasoby w chmurze:

* Grupa zasobów do zarządzania wszystkimi zasobami używanymi w tym przewodniku Szybki start.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Urządzenie usługi IoT Edge:

* Urządzenie musi być komputerem lub serwerem z systemem Windows w wersji 1809 lub nowszej
* Co najmniej 4 GB pamięci, zalecane 8 GB pamięci
* 10 GB wolnego miejsca na dysku

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

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

Zainstaluj IoT Edge dla systemu Linux w systemie Windows na urządzeniu i skonfiguruj go za pomocą parametrów połączenia urządzenia.

![Diagram — uruchamianie IoT Edge środowiska uruchomieniowego na urządzeniu](./media/quickstart/start-runtime.png)

1. [Pobierz centrum administracyjne systemu Windows](https://aka.ms/WACDownloadEFLOW).

1. Postępuj zgodnie z instrukcjami Kreatora instalacji, aby skonfigurować centrum administracyjne systemu Windows na urządzeniu.

1. Gdy jesteś w centrum administracyjnym systemu Windows, w prawym górnym rogu ekranu wybierz **ikonę koła zębatego ustawienia**

1. W menu Ustawienia w obszarze Brama wybierz pozycję **rozszerzenia** .

1. Wybierz kartę **źródła danych** , a następnie wybierz pozycję **Dodaj**.

1. Wprowadź https://aka.ms/wac-insiders-feed tekst w polu tekstowym, a następnie wybierz pozycję **Dodaj**.

1. Po dodaniu kanału informacyjnego przejdź do karty **dostępne rozszerzenia** . Aktualizacja listy rozszerzeń może chwilę potrwać.

1. Z listy **dostępnych rozszerzeń** wybierz pozycję **Azure IoT Edge**

1. **Zainstaluj** rozszerzenie

1. Po zainstalowaniu rozszerzenia przejdź do głównej strony pulpitu nawigacyjnego, wybierając pozycję **Centrum administracyjne systemu Windows** w lewym górnym rogu ekranu.

1. Zostanie wyświetlone połączenie hosta lokalnego reprezentujące komputer z uruchomionym centrum administracyjnym systemu Windows.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Zrzut ekranu — Strona startowa administratora systemu Windows":::

1. Wybierz pozycję **Dodaj**.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Zrzut ekranu — przycisk Dodaj strony początkowej administratora systemu Windows":::

1. Znajdź kafelek Azure IoT Edge i wybierz pozycję **Utwórz nowy**. Spowoduje to uruchomienie Kreatora instalacji.

    :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Zrzut ekranu — Azure IoT Edge dla systemu Linux na kafelku z systemem Windows":::

1. Przejdź do Kreatora instalacji, aby zaakceptować umowę licencyjną i wybierz pozycję **dalej** .

    :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Zrzut ekranu — Witamy w Kreatorze":::

1. Wybierz **opcjonalne dane diagnostyczne** , aby zapewnić rozszerzone dane diagnostyczne, które umożliwiają firmie Microsoft monitorowanie i konserwowanie jakości usług, a następnie kliknij przycisk **Dalej: Wdróż.**

    :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Zrzut ekranu — dane diagnostyczne":::

1. Na ekranie **Wybieranie urządzenia docelowego** wybierz odpowiednie urządzenie docelowe, aby sprawdzić, czy spełnia ono wymagania minimalne. Na potrzeby tego przewodnika Szybki Start instalujemy IoT Edge na urządzeniu lokalnym, dlatego należy wybrać połączenie localhost. Po potwierdzeniu wybierz pozycję **dalej** , aby kontynuować.

    :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Zrzut ekranu — wybierz urządzenie docelowe":::

1. Zaakceptuj ustawienia domyślne, wybierając pozycję **dalej**.

1. Na ekranie wdrożenia jest wyświetlany proces pobierania pakietu, instalowania pakietu, konfigurowania hosta i finalnej konfiguracji maszyny wirtualnej systemu Linux.  Pomyślne wdrożenie będzie wyglądać następująco:

    :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Zrzut ekranu — powodzenie wdrażania Kreatora":::

1. Kliknij przycisk **Dalej: Połącz** , aby przejść do ostatniego kroku, aby zainicjować obsługę administracyjną urządzenia Azure IoT Edge przy użyciu identyfikatora urządzenia z wystąpienia usługi IoT Hub.

1. Skopiuj parametry połączenia z urządzenia na platformie Azure IoT Hub i wklej je do pola parametry połączenia urządzenia. Następnie wybierz opcję **aprowizacji z wybraną metodą**.

    > [!NOTE]
    > Zobacz Krok 3 w poprzedniej sekcji, [Zarejestruj urządzenie IoT Edge](#register-an-iot-edge-device), aby pobrać parametry połączenia.

    :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Zrzut ekranu — Inicjowanie obsługi Kreatora":::

1. Po zakończeniu aprowizacji wybierz pozycję **Zakończ** , aby zakończyć, i wróć do ekranu startowego centrum administracyjnego systemu Windows. Teraz powinno być możliwe wyświetlenie urządzenia na liście jako urządzenie IoT Edge.

    :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Zrzut ekranu — Centrum administracyjne systemu Windows Azure IoT Edge urządzenie":::

1. Wybierz urządzenie Azure IoT Edge, aby wyświetlić jego pulpit nawigacyjny. Należy sprawdzić, czy zostały wdrożone obciążenia z sznurka urządzenia w usłudze Azure IoT Hub. **Lista modułów IoT Edge** powinna zawierać jeden moduł z systemem, **EdgeAgent**, a **stan IoT Edge** powinien być widoczny jako **aktywny (uruchomiony)**.

Urządzenie usługi IoT Edge jest teraz skonfigurowane. Jest ono gotowe do uruchamiania modułów wdrożonych w chmurze.

## <a name="deploy-a-module"></a>Wdrażanie modułu

Zarządzając urządzeniem usługi Azure IoT Edge z chmury, wdróż moduł przesyłający dane telemetryczne do centrum IoT Hub.

![Diagram — wdrażanie modułu z chmury do urządzenia](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

W tym przewodniku Szybki start utworzono nowe urządzenie usługi IoT Edge i zainstalowano na nim środowisko uruchomieniowe usługi IoT Edge. Następnie użyto witryny Azure Portal do wdrożenia modułu usługi IoT Edge w celu uruchomienia go na urządzeniu bez konieczności wprowadzenia zmian na samym urządzeniu.

W takim przypadku moduł, który wypychał, generuje przykładowe dane środowiska, których można użyć do testowania później. Symulowany czujnik monitoruje maszynę i środowisko wokół maszyny. Na przykład ten czujnik może być umieszczony w serwerowni, w hali fabrycznej lub na turbinie wiatrowej. Komunikat zawiera temperaturę i wilgotność otoczenia, temperaturę maszyny, ciśnienie oraz znacznik czasu. W samouczkach usługi IoT Edge dane utworzone przez ten moduł są używane jako dane testowe do analizy.

Upewnij się, że moduł wdrożony z chmury jest uruchomiony na urządzeniu IoT Edge, przechodząc do powłoki poleceń w centrum administracyjnym systemu Windows.

1. Nawiązywanie połączenia z nowo utworzonym urządzeniem IoT Edge

   :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Zrzut ekranu — łączenie urządzenia":::

2. Na stronie **Omówienie** zostanie wyświetlona **Lista modułów IoT Edge** i **stan IoT Edge** , w których można zobaczyć różne moduły, które zostały wdrożone, a także stan urządzenia.  

3. W obszarze **Narzędzia** wybierz pozycję **powłoka poleceń**. Powłoka poleceń jest terminalem programu PowerShell, który automatycznie używa protokołu SSH (Secure Shell) do nawiązywania połączenia z maszyną wirtualną z systemem Azure IoT Edge przy użyciu urządzenia PRZENOŚNEgo w systemie Windows.

   :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Zrzut ekranu — powłoka poleceń":::

4. Aby sprawdzić trzy moduły na urządzeniu, uruchom następujące **polecenie bash**:

   ```bash
   sudo iotedge list
   ```

   :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Zrzut ekranu — lista powłok poleceń":::

5. Wyświetl komunikaty wysyłane z modułu czujnika temperatury do chmury.

   ```bash
   iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >Przy odwoływaniu się do nazw modułów w poleceniach usługi IoT Edge jest rozróżniana wielkość liter.

   :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Zrzut ekranu — czujnik temperatury":::

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

### <a name="clean-removal-of-azure-iot-edge-for-linux-on-windows"></a>Czyste usuwanie Azure IoT Edge dla systemu Linux w systemie Windows

Azure IoT Edge dla systemu Linux w systemie Windows można odinstalować z urządzenia IoT Edge za pomocą rozszerzenia pulpitu nawigacyjnego w centrum administracyjnym systemu Windows.

1. Nawiąż połączenie z Azure IoT Edge dla systemu Linux na urządzeniu z systemem Windows w centrum administracyjnym systemu Windows. Zostanie załadowane rozszerzenie narzędzia pulpitu nawigacyjnego platformy Azure.
2. Wybierz pozycję **Odinstaluj**. Po usunięciu Azure IoT Edge dla systemu Linux w systemie Windows centrum administracyjne systemu Windows przejdzie do strony Start i usunie z listy wpis połączenia z urządzeniem Azure IoT Edge.

Innym sposobem usunięcia Azure IoT Edge z systemu Windows jest przejście do pozycji **Rozpocznij**  >  **Ustawienia**  >  **aplikacje**  >  **Azure IoT Edge**  >  **odinstalowania** na urządzeniu IoT Edge. Spowoduje to usunięcie Azure IoT Edge z urządzenia IoT Edge, ale pozostawienie połączenia za pośrednictwem Centrum administracyjnego systemu Windows. Centrum administracyjne systemu Windows można odinstalować również z poziomu menu Ustawienia.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono urządzenie usługi IoT Edge i wdrożono na nim kod przy użyciu interfejsu usługi Azure IoT Edge w chmurze. Masz teraz urządzenie testowe generujące dane pierwotne dotyczące jego otoczenia.

Następnym krokiem jest skonfigurowanie lokalnego środowiska programistycznego, aby można było rozpocząć tworzenie modułów IoT Edge, które uruchamiają logikę biznesową.

> [!div class="nextstepaction"]
> [Rozpocznij opracowywanie modułów IoT Edge](tutorial-develop-for-linux.md)
