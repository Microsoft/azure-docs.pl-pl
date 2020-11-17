---
title: Przewodnik konfigurowania komputera z szablonem systemu Windows | Microsoft Docs
description: Ogólne kroki przygotowujące maszynę szablonu systemu Windows w usługach Lab Services.  Kroki te obejmują ustawienie Windows Update harmonogram, Instalowanie usługi OneDrive i instalowanie pakietu Office.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 216dc843b31eac355e1d818014f3d70b2ef83132
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647906"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Przewodnik konfigurowania komputera z szablonem systemu Windows w Azure Lab Services

Jeśli konfigurujesz komputer z szablonem systemu Windows 10 na potrzeby Azure Lab Services, poniżej przedstawiono niektóre najlepsze rozwiązania i wskazówki, które należy wziąć pod uwagę. Poniższe kroki konfiguracji są opcjonalne.  Jednak te kroki przygotowawcze mogą pomóc Ci zwiększyć produktywność, zminimalizować liczbę przerw w działaniu klasy i upewnić się, że korzystają z najnowszych technologii.

>[!IMPORTANT]
>Ten artykuł zawiera fragmenty kodu programu PowerShell usprawniające proces modyfikacji szablonu komputera.  W przypadku wszystkich wyświetlanych skryptów programu PowerShell należy uruchomić je w programie Windows PowerShell z uprawnieniami administratora. W systemie Windows 10 Szybka metoda polega na kliknięciu prawym przyciskiem myszy menu Start i wybraniu polecenia "Windows PowerShell (Administrator)".

## <a name="install-and-configure-onedrive"></a>Instalowanie i Konfigurowanie usługi OneDrive

Aby chronić dane uczniów przed utratą w przypadku zresetowania maszyny wirtualnej, zalecamy uczniów z powrotem do danych w chmurze.  Usługa Microsoft OneDrive może pomóc uczniom w ochronie swoich danych.  

### <a name="install-onedrive"></a>Instalowanie usługi OneDrive

Aby ręcznie pobrać i zainstalować usługę OneDrive, zapoznaj się ze stronami pobierania [usługi OneDrive](https://onedrive.live.com/about/download/) lub [OneDrive dla firm](https://onedrive.live.com/about/business/) .

Można również użyć następującego skryptu programu PowerShell.  Spowoduje to automatyczne pobranie i zainstalowanie najnowszej wersji usługi OneDrive.  Po zainstalowaniu klienta usługi OneDrive Uruchom Instalatora.  W naszym przykładzie użyto `/allUsers` przełącznika, aby zainstalować usługę OneDrive dla wszystkich użytkowników na komputerze. Korzystamy również z `/silent` przełącznika, aby zainstalować usługę OneDrive w trybie dyskretnym.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>Dostosowania usługi OneDrive

Istnieje wiele [dostosowań, które można wykonać w usłudze OneDrive](/onedrive/use-group-policy). Przyjrzyjmy się niektórym bardziej typowym dostosowaniu.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Przenoszenie znanych folderów systemu Windows w trybie dyskretnym do usługi OneDrive

Foldery, takie jak dokumenty, pliki do pobrania i obrazy, są często używane do przechowywania plików uczniów. Aby zapewnić, że kopie zapasowe tych folderów są tworzone w usłudze OneDrive, zalecamy przeniesienie tych folderów do usługi OneDrive.

Jeśli korzystasz z komputera, który nie używa Active Directory, użytkownicy mogą ręcznie przenieść te foldery do usługi OneDrive po uwierzytelnieniu w usłudze OneDrive.

1. Otwórz Eksploratora plików
2. Kliknij prawym przyciskiem myszy folder dokumenty, pliki do pobrania lub obrazy.
3. Przejdź do właściwości > lokalizacji.  Przenieś folder do nowego folderu w katalogu usługi OneDrive.

Jeśli maszyna wirtualna jest połączona z usługą Active Directory, możesz ustawić na komputerze szablonu Automatyczne monitowanie uczniów o przeniesienie znanych folderów do usługi OneDrive.  

Musisz najpierw pobrać identyfikator organizacji.  Aby uzyskać więcej instrukcji, zobacz [Znajdowanie identyfikatora organizacji Microsoft 365](/onedrive/find-your-office-365-tenant-id).  Identyfikator organizacji można także uzyskać, korzystając z następującego programu PowerShell.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Gdy masz identyfikator organizacji, w usłudze OneDrive wpisz monit o przeniesienie znanych folderów do usługi OneDrive przy użyciu następującego programu PowerShell.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>Korzystanie z plików usługi OneDrive na żądanie

Studenci mogą mieć wiele plików w ramach swoich kont w usłudze OneDrive. Aby zaoszczędzić miejsce na maszynie i skrócić czas pobierania, zalecamy utworzenie wszystkich plików przechowywanych na koncie w usłudze OneDrive dla uczniów na żądanie.  Pliki na żądanie są pobierane tylko wtedy, gdy użytkownik uzyskuje dostęp do pliku.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Logowanie dyskretne użytkowników do usługi OneDrive

Usługę OneDrive można skonfigurować do automatycznego logowania przy użyciu poświadczeń systemu Windows zalogowanego użytkownika.  Automatyczne logowanie jest przydatne w przypadku klas, w których student loguje się przy użyciu swoich poświadczeń szkolnych.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Wyłączenie samouczka, który pojawia się na końcu instalacji usługi OneDrive

To ustawienie umożliwia uniemożliwienie uruchamiania samouczka w przeglądarce sieci Web po zakończeniu instalacji usługi OneDrive.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Ustaw maksymalny rozmiar pliku, który ma być pobierany automatycznie

To ustawienie jest używane w połączeniu z dyskretnym logowaniem użytkowników do klienta synchronizacji usługi OneDrive z poświadczeniami systemu Windows na urządzeniach, na których nie włączono obsługi plików usługi OneDrive na żądanie. Każdy użytkownik, który ma OneDrive o rozmiarze większym niż określony próg (w MB) będzie monitowany o wybranie folderów, które chcą synchronizować, zanim klient synchronizacji z usługą OneDrive (OneDrive.exe) pobierze pliki.  W naszym przykładzie "1111-2222-3333-4444" jest IDENTYFIKATORem organizacji i 0005000 ustawia próg 5 GB.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-365"></a>Zainstaluj i skonfiguruj Microsoft 365

### <a name="install-microsoft-365"></a>Zainstaluj Microsoft 365

Jeśli komputer szablonu wymaga pakietu Office, zalecamy instalację pakietu Office za pomocą [Narzędzia do wdrażania pakietu Office (ODT)](https://www.microsoft.com/download/details.aspx?id=49117). Należy utworzyć plik konfiguracyjny wielokrotnego użytku za pomocą [Centrum administracyjnego Microsoft 365 aplikacje](https://config.office.com/) , aby wybrać architekturę, jakie funkcje będą potrzebne z pakietu Office i jak często aktualizacje.

1. Przejdź do [Centrum administracyjnego aplikacji Microsoft 365 Apps](https://config.office.com/) i Pobierz własny plik konfiguracji.
2. Pobierz [Narzędzie do wdrażania pakietu Office](https://www.microsoft.com/download/details.aspx?id=49117).  Pobrany plik będzie `setup.exe` .
3. Uruchom `setup.exe /download configuration.xml` , aby pobrać składniki pakietu Office.
4. Uruchom `setup.exe /configure configuration.xml` , aby zainstalować składniki pakietu Office.

### <a name="change-the-microsoft-365-update-channel"></a>Zmienianie Microsoft 365 kanału aktualizacji

Za pomocą narzędzia konfiguracji pakietu Office można ustawić, jak często pakiet Office otrzymuje aktualizacje. Jeśli jednak chcesz zmodyfikować częstotliwość otrzymywania aktualizacji przez pakiet Office po instalacji, możesz zmienić adres URL kanału aktualizacji. Adresy URL kanału aktualizacji można znaleźć w temacie [Zmienianie kanału aktualizacji Microsoft 365 aplikacje dla urządzeń w organizacji](/deployoffice/change-update-channels). W poniższym przykładzie pokazano, jak ustawić Microsoft 365 na korzystanie z kanału aktualizacji miesięcznej.

```powershell
# Update to the Microsoft 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Instalowanie i Konfigurowanie aktualizacji

### <a name="install-the-latest-windows-updates"></a>Zainstaluj najnowsze aktualizacje systemu Windows

Przed opublikowaniem maszyny wirtualnej w szablonie zaleca się zainstalowanie najnowszych aktualizacji firmy Microsoft na komputerze szablonu ze względów bezpieczeństwa.  Ponadto może to spowodować, że uczniowie nie zostaną zakłócone w pracy, gdy aktualizacje są uruchamiane w nieoczekiwanym czasie.

1. **Ustawienia** uruchamiania z menu Start
2. Kliknij pozycję **aktualizuj** & zabezpieczenia
3. Kliknij pozycję **Sprawdź dostępność aktualizacji**
4. Aktualizacje zostaną pobrane i zainstalowane.

Aby zaktualizować maszynę szablonu, można także użyć programu PowerShell.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Niektóre aktualizacje mogą wymagać ponownego uruchomienia komputera.  Po ponownym uruchomieniu zostanie wyświetlony monit.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Zainstaluj najnowsze aktualizacje dla aplikacji Microsoft Store

Zalecamy, aby wszystkie aplikacje Microsoft Store były aktualizowane do ich najnowszych wersji.  Poniżej znajdują się instrukcje ręcznego aktualizowania aplikacji z Microsoft Store.  

1. Uruchom aplikację **Microsoft Store** .
2. Kliknij przycisk wielokropka (...) obok Zdjęcia użytkownika w górnym rogu aplikacji.
3. Z menu rozwijanego wybierz pozycję **Pobierz** i aktualizacje.
4. Kliknij przycisk **Pobierz aktualizację** .

Za pomocą programu PowerShell można także aktualizować Microsoft Store aplikacje, które są już zainstalowane.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Zatrzymaj automatyczne aktualizacje systemu Windows

Po zaktualizowaniu systemu Windows do najnowszej wersji można rozważyć zatrzymanie aktualizacji systemu Windows.  Aktualizacje automatyczne mogą potencjalnie zakłócać zaplanowaną godzinę klasy.  Jeśli kurs jest już uruchomiony, rozważ zaproszenie uczniów o ręczne sprawdzenie dostępności aktualizacji lub ustawienie automatycznych aktualizacji na czas poza godzinami zaplanowanych klas.  Aby uzyskać więcej informacji na temat opcji dostosowywania dla Windows Update, zobacz [Zarządzanie dodatkowymi ustawieniami Windows Update](/windows/deployment/update/waas-wu-settings).

Automatyczne aktualizacje systemu Windows można zatrzymać przy użyciu poniższego skryptu programu PowerShell.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Instalowanie pakietów języka obcego

Jeśli potrzebujesz dodatkowych języków zainstalowanych na maszynie wirtualnej, możesz dodać je za pomocą Microsoft Store.

1. Microsoft Store uruchamiania
2. Wyszukaj "pakiet językowy"
3. Wybierz język do zainstalowania

Jeśli użytkownik jest już zalogowany do szablonu maszyny wirtualnej, użyj skrótu "Zainstaluj pakiet językowy" ( `ms-settings:regionlanguage?activationSource=SMC-IA-4027670` ), aby przejść bezpośrednio do odpowiedniej strony ustawień.

## <a name="remove-unneeded-built-in-apps"></a>Usuń niepotrzebne aplikacje wbudowane

System Windows 10 zawiera wiele wbudowanych aplikacji, które mogą nie być potrzebne dla określonej klasy. Aby uprościć obraz komputera dla studentów, możesz chcieć odinstalować niektóre aplikacje z komputera szablonu.  Aby wyświetlić listę zainstalowanych aplikacji, należy użyć `Get-AppxPackage` polecenia cmdlet programu PowerShell.  W poniższym przykładzie pokazano wszystkie zainstalowane aplikacje, które można usunąć.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Aby usunąć aplikację, należy użyć polecenia cmdlet Remove-Appx.  W poniższym przykładzie przedstawiono sposób usuwania wszystkich elementów związanych z konsolą XBox.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Instalowanie typowych aplikacji związanych z nauczaniem

Zainstaluj inne aplikacje, które są często używane do uczenia się w aplikacji ze sklepu Windows. Sugestie obejmują aplikacje, takie jak [aplikacja Microsoft tablica](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)i [Minecraft Education Edition](https://education.minecraft.net/). Te aplikacje należy instalować ręcznie za pomocą Sklepu Windows lub za pomocą odpowiednich witryn sieci Web na maszynie wirtualnej szablonu.

## <a name="conclusion"></a>Wniosek

W tym artykule przedstawiono kroki opcjonalne umożliwiające przygotowanie maszyny wirtualnej szablonu systemu Windows dla efektywnej klasy.  Kroki obejmują zainstalowanie usługi OneDrive i zainstalowanie Microsoft 365, zainstalowanie aktualizacji dla systemu Windows i zainstalowanie aktualizacji dla Microsoft Store aplikacji.  Omówiono również sposób ustawiania aktualizacji dla harmonogramu, który najlepiej sprawdza się w przypadku danej klasy.  

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z artykułem dotyczącym sposobu kontrolowania zachowania zamykania systemu Windows, aby ułatwić zarządzanie kosztami: [Przewodnik dotyczący kontrolowania zachowania zamykania systemu Windows](how-to-windows-shutdown.md)