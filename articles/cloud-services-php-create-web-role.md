---
title: Tworzenie ról sieci Web i procesu roboczego platformy Azure dla języka PHP
description: Przewodnik dotyczący tworzenia ról sieci Web i procesów roboczych języka PHP w usłudze w chmurze platformy Azure oraz konfigurowania środowiska uruchomieniowego PHP.
services: ''
documentationcenter: php
author: msangapu-msft
manager: gwallace
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 0b02662658b2199a13f117ad95c6402f9b2974f9
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95519850"
---
# <a name="create-php-web-and-worker-roles"></a>Tworzenie roli internetowej i procesu roboczego PHP

## <a name="overview"></a>Omówienie

W tym przewodniku opisano sposób tworzenia ról sieci Web lub procesów roboczych języka PHP w środowisku deweloperskim systemu Windows. Wybierz określoną wersję środowiska PHP z dostępnych wersji "wbudowane", Zmień konfigurację PHP, Włącz rozszerzenia i na koniec Wdróż ją na platformie Azure. Opisano w nim również sposób konfigurowania roli sieci Web lub procesu roboczego w celu używania środowiska uruchomieniowego PHP (z niestandardowymi rozszerzeniami), które zapewniasz.

Platforma Azure udostępnia trzy modele obliczeniowe na potrzeby uruchamiania aplikacji: Azure App Service, Azure Virtual Machines i Azure Cloud Services. Wszystkie trzy modele obsługują język PHP. Cloud Services, która obejmuje role sieci Web i procesu roboczego, zapewnia *platformę jako usługę (PaaS)*. W ramach usługi w chmurze rola sieci Web zapewnia dedykowany serwer sieci Web Internet Information Services (IIS) do hostowania aplikacji sieci Web frontonu. Rola procesu roboczego może uruchamiać asynchroniczne, długotrwałe lub nieograniczone zadania niezależne od interakcji z użytkownikiem lub danych wejściowych.

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Opcje hostingu obliczeń udostępniane przez platformę Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Pobierz zestaw Azure SDK dla języka PHP

[Zestaw Azure SDK dla języka PHP](https://github.com/Azure/azure-sdk-for-php) zawiera kilka składników. W tym artykule zostaną użyte dwa z nich: Azure PowerShell i emulatory platformy Azure. Te dwa składniki można zainstalować za pośrednictwem Instalator platformy Microsoft Web. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/).

## <a name="create-a-cloud-services-project"></a>Tworzenie projektu Cloud Services

Pierwszym krokiem tworzenia roli sieci Web lub procesu roboczego w języku PHP jest utworzenie projektu usługi platformy Azure. Projekt usługi platformy Azure służy jako kontener logiczny dla ról Sieć Web i proces roboczy oraz zawiera pliki [definicji usługi projektu (. csdef)] i [konfiguracji usługi (. cscfg)] .

Aby utworzyć nowy projekt usługi platformy Azure, uruchom Azure PowerShell jako administrator i wykonaj następujące polecenie:

```powershell
PS C:\>New-AzureServiceProject myProject
```

To polecenie spowoduje utworzenie nowego katalogu ( `myProject` ), do którego można dodać role sieci Web i procesu roboczego.

## <a name="add-php-web-or-worker-roles"></a>Dodaj role sieci Web lub procesów roboczych języka PHP

Aby dodać rolę sieci Web w języku PHP do projektu, uruchom następujące polecenie w katalogu głównym projektu:

```powershell
PS C:\myProject> Add-AzurePHPWebRole roleName
```

Dla roli proces roboczy Użyj tego polecenia:

```powershell
PS C:\myProject> Add-AzurePHPWorkerRole roleName
```

> [!NOTE]
> `roleName`Parametr jest opcjonalny. W przypadku pominięcia zostanie wygenerowana automatycznie nazwa roli. Pierwsza utworzona rola sieci Web to `WebRole1` , drugi, `WebRole2` i tak dalej. Zostanie utworzona pierwsza rola procesu roboczego `WorkerRole1` , a druga to `WorkerRole2` i tak dalej.
>
>

## <a name="use-your-own-php-runtime"></a>Korzystanie z własnego środowiska uruchomieniowego PHP

W niektórych przypadkach zamiast wybierać wbudowane środowisko uruchomieniowe PHP i skonfigurować je zgodnie z powyższym opisem, warto udostępnić własne środowisko uruchomieniowe PHP. Na przykład można użyć tego samego środowiska uruchomieniowego PHP w roli sieci Web lub procesu roboczego, która jest używana w środowisku programistycznym. Ułatwia to zapewnienie, że aplikacja nie będzie zmieniać zachowania w środowisku produkcyjnym.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Konfigurowanie roli sieci Web do używania własnego środowiska uruchomieniowego PHP

Aby skonfigurować rolę sieci Web do używania środowiska uruchomieniowego PHP, które jest podane, wykonaj następujące kroki:

1. Utwórz projekt usługi platformy Azure i dodaj rolę sieci Web w języku PHP, jak opisano wcześniej w tym temacie.
2. Utwórz `php` folder w folderze, `bin` który znajduje się w katalogu głównym roli sieci Web, a następnie Dodaj do folderu środowisko uruchomieniowe php (wszystkie dane binarne, pliki konfiguracji, podfoldery itp.) `php` .
3. OBOWIĄZKOWE Jeśli środowisko uruchomieniowe PHP korzysta ze [sterowników firmy Microsoft dla języka PHP dla SQL Server][sqlsrv drivers], należy skonfigurować rolę sieci Web w celu zainstalowania [SQL Server Native Client 2012][sql native client] po zainicjowaniu obsługi administracyjnej. W tym celu należy dodać [ instalatorasqlncli.msi x64] do `bin` folderu w katalogu głównym roli sieci Web. Skrypt uruchamiania opisany w następnym kroku uruchomi Instalatora w trybie dyskretnym, gdy rola zostanie zainicjowana. Jeśli środowisko uruchomieniowe PHP nie korzysta ze sterowników firmy Microsoft dla języka PHP dla SQL Server, można usunąć następujący wiersz ze skryptu pokazanego w następnym kroku:

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. Zdefiniuj zadanie uruchamiania, które konfiguruje [Internet Information Services (IIS)][iis.net] do używania środowiska uruchomieniowego php do obsługi żądań `.php` stron. Aby to zrobić, Otwórz `setup_web.cmd` plik (w `bin` pliku katalogu głównego roli sieci Web) w edytorze tekstów i Zastąp jego zawartość następującym skryptem:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Dodaj pliki aplikacji do katalogu głównego roli sieci Web. Będzie to katalog główny serwera sieci Web.
6. Opublikuj aplikację zgodnie z opisem w sekcji [publikowanie aplikacji](#publish-your-application) poniżej.

> [!NOTE]
> `download.ps1`Skrypt (w `bin` folderze katalogu głównego roli sieci Web) można usunąć po wykonaniu czynności opisanych powyżej przy użyciu własnego środowiska uruchomieniowego php.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurowanie roli procesu roboczego do korzystania z własnego środowiska uruchomieniowego PHP

Aby skonfigurować rolę procesu roboczego do korzystania z środowiska uruchomieniowego PHP, które zapewniasz, wykonaj następujące kroki:

1. Utwórz projekt usługi platformy Azure i dodaj rolę procesu roboczego PHP, jak opisano wcześniej w tym temacie.
2. Utwórz `php` folder w katalogu głównym roli proces roboczy, a następnie Dodaj do tego folderu środowisko uruchomieniowe php (wszystkie dane binarne, pliki konfiguracji, podfoldery itp.) `php` .
3. OBOWIĄZKOWE Jeśli środowisko uruchomieniowe PHP używa [sterowników firmy Microsoft dla języka PHP dla SQL Server][sqlsrv drivers], należy skonfigurować rolę procesu roboczego w celu zainstalowania [SQL Server Native Client 2012][sql native client] po zainicjowaniu obsługi administracyjnej. W tym celu należy dodać [ instalatorasqlncli.msi x64] do katalogu głównego roli procesu roboczego. Skrypt uruchamiania opisany w następnym kroku uruchomi Instalatora w trybie dyskretnym, gdy rola zostanie zainicjowana. Jeśli środowisko uruchomieniowe PHP nie korzysta ze sterowników firmy Microsoft dla języka PHP dla SQL Server, można usunąć następujący wiersz ze skryptu pokazanego w następnym kroku:

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. Zdefiniuj zadanie uruchamiania, które dodaje `php.exe` plik wykonywalny do zmiennej środowiskowej PATH procesu roboczego, gdy rola jest inicjowana. W tym celu Otwórz `setup_worker.cmd` plik (w katalogu głównym roli proces roboczy) w edytorze tekstów i Zastąp jego zawartość następującym skryptem:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Dodaj pliki aplikacji do katalogu głównego roli procesu roboczego.
6. Opublikuj aplikację zgodnie z opisem w sekcji [publikowanie aplikacji](#publish-your-application) poniżej.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Uruchamianie aplikacji w emulatorach obliczeniowych i magazynowych

Emulatory platformy Azure udostępniają lokalne środowisko, w którym można testować aplikację platformy Azure przed wdrożeniem jej w chmurze. Istnieją pewne różnice między emulatorami i środowiskiem platformy Azure. Aby to lepiej zrozumieć, zobacz [Używanie emulatora usługi Azure Storage do programowania i testowania](storage/common/storage-use-emulator.md).

Należy pamiętać, że program PHP jest zainstalowany lokalnie, aby można było korzystać z emulatora obliczeń. Emulator obliczeń będzie używać lokalnej instalacji języka PHP do uruchamiania aplikacji.

Aby uruchomić projekt w emulatorach, wykonaj następujące polecenie w katalogu głównym projektu:

```powershell
PS C:\MyProject> Start-AzureEmulator
```

Zostaną wyświetlone dane wyjściowe podobne do tych: 

```output
Creating local package...
Starting Emulator...
Role is running at http://127.0.0.1:81
Started
```

Twoja aplikacja jest uruchamiana w emulatorze, otwierając przeglądarkę internetową i przechodząc do adresu lokalnego wyświetlanego w danych wyjściowych ( `http://127.0.0.1:81` w przykładowych danych wyjściowych powyżej).

Aby zatrzymać emulatory, wykonaj następujące polecenie:

```powershell
PS C:\MyProject> Stop-AzureEmulator
```

## <a name="publish-your-application"></a>Publikowanie aplikacji

Aby opublikować aplikację, należy najpierw zaimportować ustawienia publikowania za pomocą polecenia cmdlet [Import-AzurePublishSettingsFile](/powershell/module/servicemanagement/azure.service/import-azurepublishsettingsfile) . Następnie można opublikować aplikację przy użyciu polecenia cmdlet [Publish-AzureServiceProject](/powershell/module/servicemanagement/azure.service/publish-azureserviceproject) . Informacje o logowaniu można znaleźć w temacie [How to Install and configure Azure PowerShell](/powershell/azure/).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[Definicja usługi (. csdef)]: /previous-versions/azure/reference/ee758711(v=azure.100)
[Konfiguracja usługi (. cscfg)]: /previous-versions/azure/reference/ee758710(v=azure.100)
[iis.net]: https://www.iis.net/
[sql native client]: /sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[ Instalatorsqlncli.msi x64]: https://go.microsoft.com/fwlink/?LinkID=239648