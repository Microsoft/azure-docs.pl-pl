---
title: Typowe zadania uruchamiania dla Cloud Services (klasyczne) | Microsoft Docs
description: Zawiera kilka przykładów typowych zadań uruchamiania, które można wykonać w roli sieci Web usług w chmurze lub roli proces roboczy.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: f55b225e615a3e7a5fbcf56b405054883d3b5413
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741200"
---
# <a name="common-cloud-service-classic-startup-tasks"></a>Typowe zadania uruchamiania usługi w chmurze (klasycznej)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

W tym artykule przedstawiono kilka przykładów typowych zadań uruchamiania, które można wykonać w usłudze w chmurze. Zadania uruchamiania umożliwiają wykonywanie operacji przed rozpoczęciem roli. Operacje, które można wykonać, obejmują zainstalowanie składnika, zarejestrowanie składników modelu COM, ustawienie kluczy rejestru lub uruchomienie długotrwałego procesu. 

Zapoznaj się z [tym artykułem](cloud-services-startup-tasks.md) , aby dowiedzieć się, jak działają zadania uruchamiania, oraz w jaki sposób utworzyć wpisy definiujące zadanie uruchamiania.

> [!NOTE]
> Zadania uruchamiania nie mają zastosowania do Virtual Machines tylko do ról Sieć Web i proces roboczy usługi w chmurze.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definiuj zmienne środowiskowe przed rozpoczęciem roli
Jeśli potrzebujesz zmiennych środowiskowych zdefiniowanych dla określonego zadania, użyj elementu [Environment] wewnątrz elementu [Task] .

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Zmienne mogą również używać [prawidłowej wartości XPath platformy Azure](cloud-services-role-config-xpath.md) do odwoływania się do czegoś dotyczącego wdrożenia. Zamiast używać `value` atrybutu, zdefiniuj element podrzędny [RoleInstanceValue] .

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Skonfiguruj uruchamianie usług IIS przy użyciu AppCmd.exe
[AppCmd.exe](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj635852(v=ws.11)) narzędzia wiersza polecenia można użyć do zarządzania ustawieniami usług IIS podczas uruchamiania na platformie Azure. *AppCmd.exe* zapewnia wygodny, w wierszu polecenia dostęp do ustawień konfiguracji, które mogą być używane w zadaniach uruchamiania na platformie Azure. Za pomocą *AppCmd.exe* można dodawać, modyfikować lub usuwać ustawienia witryny sieci Web dla aplikacji i lokacji.

Istnieje jednak kilka rzeczy, które należy obserwować w przypadku używania *AppCmd.exe* jako zadania uruchamiania:

* Zadania uruchamiania można uruchomić więcej niż jeden raz między ponownymi uruchomieniami. Na przykład podczas odtwarzania roli.
* Jeśli akcja *AppCmd.exe* jest wykonywana więcej niż raz, może ona wygenerować błąd. Przykładowo próba dodania sekcji do *Web.config* dwa razy może spowodować wygenerowanie błędu.
* Zadania uruchamiania kończą się niepowodzeniem, jeśli zwracają kod zakończenia inny niż zero lub **ERRORLEVEL**. Na przykład, gdy *AppCmd.exe* generuje błąd.

Dobrym sposobem jest sprawdzenie, czy **zmienna ERRORLEVEL** po wywołaniu *AppCmd.exe*, co jest łatwe do wykonania w przypadku zawijania wywołania do *AppCmd.exe* za pomocą pliku *. cmd* . Jeśli wykryjesz znaną odpowiedź **ERRORLEVEL** , możesz ją zignorować lub przekazać ponownie.

Zmienna ERRORLEVEL zwracana przez *AppCmd.exe* jest wymieniona w pliku Winerror. h i może być również widoczna w [witrynie MSDN](/windows/desktop/Debug/system-error-codes--0-499-).

### <a name="example-of-managing-the-error-level"></a>Przykład zarządzania poziomem błędu
Ten przykład dodaje sekcję kompresji i wpis kompresji dla formatu JSON do pliku *Web.config* , z obsługą błędów i rejestrowaniem.

Poniżej przedstawiono odpowiednie sekcje pliku [ServiceDefinition. csdef] , które obejmują ustawienie atrybutu [kontekście wykonywania](/previous-versions/azure/reference/gg557552(v=azure.100)#task) w `elevated` celu udzielenia *AppCmd.exe* wystarczających uprawnień, aby zmienić ustawienia w pliku *Web.config* :

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Plik wsadowy *Start. cmd* używa *AppCmd.exe* do dodawania sekcji kompresji i wpisu kompresji dla formatu JSON do pliku *Web.config* . Oczekiwana liczba **ERRORLEVEL** z 183 jest ustawiona na zero przy użyciu programu wiersza polecenia VERIFY.EXE. Nieoczekiwane ERRORLEVEL są rejestrowane w StartupErrorLog.txt.

```cmd
REM   **_ Add a compression section to the Web.config file. _*_
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   _*_ Add compression for json. _*_
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   _*_ Exit batch file. _*_
EXIT /b 0

REM   _*_ Log error and exit _*_
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>Dodawanie reguł zapory
Na platformie Azure istnieją efektywne dwie zapory. Pierwsza Zapora kontroluje połączenia między maszyną wirtualną a światem zewnętrznym. Ta zapora jest kontrolowana przez element [Endpoints] w pliku [ServiceDefinition. csdef] .

Druga Zapora kontroluje połączenia między maszyną wirtualną a procesami w ramach tej maszyny wirtualnej. Tę zaporę można kontrolować za pomocą `netsh advfirewall firewall` narzędzia wiersza polecenia.

Platforma Azure tworzy reguły zapory dla procesów uruchomionych w ramach ról. Na przykład po uruchomieniu usługi lub programu Platforma Azure automatycznie tworzy niezbędne reguły zapory, aby umożliwić tej usłudze komunikowanie się z Internetem. Jeśli jednak utworzysz usługę uruchomioną przez proces poza rolą (np. z usługą COM+ lub zaplanowanym zadaniem systemu Windows), musisz ręcznie utworzyć regułę zapory, aby zezwolić na dostęp do tej usługi. Te reguły zapory można utworzyć przy użyciu zadania uruchamiania.

Zadanie uruchamiania, które tworzy regułę zapory, musi mieć zadanie [kontekście wykonywania][] _ * z podwyższonym poziomem uprawnień * *. Dodaj następujące zadanie uruchamiania do pliku [ServiceDefinition. csdef] .

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Aby dodać regułę zapory, należy użyć odpowiednich `netsh advfirewall firewall` poleceń w pliku wsadowym uruchamiania. W tym przykładzie zadanie uruchamiania wymaga zabezpieczeń i szyfrowania dla portu TCP 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Blokuj określony adres IP
Możesz ograniczyć dostęp roli sieci Web platformy Azure do zestawu określonych adresów IP, modyfikując plik **web.config** IIS. Należy również użyć pliku polecenia, który odblokowuje sekcję **ipSecurity** pliku **ApplicationHost.config** .

Aby odblokować sekcję **ipSecurity** pliku **ApplicationHost.config** , Utwórz plik poleceń, który jest uruchamiany podczas uruchamiania roli. Utwórz folder na poziomie głównym roli sieci Web o nazwie **Startup** i, w tym folderze, Utwórz plik wsadowy o nazwie **Startup. cmd**. Dodaj ten plik do projektu programu Visual Studio i ustaw właściwości tak, aby **zawsze** były dostępne w pakiecie.

Dodaj następujące zadanie uruchamiania do pliku [ServiceDefinition. csdef] .

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Dodaj to polecenie do pliku **Start. cmd** :

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

To zadanie powoduje, że plik wsadowy **Start. cmd** ma być uruchamiany za każdym razem, gdy rola sieci Web zostanie zainicjowana, co oznacza, że wymagana sekcja **ipSecurity** jest odblokowana.

Na koniec zmodyfikuj [sekcję system. WebServer](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) **web.config** pliku roli sieci Web, aby dodać listę adresów IP, którym udzielono dostępu, jak pokazano w następującym przykładzie:

Ta Przykładowa konfiguracja **zezwala** wszystkim adresom IP na dostęp do serwera z wyjątkiem dwóch zdefiniowanych

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Ta Przykładowa konfiguracja **odmówi** wszystkim adresom IP uzyskiwania dostępu do serwera z wyjątkiem dwóch zdefiniowanych.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>Tworzenie zadania uruchamiania programu PowerShell
Skryptów programu Windows PowerShell nie można wywołać bezpośrednio z pliku [ServiceDefinition. csdef] , ale mogą one być wywoływane z poziomu pliku wsadowego uruchamiania.

Program PowerShell (domyślnie) nie uruchamia niepodpisanych skryptów. Jeśli skrypt nie zostanie podpisany, należy skonfigurować program PowerShell, aby uruchamiał niepodpisane skrypty. Aby uruchamiać niepodpisane skrypty, **ExecutionPolicy** musi być ustawiona na **nieograniczone**. Używane ustawienie **ExecutionPolicy** jest oparte na wersji programu Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

W przypadku korzystania z systemu operacyjnego gościa z uruchomionym programem PowerShell 2,0 lub 1,0 można wymusić uruchomienie wersji 2, a jeśli jest ona niedostępna, użyj wersji 1.

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Tworzenie plików w magazynie lokalnym z poziomu zadania uruchamiania
Zasobów magazynu lokalnego można użyć do przechowywania plików utworzonych przez zadanie uruchamiania, które są dostępne później przez aplikację.

Aby utworzyć zasób magazynu lokalnego, Dodaj sekcję [LocalResources] do pliku [ServiceDefinition. csdef] , a następnie Dodaj element podrzędny [LOCALSTORAGE] . Nadaj zasobowi lokalnego magazynu unikatową nazwę i odpowiedni rozmiar zadania uruchamiania.

Aby użyć zasobu magazynu lokalnego w ramach zadania uruchamiania, należy utworzyć zmienną środowiskową, aby odwołać się do lokalizacji zasobów magazynu lokalnego. Następnie zadanie uruchamiania i aplikacja mogą odczytywać i zapisywać pliki w lokalnym magazynie zasobów magazynu.

Odpowiednie sekcje pliku **ServiceDefinition. csdef** są przedstawione tutaj:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

Przykładowo plik wsadowy **Start. cmd** używa zmiennej środowiskowej **PathToStartupStorage** , aby utworzyć plik **MyTest.txt** w lokalizacji magazynu lokalnego.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Możesz uzyskać dostęp do lokalnego folderu magazynu z zestawu Azure SDK przy użyciu metody [GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) .

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Uruchom w emulatorze lub w chmurze
Zadanie uruchamiania może wykonywać różne kroki, gdy działa w chmurze w porównaniu z, gdy znajduje się w emulatorze obliczeniowym. Na przykład możesz chcieć użyć nowej kopii danych SQL tylko w przypadku uruchamiania w emulatorze. Można też wykonać pewne optymalizacje wydajności w chmurze, które nie muszą być wykonywane w przypadku uruchamiania w emulatorze.

Możliwość wykonywania różnych akcji na emulatorze obliczeniowym i w chmurze można osiągnąć przez utworzenie zmiennej środowiskowej w pliku [ServiceDefinition. csdef] . Następnie należy przetestować tę zmienną środowiskową dla wartości w zadaniu startowym.

Aby utworzyć zmienną środowiskową, Dodaj element [Variable] / [RoleInstanceValue] i utwórz wartość XPath `/RoleEnvironment/Deployment/@emulated` . Wartość zmiennej środowiskowej **% ComputeEmulatorRunning%** jest `true` uruchamiana w emulatorze obliczeniowym i `false` w przypadku uruchamiania w chmurze.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

Zadanie może teraz sprawdzić zmienną środowiskową **% ComputeEmulatorRunning%** , aby wykonać różne akcje w zależności od tego, czy rola jest uruchomiona w chmurze, czy emulatorze. Oto skrypt powłoki. cmd, który sprawdza, czy ta zmienna środowiskowa.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Wykryj, że zadanie zostało już uruchomione
Rola może odtworzyć się bez ponownego uruchomienia, powodując ponowne uruchomienie zadań uruchamiania. Brak flagi wskazującej, że zadanie zostało już uruchomione na maszynie wirtualnej hostingu. Może istnieć kilka zadań, w których nie ma znaczenia, że są one uruchamiane wiele razy. Może jednak wystąpić w sytuacji, w której trzeba zapobiec uruchamianiu zadania więcej niż jeden raz.

Najprostszym sposobem wykrywania, że zadanie zostało już uruchomione, jest utworzenie pliku w folderze **% temp%** , gdy zadanie zakończy się pomyślnie i poszukaj na początku zadania. Oto przykładowy skrypt powłoki cmd, który robi to za Ciebie.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%PathToApp1Install%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%PathToApp1Install%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>Najlepsze rozwiązania dotyczące zadań
Poniżej przedstawiono niektóre najlepsze rozwiązania, które należy wykonać podczas konfigurowania zadania dla roli sieci Web lub procesu roboczego.

### <a name="always-log-startup-activities"></a>Zawsze Rejestruj działania uruchamiania
Program Visual Studio nie oferuje debugera, który umożliwia przechodzenie do kolejnych plików wsadowych, dlatego warto uzyskać tyle danych na potrzeby operacji plików wsadowych, jak to możliwe. Rejestrowanie danych wyjściowych plików wsadowych, zarówno **stdout** , jak i **stderr**, może dać ważne informacje podczas próby debugowania i naprawiania plików wsadowych. Aby zalogować zarówno **stdout** , jak i **stderr** do pliku StartupLog.txt w katalogu wskazywanym przez zmienną środowiskową **% temp%** , należy dodać tekst `>>  "%TEMP%\\StartupLog.txt" 2>&1` na końcu określonych wierszy, które mają być rejestrowane. Na przykład, aby wykonać setup.exe w katalogu **% PathToApp1Install%** : `"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1`

Aby uprościć kod XML, można utworzyć plik *cmd* otoki, który wywołuje wszystkie zadania uruchamiania programu wraz z rejestrowaniem i zapewnia, że każde zadanie podrzędne udostępnia te same zmienne środowiskowe.

Może się okazać, że jest to irytujące użycie `>> "%TEMP%\StartupLog.txt" 2>&1` na końcu każdego zadania uruchamiania. Rejestrowanie zadań można wymusić, tworząc otokę, która obsługuje logowanie. Ta otoka wywołuje rzeczywisty plik wsadowy, który ma zostać uruchomiony. Wszystkie dane wyjściowe z docelowego pliku wsadowego zostaną przekierowane do pliku *Startuplog.txt* .

Poniższy przykład pokazuje, jak przekierować wszystkie dane wyjściowe z pliku wsadowego uruchamiania. W tym przykładzie plik ServerDefinition. csdef tworzy zadanie uruchamiania wywołujące *logwrap. cmd*. *logwrap. cmd* wywołuje *Startup2. cmd*, przekierowując wszystkie dane wyjściowe do **% temp% \\StartupLog.txt**.

ServiceDefinition. cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap. cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Startup2. cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

Przykładowe dane wyjściowe w pliku **StartupLog.txt** :

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> Plik **StartupLog.txt** znajduje się w folderze *C:\Resources\temp \\ {rolename} \RoleTemp* .
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Ustaw kontekście wykonywania odpowiednio dla zadań uruchamiania
Ustaw uprawnienia odpowiednio dla zadania uruchamiania. Czasami zadania uruchamiania muszą być uruchamiane z podniesionymi uprawnieniami nawet wtedy, gdy rola jest uruchomiona z normalnymi uprawnieniami.

Atrybut [][zadania] kontekście wykonywania ustawia poziom uprawnień zadania uruchamiania. Użycie `executionContext="limited"` oznacza, że zadanie uruchamiania ma ten sam poziom uprawnień co rola. Użycie `executionContext="elevated"` oznacza, że zadanie uruchamiania ma uprawnienia administratora, co umożliwia zadanie uruchamiania wykonywanie zadań administratora bez nadawania uprawnień administratora do roli.

Przykładem zadania uruchamiania, które wymaga podniesionych uprawnień, jest zadanie uruchamiania, które używa **AppCmd.exe** do konfigurowania usług IIS. **AppCmd.exe** wymaga `executionContext="elevated"` .

### <a name="use-the-appropriate-tasktype"></a>Użyj odpowiedniego zadania
Atrybut [][zadania] TaskType określa sposób wykonywania zadania uruchamiania. Istnieją trzy wartości: **proste**, **tła** i **pierwszego planu**. Zadania tła i pierwszego planu są uruchamiane asynchronicznie, a następnie proste zadania są wykonywane synchronicznie po jednym naraz.

Przy użyciu **prostych** zadań uruchamiania można ustawić kolejność wykonywania zadań w kolejności, w której zadania są wyświetlane w pliku ServiceDefinition. csdef. Jeśli **proste** zadanie kończy się niezerowym kodem zakończenia, procedura uruchamiania zostaje zatrzymana, a rola nie zostanie uruchomiona.

Różnica między zadaniami uruchamiania w **tle** i zadaniami uruchamiania **pierwszego planu** polega na tym, że zadania na **pierwszym planie** zachowują rolę działającą do momentu zakończenia zadania **pierwszego planu** . Oznacza to również, że jeśli zadanie **pierwszego planu** zawiesza się lub ulega awarii, rola nie będzie odtwarzana do momentu wymuszonego zamknięcia zadania na **pierwszym planie** . Z tego powodu zadania w **tle** są zalecane w przypadku asynchronicznych zadań uruchamiania, chyba że jest potrzebna funkcja zadania **pierwszego planu** .

### <a name="end-batch-files-with-exit-b-0"></a>Zakończ pliki wsadowe z opcją EXIT/B 0
Rola zostanie uruchomiona tylko wtedy, gdy **zmienna ERRORLEVEL** z każdego prostego zadania uruchamiania ma wartość zero. Nie wszystkie programy poprawnie ustawiają **ERRORLEVEL** (kod zakończenia), więc plik wsadowy powinien kończyć się, `EXIT /B 0` Jeśli wszystko działa prawidłowo.

Brak `EXIT /B 0` na końcu pliku wsadowego uruchamiania jest powszechną przyczyną ról, które nie są uruchamiane.

> [!NOTE]
> Zauważyliśmy, że zagnieżdżone pliki wsadowe czasami przestają odpowiadać przy użyciu `/B` parametru. Warto upewnić się, że ten problem nie występuje, jeśli inny plik wsadowy wywołuje bieżący plik wsadowy, na przykład jeśli używasz [otoki dziennika](#always-log-startup-activities). `/B`W tym przypadku można pominąć parametr.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Oczekiwanie na uruchomienie zadań uruchamiania więcej niż raz
Nie wszystkie oddziałania ról obejmują ponowny rozruch, ale wszystkie odtwarzanie ról obejmują uruchamianie wszystkich zadań uruchamiania. Oznacza to, że zadania uruchamiania muszą być w stanie uruchamiać wiele razy między ponownymi uruchomieniami bez jakichkolwiek problemów. Ta sekcja została omówiona w [poprzedniej sekcji](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Przechowywanie plików, do których należy uzyskać dostęp w roli, przy użyciu magazynu lokalnego
Jeśli chcesz skopiować lub utworzyć plik podczas zadania uruchamiania, które jest następnie dostępne dla roli, ten plik musi być umieszczony w magazynie lokalnym. Zapoznaj się z [poprzednią sekcją](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Następne kroki
Przejrzyj [model i pakiet usługi w](cloud-services-model-and-package.md) chmurze

Dowiedz się więcej na temat działania [zadań](cloud-services-startup-tasks.md) .

[Utwórz i Wdróż](cloud-services-how-to-create-deploy-portal.md) pakiet usługi w chmurze.

[ServiceDefinition. csdef]: cloud-services-model-and-package.md#csdef
[Zadanie]: /previous-versions/azure/reference/gg557552(v=azure.100)#Task
[Startup]: /previous-versions/azure/reference/gg557552(v=azure.100)#Startup
[Runtime]: /previous-versions/azure/reference/gg557552(v=azure.100)#Runtime
[Środowisko]: /previous-versions/azure/reference/gg557552(v=azure.100)#Environment
[Zmienna]: /previous-versions/azure/reference/gg557552(v=azure.100)#Variable
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue
[RoleEnvironment]: /previous-versions/azure/reference/ee773173(v=azure.100)
[Punkty końcowe]: /previous-versions/azure/reference/gg557552(v=azure.100)#Endpoints
[LocalStorage]: /previous-versions/azure/reference/gg557552(v=azure.100)#LocalStorage
[LocalResources]: /previous-versions/azure/reference/gg557552(v=azure.100)#LocalResources
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue