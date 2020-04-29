---
title: Przykładowe skrypty programu PowerShell
description: Przykłady pokazujące, jak używać frontonu za pośrednictwem skryptów programu PowerShell
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: c45d2fc34ccbab6d813f12563678d036f9f35753
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80891496"
---
# <a name="example-powershell-scripts"></a>Przykładowe skrypty programu PowerShell

Zdalne renderowanie na platformie Azure udostępnia dwa następujące interfejsy API REST:

- [Interfejs API REST konwersji](../how-tos/conversion/conversion-rest-api.md)
- [Interfejs API REST sesji](../how-tos/session-rest-api.md)

[Repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering) zawiera przykładowe skrypty w folderze *skryptów* na potrzeby współpracy z interfejsami API REST usługi. W tym artykule opisano ich użycie.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać przykładowe skrypty, musisz mieć funkcjonalną konfigurację [Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

1. Zainstaluj moduł Azure PowerShell:
    1. Otwieranie programu PowerShell z uprawnieniami administratora
    1. Wykonane`Install-Module -Name Az -AllowClobber`

1. W przypadku wystąpienia błędów dotyczących uruchamiania skryptów upewnij się, że [zasady wykonywania](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) zostały odpowiednio ustawione:
    1. Otwieranie programu PowerShell z uprawnieniami administratora
    1. Wykonane`Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [Przygotowywanie konta usługi Azure Storage](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Zaloguj się do subskrypcji zawierającej konto renderowania zdalnego platformy Azure:
    1. Otwieranie programu PowerShell
    1. Uruchom: `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

> [!NOTE]
> W przypadku, gdy organizacja ma więcej niż jedną subskrypcję, może być konieczne określenie argumentów subskrypcji i dzierżawy. Szczegółowe informacje znajdują się w [dokumentacji Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

1. Pobierz folder *skryptów* z [repozytorium GithHub zdalnego renderowania platformy Azure](https://github.com/Azure/azure-remote-rendering).

## <a name="configuration-file"></a>Plik konfiguracji

Obok plików `arrconfig.json` należy `.ps1` wypełnić następujące pola:

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> Upewnij się, że w ścieżce LocalAssetDirectoryPath są poprawnie wysunięte ukośniki odwrotne: "\\\\" i użyj ukośników "/" we wszystkich innych ścieżkach, takich jak inputFolderPath i inputAssetPath.

### <a name="accountsettings"></a>accountSettings

W `arrAccountId` przypadku `arrAccountKey`i, zobacz [Tworzenie konta renderowania zdalnego dla platformy Azure](../how-tos/create-an-account.md).
Aby `region` zapoznać się z [listą dostępnych regionów](../reference/regions.md).

### <a name="renderingsessionsettings"></a>renderingSessionSettings

Ta struktura musi zostać wypełniona, jeśli chcesz uruchomić **RenderingSession. ps1**.

- **vmSize:** Wybiera rozmiar maszyny wirtualnej. Wybierz pozycję *standardowa* lub *Premium*. Zamknij sesje renderowania, gdy nie są już potrzebne.
- **maxLeaseTime:** Czas trwania dzierżawy maszyny wirtualnej. Zostanie on zamknięty po wygaśnięciu dzierżawy. Czas dzierżawy można przedłużyć później (patrz poniżej).

### <a name="assetconversionsettings"></a>assetConversionSettings

Ta struktura musi zostać wypełniona, jeśli chcesz uruchomić program **Conversion. ps1**.

Aby uzyskać szczegółowe informacje, zobacz [Przygotowywanie konta usługi Azure Storage](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts).

## <a name="script-renderingsessionps1"></a>Skrypt: RenderingSession. ps1

Ten skrypt służy do tworzenia, wykonywania zapytań i przerywania renderowania sesji.

> [!IMPORTANT]
> Upewnij się, że wypełniono sekcje *accountSettings* i *renderingSessionSettings* w pliku arrconfig. JSON.

### <a name="create-a-rendering-session"></a>Tworzenie sesji renderowania

Normalne użycie z w pełni wypełnionym arrconfig. JSON:

```PowerShell
.\RenderingSession.ps1
```

Skrypt wywoła [interfejs API REST zarządzania sesją](../how-tos/session-rest-api.md) , aby uruchomić maszynę wirtualną renderowania z określonymi ustawieniami. Po pomyślnym zakończeniu zostanie pobrany *Identyfikator sesji*. Następnie będzie sondował właściwości sesji do momentu, aż sesja będzie gotowa lub wystąpi błąd.

Aby użyć **alternatywnego pliku konfiguracji** :

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Można **przesłonić poszczególne ustawienia** z pliku konfiguracyjnego:

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Aby **rozpocząć sesję tylko bez sondowania**, można użyć:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

*Identyfikator sesji* pobierany przez skrypt musi być przesyłany do większości innych poleceń sesji.

### <a name="retrieve-session-properties"></a>Pobieranie właściwości sesji

Aby uzyskać właściwości sesji, uruchom polecenie:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

Użyj `-Poll` , aby zaczekać, aż sesja będzie *gotowa* lub wystąpił błąd.

### <a name="list-active-sessions"></a>Wyświetl aktywne sesje

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>Zatrzymaj sesję

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>Zmień właściwości sesji

Obecnie obsługujemy tylko Zmienianie maxLeaseTime sesji.

> [!NOTE]
> Czas dzierżawy jest zawsze liczony od momentu, kiedy maszyna wirtualna sesji została początkowo utworzona. W celu przedłużenia dzierżawy sesji o inną godzinę Zwiększ *maxLeaseTime* o jedną godzinę.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Skrypt: Conversion. ps1

Ten skrypt służy do konwertowania modeli wejściowych na format środowiska uruchomieniowego dla renderowania zdalnego platformy Azure.

> [!IMPORTANT]
> Upewnij się, że wypełniono sekcje *accountSettings* i *assetConversionSettings* w pliku arrconfig. JSON.

Skrypt pokazuje dwie opcje używania kont magazynu z usługą:

- Konto magazynu połączone z kontem renderowania zdalnego na platformie Azure
- Zapewnianie dostępu do magazynu za pośrednictwem sygnatur dostępu współdzielonego (SAS)

### <a name="linked-storage-account"></a>Połączone konto magazynu

Po pełnym wypełnieniu arrconfig. JSON i powiązaniu konta magazynu możesz użyć następującego polecenia. Łączenie konta magazynu zostało opisane w temacie [Tworzenie konta](../how-tos/create-an-account.md#link-storage-accounts).

Użycie połączonego konta magazynu jest preferowanym sposobem korzystania z usługi konwersji, ponieważ nie ma potrzeby generowania sygnatur dostępu współdzielonego.

```PowerShell
.\Conversion.ps1
```

1. Przekaż wszystkie pliki znajdujące się `assetConversionSettings.modelLocation` w kontenerze wejściowego obiektu BLOB pod danym`inputFolderPath`
1. Wywołaj [interfejs API REST konwersji modelu](../how-tos/conversion/conversion-rest-api.md) , aby uruchomić [konwersję modelu](../how-tos/conversion/model-conversion.md)
1. Sondowanie stanu konwersji do momentu pomyślnego przeprowadzenia konwersji lub niepowodzenia
1. Szczegóły danych wyjściowych przekonwertowanej lokalizacji pliku (konto magazynu, kontener danych wyjściowych, ścieżka pliku w kontenerze)

### <a name="access-to-storage-via-shared-access-signatures"></a>Dostęp do magazynu za pośrednictwem sygnatur dostępu współdzielonego

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Spowoduje to:

1. Przekaż plik lokalny z `assetConversionSettings.localAssetDirectoryPath` do wejściowego kontenera obiektów BLOB
1. Generuj identyfikator URI sygnatury dostępu współdzielonego dla kontenera wejściowego
1. Generuj identyfikator URI sygnatury dostępu współdzielonego dla kontenera wyjściowego
1. Wywołaj [interfejs API REST konwersji modelu](../how-tos/conversion/conversion-rest-api.md) , aby uruchomić [konwersję modelu](../how-tos/conversion/model-conversion.md)
1. Sondowanie stanu konwersji do momentu pomyślnego przeprowadzenia konwersji lub niepowodzenia
1. Szczegóły danych wyjściowych przekonwertowanej lokalizacji pliku (konto magazynu, kontener danych wyjściowych, ścieżka pliku w kontenerze)
1. Wyprowadzanie identyfikatora URI sygnatury dostępu współdzielonego do konwertowanego modelu w wyjściowym kontenerze obiektów BLOB

### <a name="additional-command-line-options"></a>Dodatkowe opcje wiersza polecenia

Aby użyć **alternatywnego pliku konfiguracji** :

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Aby **uruchomić wyłącznie konwersję modelu bez sondowania**, można użyć:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

**Poszczególne ustawienia można przesłonić** z pliku konfiguracji przy użyciu następujących przełączników wiersza polecenia:

* **Identyfikator:** ConversionId używany z GetConversionStatus
* **ArrAccountId:** ArrAccountId of accountSettings
* **ArrAccountKey:** override dla ArrAccountKey accountSettings
* **Region:** override dla regionu accountSettings
* Podzbiór **zasobów:** przesłonięcie dla assetConversionSettings zasobów
* **StorageAccountName:** override dla StorageAccountName assetConversionSettings
* **BlobInputContainerName:** override dla blobInputContainer assetConversionSettings
* **LocalAssetDirectoryPath:** override dla LocalAssetDirectoryPath assetConversionSettings
* **InputAssetPath:** override dla InputAssetPath assetConversionSettings
* **BlobOutputContainerName:** override dla BlobOutputContainerName assetConversionSettings
* **OutputFolderPath:** Przesłoń dla OutputFolderPath assetConversionSettings
* **OutputAssetFileName:** override dla OutputAssetFileName assetConversionSettings

Na przykład można połączyć wiele z tych opcji, takich jak:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>Uruchamianie poszczególnych etapów konwersji

Jeśli chcesz wykonać poszczególne kroki procesu, możesz użyć:

Przekazuj tylko dane z danego LocalAssetDirectoryPath

```PowerShell
.\Conversion.ps1 -Upload
```

Tylko proces konwersji modelu został już przekazany do magazynu obiektów BLOB (nie uruchamiaj przekazywania, nie sonduje stanu konwersji) skrypt zwróci *conversionId*.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

Można też pobrać stan konwersji tej konwersji przy użyciu:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

Użyj `-Poll` , aby poczekać na zakończenie konwersji lub wystąpił błąd.

## <a name="next-steps"></a>Następne kroki

- [Szybki Start: renderowanie modelu przy użyciu aparatu Unity](../quickstarts/render-model.md)
- [Szybki Start: konwertowanie modelu do renderowania](../quickstarts/convert-model.md)
- [Konwersja modelu](../how-tos/conversion/model-conversion.md)
