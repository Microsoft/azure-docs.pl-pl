---
title: Konfigurowanie dołączania skryptów programu PowerShell w systemie Windows Virtual Desktop MSIX — Azure
description: Jak utworzyć skrypty programu PowerShell na potrzeby dołączania aplikacji MSIX dla pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f625b7dd68d4b5a5e1af68aeb53dac453ff8cbfd
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400832"
---
# <a name="create-powershell-scripts-for-msix-app-attach-preview"></a>Tworzenie skryptów programu PowerShell na potrzeby dołączania aplikacji MSIX (wersja zapoznawcza)

> [!IMPORTANT]
> Dołączenie do aplikacji MSIX jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym temacie opisano sposób konfigurowania skryptów programu PowerShell na potrzeby dołączania aplikacji MSIX.

>[!IMPORTANT]
>Przed rozpoczęciem upewnij się, że wypełniasz i prześlesz [ten formularz](https://aka.ms/enablemsixappattach) , aby umożliwić dołączenie aplikacji MSIX do subskrypcji. Jeśli nie masz zatwierdzonego żądania, dołączenie do aplikacji MSIX nie będzie działało. Zatwierdzenie żądań może potrwać do 24 godzin w dniach roboczych. Po zaakceptowaniu i zakończeniu żądania otrzymasz wiadomość e-mail.

## <a name="install-certificates"></a>Instalowanie certyfikatów

Należy zainstalować certyfikaty na wszystkich hostach sesji w puli hostów, które będą hostować punkty dostępu z dołączanych pakietów aplikacji MSIX.

Jeśli aplikacja używa certyfikatu, który nie jest zaufany lub został podpisany z podpisem własnym, poniżej przedstawiono sposób jego instalacji:

1. Kliknij prawym przyciskiem myszy pakiet i wybierz polecenie **Właściwości**.
2. W wyświetlonym oknie Wybierz kartę **podpisy cyfrowe** . Na karcie powinna znajdować się tylko jeden element, jak pokazano na poniższej ilustracji. Wybierz ten element, aby wyróżnić element, a następnie wybierz pozycję **szczegóły**.
3. Po wyświetleniu okna Szczegóły podpisu cyfrowego wybierz kartę **Ogólne** , a następnie wybierz pozycję **Wyświetl certyfikat**, a następnie wybierz pozycję **Zainstaluj certyfikat**.
4. Po otwarciu Instalatora wybierz pozycję **komputer lokalny** jako lokalizację magazynu, a następnie wybierz pozycję **dalej**.
5. Jeśli Instalator wyświetli monit z pytaniem, czy chcesz zezwolić aplikacji na wprowadzanie zmian na urządzeniu, wybierz opcję **tak**.
6. Wybierz pozycję **Umieść wszystkie certyfikaty w następującym magazynie**, a następnie wybierz pozycję **Przeglądaj**.
7. Po wyświetleniu okna Wybieranie magazynu certyfikatów wybierz pozycję **zaufane osoby**, a następnie wybierz przycisk **OK**.
8. Wybierz pozycję **dalej** i **Zakończ**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Przygotuj skrypty programu PowerShell do dołączenia do aplikacji MSIX

Dołączenie do aplikacji MSIX ma cztery różne etapy, które należy wykonać w następującej kolejności:

1. Etap
2. Zarejestruj
3. Wyrejestrowania
4. Cofnij przygotowanie

Każda faza tworzy skrypt programu PowerShell. Przykładowe skrypty dla każdej fazy są dostępne [tutaj](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-powershell-script"></a>Skrypt programu PowerShell na etapie

Przed aktualizacją skryptów programu PowerShell upewnij się, że masz identyfikator GUID woluminu woluminu na dysku VHD. Aby uzyskać identyfikator GUID woluminu:

1.  Otwórz udział sieciowy, w którym znajduje się wirtualny dysk twardy na maszynie wirtualnej, na której zostanie uruchomiony skrypt.

2.  Kliknij prawym przyciskiem myszy dysk VHD i wybierz polecenie **Zainstaluj**. Spowoduje to zainstalowanie wirtualnego dysku twardego na literę dysku.

3.  Po zainstalowaniu dysku VHD zostanie otwarte okno **Eksplorator plików** . Przechwyć folder nadrzędny i zaktualizuj zmienną **$parentFolder**

    >[!NOTE]
    >Jeśli nie widzisz folderu nadrzędnego, oznacza to, że MSIX nie został prawidłowo rozwinięty. Powtórz poprzednią sekcję i spróbuj ponownie.

4.  Otwórz folder nadrzędny. Jeśli jest prawidłowo rozwinięty, zobaczysz folder o tej samej nazwie co pakiet. Zaktualizuj zmienną **$PackageName** , aby odpowiadała nazwie tego folderu.

    Na przykład `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Otwórz wiersz polecenia i wprowadź polecenie **mountvol**. To polecenie spowoduje wyświetlenie listy woluminów i ich identyfikatorów GUID. Skopiuj identyfikator GUID woluminu, na którym litera dysku jest zgodna z dyskiem, który został zainstalowany w kroku 2.

    Na przykład w tym przykładzie dane wyjściowe polecenia mountvol, jeśli dysk VHD został zainstalowany na dysku C, należy skopiować powyższą wartość `C:\` :

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    **_ NO MOUNT POINTS _*_

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Zaktualizuj zmienną _ *$volumeGuid** za pomocą właśnie SKOPIOWANEGO identyfikatora GUID woluminu.

7. Otwórz wiersz administracyjny programu PowerShell i zaktualizuj następujący skrypt programu PowerShell przy użyciu zmiennych, które są stosowane do danego środowiska.

    ```powershell
    #MSIX app attach staging sample

    #region variables
    $vhdSrc="<path to vhd>"
    $packageName = "<package name>"
    $parentFolder = "<package parent folder>"
    $parentFolder = "\" + $parentFolder + "\"
    $volumeGuid = "<vol guid>"
    $msixJunction = "C:\temp\AppAttach\"
    #endregion

    #region mountvhd
    try
    {
          Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly
          Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green
    }
    catch
    {
          Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red
    }
    #endregion

    #region makelink
    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"
    if (!(Test-Path $msixJunction))
    {
         md $msixJunction
    }

    $msixJunction = $msixJunction + $packageName
    cmd.exe /c mklink /j $msixJunction $msixDest
    #endregion

    #region stage
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>Zarejestruj skrypt programu PowerShell

Aby uruchomić skrypt rejestru, uruchom następujące polecenia cmdlet programu PowerShell z wartościami zastępczymi zamienionymi na wartości, które dotyczą danego środowiska.

```powershell
#MSIX app attach registration sample

#region variables
$packageName = "<package name>"
$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"
#endregion

#region register
Add-AppxPackage -Path $path -DisableDevelopmentMode -Register
#endregion
```

### <a name="deregister-powershell-script"></a>Wyrejestrowywanie skryptu programu PowerShell

Dla tego skryptu Zastąp symbol zastępczy **$PackageName** nazwą testowanego pakietu.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>Skrypt zrzutów programu PowerShell

Dla tego skryptu Zastąp symbol zastępczy **$PackageName** nazwą testowanego pakietu. W przypadku wdrożenia produkcyjnego najlepiej jest uruchomić to przy zamykaniu.

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Konfigurowanie skryptów symulacji dla agenta dołączania aplikacji MSIX

Po utworzeniu skryptów użytkownicy mogą ręcznie uruchamiać je lub konfigurować do uruchamiania automatycznie jako skrypty uruchamiania, logowania, wylogowywania i zamykania. Aby dowiedzieć się więcej na temat tych typów skryptów, zobacz [Używanie skryptów uruchamiania, zamykania, logowania i wylogowywania w zasady grupy](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

W każdym z tych skryptów automatycznych działa jedna faza dołączania skryptów:

- Skrypt uruchamiania uruchamia skrypt etapowy.
- Skrypt logowania uruchamia skrypt rejestru.
- Skrypt wylogowywania uruchamia skrypt wyrejestrowywania.
- Skrypt zamykania uruchamia skrypt destage.

## <a name="use-packages-offline"></a>Korzystanie z pakietów w trybie offline

Jeśli używasz pakietów z [Microsoft Store dla firm](https://businessstore.microsoft.com/) lub [Microsoft Store do edukacji](https://educationstore.microsoft.com/) w sieci lub na urządzeniach, które nie są połączone z Internetem, musisz pobrać licencje pakietów z Microsoft Store i zainstalować je na urządzeniu, aby pomyślnie uruchomić aplikację. Jeśli urządzenie jest w trybie online i może nawiązać połączenie z Microsoft Store dla firm, wymagane licencje należy pobrać automatycznie, ale jeśli jesteś w trybie offline, musisz ręcznie skonfigurować licencje.

Aby zainstalować pliki licencji, należy użyć skryptu programu PowerShell, który wywołuje klasę MDM_EnterpriseModernAppManagement_StoreLicenses02_01 w dostawcy mostka WMI.

Poniżej przedstawiono sposób konfigurowania licencji do użytku w trybie offline:

1. Pobierz pakiet aplikacji, licencje i wymagane platformy z Microsoft Store dla firm. Wymagane są zarówno zakodowane, jak i niezakodowane pliki licencji. Szczegółowe instrukcje dotyczące pobierania można znaleźć [tutaj](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Zaktualizuj następujące zmienne w skrypcie dla kroku 3:
      1. `$contentID` jest wartością identyfikatorze z niezakodowanego pliku licencji (XML). Możesz otworzyć plik licencji w wybranym edytorze tekstu.
      2. `$licenseBlob` to cały ciąg dla obiektu BLOB licencji w zakodowanym pliku licencji (bin). Możesz otworzyć zakodowany plik licencji w wybranym edytorze tekstu.
3. Uruchom następujący skrypt z wiersza polecenia administratora programu PowerShell. Dobrym miejscem do przeprowadzenia instalacji licencji jest koniec [skryptu przemieszczania](#stage-powershell-script) , który również należy uruchomić z poziomu monitu administratora.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param)


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}
```

## <a name="next-steps"></a>Następne kroki

Ta funkcja nie jest obecnie obsługiwana, ale możesz zadawać pytania do społeczności w [TechCommunity pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Możesz również wystawić opinię na temat pulpitu wirtualnego systemu Windows w [centrum opinii pulpitu wirtualnego systemu Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
