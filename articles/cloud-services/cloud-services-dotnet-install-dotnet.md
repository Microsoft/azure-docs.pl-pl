---
title: Instalowanie ról platformy .NET na platformie Azure Cloud Services (klasyczne) | Microsoft Docs
description: W tym artykule opisano sposób ręcznej instalacji .NET Framework w roli sieć Web i proces roboczy usługi w chmurze
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: aa05fc9f02c26192762ed34db54b60b4760bf3bf
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99061855"
---
# <a name="install-net-on-azure-cloud-services-classic-roles"></a>Instalowanie ról platformy .NET na platformie Azure Cloud Services (klasyczne)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

W tym artykule opisano sposób instalowania wersji .NET Framework, które nie są dostępne w systemie operacyjnym gościa platformy Azure. Możesz użyć platformy .NET w systemie operacyjnym gościa, aby skonfigurować role sieci Web i proces roboczy usługi w chmurze.

Na przykład można zainstalować .NET Framework 4.6.2 w rodzinie systemu operacyjnego gościa 4, która nie jest dostępna w żadnej wersji .NET Framework 4,6. (Rodzina systemów operacyjnych gościa 5 zawiera .NET Framework 4,6). Aby uzyskać najnowsze informacje na temat wydań systemu operacyjnego gościa platformy Azure, zobacz informacje o [wersji systemu operacyjnego gościa platformy Azure](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Zestaw Azure SDK 2,9 zawiera ograniczenie dotyczące wdrażania .NET Framework 4,6 w rodzinie systemów operacyjnych Gości 4 lub starszej. Poprawka jest dostępna w witrynie [Microsoft docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) .

Aby zainstalować platformę .NET w rolach sieci Web i procesu roboczego, Dołącz Instalatora sieci Web .NET w ramach projektu usługi w chmurze. Uruchom Instalatora w ramach zadań uruchamiania roli. 

## <a name="add-the-net-installer-to-your-project"></a>Dodawanie Instalatora platformy .NET do projektu
Aby pobrać Instalatora sieci Web dla .NET Framework, wybierz wersję, którą chcesz zainstalować:

* [Instalator sieci Web .NET Framework 4,8](https://go.microsoft.com/fwlink/?LinkId=2150985)
* [.NET Framework Instalator sieci Web 4.7.2](https://go.microsoft.com/fwlink/?LinkId=863262)
* [.NET Framework Instalator sieci Web 4.6.2](https://www.microsoft.com/download/details.aspx?id=53345)

Aby dodać Instalatora dla roli *sieci Web* :
  1. W **Eksplorator rozwiązań**, w obszarze **role** w projekcie usługi w chmurze, kliknij prawym przyciskiem myszy rolę *sieci Web* i wybierz polecenie **Dodaj**  >  **Nowy folder**. Utwórz folder o nazwie **bin**.
  2. Kliknij prawym przyciskiem myszy folder bin i wybierz polecenie **Dodaj**  >  **istniejący element**. Wybierz Instalatora .NET i dodaj go do folderu bin.
  
Aby dodać Instalatora dla roli *proces roboczy* :
* Kliknij prawym przyciskiem myszy rolę *procesu roboczego* i wybierz pozycję **Dodaj**  >  **istniejący element**. Wybierz Instalatora .NET i dodaj go do roli. 

Gdy pliki są dodawane w ten sposób do folderu zawartości roli, są one automatycznie dodawane do pakietu usługi w chmurze. Pliki są następnie wdrażane do spójnej lokalizacji na maszynie wirtualnej. Powtórz ten proces dla każdej roli sieci Web i procesu roboczego w usłudze w chmurze, aby wszystkie role miały kopię Instalatora.

> [!NOTE]
> Należy zainstalować .NET Framework 4.6.2 w roli usługi w chmurze, nawet jeśli aplikacja jest przeznaczona dla .NET Framework 4,6. System operacyjny gościa zawiera aktualizację bazy wiedzy [3098779](https://support.microsoft.com/kb/3098779) i [aktualizację 3097997](https://support.microsoft.com/kb/3097997). Problemy mogą wystąpić podczas uruchamiania aplikacji .NET, jeśli .NET Framework 4,6 jest zainstalowany na podstawie aktualizacji bazy wiedzy. Aby uniknąć tych problemów, Zainstaluj .NET Framework 4.6.2, a nie wersję 4,6. Aby uzyskać więcej informacji, zobacz [artykuł w bazie wiedzy 3118750](https://support.microsoft.com/kb/3118750) i [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Zawartość roli z plikami Instalatora][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definiowanie zadań uruchamiania dla ról
Zadania uruchamiania umożliwiają wykonywanie operacji przed rozpoczęciem roli. Zainstalowanie .NET Framework w ramach zadania uruchamiania zapewnia, że struktura jest zainstalowana przed uruchomieniem kodu aplikacji. Aby uzyskać więcej informacji na temat zadań uruchamiania, zobacz [Uruchamianie zadań uruchamiania na platformie Azure](cloud-services-startup-tasks.md). 

1. Dodaj następującą zawartość do pliku ServiceDefinition. csdef w węźle **webrole** lub **rola procesu roboczego** dla wszystkich ról:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    Poprzednia konfiguracja uruchamia polecenie konsoli `install.cmd` z uprawnieniami administratora w celu zainstalowania .NET Framework. Konfiguracja tworzy również element **LOCALSTORAGE** o nazwie **NETFXInstall**. Skrypt uruchamiania ustawia folder tymczasowy do korzystania z tego zasobu magazynu lokalnego. 
    
    > [!IMPORTANT]
    > Aby zapewnić poprawną instalację platformy, należy ustawić rozmiar tego zasobu na co najmniej 1 024 MB.
    
    Aby uzyskać więcej informacji na temat zadań uruchamiania, zobacz [typowe zadania uruchamiania Cloud Services platformy Azure](cloud-services-startup-tasks-common.md).

2. Utwórz plik o nazwie **install. cmd** i Dodaj do pliku następujący skrypt instalacyjny.

   Skrypt sprawdza, czy określona wersja .NET Framework jest już zainstalowana na maszynie przez przeszukiwanie rejestru. Jeśli .NET Framework wersja nie jest zainstalowana, zostanie otwarty Instalator .NET Framework sieci Web. Aby pomóc w rozwiązywaniu wszelkich problemów, skrypt rejestruje wszystkie działania w pliku startuptasklog — (bieżąca data i godzina). txt, który jest przechowywany w magazynie lokalnym **InstallLogs** .
   
   > [!IMPORTANT]
   > Użyj podstawowego edytora tekstu, takiego jak Notatnik systemu Windows, aby utworzyć plik Install. cmd. Jeśli używasz programu Visual Studio do utworzenia pliku tekstowego i zmienisz rozszerzenie na cmd, plik może nadal zawierać znacznik kolejności bajtów UTF-8. Ten znacznik może spowodować błąd podczas uruchamiania pierwszego wiersza skryptu. Aby uniknąć tego błędu, Utwórz pierwszy wiersz skryptu instrukcja REM, która może zostać pominięta przez przetwarzanie kolejności bajtów. 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** https://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** https://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** https://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   REM ***** To install .NET 4.8 set the variable netfx to "NDP48" ***** https://dotnet.microsoft.com/download/thank-you/net48
      
   REM ***** Set script start timestamp *****
   set timehour=%time:~0,2%
   set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
   set "log=install.cmd started %timestamp%."
   
   REM ***** Exit script if running in Emulator *****
   if "%ComputeEmulatorRunning%"=="true" goto exit
   
   REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
   set TMP=%PathToNETFXInstall%
   set TEMP=%PathToNETFXInstall%
   
   REM ***** Setup .NET filenames and registry keys *****
   if %netfx%=="NDP472" goto NDP472
   if %netfx%=="NDP471" goto NDP471
   if %netfx%=="NDP47" goto NDP47
   if %netfx%=="NDP462" goto NDP462
   if %netfx%=="NDP461" goto NDP461
   if %netfx%=="NDP46" goto NDP46
   
   set "netfxinstallfile=NDP452-KB2901954-Web.exe"
   set netfxregkey="0x5cbf5"
   goto logtimestamp
   
   :NDP46
   set "netfxinstallfile=NDP46-KB3045560-Web.exe"
   set netfxregkey="0x6004f"
   goto logtimestamp
   
   :NDP461
   set "netfxinstallfile=NDP461-KB3102438-Web.exe"
   set netfxregkey="0x6040e"
   goto logtimestamp
   
   :NDP462
   set "netfxinstallfile=NDP462-KB3151802-Web.exe"
   set netfxregkey="0x60632"
   goto logtimestamp
   
   :NDP47
   set "netfxinstallfile=NDP47-KB3186500-Web.exe"
   set netfxregkey="0x707FE"
   goto logtimestamp
   
   :NDP471
   set "netfxinstallfile=NDP471-KB4033344-Web.exe"
   set netfxregkey="0x709fc"
   goto logtimestamp
   
   :NDP472
   set "netfxinstallfile=NDP472-KB4054531-Web.exe"
   set netfxregkey="0x70BF6"
   goto logtimestamp
   
   :logtimestamp
   REM ***** Setup LogFile with timestamp *****
   md "%PathToNETFXInstall%\log"
   set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
   set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
   echo %log% >> %startuptasklog%
   echo Logfile generated at: %startuptasklog% >> %startuptasklog%
   echo TMP set to: %TMP% >> %startuptasklog%
   echo TEMP set to: %TEMP% >> %startuptasklog%
   
   REM ***** Check if .NET is installed *****
   echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
   set /A netfxregkeydecimal=%netfxregkey%
   set foundkey=0
   FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
   echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
   if %foundkey% GEQ %netfxregkeydecimal% goto installed
   
   REM ***** Installing .NET *****
   echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
   start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
   if %ERRORLEVEL%== 0 goto installed
       echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
       if %ERRORLEVEL%== 3010 goto restart
       if %ERRORLEVEL%== 1641 goto restart
       echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
       goto exit
       
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   shutdown.exe /r /t 5 /c "Installed .NET framework" /f /d p:2:4
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. Dodaj plik Install. cmd do każdej roli za pomocą polecenia **Dodaj**  >  **istniejący element** w **Eksplorator rozwiązań** zgodnie z opisem we wcześniejszej części tego tematu. 

    Po zakończeniu tego kroku wszystkie role powinny mieć plik Instalatora .NET i plik Install. cmd.

   ![Zawartość roli ze wszystkimi plikami][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Konfigurowanie diagnostyki do transferowania dzienników uruchamiania do magazynu obiektów BLOB
Aby uprościć Rozwiązywanie problemów z instalacją, można skonfigurować Diagnostyka Azure do transferu plików dziennika wygenerowanych przez skrypt uruchamiania lub Instalatora .NET do usługi Azure Blob Storage. Korzystając z tej metody, można wyświetlić dzienniki, pobierając pliki dziennika z magazynu obiektów blob, a nie przyłączając pulpitu zdalnego do roli.


Aby skonfigurować diagnostykę, Otwórz plik Diagnostics. wadcfgx i Dodaj następującą zawartość w węźle **katalogi** : 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Ten plik XML konfiguruje diagnostykę do transferu plików w katalogu dziennika w zasobie **NETFXInstall** do konta magazynu diagnostyki w kontenerze obiektów BLOB **NetFx-Install** .

## <a name="deploy-your-cloud-service"></a>Wdrażanie usługi w chmurze
Podczas wdrażania usługi w chmurze zadania uruchamiania instalują .NET Framework, jeśli nie zostały jeszcze zainstalowane. Twoje role usługi w chmurze są w stanie *zajętym* podczas instalowania platformy. Jeśli instalacja struktury wymaga ponownego uruchomienia, role usługi mogą być również ponownie uruchomione. 

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Instalowanie programu .NET Framework][Installing the .NET Framework]
* [Ustal, które wersje .NET Framework są zainstalowane][How to: Determine Which .NET Framework Versions Are Installed]
* [Rozwiązywanie problemów z instalacjami .NET Framework][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



