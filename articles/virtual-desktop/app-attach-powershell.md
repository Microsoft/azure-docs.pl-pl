---
title: Windows Virtual Desktop MSIX — dołączanie do wersji zapoznawczej programu PowerShell — Azure
description: Jak skonfigurować dołączenie aplikacji MSIX dla pulpitu wirtualnego systemu Windows przy użyciu programu PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 8b6bad32ec653fb2ba63c6940cf6a89a13a8afd0
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448325"
---
# <a name="set-up-msix-app-attach-preview-using-powershell"></a>Konfigurowanie dołączania aplikacji MSIX (wersja zapoznawcza) przy użyciu programu PowerShell

> [!IMPORTANT]
> Dołączenie do aplikacji MSIX jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Oprócz Azure Portal można także skonfigurować dołączenie aplikacji MSIX (wersja zapoznawcza) ręcznie przy użyciu programu PowerShell. W tym artykule opisano sposób korzystania z programu PowerShell w celu skonfigurowania dołączania aplikacji MSIX.

## <a name="requirements"></a>Wymagania

>[!IMPORTANT]
>Przed rozpoczęciem upewnij się, że wypełniasz i prześlesz [ten formularz](https://aka.ms/enablemsixappattach) , aby umożliwić dołączenie aplikacji MSIX do subskrypcji. Jeśli nie masz zatwierdzonego żądania, dołączenie do aplikacji MSIX nie będzie działało. Zatwierdzenie żądań może potrwać do 24 godzin w dniach roboczych. Po zaakceptowaniu i zakończeniu żądania otrzymasz wiadomość e-mail.

Oto, co jest potrzebne do skonfigurowania dołączania aplikacji MSIX:

- Działające wdrożenie pulpitu wirtualnego systemu Windows. Aby dowiedzieć się, jak wdrożyć pulpit wirtualny systemu Windows (klasyczny), zobacz [Tworzenie dzierżawy w systemie Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Aby dowiedzieć się, jak wdrożyć pulpit wirtualny systemu Windows z integracją Azure Resource Manager, zobacz [Tworzenie puli hostów przy użyciu Azure Portal](./create-host-pools-azure-marketplace.md).
- Pula hostów pulpitu wirtualnego systemu Windows z co najmniej jednym aktywnym hostem sesji.
- Ta Pula hostów musi znajdować się w środowisku walidacji.
- Zdalna Grupa aplikacji pulpitu.
- Narzędzie MSIX pakowanie.
- MSIX — spakowana aplikacja rozwinięta do obrazu MSIX, który jest przekazywany do udziału plików.
- Udział plików w ramach wdrożenia pulpitu wirtualnego systemu Windows, w którym będzie przechowywany pakiet MSIX.
- Udział plików, do którego został przekazany obraz MSIX, musi być również dostępny dla wszystkich maszyn wirtualnych w puli hostów. Użytkownicy będą musieli mieć uprawnienia tylko do odczytu w celu uzyskania dostępu do obrazu.
- Pobierz i zainstaluj program PowerShell Core.
- Pobierz publiczną wersję zapoznawczą Azure PowerShell module i rozwiń ją do folderu lokalnego.
- Zainstaluj moduł platformy Azure, uruchamiając następujące polecenie cmdlet:

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Zaloguj się do platformy Azure i zaimportuj moduł

Gdy wszystkie wymagania są gotowe, Otwórz program PowerShell Core w wierszu polecenia z podwyższonym poziomem uprawnień i Uruchom to polecenie cmdlet:

```powershell
Connect-AzAccount
```

Po uruchomieniu usługi należy uwierzytelnić konto przy użyciu swoich poświadczeń. W takim przypadku może zostać wyświetlony monit o podanie adresu URL urządzenia lub tokenu.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Zaimportuj moduł AZ. WindowsVirtualDesktop

Aby postępować zgodnie z instrukcjami w tym artykule, musisz mieć moduł AZ. DesktopVirtualization.

>[!NOTE]
>W publicznej wersji zapoznawczej firma Microsoft udostępni moduł jako osobne pliki ZIP, które trzeba ręcznie zaimportować.

Przed rozpoczęciem można uruchomić następujące polecenie cmdlet, aby sprawdzić, czy moduł AZ. DesktopVirtualization jest już zainstalowany w sesji lub maszynie wirtualnej:

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Jeśli sieć WAN ma zostać odinstalowana z istniejącej kopii modułu i uruchomiona, uruchom następujące polecenie cmdlet:

```powershell
Uninstall-Module Az.DesktopVirtualization
```

Jeśli moduł jest zablokowany na maszynie wirtualnej, Uruchom to polecenie cmdlet w celu odblokowania:

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

Dzięki temu oczyszczaniu można zaimportować moduł.

1. Uruchom następujące polecenie cmdlet, a następnie naciśnij klawisz **R** po wyświetleniu monitu, aby wyrazić zgodę na uruchomienie niestandardowego kodu.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. Po uruchomieniu polecenia cmdlet Import Sprawdź, czy ma ono polecenia cmdlet programu MSIX, uruchamiając następujące polecenie cmdlet:

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Jeśli tam znajdują się polecenia cmdlet, dane wyjściowe powinny wyglądać następująco:

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   Jeśli nie widzisz tych danych wyjściowych, zamknij wszystkie sesje programu PowerShell i programu PowerShell Core i spróbuj ponownie.

## <a name="set-up-helper-variables"></a>Skonfiguruj zmienne pomocnika

Po zaimportowaniu modułu należy skonfigurować zmienne pomocnika. W poniższych przykładach pokazano, jak wykonać każdą z nich.

Aby uzyskać identyfikator subskrypcji:

```powershell
Get-AzContext -ListAvailable | fl
```

Aby wybrać kontekst dzierżawy i subskrypcję platformy Azure o nazwie:

```powershell
$obj = Select-AzContext -Name "<Name>"
```

Aby ustawić zmienną subskrypcji:

```powershell
$subId = $obj.Subscription.Id
```

Aby ustawić nazwę obszaru roboczego:

```powershell
$ws = "<WorksSpaceName>"
```

Aby ustawić nazwę puli hostów:

```powershell
$hp = "<HostPoolName>"
```

Aby skonfigurować grupę zasobów, w której skonfigurowano maszyny wirtualne hosta sesji:

```powershell
$rg = "<ResourceGroupName>"
```

A wreszcie, aby upewnić się, że zostały prawidłowo ustawione wszystkie zmienne:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>Dodawanie pakietu MSIX do puli hostów

Po skonfigurowaniu wszystkiego czasowego należy dodać pakiet MSIX do puli hostów. Aby to zrobić, najpierw musisz uzyskać ścieżkę UNC do obrazu MSIX.

Używając ścieżki UNC, Uruchom to polecenie cmdlet w celu rozszerzenia obrazu MSIX:

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

Uruchom to polecenie cmdlet, aby dodać pakiet MSIX do żądanej puli hostów:

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

Gdy wszystko będzie gotowe, potwierdź, że pakiet został utworzony za pomocą tego polecenia cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>Usuwanie pakietu MSIX z puli hostów

Aby usunąć pakiet z puli hostów:

Pobierz listę wszystkich pakietów skojarzonych z pulą hostów za pomocą tego polecenia cmdlet, a następnie Znajdź nazwę pakietu, który chcesz usunąć w danych wyjściowych:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

Alternatywnie można również uzyskać określony pakiet na podstawie jego nazwy wyświetlanej za pomocą tego polecenia cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

Aby usunąć pakiet, uruchom następujące polecenie cmdlet:

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>Publikowanie aplikacji MSIX w grupie aplikacji

Instrukcje podane w tej sekcji można wykonać tylko po zakończeniu wykonywania instrukcji opisanych w poprzednich sekcjach. Jeśli masz pulę hostów z aktywnym hostem sesji, co najmniej jedną grupą aplikacji klasycznych i dodano pakiet MSIX do puli hostów, możesz rozpocząć pracę.

Aby opublikować aplikację z pakietu MSIX w grupie aplikacji, musisz znaleźć jej nazwę, a następnie użyć tej nazwy w poleceniu cmdlet do publikowania.

Aby opublikować aplikację:

Uruchom to polecenie cmdlet, aby wyświetlić listę wszystkich dostępnych grup aplikacji:

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

Po znalezieniu nazwy grupy aplikacji, w której chcesz opublikować aplikacje, użyj jej nazwy w tym poleceniu cmdlet:

```powershell
$grName = "<AppGroupName>"
```

Na koniec należy opublikować aplikację.

- Aby opublikować aplikację MSIX w grupie aplikacji klasycznych, uruchom następujące polecenie cmdlet:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Aby opublikować aplikację w zdalnej grupie aplikacji, Uruchom to polecenie cmdlet:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Jeśli użytkownik jest przypisany do grupy aplikacji zdalnych i grupy aplikacji klasycznych w tej samej puli hostów, gdy użytkownik nawiąże połączenie z pulpitem zdalnym, zobaczy aplikacje MSIX z obu grup.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z naszymi pytaniami dotyczącymi tej funkcji w [TechCommunity pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Możesz również wystawić opinię na temat pulpitu wirtualnego systemu Windows w [centrum opinii pulpitu wirtualnego systemu Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Poniżej znajdują się inne artykuły, które mogą okazać się przydatne:

- [Dołączanie słownika aplikacji MSIX](app-attach-glossary.md)
- [Załączanie aplikacji MSIX — często zadawane pytania](app-attach-faq.md)
