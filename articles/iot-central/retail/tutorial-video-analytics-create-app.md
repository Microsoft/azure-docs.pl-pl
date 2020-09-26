---
title: Samouczek — Tworzenie aplikacji do wykrywania filmów i obiektów wideo na platformie Azure IoT Central
description: W tym samouczku pokazano, jak utworzyć aplikację do analizy wideo w IoT Central. Tworzysz go, dostosowujesz i nawiązujesz połączenie z innymi usługami platformy Azure.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 07/31/2020
ms.openlocfilehash: c7ddff70d24fe87380c4bf1439811042418ac76a
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369573"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central"></a>Samouczek: Tworzenie aplikacji do wykrywania filmów i obiektów wideo na platformie Azure IoT Central

Korzystając z konstruktora rozwiązań, Dowiedz się, jak utworzyć aplikację analityczną wideo z szablonem aplikacji IoT Central *Video Analytics — Object and Motion* , Azure IoT Edge devices i Azure Media Services. Rozwiązanie używa magazynu detalicznego, aby pokazać, jak spełnić typowe potrzeby biznesowe do monitorowania kamer zabezpieczeń. Rozwiązanie używa automatycznego wykrywania obiektów w kanale wideo, aby szybko identyfikować i lokalizować interesujące zdarzenia.

Przykładowa aplikacja zawiera dwa symulowane urządzenia i jedną bramę IoT Edge. W poniższych samouczkach przedstawiono dwa podejścia do eksperymentowania z i zrozumienia możliwości bramy:

* Utwórz bramę IoT Edge na maszynie wirtualnej platformy Azure i podłącz symulowany aparat.
* Utwórz bramę IoT Edge na rzeczywistym urządzeniu, takim jak Intel NUC, i podłącz rzeczywistą kamerę.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie aplikacji dla sklepu detalicznego za pomocą szablonu aplikacji Azure IoT Central Video Analytics
> * Dostosowywanie ustawień aplikacji
> * Tworzenie szablonu urządzenia dla urządzenia bramy IoT Edge
> * Dodawanie urządzenia bramy do aplikacji IoT Central

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć tę serię samouczków, potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription).
* Jeśli używasz rzeczywistego aparatu fotograficznego, potrzebujesz łączności między urządzeniem IoT Edge i kamerą i potrzebujesz kanału **protokołu przesyłania strumieniowego w czasie rzeczywistym** .

## <a name="initial-setup"></a>Początkowa konfiguracja

W tych samouczkach można aktualizować i używać kilku plików konfiguracji. Początkowe wersje tych plików są dostępne w repozytorium [LVA-Gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) GitHub. Repozytorium zawiera również plik tekstowy ScratchPad do pobrania i użycia w celu rejestrowania wartości konfiguracyjnych z wdrażanych usług.

Utwórz folder o nazwie *LVA-Configuration* na maszynie lokalnej, aby zapisać kopie tych plików. Następnie kliknij prawym przyciskiem myszy poszczególne poniższe linki i wybierz polecenie **Zapisz jako** , aby zapisać plik w folderze *LVA-Configuration* :

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.jsna](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.jsna](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.jsna](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> Repozytorium GitHub obejmuje również kod źródłowy dla modułów **IoT Edge i** **lvaYolov3** . Aby uzyskać więcej informacji na temat pracy z kodem źródłowym, zobacz [Tworzenie modułów bramy LVA](tutorial-video-analytics-build-module.md).

## <a name="deploy-and-configure-azure-media-services"></a>Wdróż i skonfiguruj Azure Media Services

Rozwiązanie używa konta Azure Media Services do przechowywania klipów wideo wykrywania obiektów wykonanych przez urządzenie bramy IoT Edge Gateway.

Podczas tworzenia konta Media Services:

- Musisz podać nazwę konta, subskrypcję platformy Azure, lokalizację, grupę zasobów i konto magazynu. Utwórz nowe konto magazynu przy użyciu ustawień domyślnych podczas tworzenia konta Media Services.

- Wybierz region **Wschodnie stany USA** dla lokalizacji.

- Utwórz nową grupę zasobów o nazwie *LVA-RG* w regionie **Wschodnie stany usa** dla kont Media Services i magazynu. Po zakończeniu samouczków można łatwo usunąć wszystkie zasoby, usuwając grupę zasobów *LVA-RG* .

Utwórz [konto Media Services w Azure Portal](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> Te samouczki korzystają z regionu **Wschodnie stany USA** we wszystkich przykładach. Jeśli wolisz, możesz użyć najbliższego regionu.

Zanotuj nazwę konta **Media Services** w pliku *scratchpad.txt* .

Po zakończeniu wdrażania przejdź do strony **Właściwości** dla konta **Media Services** . Zanotuj **Identyfikator zasobu** w pliku *scratchpad.txt* , użyjesz tej wartości później podczas konfigurowania modułu IoT Edge.

Następnie skonfiguruj nazwę główną usługi Azure Active Directory dla zasobu Media Services. Wybierz opcję **dostęp do interfejsu API** , a następnie pozycję **uwierzytelnianie nazwy głównej usługi**. Utwórz nową aplikację Azure Active Directory o takiej samej nazwie jak zasób Media Services i Utwórz wpis tajny z opisem *IoT Edge dostęp*.

:::image type="content" source="./media/tutorial-video-analytics-create-app/media-service-authentication.png" alt-text="Konfigurowanie aplikacji usługi AAD dla usługi AMS":::

Po wygenerowaniu wpisu tajnego przewiń w dół do sekcji **Kopiowanie poświadczeń w celu połączenia z aplikacją główną usługi** . Następnie wybierz pozycję **JSON**. Wszystkie informacje o poświadczeniach można skopiować z tego miejsca w jednym miejscu. Zanotuj te informacje w pliku *scratchpad.txt* , użyjesz go później podczas konfigurowania urządzenia IoT Edge.

> [!WARNING]
> Jest to jedyna okazja do wyświetlania i zapisywania klucza tajnego. Jeśli utracisz tę opcję, musisz wygenerować kolejny klucz tajny.

## <a name="create-the-azure-iot-central-application"></a>Tworzenie aplikacji IoT Central platformy Azure

W tej sekcji utworzysz nową aplikację usługi Azure IoT Central na podstawie szablonu. Ta aplikacja jest używana w całej serii samouczków, aby skompilować kompletne rozwiązanie.

Aby utworzyć nową aplikację usługi Azure IoT Central:

1. Przejdź do witryny sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) .

1. Zaloguj się przy użyciu poświadczeń używanych w celu uzyskania dostępu do subskrypcji platformy Azure.

1. Aby rozpocząć tworzenie nowej aplikacji IoT Central platformy Azure, wybierz pozycję **Nowa aplikacja** na stronie **kompilacja** .

1. Wybierz pozycję **sprzedaż detaliczna**. Na stronie detalicznej są wyświetlane kilka szablonów aplikacji sieci Web.

Aby utworzyć nową aplikację do analizy wideo:

1. Wybierz szablon aplikacja do **analizy filmów wideo — obiekt i ruch** . Ten szablon zawiera szablony urządzeń dla urządzeń używanych w samouczku. Szablon zawiera przykładowe pulpity nawigacyjne, których mogą używać operatory do wykonywania zadań, takich jak monitorowanie i zarządzanie kamerami.

1. Opcjonalnie możesz wybrać przyjazną **nazwę aplikacji**. Ta aplikacja jest oparta na fikcyjnym sklepie detalicznym o nazwie Northwind Traders. W tym samouczku zostanie użyta **Nazwa aplikacji** *Northwind Traders Video Analytics*.

    > [!NOTE]
    > Jeśli używasz przyjaznej **nazwy aplikacji**, nadal musisz użyć unikatowej wartości dla **adresu URL**aplikacji.

1. Jeśli masz subskrypcję platformy Azure, wybierz swój **katalog**, **subskrypcję platformy azure**, a **Stany Zjednoczone** jako **lokalizację**. Jeśli nie masz subskrypcji, możesz włączyć **7-dniową bezpłatną wersję próbną** i uzupełnić wymagane informacje kontaktowe. W tym samouczku są używane trzy urządzenia — dwie aparaty fotograficzne i urządzenie IoT Edge — Jeśli nie będziesz korzystać z bezpłatnej wersji próbnej, opłaty będą naliczane za użycie.

    Aby uzyskać więcej informacji na temat katalogów, subskrypcji i lokalizacji, zobacz [Tworzenie aplikacji — szybki start](../core/quick-deploy-iot-central.md).

1. Wybierz przycisk **Utwórz**.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/new-application.png" alt-text="Strona Tworzenie aplikacji usługi Azure IoT Central":::

### <a name="retrieve-the-configuration-data"></a>Pobieranie danych konfiguracyjnych

W dalszej części tego samouczka podczas konfigurowania bramy IoT Edge wymagane są pewne dane konfiguracyjne z aplikacji IoT Central. Urządzenie IoT Edge potrzebuje tych informacji do zarejestrowania się w usłudze i nawiązania połączenia z aplikacją.

W sekcji **Administracja** wybierz **aplikację** i zanotuj **adres URL aplikacji** oraz **Identyfikator aplikacji** w pliku *scratchpad.txt* :

:::image type="content" source="./media/tutorial-video-analytics-create-app/administration.png" alt-text="Zrzut ekranu przedstawia okienko Administracja na stronie Analiza wideo z wyróżnioną aplikacją U R L i aplikacją I D.":::

Wybierz pozycję **tokeny interfejsu API** i Wygeneruj nowy token o nazwie **LVAEdgeToken** dla roli **operatora** :

:::image type="content" source="./media/tutorial-video-analytics-create-app/token.png" alt-text="Generuj token":::

Zanotuj token w pliku *scratchpad.txt* w przyszłości. Po zamknięciu okna dialogowego nie można ponownie wyświetlić tokenu.

W sekcji **Administracja** wybierz pozycję **połączenie z urządzeniem**, a następnie wybierz pozycję **SAS-IoT-Devices**.

Zanotuj **klucz podstawowy** dla urządzeń w pliku *scratchpad.txt* . Ten *token sygnatury dostępu współdzielonego* jest używany później podczas konfigurowania urządzenia IoT Edge.

## <a name="edit-the-deployment-manifest"></a>Edytowanie manifestu wdrożenia

Moduł IoT Edge jest wdrażany przy użyciu manifestu wdrożenia. W IoT Central można zaimportować manifest jako szablon urządzenia, a następnie pozwolić IoT Central na zarządzanie wdrożeniem modułu.

Aby przygotować manifest wdrożenia:

1. Otwórz *deployment.amd64.jsw* pliku, który został zapisany w folderze *LVA-Configuration* przy użyciu edytora tekstów.

1. Znajdź `LvaEdgeGatewayModule` Ustawienia i Zmień nazwę obrazu, jak pokazano w poniższym fragmencie kodu:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Dodaj nazwę konta Media Services w `env` węźle w `LvaEdgeGatewayModule` sekcji. Nazwa konta została zanotowana w pliku *scratchpad.txt* :

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. Szablon nie uwidacznia tych właściwości w IoT Central, dlatego należy dodać wartości konfiguracji Media Services do manifestu wdrożenia. Znajdź `lvaEdge` moduł i Zastąp symbole zastępcze wartościami, które zostały zanotowane w pliku *scratchpad.txt* podczas tworzenia konta Media Services.

    `azureMediaServicesArmId`Jest to **Identyfikator zasobu** , który został zanotowany w pliku *scratchpad.txt* podczas tworzenia konta Media Services.

    `aadTenantId` `aadServicePrincipalAppId` `aadServicePrincipalSecret` Podczas tworzenia jednostki usługi dla konta Media Services należy zanotować, i w pliku *scratchpad.txt* :

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Zapisz zmiany.

Opcjonalnie moduł Yolov3 można zastąpić za pomocą zoptymalizowanego pod kątem sprzętu [ &trade; modułu rozszerzenia AI OpenVINO modelu](https://github.com/openvinotoolkit/model_server/tree/ams/extras/ams_wrapper) z technologią Intel. Przykładdeployment.openvino.amd64.jsmanifestu wdrożenia można pobrać [ na](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json). Ten manifest zastępuje ustawienia konfiguracji `lvaYolov3` modułu następującym konfiguracją:

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="create-the-azure-iot-edge-gateway-device"></a>Tworzenie urządzenia bramy Azure IoT Edge

Aplikacja do wykrywania filmów wideo — obiekt i ruch zawiera szablon urządzenia **LVA Edge** oraz szablon urządzenia do **wykrywania ruchu brzegowego** . W tej sekcji utworzysz szablon urządzenia bramy przy użyciu manifestu wdrażania i dodasz urządzenie bramy do aplikacji IoT Central.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Tworzenie szablonu urządzenia dla bramy LVA Edge

Aby zaimportować manifest wdrożenia i utworzyć szablon urządzenia **bramy LVA Edge** :

1. W aplikacji IoT Central przejdź do **szablonów urządzeń**i wybierz pozycję **+ Nowy**.

1. Na stronie **Wybieranie typu szablonu** wybierz kafelek **Azure IoT Edge** . Następnie wybierz pozycję **Dalej: Dostosuj**.

1. Na stronie **Przekaż manifest wdrożenia Azure IoT Edge** wprowadź *LVA Edge Gateway* jako nazwę szablonu i sprawdź **urządzenie bramy z urządzeniami podrzędnymi**.

    Nie wyszukuj jeszcze manifestu wdrożenia. W takim przypadku Kreator wdrażania oczekuje interfejsu dla każdego modułu, ale musisz tylko uwidocznić interfejs dla **LvaEdgeGatewayModule**. Manifest można przekazać w późniejszym kroku.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/upload-deployment-manifest.png" alt-text="Nie przekazuj manifestu wdrożenia":::

    Wybierz opcję **Dalej: Review** (Dalej: przegląd).

1. Na stronie **Przegląd** wybierz pozycję **Utwórz**.

### <a name="import-the-device-capability-model"></a>Importuj model możliwości urządzenia

Szablon urządzenia musi zawierać model możliwości urządzenia. Na stronie **LVA Edge Gateway** wybierz kafelek **model możliwości importowania** . Przejdź do utworzonego wcześniej folderu *LVA-Configuration* i wybierz *LvaEdgeGatewayDcm.jsw* pliku.

Szablon urządzenia **bramy LVA Edge** zawiera teraz **moduł LVA graniczny Gateway** oraz trzy interfejsy: **Informacje o urządzeniu**, **LVAe bramy graniczne**i **LVA**.

### <a name="replace-the-manifest"></a>Zastąp manifest

Na stronie **LVA Edge Gateway** wybierz pozycję **+ Zamień manifest**.

:::image type="content" source="./media/tutorial-video-analytics-create-app/replace-manifest.png" alt-text="Zastąp manifest":::

Przejdź do folderu *LVA-Configuration* i wybierz *deployment.amd64.jsw* pliku manifestu, który był wcześniej edytowany. Wybierz pozycję **Przekaż**. Po zakończeniu walidacji wybierz pozycję **Zamień**.

### <a name="add-relationships"></a>Dodaj relacje

W szablonie urządzenia **bramy LVA Edge** w obszarze **moduły/LVA krawędź bramy**wybierz pozycję **relacje**. Wybierz pozycję **+ Dodaj relację** i Dodaj następujące dwie relacje:

|Nazwa wyświetlana               |Nazwa          |Cel |
|-------------------------- |------------- |------ |
|Detektor ruchu LVA Edge   |Używanie wartości domyślnej   |Urządzenie czujnika ruchu LVA Edge |
|LVA — detektor obiektów krawędzi   |Używanie wartości domyślnej   |Urządzenie do wykrywania obiektów brzegowych LVA |

Następnie wybierz przycisk **Zapisz**.

:::image type="content" source="media/tutorial-video-analytics-create-app/relationships.png" alt-text="Dodaj relacje":::

### <a name="add-views"></a>Dodawanie widoków

Szablon urządzenia **bramy LVA Edge** nie zawiera żadnych definicji widoku.

Aby dodać widok do szablonu urządzenia:

1. W szablonie urządzenia **bramy LVA Edge** przejdź do **widoku widoki** i wybierz kafelek **Wizualizacja urządzenia** .

1. Wprowadź *urządzenie bramy LVA Edge* jako nazwę widoku.

1. Dodaj następujące kafelki do widoku:

    * Kafelek z właściwościami **informacji o urządzeniu** : **model urządzenia**, **producent**, **system operacyjny**, **architektura procesora**, **wersja oprogramowania**, **Łączna ilość pamięci**i **łączny magazyn**.
    * Kafelek wykresu liniowego z **bezpłatną ilością pamięci** i wartościami telemetrycznymi **pulsu systemowego** .
    * Kafelek historia zdarzeń z następującymi zdarzeniami: **Tworzenie aparatu**, **usuwanie aparatu**, **Ponowne uruchamianie modułu**, **moduł rozpoczęty**, **moduł został zatrzymany**.
    * Kafelek "2x1 Ostatnia znana wartość" pokazujący telemetrię **stanu klienta IoT Central** .
    * Kafelek "2x1 Ostatnia znana wartość" pokazujący telemetrię **stanu modułu** .
    * Kafelek 1x1 Ostatnia znana wartość przedstawiająca dane telemetryczne **pulsu systemu** .
    * Kafelek "1x1 Ostatnia znana wartość" pokazujący telemetrię **podłączonych kamer** .

    :::image type="content" source="media/tutorial-video-analytics-create-app/gateway-dashboard.png" alt-text="Pulpit nawigacyjny":::

1. Wybierz pozycję **Zapisz**.

### <a name="publish-the-device-template"></a>Publikowanie szablonu urządzenia

Aby można było dodać urządzenie do aplikacji, należy opublikować szablon urządzenia:

1. W szablonie urządzenia **bramy LVA Edge** wybierz pozycję **Publikuj**.

1. Na stronie **Publikuj ten szablon urządzenia na potrzeby aplikacji** wybierz pozycję **Publikuj**.

**Brama LVA Edge** jest teraz dostępna jako typ urządzenia do użycia na stronie **urządzenia** w aplikacji.

## <a name="add-a-gateway-device"></a>Dodawanie urządzenia bramy

Aby dodać urządzenie **bramy LVA Edge** do aplikacji:

1. Przejdź do strony **urządzenia** i wybierz szablon urządzenia **bramy LVA Edge** .

1. Wybierz pozycję **+ Nowe**.

1. W oknie dialogowym **Tworzenie nowego urządzenia** Zmień nazwę urządzenia na *LVA Gateway 001*i zmień identyfikator urządzenia na *LVA-Gateway-001*.

    > [!NOTE]
    > Identyfikator urządzenia musi być unikatowy w aplikacji.

1. Wybierz przycisk **Utwórz**.

Stan urządzenia jest **zarejestrowany**.

### <a name="get-the-device-credentials"></a>Pobieranie poświadczeń urządzenia

Wymagane są poświadczenia zezwalające urządzeniu na łączenie się z aplikacją IoT Central. Pobierz poświadczenia urządzenia:

1. Na stronie **urządzenia** wybierz utworzone urządzenie **LVA-Gateway-001** .

1. Wybierz pozycję **Połącz**.

1. Na stronie **połączenie urządzenia** zanotuj *scratchpad.txt* pliku z **zakresem identyfikatorów**, **identyfikatorem urządzenia**i **kluczem podstawowym**urządzenia. Te wartości są używane później.

1. Upewnij się, że metoda połączenia jest ustawiona na **sygnaturę dostępu współdzielonego**.

1. Wybierz pozycję **Close** (Zamknij).

## <a name="next-steps"></a>Następne kroki

Aplikacja IoT Central została utworzona przy użyciu szablonu **wideo Analytics — obiekty i wykrywanie ruchu** , utworzyła szablon urządzenia dla urządzenia bramy i dodał urządzenie bramy do aplikacji.

Jeśli chcesz wypróbować aplikację do wykrywania obiektów wideo i wykrycia ruchu przy użyciu modułów IoT Edge, na których uruchomiono maszynę wirtualną w chmurze z symulowanymi strumieniami wideo:

> [!div class="nextstepaction"]
> [Tworzenie wystąpienia IoT Edge dla analiz wideo (maszyna wirtualna z systemem Linux)](./tutorial-video-analytics-iot-edge-vm.md)

Jeśli chcesz wypróbować aplikację do wykrywania obiektów wideo i wykrycia ruchu przy użyciu modułów IoT Edge, na których działa rzeczywiste urządzenie z **ONVIFą** kamerą:

> [!div class="nextstepaction"]
> [Tworzenie wystąpienia IoT Edge dla analiz wideo (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md)
