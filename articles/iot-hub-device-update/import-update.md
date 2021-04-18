---
title: Jak zaimportować nowy | Microsoft Docs
description: How-To importowania nowej aktualizacji do usługi IoT Hub aktualizacji urządzenia na IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: d33c7f87d77f371d4eb4bf903e4d534f334e39c4
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599506"
---
# <a name="import-new-update"></a>Importowanie nowej aktualizacji
Dowiedz się, jak zaimportować nową aktualizację do usługi Device Update na IoT Hub. Jeśli jeszcze tego nie zrobiono, zapoznaj się z podstawowymi pojęciami [importowania.](import-concepts.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Istniejący plik aktualizacji, który chcesz wdrożyć na urządzeniach. Może to być plik obrazu do aktualizowania na podstawie obrazu lub plik [manifestu APT](device-update-apt-manifest.md) do aktualizowania opartego na pakietach. (Jak mogę[wybrać?](understand-device-update.md#support-for-a-wide-range-of-update-artifacts))
* [Dostęp do aplikacji IoT Hub z włączoną aktualizacją urządzenia IoT Hub .](create-device-update-account.md) 
* Urządzenie IoT (lub symulator) aprowizowane dla aktualizacji urządzenia w IoT Hub.
* [Program PowerShell 5](/powershell/scripting/install/installing-powershell) lub nowszy (obejmuje instalacje systemów Linux, macOS i Windows)
* Obsługiwane przeglądarki:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Niektóre dane przesłane do tej usługi mogą być przetwarzane w regionie spoza regionu, w których to wystąpienie zostało utworzone.

## <a name="create-device-update-import-manifest"></a>Tworzenie manifestu importu aktualizacji urządzenia

1. Jeśli jeszcze tego nie zrobiono, uzyskaj plik obrazu lub plik manifestu APT, który chcesz wdrożyć na urządzeniach. Może to pochodzić od producenta urządzeń lub integratora systemu, z który pracujesz, a nawet grupy w organizacji. Upewnij się, że plik obrazu aktualizacji lub plik manifestu APT znajduje się w katalogu dostępnym z programu PowerShell.

2. Utwórz plik tekstowy **o nazwie AduUpdate.psm1** w katalogu, w którym znajduje się plik obrazu aktualizacji lub plik manifestu APT. Następnie otwórz polecenie cmdlet programu PowerShell [AduUpdate.psm1,](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) skopiuj zawartość do pliku tekstowego, a następnie zapisz plik tekstowy.

3. W programie PowerShell przejdź do katalogu, w którym utworzono polecenie cmdlet programu PowerShell z kroku 2. Użyj poniższej opcji Kopiowania, a następnie wklej ją do programu PowerShell, aby uruchomić polecenia:

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Uruchom następujące polecenia, zastępując przykładowe wartości parametrów, aby wygenerować manifest importu, plik JSON opisujący aktualizację:
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    Poniżej znajdują się przykładowe wartości powyższych parametrów. Możesz również wyświetlić pełny schemat [manifestu importu, aby](import-schema.md) uzyskać więcej szczegółów.

    | Parametr | Opis |
    | --------- | ----------- |
    | deviceManufacturer | Producent urządzenia, dla których aktualizacja jest zgodna, na przykład z contoso. Musi _odpowiadać właściwości_ [urządzenia producenta](./device-update-plug-and-play.md#device-properties).
    | deviceModel | Model urządzenia, z który jest zgodna aktualizacja, na przykład Toaster. Musi _odpowiadać właściwości urządzenia_ [modelu](./device-update-plug-and-play.md#device-properties).
    | updateProvider | Jednostka, która tworzy aktualizację lub jest za nie bezpośrednio odpowiedzialna. Często będzie to nazwa firmy.
    | updateName (nazwa_aktualizacji) | Identyfikator klasy aktualizacji. Klasa może być wszystkim, co wybierzesz. Często będzie to nazwa urządzenia lub modelu.
    | UpdateVersion | Numer wersji odróżnia tę aktualizację od innych, które mają ten sam dostawca i nazwę. Nie jest zgodne z wersją pojedynczego składnika oprogramowania na urządzeniu (ale może, jeśli wybierzesz).
    | updateType (typ aktualizacji) | <ul><li>Określanie `microsoft/swupdate:1` dla aktualizacji obrazu</li><li>Określanie `microsoft/apt:1` aktualizacji pakietu</li></ul>
    | installedCriteria | <ul><li>Określanie wartości swVersion dla `microsoft/swupdate:1` typu aktualizacji</li><li>Określ **nazwę wersji**, gdzie _nazwa_ to nazwa manifestu APT, a _version_ to wersja manifestu APT. Na przykład contoso-iot-edge-1.0.0.0.
    | updateFilePath(s) | Ścieżka do plików aktualizacji na komputerze


## <a name="review-generated-import-manifest"></a>Przegląd wygenerowanego manifestu importu

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

## <a name="import-update"></a>Importowanie aktualizacji

[!NOTE]
Poniższe instrukcje pokazują, jak zaimportować aktualizację za pośrednictwem interfejsu Azure Portal użytkownika. Możesz również użyć aktualizacji [urządzenia dla interfejsów API IoT Hub,](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) aby zaimportować aktualizację. 

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do swojego IoT Hub aktualizacji urządzenia.

2. Po lewej stronie wybierz pozycję "Aktualizacje urządzenia" w obszarze "Automatyczne Zarządzanie urządzeniami".

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Importowanie aktualizacji" lightbox="media/import-update/import-updates-3.png":::

3. W górnej części ekranu zostanie wyświetlonych kilka kart. Wybierz kartę Aktualizacje.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Aktualizacje" lightbox="media/import-update/updates-tab.png":::

4. Wybierz pozycję "+ Zaimportuj nową aktualizację" poniżej nagłówka "Gotowe do wdrożenia".

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Importowanie nowej aktualizacji" lightbox="media/import-update/import-new-update-2.png":::

5. Wybierz ikonę folderu lub pole tekstowe w obszarze "Wybierz import pliku manifestu". Zostanie wyświetlone okno dialogowe s wyboru plików. Wybierz utworzony wcześniej manifest importu przy użyciu polecenia cmdlet programu PowerShell. Następnie wybierz ikonę folderu lub pole tekstowe w obszarze "Wybierz co najmniej jeden plik aktualizacji". Zostanie wyświetlone okno dialogowe s wyboru plików. Wybierz pliki aktualizacji.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Wybieranie opcji Aktualizuj pliki" lightbox="media/import-update/select-update-files.png":::

6. Wybierz ikonę folderu lub pole tekstowe w obszarze "Wybierz kontener magazynu". Następnie wybierz odpowiednie konto magazynu. Kontener magazynu służy do tymczasowego przechowywania plików aktualizacji.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Konto magazynu" lightbox="media/import-update/storage-account.png":::

7. Jeśli kontener został już utworzony, możesz go użyć ponownie. (W przeciwnym razie wybierz pozycję "+ Kontener", aby utworzyć nowy kontener magazynu na aktualizacje).  Wybierz kontener, który chcesz użyć, a następnie kliknij pozycję "Wybierz".

   :::image type="content" source="media/import-update/container.png" alt-text="Wybieranie kontenera" lightbox="media/import-update/container.png":::

8. Wybierz pozycję "Prześlij", aby rozpocząć proces importowania.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Publikowanie aktualizacji" lightbox="media/import-update/publish-update.png":::

9. Rozpocznie się proces importowania, a ekran przełączy się do sekcji "Historia importu". Wybierz pozycję "Odśwież", aby wyświetlić postęp aż do zakończenia procesu importowania (w zależności od rozmiaru aktualizacji może to potrwać kilka minut, ale może trwać dłużej).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Aktualizowanie sekwencjonowania importu" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Gdy kolumna Stan wskazuje, że importowanie zakończyło się pomyślnie, wybierz nagłówek "Gotowe do wdrożenia". Zaimportowana aktualizacja powinna być teraz wyświetlona na liście.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="Stan zadania" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Następne kroki

[Tworzenie grup](create-update-group.md)

[Dowiedz się więcej o pojęciach związanych z importowaniem](import-concepts.md)