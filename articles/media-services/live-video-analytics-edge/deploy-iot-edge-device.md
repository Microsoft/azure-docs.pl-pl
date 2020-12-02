---
title: Wdrażanie analizy filmów wideo na żywo na urządzeniu IoT Edge — Azure
description: W tym artykule przedstawiono kroki, które ułatwią wdrożenie analizy wideo na żywo na urządzeniu IoT Edge. Można to zrobić na przykład, jeśli masz dostęp do lokalnej maszyny z systemem Linux i/lub wcześniej utworzono konto Azure Media Services.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: 4fa4a9643976ba513b025706cacec26b2a50afb5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498323"
---
# <a name="deploy-live-video-analytics-on-an-iot-edge-device"></a>Wdrażanie analizy filmów wideo na żywo na urządzeniu IoT Edge

W tym artykule przedstawiono kroki, które ułatwią wdrożenie analizy wideo na żywo na urządzeniu IoT Edge. Można to zrobić na przykład, jeśli masz dostęp do lokalnej maszyny z systemem Linux i/lub wcześniej utworzono konto Azure Media Services.

> [!NOTE]
> Obsługa urządzeń z systemem ARM64 jest dostępna w usłudze analiza filmów wideo na żywo na IoT Edge kompilacjach `1.0.4` i nowszych.
> Obsługa środowiska uruchomieniowego Azure IoT Edge na urządzeniach ARM64 jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie z systemem x86/64 lub ARM64 z jednym z [obsługiwanych systemów operacyjnych Linux](../../iot-edge/support.md#operating-systems)
* Subskrypcja platformy Azure, do której masz [uprawnienia właściciela](../../role-based-access-control/built-in-roles.md#owner)
* [Utwórz i skonfiguruj IoT Hub](../../iot-hub/iot-hub-create-through-portal.md)
* [Zarejestruj urządzenie IoT Edge](../../iot-edge/how-to-manual-provision-symmetric-key.md)
* [Install the Azure IoT Edge runtime on Debian-based Linux systems (Instalowanie środowiska uruchomieniowego usługi IoT Edge w systemach Linux opartych na rozwiązaniu Debian)](../../iot-edge/how-to-install-iot-edge.md)
* [Tworzenie konta usługi Azure Media Services](../latest/create-account-howto.md)

    * Użyj jednego z następujących regionów: Wschodnie stany USA 2, Wschodnie stany USA, środkowe stany USA, Północno-środkowe stany USA, Japonia Wschodnia, zachodnie stany USA, zachodnie stany USA 2, zachodnie stany USA, Kanada Wschodnia, Południowe Zjednoczone Królestwo, Francja środkowa, Francja Południowa, Szwajcaria Północna, Szwajcaria Zachodnia i Japonia Zachodnia.
    * Zalecane jest użycie kont magazynu ogólnego przeznaczenia w wersji 2 (GPv2)

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>Konfigurowanie zasobów platformy Azure do korzystania z analizy filmów wideo na żywo

### <a name="create-custom-azure-resource-manager-role"></a>Tworzenie niestandardowej roli usługi Azure Resource Manager

Zobacz [Tworzenie roli niestandardowego Azure Resource Manager](create-custom-azure-resource-manager-role-how-to.md) i przypisywanie jej do jednostki usługi w celu użycia na żywo analizy wideo.

### <a name="set-up-a-premium-streaming-endpoint"></a>Konfigurowanie punktu końcowego przesyłania strumieniowego Premium

Jeśli zamierzasz korzystać z usługi Live Video Analytics do ciągłego rejestrowania wideo w chmurze, a następnie używać [interfejsów API zapytań](playback-recordings-how-to.md#query-api) przed ich odtworzeniem, Zalecamy zaktualizowanie usług multimedialnych do korzystania z [punktu końcowego przesyłania strumieniowego Premium](../latest/streaming-endpoint-concept.md#types).  

Ten krok jest opcjonalny. Aby to zrobić, możesz użyć tego polecenia platformy Azure:

```azurecli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

Możesz użyć tego polecenia, aby uruchomić punkt końcowy przesyłania strumieniowego 

> [!IMPORTANT]
> Opłaty zostaną naliczone w tym momencie.

```azurecli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

Wykonaj kroki opisane w tym artykule, aby uzyskać poświadczenia dostępu do interfejsów API usługi Media Service: [dostęp do interfejsów API usługi multimediów](../latest/access-api-howto.md?tabs=portal) i Wybieranie karty Portal.

## <a name="create-and-use-local-user-account-for-deployment"></a>Tworzenie i używanie konta użytkownika lokalnego na potrzeby wdrożenia
Aby uruchomić usługę analiza filmów wideo na żywo w IoT Edge module, Utwórz konto użytkownika lokalnego z możliwie jak najmniejszej liczby uprawnień. Na przykład uruchom następujące polecenia na komputerze z systemem Linux:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

## <a name="granting-permissions-to-device-storage"></a>Przyznawanie uprawnień do magazynu urządzeń

Teraz, gdy utworzono konto użytkownika lokalnego, 

* Do przechowywania danych konfiguracji aplikacji potrzebny jest folder lokalny. Utwórz folder i Udziel uprawnień do zapisu konta LocalUser w tym folderze przy użyciu następujących poleceń:

```
sudo mkdir /var/lib/azuremediaservices
sudo chown -R edgeuser /var/lib/azuremediaservices
```

* Potrzebny będzie również folder do [nagrywania filmów wideo do pliku lokalnego](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources). Użyj następujących poleceń, aby utworzyć folder lokalny dla tego samego:

```
sudo mkdir /var/media
sudo chown -R edgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>Wdrażanie modułu usługi Microsoft Live Video Analytics

Na żywo analiza filmów wideo na IoT Edge uwidacznia właściwości wieloosiowe modułu, które są udokumentowane w [schemacie konfiguracji sznurka modułu](module-twin-configuration-schema.md). 

### <a name="deploy-using-the-azure-portal"></a>Wdrażanie przy użyciu witryny Azure Portal

Azure Portal przeprowadzi Cię przez proces tworzenia manifestu wdrażania i wypychania wdrożenia na urządzenie IoT Edge.  
#### <a name="select-your-device-and-set-modules"></a>Wybierz urządzenie i ustaw moduły

1. Zaloguj się do [Azure Portal](https://ms.portal.azure.com/) i przejdź do centrum IoT Hub.
1. Wybierz **IoT Edge** z menu.
1. Kliknij identyfikator urządzenia docelowego z listy urządzeń.
1. Wybierz pozycję **Ustaw moduły**.

#### <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia to dokument JSON, który opisuje moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości modułu bliźniaczych reprezentacji. Azure Portal zawiera kreatora, który przeprowadzi Cię przez proces tworzenia manifestu wdrożenia. Trzy kroki są zorganizowane na kartach: **moduły**, **trasy** i **Przegląd + Utwórz**.

#### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **IoT Edge modułów** na stronie kliknij pozycję **Dodaj** listę rozwijaną i wybierz **moduł IoT Edge** , aby wyświetlić stronę **Dodawanie modułu IoT Edge** .
1. Na karcie **Ustawienia modułu** Podaj nazwę modułu, a następnie określ identyfikator URI obrazu kontenera:   
    Przykłady:
    
    * **Nazwa modułu IoT Edge**: lvaEdge
    * **Identyfikator URI obrazu**: MCR.Microsoft.com/Media/Live-Video-Analytics:1.0    
    
    ![Zrzut ekranu przedstawia kartę Ustawienia modułu.](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > Nie zaznaczaj opcji **Dodaj** , dopóki nie zostaną określone wartości na kartach **Ustawienia modułu**, **Opcje tworzenia kontenera** i **Ustawienia sznurka modułu** , zgodnie z opisem w tej procedurze.
    
    > [!WARNING]
    > Podczas wykonywania wywołań do modułów w Azure IoT Edge rozróżniana jest wielkość liter. Zanotuj dokładny ciąg używany jako nazwa modułu.

1. Otwórz kartę **zmienne środowiskowe** .
   
   Dodaj następujące wartości w polach wejściowych, w których widoczne są ![ zmienne środowiskowe](./media/deploy-iot-edge-device/environment-variables.png) 

1. Otwórz kartę **Opcje tworzenia kontenera** .

    ![Opcje tworzenia kontenera](./media/deploy-iot-edge-device/container-create-options.png)
 
    Skopiuj i wklej poniższy kod JSON w polu, aby ograniczyć rozmiar plików dziennika generowanych przez moduł.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
               "/var/media:/var/media"
            ]
        }
    }
    ````
   
   Sekcja "powiązania" w formacie JSON zawiera 2 wpisy:
   1. "/var/lib/azuremediaservices:/var/lib/azuremediaservices": służy do powiązania trwałych danych konfiguracji aplikacji z kontenera i zapisywania go na urządzeniu brzegowym.
   1. "/var/media:/var/media": to wiąże foldery multimedialne między urządzeniem brzegowym i kontenerem. Służy do przechowywania nagrań wideo podczas uruchamiania topologii programu Media Graph, która obsługuje przechowywanie klipów wideo na urządzeniu brzegowym.
   
1. Na karcie **Ustawienia sznurka modułu** Skopiuj poniższy kod JSON i wklej go w polu.
 
    ![Ustawienia przędzy](./media/deploy-iot-edge-device/twin-settings.png)

    Analiza wideo na żywo na IoT Edge wymaga zestawu obowiązkowych właściwości bliźniaczych do uruchomienia, zgodnie z opisem w [schemacie konfiguracji sznurka modułu](module-twin-configuration-schema.md).  

    KOD JSON, który należy wprowadzić w polu edycji ustawień sznurka modułu, będzie wyglądać następująco:    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    Są to **wymagane** właściwości i dla kodu JSON powyżej,  
    * {Subskrypcji} — Identyfikator subskrypcji platformy Azure
    * {resourceGroupName} — ta grupa zasobów, do której należy konto usługi multimediów
    * {AMS-account-name} — jest to nazwa konta Media Services
    
    Aby uzyskać inne wartości, zapoznaj się z tematem [dostęp Azure Media Services interfejsu API](../latest/access-api-howto.md?tabs=portal) i wybierz kartę Portal.  
    * aadTenantId — jest to identyfikator dzierżawy, który jest taki sam jak "AadTenantId" w powyższym łączu.
    * aadServicePrincipalAppId — identyfikator aplikacji nazwy głównej usługi dla konta usługi multimediów i jest taki sam jak "AadClientId" w powyższym łączu.
    * aadServicePrincipalSecret — jest to hasło nazwy głównej usługi i jest taka sama jak "AadSecret" w powyższym łączu.

    Poniżej znajdują się dodatkowe **zalecane** właściwości, które można dodać do pliku JSON, a następnie pomóc w monitorowaniu modułu. Aby uzyskać więcej informacji, zobacz [monitorowanie i rejestrowanie](monitoring-logging.md):
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    Poniżej przedstawiono niektóre **opcjonalne** właściwości, które można dodać do pliku JSON:
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```

   > [!Note]
   > Właściwość przędzy **allowUnsecuredEndpoints** jest ustawiona na wartość true na potrzeby samouczków i przewodników Szybki Start.   
   Należy ustawić dla tej właściwości **wartość false** podczas uruchamiania w środowisku produkcyjnym. Pozwoli to zagwarantować, że aplikacja będzie blokować wszystkie niezabezpieczone punkty końcowe i w celu uruchomienia topologii wykresu będą potrzebne prawidłowe poświadczenia połączenia.  
   
    Wybierz pozycję Dodaj, aby dodać właściwości sznurka modułu.
1. Wybierz pozycję **Dalej: trasy** , aby przejść do sekcji trasy.
    Określ trasy.

Zachowaj trasy domyślne i wybierz kolejno pozycje **Dalej: przegląd + Utwórz** , aby przejść do sekcji Przegląd.

#### <a name="review-deployment"></a>Przegląd wdrożenia

Sekcja Przegląd przedstawia manifest wdrożenia JSON, który został utworzony na podstawie wybranych opcji w poprzednich dwóch sekcjach. Istnieją także dwa moduły zadeklarowane jako niedodane: $edgeAgent i $edgeHub. Te dwa moduły składają się na IoT Edge środowisko uruchomieniowe i są wymagane wartości domyślne w każdym wdrożeniu.

Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję Utwórz.

### <a name="verify-your-deployment"></a>Weryfikowanie wdrożenia

Po utworzeniu wdrożenia powrócisz do IoT Edge stronie Centrum IoT Hub.

1. Wybierz urządzenie IoT Edge, do którego należy wdrożenie, aby otworzyć jego szczegóły.
2. Sprawdź szczegóły urządzenia, aby sprawdzić, czy moduł usługi BLOB Storage jest wymieniony jako **określony we wdrożeniu i zgłoszony przez urządzenie**.

Uruchomienie modułu na urządzeniu może potrwać kilka minut, a następnie zgłoszone z powrotem do IoT Hub. Odśwież stronę, aby zobaczyć zaktualizowany stan.
Kod stanu: 200 — wartość OK oznacza, że [środowisko uruchomieniowe IoT Edge jest w](../../iot-edge/iot-edge-runtime.md) dobrej kondycji i działa prawidłowo.

![Zrzut ekranu przedstawia wartość stanu środowiska uruchomieniowego IoT Edge.](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>Wywoływanie metody bezpośredniej

Następnie umożliwia przetestowanie próbki przez wywołanie metody bezpośredniej. Przeczytaj [bezpośrednie metody analizy wideo na żywo na IoT Edge](direct-methods.md) , aby zrozumieć wszystkie metody bezpośrednie dostarczone przez nasz moduł lvaEdge.

1. Kliknięcie utworzonego modułu brzegowego spowoduje przejście do strony konfiguracji.  

    ![Zrzut ekranu przedstawia stronę konfiguracji modułu krawędzi.](./media/deploy-iot-edge-device/modules.png)
1. Kliknij opcję Opcje w menu Metoda bezpośrednia.

    > [!NOTE] 
    > Musisz dodać wartość w sekcjach parametrów połączenia, jak widać na bieżącej stronie. Nie trzeba ukrywać ani zmieniać żadnych elementów w sekcji **Nazwa ustawienia** . Jest to możliwe, aby pozwolić na publiczną.

    ![Metoda bezpośrednia](./media/deploy-iot-edge-device/module-details.png)
1. Następnie wprowadź wartość "GraphTopologyList" w polu Nazwa metody.
1. Następnie skopiuj i wklej poniższy ładunek JSON w polu ładunek.
    
    ```
    {
        "@apiVersion" : "1.0"
    }
    ```
1. Kliknij opcję "Wywołaj metodę" u góry strony

    ![Metody bezpośrednie](./media/deploy-iot-edge-device/direct-method.png)
1. W polu wynik powinien zostać wyświetlony komunikat o stanie 200

    ![Komunikat o stanie 200](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>Następne kroki

Wypróbuj [Szybki Start: Rozpoczynanie pracy — Analiza filmów wideo na żywo na IoT Edge](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)

> [!TIP]
> Jeśli przejdziesz do powyższego przewodnika Szybki Start, podczas wywoływania metody bezpośredniej przy użyciu Visual Studio Code będziesz używać urządzenia, które zostało dodane do IoT Hub za pośrednictwem tego artykułu zamiast domyślnego `lva-sample-device` .