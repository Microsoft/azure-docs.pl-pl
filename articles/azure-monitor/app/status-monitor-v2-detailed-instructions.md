---
title: Szczegółowe instrukcje dotyczące agenta Application Insights platformy Azure | Microsoft Docs
description: Szczegółowe instrukcje dotyczące rozpoczynania pracy z agentem Application Insights. Monitorowanie wydajności witryny sieci Web bez ponownego wdrażania witryny sieci Web. Współpracuje z usługą ASP.NET Web Apps hostowaną lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 2a955273c01d8c0d865aabd91bb1bfcce70fd373
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587372"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Agent Application Insights (dawniej nazwany monitor stanu v2): szczegółowe instrukcje

W tym artykule opisano, jak dołączyć do Galeria programu PowerShell i pobrać moduł ApplicationMonitor.
Uwzględniono najbardziej typowe parametry, które należy rozpocząć.
Instrukcje pobierania ręcznego są również udostępniane w przypadku braku dostępu do Internetu.

## <a name="get-an-instrumentation-key"></a>Pobieranie klucza Instrumentacji

Aby rozpocząć, musisz dysponować kluczem Instrumentacji. Aby uzyskać więcej informacji, zobacz [Tworzenie zasobu Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Uruchom program PowerShell jako administrator z zasadami wykonywania podwyższonego poziomu

### <a name="run-as-admin"></a>Uruchom jako administrator

Program PowerShell wymaga uprawnień na poziomie administratora, aby wprowadzać zmiany na komputerze.
### <a name="execution-policy"></a>Zasady wykonywania
- Opis: domyślnie uruchamianie skryptów programu PowerShell jest wyłączone. Zalecamy Zezwalanie na skrypty RemoteSigned tylko dla bieżącego zakresu.
- Dokumentacja: [informacje na temat zasad wykonywania](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) i [Set-executionpolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6).
- Polecenie: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process` .
- Opcjonalny parametr:
    - `-Force`. Pomija monit o potwierdzenie.

**Przykłady błędów**

```output
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.

Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Wymagania wstępne dotyczące programu PowerShell

Inspekcja wystąpienia programu PowerShell przez uruchomienie `$PSVersionTable` polecenia.
To polecenie tworzy następujące dane wyjściowe:

```output
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

Te instrukcje zostały zanotowane i przetestowane na komputerze z systemem Windows 10 i wymienionych powyżej wersjach.

## <a name="prerequisites-for-powershell-gallery"></a>Wymagania wstępne dotyczące Galeria programu PowerShell

Te kroki spowodują przygotowanie serwera do pobierania modułów z Galeria programu PowerShell.

> [!NOTE] 
> Galeria programu PowerShell jest obsługiwana w systemach Windows 10, Windows Server 2016 i PowerShell 6.
> Aby uzyskać informacje na temat wcześniejszych wersji, zobacz [Installing PowerShellGet](/powershell/scripting/gallery/installing-psget).


1. Uruchom program PowerShell jako administrator z zasadami wykonywania podwyższonego poziomu uprawnień.
2. Zainstaluj dostawcę pakietów NuGet.
    - Opis: ten dostawca jest potrzebny do współpracy z repozytoriami opartymi na narzędziu NuGet, takimi jak Galeria programu PowerShell.
    - Odwołanie: [Install-PackageProvider](/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Polecenie: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201` .
    - Parametry opcjonalne:
        - `-Proxy`. Określa serwer proxy żądania.
        - `-Force`. Pomija monit o potwierdzenie.
    
    Ten monit zostanie wyświetlony, jeśli nie skonfigurowano narzędzia NuGet:

    ```output
    NuGet provider is required to continue
    PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. 
    The NuGet provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
    'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
    'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
    the NuGet provider now?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```    

3. Skonfiguruj Galeria programu PowerShell jako zaufane repozytorium.
    - Opis: domyślnie Galeria programu PowerShell jest niezaufanym repozytorium.
    - Odwołanie: [Set-PSRepository](/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Polecenie: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted` .
    - Opcjonalny parametr:
        - `-Proxy`. Określa serwer proxy żądania.

    Ten monit zostanie wyświetlony, jeśli Galeria programu PowerShell nie jest zaufany:

    ```output
    Untrusted repository
    You are installing the modules from an untrusted repository. 
    If you trust this repository, change its InstallationPolicy value 
    by running the Set-PSRepository cmdlet. Are you sure you want to 
    install the modules from 'PSGallery'?
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
    ```

    Możesz potwierdzić tę zmianę i Przeprowadź inspekcję wszystkich PSRepositories, uruchamiając `Get-PSRepository` polecenie.

4. Zainstaluj najnowszą wersję programu PowerShellGet.
    - Opis: ten moduł zawiera narzędzia używane do uzyskiwania innych modułów z Galeria programu PowerShell. Wersja 1.0.0.1 jest dostarczana z systemami Windows 10 i Windows Server. Wymagana jest wersja 1.6.0 lub nowsza. Aby określić, która wersja jest zainstalowana, uruchom `Get-Command -Module PowerShellGet` polecenie.
    - Odwołanie: [Instalowanie PowerShellGet](/powershell/scripting/gallery/installing-psget).
    - Polecenie: `Install-Module -Name PowerShellGet` .
    - Parametry opcjonalne:
        - `-Proxy`. Określa serwer proxy żądania.
        - `-Force`. Pomija ostrzeżenie "już zainstalowane" i instaluje najnowszą wersję.

    Ten błąd zostanie wyświetlony, jeśli nie używasz najnowszej wersji programu PowerShellGet:

    ```output
    Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
    At line:1 char:20
    Install-Module abc -AllowPrerelease
                   ~~~~~~~~~~~~~~~~
    CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
    FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    ```

5. Uruchom ponownie program PowerShell. Nie można załadować nowej wersji w bieżącej sesji. Nowe sesje programu PowerShell będą ładować najnowszą wersję programu PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Pobierz i zainstaluj moduł za pośrednictwem Galeria programu PowerShell

Te kroki spowodują pobranie modułu AZ. ApplicationMonitor z Galeria programu PowerShell.

1. Upewnij się, że spełniono wszystkie wymagania wstępne dotyczące Galeria programu PowerShell.
2. Uruchom program PowerShell jako administrator z zasadami wykonywania podwyższonego poziomu uprawnień.
3. Zainstaluj moduł AZ. ApplicationMonitor.
    - Reference: [Install-module](/powershell/module/powershellget/install-module?view=powershell-6).
    - Polecenie: `Install-Module -Name Az.ApplicationMonitor` .
    - Parametry opcjonalne:
        - `-Proxy`. Określa serwer proxy żądania.
        - `-AllowPrerelease`. Umożliwia instalację wersji Alpha i beta.
        - `-AcceptLicense`. Pomija monit "Akceptuj licencję"
        - `-Force`. Pomija ostrzeżenie "niezaufane repozytorium".

## <a name="download-and-install-the-module-manually-offline-option"></a>Pobierz i zainstaluj moduł ręcznie (opcja offline)

Jeśli z jakiegoś powodu nie można nawiązać połączenia z modułem programu PowerShell, można ręcznie pobrać i zainstalować moduł AZ. ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Ręcznie Pobierz najnowszy plik NUPKG

1. Przejdź do witryny https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Wybierz najnowszą wersję pliku w tabeli **historii wersji** .
3. W obszarze **Opcje instalacji** wybierz pozycję **Pobieranie ręczne**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Opcja 1: Instalowanie w katalogu modułów programu PowerShell
Zainstaluj ręcznie pobrany moduł programu PowerShell w katalogu programu PowerShell, aby można było go odnajdywać za pomocą sesji programu PowerShell.
Aby uzyskać więcej informacji, zobacz [Instalowanie modułu programu PowerShell](/powershell/scripting/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Rozpakuj NUPKG jako plik zip przy użyciu Expand-Archive (v 1.0.1.0)

- Opis: podstawowa wersja programu Microsoft. PowerShell. Archive (v 1.0.1.0) nie może rozpakować plików NUPKG. Zmień nazwę pliku na rozszerzenie zip.
- Reference: [expand-Archive](/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Dotyczące

    ```console
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Rozpakuj NUPKG za pomocą Expand-Archive (v 1.1.0.0)

- Opis: Użyj bieżącej wersji Expand-Archive, aby rozpakować pliki NUPKG bez zmiany rozszerzenia.
- Reference: [expand-Archive](/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) i [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Dotyczące

    ```console
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Opcja 2: ręczne rozpakowywanie i import NUPKG
Zainstaluj ręcznie pobrany moduł programu PowerShell w katalogu programu PowerShell, aby można było go odnajdywać za pomocą sesji programu PowerShell.
Aby uzyskać więcej informacji, zobacz [Instalowanie modułu programu PowerShell](/powershell/scripting/developer/module/installing-a-powershell-module).

Jeśli instalujesz moduł w innym katalogu, ręcznie zaimportuj moduł za pomocą polecenia [Import-Module](/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> Biblioteki DLL zostaną zainstalowane za pośrednictwem ścieżek względnych.
> Przechowuj zawartość pakietu w Twoim zamierzonym katalogu środowiska uruchomieniowego i upewnij się, że uprawnienia dostępu Zezwalaj na odczyt, ale nie do zapisu.

1. Zmień rozszerzenie na "zip" i Wyodrębnij zawartość pakietu w żądanym katalogu instalacyjnym.
2. Znajdź ścieżkę pliku Az.ApplicationMonitor.psd1.
3. Uruchom program PowerShell jako administrator z zasadami wykonywania podwyższonego poziomu uprawnień.
4. Załaduj moduł przy użyciu `Import-Module Az.ApplicationMonitor.psd1` polecenia.
    

## <a name="route-traffic-through-a-proxy"></a>Kierowanie ruchu przez serwer proxy

W przypadku monitorowania komputera w prywatnym intranecie należy kierować ruchem HTTP za pośrednictwem serwera proxy.

Polecenia programu PowerShell do pobrania i zainstalowania AZ. ApplicationMonitor z Galeria programu PowerShell obsługują `-Proxy` parametr.
Zapoznaj się z powyższymi instrukcjami podczas pisania skryptów instalacji.

Zestaw Application Insights SDK będzie musiał wysłać dane telemetryczne aplikacji do firmy Microsoft. Zalecamy skonfigurowanie ustawień serwera proxy dla aplikacji w pliku web.config. Aby uzyskać więcej informacji, zobacz [Application Insights często zadawanych pytań: przekazywania proxy](../faq.md#proxy-passthrough).


## <a name="enable-monitoring"></a>Włączanie monitorowania

Użyj `Enable-ApplicationInsightsMonitoring` polecenia, aby włączyć monitorowanie.

Szczegółowy opis sposobu korzystania z tego polecenia cmdlet można znaleźć w [dokumentacji interfejsu API](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring) .



## <a name="next-steps"></a>Następne kroki

 Wyświetlanie telemetrii:

- [Poznaj metryki](../essentials/metrics-charts.md) , aby monitorować wydajność i użycie.
- [Wyszukaj zdarzenia i dzienniki](./diagnostic-search.md) , aby zdiagnozować problemy.
- [Użyj analizy](../logs/log-query-overview.md) , aby uzyskać bardziej zaawansowane zapytania.
- [Tworzenie pulpitów nawigacyjnych](./overview-dashboard.md).

 Dodawanie kolejnych funkcji telemetrii:

- [Tworzenie testów sieci Web](monitor-web-app-availability.md), aby upewnić się, że witryna pozostaje aktywna.
- [Dodaj telemetrię klienta sieci Web](./javascript.md) , aby zobaczyć wyjątki z kodu strony sieci Web i włączyć wywołania śledzenia.
- [Dodaj do kodu zestaw SDK Application Insights](./asp-net.md) , aby móc wstawiać wywołania śledzenia i rejestrowania.

Zrób więcej dzięki Application Insights agentowi:

- Skorzystaj z naszego przewodnika, aby [rozwiązać problemy z](status-monitor-v2-troubleshoot.md) agentem Application Insights.

