---
title: Przewodnik Szybki Start dotyczący tworzenia urządzenia Azure IoT Edge w systemie Windows | Microsoft Docs
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
ms.openlocfilehash: a00e5d262724f4799f8b7b91a54e4b2710f4ce2b
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008081"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Szybki Start: wdrażanie pierwszego modułu IoT Edge na urządzeniu z systemem Windows (wersja zapoznawcza)

Wypróbuj Azure IoT Edge w tym przewodniku Szybki Start, wdrażając kod kontenerowy w systemie Linux na urządzeniu z systemem Windows IoT Edge. IoT Edge umożliwia zdalne zarządzanie kodem na urządzeniach, dzięki czemu można wysyłać więcej obciążeń do krawędzi. W tym przewodniku szybki start zalecamy użycie własnego urządzenia, aby sprawdzić, jak łatwo jest używać Azure IoT Edge dla systemu Linux w systemie Windows.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Utwórz centrum IoT.
* Rejestrowanie urządzenia usługi IoT Edge w centrum IoT Hub.
* Zainstaluj i uruchom IoT Edge dla systemu Linux w środowisku uruchomieniowym Windows na urządzeniu.
* Zdalne wdrażanie modułu na urządzeniu IoT Edge i wysyłanie danych telemetrycznych.

![Diagram przedstawiający architekturę tego przewodnika Szybki Start dla Twojego urządzenia i chmury.](./media/quickstart/install-edge-full.png)

Ten przewodnik Szybki Start przeprowadzi Cię przez proces konfigurowania Azure IoT Edge dla systemu Linux na urządzeniu z systemem Windows. Następnie na urządzeniu zostanie wdrożony moduł z Azure Portal. Moduł, którego będziesz używać, to symulowany czujnik, który generuje dane dotyczące temperatury, wilgotności i ciśnienia. Inne samouczki Azure IoT Edge kompilują zadania wykonywane w tym miejscu przez wdrożenie modułów, które analizują symulowane dane dla szczegółowych informacji o firmie.

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

>[!NOTE]
>IoT Edge dla systemu Linux w systemie Windows jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Przygotuj środowisko dla interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Utwórz grupę zasobów w chmurze, aby zarządzać wszystkimi zasobami, które będą używane w tym przewodniku Szybki Start.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Upewnij się, że urządzenie IoT Edge spełnia następujące wymagania:

* KOMPUTER z systemem Windows lub Server, wersja 1809 lub nowsza
* Co najmniej 4 GB pamięci, zalecane 8 GB pamięci
* 10 GB wolnego miejsca na dysku

>[!NOTE]
>Ten przewodnik Szybki Start używa centrum administracyjnego systemu Windows, aby utworzyć wdrożenie IoT Edge dla systemu Linux w systemie Windows. Można również użyć programu PowerShell. Jeśli chcesz użyć programu PowerShell do utworzenia wdrożenia, wykonaj kroki opisane w przewodniku krok po [instalacji i aprowizacji Azure IoT Edge dla systemu Linux na urządzeniu z systemem Windows](how-to-install-iot-edge-on-windows.md).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Zacznij od utworzenia Centrum IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure.

![Diagram przedstawiający krok tworzenia centrum we/wy.](./media/quickstart/create-iot-hub.png)

W tym przewodniku szybki start działa bezpłatna IoT Hub platformy Azure. Jeśli w przeszłości użyto IoT Hub i masz już utworzone centrum, możesz użyć tego Centrum IoT.

Poniższy kod tworzy bezpłatny koncentrator **F1** w grupie zasobów `IoTEdgeResources` . Zamień na `{hub_name}` unikatową nazwę Centrum IoT. Utworzenie centrum IoT Hub może potrwać kilka minut.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

Jeśli zostanie wyświetlony błąd, ponieważ masz już jedno bezpłatne centrum w ramach subskrypcji, Zmień jednostkę SKU na `S1` . Jeśli zostanie wyświetlony komunikat o błędzie, że nazwa Centrum IoT jest niedostępna, ktoś inny ma już centrum o tej nazwie. Wypróbuj nową nazwę.

## <a name="register-an-iot-edge-device"></a>Rejestrowanie urządzenia usługi IoT Edge

Zarejestruj urządzenie usługi IoT Edge, korzystając z nowo utworzonego centrum IoT.

![Diagram przedstawiający krok rejestracji urządzenia przy użyciu tożsamości Centrum IoT Hub.](./media/quickstart/register-device.png)

Utwórz tożsamość urządzenia symulowanego, aby umożliwić mu komunikowanie się z centrum IoT Hub. Tożsamość urządzenia jest przechowywana w chmurze, a w celu skojarzenia urządzenia fizycznego z tożsamością urządzenia używane są unikatowe parametry połączenia urządzenia.

Urządzenia IoT Edge działają i mogą być zarządzane inaczej niż typowe urządzenia IoT. Użyj `--edge-enabled` flagi, aby zadeklarować, że ta tożsamość dotyczy urządzenia IoT Edge.

1. W Azure Cloud Shell wprowadź następujące polecenie, aby utworzyć urządzenie o nazwie **myEdgeDevice** w centrum.

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     Jeśli wystąpi błąd dotyczący `iothubowner` kluczy zasad, upewnij się, że na Cloud Shell jest uruchomiona Najnowsza wersja rozszerzenia Azure IoT.

1. Wyświetl parametry połączenia dla urządzenia, które łączy urządzenie fizyczne z jego tożsamością w IoT Hub. Zawiera nazwę Centrum IoT, nazwę urządzenia i klucz współużytkowany, który uwierzytelnia połączenia między nimi.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. Skopiuj wartość klucza `connectionString` z danych wyjściowych JSON i zapisz ją. Ta wartość to parametry połączenia urządzenia. Zostanie ona użyta do skonfigurowania środowiska uruchomieniowego IoT Edge w następnej sekcji.

     ![Zrzut ekranu pokazujący dane wyjściowe connectionString w Cloud Shell.](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalowanie i uruchamianie środowiska uruchomieniowego usługi IoT Edge

Zainstaluj IoT Edge dla systemu Linux w systemie Windows na urządzeniu i skonfiguruj go przy użyciu parametrów połączenia urządzenia.

![Diagram przedstawiający krok do uruchomienia środowiska uruchomieniowego IoT Edge.](./media/quickstart/start-runtime.png)

1. [Pobierz centrum administracyjne systemu Windows](https://aka.ms/WACDownloadEFLOW).

1. Postępuj zgodnie z monitami wyświetlanymi w Kreatorze instalacji, aby skonfigurować centrum administracyjne systemu Windows na urządzeniu.

1. Otwórz Centrum administracyjne systemu Windows.

1. Wybierz ikonę **koła zębatego ustawienia** w prawym górnym rogu, a następnie wybierz pozycję **rozszerzenia**.

1. Na karcie **kanały informacyjne** wybierz pozycję **Dodaj**.

1. Wprowadź `https://aka.ms/wac-insiders-feed` tekst w polu tekstowym, a następnie wybierz pozycję **Dodaj**.

1. Po dodaniu kanału informacyjnego przejdź do karty **dostępne rozszerzenia** i poczekaj na zaktualizowanie listy rozszerzeń.

1. Z listy **dostępnych rozszerzeń** wybierz pozycję **Azure IoT Edge**.

1. Zainstaluj rozszerzenie.

1. Po zainstalowaniu rozszerzenia wybierz pozycję **Centrum administracyjne systemu Windows** w lewym górnym rogu, aby przejść do głównej strony pulpitu nawigacyjnego.

     Połączenie **localhost** reprezentuje komputer, na którym jest uruchomione centrum administracyjne systemu Windows.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Zrzut ekranu przedstawiający stronę startową administratora systemu Windows.":::

1. Wybierz pozycję **Dodaj**.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Zrzut ekranu, który pokazuje Wybieranie przycisku Dodaj w centrum administracyjnym systemu Windows.":::

1. Na kafelku Azure IoT Edge wybierz pozycję **Utwórz nowy** , aby uruchomić Kreatora instalacji.

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Zrzut ekranu przedstawiający tworzenie nowego wdrożenia w Azure IoT Edge do odwołania.":::

1. Kontynuuj pracę z kreatorem instalacji, aby zaakceptować postanowienia licencyjne dotyczące oprogramowania firmy Microsoft, a następnie wybierz przycisk **dalej**.

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Zrzut ekranu, który pokazuje Wybieranie przycisku Dalej, aby kontynuować w Kreatorze instalacji.":::

1. Wybierz opcję **opcjonalne dane diagnostyczne**, a następnie wybierz pozycję **Dalej: Wdróż**. Ten wybór zawiera rozszerzone dane diagnostyczne, które ułatwiają firmie Microsoft monitorowanie i konserwowanie jakości usług.

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Zrzut ekranu przedstawiający opcje danych diagnostycznych.":::

1. Na ekranie **Wybieranie urządzenia docelowego** wybierz odpowiednie urządzenie docelowe, aby sprawdzić, czy spełnia ono wymagania minimalne. Na potrzeby tego przewodnika Szybki Start instalujemy IoT Edge na urządzeniu lokalnym, dlatego należy wybrać połączenie **localhost** . Jeśli urządzenie docelowe spełnia wymagania, wybierz pozycję **dalej** , aby kontynuować.

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Zrzut ekranu przedstawiający listę urządzeń docelowych.":::

1. Wybierz pozycję **dalej** , aby zaakceptować ustawienia domyślne. Na ekranie wdrożenia jest wyświetlany proces pobierania pakietu, instalowania pakietu, konfigurowania hosta i ostatecznego skonfigurowania maszyny wirtualnej z systemem Linux. Pomyślne wdrożenie wygląda następująco:

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Zrzut ekranu przedstawiający pomyślne wdrożenie.":::

1. Wybierz pozycję **Dalej: Połącz** , aby przejść do ostatniego kroku, aby zainicjować obsługę administracyjną urządzenia Azure IoT Edge przy użyciu identyfikatora urządzenia z wystąpienia usługi IoT Hub.

1. Wklej parametry połączenia, które zostały [wcześniej skopiowane w tym przewodniku szybki start](#register-an-iot-edge-device) do pola **Parametry połączenia urządzenia** . Następnie wybierz pozycję **Inicjowanie obsługi przy użyciu wybranej metody**.

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Zrzut ekranu przedstawiający parametry połączenia w polu Parametry połączenia urządzenia.":::

1. Po zakończeniu aprowizacji wybierz pozycję **Zakończ** , aby zakończyć, i wróć do ekranu startowego centrum administracyjnego systemu Windows. Urządzenie powinno być widoczne na liście jako urządzenie IoT Edge.

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Zrzut ekranu pokazujący wszystkie połączenia w centrum administracyjnym systemu Windows.":::

1. Wybierz urządzenie Azure IoT Edge, aby wyświetlić jego pulpit nawigacyjny. Należy sprawdzić, czy zostały wdrożone obciążenia z sznurka urządzenia w usłudze Azure IoT Hub. **Lista modułów IoT Edge** powinna zawierać jeden moduł z systemem **EdgeAgent**, a **stan IoT Edge** powinien być **aktywny (uruchomiony)**.

Urządzenie usługi IoT Edge jest teraz skonfigurowane. Jest ono gotowe do uruchamiania modułów wdrożonych w chmurze.

## <a name="deploy-a-module"></a>Wdrażanie modułu

Zarządzając urządzeniem usługi Azure IoT Edge z chmury, wdróż moduł przesyłający dane telemetryczne do centrum IoT Hub.

![Diagram przedstawiający krok do wdrożenia modułu.](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-the-generated-data"></a>Wyświetlanie wygenerowanych danych

W tym przewodniku Szybki start utworzono nowe urządzenie usługi IoT Edge i zainstalowano na nim środowisko uruchomieniowe usługi IoT Edge. Następnie użyto Azure Portal, aby wdrożyć moduł IoT Edge do uruchamiania na urządzeniu bez konieczności wprowadzania zmian na urządzeniu.

Wypchnięci moduł generuje przykładowe dane środowiska, których można użyć do testowania później. Symulowany czujnik monitoruje maszynę i środowisko wokół maszyny. Na przykład ten czujnik może być umieszczony w serwerowni, w hali fabrycznej lub na turbinie wiatrowej. Wysyłane komunikaty obejmują temperaturę otoczenia i wilgotność, temperaturę i ciśnienie maszyny oraz sygnaturę czasową. Samouczki IoT Edge używają danych utworzonych przez ten moduł jako danych testowych do analizy.

Z poziomu powłoki poleceń w centrum administracyjnym systemu Windows upewnij się, że moduł wdrożony z chmury jest uruchomiony na urządzeniu IoT Edge.

1. Nawiąż połączenie z nowo utworzonym urządzeniem IoT Edge.

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Zrzut ekranu pokazujący Wybieranie opcji Połącz w centrum administracyjnym systemu Windows.":::

     Na stronie **Przegląd** zobaczysz **listę IoT Edge modułów** i **stan IoT Edge**. Można zobaczyć wdrożone moduły i stan urządzenia.  

1. W obszarze **Narzędzia** wybierz pozycję **powłoka poleceń**. Powłoka poleceń jest terminalem programu PowerShell, który automatycznie używa Secure Shell (SSH) do nawiązywania połączenia z maszyną wirtualną z systemem Azure IoT Edge na komputerze z systemem Windows.

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Zrzut ekranu pokazujący otwieranie powłoki poleceń.":::

1. Aby sprawdzić trzy moduły na urządzeniu, uruchom następujące polecenie bash:

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Zrzut ekranu pokazujący dane wyjściowe powłoki poleceń I o.":::

1. Wyświetl komunikaty wysyłane z modułu czujnika temperatury do chmury.

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >W IoT Edge poleceniach jest rozróżniana wielkość liter, gdy odwołują się do nazw modułów.

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Zrzut ekranu przedstawiający listę komunikatów wysyłanych z modułu do chmury.":::

Możesz również użyć [rozszerzenia IoT Hub platformy Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) , aby Visual Studio Code do oglądania komunikatów docierających do centrum IoT.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz kontynuować korzystanie z samouczków IoT Edge, Pomiń ten krok. Możesz użyć urządzenia, które zostało zarejestrowane i skonfigurowane w tym przewodniku Szybki Start. W przeciwnym razie możesz usunąć utworzone zasoby platformy Azure, aby uniknąć naliczania opłat.

Jeśli maszyna wirtualna i centrum IoT Hub zostały utworzone w nowej grupie zasobów, możesz usunąć tę grupę i wszystkie powiązane zasoby. Jeśli nie chcesz usuwać całej grupy, możesz usunąć poszczególne zasoby.

> [!IMPORTANT]
> Sprawdź zawartość grupy zasobów, aby upewnić się, że nie ma żadnych danych, które chcesz zachować. Usunięcie grupy zasobów jest nieodwracalne.

Użyj poniższego polecenia, aby usunąć grupę **IoTEdgeResources** . Usuwanie może potrwać kilka minut.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Można potwierdzić, że grupa zasobów została usunięta za pomocą tego polecenia, aby wyświetlić listę grup zasobów.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>Usuwanie Azure IoT Edge dla systemu Linux w systemie Windows

Aby odinstalować Azure IoT Edge dla systemu Linux w systemie Windows, użyj rozszerzenia pulpitu nawigacyjnego w centrum administracyjnym systemu Windows.

1. Połącz się z urządzeniem IoT Edge w centrum administracyjnym systemu Windows. Rozszerzenie narzędzia pulpitu nawigacyjnego platformy Azure ładuje się.

1. Wybierz pozycję **Odinstaluj**. Po usunięciu Azure IoT Edge centrum administracyjne systemu Windows usunie wpis połączenia z urządzeniem Azure IoT Edge z poziomu strony **startowej** .

>[!Note]
>Innym sposobem usunięcia Azure IoT Edge z systemu Windows jest wybranie opcji **Uruchom**  >  **Ustawienia**  >  **aplikacje**  >  **Azure IoT Edge**  >  **odinstalowania** na urządzeniu IoT Edge. Ta metoda usuwa Azure IoT Edge z urządzenia IoT Edge, ale pozostawia połączenie za pośrednictwem Centrum administracyjnego systemu Windows. Aby ukończyć usuwanie, Odinstaluj centrum administracyjne systemu Windows z poziomu menu **Ustawienia** .

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono urządzenie usługi IoT Edge i wdrożono na nim kod przy użyciu interfejsu usługi Azure IoT Edge w chmurze. Teraz masz urządzenie testowe generujące dane pierwotne środowiska.

Następnie skonfiguruj lokalne środowisko programistyczne, aby rozpocząć tworzenie modułów IoT Edge, które uruchamiają logikę biznesową.

> [!div class="nextstepaction"]
> [Rozpocznij opracowywanie modułów IoT Edge](tutorial-develop-for-linux.md)
