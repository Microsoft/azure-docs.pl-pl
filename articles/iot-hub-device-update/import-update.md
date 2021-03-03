---
title: Jak zaimportować nową aktualizację | Microsoft Docs
description: Przewodnik How-To dotyczący importowania nowej aktualizacji do IoT Hub aktualizacji urządzenia IoT Hub.
author: andbrown
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: d8757f3076f784576f95bbdfc30abf578446c776
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663290"
---
# <a name="import-new-update"></a>Importuj nową aktualizację
Dowiedz się, jak zaimportować nową aktualizację do aktualizacji urządzenia dla IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne

* [Dostęp do IoT Hub z aktualizacją urządzenia dla IoT Hub włączona](create-device-update-account.md). Zalecane jest użycie warstwy S1 (standardowa) lub wyższej dla IoT Hub. 
* Urządzenie IoT (lub symulator) obsługiwane dla aktualizacji urządzenia w ramach IoT Hub.
   * W przypadku korzystania z rzeczywistego urządzenia należy zaktualizować plik obrazu aktualizacji obrazu lub [plik manifestu apt](device-update-apt-manifest.md) na potrzeby aktualizacji pakietu.
* Program [PowerShell 5](https://docs.microsoft.com/powershell/scripting/install/installing-powershell) lub nowszy.
* Obsługiwane przeglądarki:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Niektóre dane przesłane do tej usługi mogą być przetwarzane w regionie poza regionem, w którym utworzono to wystąpienie.

## <a name="create-device-update-import-manifest"></a>Tworzenie manifestu importowania aktualizacji urządzeń

1. Upewnij się, że plik obrazu aktualizacji lub plik manifestu APT znajduje się w katalogu dostępnym z programu PowerShell.

2. Sklonuj [aktualizację urządzenia dla repozytorium IoT Hub](https://github.com/azure/iot-hub-device-update)lub Pobierz ją jako plik. zip do lokalizacji dostępnej w programie PowerShell (po pobraniu pliku zip kliknij prawym przyciskiem myszy, `Properties`  >  `General` > aby sprawdzić, czy w sekcji znajduje się okno `Unblock` dialogowe, `Security` Aby uniknąć wyświetlania ostrzeżeń dotyczących zabezpieczeń programu PowerShell).

3. W programie PowerShell przejdź do `tools/AduCmdlets` katalogu i uruchom polecenie:

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Uruchom następujące polecenia, zastępując przykładowe wartości parametrów w celu wygenerowania manifestu importu, plik JSON opisujący aktualizację:
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    Aby uzyskać krótkie informacje, poniżej przedstawiono kilka przykładowych wartości dla powyższych parametrów. Pełną dokumentację można znaleźć w sekcji Schemat manifestu pełnego importu poniżej.

    | Parametr | Opis |
    | --------- | ----------- |
    | deviceManufacturer | Producent urządzenia, z którym jest zgodna aktualizacja, na przykład contoso
    | deviceModel | Model urządzenia, z którym jest zgodna aktualizacja, na przykład wyskakujące powiadomienia
    | updateProvider | Składnik dostawcy aktualizacji tożsamości, na przykład Fabrikam
    | aktualizacjaname | Część nazwy tożsamości aktualizacji, na przykład ImageUpdate
    | updateVersion | Aktualizacja wersji, na przykład 2,0
    | Typ aktualizacji | <ul><li>Określ `microsoft/swupdate:1` dla aktualizacji obrazu</li><li>Określ `microsoft/apt:1` dla aktualizacji pakietu</li></ul>
    | installedCriteria | <ul><li>Określ wartość SWVersion dla `microsoft/swupdate:1` typu aktualizacji</li><li>Określ zalecaną wartość dla `microsoft/apt:1` typu aktualizacji.
    | updateFilePath | Ścieżka do plików aktualizacji na komputerze

    Schemat manifestu pełnego importu

    | Nazwa | Typ | Opis | Ograniczenia |
    | --------- | --------- | --------- | --------- |
    | UpdateId | `UpdateId` Stream | Aktualizowanie tożsamości. |
    | Typ aktualizacji | ciąg | Typ aktualizacji: <ul><li>Określ `microsoft/apt:1` , kiedy ma być wykonywana Aktualizacja oparta na pakiecie przy użyciu agenta odwołań.</li><li>Określ `microsoft/swupdate:1` podczas przeprowadzania aktualizacji opartej na obrazie przy użyciu agenta odwołań.</li><li>Określ, kiedy ma być `microsoft/simulator:1` używany przykładowy symulator agentów.</li><li>Określ typ niestandardowy w przypadku tworzenia niestandardowego agenta.</li></ul> | <ul><li>Formatowanie `{provider}/{type}:{typeVersion}`</li><li>Łącznie z 32 znaków</li></ul> |
    | InstalledCriteria | ciąg | Ciąg interpretowany przez agenta w celu ustalenia, czy aktualizacja została zastosowana pomyślnie:  <ul><li>Określ **wartość** SWVersion dla typu aktualizacji `microsoft/swupdate:1` .</li><li>Określ `{name}-{version}` dla typu aktualizacji `microsoft/apt:1` , która nazwa i wersja są uzyskiwane z pliku apt.</li><li>Określ skrót pliku aktualizacji dla typu aktualizacji `microsoft/simulator:1` .</li><li>Określ niestandardowy ciąg w przypadku tworzenia niestandardowego agenta.</li></ul> | Maksymalnie 64 znaków |
    | Zgodność | Tablica `CompatibilityInfo` obiektów | Informacje o zgodności urządzenia zgodne z tą aktualizacją. | Maksymalnie 10 elementów |
    | CreatedDateTime | Data/godzina | Data i godzina utworzenia aktualizacji. | Rozdzielany format daty i godziny ISO 8601, w formacie UTC |
    | ManifestVersion | ciąg | Importuj wersję schematu manifestu. Określ `2.0` , która będzie zgodna z `urn:azureiot:AzureDeviceUpdateCore:1` interfejsem i `urn:azureiot:AzureDeviceUpdateCore:4` interfejsem.</li></ul> | Musi być `2.0` |
    | Pliki | Tablica `File` obiektów | Aktualizowanie plików ładunku | Maksymalnie 5 plików |

Uwaga: wszystkie pola są wymagane.

## <a name="review-generated-import-manifest"></a>Przejrzyj wygenerowany manifest importu

Przykład:
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-update"></a>Importuj aktualizację

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do IoT Hub za pomocą aktualizacji urządzenia.

2. Po lewej stronie strony wybierz pozycję "aktualizacje urządzeń" w obszarze "Automatyczne zarządzanie urządzeniami".

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Importuj aktualizacje" lightbox="media/import-update/import-updates-3.png":::

3. Zobaczysz kilka kart w górnej części ekranu. Wybierz kartę aktualizacje.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Aktualizacje" lightbox="media/import-update/updates-tab.png":::

4. Wybierz pozycję "+ Importuj nową aktualizację" poniżej nagłówka "gotowe do wdrożenia".

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Importuj nową aktualizację" lightbox="media/import-update/import-new-update-2.png":::

5. Zaznacz ikonę folderu lub pole tekstowe w obszarze "Wybierz plik manifestu importowania". Zostanie wyświetlone okno dialogowe selektora plików. Wybierz manifest importu, który został utworzony wcześniej przy użyciu polecenia cmdlet programu PowerShell. Następnie wybierz ikonę folderu lub pole tekstowe w obszarze "Wybierz jeden lub więcej plików aktualizacji". Zostanie wyświetlone okno dialogowe selektora plików. Wybierz pliki aktualizacji.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Wybierz pliki aktualizacji" lightbox="media/import-update/select-update-files.png":::

6. Zaznacz ikonę folderu lub pole tekstowe w obszarze "Wybieranie kontenera magazynu". Następnie wybierz odpowiednie konto magazynu. Kontener magazynu jest używany do tymczasowego przygotowywania plików aktualizacji.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Konto magazynu" lightbox="media/import-update/storage-account.png":::

7. Jeśli kontener został już utworzony, możesz go użyć ponownie. (W przeciwnym razie wybierz pozycję "+ kontener", aby utworzyć nowy kontener magazynu dla aktualizacji).  Wybierz kontener, którego chcesz użyć, a następnie kliknij przycisk "Wybierz".

   :::image type="content" source="media/import-update/container.png" alt-text="Wybieranie kontenera" lightbox="media/import-update/container.png":::

8. Wybierz pozycję "Prześlij", aby rozpocząć proces importowania.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Publikuj aktualizację" lightbox="media/import-update/publish-update.png":::

9. Rozpocznie się proces importowania, a ekran przechodzi do sekcji "Historia importowania". Wybierz pozycję "Odśwież", aby wyświetlić postęp do momentu zakończenia procesu importowania (w zależności od rozmiaru aktualizacji, co może zakończyć się w ciągu kilku minut, ale może trwać dłużej).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Aktualizowanie sekwencjonowania importu" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Gdy kolumna stan wskazuje, że importowanie zakończyło się pomyślnie, wybierz nagłówek "gotowe do wdrożenia". Zaimportowana aktualizacja powinna zostać wyświetlona na liście teraz.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="Stan zadania" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Następne kroki

[Tworzenie grup](create-update-group.md)

[Poznaj pojęcia dotyczące importowania](import-concepts.md)
