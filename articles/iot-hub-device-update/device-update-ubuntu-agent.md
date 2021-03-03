---
title: Samouczek dotyczący aktualizacji urządzeń z platformą Azure IoT Hub przy użyciu agenta pakietu Ubuntu Server 18,04 x64 | Microsoft Docs
description: Rozpocznij pracę z aktualizacją urządzenia dla platformy Azure IoT Hub przy użyciu agenta pakietu Ubuntu Server 18,04 x64.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 19be3b141fb663ea0f4f11d811bf6ffc33252504
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665178"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-server-1804-x64-package-agent"></a>Samouczek dotyczący aktualizacji urządzeń z platformą Azure IoT Hub przy użyciu agenta pakietu Ubuntu Server 18,04 x64

Aktualizacja urządzenia dla IoT Hub obsługuje dwie formy aktualizacji — oparte na obrazach i pakietach.

Aktualizacje oparte na pakiecie są przeznaczonymi do aktualizacji, które zmieniają tylko określony składnik lub aplikację na urządzeniu. Prowadzi to do obniżenia zużycia przepustowości i skrócenia czasu pobierania i instalowania aktualizacji. Aktualizacje pakietu zwykle umożliwiają zmniejszenie przestojów urządzeń podczas stosowania aktualizacji i unikania nakładów tworzenia obrazów.

Ten samouczek przeprowadzi Cię przez kroki umożliwiające ukończenie kompleksowej aktualizacji opartej na pakiecie za pośrednictwem aktualizacji urządzenia dla IoT Hub. Na potrzeby tego samouczka będziemy używać przykładowego agenta pakietu dla programu Ubuntu Server 18,04 x64. Nawet jeśli planujesz korzystanie z innej konfiguracji platformy systemu operacyjnego, ten samouczek nadal jest przydatny do poznania narzędzi i koncepcji dotyczących aktualizacji urządzeń IoT Hub. Wykonaj to wprowadzenie do kompleksowego procesu aktualizacji, a następnie wybierz preferowaną formę aktualizacji i platformy systemu operacyjnego, aby szczegółowe szczegółowe informacje. W ramach tego samouczka możesz zaktualizować urządzenie usługi Azure IoT lub Azure IoT Edge za pomocą aktualizacji urządzenia do IoT Hub. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj repozytorium pakietu aktualizacji urządzeń
> * Pobieranie i Instalowanie agenta aktualizacji urządzeń wraz z jego zależnościami
> * Dodawanie tagu do urządzenia IoT
> * Importowanie aktualizacji
> * Tworzenie grupy urządzeń
> * Wdróż aktualizację pakietu
> * Monitorowanie wdrożenia aktualizacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne
* Dostęp do IoT Hub. Zaleca się użycie warstwy S1 (standardowa) lub wyższej.
* Urządzenie usługi Azure IoT lub Azure IoT Edge z systemem Ubuntu Server 18,04 x64 połączone z IoT Hub.
   * Jeśli używasz urządzenia Azure IoT Edge, upewnij się, że jest on w wersji 1.2.0 środowiska uruchomieniowego Edge lub nowszej 
* Jeśli nie korzystasz z urządzenia Azure IoT Edge, [Zainstaluj najnowszy `aziot-identity-service` pakiet (wersja zapoznawcza) na urządzeniu IoT](https://github.com/Azure/iot-identity-service/actions/runs/575919358) 
* [Konto i wystąpienie aktualizacji urządzenia połączone z tym samym IoT Hub jak powyżej.](create-device-update-account.md)

## <a name="configure-device-update-package-repository"></a>Skonfiguruj repozytorium pakietu aktualizacji urządzeń

1. Zainstaluj konfigurację repozytorium zgodną z systemem operacyjnym urządzenia. W tym samouczku zostanie Ubuntu serwer 18,04. 
   
   ```shell
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

2. Skopiuj wytworzoną listę do katalogu sources. list. d.

   ```shell
      sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```
   
3. Zainstaluj klucz publiczny programu Microsoft GPG.

   ```shell
      curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
      sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-device-update-deb-agent-packages"></a>Zainstaluj pakiety agentów aktualizacji urządzeń deb

1. Aktualizowanie list pakietów na urządzeniu

   ```shell
      sudo apt-get update
   ```

2. Instalowanie pakietu deviceupdate-Agent wraz z jego zależnościami

   ```shell
      sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
   ```

Aktualizacja urządzenia dla pakietów oprogramowania platformy Azure IoT Hub podlega następującym postanowieniom licencyjnym:
   * [Aktualizacja urządzenia z licencją IoT Hub](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licencja klienta optymalizacji dostarczania](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Przeczytaj postanowienia licencyjne przed użyciem pakietu. Instalacja i użycie pakietu oznacza akceptację tych warunków. Jeśli nie akceptujesz postanowień licencyjnych, nie używaj tego pakietu.

## <a name="configure-device-update-agent-using-azure-iot-identity-service-preview"></a>Konfigurowanie agenta aktualizacji urządzeń przy użyciu usługi Azure IoT Identity Service (wersja zapoznawcza)

Po zainstalowaniu wymaganych pakietów należy zainicjować obsługę administracyjną urządzenia, korzystając z informacji o tożsamości i uwierzytelnianiu w chmurze.

1. Otwórz plik konfiguracji

   ```shell
      sudo nano /etc/aziot/config.toml
   ```

2. Znajdź sekcję Konfiguracja aprowizacji pliku. Usuń komentarz z sekcji "Ręczne inicjowanie obsługi przy użyciu parametrów połączenia". Zaktualizuj wartość connection_string przy użyciu parametrów połączenia dla urządzenia IoT (Edge). Upewnij się, że wszystkie pozostałe sekcje aprowizacji zostały oznaczone jako komentarze.


   ```toml
      # Manual provisioning configuration using a connection string
      [provisioning]
      source = "manual"
      iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
      device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
      dynamic_reprovisioning = false 
   ```

3. Zapisz i zamknij plik, używając kombinacji klawiszy Ctrl + X, Y

4. Zastosuj konfigurację. 

   Jeśli używasz urządzenia IoT Edge, użyj następującego polecenia. 
   
   ```shell
      sudo iotedge config apply
   ```
   
   Jeśli używasz urządzenia IoT z `aziot-identity-service` zainstalowanym pakietem, użyj następującego polecenia. 
      
   ```shell
      sudo aziotctl config apply
   ```

5. Opcjonalnie możesz sprawdzić, czy usługi są uruchomione przez

    ```shell
       sudo systemctl list-units --type=service | grep 'adu-agent\.service\|deliveryoptimization-agent\.service'
    ```

    Dane wyjściowe powinny być odczytane:

    ```markdown
       adu-agent.service                   loaded active running Device Update for IoT Hub Agent daemon.

       deliveryoptimization-agent.service               loaded active running deliveryoptimization-agent.service: Performs content delivery optimization tasks   `
    ```

## <a name="add-a-tag-to-your-device"></a>Dodawanie tagu do urządzenia

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do IoT Hub.

2. W okienku nawigacji po lewej stronie "IoT Devices" lub "IoT Edge" Znajdź urządzenie IoT i przejdź do tego samego urządzenia.

3. W obszarze urządzenia Usuń wszystkie istniejące wartości tagu aktualizacji urządzenia, ustawiając je na wartość null.

4. Dodaj nową wartość znacznika aktualizacji urządzenia, jak pokazano poniżej.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Importuj aktualizację

1. Pobierz następujący [plik manifestu apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-apt-manifest.json) i [Zaimportuj plik manifestu](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.1-importManifest.json). Ten manifest apt zainstaluje najnowszą dostępną wersję programu `libcurl4-doc package` na urządzeniu IoT. 

   Alternatywnie można pobrać ten [plik manifestu apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-7.58-apt-manifest.json) i [zaimportować plik manifestu](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-2-2.0.1-importManifest.json). Spowoduje to zainstalowanie określonej wersji v 7.58.0 programu `libcurl4-doc package` na urządzeniu IoT. 

2. W Azure Portal wybierz opcję Aktualizacje urządzenia w obszarze Automatyczne zarządzanie urządzeniami z paska nawigacyjnego po lewej stronie w IoT Hub.

3. Wybierz kartę aktualizacje.

4. Wybierz pozycję "+ Importuj nową aktualizację".

5. Zaznacz ikonę folderu lub pole tekstowe w obszarze "Wybierz plik manifestu importowania". Zostanie wyświetlone okno dialogowe selektora plików. Wybierz pobrany wcześniej manifest importu. Następnie wybierz ikonę folderu lub pole tekstowe w obszarze "Wybierz jeden lub więcej plików aktualizacji". Zostanie wyświetlone okno dialogowe selektora plików. Wybierz pobrany wcześniej plik aktualizacji manifestu apt.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Zrzut ekranu przedstawiający wybór pliku aktualizacji." lightbox="media/import-update/select-update-files.png":::

6. Zaznacz ikonę folderu lub pole tekstowe w obszarze "Wybieranie kontenera magazynu". Następnie wybierz odpowiednie konto magazynu.

7. Jeśli kontener został już utworzony, możesz go użyć ponownie. (W przeciwnym razie wybierz pozycję "+ kontener", aby utworzyć nowy kontener magazynu dla aktualizacji).  Wybierz kontener, którego chcesz użyć, a następnie kliknij przycisk "Wybierz".

   :::image type="content" source="media/import-update/container.png" alt-text="Zrzut ekranu przedstawiający wybór kontenera." lightbox="media/import-update/container.png":::

8. Wybierz pozycję "Prześlij", aby rozpocząć proces importowania.

9. Rozpocznie się proces importowania, a ekran zostanie zmieniony na sekcję "Historia importowania". Wybierz pozycję "Odśwież", aby wyświetlić postęp do momentu zakończenia procesu importowania. W zależności od rozmiaru aktualizacji może to zakończyć się w ciągu kilku minut, ale może to potrwać dłużej.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Zrzut ekranu przedstawiający aktualizację sekwencji importu." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Gdy kolumna stan wskazuje, że importowanie zakończyło się pomyślnie, wybierz nagłówek "gotowe do wdrożenia". Zaimportowana aktualizacja powinna zostać wyświetlona na liście teraz.

[Dowiedz się więcej](import-update.md) o importowaniu aktualizacji.

## <a name="create-update-group"></a>Utwórz grupę aktualizacji

1. Przejdź do IoT Hub, który został wcześniej połączony z wystąpieniem aktualizacji urządzenia.

2. Wybierz opcję Aktualizacje urządzenia w obszarze Automatyczne zarządzanie urządzeniami na pasku nawigacyjnym po lewej stronie.

3. Wybierz kartę grupy w górnej części strony. 

4. Wybierz przycisk Dodaj, aby utworzyć nową grupę.

5. Wybierz z listy tag IoT Hub utworzony w poprzednim kroku. Wybierz pozycję Utwórz grupę aktualizacji.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Zrzut ekranu przedstawiający wybór znacznika." lightbox="media/create-update-group/select-tag.PNG":::

[Dowiedz się więcej](create-update-group.md) na temat dodawania tagów i tworzenia grup aktualizacji


## <a name="deploy-update"></a>Wdróż aktualizację

1. Po utworzeniu grupy powinna zostać wyświetlona nowa aktualizacja dla grupy urządzeń z linkiem do aktualizacji w obszarze oczekujące aktualizacje. Może być konieczne odświeżenie jednego z nich. 

2. Kliknij dostępną aktualizację.

3. Upewnij się, że wybrano poprawną grupę jako grupę docelową. Zaplanuj wdrożenie, a następnie wybierz pozycję Wdróż aktualizację.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Wybierz aktualizację" lightbox="media/deploy-update/select-update.png":::

4. Wyświetl wykres zgodności. Powinna zostać wyświetlona aktualizacja, która jest teraz w toku. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Aktualizacja w toku" lightbox="media/deploy-update/update-in-progress.png":::

5. Po pomyślnym zaktualizowaniu urządzenia powinna zostać wyświetlona aktualizacja wykresu zgodności i szczegółów wdrożenia w celu odzwierciedlenia tego samego. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Aktualizacja powiodła się" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorowanie wdrożenia aktualizacji

1. Wybierz kartę wdrożenia w górnej części strony.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Karta wdrożenia" lightbox="media/deploy-update/deployments-tab.png":::

2. Wybierz utworzone wdrożenie, aby wyświetlić szczegóły wdrożenia.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Szczegóły wdrożenia" lightbox="media/deploy-update/deployment-details.png":::

3. Wybierz pozycję Odśwież, aby wyświetlić najnowsze Szczegóły stanu. Kontynuuj ten proces do momentu, aż stan zmieni się na powodzenie.

Ukończono pomyślnie kompleksową aktualizację pakietu przy użyciu aktualizacji urządzenia dla IoT Hub na urządzeniu z systemem Ubuntu Server 18,04 x64. 

## <a name="bonus-steps"></a>Dodatkowe kroki

1. Pobierz następujący [plik manifestu apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-remove-apt-manifest.json) i [Zaimportuj plik manifestu](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.2-importManifest.json). Ten manifest apt spowoduje usunięcie instalacji `libcurl4-doc package` z urządzenia IoT. 

2. Powtórz sekcje "Importuj aktualizację" i "wdróż aktualizację"

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, Oczyść konto, wystąpienie, IoT Hub i urządzenie IoT aktualizacji urządzenia. Możesz to zrobić, przechodząc do poszczególnych zasobów i wybierając pozycję "Usuń". Należy pamiętać, że przed oczyszczeniem konta aktualizacji urządzenia należy oczyścić wystąpienie aktualizacji urządzenia. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aktualizacja obrazu na Raspberry Pi 3 B + samouczek](device-update-raspberry-pi.md)

