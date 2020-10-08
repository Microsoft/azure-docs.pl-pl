---
title: Dołączanie aplikacji MSIX pulpitu wirtualnego systemu Windows — Azure
description: Jak skonfigurować dołączenie aplikacji MSIX dla pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 06/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3b02be8f35ff33f758aebe03c89287c51c9ffef7
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91816334"
---
# <a name="set-up-msix-app-attach"></a>Konfigurowanie dołączania aplikacji MSIX

> [!IMPORTANT]
> Dołączenie do aplikacji MSIX jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym temacie opisano sposób konfigurowania dołączania aplikacji MSIX w środowisku pulpitu wirtualnego systemu Windows.

## <a name="requirements"></a>Wymagania

Przed rozpoczęciem należy wykonać następujące czynności w celu skonfigurowania dołączania aplikacji MSIX:

- Dostęp do portalu niejawnego testera systemu Windows w celu uzyskania wersji systemu Windows 10 z obsługą interfejsów API dołączania aplikacji MSIX.
- Działające wdrożenie pulpitu wirtualnego systemu Windows. Aby dowiedzieć się, jak wdrożyć pulpit wirtualny systemu Windows (klasyczny), zobacz [Tworzenie dzierżawy w systemie Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Aby dowiedzieć się, jak wdrożyć pulpit wirtualny systemu Windows z integracją Azure Resource Manager, zobacz [Tworzenie puli hostów przy użyciu Azure Portal](./create-host-pools-azure-marketplace.md).
- Narzędzie MSIX pakowanie.
- Udział sieciowy w wdrożeniu pulpitu wirtualnego systemu Windows, w którym będzie przechowywany pakiet MSIX.

## <a name="get-the-os-image"></a>Pobierz obraz systemu operacyjnego

Najpierw należy uzyskać obraz systemu operacyjnego. Obraz systemu operacyjnego można uzyskać za pomocą Azure Portal. Jeśli jednak jesteś członkiem niejawnego programu testów systemu Windows, możesz zamiast tego skorzystać z portalu niejawnego testera systemu Windows.

### <a name="get-the-os-image-from-the-azure-portal"></a>Pobierz obraz systemu operacyjnego z Azure Portal

Aby uzyskać obraz systemu operacyjnego z Azure Portal:

1. Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się.

2. Przejdź do pozycji **Utwórz maszynę wirtualną**.

3. Na karcie **podstawowa** wybierz pozycję **Windows 10 Enterprise wiele sesji, wersja 2004**.

4. Postępuj zgodnie z pozostałymi instrukcjami, aby zakończyć tworzenie maszyny wirtualnej.

     >[!NOTE]
     >Możesz użyć tej maszyny wirtualnej do bezpośredniego testowania dołączania aplikacji MSIX. Aby dowiedzieć się więcej, zapoznaj się z tematem [Generowanie pakietu dysku VHD lub VHDX dla MSIX](#generate-a-vhd-or-vhdx-package-for-msix). W przeciwnym razie zapoznaj się z tą sekcją.

### <a name="get-the-os-image-from-the-windows-insider-portal"></a>Pobierz obraz systemu operacyjnego z portalu niejawnego testera systemu Windows

Aby pobrać obraz systemu operacyjnego z portalu niejawnego testera systemu Windows:

1. Otwórz [Portal niejawnego testera systemu Windows](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) i zaloguj się.

     >[!NOTE]
     >Aby uzyskać dostęp do portalu niejawnego testera systemu Windows, musisz być członkiem niejawnego programu testów systemu Windows. Aby dowiedzieć się więcej o programie testów systemu Windows, zapoznaj się z [dokumentacją zatestera systemu Windows](/windows-insider/at-home/).

2. Przewiń w dół do sekcji **Wybierz wersję** i wybierz pozycję **Windows 10 wersja zapoznawcza wersji zapoznawczej Enterprise (Fast) — Kompilacja 19041** lub nowsza.

3. Wybierz pozycję **Potwierdź**, a następnie wybierz język, którego chcesz użyć, a następnie wybierz pozycję **Potwierdź** ponownie.

     >[!NOTE]
     >W tej chwili w języku angielskim jest jedynym językiem, który został przetestowany przy użyciu funkcji. Można wybrać inne języki, ale mogą one nie być wyświetlane zgodnie z oczekiwaniami.

4. Po wygenerowaniu linku pobierania wybierz pozycję **64-bitowe pobieranie** i Zapisz ją na lokalnym dysku twardym.

## <a name="prepare-the-vhd-image-for-azure"></a>Przygotowanie obrazu wirtualnego dysku twardego dla platformy Azure

Następnie musisz utworzyć główny obraz wirtualnego dysku twardego. Jeśli nie utworzono jeszcze głównego obrazu wirtualnego dysku twardego, przejdź do [przygotowania i dostosowania głównego obrazu wirtualnego dysku twardego](set-up-customize-master-image.md) i postępuj zgodnie z instrukcjami w tym miejscu.

Po utworzeniu głównego obrazu wirtualnego dysku twardego należy wyłączyć aktualizacje automatyczne dla aplikacji MSIX. Aby wyłączyć aktualizacje automatyczne, należy uruchomić następujące polecenia w wierszu polecenia z podwyższonym poziomem uprawnień:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

Po wyłączeniu funkcji Aktualizacje automatyczne należy włączyć funkcję Hyper-V, ponieważ w celu przełączenia i zamontowania wirtualnego dysku twardego na nośniku należy użyć polecenia mount-VHD.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```
>[!NOTE]
>Ta zmiana będzie wymagała ponownego uruchomienia maszyny wirtualnej.

Następnie przygotuj dysk VHD maszyny wirtualnej dla platformy Azure i przekaż otrzymany na platformie Azure plik VHD. Aby dowiedzieć się więcej, zobacz [Przygotowywanie i dostosowywanie głównego obrazu wirtualnego dysku twardego](set-up-customize-master-image.md).

Po przekazaniu wirtualnego dysku twardego na platformę Azure Utwórz pulę hostów opartą na tym nowym obrazie, postępując zgodnie z instrukcjami w temacie [Tworzenie puli hostów za pomocą samouczka Azure Marketplace](create-host-pools-azure-marketplace.md) .

## <a name="prepare-the-application-for-msix-app-attach"></a>Przygotowywanie aplikacji do dołączenia do aplikacji MSIX

Jeśli masz już pakiet MSIX, przejdź do [konfiguracji infrastruktury pulpitów wirtualnych systemu Windows](#configure-windows-virtual-desktop-infrastructure). Jeśli chcesz przetestować starsze aplikacje, postępuj zgodnie z instrukcjami w temacie [Tworzenie pakietu MSIX z poziomu Instalatora pulpitu na maszynie wirtualnej](/windows/msix/packaging-tool/create-app-package-msi-vm/) w celu przekonwertowania starszej aplikacji na pakiet MSIX.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Wygeneruj pakiet VHD lub VHDX dla MSIX

Pakiety są w formacie VHD lub VHDX, aby zoptymalizować wydajność. MSIX wymaga poprawnego działania pakietów VHD lub VHDX.

Aby wygenerować pakiet VHD lub VHDX dla MSIX:

1. [Pobierz narzędzie msixmgr](https://aka.ms/msixmgr) i Zapisz folder. zip do folderu na maszynie wirtualnej hosta sesji.

2. Rozpakuj folder Narzędzia msixmgr. zip.

3. Umieść pakiet źródłowy MSIX w tym samym folderze, w którym zostało rozpakowane narzędzie msixmgr.

4. Uruchom następujące polecenie cmdlet w programie PowerShell, aby utworzyć dysk VHD:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Upewnij się, że rozmiar wirtualnego dysku twardego jest wystarczająco duży, aby pomieścić rozszerzony MSIX. *

5. Uruchom następujące polecenie cmdlet, aby zainstalować nowo utworzony dysk VHD:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Uruchom to polecenie cmdlet, aby zainicjować dysk VHD:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Uruchom to polecenie cmdlet, aby utworzyć nową partycję:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. Uruchom to polecenie cmdlet, aby sformatować partycję:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Utwórz folder nadrzędny na zainstalowanym wirtualnym dysku twardym. Ten krok jest obowiązkowy, ponieważ dołączenie aplikacji MSIX wymaga folderu nadrzędnego. Możesz zmienić nazwę folderu nadrzędnego.

### <a name="expand-msix"></a>Rozwiń MSIX

Po tym celu należy "rozwinąć" obraz MSIX, rozpakowywanie go. Aby rozpakować obraz MSIX:

1. Otwórz wiersz polecenia jako administrator i przejdź do folderu, w którym zostało pobrane i rozpakowane narzędzie msixmgr.

2. Uruchom następujące polecenie cmdlet, aby rozpakować MSIX do dysku VHD, który został utworzony i zainstalowany w poprzedniej sekcji.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Następujący komunikat powinien pojawić się po rozpakowaniu:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > W przypadku używania pakietów z Microsoft Store dla firm (lub edukacji) w sieci lub na urządzeniach, które nie są połączone z Internetem, należy uzyskać licencje pakietu ze sklepu i zainstalować je w celu pomyślnego uruchomienia aplikacji. Zobacz [Korzystanie z pakietów w trybie offline](#use-packages-offline).

3. Przejdź do zainstalowanego wirtualnego dysku twardego i Otwórz folder aplikacji i sprawdź, czy jest dostępna zawartość pakietu.

4. Odinstaluj dysk VHD.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Konfigurowanie infrastruktury pulpitów wirtualnych systemu Windows

Po zaprojektowaniu jeden rozwinięty pakiet MSIX (wirtualny dysk twardy utworzony w poprzedniej sekcji) może być współużytkowany między wieloma maszynami wirtualnymi hosta sesji, ponieważ wirtualne dyski twarde są dołączone w trybie tylko do odczytu.

Przed rozpoczęciem upewnij się, że udział sieciowy spełnia następujące wymagania:

- Udział jest zgodny z protokołem SMB.
- Maszyny wirtualne będące częścią puli hostów sesji mają uprawnienia NTFS do udziału.

### <a name="set-up-an-msix-app-attach-share"></a>Skonfiguruj udział dołączania aplikacji MSIX

W środowisku pulpitu wirtualnego systemu Windows Utwórz udział sieciowy i Przenieś pakiet.

>[!NOTE]
> Najlepszym rozwiązaniem w przypadku tworzenia udziałów sieciowych MSIX jest skonfigurowanie udziału sieciowego z uprawnieniami tylko do odczytu systemu plików NTFS.

## <a name="install-certificates"></a>Instalowanie certyfikatów

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
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Zaktualizuj zmienną **$volumeGuid** przy użyciu właśnie SKOPIOWANEGO identyfikatora GUID woluminu.

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
