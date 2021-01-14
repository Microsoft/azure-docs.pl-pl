---
title: Wdróż aplikację sieci Web z analizą przestrzenną
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać analizy przestrzennej w aplikacji sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: de011fb0f827ea90efe33e237bbf1c5100dc76a7
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183476"
---
# <a name="how-to-deploy-a-people-counting-web-application"></a>Instrukcje: wdrażanie aplikacji sieci Web zliczanie osób

Skorzystaj z tego artykułu, aby dowiedzieć się, jak zintegrować analizę przestrzenną z aplikacją sieci Web, która zrozumie przepływ osób i monitoruje liczbę osób zajmujących miejsce fizyczne. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

* Wdrażanie kontenera analizy przestrzennej
* Konfigurowanie operacji i aparatu
* Skonfiguruj połączenie IoT Hub w aplikacji sieci Web
* Wdrażanie i testowanie aplikacji sieci Web

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Podstawowe informacje na temat konfiguracji wdrażania Azure IoT Edge i [platformy Azure IoT Hub](../../iot-hub/index.yml)
* Skonfigurowany [komputer-host](spatial-analysis-container.md).

## <a name="deploy-the-spatial-analysis-container"></a>Wdrażanie kontenera analizy przestrzennej

Wypełnij [aplikację żądania](https://aka.ms/csgate) , aby uzyskać dostęp do uruchamiania kontenera. 

Postępuj zgodnie [z konfiguracją komputera hosta](./spatial-analysis-container.md) , aby skonfigurować komputer hosta i podłączyć urządzenie IoT Edge do usługi Azure IoT Hub. 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>Wdrażanie usługi Azure IoT Hub w ramach subskrypcji

Najpierw Utwórz wystąpienie usługi Azure IoT Hub przy użyciu warstwy cenowej standardowa (S1) lub warstwy Bezpłatna (S0). Postępuj zgodnie z tymi instrukcjami, aby utworzyć to wystąpienie przy użyciu interfejsu wiersza polecenia platformy Azure.

Wypełnij wymagane parametry:
* Subskrypcja: nazwa lub Identyfikator subskrypcji platformy Azure
* Grupa zasobów: Utwórz nazwę grupy zasobów
* Nazwa Centrum IoT: Utwórz nazwę IoT Hub
* Nazwa IoTHub: Nazwa utworzonego IoT Hub 
* Nazwa urządzenia brzegowego: Utwórz nazwę urządzenia brzegowego

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>Wdróż kontener na Azure IoT Edge na komputerze hosta

Wdróż kontener analizy przestrzennej jako moduł IoT na komputerze hosta przy użyciu interfejsu wiersza polecenia platformy Azure. Proces wdrażania wymaga pliku manifestu wdrożenia, który zawiera opis wymaganych kontenerów, zmiennych i konfiguracji dla danego wdrożenia. Można znaleźć przykładowy [Azure Stack krawędź określonego manifestu wdrożenia](https://go.microsoft.com/fwlink/?linkid=2142179), [nieAzure Stackego, manifestu wdrożenia](https://go.microsoft.com/fwlink/?linkid=2152189), a także [maszynę wirtualną platformy Azure z określonym przez procesor GPU manifestem wdrożenia](https://go.microsoft.com/fwlink/?linkid=2152189) w witrynie GitHub, która obejmuje podstawową konfigurację wdrożenia dla kontenera *analizy przestrzennej* . 

Możesz również użyć rozszerzeń usługi Azure IoT, aby Visual Studio Code do wykonywania operacji w usłudze IoT Hub. Przejdź do pozycji [wdróż Azure IoT Edge modułów z Visual Studio Code](../../iot-edge/how-to-deploy-modules-vscode.md) , aby dowiedzieć się więcej.

> [!NOTE] 
> Kontenery diagnostyki *telegraf* i *analizy przestrzennej* są opcjonalne. Możesz zdecydować się na ich usunięcie z *DeploymentManifest.js* pliku. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [telemetrii i rozwiązywania problemów](./spatial-analysis-logging.md) . Możesz znaleźć trzy przykładowe *DeploymentManifest.jsw* plikach w serwisie GitHub, [na Azure Stack urządzenia brzegowe](https://go.microsoft.com/fwlink/?linkid=2142179), [MASZYNę stacjonarną](https://go.microsoft.com/fwlink/?linkid=2152189)lub [maszynę wirtualną platformy Azure z procesorem GPU](https://go.microsoft.com/fwlink/?linkid=2152189)

### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Większość **zmiennych środowiskowych** modułu IoT Edge jest już ustawiona w przykładowym *DeploymentManifest.jsw* plikach podanych powyżej. W pliku Wyszukaj `BILLING_ENDPOINT` `API_KEY` zmienne środowiskowe i poniżej. Zastąp wartości identyfikatorem URI punktu końcowego i utworzonym wcześniej kluczem interfejsu API. Upewnij się, że wartość EULA jest ustawiona na "Akceptuj". 

```json
"EULA": { 
    "value": "accept"
},

"BILLING_ENDPOINT":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"API_KEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>Skonfiguruj parametry operacji

Teraz, gdy początkowa konfiguracja kontenera do *analizy przestrzennej* została ukończona, następnym krokiem jest skonfigurowanie parametrów operacji i dodanie ich do wdrożenia. 

Pierwszym krokiem jest aktualizacja przykładowego manifestu wdrażania połączonego powyżej i skonfigurowanie operationId dla programu `cognitiveservices.vision.spatialanalysis-personcount` , jak pokazano poniżej:


```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

Po zaktualizowaniu manifestu wdrożenia postępuj zgodnie z instrukcjami producenta aparatu fotograficznego, aby zainstalować aparat, skonfigurować adres URL aparatu i skonfigurować nazwę użytkownika i hasło. 

Następnie ustaw wartość `VIDEO_URL` na adres URL RTSP aparatu, a następnie poświadczenia dotyczące łączenia się z kamerą.

Jeśli urządzenie brzegowe ma więcej niż jeden procesor GPU, wybierz procesor GPU, na którym ma zostać uruchomiona ta operacja. Należy się upewnić, że równoważenie obciążenia obejmuje operacje, w których nie ma więcej niż 8 operacji uruchomionych w jednym procesorze GPU jednocześnie.  

Następnie skonfiguruj strefę, w której chcesz zliczyć osoby. Aby skonfigurować Wielokąt strefy, najpierw postępuj zgodnie z instrukcjami producenta, aby pobrać ramkę z aparatu. Aby określić każdy wierzchołek wielokąta, wybierz punkt w ramce, podejmij współrzędne x, y w punkcie względem lewej, górnego rogu ramki i podziel je na odpowiednie wymiary ramki. Ustaw wyniki jako współrzędne x, y wierzchołka. W polu można ustawić konfigurację wielokąta strefy `SPACEANALYTICS_CONFIG` .

Jest to przykładowa ramka wideo, która pokazuje, jak są obliczane współrzędne wierzchołka dla ramki o rozmiarze 1920/1080.
![Przykładowa ramka wideo](./media/spatial-analysis/sample-video-frame.jpg)

Można również wybrać próg ufności dla wykrytych osób i generować zdarzenia. Ustaw próg na wartość 0, jeśli chcesz, aby wszystkie zdarzenia były wyprowadzane.

### <a name="execute-the-deployment"></a>Wykonanie wdrożenia

Teraz, gdy manifest wdrożenia został ukończony, użyj tego polecenia w wierszu poleceń platformy Azure, aby wdrożyć kontener na komputerze hosta jako moduł IoT Edge.

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

Wypełnij wymagane parametry:

* Nazwa IoT Hub: Nazwa IoT Hub platformy Azure
* DeploymentManifest.js: nazwa pliku wdrożenia
* IoT Edge nazwa urządzenia: nazwa urządzenia IoT Edge komputera hosta
* Subskrypcja: identyfikator lub nazwa subskrypcji

To polecenie rozpocznie wdrożenie i będzie można wyświetlić stan wdrożenia w wystąpieniu usługi Azure IoT Hub w Azure Portal. Stan może być wyświetlany jako *417 — konfiguracja wdrożenia urządzenia nie jest ustawiona* , dopóki nie zakończy się pobieranie obrazów kontenera i zacznie działać.

### <a name="validate-that-the-deployment-was-successful"></a>Sprawdź, czy wdrożenie zakończyło się pomyślnie

Znajdź *stan środowiska uruchomieniowego* w IoT Edge ustawienia modułu dla modułu analizy przestrzennej w wystąpieniu IoT Hub na Azure Portal. **Wymagana wartość** i **raportowana wartość** dla *stanu czasu wykonywania* powinna być wymawiana `Running` . Poniżej znajdują się informacje o tym, co będzie wyglądać na Azure Portal.

![Przykładowa weryfikacja wdrożenia](./media/spatial-analysis/deployment-verification.png)

W tym momencie kontener analizy przestrzennej uruchamia operację. Emituje on usługę AI Insights dla `cognitiveservices.vision.spatialanalysis-personcount` operacji i kieruje te informacje jako dane telemetryczne do wystąpienia usługi Azure IoT Hub. Aby skonfigurować dodatkowe aparaty fotograficzne, można zaktualizować plik manifestu wdrożenia i ponownie wykonać wdrożenie.

## <a name="person-counting-web-application"></a>Aplikacja sieci Web do zliczania osób

Ta osoba zlicza aplikację sieci Web umożliwia szybkie konfigurowanie przykładowej aplikacji sieci Web i hostowanie jej w środowisku platformy Azure.

### <a name="get-the-person-counting-app-container"></a>Pobierz kontener aplikacji do zliczania osób

Formularz kontenera tej aplikacji dostępny na Azure Container Registry. Aby pobrać plik, użyj następującego polecenia docker pull. Skontaktuj się z firmą Microsoft pod projectarchon@microsoft.com kątem tokenu dostępu.

```bash
docker login rtvsofficial.azurecr.io -u <token name> -p <password>
docker pull rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0
```

Wypchnij kontener do Azure Container Registry (ACR).

```bash
az acr login --name <your ACR name>

docker tag rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0 [desired local image name]

docker push [desired local image name]
```

Aby zainstalować kontener, Utwórz nowy Web App for Containers platformy Azure i Wypełnij wymagane parametry. Następnie przejdź do karty **Docker** i wybierz pozycję **pojedynczy kontener**, a następnie **Azure Container Registry**. Użyj wystąpienia Azure Container Registry, na którym wypchnięciem Powyższy obraz.

![Wprowadź szczegóły obrazu](./media/spatial-analysis/solution-app-create-screen.png)

Po wprowadzeniu powyższych parametrów kliknij pozycję **Recenzja + Utwórz** i Utwórz aplikację.

### <a name="configure-the-app"></a>Konfigurowanie aplikacji 

Poczekaj na zakończenie instalacji i przejdź do zasobu w Azure Portal. Przejdź do sekcji **Konfiguracja** i Dodaj następujące dwa **Ustawienia aplikacji**.

* `EventHubConsumerGroup` — Nazwa ciągu grupy odbiorców z usługi Azure IoT Hub, można utworzyć nową grupę odbiorców w IoT Hub lub użyć grupy domyślnej. 
* `IotHubConnectionString` — Parametry połączenia z IoT Hubem platformy Azure, które można pobrać z sekcji klucze zasobów usługi Azure IoT Hub ![ skonfigurować parametry](./media/spatial-analysis/solution-app-config-page.png)

Po dodaniu tych 2 ustawień kliknij przycisk **Zapisz**. Następnie w menu nawigacji po lewej stronie kliknij pozycję **uwierzytelnianie/autoryzacja** i zaktualizuj ją na żądanym poziomie uwierzytelniania. Zalecamy Azure Active Directory (Azure AD) Express. 

### <a name="test-the-app"></a>Testowanie aplikacji

Przejdź do aplikacji sieci Web platformy Azure i sprawdź, czy wdrożenie zakończyło się pomyślnie, a aplikacja sieci Web jest uruchomiona. Przejdź do skonfigurowanego adresu URL: `<yourapp>.azurewebsites.net` Aby wyświetlić uruchomioną aplikację.

![Testowanie wdrożenia](./media/spatial-analysis/solution-app-output.png)

## <a name="get-the-personcount-source-code"></a>Pobierz kod źródłowy PersonCount
Jeśli chcesz wyświetlić lub zmodyfikować kod źródłowy dla tej aplikacji, możesz go znaleźć [w serwisie GitHub](https://github.com/Azure-Samples/cognitive-services-spatial-analysis).

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie operacji analizy przestrzennej](./spatial-analysis-operations.md)
* [Rejestrowanie i rozwiązywanie problemów](spatial-analysis-logging.md)
* [Przewodnik umieszczania w aparacie](spatial-analysis-camera-placement.md)
* [Przewodnik umieszczania strefy i linii](spatial-analysis-zone-line-placement.md)
