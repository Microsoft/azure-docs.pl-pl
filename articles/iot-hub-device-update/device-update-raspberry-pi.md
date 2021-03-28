---
title: Samouczek dotyczący aktualizacji urządzeń dla platformy Azure IoT Hub przy użyciu obrazu Raspberry Pi 3 B + Reference yocto | Microsoft Docs
description: Rozpocznij pracę z aktualizacją urządzenia dla platformy Azure IoT Hub przy użyciu obrazu Raspberry Pi 3 B + Reference yocto.
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: b6a9fa3ac85460a46653c171198a2dfea8580f3a
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644485"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-raspberry-pi-3-b-reference-image"></a>Samouczek dotyczący aktualizacji urządzeń z platformą Azure IoT Hub przy użyciu obrazu referencyjnego Raspberry Pi 3 B +

Aktualizacja urządzenia dla IoT Hub obsługuje dwie formy aktualizacji — oparte na obrazach i pakietach.

Aktualizacje obrazów zapewniają wyższy poziom zaufania w stanie końcowym urządzenia. Zwykle łatwiej jest replikować wyniki aktualizacji obrazu między środowiskiem przedprodukcyjnym i środowiskiem produkcyjnym, ponieważ nie ma ona takich samych wyzwań jak pakiety i ich zależności. Ze względu na ich charakter niepodzielny, jeden może również łatwo przyjmować model trybu failover A/B.

Ten samouczek przeprowadzi Cię przez kroki umożliwiające ukończenie kompleksowej aktualizacji opartej na obrazie przy użyciu aktualizacji urządzenia dla IoT Hub. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Pobierz obraz
> * Dodawanie tagu do urządzenia IoT
> * Importowanie aktualizacji
> * Tworzenie grupy urządzeń
> * Wdróż aktualizację obrazu
> * Monitorowanie wdrożenia aktualizacji

## <a name="prerequisites"></a>Wymagania wstępne
* Jeśli jeszcze tego nie zrobiono, Utwórz [konto i wystąpienie aktualizacji urządzenia](create-device-update-account.md), w tym Konfigurowanie IoT Hub.

## <a name="download-image"></a>Pobierz obraz

Istnieją trzy obrazy dostępne jako część "zasobów" w ramach danej [aktualizacji usługi GitHub](https://github.com/Azure/iot-hub-device-update/releases). Obraz podstawowy (adu-Base-Image) i jeden obraz aktualizacji (adu-Update-Image) są udostępniane, dzięki czemu można wypróbować wdrożenia do różnych wersji bez konieczności przeprowadzania na urządzeniu karty SD. W tym celu należy przekazać obrazy aktualizacji do aktualizacji urządzenia dla usługi IoT Hub w ramach importu.

## <a name="flash-sd-card-with-image"></a>Karta flash SD z obrazem

Korzystając z swojego ulubionego narzędzia do migania systemu operacyjnego, zainstaluj podstawowy obraz aktualizacji urządzenia (adu-Base-Image) na karcie SD, który będzie używany na urządzeniu Raspberry Pi 3 B + +.

### <a name="using-bmaptool-to-flash-sd-card"></a>Korzystanie z bmaptool do karty flash SD

1. Jeśli jeszcze tego nie zrobiono, zainstaluj `bmaptool` Narzędzie.

   ```shell
   sudo apt-get install bmap-tools
   ```

2. Znajdź ścieżkę dla karty SD w `/dev` . Ścieżka powinna wyglądać podobnie do czegoś `/dev/sd*` lub `/dev/mmcblk*` . Możesz użyć narzędzia, `dmesg` Aby pomóc znaleźć poprawną ścieżkę.

3. Przed miganiem należy odinstalować wszystkie zainstalowane partycje.

   ```shell
   sudo umount /dev/<device>
   ```

4. Upewnij się, że masz uprawnienia do zapisu na urządzeniu.

   ```shell
   sudo chmod a+rw /dev/<device>
   ```

5. Opcjonalny. W szybszym czasie Pobierz plik Bimap wraz z plikiem obrazu i umieść je w tym samym katalogu.

6. Przebłysk karty SD.

   ```shell
   sudo bmaptool copy <path to image> /dev/<device>
   ```
   
Aktualizacja urządzenia dla platformy Azure IoT Hub podlega następującym postanowieniom licencyjnym:
   * [Aktualizacja urządzenia z licencją IoT Hub](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licencja klienta optymalizacji dostarczania](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
Przeczytaj postanowienia licencyjne przed użyciem agenta. Twoja instalacja i użycie stanowią zatwierdzenie tych warunków. Jeśli nie akceptujesz postanowień licencyjnych, nie należy używać aktualizacji urządzenia dla IoT Hub agenta.

## <a name="create-device-in-iot-hub-and-get-connection-string"></a>Tworzenie urządzenia w IoT Hub i pobieranie parametrów połączenia

Teraz urządzenie należy dodać do IoT Hub platformy Azure.  W ramach usługi Azure IoT Hub dla urządzenia zostaną wygenerowane parametry połączenia.

1. Na Azure Portal Uruchom IoT Hub aktualizacji urządzenia.
2. Utwórz nowe urządzenie.
3. Po lewej stronie strony przejdź do obszaru "Explorers" > "IoT Devices" > wybierz pozycję "New" ("nowe").
4. Podaj nazwę urządzenia w obszarze "Identyfikator urządzenia" — Upewnij się, że pole wyboru "Automatyczne generowanie kluczy" jest zaznaczone.
5. Wybierz pozycję "Zapisz".
6. Teraz nastąpi powrót do strony "Devices", a utworzone urządzenie powinno znajdować się na liście. Wybierz to urządzenie.
7. W widoku urządzenia wybierz ikonę "Kopiuj" obok pozycji "podstawowe parametry połączenia".
8. Wklej skopiowane znaki z lokalizacji do późniejszego użycia w poniższych krokach.
   **Ten skopiowany ciąg jest parametrami połączenia urządzenia**.

## <a name="provision-connection-string-on-sd-card"></a>Udostępnianie parametrów połączenia na karcie SD

1. Upewnij się, że Raspberry PI3 jest połączony z siecią.
2. W programie PowerShell Użyj poniższego polecenia, aby SSH do urządzenia
   ```markdown
   ssh raspberrypi3 -l root
      ```
4. Wprowadź nazwę logowania jako "root", a hasło powinno pozostać puste.
5. Po pomyślnym przeprowadzeniu połączenia SSH z urządzeniem uruchom poniższe polecenia.
 
Zamień `<device connection string>` na parametry połączenia
 ```markdown
    echo "connection_string=<device connection string>" > adu-conf.txt  
    echo "aduc_manufacturer=ADUTeam" >> adu-conf.txt
    echo "aduc_model=RefDevice" >> adu-conf.txt
   ```

## <a name="connect-the-device-in-device-update-iot-hub"></a>Połącz urządzenie w IoT Hub aktualizacji urządzenia

1. Po lewej stronie strony wybierz pozycję "IoT Devices" w obszarze "Explorers".
2. Wybierz łącze z nazwą urządzenia.
3. W górnej części strony wybierz pozycję "sznurki urządzenia".
4. W sekcji "raportowane" właściwości przędzy urządzenia Znajdź wersję jądra systemu Linux.
Dla nowego urządzenia, które nie otrzymało aktualizacji z aktualizacji urządzenia, wartość [DeviceManagement: DeviceInformation: 1. swVersion](device-update-plug-and-play.md) będzie reprezentować wersję oprogramowania układowego działającą na urządzeniu.  Po zastosowaniu aktualizacji urządzenia aktualizacja urządzenia użyje wartości właściwości [AzureDeviceUpdateCore: ClientMetadata: 4. installedUpdateId](device-update-plug-and-play.md) , aby reprezentować wersję oprogramowania układowego działającą na urządzeniu.
5. Plik obrazu podstawowego i aktualizacji ma numer wersji w nazwie pliku.

   ```markdown
   adu-<image type>-image-<machine>-<version number>.<extension>
   ```

Użyj tego numeru wersji w kroku Importuj aktualizację poniżej.

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

1. Utwórz manifest importu, wykonując te [instrukcje](import-update.md).
2. Wybierz opcję Aktualizacje urządzenia w obszarze Automatyczne zarządzanie urządzeniami na pasku nawigacyjnym po lewej stronie.
3. Wybierz kartę aktualizacje.
4. Wybierz pozycję "+ Importuj nową aktualizację".
5. Zaznacz ikonę folderu lub pole tekstowe w obszarze "Wybierz plik manifestu importowania". Zostanie wyświetlone okno dialogowe selektora plików. Wybierz utworzony powyżej manifest importu.  Następnie wybierz ikonę folderu lub pole tekstowe w obszarze "Wybierz jeden lub więcej plików aktualizacji". Zostanie wyświetlone okno dialogowe selektora plików. Wybierz plik aktualizacji, który ma zostać wdrożony na urządzeniach IoT.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Zrzut ekranu przedstawiający wybór pliku aktualizacji." lightbox="media/import-update/select-update-files.png":::

5. Zaznacz ikonę folderu lub pole tekstowe w obszarze "Wybieranie kontenera magazynu". Następnie wybierz odpowiednie konto magazynu.

6. Jeśli kontener został już utworzony, możesz go użyć ponownie. (W przeciwnym razie wybierz pozycję "+ kontener", aby utworzyć nowy kontener magazynu dla aktualizacji).  Wybierz kontener, którego chcesz użyć, a następnie kliknij przycisk "Wybierz".
  
  :::image type="content" source="media/import-update/container.png" alt-text="Zrzut ekranu przedstawiający wybór kontenera." lightbox="media/import-update/container.png":::

7. Wybierz pozycję "Prześlij", aby rozpocząć proces importowania.

8. Rozpocznie się proces importowania, a ekran zostanie zmieniony na sekcję "Historia importowania". Wybierz pozycję "Odśwież", aby wyświetlić postęp do momentu zakończenia procesu importowania. W zależności od rozmiaru aktualizacji może to zakończyć się w ciągu kilku minut, ale może to potrwać dłużej.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Zrzut ekranu przedstawiający aktualizację sekwencji importu." lightbox="media/import-update/update-publishing-sequence-2.png":::

9. Gdy kolumna stan wskazuje, że importowanie zakończyło się pomyślnie, wybierz nagłówek "gotowe do wdrożenia". Zaimportowana aktualizacja powinna zostać wyświetlona na liście teraz.

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

Ukończono pomyślnie kompleksową aktualizację obrazu przy użyciu aktualizacji urządzenia dla IoT Hub na urządzeniu Raspberry Pi 3 B + +. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, Oczyść konto, wystąpienie, IoT Hub i urządzenie IoT aktualizacji urządzenia. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Agent referencyjny symulatora](device-update-simulator.md)
