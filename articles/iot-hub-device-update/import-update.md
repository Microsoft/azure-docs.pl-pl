---
title: Jak zaimportować nową aktualizację | Microsoft Docs
description: Przewodnik How-To dotyczący importowania nowej aktualizacji do IoT Hub aktualizacji urządzenia IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: ede0d279b8769f49afcdae1cb9352c1b47fb59b5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932407"
---
# <a name="import-new-update"></a>Importuj nową aktualizację
Dowiedz się, jak zaimportować nową aktualizację do aktualizacji urządzenia dla IoT Hub. Jeśli jeszcze tego nie zrobiono, pamiętaj o zapoznaniu się z podstawowymi [pojęciami dotyczącymi importu](import-concepts.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Dostęp do IoT Hub z aktualizacją urządzenia dla IoT Hub włączona](create-device-update-account.md). 
* Urządzenie IoT (lub symulator) obsługiwane dla aktualizacji urządzenia w ramach IoT Hub.
   * W przypadku korzystania z rzeczywistego urządzenia należy zaktualizować plik obrazu aktualizacji obrazu lub [plik manifestu apt](device-update-apt-manifest.md) na potrzeby aktualizacji pakietu.
* Program [PowerShell 5](/powershell/scripting/install/installing-powershell) lub nowszy (w tym instalacje systemu Linux, MacOS i Windows)
* Obsługiwane przeglądarki:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Niektóre dane przesłane do tej usługi mogą być przetwarzane w regionie poza regionem, w którym utworzono to wystąpienie.

## <a name="create-device-update-import-manifest"></a>Tworzenie manifestu importowania aktualizacji urządzeń

1. Upewnij się, że plik obrazu aktualizacji lub plik manifestu APT znajduje się w katalogu dostępnym z programu PowerShell.

2. Utwórz plik tekstowy o nazwie **AduUpdate. PSM1** w katalogu, w którym znajduje się plik obrazu aktualizacji lub plik manifestu apt. Następnie otwórz polecenie cmdlet programu PowerShell [AduUpdate. PSM1](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) , skopiuj zawartość do pliku tekstowego, a następnie Zapisz plik tekstowy.

3. W programie PowerShell przejdź do katalogu, w którym utworzono polecenie cmdlet programu PowerShell w kroku 2. Użyj opcji Kopiuj poniżej, a następnie wklej do programu PowerShell, aby uruchomić polecenia:

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

    Aby uzyskać krótkie informacje, poniżej przedstawiono kilka przykładowych wartości dla powyższych parametrów. Możesz również wyświetlić kompletny [Schemat manifestu importowania](import-schema.md) , aby uzyskać więcej szczegółów.

    | Parametr | Opis |
    | --------- | ----------- |
    | deviceManufacturer | Producent urządzenia, z którym jest zgodna aktualizacja, na przykład contoso. Musi być zgodna z [właściwością urządzenia](./device-update-plug-and-play.md#device-properties) _producenta_ .
    | deviceModel | Model urządzenia, z którym jest zgodna aktualizacja, na przykład wyskakujące powiadomienia. Musi być zgodna z [właściwością urządzenia](./device-update-plug-and-play.md#device-properties) _modelu_ .
    | updateProvider | Jednostka, która jest tworzona lub bezpośrednio odpowiedzialna za aktualizację. Często będzie to nazwa firmy.
    | aktualizacjaname | Identyfikator klasy aktualizacji. Klasa może być dowolna. Często będzie to nazwa urządzenia lub modelu.
    | updateVersion | Numer wersji odróżniający tę aktualizację od innych, które mają tego samego dostawcę i nazwę. Nie ma dopasowania do wersji pojedynczego składnika oprogramowania na urządzeniu (ale można go wybrać).
    | Typ aktualizacji | <ul><li>Określ `microsoft/swupdate:1` dla aktualizacji obrazu</li><li>Określ `microsoft/apt:1` dla aktualizacji pakietu</li></ul>
    | installedCriteria | <ul><li>Określ wartość SWVersion dla `microsoft/swupdate:1` typu aktualizacji</li><li>Określ nazwę **-Version**, gdzie _name_ to nazwa manifestu apt, a _wersja_ to wersja manifestu apt. Na przykład contoso-IoT-Edge-1.0.0.0.
    | updateFilePath | Ścieżka do plików aktualizacji na komputerze


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

[!NOTE]
Poniższe instrukcje przedstawiają sposób importowania aktualizacji za pomocą interfejsu użytkownika Azure Portal. Do zaimportowania aktualizacji można także użyć [aktualizacji urządzenia dla IoT Hub interfejsów API](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) . 

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