---
title: Wizualizacja danych centrum IoT w czasie rzeczywistym w aplikacji internetowej
description: Użyj aplikacji internetowej do wizualizacji danych temperatury i wilgotności, które są zbierane z czujnika i wysyłane do centrum IoT.
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
ms.openlocfilehash: 4f2f0678b421ac6965b2848cc25564b4e95c7c6b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567068"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Wizualizowanie danych czujników w czasie rzeczywistym z centrum Azure IoT Hub w aplikacji internetowej

![Diagram end-to-end](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Z tego artykułu dowiesz się, jak wizualizować dane czujników w czasie rzeczywistym odbierane przez centrum IoT przy użyciu aplikacji internetowej node.js działającej na komputerze lokalnym. Po uruchomieniu aplikacji internetowej lokalnie możesz opcjonalnie wykonać kroki, aby hostować aplikację internetową w Azure App Service. Jeśli chcesz spróbować zwizualizować dane w centrum IoT hub przy użyciu usługi Power BI, zobacz Używanie usługi [Power BI do](iot-hub-live-data-visualization-in-power-bi.md)wizualizacji danych czujników w czasie rzeczywistym z Azure IoT Hub .

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [samouczek dotyczący symulatora online](iot-hub-raspberry-pi-web-simulator-get-started.md) urządzenia Raspberry Pi lub jeden z samouczków urządzenia. Na przykład możesz przejść do urządzenia [Raspberry Pi ](iot-hub-raspberry-pi-kit-node-get-started.md) z node.jslub do jednego z przewodników Szybki start [Wysyłanie](quickstart-send-telemetry-dotnet.md) danych telemetrycznych. Te artykuły obejmują następujące wymagania:

  * Aktywna subskrypcja platformy Azure
  * Centrum IoT w ramach subskrypcji
  * Aplikacja klienca, która wysyła komunikaty do centrum IoT

* [Pobierz oprogramowanie Git](https://www.git-scm.com/downloads)

* W krokach w tym artykule założono, że jest to maszyna dewelopera z systemem Windows. Można jednak łatwo wykonać te kroki w systemie Linux w preferowanej powłoki.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Dodawanie grupy odbiorców do centrum IoT

[Grupy odbiorców](../event-hubs/event-hubs-features.md#event-consumers) zapewniają niezależne widoki strumienia zdarzeń, które umożliwiają aplikacjom i usługom platformy Azure niezależne korzystanie z danych z tego samego punktu końcowego centrum zdarzeń. W tej sekcji dodasz grupę odbiorców do wbudowanego punktu końcowego centrum IoT, z których aplikacja internetowa będzie korzystać do odczytywania danych.

Uruchom następujące polecenie, aby dodać grupę odbiorców do wbudowanego punktu końcowego centrum IoT:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Zanotuj nazwę, która będzie potrzebna w dalszej części tego samouczka.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Uzyskiwanie parametrów połączenia usługi dla centrum IoT

Centra IoT Hub są tworzone przy użyciu kilku domyślnych zasad dostępu. Jedną z takich zasad są **zasady** usługi, które zapewniają wystarczające uprawnienia dla usługi do odczytu i zapisu punktów końcowych centrum IoT. Uruchom następujące polecenie, aby uzyskać ciąg połączenia dla centrum IoT, które jest zgodne z zasadami usługi:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

Ciąg połączenia powinien wyglądać podobnie do następujących:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Zanotuj wartości parametrów połączenia usługi. Będą potrzebne w dalszej części tego samouczka.

## <a name="download-the-web-app-from-github"></a>Pobieranie aplikacji internetowej z usługi GitHub

Otwórz okno polecenia i wprowadź następujące polecenia, aby pobrać przykład z usługi GitHub i zmienić katalog na przykładowy:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Badanie kodu aplikacji internetowej

W katalogu web-apps-node-iot-hub-data-visualization otwórz aplikację internetową w ulubionym edytorze. Poniżej przedstawiono strukturę plików przeglądaną w VS Code:

![Struktura pliku aplikacji internetowej](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Pomiń chwilę, aby zbadać następujące pliki:

* **Server.js** to skrypt po stronie usługi, który inicjuje klasę web socket i otokę centrum zdarzeń. Zapewnia ona wywołanie zwrotne do klasy otoki centrum zdarzeń, która jest używana przez klasę do rozgłaszania komunikatów przychodzących do gniazda internetowego.

* **Event-hub-reader.js** to skrypt po stronie usługi, który łączy się z wbudowanym punktem końcowym centrum IoT przy użyciu określonych parametrów połączenia i grupy odbiorców. Wyodrębnia ona wartości DeviceId i EnqueuedTimeUtc z metadanych przychodzących komunikatów, a następnie przekazuje komunikat przy użyciu metody wywołania zwrotnego zarejestrowanej przez server.js.

* **Chart-device-data.js** to skrypt po stronie klienta, który nasłuchuje na gnieździe internetowym, śledzi poszczególne dane DeviceId i przechowuje ostatnie 50 punktów danych przychodzących dla każdego urządzenia. Następnie wiąże wybrane dane urządzenia z obiektem wykresu.

* **Index.html** obsługuje układ interfejsu użytkownika dla strony internetowej i odwołuje się do skryptów niezbędnych dla logiki po stronie klienta.

## <a name="configure-environment-variables-for-the-web-app"></a>Konfigurowanie zmiennych środowiskowych dla aplikacji internetowej

Aby odczytać dane z centrum IoT Hub, aplikacja internetowa potrzebuje parametrów połączenia centrum IoT oraz nazwy grupy odbiorców, która ma zostać odczytana. Pobiera te ciągi ze środowiska przetwarzania w następujących wierszach w server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Ustaw zmienne środowiskowe w oknie polecenia za pomocą następujących poleceń. Zastąp wartości symboli zastępczych ciągami połączenia usługi dla centrum IoT Hub i nazwą utworzonej wcześniej grupy odbiorców. Nie przytłaczaj ciągów.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Uruchom aplikację internetową

1. Upewnij się, że urządzenie jest uruchomione i wysyła dane.

2. W oknie polecenia uruchom następujące wiersze, aby pobrać i zainstalować pakiety, do których się odwoływuje, oraz uruchomić witrynę internetową:

   ```cmd
   npm install
   npm start
   ```

3. W konsoli powinny zostać wyświetlony dane wyjściowe, które wskazują, że aplikacja internetowa pomyślnie nałączono połączenie z centrum IoT i nasłuchuje na porcie 3000:

   ![Aplikacja internetowa uruchomiona w konsoli](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Otwieranie strony internetowej w celu zobaczenia danych z centrum IoT

Otwórz w przeglądarce okno `http://localhost:3000` .

Na liście **Wybierz** urządzenie wybierz urządzenie, aby wyświetlić uruchomiony wykres ostatnich 50 punktów danych temperatury i wilgotności wysłanych przez urządzenie do centrum IoT.

![Strona aplikacji internetowej przedstawiająca temperaturę i wilgotność w czasie rzeczywistym](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

W konsoli powinny być również wyświetlane dane wyjściowe, które pokazują komunikaty, które aplikacja internetowa emituje do klienta przeglądarki:  

![Dane wyjściowe emisji aplikacji internetowej w konsoli](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Hostuj aplikację internetową w App Service

Ta [Web Apps usługi Azure App Service](../app-service/overview.md) udostępnia platformę jako usługę (PAAS) do hostowania aplikacji internetowych. Aplikacje internetowe hostowane w usłudze Azure App Service mogą korzystać z zaawansowanych funkcji platformy Azure, takich jak dodatkowe zabezpieczenia, równoważenie obciążenia i skalowalność, a także rozwiązania DevOps platformy Azure i partnerów, takie jak ciągłe wdrażanie, zarządzanie pakietami itp. Azure App Service obsługuje aplikacje internetowe opracowane w wielu popularnych językach i wdrożone w infrastrukturze systemu Windows lub Linux.

W tej sekcji aprowizujesz aplikację internetową w usłudze App Service i wdrażasz w nim kod przy użyciu poleceń interfejsu wiersza polecenia platformy Azure. Szczegółowe informacje o poleceniach używanych w dokumentacji [polecenia az webapp.](/cli/azure/webapp) Przed rozpoczęciem upewnij się, że zostały wykonane kroki dodawania grupy zasobów do centrum [IoT,](#add-a-consumer-group-to-your-iot-hub)pobierania parametrów połączenia usługi dla centrum [IoT i](#get-a-service-connection-string-for-your-iot-hub)pobierania aplikacji internetowej z usługi [GitHub.](#download-the-web-app-from-github)

1. Plan [App Service definiuje](../app-service/overview-hosting-plans.md) zestaw zasobów obliczeniowych dla aplikacji hostowanej w App Service do uruchomienia. W tym samouczku używamy warstwy Deweloper/Bezpłatna do hostacji aplikacji internetowej. W warstwie Bezpłatna aplikacja internetowa działa w udostępnionych zasobach systemu Windows z innymi App Service, w tym aplikacjami innych klientów. Platforma Azure oferuje również App Service wdrażania aplikacji internetowych w zasobach obliczeniowych systemu Linux. Możesz pominąć ten krok, jeśli masz już App Service planu, którego chcesz użyć.

   Aby utworzyć plan App Service w warstwie Bezpłatna systemu Windows, uruchom następujące polecenie. Użyj tej samej grupy zasobów, w których znajduje się centrum IoT. Nazwa planu usługi może zawierać wielkie i małe litery, cyfry i łączniki.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Teraz aprowizuj aplikację internetową w planie App Service aplikacji. Parametr umożliwia przekazywany i wdrażany kod aplikacji internetowej z repozytorium `--deployment-local-git` Git na komputerze lokalnym. Nazwa aplikacji internetowej musi być globalnie unikatowa i może zawierać wielkie i małe litery, cyfry i łączniki. Upewnij się, że parametr Node jest w wersji 10.6 lub nowszej, w zależności od wersji `--runtime` Node.js uruchomieniowego. Aby uzyskać listę obsługiwanych środowisk uruchomieniowych, możesz `az webapp list-runtimes` użyć polecenia .

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Teraz dodaj ustawienia aplikacji dla zmiennych środowiskowych, które określają ciąg połączenia centrum IoT i grupę odbiorców centrum zdarzeń. Poszczególne ustawienia są rozdzielone spacjami w `-settings` parametrze . Użyj parametrów połączenia usługi dla centrum IoT i grupy odbiorców utworzonej wcześniej w tym samouczku. Nie należy pocytować wartości.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. Włącz protokół Web Sockets dla aplikacji internetowej i ustaw aplikację internetową tak, aby odbierała tylko żądania HTTPS (żądania HTTP są przekierowywany do protokołu HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Aby wdrożyć kod w App Service, użyjesz poświadczeń wdrożenia [na poziomie użytkownika.](../app-service/deploy-configure-credentials.md) Poświadczenia wdrożenia na poziomie użytkownika różnią się od poświadczeń platformy Azure i są używane w przypadku lokalnych wdrożeń usługi Git i protokołu FTP w aplikacji internetowej. Po ich skonfigurowaniu są one prawidłowe we wszystkich aplikacjach App Service wszystkich subskrypcjach na koncie platformy Azure. Jeśli wcześniej ustawiono poświadczenia wdrożenia na poziomie użytkownika, możesz ich użyć.

   Jeśli nie ustawiono wcześniej poświadczeń wdrożenia na poziomie użytkownika lub nie możesz zapamiętać hasła, uruchom następujące polecenie. Nazwa użytkownika wdrożenia musi być unikatowa w obrębie platformy Azure i nie może zawierać symbolu " \@ " dla lokalnych wypchnięć git. Po wyświetleniu monitu wprowadź i potwierdź nowe hasło. Hasło musi mieć co najmniej osiem znaków i zawierać dwa z następujących trzech elementów: litery, cyfry i symbole.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Pobierz adres URL usługi Git do użycia w celu wypchania kodu do App Service.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Dodaj do klonu repozytorium zdalnego, które odwołuje się do repozytorium Git aplikacji internetowej w App Service. W \<Git clone URL\> przypadku pliku użyj adresu URL zwróconego w poprzednim kroku. Uruchom następujące polecenie w oknie polecenia.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Aby wdrożyć kod w App Service, wprowadź następujące polecenie w oknie polecenia. Jeśli zostanie wyświetlony monit o poświadczenia, wprowadź poświadczenia wdrożenia na poziomie użytkownika utworzone w kroku 5. Upewnij się, że wypychasz do głównej gałęzi App Service zdalnego.

    ```cmd
    git push webapp main:main
    ```

9. Postęp wdrażania zostanie zaktualizowany w oknie polecenia. Pomyślne wdrożenie zakończy się przy użyciu wierszy podobnych do następujących danych wyjściowych:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  main -> main
    ```

10. Uruchom następujące polecenie, aby wykonać zapytanie o stan aplikacji internetowej i upewnić się, że jest uruchomiona:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. W przeglądarce przejdź do adresu `https://<your web app name>.azurewebsites.net`. Zostanie wyświetlona strona internetowa podobna do tej, która została wyświetlona podczas lokalnego korzystania z aplikacji internetowej. Przy założeniu, że urządzenie jest uruchomione i wysyła dane, powinien zostać wyświetlony uruchomiony wykres 50 najnowszych odczytów temperatury i wilgotności wysłanych przez urządzenie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz problemy z tym przykładem, spróbuj wykonać kroki opisane w poniższych sekcjach. Jeśli nadal masz problemy, prześlij nam swoją opinię w dolnej części tego tematu.

### <a name="client-issues"></a>Problemy z klientem

* Jeśli urządzenie nie jest wyświetlane na liście lub żaden graf nie jest rysowany, upewnij się, że kod urządzenia jest uruchomiony na urządzeniu.

* W przeglądarce otwórz narzędzia deweloperskie (w wielu przeglądarkach zostanie otwarty klucz F12) i znajdź konsolę. Poszukaj tam ostrzeżeń lub błędów.

* Skrypt po stronie klienta można debugować w /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problemy z lokalną witryną internetową

* Obserwuj dane wyjściowe w oknie, w którym uruchomiono węzeł, aby uzyskać dane wyjściowe konsoli.

* Debugowanie kodu serwera, w szczególności server.js i /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Azure App Service problemów

* W Azure Portal przejdź do swojej aplikacji internetowej. W **obszarze Monitorowanie** w okienku po lewej stronie wybierz pozycję App Service **dzienniki.** Włącz **Application Logging (System plików),** ustaw **poziom** na Błąd, a następnie wybierz pozycję **Zapisz.** Następnie otwórz **strumień dzienników** (w **obszarze Monitorowanie).**

* W aplikacji internetowej w programie Azure Portal narzędzia **programowe**  wybierz pozycję  **Konsola** i zweryfikuj wersje węzła i narzędzia npm za pomocą `node -v` i `npm -v` .

* Jeśli zostanie wyświetlony błąd z komunikatem o nieuzysłaniu pakietu, być może kroki zostały uruchomione poza kolejnością. Po wdrożeniu witryny (z usługą ) usługa App Service uruchamia program , który jest uruchamiany w oparciu o bieżącą wersję `git push` `npm install` skonfigurowanego węzła. Jeśli później zostanie to zmienione w konfiguracji, musisz wprowadzić bezsensowną zmianę w kodzie i wypchnąć go ponownie.

## <a name="next-steps"></a>Następne kroki

Pomyślnie używasz aplikacji internetowej do wizualizacji danych czujników w czasie rzeczywistym z centrum IoT.

Inny sposób wizualizacji danych z usługi Azure IoT Hub można znaleźć w te Power BI use [Power BI to visualize real-time sensor data from your IoT hub](iot-hub-live-data-visualization-in-power-bi.md)(Wizualizowanie danych czujników w czasie rzeczywistym z centrum IoT).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
