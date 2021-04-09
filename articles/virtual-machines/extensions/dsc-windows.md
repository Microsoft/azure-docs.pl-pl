---
title: Procedura obsługi rozszerzenia konfiguracji żądanego stanu platformy Azure
description: Przekazywanie i stosowanie konfiguracji DSC programu PowerShell na maszynie wirtualnej platformy Azure przy użyciu rozszerzenia DSC
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: bobbytreed
ms.author: robreed
ms.collection: windows
ms.date: 03/26/2018
ms.openlocfilehash: 72f66aeee64133a13ce0e49155c4b2a90240a3fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559991"
---
# <a name="powershell-dsc-extension"></a>Rozszerzenie DSC programu PowerShell

## <a name="overview"></a>Omówienie

Rozszerzenie DSC programu PowerShell dla systemu Windows jest publikowane i obsługiwane przez firmę Microsoft. Rozszerzenie przekazuje i stosuje konfigurację DSC programu PowerShell na maszynie wirtualnej platformy Azure. Rozszerzenie DSC wywołuje program PowerShell DSC w celu przeprowadzenia odebranej konfiguracji DSC na maszynie wirtualnej. Ten dokument zawiera szczegółowe informacje o obsługiwanych platformach, konfiguracjach i opcjach wdrażania dla rozszerzenia maszyny wirtualnej DSC dla systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie DSC obsługuje następujące elementy systemu operacyjnego:

Windows Server 2019, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 z dodatkiem SP1, klient z systemem Windows 7/8.1/10

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie DSC dla systemu Windows wymaga, aby docelowa maszyna wirtualna mogła komunikować się z platformą Azure i lokalizacją pakietu konfiguracji (plik zip), jeśli jest przechowywana w lokalizacji poza platformą Azure. 

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON przedstawia schemat części ustawień rozszerzenia DSC w szablonie Azure Resource Manager. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false,
            "downloadMappings": {
             "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | data |
| publisher | Microsoft. PowerShell. DSC | ciąg |
| typ | DSC | ciąg |
| typeHandlerVersion | 2,77 | int |

### <a name="settings-property-values"></a>Wartości właściwości ustawień

| Nazwa | Typ danych | Opis
| ---- | ---- | ---- |
| Settings. wmfVersion | ciąg | Określa wersję platformy zarządzania systemem Windows, która ma zostać zainstalowana na maszynie wirtualnej. Ustawienie dla tej właściwości wartości "Najnowsza" spowoduje zainstalowanie najbardziej zaktualizowanej wersji programu WMF. Jedyne bieżące możliwe wartości tej właściwości to "4,0", "5,0" i "Najnowsza". Te możliwe wartości podlegają aktualizacjom. Wartość domyślna to "Najnowsza". |
| settings.configwersja. URL | ciąg | Określa lokalizację adresu URL, z którego ma zostać pobrany plik zip konfiguracji DSC. Jeśli podany adres URL wymaga tokenu sygnatury dostępu współdzielonego, należy ustawić właściwość protectedSettings.configurationUrlSasToken na wartość tokenu SAS. Ta właściwość jest wymagana, jeśli są zdefiniowane settings.configwersja. Script i/lub settings.configwersja. Function.
| settings.configwersja. Script | ciąg | Określa nazwę pliku skryptu, który zawiera definicję konfiguracji DSC. Ten skrypt musi znajdować się w folderze głównym pliku zip pobranego z adresu URL określonego przez właściwość Configuration. URL. Ta właściwość jest wymagana, jeśli są zdefiniowane settings.configwersja. URL i/lub settings.configwersja. Script.
| settings.configwersja. Function | ciąg | Określa nazwę konfiguracji DSC. Konfiguracja musi być zawarta w skrypcie zdefiniowanym przez Configuration. Script. Ta właściwość jest wymagana, jeśli są zdefiniowane settings.configwersja. URL i/lub settings.configwersja. Function.
| settings.configurationArguments | Kolekcja | Definiuje wszystkie parametry, które chcesz przekazać do konfiguracji DSC. Ta właściwość nie zostanie zaszyfrowana.
| settings.configurationData. URL | ciąg | Określa adres URL, z którego należy pobrać plik danych konfiguracji (pds1), który ma być używany jako dane wejściowe dla konfiguracji DSC. Jeśli podany adres URL wymaga tokenu sygnatury dostępu współdzielonego, należy ustawić właściwość protectedSettings.configurationDataUrlSasToken na wartość tokenu SAS.
| Settings. privacy. dataenabledd | ciąg | Włącza lub wyłącza zbieranie danych telemetrycznych. Jedyne możliwe wartości tej właściwości to "Enable", "Disable", "lub $null. Pozostawienie tej właściwości pustej lub wartości null spowoduje włączenie telemetrii
| Settings. advancedOptions. forcePullAndApply | Wartość logiczna | To ustawienie ma na celu zwiększenie komfortu pracy z rozszerzeniem w celu rejestrowania węzłów za pomocą Azure Automation DSC.  Jeśli wartość to `$true` , rozszerzenie będzie oczekiwać na pierwsze uruchomienie konfiguracji pobranej z usługi przed zwróceniem powodzenia/niepowodzenia.  Jeśli wartość jest ustawiona na $false, stan zwrócony przez rozszerzenie będzie odnosić się tylko do tego, czy węzeł został zarejestrowany z konfiguracją stanu Azure Automation pomyślnie, a konfiguracja węzła nie zostanie uruchomiona podczas rejestracji.
| Settings. advancedOptions. downloadMappings | Kolekcja | Definiuje alternatywne lokalizacje do pobierania zależności, takich jak WMF i .NET.

### <a name="protected-settings-property-values"></a>Wartości właściwości ustawień chronionych

| Nazwa | Typ danych | Opis
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | ciąg | Definiuje wszystkie parametry, które chcesz przekazać do konfiguracji DSC. Ta właściwość zostanie zaszyfrowana. |
| protectedSettings.configurationUrlSasToken | ciąg | Określa token sygnatury dostępu współdzielonego, aby uzyskać dostęp do adresu URL zdefiniowanego przez wartość Configuration. URL. Ta właściwość zostanie zaszyfrowana. |
| protectedSettings.configurationDataUrlSasToken | ciąg | Określa token sygnatury dostępu współdzielonego, aby uzyskać dostęp do adresu URL zdefiniowanego przez configurationData. URL. Ta właściwość zostanie zaszyfrowana. |


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager.
Szablony są idealne do wdrożenia co najmniej jednej maszyny wirtualnej, która wymaga konfiguracji po wdrożeniu.
Przykładowy szablon Menedżer zasobów, który zawiera rozszerzenie DSC dla systemu Windows, można znaleźć w [galerii szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91).

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Pakiet rozszerzenia zostanie pobrany i wdrożony w tej lokalizacji na maszynie wirtualnej platformy Azure
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Plik stanu rozszerzenia zawiera stan podrzędny i kody błędów stanu, a także szczegółowy błąd i opis każdego uruchomienia rozszerzenia.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Dzienniki wyjściowe rozszerzenia są rejestrowane w następującym katalogu:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Kody błędów i ich znaczenie

| Kod błędu | Znaczenie | Możliwa akcja |
| :---: | --- | --- |
| 1000 | Błąd rodzajowy | Komunikat dla tego błędu jest dostarczany przez konkretny wyjątek w dziennikach rozszerzeń |
| 52 | Błąd instalacji rozszerzenia | Komunikat dla tego błędu jest dostarczany przez konkretny wyjątek |
| 1002 | Błąd instalacji WMF | Wystąpił błąd podczas instalowania pakietu WMF. |
| 1004 | Nieprawidłowy pakiet ZIP | Nieprawidłowy plik zip; Błąd podczas rozpakowywania pliku zip |
| 1100 | Błąd argumentu | Wskazuje problem w danych wejściowych dostarczonych przez użytkownika. Komunikat o błędzie jest dostarczany przez konkretny wyjątek|


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).
