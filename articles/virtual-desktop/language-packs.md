---
title: Instalowanie pakietów językowych na Windows 10 wirtualnych na platformie Windows Virtual Desktop — Azure
description: Jak zainstalować pakiety językowe dla Windows 10 wielosesyjnej maszyny wirtualnej w Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: db1ac3f5de507a5cfdbfec7216afea9a0f4ac541
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515043"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Dodawanie pakietów językowych do obrazu Windows 10 wielu sesji

Windows Virtual Desktop to usługa, która umożliwia użytkownikom wdrażanie w dowolnym czasie i miejscu. Dlatego ważne jest, aby użytkownicy mogli dostosować język wyświetlany przez Windows 10 Enterprise obraz z wieloma sesjami.

Istnieją dwa sposoby, aby dostosować się do potrzeb językowych użytkowników:

- Twórz dedykowane pule hostów z dostosowanym obrazem dla każdego języka.
- Użytkownicy z różnymi wymaganiami w zakresie języka i lokalizacji muszą korzystać z tej samej puli hostów, ale dostosowywać swoje obrazy, aby mieć pewność, że będą oni w stanie wybrać język, którego potrzebują.

Ta druga metoda jest znacznie bardziej wydajna i tańsza. Jednak to Ty decydujesz, która metoda najlepiej odpowiada Twoim potrzebom. W tym artykule opisano sposób dostosowywania języków obrazów.

## <a name="prerequisites"></a>Wymagania wstępne

Do dostosowania obrazów wielosesyjnej Windows 10 Enterprise dodawania wielu języków potrzebne są następujące elementy:

- Maszyna wirtualna platformy Azure z wieloma Windows 10 Enterprise w wersji 1903 lub nowszej

- Obraz ISO języka, dysk funkcji na żądanie (FOD) 1 i obraz ISO aplikacji skrzynki odbiorczej wersji systemu operacyjnego używanej przez obraz. Możesz je pobrać tutaj:
     
     - Iso języka:
        - [Windows 10, wersja 1903 lub 1909 Language Pack ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, wersja 2004 lub 20H2 Language Pack ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - FOD Disk 1 ISO:
        - [Windows 10, wersja 1903 lub 1909 FOD Disk 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, wersja 2004 lub 20H2 FOD Disk 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - Obraz ISO aplikacji skrzynki odbiorczej:
        - [Windows 10 iso aplikacji skrzynki odbiorczej w wersji 1903 lub 1909](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, wersja 2004 Aplikacje skrzynki odbiorczej ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, wersja 20H2 Aplikacje skrzynki odbiorczej ISO](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Jeśli do lokalizacji obrazów używasz plików ISO Pakiet lokalizacyjny (LXP), musisz również pobrać odpowiedni obraz ISO LXP, aby uzyskać najlepsze środowisko językowe
        - Jeśli używasz programu Windows 10 wersji 1903 lub 1909:
          - [Windows 10, wersja 1903 lub 1909 LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Jeśli używasz programu Windows 10 w wersji 2004 lub 20H2, skorzystaj z informacji w tece Dodawanie języków w programie [Windows 10: Znane](/windows-hardware/manufacture/desktop/language-packs-known-issue) problemy, aby ustalić, która z następujących funkcji LXP ISO jest dla Ciebie właściwa:
          - [Windows 10, wersja 2004 lub 20H2 **9B** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [Windows 10, wersja 2004 lub 20H2 **9C** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [Windows 10, wersja 2004 lub 20H2 **10C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [Windows 10, wersja 2004 lub 20H2 **11C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [Windows 10, wersja 2004 lub 20H2 **1C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)
          - [Windows 10, wersja 2004 lub 20H2 **2C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2102C.iso)
          - [Windows 10, wersja 2004 lub 20H2 **3C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2103C.iso)

- Udział Azure Files lub udział plików na maszynie wirtualnej serwera plików z systemem Windows

>[!NOTE]
>Udział plików (repozytorium) musi być dostępny z maszyny wirtualnej platformy Azure, która ma zostać użyć do utworzenia obrazu niestandardowego.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Tworzenie repozytorium zawartości dla pakietów językowych i funkcji na żądanie

Aby utworzyć repozytorium zawartości dla pakietów językowych i identyfikatorów FOD oraz repozytorium pakietów Aplikacji skrzynki odbiorczej:

1. Na maszynie wirtualnej platformy Azure pobierz obrazy WINDOWS 10 Multi-Language ISO, FOD i Inbox Apps for Windows 10 Enterprise multi-session, version 1903/1909 i 2004 z linków w temacie [Wymagania wstępne.](#prerequisites)

2. Otwórz i zainstaluj pliki ISO na maszynie wirtualnej.

3. Przejdź do pliku ISO pakietu językowego i skopiuj zawartość z folderów **Langpacks LocalExperiencePacks** i **x64, \\** a następnie wklej zawartość do udziału plików.

4. Przejdź do pliku **ISO FOD,** skopiuj całą jego zawartość, a następnie wklej go do udziału plików.
5. Przejdź do folderu **amd64fre** w pliku ISO Aplikacje skrzynki odbiorczej i skopiuj zawartość do repozytorium dla przygotowanych aplikacji skrzynki odbiorczej.

     >[!NOTE]
     > Jeśli pracujesz z ograniczoną przestrzenią dyskową, skopiuj tylko pliki dla języków, których potrzebują Twoi użytkownicy. Możesz odróżnić pliki, patrząc na kody języków w ich nazwach plików. Na przykład w nazwie pliku w języku francuskim znajduje się kod "fr-FR". Aby uzyskać pełną listę kodów języków dla wszystkich dostępnych języków, zobacz [Dostępne pakiety językowe dla systemu Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

     >[!IMPORTANT]
     > Niektóre języki wymagają dodatkowych czcionek zawartych w pakietach satelitarnych, które są zgodne z różnymi konwencjami nazewnictwa. Na przykład nazwy plików czcionek w języku japońskim obejmują "Jpan".
     >
     > [!div class="mx-imgBorder"]
     > ![Przykład pakietów językowych w języku japońskim z tagiem języka "Jpan" w nazwach plików.](media/language-pack-example.png)

6. Ustaw uprawnienia do udziału repozytorium zawartości języka, aby mieć dostęp do odczytu z maszyny wirtualnej, która będzie używany do kompilowania obrazu niestandardowego.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Ręczne tworzenie niestandardowego obrazu Windows 10 Enterprise wielu sesji

Aby ręcznie utworzyć obraz Windows 10 Enterprise wielu sesji:

1. Wdzyskaj maszynę wirtualną platformy Azure, a następnie przejdź do galerii platformy Azure i wybierz bieżącą wersję Windows 10 Enterprise wielu sesji, których używasz.
2. Po wdrożeniu maszyny wirtualnej połącz się z nim przy użyciu protokołu RDP jako administrator lokalny.
3. Upewnij się, że maszyna wirtualna ma wszystkie najnowsze aktualizacje systemu Windows. Pobierz aktualizacje i w razie potrzeby uruchom ponownie maszynę wirtualną.
4. Nawiąż połączenie z repozytorium udziałów plików pakietu językowego, pliku FOD i aplikacji skrzynki odbiorczej i zainstaluj je na dysku litery (na przykład dysku E).

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Automatyczne tworzenie niestandardowego obrazu Windows 10 Enterprise wielu sesji

Jeśli wolisz instalować języki za pomocą zautomatyzowanego procesu, możesz skonfigurować skrypt w programie PowerShell. Poniższy przykładowy skrypt umożliwia zainstalowanie pakietów językowych języka hiszpańskiego (Hiszpania), francuskiego (Francja) i chińskiego (CHRL) oraz pakietów satelitarnych dla Windows 10 Enterprise wielu sesji w wersji 2004. Skrypt integruje pakiet interfejsu językowego i wszystkie niezbędne pakiety sateliacyjne z obrazem. Można jednak również zmodyfikować ten skrypt, aby zainstalować inne języki. Pamiętaj, aby uruchomić skrypt z sesji programu PowerShell z podwyższonym poziomem uprawnień. W innym przypadku nie będzie on działać.

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

Skrypt może trochę potrwać w zależności od liczby języków, które należy zainstalować.

Po zakończeniu działania skryptu upewnij się, że pakiety językowe zostały poprawnie zainstalowane, przechodząc do tematu **Start**  >  **Settings**  >  **Time & Language** Language  >  **.** Jeśli znajdują się tam pliki językowe, wszystko jest już ustawione.

Po dodaniu kolejnych języków do obrazu systemu Windows należy również zaktualizować aplikacje skrzynki odbiorczej w celu obsługi dodanych języków. Można to zrobić, odświeżając wstępnie zainstalowane aplikacje z zawartością z obrazu ISO aplikacji skrzynki odbiorczej. Aby wykonać to odświeżanie w środowisku bez połączenia (nie jest możliwy dostęp do Internetu z maszyny wirtualnej), możesz zautomatyzować ten proces za pomocą następującego przykładowego skryptu programu PowerShell.

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
>Aplikacje skrzynki odbiorczej uwzględnione w obrazie ISO nie są najnowszymi wersjami wstępnie zainstalowanych aplikacji systemu Windows. Aby uzyskać najnowszą wersję wszystkich aplikacji, musisz zaktualizować aplikacje przy użyciu aplikacji ze Sklepu Windows i przeprowadzić ręczne wyszukiwanie aktualizacji po zainstalowaniu dodatkowych języków.

Gdy wszystko będzie gotowe, pamiętaj o rozłączeniu udziału.

## <a name="finish-customizing-your-image"></a>Kończanie dostosowywania obrazu

Po zainstalowaniu pakietów językowych można zainstalować dowolne inne oprogramowanie, które chcesz dodać do dostosowanego obrazu.

Po zakończeniu dostosowywania obrazu należy uruchomić narzędzie przygotowywania systemu (sysprep).

Aby uruchomić narzędzie sysprep:

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień i uruchom następujące polecenie, aby uogólnić obraz:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Zatrzymaj maszynę wirtualną, a następnie przechwyć ją w obrazie zarządzanym, zgodnie z instrukcjami w tesłudze Create a managed image of a generalized VM in Azure (Tworzenie obrazu zarządzanego uogólnionej maszyny wirtualnej na [platformie Azure).](../virtual-machines/windows/capture-image-resource.md)

3. Teraz możesz użyć dostosowanego obrazu do wdrożenia Windows Virtual Desktop puli hostów. Aby dowiedzieć się, jak wdrożyć pulę hostów, zobacz [Samouczek: tworzenie puli hostów przy](create-host-pools-azure-marketplace.md)użyciu Azure Portal .

## <a name="enable-languages-in-windows-settings-app"></a>Włączanie języków w aplikacji ustawień systemu Windows

Na koniec po wdrożeniu puli hostów należy dodać język do listy języków poszczególnych użytkowników, aby mógł wybrać preferowany język w menu Ustawienia.

Aby upewnić się, że użytkownicy mogą wybrać zainstalowane języki, zaloguj się jako użytkownik, a następnie uruchom następujące polecenie cmdlet programu PowerShell, aby dodać zainstalowane pakiety językowe do menu Języki. Możesz również skonfigurować ten skrypt jako zautomatyzowane zadanie lub skrypt logowania, który jest aktywowany, gdy użytkownik loguje się do swojej sesji.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Gdy użytkownik zmieni ustawienia języka, będzie musiał wylogować się z sesji Windows Virtual Desktop i zalogować się ponownie, aby zmiany zostały wprowadzone. 

## <a name="next-steps"></a>Następne kroki

Jeśli zastanawiasz się nad znanymi problemami z pakietami językowym, zobacz Dodawanie pakietów językowych w programie Windows 10 w wersji [1803](/windows-hardware/manufacture/desktop/language-packs-known-issue)i nowszych: Znane problemy.

Jeśli masz inne pytania dotyczące Windows 10 Enterprise wielu sesji, zapoznaj się z często zadawanymi [pytaniami.](windows-10-multisession-faq.yml)
