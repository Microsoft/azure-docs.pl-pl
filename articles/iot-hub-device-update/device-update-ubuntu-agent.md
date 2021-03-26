---
title: Samouczek dotyczący aktualizacji urządzeń z platformą Azure IoT Hub przy użyciu agenta pakietu Ubuntu Server 18,04 x64 | Microsoft Docs
description: Rozpocznij pracę z aktualizacją urządzenia dla platformy Azure IoT Hub przy użyciu agenta pakietu Ubuntu Server 18,04 x64.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 9468b3b53e0f7c435bf84b6ef99eb1e0f85d0c8e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560271"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-package-agent-on-ubuntu-server-1804-x64"></a>Samouczek dotyczący aktualizacji urządzenia dla platformy Azure IoT Hub przy użyciu agenta pakietu na serwerze Ubuntu 18,04 x64

Aktualizacja urządzenia dla IoT Hub obsługuje dwie formy aktualizacji — oparte na obrazach i pakietach.

Aktualizacje oparte na pakiecie są przeznaczonymi do aktualizacji, które zmieniają tylko określony składnik lub aplikację na urządzeniu. Prowadzi to do obniżenia zużycia przepustowości i skrócenia czasu pobierania i instalowania aktualizacji. Aktualizacje pakietu zwykle umożliwiają zmniejszenie przestojów urządzeń podczas stosowania aktualizacji i unikania nakładów tworzenia obrazów.

Ten samouczek przeprowadzi Cię przez kroki umożliwiające ukończenie kompleksowej aktualizacji opartej na pakiecie za pośrednictwem aktualizacji urządzenia dla IoT Hub. Na potrzeby tego samouczka korzystamy z programu Ubuntu Server 18,04 x64 z uruchomioną Azure IoT Edge oraz z agentem pakietu aktualizacji urządzenia. W tym samouczku przedstawiono aktualizację przykładowego pakietu, ale przy użyciu podobnych kroków można zaktualizować inne pakiety, takie jak Azure IoT Edge lub aparat kontenerów, z których korzysta.

Narzędzia i koncepcje w tym samouczku nadal mają zastosowanie nawet wtedy, gdy planujesz użyć innej konfiguracji platformy systemu operacyjnego. Wykonaj to wprowadzenie do kompleksowego procesu aktualizacji, a następnie wybierz preferowaną formę aktualizacji i platformy systemu operacyjnego, aby szczegółowe szczegółowe informacje.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Pobieranie i Instalowanie agenta aktualizacji urządzeń wraz z jego zależnościami
> * Dodawanie tagu do urządzenia
> * Importowanie aktualizacji
> * Tworzenie grupy urządzeń
> * Wdróż aktualizację pakietu
> * Monitorowanie wdrożenia aktualizacji

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli jeszcze tego nie zrobiono, Utwórz [konto i wystąpienie aktualizacji urządzenia](create-device-update-account.md), w tym Konfigurowanie IoT Hub.
* [Parametry połączenia dla IoT Edge urządzenia](../iot-edge/how-to-register-device.md?view=iotedge-2020-11&preserve-view=true#view-registered-devices-and-retrieve-connection-strings).

## <a name="prepare-a-device"></a>Przygotowywanie urządzenia
### <a name="using-the-automated-deploy-to-azure-button"></a>Korzystanie z przycisku Automatyczne wdrażanie na platformie Azure

Dla wygody w tym samouczku zostanie użyty [szablon Azure Resource Manager](../azure-resource-manager/templates/overview.md) oparty na usłudze [Cloud-init](../virtual-machines/linux/using-cloud-init.md), który pomoże szybko skonfigurować maszynę wirtualną z systemem Ubuntu 18,04 LTS. Instaluje zarówno środowisko uruchomieniowe Azure IoT Edge, jak i agenta pakietu aktualizacji urządzeń, a następnie automatycznie konfiguruje urządzenie z informacjami o aprowizacji przy użyciu parametrów połączenia urządzenia IoT Edge dla podanego wymagania wstępnego. Pozwala to uniknąć konieczności uruchomienia sesji SSH w celu ukończenia instalacji.

1. Aby rozpocząć, kliknij poniższy przycisk:

   [![Przycisk Wdrażanie na platformie Azure dla szablonu iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2F1.2.0-rc4%2FedgeDeploy.json)

1. W nowo uruchomionym oknie Wypełnij pola dostępne formularza:

    > [!div class="mx-imgBorder"]
    > [![Zrzut ekranu przedstawiający szablon iotedge-vm-deploy](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Subskrypcja**: aktywna subskrypcja platformy Azure, w której ma zostać wdrożona maszyna wirtualna.

    **Grupa zasobów**: istniejąca lub nowo utworzona grupa zasobów zawierająca maszynę wirtualną i skojarzone z nią zasoby.

    **Prefiks etykiety DNS**: wymagana wartość wyboru, która jest używana do prefiksu nazwy hosta maszyny wirtualnej.

    **Nazwa użytkownika administratora**: Nazwa użytkownika, która będzie mieć uprawnienia root dotyczące wdrożenia.

    **Parametry połączenia urządzenia**: [Parametry połączenia urządzenia](../iot-edge/how-to-register-device.md) , które zostały utworzone w ramach zamierzonego [IoT Hub](../iot-hub/about-iot-hub.md).

    **Rozmiar maszyny** wirtualnej: [rozmiar](../cloud-services/cloud-services-sizes-specs.md) maszyn wirtualnych do wdrożenia

    **Ubuntu wersja systemu operacyjnego**: wersja systemu operacyjnego Ubuntu, która ma zostać zainstalowana na podstawowej maszynie wirtualnej. Pozostaw wartość domyślną bez zmian, ponieważ zostanie ona ustawiona na Ubuntu 18,04-LTS.

    **Lokalizacja**: [region geograficzny](https://azure.microsoft.com/global-infrastructure/locations/) , w którym ma zostać wdrożona maszyna wirtualna, wartość domyślna to lokalizacja wybranej grupy zasobów.

    **Typ uwierzytelniania**: Wybierz **sshPublicKey** lub **hasło** w zależności od preferencji użytkownika.

    **Hasło administratora lub klucz**: wartość klucza publicznego SSH lub wartość hasła w zależności od wybranego typu uwierzytelniania.

    Gdy wypełniono wszystkie pola, zaznacz pole wyboru u dołu strony, aby zaakceptować warunki i wybierz pozycję **Kup** , aby rozpocząć wdrażanie.

1. Sprawdź, czy wdrożenie zakończyło się pomyślnie. Poczekaj kilka minut po zakończeniu wdrożenia w celu zakończenia instalacji i konfiguracji, aby zakończyć instalowanie IoT Edge i agenta aktualizacji pakietu urządzeń.

   Zasób maszyny wirtualnej powinien zostać wdrożony w wybranej grupie zasobów.  Zanotuj nazwę maszyny, która powinna mieć format `vm-0000000000000` . Zanotuj również powiązaną **nazwę DNS**, która powinna być w formacie `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    **Nazwa DNS** znajduje się w sekcji **Przegląd** nowo wdrożonej maszyny wirtualnej w witrynie Azure Portal.

    > [!div class="mx-imgBorder"]
    > [![Zrzut ekranu przedstawiający nazwę DNS maszyny wirtualnej iotedge](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

   > [!TIP]
   > Aby SSH do tej maszyny wirtualnej po zakończeniu instalacji, należy użyć skojarzonej **nazwy DNS** z poleceniem: `ssh <adminUsername>@<DNS_Name>`

### <a name="optional-manually-prepare-a-device"></a>Obowiązkowe Ręczne przygotowanie urządzenia
Poniższe kroki ręcznego instalacji i konfiguracji urządzenia są równoważne z tymi, które były zautomatyzowane przez ten skrypt w [chmurze](https://github.com/Azure/iotedge-vm-deploy/blob/1.2.0-rc4/cloud-init.txt). Mogą służyć do przygotowania urządzenia fizycznego.

1. Postępuj zgodnie z instrukcjami, aby [zainstalować środowisko uruchomieniowe Azure IoT Edge](../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true).
   > [!NOTE]
   > Agent pakietu aktualizacji urządzenia nie jest zależny od IoT Edge. Jest to jednak zależne od demona usługi IoT Identity, która jest instalowana z programem IoT Edge (1.2.0 lub nowszym) w celu uzyskania tożsamości i nawiązania połączenia z IoT Hub.
   >
   > Chociaż nie zostały omówione w tym samouczku, [demona usługi IoT Identity może być instalowana autonomicznie na urządzeniach IoT opartych](https://azure.github.io/iot-identity-service/packaging.html)na systemie Linux. Sekwencja instalacji. _Po_ usłudze tożsamości IoT należy zainstalować agenta pakietu aktualizacji urządzenia. W przeciwnym razie Agent pakietu nie będzie zarejestrowany jako składnik autoryzowany do nawiązywania połączenia z IoT Hub.

1. Następnie zainstaluj pakiet Updates Agent. deb.

   ```bash
   sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt 
   ```

Aktualizacja urządzenia dla pakietów oprogramowania platformy Azure IoT Hub podlega następującym postanowieniom licencyjnym:
  * [Aktualizacja urządzenia z licencją IoT Hub](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
  * [Licencja klienta optymalizacji dostarczania](https://github.com/microsoft/do-client/blob/main/LICENSE)

Przeczytaj postanowienia licencyjne przed użyciem pakietu. Instalacja i użycie pakietu oznacza akceptację tych warunków. Jeśli nie akceptujesz postanowień licencyjnych, nie używaj tego pakietu.

## <a name="add-a-tag-to-your-device"></a>Dodawanie tagu do urządzenia

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do IoT Hub.

2. W obszarze "IoT Edge" w okienku nawigacji po lewej stronie Znajdź urządzenie IoT Edge i przejdź do sznurka urządzenia.

3. W obszarze urządzenia Usuń wszystkie istniejące wartości tagu aktualizacji urządzenia, ustawiając je na wartość null.

4. Dodaj nową wartość znacznika aktualizacji urządzenia, jak pokazano poniżej.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            },
```

## <a name="import-update"></a>Importuj aktualizację

1. Przejdź do pozycji [aktualizacje urządzeń](https://github.com/Azure/iot-hub-device-update/releases) w usłudze GitHub i kliknij listę rozwijaną "zasoby".

3. Pobierz `apt-update-import-samples.zip` plik, klikając go.

5. Wyodrębnij zawartość folderu w celu odnalezienia różnych próbek aktualizacji i odpowiednich manifestów importu. 

2. W Azure Portal wybierz opcję Aktualizacje urządzenia w obszarze Automatyczne zarządzanie urządzeniami z paska nawigacyjnego po lewej stronie w IoT Hub.

3. Wybierz kartę aktualizacje.

4. Wybierz pozycję "+ Importuj nową aktualizację".

5. Zaznacz ikonę folderu lub pole tekstowe w obszarze "Wybierz plik manifestu importowania". Zostanie wyświetlone okno dialogowe selektora plików. Wybierz `sample-package-update-1.0.1-importManifest.json` manifest importu z pobranego wcześniej folderu. Następnie wybierz ikonę folderu lub pole tekstowe w obszarze "Wybierz jeden lub więcej plików aktualizacji". Zostanie wyświetlone okno dialogowe selektora plików. Wybierz `sample-1.0.1-libcurl4-doc-apt-manifest.json` plik aktualizacji manifestu apt z folderu pobranego wcześniej.
Ta aktualizacja zainstaluje najnowszą dostępną wersję programu `libcurl4-doc package` na urządzeniu.

   Alternatywnie można wybrać `sample-package-update-2-2.0.1-importManifest.json` plik manifestu i plik `sample-2.0.1-libcurl4-doc-7.58-apt-manifest.json` aktualizacji manifestu apt z folderu pobranego wcześniej. Spowoduje to zainstalowanie określonej wersji v 7.58.0 `libcurl4-doc package` urządzenia na urządzeniu.

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

1. Wybierz opcję Aktualizacje urządzenia w obszarze Automatyczne zarządzanie urządzeniami na pasku nawigacyjnym po lewej stronie.

1. Wybierz kartę grupy w górnej części strony.

1. Wybierz przycisk Dodaj, aby utworzyć nową grupę.

1. Wybierz z listy tag IoT Hub utworzony w poprzednim kroku. Wybierz pozycję Utwórz grupę aktualizacji.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Zrzut ekranu przedstawiający wybór znacznika." lightbox="media/create-update-group/select-tag.PNG":::

[Dowiedz się więcej](create-update-group.md) na temat dodawania tagów i tworzenia grup aktualizacji

## <a name="deploy-update"></a>Wdróż aktualizację

1. Po utworzeniu grupy powinna zostać wyświetlona nowa aktualizacja dla grupy urządzeń z linkiem do aktualizacji w kolumnie _dostępne aktualizacje_ . Może być konieczne odświeżenie jednego z nich.

1. Kliknij link do dostępnej aktualizacji.

1. Upewnij się, że wybrano poprawną grupę jako grupę docelową i Zaplanuj wdrożenie

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Wybierz aktualizację" lightbox="media/deploy-update/select-update.png":::

   > [!TIP]
   > Domyślnie Data/godzina rozpoczęcia to 24 godziny od bieżącego czasu. Pamiętaj, aby wybrać inną datę i godzinę, jeśli chcesz, aby wdrożenie zostało rozpoczęte wcześniej.

1. Wybierz pozycję Wdróż aktualizację.

1. Wyświetl wykres zgodności. Powinna zostać wyświetlona aktualizacja, która jest teraz w toku. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Aktualizacja w toku" lightbox="media/deploy-update/update-in-progress.png":::

1. Po pomyślnym zaktualizowaniu urządzenia powinna zostać wyświetlona aktualizacja wykresu zgodności i szczegółów wdrożenia w celu odzwierciedlenia tego samego. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Aktualizacja powiodła się" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorowanie wdrożenia aktualizacji

1. Wybierz kartę wdrożenia w górnej części strony.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Karta wdrożenia" lightbox="media/deploy-update/deployments-tab.png":::

1. Wybierz utworzone wdrożenie, aby wyświetlić szczegóły wdrożenia.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Szczegóły wdrożenia" lightbox="media/deploy-update/deployment-details.png":::

1. Wybierz pozycję Odśwież, aby wyświetlić najnowsze Szczegóły stanu. Kontynuuj ten proces do momentu, aż stan zmieni się na powodzenie.

Ukończono pomyślnie kompleksową aktualizację pakietu przy użyciu aktualizacji urządzenia dla IoT Hub na urządzeniu z systemem Ubuntu Server 18,04 x64. 

## <a name="bonus-steps"></a>Dodatkowe kroki

1. Powtórz sekcje "Importuj aktualizację" i "wdróż aktualizację"

3. W kroku "Importowanie aktualizacji" Wybierz `sample-package-update-1.0.2-importManifest.json` plik manifestu i pliku `sample-1.0.2-libcurl4-doc-remove-apt-manifest.json` aktualizacji manifestu apt z folderu pobranego wcześniej. Ta aktualizacja spowoduje usunięcie instalacji `libcurl4-doc package` z urządzenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy maszyna wirtualna została utworzona za pomocą przycisku Wdróż na platformie Azure nie jest już potrzebne, Wyczyść konto aktualizacji urządzenia, jego wystąpienie IoT Hub i IoT Edge. Możesz to zrobić, przechodząc do poszczególnych zasobów i wybierając pozycję "Usuń". Należy pamiętać, że przed oczyszczeniem konta aktualizacji urządzenia należy oczyścić wystąpienie aktualizacji urządzenia.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aktualizacja obrazu na Raspberry Pi 3 B + samouczek](device-update-raspberry-pi.md)
