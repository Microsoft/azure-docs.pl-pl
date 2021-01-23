---
title: Wprowadzenie do języka Python i platformy Azure Cloud Services (wersja klasyczna) | Microsoft Docs
description: Omówienie sposobu używania programu Python Tools for Visual Studio do tworzenia usług w chmurze platformy Azure, w tym ról Sieć Web i Proces roboczy.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 16aa6918c0f4b0df5ebf23f28268f8cbe5223fce
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743291"
---
# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Role Sieć Web i Proces roboczy języka Python z programem Python Tools for Visual Studio

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Ten artykuł zawiera omówienie sposobu użycia ról Sieć Web i Proces roboczy języka Python za pomocą programu [Python Tools for Visual Studio][Python Tools for Visual Studio]. Dowiedz się, jak używać programu Visual Studio do tworzenia i wdrażania podstawowej usługi w chmurze, która używa języka Python.

## <a name="prerequisites"></a>Wymagania wstępne
* [Visual Studio 2013, 2015 lub 2017](https://www.visualstudio.com/)
* [Python Tools for Visual Studio][Python Tools for Visual Studio] (PTVS)
* [Azure SDK Tools for VS 2013][Azure SDK Tools for VS 2013] lub  
[Azure SDK Tools for VS 2015][Azure SDK Tools for VS 2015] lub  
[Azure SDK Tools for VS 2017][Azure SDK Tools for VS 2017]
* [32-bitowe środowisko Python w wersji 2.7][Python 2.7 32-bit] lub [32-bitowe środowisko Python w wersji 3.5][Python 3.5 32-bit]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>Co to są role Sieć Web i Proces roboczy języka Python?
Platforma Azure udostępnia trzy modele obliczeniowe na potrzeby uruchamiania aplikacji: [funkcja Web Apps w usłudze Azure App Service][execution model-web sites], [Azure Virtual Machines][execution model-vms] i [Azure Cloud Services][execution model-cloud services]. Wszystkie trzy modele obsługują język Python. Usługi Cloud Services, które obejmują role Sieć Web i Proces roboczy, udostępniają rozwiązanie typu *Platforma jako usługa (Platform as a Service, PaaS)*. W ramach usługi w chmurze rola internetowa zapewnia dedykowany serwer internetowy usług Internet Information Services (IIS), natomiast rola procesu roboczego może uruchamiać asynchroniczne, długotrwałe lub ciągłe zadania niezależne od działań użytkownika lub danych wejściowych.

Aby uzyskać więcej informacji, zobacz [Co to jest usługa w chmurze?]

> [!NOTE]
> *Chcesz utworzyć prostą witrynę sieci Web?*
> Jeśli scenariusz obejmuje tylko prosty fronton witryny internetowej, rozważ użycie lekkiej funkcji Web Apps w usłudze App Service. Możesz łatwo przeprowadzić uaktualnienie do usługi w chmurze w przypadku rozwoju witryny sieci Web lub zmiany wymagań. W [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/) można znaleźć artykuły, które dotyczą funkcji Web Apps w usłudze App Service.
> <br />
> 
> 

## <a name="project-creation"></a>Tworzenie projektu
W programie Visual Studio możesz wybrać pozycję **Usługa w chmurze platformy Azure** w oknie dialogowym **Nowy projekt** w obszarze **Python**.

![Okno dialogowe Nowy projekt](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

W Kreatorze usługi w chmurze platformy Azure można utworzyć nowe role Sieć Web i Proces roboczy.

![Okno dialogowe Usługa w chmurze platformy Azure](./media/cloud-services-python-ptvs/new-service-wizard.png)

Szablon roli procesu roboczego zawiera schematyczny kod służący do nawiązywania połączeń z kontem magazynu Azure lub z usługą Azure Service Bus.

![Rozwiązanie usługi w chmurze](./media/cloud-services-python-ptvs/worker.png)

W każdej chwili możliwe jest dodanie roli Sieć Web lub Proces roboczy do istniejącej usługi w chmurze.  Możesz dodawać istniejące projekty do rozwiązania lub tworzyć nowe.

![Polecenie Dodaj rolę](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Usługa w chmurze może zawierać role zaimplementowane w różnych językach.  Na przykład można mieć rolę Sieć Web języka Python zaimplementowaną za pomocą środowiska Django, języka Python lub roli Proces roboczy języka C#.  Między rolami można się w łatwy sposób komunikować za pomocą kolejek usługi Service Bus lub kolejek magazynu.

## <a name="install-python-on-the-cloud-service"></a>Instalowanie języka Python w usłudze w chmurze
> [!WARNING]
> Skrypty instalacji instalowane z programem Visual Studio (w momencie ostatniej aktualizacji artykułu) nie działają. W tej sekcji opisano sposób obejścia problemu.
> 
> 

Główny problem ze skryptami instalacji polega na tym, że nie instalują one środowiska Python. Najpierw należy zdefiniować dwa [zadania uruchamiania](cloud-services-startup-tasks.md) w pliku [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef). Pierwsze zadanie (**PrepPython.ps1**) pobiera i instaluje środowiska uruchomieniowe języka Python. Drugie zadanie (**PipInstaller.ps1**) uruchamia mechanizm pip, aby zainstalować wszystkie zależności.

Poniższe skrypty zostały napisane dla języka Python 3.5. Jeśli chcesz korzystać z wersji 2.x języka Python, ustaw plik zmiennej **PYTHON2** na **on** dla dwóch zadań uruchamiania i zadania środowiska uruchomieniowego: `<Variable name="PYTHON2" value="<mark>on</mark>" />`.

```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>

  </Task>

</Startup>
```

Zmienne **PYTHON2** i **PYPATH** muszą zostać dodane do zadania uruchamiania procesu roboczego. Zmienna **PYPATH** jest używana tylko wtedy, gdy zmienna **PYTHON2** jest ustawiona na wartość **on**.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>Przykładowy plik ServiceDefinition.csdef
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



Następnie należy utworzyć pliki **PrepPython.ps1** i **PipInstaller.ps1** w folderze **./bin** roli użytkownika.

#### <a name="preppythonps1"></a>PrepPython.ps1
Ten skrypt instaluje język Python. Jeśli zmienna środowiskowa **PYTHON2** jest ustawiona na wartość **on**, jest zainstalowane środowisko Python 2.7. W przeciwnym razie jest zainstalowane środowisko Python 3.5.

```powershell
[Net.ServicePointManager]::SecurityProtocol = "tls12, tls11, tls"
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
        $outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
        }

        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>PipInstaller.ps1
Ten skrypt wywołuje kod pip i instaluje wszystkie zależności w pliku **requirements.txt**. Jeśli zmienna środowiskowa **PYTHON2** jest ustawiona na wartość **on**, jest używane środowisko Python 2.7. W przeciwnym razie jest używane środowisko Python 3.5.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>Modyfikowanie skryptu LaunchWorker.ps1
> [!NOTE]
> W przypadku projektu **roli procesu roboczego** plik **LauncherWorker.ps1** jest wymagany do wykonania pliku uruchamiania. W projektach **roli sieci Web** plik uruchamiania jest zdefiniowany we właściwościach projektu.
> 
> 

Skrypt **Bin\LaunchWorker.ps1** pierwotnie został utworzony w celu wykonywania działań przygotowawczych, ale w praktyce nie działa. Zastąp zawartość tego pliku następującym skryptem.

Ten skrypt wywołuje plik **worker.py** z projektu języka Python. Jeśli zmienna środowiskowa **PYTHON2** jest ustawiona na wartość **on**, jest używane środowisko Python 2.7. W przeciwnym razie jest używane środowisko Python 3.5.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>ps.cmd
Szablony Visual Studio powinny utworzyć plik **ps.cmd** w folderze **./bin**. Ten skrypt powłoki wywołuje powyższe skrypty otoki PowerShell i zapewnia rejestrowanie na podstawie nazwy wywołanej otoki PowerShell. Jeśli ten plik nie został utworzony, jego zawartość powinna wyglądać następująco. 

```cmd
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Uruchamianie lokalnie
Jeśli ustawisz projekt usługi w chmurze jako projekt startowy i naciśniesz klawisz F5, usługa w chmurze zostanie uruchomiona w lokalnym emulatorze platformy Azure.

Mimo że program PTVS obsługuje uruchamianie w emulatorze, debugowanie nie działa (dotyczy to na przykład punktów przerwania).

Aby debugować role Sieć Web i Proces roboczy, możesz ustawić projekt roli jako projekt startowy i debugować go zamiast ról.  Można również ustawić wiele projektów startowych.  Kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Ustaw projekty startowe**.

![Właściwości projektu startowego rozwiązania](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure
Aby przeprowadzić publikowanie, kliknij prawym przyciskiem myszy projekt usługi w chmurze w rozwiązaniu, a następnie wybierz pozycję **Publikuj**.

![Logowanie na potrzeby publikowania na platformie Microsoft Azure](./media/cloud-services-python-ptvs/publish-sign-in.png)

Postępuj zgodnie z poleceniami kreatora. Jeśli trzeba, włącz pulpit zdalny. Pulpit zdalny jest przydatny w przypadku konieczności debugowania elementów.

Po zakończeniu konfigurowania ustawień kliknij pozycję **Publikuj**.

W oknie danych wyjściowych jest wyświetlany postęp, a następnie zostanie wyświetlone okno Dziennik aktywności platformy Microsoft Azure.

![Okno Dziennik aktywności platformy Microsoft Azure](./media/cloud-services-python-ptvs/publish-activity-log.png)

Wdrożenie potrwa kilka minut, a następnie rola internetowa i/lub procesu roboczego będą działać na platformie Azure.

### <a name="investigate-logs"></a>Sprawdzanie dzienników
Po uruchomieniu maszyny wirtualnej usługi w chmurze i zainstalowaniu języka Python można sprawdzić dzienniki pod kątem komunikatów o błędach. Te dzienniki znajdują się w folderze **folderze c:\resources\directory \\ {role} \LogFiles** . Plik **PrepPython.err.txt** zawiera co najmniej jeden błąd zwracany, gdy skrypt próbuje wykryć instalację środowiska Python, a plik **PipInstaller.err.txt** może zgłaszać błąd nieaktualnej wersji kodu pip.

## <a name="next-steps"></a>Następne kroki
Bardziej szczegółowe informacje na temat pracy z rolami Sieć Web i Proces roboczy w ramach programu Python Tools for Visual Studio zawiera dokumentacja programu PTVS:

* [Projekty usługi w chmurze][Cloud Service Projects]

Więcej szczegółów dotyczących korzystania z usług Azure na podstawie roli internetowej i roli procesu roboczego, na przykład używania usługi Azure Storage lub Service Bus, można znaleźć w następujących artykułach:

* [Usługa BLOB][Blob Service]
* [Table Service][Table Service]
* [Queue Service][Queue Service]
* [Kolejki Service Bus][Service Bus Queues]
* [Tematy Service Bus][Service Bus Topics]

<!--Link references-->

[Co to jest usługa w chmurze?]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service/overview.md
[execution model-vms]:../virtual-machines/windows/overview.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Blob Service]:../storage/blobs/storage-python-how-to-use-blob-storage.md
[Queue Service]: ../storage/queues/storage-python-how-to-use-queue-storage.md
[Table Service]:../cosmos-db/table-storage-how-to-use-python.md
[Service Bus Queues]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Service Bus Topics]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools for Visual Studio]: https://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: /visualstudio/python/
[Cloud Service Projects]: /visualstudio/python/python-azure-cloud-service-project-template
[Azure SDK Tools for VS 2013]: https://go.microsoft.com/fwlink/?LinkId=746482
[Azure SDK Tools for VS 2015]: https://go.microsoft.com/fwlink/?LinkId=746481
[Azure SDK Tools for VS 2017]: https://go.microsoft.com/fwlink/?LinkId=746483
[Python 2.7 32-bit]: https://www.python.org/downloads/
[Python 3.5 32-bit]: https://www.python.org/downloads/