---
title: Pracuj z historią wersji rozszerzenia konfiguracji żądanego stanu platformy Azure
description: Ten artykuł udostępnia informacje o historii wersji dla rozszerzenia konfiguracji żądanego stanu (DSC) na platformie Azure.
ms.date: 02/17/2021
keywords: dsc, powershell, azure, extension
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.openlocfilehash: e51bce6e015ef7367625b010b74e5d2422b35051
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651806"
---
# <a name="work-with-azure-desired-state-configuration-extension-version-history"></a>Pracuj z historią wersji rozszerzenia konfiguracji żądanego stanu platformy Azure

[Rozszerzenie](../virtual-machines/extensions/dsc-overview.md) maszyny wirtualnej konfiguracji żądanego stanu (DSC) platformy Azure jest aktualizowane w miarę potrzeb w celu obsługi ulepszeń i nowych funkcji dostarczanych przez platformę Azure, system Windows Server oraz środowisko Windows Management Framework (WMF), które zawiera program Windows PowerShell.

Ten artykuł zawiera informacje o każdej wersji rozszerzenia maszyny wirtualnej usługi Azure DSC, obsługiwanych środowiskach oraz komentarzach i uwagach dotyczących nowych funkcji lub zmian.

## <a name="latest-version"></a>Najnowsza wersja

### <a name="version-283"></a>Wersja 2,83

- **Data wydania:**
  - Luty 2021 r.
- **Obsługa systemu operacyjnego:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 z dodatkiem R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Klient z systemem Windows 7/8.1/10
  - Nano Server
- **Obsługa WMF:**
  - WMF 5.1
  - WMF 5,0 RTM
  - Aktualizacja WMF 4,0
  - WMF 4.0
- **Naturalne**
  - Azure
  - Azure Chiny Vianet 21
  - Azure Government
- **Uwagi:** Ta wersja zawiera poprawkę dla niepodpisanych plików binarnych z rozszerzeniem maszyny wirtualnej systemu Windows.

### <a name="version-280"></a>Wersja 2,80

- **Data wydania:**
  - 26 września SEP-2019 (Azure) | 6 lipca 2020 (Chiny Vianet 21) | 20 lipca 2020 (Azure Government)
- **Obsługa systemu operacyjnego:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 z dodatkiem R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Klient z systemem Windows 7/8.1/10
  - Nano Server
- **Obsługa WMF:**
  - WMF 5.1
  - WMF 5,0 RTM
  - Aktualizacja WMF 4,0
  - WMF 4.0
- **Naturalne**
  - Azure
  - Azure Chiny Vianet 21
  - Azure Government
- **Uwagi:** W tej wersji nie ma żadnych nowych funkcji.

### <a name="version-276"></a>Wersja 2,76

- **Data wydania:**
  - 9 maja 2018 (Azure) | 21 czerwca 2018 (Vianet platformy Azure — Chiny 21, Azure Government)
- **Obsługa systemu operacyjnego:**
  - Windows Server 2016
  - Windows Server 2012 z dodatkiem R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Klient z systemem Windows 7/8.1/10
  - Nano Server
- **Obsługa WMF:**
  - WMF 5.1
  - WMF 5,0 RTM
  - Aktualizacja WMF 4,0
  - WMF 4.0
- **Naturalne**
  - Azure
  - Azure Chiny Vianet 21
  - Azure Government
- **Uwagi:** Ta wersja używa DSC w systemie Windows Server 2016; w przypadku innych usługi Windows OSs instalowany jest program [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia). W przypadku serwera nano Server rola DSC jest zainstalowana na maszynie wirtualnej.
- **Nowe funkcje:**
  - Poprawa w metadanych rozszerzenia dla podstanów i innych poprawek drobnych błędów.

## <a name="supported-versions"></a>Obsługiwane wersje

> [!WARNING]
> Wersje 2,4 do 2,13 korzystają z wersji zapoznawczej WMF 5,0, której certyfikaty podpisywania wygasły w sierpniu 2016.
> Aby uzyskać więcej informacji o tym problemie, zobacz następujący [artykuł w blogu](https://devblogs.microsoft.com/powershell/azure-dsc-extension-versions-2-4-up-to-2-13-will-retire-in-august/).

### <a name="version-275"></a>Wersja 2,75

- **Data wydania:** 5 marca 2018
- **Obsługa systemu operacyjnego:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Obsługa WMF:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Środowisko:** Azure
- **Uwagi:** Ta wersja używa DSC w systemie Windows Server 2016; w przypadku innych usługi Windows OSs instalowany jest program [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia). W przypadku serwera nano Server rola DSC jest zainstalowana na maszynie wirtualnej.
- **Nowe funkcje:**
  - Po wymuszeniu protokołu TLS 1,2 w witrynie GitHub nie można dodać maszyny wirtualnej do Azure Automation konfiguracji stanu przy użyciu szablonów Menedżer zasobów możesz dostępnych w portalu Azure Marketplace lub użyć rozszerzenia DSC w celu pobrania wszystkich konfiguracji hostowanych w witrynie GitHub. Zwracany jest błąd podobny do poniższego podczas wdrażania rozszerzenia:

    ```json
    {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
        "details": [{
            "code": "Conflict",
            "message": "{
                \"status\": \"Failed\",
                \"error\": {
                    \"code\": \"ResourceDeploymentFailure\",
                    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",
                    \"details\": [ {
                        \"code\": \"VMExtensionProvisioningError\",
                        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'.
                        Error message: \\\"The DSC Extension failed to execute: Error downloading
                        https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip
                        after 29 attempts: The request was aborted: Could not create SSL/TLS secure channel..\\nMore information about the failure can
                        be found in the logs located under 'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.74.0.0' on the VM.\\\".\"
                    } ]
                }
            }"
        }]
    }
    ```

  - W nowej wersji rozszerzenia protokół TLS 1,2 jest teraz wymuszany. Po wdrożeniu rozszerzenia, jeśli zostało już określone `AutoUpgradeMinorVersion = true` w szablonie Menedżer zasobów, rozszerzenie jest autouaktualniane do 2,75. W przypadku ręcznych aktualizacji Określ `TypeHandlerVersion = 2.75` w szablonie Menedżer zasobów.

### <a name="version-270---272"></a>Wersja 2,70-2,72

- **Data wydania:** 13 listopada 2017
- **Obsługa systemu operacyjnego:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Obsługa WMF:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Środowisko:** Azure
- **Uwagi:** Ta wersja używa DSC w systemie Windows Server 2016; w przypadku innych usługi Windows OSs instalowany jest program [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia). W przypadku serwera nano Server rola DSC jest zainstalowana na maszynie wirtualnej.
- **Nowe funkcje:**
  - Poprawki błędów & usprawnienia, które upraszczają korzystanie z konfiguracji stanu Azure Automation w portalu i z szablonem Menedżer zasobów. Aby uzyskać więcej informacji, zobacz [domyślny skrypt konfiguracji](../virtual-machines/extensions/dsc-overview.md) w dokumentacji rozszerzenia DSC.

### <a name="version-226"></a>Wersja 2,26

- **Data wydania:** 9 czerwca 2017
- **Obsługa systemu operacyjnego:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Obsługa WMF:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Środowisko:** Azure
- **Uwagi:** Ta wersja używa DSC w systemie Windows Server 2016; w przypadku innych usługi Windows OSs instalowany jest program [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia). W przypadku serwera nano Server rola DSC jest zainstalowana na maszynie wirtualnej.
- **Nowe funkcje:**
  - Udoskonalenia telemetrii.

### <a name="version-225"></a>Wersja 2,25

- **Data wydania:** 2 czerwca 2017
- **Obsługa systemu operacyjnego:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Obsługa WMF:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Środowisko:** Azure
- **Uwagi:** Ta wersja używa DSC w systemie Windows Server 2016; w przypadku innych usługi Windows OSs instalowany jest program [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia). W przypadku serwera nano Server rola DSC jest zainstalowana na maszynie wirtualnej.
- **Nowe funkcje:**
  - Dodano kilka poprawek usterek i innych drobnych ulepszeń.

### <a name="version-224"></a>Wersja 2,24

- **Data wydania:** 13 kwietnia 2017
- **Obsługa systemu operacyjnego:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Obsługa WMF:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Środowisko:** Azure
- **Uwagi:** Ta wersja używa DSC w systemie Windows Server 2016; w przypadku innych usługi Windows OSs instalowany jest program [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia). W przypadku serwera nano Server rola DSC jest zainstalowana na maszynie wirtualnej.
- **Nowe funkcje:**
  - Udostępnia identyfikator UUID & maszyny wirtualnej jako metadane rozszerzenia. Dodano inne drobne ulepszenia.

### <a name="version-223"></a>Wersja 2,23

- **Data wydania:** 15 marca 2017
- **Obsługa systemu operacyjnego:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Obsługa WMF:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Środowisko:** Azure
- **Uwagi:** Ta wersja używa DSC w systemie Windows Server 2016; w przypadku innych usługi Windows OSs instalowany jest program [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia). W przypadku serwera nano Server rola DSC jest zainstalowana na maszynie wirtualnej.
- **Nowe funkcje:**
  - Dodano poprawki błędów i inne ulepszenia.

### <a name="version-222"></a>Wersja 2,22

- **Data wydania:** 8 lutego 2017
- **Obsługa systemu operacyjnego:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Obsługa WMF:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Środowisko:** Azure
- **Uwagi:** Ta wersja używa DSC w systemie Windows Server 2016; w przypadku innych usługi Windows OSs instalowany jest program [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia). W przypadku serwera nano Server rola DSC jest zainstalowana na maszynie wirtualnej.
- **Nowe funkcje:**
  - Rozszerzenie DSC obsługuje teraz program WMF 5,1.
  - Dodano drobne inne ulepszenia.

### <a name="version-221"></a>Wersja 2,21

- **Data wydania:** 2 grudnia 2016
- **Obsługa systemu operacyjnego:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Obsługa WMF:** WMF 5,1 w wersji zapoznawczej, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Środowisko:** Azure
- **Uwagi:** Ta wersja używa DSC w systemie Windows Server 2016; w przypadku innych usługi Windows OSs instalowany jest [pakiet Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia). W przypadku serwera nano Server rola DSC jest zainstalowana na maszynie wirtualnej.
- **Nowe funkcje:**
  - Rozszerzenie DSC jest teraz dostępne na serwerze nano Server. Ta wersja zawiera głównie zmiany w kodzie dotyczące uruchamiania rozszerzenia na serwerze nano Server.
  - Dodano drobne inne ulepszenia.

### <a name="version-220"></a>Wersja 2,20

- **Data wydania:** 2 sierpnia 2016
- **Obsługa systemu operacyjnego:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 z dodatkiem SP1
- **Obsługa WMF:** WMF 5,1 w wersji zapoznawczej, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Środowisko:** Azure
- **Uwagi:** Ta wersja używa DSC jako dołączonego do systemu Windows Server 2016 Technical Preview; w przypadku innych usługi Windows OSs instalowany jest [pakiet Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia).
- **Nowe funkcje:**
  - Obsługa wersji zapoznawczej WMF 5,1. Podczas pierwszej publikacji ta wersja była uaktualnieniem opcjonalnym i należy określić Wmfversion = "5.1 PP" w szablonach Menedżer zasobów, aby zainstalować wersję zapoznawczą WMF 5,1.
    Wmfversion = "Najnowsza" nadal instaluje pakiet [WMF 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/).
    Aby uzyskać więcej informacji na temat wersji zapoznawczej WMF 5,1, zobacz [ten blog](https://devblogs.microsoft.com/powershell/announcing-windows-management-framework-wmf-5-1-preview/).
  - Dodano drobne inne poprawki i ulepszenia.

### <a name="version--219"></a>Wersja 2,19

- **Data wydania:** 3 czerwca 2016
- **Obsługa systemu operacyjnego:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 z dodatkiem SP1
- **Obsługa WMF:** WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Środowisko:** Azure, Azure Chiny Vianet 21, Azure Government
- **Uwagi:** Ta wersja używa DSC jako dołączonego do systemu Windows Server 2016 Technical Preview; w przypadku innych usługi Windows OSs instalowany jest [pakiet Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia).
- **Nowe funkcje:**
  - Rozszerzenie DSC jest teraz dostępne na platformie Azure (Chiny) Vianet 21. Ta wersja zawiera poprawki do uruchamiania rozszerzenia na platformie Azure (Chiny) Vianet 21.

### <a name="version-218"></a>Wersja 2,18

- **Data wydania:** 3 czerwca 2016
- **Obsługa systemu operacyjnego:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 z dodatkiem SP1
- **Obsługa WMF:** WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Środowisko:** Azure
- **Uwagi:** Ta wersja używa DSC jako dołączonego do systemu Windows Server 2016 Technical Preview; w przypadku innych usługi Windows OSs instalowany jest [pakiet Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia).
- **Nowe funkcje:**
  - Utwórz nieblokującą telemetrię, gdy wystąpi błąd podczas pobierania poprawki telemetrycznej (znany problem Azure DNS) lub podczas instalacji.
  - Poprawka dla sporadycznego problemu, w którym rozszerzenie przestaje przetwarzać konfigurację po ponownym uruchomieniu. Spowodowało to, że rozszerzenie DSC pozostanie w stanie "przechodzenie".
  - Dodano drobne inne poprawki i ulepszenia.

### <a name="version-217"></a>Wersja 2,17

- **Data wydania:** 26 kwietnia 2016
- **Obsługa systemu operacyjnego:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 z dodatkiem SP1
- **Obsługa WMF:** WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Środowisko:** Azure
- **Uwagi:** Ta wersja używa DSC jako dołączonego do systemu Windows Server 2016 Technical Preview; w przypadku innych usługi Windows OSs instalowany jest [pakiet Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia).
- **Nowe funkcje:**
  - Obsługa aktualizacji WMF 4,0. Aby uzyskać więcej informacji na temat aktualizacji WMF 4,0, zobacz [ten blog](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/).
  - Wykonaj ponowną próbę logiki w przypadku błędów, które wystąpiły podczas instalacji rozszerzenia DSC, lub stosując instalację rozszerzenia konfiguracji DSC. W ramach tej zmiany rozszerzenie ponowi próbę instalacji, jeśli poprzednia instalacja zakończyła się niepowodzeniem lub ponownie przetworzyć konfigurację DSC, która wcześniej zakończyła się niepowodzeniem, przez maksymalnie trzy razy do momentu osiągnięcia stanu ukończenia (powodzenie/błąd) lub jeśli nowe żądanie zostanie wykonane. Jeśli rozszerzenie nie powiedzie się z powodu nieprawidłowych ustawień użytkownika/danych wejściowych użytkownika, próba nie zostanie ponowiona. W takim przypadku rozszerzenie musi zostać wywołane ponownie z nowym żądaniem i prawidłowymi ustawieniami użytkownika. 

  > [!NOTE]
   > Rozszerzenie DSC jest zależne od agenta maszyny wirtualnej platformy Azure na potrzeby ponownych prób. Agent maszyny wirtualnej platformy Azure wywołuje rozszerzenie przy ostatnim niepomyślnym żądaniu, dopóki nie osiągnie stanu powodzenia lub błędu.

### <a name="version-216"></a>Wersja 2,16

- **Data wydania:** 21 kwietnia 2016
- **Obsługa systemu operacyjnego:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 z dodatkiem SP1
- **Obsługa WMF:** WMF 5,0 RTM, WMF 4,0
- **Środowisko:** Azure
- **Uwagi:** Ta wersja używa DSC jako dołączonego do systemu Windows Server 2016 Technical Preview; w przypadku innych usługi Windows OSs instalowany jest [pakiet Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia).
- **Nowe funkcje:**
  - Poprawa obsługi błędów i innych mniejszych poprawek błędów.
  - Nowa właściwość w ustawieniach rozszerzenia DSC. `ForcePullAndApply` w programie AdvancedOptions został dodany, aby włączyć rozszerzenie DSC w celu skonfigurowania konfiguracji DSC, gdy zostanie pobrany tryb odświeżania (w przeciwieństwie do domyślnego trybu wypychania). Więcej informacji o ustawieniach rozszerzenia DSC można znaleźć w [tym blogu](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/).

### <a name="version-215"></a>Wersja 2,15

- **Data wydania:** 14 marca 2016
- **Obsługa systemu operacyjnego:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 z dodatkiem SP1
- **Obsługa WMF:** WMF 5,0 RTM, WMF 4,0
- **Środowisko:** Azure
- **Uwagi:** Ta wersja używa DSC jako dołączonego do systemu Windows Server 2016 Technical Preview; w przypadku innych usługi Windows OSs instalowany jest [pakiet Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia).
- **Nowe funkcje:**
  - W przypadku rozszerzenia w wersji 2,14 wprowadzono zmiany w celu zainstalowania pakietu WMF RTM. Podczas uaktualniania z rozszerzenia Version 2.13.2.0 do 2.14.0.0 można zauważyć, że niektóre polecenia cmdlet DSC kończą się niepowodzeniem lub Konfiguracja kończy się niepowodzeniem z powodu błędu — "nie znaleziono wystąpienia z podaną wartością właściwości". Aby uzyskać więcej informacji, zobacz informacje o [wersji DSC](/powershell/scripting/wmf/known-issues/known-issues-dsc). Obejścia tych problemów zostały dodane w wersji 2,15. 
  - Jeśli zainstalowano już wersję 2,14 i uruchomiono jeden z powyższych dwóch problemów, należy wykonać te kroki ręcznie. W sesji programu PowerShell z podwyższonym poziomem uprawnień uruchom następujące polecenia:
    - `Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof`
    - `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

### <a name="version-214"></a>Wersja 2,14

- **Data wydania:** 25 lutego 2016
- **Obsługa systemu operacyjnego:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 z dodatkiem SP1
- **Obsługa WMF:** WMF 5,0 RTM, WMF 4,0
- **Środowisko:** Azure
- **Uwagi:** Ta wersja używa DSC jako dołączonego do systemu Windows Server 2016 Technical Preview; w przypadku innych usługi Windows OSs instalowany jest [pakiet Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (zainstalowanie pakietu WMF wymaga ponownego uruchomienia).
- **Nowe funkcje:**
  - Używa pakietu WMF RTM.
  - Umożliwia zbieranie danych w celu poprawienia jakości rozszerzenia DSC. Aby uzyskać więcej informacji, zobacz ten [artykuł w blogu](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/).
  - Udostępnia zaktualizowany format ustawień dla rozszerzenia w szablonie Menedżer zasobów. Aby uzyskać więcej informacji, zobacz ten [artykuł w blogu](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/).
  - Poprawki błędów i inne ulepszenia.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat programu PowerShell DSC, zobacz [centrum dokumentacji programu PowerShell](/powershell/scripting/dsc/overview/overview).
- Zapoznaj się z [szablonem Menedżer zasobów dla rozszerzenia DSC](../virtual-machines/extensions/dsc-template.md).
- Aby uzyskać inne funkcje i zasoby, którymi można zarządzać za pomocą programu PowerShell DSC, przejrzyj [galerię programu PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Aby uzyskać szczegółowe informacje o przekazywaniu poufnych parametrów do konfiguracji, zobacz [Zarządzanie poświadczeniami bezpiecznie przy użyciu programu obsługi rozszerzeń DSC](../virtual-machines/extensions/dsc-credentials.md).
