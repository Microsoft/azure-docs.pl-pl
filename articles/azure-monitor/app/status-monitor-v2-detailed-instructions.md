---
title: Szczegółowe instrukcje programu Azure Application Insights Agent | Dokumenty firmy Microsoft
description: Szczegółowe instrukcje dotyczące rozpoczynania pracy z agentem usługi Application Insights. Monitoruj działanie witryny bez ponownego rozmieszczania witryny. Współpracuje ze ASP.NET aplikacjami sieci web hostowanymi lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 8f6134e8f8fdb9af3f578afaf0670c32a3896e01
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766864"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Agent usługi Application Insights (dawniej Monitor stanu w wersji 2): szczegółowe instrukcje

W tym artykule opisano sposób dołączania do galerii programu PowerShell i pobierania modułu ApplicationMonitor.
Zawarte są najczęstsze parametry, które trzeba rozpocząć.
Udostępniliśmy również instrukcje do pobrania ręcznego w przypadku, gdy nie masz dostępu do Internetu.

## <a name="get-an-instrumentation-key"></a>Pobierz klucz oprzyrządowania

Aby rozpocząć, potrzebny jest klucz oprzyrządowania. Aby uzyskać więcej informacji, zobacz [Tworzenie zasobu usługi Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Uruchamianie programu PowerShell jako administratora z podwyższonym poziomem uprawnień zasady wykonywania

### <a name="run-as-admin"></a>Uruchom jako administrator

Program PowerShell potrzebuje uprawnień na poziomie administratora, aby wprowadzić zmiany na komputerze.
### <a name="execution-policy"></a>Zasady wykonywania
- Opis: domyślnie uruchamianie skryptów programu PowerShell jest wyłączone. Zaleca się zezwolenie skryptom RemoteSigned tylko dla bieżącego zakresu.
- Odwołanie: [Informacje o zasadach wykonywania](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) i [zasadach ustawiania-wykonywania](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Polecenie: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Parametr opcjonalny:
    - `-Force`. Pomija monit potwierdzenia.

**Przykładowe błędy**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Wymagania wstępne programu PowerShell

Inspekcja wystąpienia programu PowerShell `$PSVersionTable` przez uruchomienie polecenia.
To polecenie generuje następujące dane wyjściowe:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Te instrukcje zostały napisane i przetestowane na komputerze z systemem Windows 10 i wersjach wymienionych powyżej.

## <a name="prerequisites-for-powershell-gallery"></a>Wymagania wstępne dla galerii programu PowerShell

Te kroki przygotują serwer do pobrania modułów z galerii programu PowerShell.

> [!NOTE] 
> Galeria programu PowerShell jest obsługiwana w systemach Windows 10, Windows Server 2016 i PowerShell 6.
> Aby uzyskać informacje o wcześniejszych wersjach, zobacz [Instalowanie programu PowerShellGet](/powershell/scripting/gallery/installing-psget).


1. Uruchom program PowerShell jako administrator z podwyższonym poziomem uprawnień.
2. Zainstaluj dostawcę pakietu NuGet.
    - Opis: ten dostawca jest potrzebny do interakcji z repozytoriami opartymi na nuget, takimi jak Galeria programu PowerShell.
    - [Referencje: Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Polecenie: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Parametry opcjonalne:
        - `-Proxy`. Określa serwer proxy dla żądania.
        - `-Force`. Pomija monit potwierdzenia.
    
    Ten monit zostanie wyświetlony, jeśli nuget nie jest skonfigurowany:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Skonfiguruj galerię programu PowerShell jako zaufane repozytorium.
    - Opis: Domyślnie Galeria programu PowerShell jest niezaufanym repozytorium.
    - Referencje: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Polecenie: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Parametr opcjonalny:
        - `-Proxy`. Określa serwer proxy dla żądania.

    Ten monit zostanie wyświetlony, jeśli galeria programu PowerShell nie jest zaufana:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Możesz potwierdzić tę zmianę i przeprowadzić inspekcję `Get-PSRepository` wszystkich psrepositories, uruchamiając polecenie.

4. Zainstaluj najnowszą wersję programu PowerShellGet.
    - Opis: Ten moduł zawiera narzędzia używane do uzyskania innych modułów z galerii programu PowerShell. Wersja 1.0.0.1 jest dostarczana z systemami Windows 10 i Windows Server. Wymagana jest wersja 1.6.0 lub nowsza. Aby ustalić, która wersja `Get-Command -Module PowerShellGet` jest zainstalowana, uruchom polecenie.
    - Odwołanie: [Instalowanie programu PowerShellGet](/powershell/scripting/gallery/installing-psget).
    - Polecenie: `Install-Module -Name PowerShellGet`.
    - Parametry opcjonalne:
        - `-Proxy`. Określa serwer proxy dla żądania.
        - `-Force`. Pomija ostrzeżenie "już zainstalowane" i instaluje najnowszą wersję.

    Ten błąd zostanie wyświetlony, jeśli nie używasz najnowszej wersji programu PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Uruchom ponownie program PowerShell. Nie można załadować nowej wersji w bieżącej sesji. Nowe sesje programu PowerShell załadują najnowszą wersję programu PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Pobierz i zainstaluj moduł za pośrednictwem programu PowerShell Gallery

Te kroki zostaną pobrane moduł Az.ApplicationMonitor z Galerii Programu PowerShell.

1. Upewnij się, że wszystkie wymagania wstępne dla galerii programu PowerShell są spełnione.
2. Uruchom program PowerShell jako administrator z podwyższonym poziomem uprawnień.
3. Zainstaluj moduł Az.ApplicationMonitor.
    - Referencje: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Polecenie: `Install-Module -Name Az.ApplicationMonitor`.
    - Parametry opcjonalne:
        - `-Proxy`. Określa serwer proxy dla żądania.
        - `-AllowPrerelease`. Umożliwia instalację wersji alfa i beta.
        - `-AcceptLicense`. Pomija monit "Akceptuj licencję"
        - `-Force`. Pomija ostrzeżenie "Niezaufane repozytorium".

## <a name="download-and-install-the-module-manually-offline-option"></a>Pobieranie i instalowanie modułu ręcznie (opcja offline)

Jeśli z jakiegoś powodu nie można połączyć się z modułem programu PowerShell, można ręcznie pobrać i zainstalować moduł Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Ręcznie pobierz najnowszy plik nupkg

1. Przejdź do pozycji https://www.powershellgallery.com/packages/Az.ApplicationMonitor (Plik > Nowy > Inny).
2. Wybierz najnowszą wersję pliku w **tabeli Historia wersji.**
3. W obszarze **Opcje instalacji**wybierz pozycję **Ręczne pobieranie**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Opcja 1: Instalacja w katalogu modułów programu PowerShell
Zainstaluj ręcznie pobrany moduł programu PowerShell w katalogu programu PowerShell, aby był wykrywalny przez sesje programu PowerShell.
Aby uzyskać więcej informacji, zobacz [Instalowanie modułu programu PowerShell](/powershell/scripting/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Rozpaj nupkg jako plik zip za pomocą Rozwiń-Archiwum (v1.0.1.0)

- Opis: podstawowa wersja programu Microsoft.PowerShell.Archive (wersja 1.0.1.0) nie może rozpakować plików nupkg. Zmień nazwę pliku za pomocą rozszerzenia zip.
- [Referencje: Rozwiń archiwum](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Polecenia:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Rozpaj nupkg za pomocą Rozwiń-Archiwum (v1.1.0.0)

- Opis: użyj bieżącej wersji Expand-Archive, aby rozpakować pliki nupkg bez zmiany rozszerzenia.
- Odwołanie: [Rozwiń archiwum](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) i [microsoft.powershell.archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Polecenia:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Opcja 2: Rozpaj i importuj nupkg ręcznie
Zainstaluj ręcznie pobrany moduł programu PowerShell w katalogu programu PowerShell, aby był wykrywalny przez sesje programu PowerShell.
Aby uzyskać więcej informacji, zobacz [Instalowanie modułu programu PowerShell](/powershell/scripting/developer/module/installing-a-powershell-module).

Jeśli instalujesz moduł w innym katalogu, zaimportuj moduł ręcznie za pomocą [importu modułu](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> Biblioteki DLL zostaną zainstalowane za pomocą ścieżek względnych.
> Przechowuj zawartość pakietu w katalogu zamierzonego środowiska wykonawczego i upewnij się, że uprawnienia dostępu zezwalają na odczyt, ale nie zapis.

1. Zmień rozszerzenie na ".zip" i wyodrębnij zawartość pakietu do katalogu instalacji zamierzonej.
2. Znajdź ścieżkę pliku Az.ApplicationMonitor.psd1.
3. Uruchom program PowerShell jako administrator z podwyższonym poziomem uprawnień.
4. Załaduj `Import-Module Az.ApplicationMonitor.psd1` moduł za pomocą polecenia.
    

## <a name="route-traffic-through-a-proxy"></a>Kierowanie ruchu przez serwer proxy

Podczas monitorowania komputera w prywatnym intranecie należy rozsyłać ruch HTTP za pośrednictwem serwera proxy.

Polecenia programu PowerShell do pobrania i zainstalowania programu Az.ApplicationMonitor z galerii programu PowerShell obsługują `-Proxy` parametr.
Zapoznaj się z poprzednimi instrukcjami podczas pisania skryptów instalacyjnych.

Pakiet SDK usługi Application Insights będzie musiał wysłać dane telemetryczne aplikacji do firmy Microsoft. Zaleca się skonfigurowanie ustawień serwera proxy dla aplikacji w pliku web.config. Aby uzyskać więcej informacji, zobacz [Usługa Application Insights często zadawane pytania: Przekazywanie serwera proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Włączanie monitorowania

Użyj `Enable-ApplicationInsightsMonitoring` polecenia, aby włączyć monitorowanie.

Szczegółowe opis sposobu używania tego polecenia cmdlet można znaleźć w [odwołaniu do interfejsu API.](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring)



## <a name="next-steps"></a>Następne kroki

 Wyświetlanie telemetrii:

- [Eksploruj metryki,](../../azure-monitor/platform/metrics-charts.md) aby monitorować wydajność i użycie.
- [Szukaj zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md) w celu zdiagnozowania problemów.
- [Korzystaj z analizy](../../azure-monitor/app/analytics.md) w przypadku bardziej zaawansowanych zapytań.
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).

 Dodawanie kolejnych funkcji telemetrii:

- [Tworzenie testów sieci Web](monitor-web-app-availability.md), aby upewnić się, że witryna pozostaje aktywna.
- [Dodaj dane telemetryczne klienta sieci Web,](../../azure-monitor/app/javascript.md) aby wyświetlić wyjątki od kodu strony sieci web i włączyć śledzenie wywołań.
- [Dodaj SDK usługi Application Insights do kodu, dzięki](../../azure-monitor/app/asp-net.md) czemu można wstawić śledzenie i rejestrowanie wywołań.

Więcej informacji za pomocą agenta usługi Application Insights:

- Skorzystaj z naszego [przewodnika,](status-monitor-v2-troubleshoot.md) aby rozwiązać problem z agentem aplikacji Insights.
