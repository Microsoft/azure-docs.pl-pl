---
title: Samouczek dotyczący aktualizacji urządzenia dla platformy Azure IoT Hub przy użyciu agenta referencyjnego symulatora Ubuntu (18,04 x64) | Microsoft Docs
description: Rozpocznij pracę z aktualizacją urządzenia dla platformy Azure IoT Hub przy użyciu agenta referencyjnego symulatora Ubuntu (18,04 x64).
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 90e72bd12d9115e5ff95213428ae4ac37979dcf3
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120099"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-1804-x64-simulator-reference-agent"></a>Samouczek dotyczący aktualizacji urządzeń z platformą Azure IoT Hub przy użyciu agenta referencyjnego symulatora Ubuntu (18,04 x64)

Aktualizacja urządzenia dla IoT Hub obsługuje dwie formy aktualizacji — oparte na obrazach i pakietach.

Aktualizacje obrazów zapewniają wyższy poziom zaufania w stanie końcowym urządzenia. Zwykle łatwiej jest replikować wyniki aktualizacji obrazu między środowiskiem przedprodukcyjnym i środowiskiem produkcyjnym, ponieważ nie ma ona takich samych wyzwań jak pakiety i ich zależności. Ze względu na ich charakter niepodzielny, jeden może również łatwo przyjmować model trybu failover A/B.

Ten samouczek przeprowadzi Cię przez kroki umożliwiające ukończenie kompleksowej aktualizacji opartej na obrazie przy użyciu aktualizacji urządzenia dla IoT Hub. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Pobierz i zainstaluj obraz
> * Dodawanie tagu do urządzenia IoT
> * Importowanie aktualizacji
> * Tworzenie grupy urządzeń
> * Wdróż aktualizację obrazu
> * Monitorowanie wdrożenia aktualizacji

## <a name="prerequisites"></a>Wymagania wstępne
* Jeśli jeszcze tego nie zrobiono, Utwórz [konto i wystąpienie aktualizacji urządzenia](create-device-update-account.md), w tym Konfigurowanie IoT Hub.

### <a name="download-and-install"></a>Pobieranie i instalowanie

* Polecenia cmdlet AZ (Azure CLI) dla programu PowerShell:
  * Otwórz program PowerShell > Zainstaluj interfejs wiersza polecenia platformy Azure ("Y" w celu uzyskania wskazówek dotyczących instalacji z "niezaufanego" źródła)

```powershell
PS> Install-Module Az -Scope CurrentUser
```

### <a name="enable-wsl-on-your-windows-device-windows-subsystem-for-linux"></a>Włączanie usługi WSL na urządzeniu z systemem Windows (podsystem Windows dla systemu Linux)

1. Otwórz program PowerShell jako administrator na komputerze i uruchom następujące polecenie (może zostać wyświetlony monit o ponowne uruchomienie po każdym kroku; Uruchom ponownie po wyświetleniu monitu):

```powershell
PS> Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
PS> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

(*Może zostać wyświetlony monit o ponowne uruchomienie po wykonaniu tego kroku*)

2. Przejdź do Microsoft Store w sieci Web i zainstaluj program [Ubuntu 18,04 LTS](https://www.microsoft.com/p/ubuntu-1804-lts/9n9tngvndl3q?activetab=pivot:overviewtab`).

3. Uruchom "Ubuntu 18,04 LTS" i zainstaluj.

4. Po zainstalowaniu programu zostanie wyświetlony monit o ustawienie nazwy głównej (username) i hasła. Pamiętaj, aby użyć zapomnianego hasła nazwy głównej.

5. W programie PowerShell uruchom następujące polecenie, aby ustawić Ubuntu jako domyślną dystrybucję systemu Linux:

```powershell
PS> wsl --setdefault Ubuntu-18.04
```

6. Aby wyświetlić listę wszystkich dystrybucji systemu Linux, upewnij się, że Ubuntu jest wartością domyślną.

```powershell
PS> wsl --list
```

7. Powinien być widoczny: **Ubuntu-18,04 (wartość domyślna)**

## <a name="download-device-update-ubuntu-1804-x64-simulator-reference-agent"></a>Pobierz aktualizację urządzenia Ubuntu (18,04 x64) Agent referencyjny symulatora

Obraz aktualizacji Ubuntu Update można pobrać z sekcji *Assets* (informacje o wersji) [tutaj](https://github.com/Azure/iot-hub-device-update/releases).

Istnieją dwie wersje agenta. Jeśli wykonujesz Scenariusz oparty na obrazie, użyj AducIotAgentSim-Microsoft-SWUpdate i jeśli wykonujesz Scenariusz oparty na pakiecie, użyj AducIotAgentSim-Microsoft-apt.

## <a name="install-device-update-agent-simulator"></a>Instalowanie symulatora agenta aktualizacji urządzeń

1. Uruchom Ubuntu WSL i wprowadź następujące polecenie (należy zauważyć, że dodatkowe miejsce i kropka na końcu).

  ```shell
  explorer.exe .
  ```

2. Skopiuj AducIotAgentSim-Microsoft-SWUpdate (lub AducIotAgentSim-Microsoft-APT) z folderu lokalnego, w którym został on pobrany w/mnt do folderu macierzystego w WSL.

3. Uruchom następujące polecenie, aby wprowadzić pliki binarne.

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-swupdate
  ```

  lub

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-apt
  ```
Aktualizacja urządzenia dla platformy Azure IoT Hub podlega następującym postanowieniom licencyjnym:
   * [Aktualizacja urządzenia z licencją IoT Hub](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licencja klienta optymalizacji dostarczania](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
Przeczytaj postanowienia licencyjne przed użyciem agenta. Twoja instalacja i użycie stanowią zatwierdzenie tych warunków. Jeśli nie akceptujesz postanowień licencyjnych, nie należy używać aktualizacji urządzenia dla IoT Hub agenta.

## <a name="add-device-to-azure-iot-hub"></a>Dodawanie urządzenia do usługi Azure IoT Hub

Po uruchomieniu agenta aktualizacji urządzenia na urządzeniu IoT należy dodać urządzenie do IoT Hub platformy Azure.  W ramach usługi Azure IoT Hub parametry połączenia będą generowane dla określonego urządzenia.

1. Na Azure Portal Uruchom IoT Hub aktualizacji urządzenia.
2. Utwórz nowe urządzenie.
3. Po lewej stronie strony przejdź do obszaru "Explorers" > "IoT Devices" > wybierz pozycję "New" ("nowe").
4. Podaj nazwę urządzenia w obszarze "Identyfikator urządzenia" — Upewnij się, że pole wyboru "Automatyczne generowanie kluczy" jest zaznaczone.
5. Wybierz pozycję "Zapisz".
6. Teraz nastąpi powrót do strony "Devices", a utworzone urządzenie powinno znajdować się na liście. Wybierz to urządzenie.
7. W widoku urządzenia wybierz ikonę "Kopiuj" obok pozycji "podstawowe parametry połączenia".
8. Wklej skopiowane znaki z lokalizacji do późniejszego użycia w poniższych krokach. **Ten skopiowany ciąg jest parametrami połączenia urządzenia**.

## <a name="add-connection-string-to-simulator"></a>Dodaj parametry połączenia do symulatora

Uruchom agenta aktualizacji urządzenia na nowym urządzeniu oprogramowania.

1. Uruchom Ubuntu.
2. Uruchom agenta aktualizacji urządzenia i określ parametry połączenia urządzenia z poprzedniej sekcji opakowane apostrofami:

Zamień `<device connection string>` na parametry połączenia
```shell
./AducIotAgentSim-microsoft-swupdate -c '<device connection string>'
```

lub

```shell
./AducIotAgentSim-microsoft-apt -c '<device connection string>'
```

3. Przewiń w górę i wyszukaj ciąg wskazujący, że urządzenie jest w stanie bezczynności. Stan "bezczynne" oznacza, że urządzenie jest gotowe do obsługi poleceń usługi:

```markdown
Agent running. [main]
```

## <a name="add-a-tag-to-your-device"></a>Dodawanie tagu do urządzenia

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do IoT Hub.

2. W okienku nawigacji po lewej stronie "IoT Devices" lub "IoT Edge" Znajdź urządzenie IoT i przejdź do sznurka urządzenia albo sznurka modułu.

3. W obszarze sznurek modułu agenta aktualizacji urządzenia Usuń wszystkie istniejące wartości tagu aktualizacji urządzenia, ustawiając je na wartość null. Jeśli używasz tożsamości urządzenia z agentem aktualizacji urządzeń, wprowadź te zmiany na sznurze urządzeń.

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

5. Zaznacz ikonę folderu lub pole tekstowe w obszarze "Wybierz plik manifestu importowania". Zostanie wyświetlone okno dialogowe selektora plików. Wybierz utworzony powyżej manifest importu.  Następnie wybierz ikonę folderu lub pole tekstowe w obszarze "Wybierz jeden lub więcej plików aktualizacji". Zostanie wyświetlone okno dialogowe selektora plików. Wybierz pobrany wcześniej obraz Ubuntu Update. 

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

Ukończono pomyślnie kompleksową aktualizację obrazu przy użyciu aktualizacji urządzenia dla IoT Hub przy użyciu agenta referencyjnego symulatora Ubuntu (18,04 x64).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, Oczyść konto, wystąpienie, IoT Hub i urządzenie IoT aktualizacji urządzenia. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów](troubleshoot-device-update.md)

