---
title: Instalowanie pakietów językowych na maszynach wirtualnych z systemem Windows 10 w programie Virtual Desktop systemu Windows — Azure
description: Jak zainstalować pakiety językowe dla maszyn wirtualnych z systemem Windows 10 dla wielu sesji na pulpicie wirtualnym systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dfedec85208ef9afaa58db2b200dc9f9988a845b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709244"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Dodawanie pakietów językowych do obrazu wielosesyjnego systemu Windows 10

Windows Virtual Desktop to usługa, którą użytkownicy mogą wdrażać w dowolnym miejscu. Dlatego ważne jest, aby użytkownicy mogli dostosować język, w którym są wyświetlane obrazy obejmujące wiele sesji systemu Windows 10 Enterprise.

Istnieją dwa sposoby zaspokajania potrzeb użytkowników w języku:

- Twórz dedykowane pule hostów przy użyciu dostosowanego obrazu dla każdego języka.
- Użytkownicy mający różne wymagania dotyczące języka i lokalizacji znajdują się w tej samej puli hostów, ale dostosowują ich obrazy, aby upewnić się, że mogą wybrać dowolny język, którego potrzebują.

Druga metoda jest znacznie bardziej wydajna i opłacalna. Można jednak zdecydować, która metoda najlepiej odpowiada Twoim potrzebom. W tym artykule opisano sposób dostosowywania języków dla obrazów.

## <a name="prerequisites"></a>Wymagania wstępne

Aby dostosować obrazy wielosesyjne dla systemu Windows 10 Enterprise, należy wykonać następujące czynności, aby dodać wiele języków:

- Maszyna wirtualna platformy Azure z obsługą wielu sesji systemu Windows 10 Enterprise, wersja 1903 lub nowsza

- Język ISO, funkcja na żądanie (FDZ) dysk 1 i aplikacje skrzynki odbiorczej ISO wersji systemu operacyjnego używanego przez obraz. Można je pobrać tutaj:
     
     - Język ISO:
        - [Windows 10, wersja 1903 lub 1909 Language Pack ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, wersja 2004 lub 20H2 Language Pack ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - Dysk FDZ 1 ISO:
        - [Windows 10, wersja 1903 lub 1909 FDZ Disk 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, wersja 2004 lub 20H2 FDZ Disk 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - Plik ISO aplikacji skrzynki odbiorczej:
        - [Windows 10, wersja 1903 lub 1909 — aplikacje skrzynki odbiorczej — ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, wersja 2004 — aplikacje skrzynki odbiorczej — ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, wersja 20H2 Skrzynka odbiorcza aplikacji w wersji ISO](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Jeśli do lokalizowania obrazów są używane pliki ISO (Local Experience Pack) (LXP), należy również pobrać odpowiednie ISO LXP w celu uzyskania najlepszego środowiska
        - Jeśli używasz systemu Windows 10, wersja 1903 lub 1909:
          - [Windows 10, wersja 1903 lub 1909 LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Jeśli używasz systemu Windows 10 w wersji 2004 lub 20H2, Skorzystaj z informacji w temacie [Dodawanie języków w systemie Windows 10: znane problemy](/windows-hardware/manufacture/desktop/language-packs-known-issue) , aby ustalić, które z następujących LXP obrazów ISO są odpowiednie dla Ciebie:
          - [Windows 10, wersja 2004 lub 20H2 **9B** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [Windows 10, wersja 2004 lub 20H2 **9c** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [Windows 10, wersja 2004 lub 20H2 **10C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [Windows 10, wersja 2004 lub 20H2 **11C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [Windows 10, wersja 2004 lub 20H2 **1c** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)

- Udział Azure Files lub udział plików na maszynie wirtualnej serwera plików z systemem Windows

>[!NOTE]
>Udział plików (repozytorium) musi być dostępny z maszyny wirtualnej platformy Azure, która ma zostać użyta do utworzenia obrazu niestandardowego.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Tworzenie repozytorium zawartości dla pakietów językowych i funkcji na żądanie

Aby utworzyć repozytorium zawartości dla pakietów językowych i FODs oraz repozytorium dla pakietów aplikacji skrzynki odbiorczej:

1. Na maszynie wirtualnej platformy Azure Pobierz dla systemu Windows 10 wielojęzyczne aplikacje ISO, FODs i Skrzynka odbiorcza dla systemu Windows 10 Enterprise, wersje 1903/1909 i 2004 obrazów z linków w sekcji [wymagania wstępne](#prerequisites).

2. Otwórz i zainstaluj pliki ISO na maszynie wirtualnej.

3. Przejdź do pakietu językowego ISO i skopiuj zawartość z folderów **LocalExperiencePacks** i **x64 \\ Langpacks** , a następnie wklej zawartość do udziału plików.

4. Przejdź do **pliku ISO FDZ**, Skopiuj całą jego zawartość, a następnie wklej go do udziału plików.
5. Przejdź do folderu **amd64fre** w pliku ISO aplikacji skrzynki odbiorczej i skopiuj zawartość repozytorium dla aplikacji skrzynki odbiorczej, które zostały przygotowane.

     >[!NOTE]
     > Jeśli pracujesz z ograniczoną ilością pamięci masowej, skopiuj tylko te pliki, które są potrzebne użytkownikom. Pliki można wyróżnić, przeglądając kod języka w nazwach plików. Na przykład plik francuski zawiera kod "fr-FR" w nazwie. Aby uzyskać pełną listę kodów języków dla wszystkich dostępnych języków, zobacz [dostępne pakiety językowe dla systemu Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

     >[!IMPORTANT]
     > Niektóre języki wymagają dodatkowych czcionek zawartych w pakietach satelickich, które są zgodne z różnymi konwencjami nazewnictwa. Na przykład nazwy plików czcionek japońskich to "Jpan".
     >
     > [!div class="mx-imgBorder"]
     > ![Przykład pakietów języka japońskiego ze znacznikiem języka "Jpan" w nazwach plików.](media/language-pack-example.png)

6. Ustaw uprawnienia dla udziału repozytorium zawartości języka, aby mieć dostęp do odczytu z maszyny wirtualnej, która będzie używana do tworzenia obrazu niestandardowego.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Ręczne tworzenie niestandardowego obrazu dla wielu sesji systemu Windows 10 Enterprise

Aby ręcznie utworzyć niestandardowy obraz wielosesyjny dla systemu Windows 10 Enterprise:

1. Wdróż maszynę wirtualną platformy Azure, przejdź do galerii platformy Azure i wybierz bieżącą wersję wielosesyjnej sesji systemu Windows 10.
2. Po wdrożeniu maszyny wirtualnej Połącz się z nią przy użyciu protokołu RDP jako administrator lokalny.
3. Upewnij się, że maszyna wirtualna ma wszystkie najnowsze aktualizacje systemu Windows. Pobierz aktualizacje i ponownie uruchom maszynę wirtualną, jeśli to konieczne.
4. Połącz się z repozytorium udziałów plików pakietu językowego, FDZ i skrzynki odbiorczej i zainstaluj je na literę dysku (na przykład dysk E).

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Automatyczne tworzenie niestandardowego obrazu wielosesyjnego dla systemu Windows 10 Enterprise

Jeśli wolisz instalować Języki w procesie zautomatyzowanym, możesz skonfigurować skrypt w programie PowerShell. Możesz użyć następującego przykładowego skryptu, aby zainstalować hiszpański (Hiszpania), francuski (Francja) i chiński (ChRL) pakiety językowe i pakiety satelickie dla wielu sesji dla systemu Windows 10 Enterprise, wersja 2004. Skrypt integruje pakiet językowy i wszystkie niezbędne pakiety satelickie do obrazu. Można jednak również zmodyfikować ten skrypt, aby zainstalować inne języki. Upewnij się, że skrypt jest uruchamiany z sesji programu PowerShell z podwyższonym poziomem uprawnień lub w przeciwnym razie nie będzie działać.

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Skrypt może zająć trochę czasu w zależności od liczby języków, które należy zainstalować.

Po zakończeniu działania skryptu upewnij się, że poprawnie zainstalowano pakiety językowe, przechodząc do opcji **Rozpocznij**  >  **Ustawienia**  >  **czas &**  >  **Język**. Jeśli w tym miejscu znajdują się pliki języka, wszystko jest gotowe.

Po dodaniu dodatkowych języków do obrazu systemu Windows, aplikacje skrzynki odbiorczej również muszą zostać zaktualizowane w celu obsługi dodanych języków. Można to zrobić przez odświeżenie wstępnie zainstalowanych aplikacji przy użyciu zawartości z pliku ISO aplikacji skrzynki odbiorczej. Aby przeprowadzić to odświeżanie w odłączonym środowisku (bez dostępu do Internetu z maszyny wirtualnej), można zautomatyzować proces za pomocą następującego przykładowego skryptu programu PowerShell.

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string]$InboxApps = "F:\"
##Update Inbox Store Apps##
$AllAppx = Get-Item $inboxapps\*.appx | Select-Object name
$AllAppxBundles = Get-Item $inboxapps\*.appxbundle | Select-Object name
$allAppxXML = Get-Item $inboxapps\*.xml | Select-Object name
foreach ($Appx in $AllAppx) {
    $appname = $appx.name.substring(0,$Appx.name.length-5)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    
    Write-Host "Handeling with xml $appname"  
  
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
      
      Write-Host "Handeling without xml $appname"
      
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
foreach ($Appx in $AllAppxBundles) {
    $appname = $appx.name.substring(0,$Appx.name.length-11)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    Write-Host "Handeling with xml $appname"
    
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
       Write-Host "Handeling without xml $appname"
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
```

>[!IMPORTANT]
>Aplikacje skrzynki odbiorczej zawarte w pliku ISO nie są najnowszymi wersjami wstępnie zainstalowanych aplikacji systemu Windows. Aby uzyskać najnowszą wersję wszystkich aplikacji, należy zaktualizować aplikacje przy użyciu aplikacji ze sklepu Windows i przeprowadzić ręczne wyszukiwanie aktualizacji po zainstalowaniu dodatkowych języków.

Gdy wszystko będzie gotowe, pamiętaj o rozłączeniu udziału.

## <a name="finish-customizing-your-image"></a>Zakończenie dostosowywania obrazu

Po zainstalowaniu pakietów językowych można zainstalować dowolne inne oprogramowanie, które chcesz dodać do dostosowanego obrazu.

Po zakończeniu dostosowywania obrazu należy uruchomić narzędzie przygotowywania systemu (Sysprep).

Aby uruchomić program Sysprep:

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień i uruchom następujące polecenie, aby uogólnić obraz:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Zatrzymaj maszynę wirtualną, a następnie Przechwyć ją w zarządzanym obrazie, postępując zgodnie z instrukcjami w temacie [Tworzenie zarządzanego obrazu maszyny wirtualnej na platformie Azure](../virtual-machines/windows/capture-image-resource.md).

3. Możesz teraz użyć niestandardowego obrazu do wdrożenia puli hostów pulpitu wirtualnego systemu Windows. Aby dowiedzieć się, jak wdrożyć pulę hostów, zobacz [Samouczek: Tworzenie puli hostów przy użyciu Azure Portal](create-host-pools-azure-marketplace.md).

## <a name="enable-languages-in-windows-settings-app"></a>Włącz Języki w aplikacji Ustawienia systemu Windows

Po wdrożeniu puli hostów należy dodać język do listy języków każdego użytkownika, aby można było wybrać preferowany język w menu Ustawienia.

Aby upewnić się, że użytkownicy będą mogli wybrać zainstalowane Języki, zaloguj się jako użytkownik, a następnie uruchom następujące polecenie cmdlet programu PowerShell w celu dodania zainstalowanych pakietów językowych do menu Języki. Możesz również skonfigurować ten skrypt jako zautomatyzowany skrypt zadania lub logowania, który uaktywnia się, gdy użytkownik zaloguje się do sesji.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Gdy użytkownik zmieni ustawienia języka, musi wylogować się z sesji pulpitu wirtualnego systemu Windows i zalogować się ponownie, aby zmiany zaczęły obowiązywać. 

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz wiedzieć o znanych problemach dotyczących pakietów językowych, zobacz [Dodawanie pakietów językowych w systemie Windows 10, wersja 1803 i nowsze wersje: znane problemy](/windows-hardware/manufacture/desktop/language-packs-known-issue).

Jeśli masz jakieś pytania dotyczące wielu sesji systemu Windows 10 Enterprise, zapoznaj się z naszymi [często zadawanymi pytaniami](windows-10-multisession-faq.md).
