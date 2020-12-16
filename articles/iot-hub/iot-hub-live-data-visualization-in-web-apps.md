---
title: Wizualizacja danych w czasie rzeczywistym danych z Centrum IoT w aplikacji sieci Web
description: Użyj aplikacji sieci Web, aby wizualizować dane temperatury i wilgotności zbierane z czujnika i wysyłane do centrum IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 7753c6c118d763163e6bc8f69f5b4eee13fe2393
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588798"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Wizualizowanie danych z czujników w czasie rzeczywistym z poziomu usługi Azure IoT Hub w aplikacji sieci Web

![Diagram kompleksowy](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym samouczku dowiesz się, jak wizualizować dane z czujników w czasie rzeczywistym, które są odbierane przez Centrum IoT, za pomocą aplikacji internetowej node.js działającej na komputerze lokalnym. Po lokalnym uruchomieniu aplikacji sieci Web możesz opcjonalnie wykonać kroki, aby hostować aplikację sieci Web w Azure App Service. Jeśli chcesz próbować wizualizować dane w centrum IoT przy użyciu Power BI, zobacz [używanie Power BI do wizualizacji danych z czujników w czasie rzeczywistym z usługi Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Co robisz

* Dodawanie grupy odbiorców do centrum IoT Hub, którego aplikacja sieci Web będzie używać do odczytywania danych z czujnika
* Pobierz kod aplikacji internetowej z usługi GitHub
* Sprawdzanie kodu aplikacji sieci Web
* Konfigurowanie zmiennych środowiskowych do przechowywania artefaktów IoT Hub wymaganych przez aplikację sieci Web
* Uruchom aplikację sieci Web na swoim komputerze deweloperskim
* Otwórz stronę sieci Web, aby wyświetlić dane temperatury i wilgotności w czasie rzeczywistym z Centrum IoT
* Obowiązkowe Użyj interfejsu wiersza polecenia platformy Azure, aby hostować aplikację sieci Web w Azure App Service

## <a name="what-you-need"></a>Potrzebne elementy

* Ukończ samouczek [gry online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) lub jedno z samouczków dotyczących urządzeń; na przykład [Raspberry Pi z node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Obejmują one następujące wymagania:

  * Aktywna subskrypcja platformy Azure
  * Centrum IoT w ramach Twojej subskrypcji
  * Aplikacja kliencka, która wysyła komunikaty do centrum IoT Hub

* [Pobierz usługę git](https://www.git-scm.com/downloads)

* W krokach przedstawionych w tym artykule przyjęto założenie, że komputer deweloperski systemu Windows; można jednak łatwo wykonać te kroki w systemie Linux w preferowanej powłoce.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Dodawanie grupy odbiorców do centrum IoT Hub

[Grupy konsumentów](../event-hubs/event-hubs-features.md#event-consumers) zapewniają niezależne widoki w strumieniu zdarzeń, które umożliwiają aplikacjom i usługom platformy Azure niezależne korzystanie z danych z tego samego punktu końcowego centrum zdarzeń. W tej sekcji dodasz grupę odbiorców do wbudowanego punktu końcowego usługi IoT Hub, który będzie używany przez aplikację sieci Web do odczytywania danych z programu.

Uruchom następujące polecenie, aby dodać grupę konsumentów do wbudowanego punktu końcowego Centrum IoT Hub:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Zanotuj wybraną nazwę, która będzie potrzebna w dalszej części tego samouczka.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Pobieranie parametrów połączenia usługi dla Centrum IoT Hub

Centra IoT są tworzone z użyciem kilku domyślnych zasad dostępu. Jedną z tych zasad jest zasada **usługi** , która zapewnia wystarczające uprawnienia do odczytania i zapisania punktów końcowych usługi IoT Hub przez usługę. Uruchom następujące polecenie, aby uzyskać parametry połączenia dla Centrum IoT Hub zgodne z zasadami usługi:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

Parametry połączenia powinny wyglądać podobnie do następujących:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Zanotuj parametry połączenia usługi, które będą potrzebne w dalszej części tego samouczka.

## <a name="download-the-web-app-from-github"></a>Pobierz aplikację sieci Web z usługi GitHub

Otwórz okno wiersza polecenia, a następnie wprowadź następujące polecenia, aby pobrać przykład z witryny GitHub i przejść do katalogu przykładowego:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Sprawdzanie kodu aplikacji sieci Web

W katalogu Web-Apps-Node-IoT-Hub-Data-wizualizacji Otwórz aplikację sieci Web w ulubionym edytorze. Poniżej przedstawiono strukturę pliku wyświetlaną w VS Code:

![Struktura pliku aplikacji sieci Web](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Poświęć chwilę na przeanalizowanie następujących plików:

* **Server.js** to skrypt po stronie usługi, który inicjuje gniazdo sieci Web i klasę otoki centrum zdarzeń. Zapewnia wywołanie zwrotne klasy otoki centrum zdarzeń używanej przez klasę do emisji komunikatów przychodzących do gniazda sieci Web.

* **Event-hub-reader.js** to skrypt po stronie usługi, który nawiązuje połączenie z wbudowanym punktem końcowym Centrum IoT Hub przy użyciu określonych parametrów połączenia i grupy odbiorców. Wyodrębnia identyfikator DeviceId i EnqueuedTimeUtc z metadanych w wiadomościach przychodzących, a następnie przekazuje komunikat przy użyciu metody wywołania zwrotnego zarejestrowanej przez server.js.

* **Chart-device-data.js** to skrypt po stronie klienta, który nasłuchuje w gnieździe sieci Web, śledzi każdy identyfikator urządzenia i zapisuje ostatnie 50 punktów danych przychodzących dla każdego z urządzeń. Następnie wiąże wybrane dane urządzenia z obiektem wykresu.

* **Index.html** obsługuje układ interfejsu użytkownika strony sieci Web i odwołuje się do niezbędnych skryptów dla logiki po stronie klienta.

## <a name="configure-environment-variables-for-the-web-app"></a>Konfigurowanie zmiennych środowiskowych dla aplikacji sieci Web

Aby można było odczytywać dane z usługi IoT Hub, aplikacja sieci Web wymaga parametrów połączenia z Centrum IoT Hub oraz nazwy grupy odbiorców, którą powinien odczytać. Pobiera te ciągi ze środowiska przetwarzania w następujących wierszach w server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Ustaw zmienne środowiskowe w oknie poleceń przy użyciu następujących poleceń. Zastąp wartości symbolu zastępczego parametrami połączenia usługi dla Centrum IoT i nazwą utworzonej wcześniej grupy odbiorców. Nie należy wycudzysłowować ciągów.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Uruchom aplikację internetową

1. Upewnij się, że urządzenie jest uruchomione i wysyła dane.

2. W oknie polecenia Uruchom następujące wiersze, aby pobrać i zainstalować pakiety, do których istnieją odwołania, a następnie uruchom witrynę sieci Web:

   ```cmd
   npm install
   npm start
   ```

3. Powinny zostać wyświetlone dane wyjściowe w konsoli, które wskazują, że aplikacja sieci Web została pomyślnie połączona z Centrum IoT Hub i nasłuchuje na porcie 3000:

   ![Aplikacja internetowa uruchomiona w konsoli](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Otwieranie strony sieci Web w celu wyświetlenia danych z Centrum IoT Hub

Otwórz przeglądarkę do programu `http://localhost:3000` .

Z listy **Wybierz urządzenie** wybierz urządzenie, aby wyświetlić wykres z ostatnich 50 punktów danych dotyczących temperatury i wilgotności wysyłanych przez urządzenie do centrum IoT.

![Strona aplikacji sieci Web przedstawiająca temperaturę i wilgotność w czasie rzeczywistym](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

W konsoli programu znajdują się również dane wyjściowe, które pokazują komunikaty wysyłane przez aplikację sieci Web do klienta przeglądarki:  

![Dane wyjściowe emisji aplikacji sieci Web w konsoli](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Hostowanie aplikacji sieci Web w App Service

[Web Apps funkcja Azure App Service](../app-service/overview.md) udostępnia platformę jako usługę (PaaS) do hostowania aplikacji sieci Web. Aplikacje sieci Web hostowane w Azure App Service mogą korzystać z zaawansowanych funkcji platformy Azure, takich jak dodatkowe zabezpieczenia, równoważenie obciążenia i skalowalność, a także rozwiązań DevOps platformy Azure i partnerów, takich jak ciągłe wdrażanie, zarządzanie pakietami i tak dalej. Azure App Service obsługuje aplikacje sieci Web opracowane w wielu popularnych językach i wdrożone w infrastrukturze systemu Windows lub Linux.

W tej sekcji można zainicjować obsługę aplikacji sieci Web w App Service i wdrożyć kod przy użyciu poleceń interfejsu wiersza polecenia platformy Azure. Można znaleźć szczegółowe informacje o poleceniach użytych w dokumentacji [AZ webapp](/cli/azure/webapp?view=azure-cli-latest) . Przed rozpoczęciem upewnij się, że wykonano kroki umożliwiające [dodanie grupy zasobów do usługi IoT Hub](#add-a-consumer-group-to-your-iot-hub), [pobranie parametrów połączenia z usługą dla Centrum IoT](#get-a-service-connection-string-for-your-iot-hub)oraz [pobranie aplikacji internetowej z witryny GitHub](#download-the-web-app-from-github).

1. [Plan App Service](../app-service/overview-hosting-plans.md) definiuje zestaw zasobów obliczeniowych dla aplikacji hostowanej w App Service do uruchomienia. W tym samouczku użyjemy warstwy Developer/bezpłatna do hostowania aplikacji sieci Web. W przypadku warstwy Bezpłatna aplikacja sieci Web jest uruchamiana na udostępnionych zasobach systemu Windows przy użyciu innych aplikacji App Service, w tym aplikacji innych klientów. Platforma Azure oferuje również App Service plany wdrażania aplikacji sieci Web w ramach zasobów obliczeniowych systemu Linux. Możesz pominąć ten krok, jeśli masz już plan App Service, którego chcesz użyć.

   Aby utworzyć plan App Service przy użyciu warstwy Bezpłatna systemu Windows, uruchom następujące polecenie. Użyj tej samej grupy zasobów, w której znajduje się centrum IoT Hub. Nazwa planu usługi może zawierać wielkie i małe litery, cyfry i łączniki.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Teraz Zainicjuj obsługę aplikacji sieci Web w planie App Service. `--deployment-local-git`Parametr umożliwia przekazywanie i wdrażanie kodu aplikacji sieci Web z repozytorium Git na komputerze lokalnym. Nazwa aplikacji sieci Web musi być globalnie unikatowa i może zawierać wielkie i małe litery, cyfry i łączniki. Upewnij się, że dla parametru określono wersję Node 10,6 lub nowszą `--runtime` , w zależności od używanej wersji środowiska uruchomieniowego Node.js. Możesz użyć polecenia, `az webapp list-runtimes` Aby uzyskać listę obsługiwanych środowisk uruchomieniowych.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Teraz Dodaj ustawienia aplikacji dla zmiennych środowiskowych, które określają parametry połączenia usługi IoT Hub i grupę odbiorców centrum zdarzeń. Poszczególne ustawienia są oddzielane spacjami w `-settings` parametrze. Użyj parametrów połączenia usługi dla Centrum IoT Hub i grupy konsumentów utworzonej wcześniej w tym samouczku. Nie należy wycudzysłowować wartości.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. Włącz protokół Web Sockets dla aplikacji sieci Web i Ustaw aplikację sieci Web do odbierania tylko żądań HTTPS (żądania HTTP są przekierowywane do protokołu HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Aby wdrożyć kod do App Service, będziesz używać [poświadczeń wdrożenia na poziomie użytkownika](../app-service/deploy-configure-credentials.md). Poświadczenia wdrażania na poziomie użytkownika różnią się od poświadczeń platformy Azure i są używane na potrzeby wdrożeń lokalnych i FTP usługi Git w aplikacji sieci Web. Po ustawieniu są one prawidłowe dla wszystkich aplikacji App Service we wszystkich subskrypcjach na koncie platformy Azure. Jeśli wcześniej ustawiono poświadczenia wdrażania na poziomie użytkownika, możesz je użyć.

   Jeśli nie ustawiono wcześniej poświadczeń wdrażania na poziomie użytkownika lub nie pamiętasz hasła, uruchom następujące polecenie. Nazwa użytkownika wdrożenia musi być unikatowa w ramach platformy Azure i nie może zawierać symbolu " \@ " dla lokalnych powiadomień wypychanych git. Po wyświetleniu monitu wprowadź i Potwierdź nowe hasło. Hasło musi składać się z co najmniej ośmiu znaków, a dwa z następujących trzech elementów: litery, cyfry i symbole.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Pobierz adres URL usługi git do użycia w celu wypchnięcia kodu do App Service.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Dodaj element zdalny do klonu, który odwołuje się do repozytorium git dla aplikacji sieci Web w App Service. W przypadku programu \<Git clone URL\> należy użyć adresu URL zwróconego w poprzednim kroku. Uruchom następujące polecenie w oknie wiersza polecenia.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Aby wdrożyć kod w App Service, wprowadź następujące polecenie w oknie wiersza polecenia. Jeśli zostanie wyświetlony monit o podanie poświadczeń, wprowadź poświadczenia wdrożenia na poziomie użytkownika, które zostały utworzone w kroku 5. Upewnij się, że wypychasz do głównej gałęzi App Service zdalnej.

    ```cmd
    git push webapp main:main
    ```

9. Postęp wdrożenia zostanie zaktualizowany w oknie poleceń. Pomyślne wdrożenie zakończy się wierszami podobnymi do następujących danych wyjściowych:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  main -> main
    ```

10. Uruchom następujące polecenie, aby zbadać stan aplikacji sieci Web i upewnić się, że jest uruchomiona:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. W przeglądarce przejdź do adresu `https://<your web app name>.azurewebsites.net`. Strona sieci Web podobna do tej, która została wyświetlona po uruchomieniu lokalnie aplikacji sieci Web. Przy założeniu, że urządzenie jest uruchomione i wysyła dane, powinien zostać wyświetlony wykres z 50 ostatnich odczytów temperatury i wilgotności wysyłanych przez urządzenie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli występują problemy z tym przykładem, spróbuj wykonać kroki opisane w poniższych sekcjach. Jeśli nadal masz problemy, prześlij nam opinię na końcu tego tematu.

### <a name="client-issues"></a>Problemy z klientami

* Jeśli urządzenie nie jest wyświetlane na liście lub wykres nie jest rysowany, upewnij się, że kod urządzenia jest uruchomiony na urządzeniu.

* W przeglądarce Otwórz narzędzia deweloperskie (w wielu przeglądarkach zostanie otwarty klawisz F12) i Znajdź konsolę programu. Wyszukaj wszelkie ostrzeżenia lub błędy w tym miejscu.

* Skrypt po stronie klienta można debugować w/js/chat-device-data.js.

### <a name="local-website-issues"></a>Problemy z lokalną witryną sieci Web

* Obejrzyj dane wyjściowe w oknie, w którym uruchomiono węzeł dla danych wyjściowych konsoli.

* Debuguj kod serwera, w tym server.js i event-hub-reader.js/scripts/.

### <a name="azure-app-service-issues"></a>Problemy Azure App Service

* W Azure Portal przejdź do aplikacji sieci Web. W obszarze **monitorowanie** w okienku po lewej stronie wybierz pozycję **dzienniki App Service**. Włącz **Rejestrowanie aplikacji (system plików)** w systemie, ustaw **poziom** na błąd, a następnie wybierz pozycję **Zapisz**. Następnie otwórz **strumień dziennika** (w obszarze **monitorowanie**).

* Z poziomu aplikacji sieci Web w Azure Portal w obszarze **Narzędzia deweloperskie**  wybierz pozycję  **konsola** i sprawdź wersje węzła i npm z `node -v` i `npm -v` .

* Jeśli zobaczysz błąd dotyczący nieznalezienia pakietu, być może czynności zostały wykonane poza kolejnością. Gdy lokacja zostanie wdrożona (w programie `git push` ), zostanie uruchomiona usługa App Service `npm install` , która jest uruchamiana na podstawie bieżącej wersji skonfigurowanego węzła. Jeśli zostanie zmieniony w konfiguracji późniejszej, należy wprowadzić bezużyteczne zmiany w kodzie i ponownie wypchnąć.

## <a name="next-steps"></a>Następne kroki

Aplikacja sieci Web została pomyślnie użyta do wizualizacji danych czujników w czasie rzeczywistym z Centrum IoT.

Aby uzyskać inny sposób wizualizacji danych z usługi Azure IoT Hub, zobacz [używanie Power BI do wizualizacji danych czujników w czasie rzeczywistym z Centrum IoT](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
