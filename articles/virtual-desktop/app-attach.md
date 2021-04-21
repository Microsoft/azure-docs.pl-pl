---
title: Konfigurowanie Windows Virtual Desktop MSIX dołączania skryptów programu PowerShell — Azure
description: Jak tworzyć skrypty programu PowerShell dla dołączania aplikacji MSIX dla Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 43a8cb00804927784982999db13ee193c34f55ca
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835384"
---
# <a name="create-powershell-scripts-for-msix-app-attach"></a>Tworzenie skryptów programu PowerShell dla dołączania aplikacji MSIX

W tym temacie opisano sposób skonfigurowania skryptów programu PowerShell dla dołączania aplikacji MSIX.

## <a name="install-certificates"></a>Instalowanie certyfikatów

Certyfikaty należy zainstalować na wszystkich hostach sesji w puli hostów, które będą hostować aplikacje z pakietów dołączania aplikacji MSIX.

Jeśli aplikacja używa certyfikatu, który nie jest zaufany publicznie lub został z podpisem własnym, oto jak go zainstalować:

1. Kliknij prawym przyciskiem myszy pakiet i wybierz polecenie **Właściwości.**
2. W wyświetlonym oknie wybierz **kartę Podpisy** cyfrowe. Na liście na karcie powinien być tylko jeden element. Wybierz ten element, aby wyróżnić element, a następnie wybierz pozycję **Szczegóły.**
3. Po wyświetleniu okna szczegółów podpisu cyfrowego wybierz **kartę Ogólne,** a następnie wybierz pozycję **Wyświetl certyfikat,** a następnie wybierz **pozycję Zainstaluj certyfikat.**
4. Po otworeniu instalatora wybierz **maszynę lokalną** jako lokalizację magazynu, a następnie wybierz pozycję **Dalej.**
5. Jeśli instalator zapyta, czy chcesz zezwolić aplikacji na zmianę urządzenia, wybierz pozycję **Tak.**
6. Wybierz **pozycję Umieść wszystkie certyfikaty w następującym magazynie,** a następnie wybierz pozycję **Przeglądaj.**
7. Po wyświetlonym oknie wybierania magazynu certyfikatów wybierz pozycję **Zaufane osoby,** a następnie wybierz przycisk **OK.**
8. Wybierz **pozycję Dalej** i **Zakończ.**

## <a name="enable-microsoft-hyper-v"></a>Włącz Microsoft Hyper-V

Microsoft Hyper-V musi być włączona, ponieważ polecenie jest potrzebne do etapu `Mount-VHD` i jest potrzebne do `Dismount-VHD` cokołu.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>Ta zmiana będzie wymagać ponownego uruchomienia maszyny wirtualnej.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Przygotowywanie skryptów programu PowerShell do dołączania aplikacji MSIX

Dołączanie aplikacji MSIX ma cztery odrębne fazy, które muszą być wykonywane w następującej kolejności:

1. Etap
2. Zarejestruj
3. Wyrejestrować
4. Destage (Destage)

Każda faza tworzy skrypt programu PowerShell. Przykładowe skrypty dla każdej fazy są [dostępne tutaj.](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)

### <a name="stage-powershell-script"></a>Skrypt programu PowerShell dla etapu

Przed aktualizacją skryptów programu PowerShell upewnij się, że masz identyfikator GUID woluminu na dysku VHD. Aby uzyskać identyfikator GUID woluminu:

1.  Otwórz udział sieciowy, w którym znajduje się wirtualny dysk twardy wewnątrz maszyny wirtualnej, na której zostanie uruchomiony skrypt.

2.  Kliknij prawym przyciskiem myszy wirtualny dysk twardy i wybierz polecenie **Zainstaluj.** Spowoduje to zamontowanie dysku VHD na literze dysku.

3.  Po instalacji wirtualnego dysku twardego **zostanie Eksplorator plików** okno instalacji. Przechwyć folder nadrzędny i zaktualizuj **zmienną $parentFolder** nadrzędnej

    >[!NOTE]
    >Jeśli nie widzisz folderu nadrzędnego, oznacza to, że plik MSIX nie został prawidłowo rozwinięty. Ponownie w poprzedniej sekcji spróbuj ponownie.

4.  Otwórz folder nadrzędny. Jeśli rozszerzenie jest poprawnie rozwinięte, zobaczysz folder o takiej samej nazwie jak pakiet. Zaktualizuj **zmienną $packageName,** aby była dopasowana do nazwy tego folderu.

    Na przykład `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Otwórz wiersz polecenia i wprowadź **mountvol**. To polecenie spowoduje wyświetlenie listy woluminów i ich identyfikatorów GUID. Skopiuj identyfikator GUID woluminu, na którym litera dysku odpowiada dyskowi zainstalowanemu na dysku VHD w kroku 2.

    Na przykład w tych przykładowych danych wyjściowych polecenia mountvol, jeśli dysk VHD został zainstalowany na dysku C, należy skopiować wartość powyżej `C:\` :

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Zaktualizuj **zmienną $volumeGuid** skopiowanym identyfikatorem GUID woluminu.

7. Otwórz wiersz polecenia programu PowerShell dla administratora i zaktualizuj następujący skrypt programu PowerShell przy użyciu zmiennych, które mają zastosowanie do twojego środowiska.

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

### <a name="register-powershell-script"></a>Rejestrowanie skryptu programu PowerShell

Aby uruchomić skrypt rejestru, uruchom następujące polecenia cmdlet programu PowerShell z wartościami zastępczymi zastąpionymi wartościami, które dotyczą Twojego środowiska.

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

### <a name="deregister-powershell-script"></a>Wyrejestrowanie skryptu programu PowerShell

W przypadku tego skryptu zastąp symbol zastępczy **$packageName** nazwą testowego pakietu.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>Destage PowerShell script (Destage PowerShell script)

W przypadku tego skryptu zastąp symbol zastępczy **$packageName** nazwą testowego pakietu. W przypadku wdrożenia produkcyjnego najlepszym rozwiązaniem byłoby uruchomienie go podczas zamykania.

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

Po utworzeniu skryptów użytkownicy mogą je ręcznie uruchomić lub skonfigurować do automatycznego uruchamiania, logowania, wylogowywania i zamykania skryptów. Aby dowiedzieć się więcej o tych typach skryptów, zobacz [Using startup, shutdown, logon, and logoff scripts in zasady grupy](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)(Używanie skryptów uruchamiania, zamykania, logowania i wylogowywania) w zasady grupy .

Każdy z tych skryptów automatycznych uruchamia jedną fazę dołączania skryptów aplikacji:

- Skrypt uruchamiania uruchamia skrypt etapu.
- Skrypt logowania uruchamia skrypt rejestru.
- Skrypt wylogowania uruchamia skrypt wyrejestrowania.
- Skrypt zamykania uruchamia skrypt destage.

## <a name="use-packages-offline"></a>Korzystanie z pakietów w trybie offline

Jeśli używasz pakietów z usługi [Microsoft Store dla Firm](https://businessstore.microsoft.com/) lub [Microsoft Store dla Instytucji Edukacyjnych](https://educationstore.microsoft.com/) w sieci lub na urządzeniach, które nie są połączone z Internetem, musisz pobrać licencje pakietów z usługi Microsoft Store i zainstalować je na urządzeniu, aby pomyślnie uruchomić aplikację. Jeśli urządzenie jest w trybie online i może nawiązać połączenie z usługą Microsoft Store dla Firm, wymagane licencje powinny zostać automatycznie pobrać, ale jeśli jesteś w trybie offline, musisz ręcznie skonfigurować licencje.

Aby zainstalować pliki licencji, należy użyć skryptu programu PowerShell, który wywołuje klasę MDM_EnterpriseModernAppManagement_StoreLicenses02_01 u dostawcy mostka usługi WMI.

Oto jak skonfigurować licencje do użycia w trybie offline:

1. Pobierz pakiet aplikacji, licencje i wymagane struktury z Microsoft Store dla Firm. Potrzebne są zarówno zakodowane, jak i niekodowane pliki licencji. Szczegółowe instrukcje dotyczące pobierania można znaleźć [tutaj.](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)
2. Zaktualizuj następujące zmienne w skrypcie dla kroku 3:
      1. `$contentID` to wartość ContentID z niekodowany plik licencji (XML). Plik licencji można otworzyć w edytorze tekstów.
      2. `$licenseBlob` to cały ciąg obiektu blob licencji w zakodowanym pliku licencji (bin). Zakodowany plik licencji można otworzyć w edytorze tekstów.
3. Uruchom następujący skrypt w wierszu polecenia programu PowerShell dla administratorów. Dobrym miejscem do przeprowadzenia instalacji licencji jest [](#stage-powershell-script) zakończenie skryptu przejściowego, który również musi zostać uruchomiony z monitu administratora.

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

Ta funkcja nie jest obecnie obsługiwana, ale możesz zadawać pytania społeczności na stronie [Windows Virtual Desktop TechCo w witrynie](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Opinię na ten temat możesz również Windows Virtual Desktop w [centrum Windows Virtual Desktop opinii.](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)
