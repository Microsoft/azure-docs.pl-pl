---
title: Windows Virtual Desktop aplikacji MSIX dołącz program PowerShell — Azure
description: How to set up MSIX app attach for Windows Virtual Desktop using PowerShell (Jak skonfigurować dołączanie aplikacji MSIX Windows Virtual Desktop programie PowerShell).
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f44cbf3764063c511c896f11bb7ebfaae2973f0c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365403"
---
# <a name="set-up-msix-app-attach-using-powershell"></a>Konfigurowanie dołączania aplikacji MSIX przy użyciu programu PowerShell

Oprócz tej Azure Portal można również ręcznie skonfigurować dołączanie aplikacji MSIX przy użyciu programu PowerShell. W tym artykule opisano sposób używania programu PowerShell do skonfigurowania dołączania aplikacji MSIX.

## <a name="requirements"></a>Wymagania

>[!IMPORTANT]
>Przed rozpoczęciem upewnij się, że wypełniasz i przesyłasz ten [formularz,](https://aka.ms/enablemsixappattach) aby włączyć dołączanie aplikacji MSIX w ramach subskrypcji. Jeśli nie masz zatwierdzonego żądania, dołączanie aplikacji MSIX nie będzie działać. Zatwierdzenie żądań może potrwać do 24 godzin w dniach roboczych. Po zaakceptowaniu i zakończeniu żądania otrzymasz wiadomość e-mail.

Oto, co jest potrzebne do skonfigurowania dołączania aplikacji MSIX:

- Działające wdrożenie Windows Virtual Desktop wdrożenia. Aby dowiedzieć się, jak wdrożyć Windows Virtual Desktop (klasyczne), zobacz [Tworzenie dzierżawy w](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)Windows Virtual Desktop . Aby dowiedzieć się, jak wdrożyć Windows Virtual Desktop z Azure Resource Manager integracji, zobacz Tworzenie puli [hostów przy](./create-host-pools-azure-marketplace.md)użyciu Azure Portal .
- Pula Windows Virtual Desktop z co najmniej jednym aktywnym hostem sesji.
- Ta pula hostów musi znajdować się w środowisku weryfikacji.
- Grupa zdalnych aplikacji pulpitu.
- Narzędzie do tworzenia pakietów MSIX.
- Aplikacja w pakiecie MSIX rozszerzona na obraz MSIX przekazany do udziału plików.
- Udział plików we wdrożeniu Windows Virtual Desktop, w którym będzie przechowywany pakiet MSIX.
- Udział plików, do którego został przekazany obraz MSIX, musi być również dostępny dla wszystkich maszyn wirtualnych w puli hostów. Użytkownicy będą potrzebować uprawnień tylko do odczytu, aby uzyskać dostęp do obrazu.
- Pobierz i zainstaluj PowerShell Core.
- Pobierz publiczną wersję zapoznawczą Azure PowerShell i rozwiń go do folderu lokalnego.
- Zainstaluj moduł platformy Azure, uruchamiając następujące polecenie cmdlet:

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Zaloguj się do platformy Azure i zaimportuj moduł

Po przygotowaniu wszystkich wymagań otwórz program PowerShell Core w wierszu polecenia z podwyższonym poziomem uprawnień i uruchom to polecenie cmdlet:

```powershell
Connect-AzAccount
```

Po jego uruchomieniu uwierzytelnij swoje konto przy użyciu swoich poświadczeń. W takim przypadku może zostać poproszony o adres URL urządzenia lub token.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Importowanie modułu Az.WindowsVirtualDesktop

Moduł Az.DesktopVirtualization będzie potrzebny, aby postępować zgodnie z instrukcjami w tym artykule.

>[!NOTE]
>W przypadku publicznej wersji zapoznawczej udostępnimy moduł jako oddzielne pliki ZIP, które należy zaimportować ręcznie.

Przed rozpoczęciem możesz uruchomić następujące polecenie cmdlet, aby sprawdzić, czy moduł Az.DesktopVirtualization został już zainstalowany w sesji lub na maszynie wirtualnej:

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Jeśli chcesz odinstalować istniejącą kopię modułu i zacząć od początku, uruchom to polecenie cmdlet:

```powershell
Uninstall-Module Az.DesktopVirtualization
```

Jeśli moduł jest zablokowany na maszynie wirtualnej, uruchom to polecenie cmdlet, aby go odblokować:

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

Po tym oczyszczeniu należy zaimportować moduł.

1. Uruchom następujące polecenie cmdlet, a następnie naciśnij klawisz **R** po wyświetleniu monitu o wyrażenie zgody na uruchomienie kodu niestandardowego.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. Po uruchomieniu polecenia cmdlet importu sprawdź, czy zawiera on polecenia cmdlet msix, uruchamiając następujące polecenie cmdlet:

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Jeśli polecenia cmdlet istnieją, dane wyjściowe powinny wyglądać tak:

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   Jeśli nie widzisz tych danych wyjściowych, zamknij wszystkie sesje programu PowerShell i PowerShell Core i spróbuj ponownie.

## <a name="set-up-helper-variables"></a>Konfigurowanie zmiennych pomocnika

Po zaimportowaniu modułu należy skonfigurować zmienne pomocnika. Poniższe przykłady pokazują, jak wykonać każdy z nich.

Aby uzyskać identyfikator subskrypcji:

```powershell
Get-AzContext -ListAvailable | fl
```

Aby wybrać kontekst dzierżawy i subskrypcji platformy Azure o nazwie:

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

Na koniec, aby potwierdzić, że prawidłowo ustawiono wszystkie zmienne:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>Dodawanie pakietu MSIX do puli hostów

Po skonfigurowaniu wszystkich zasobów należy dodać pakiet MSIX do puli hostów. W tym celu najpierw musisz uzyskać ścieżkę UNC do obrazu MSIX.

Przy użyciu ścieżki UNC uruchom to polecenie cmdlet, aby rozwinąć obraz MSIX:

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

Uruchom to polecenie cmdlet, aby dodać pakiet MSIX do żądanej puli hostów:

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

Po zakończeniu upewnij się, że pakiet został utworzony za pomocą tego polecenia cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>Usuwanie pakietu MSIX z puli hostów

Aby usunąć pakiet z puli hostów:

Pobierz listę wszystkich pakietów skojarzonych z pulą hostów za pomocą tego polecenia cmdlet, a następnie znajdź nazwę pakietu, który chcesz usunąć w danych wyjściowych:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

Możesz również pobrać określony pakiet na podstawie jego nazwy wyświetlanej za pomocą tego polecenia cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

Aby usunąć pakiet, uruchom to polecenie cmdlet:

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>Publikowanie aplikacji MSIX w grupie aplikacji

Instrukcje w tej sekcji można wykonać tylko po zakończeniu pracy z instrukcjami w poprzednich sekcjach. Jeśli masz pulę hostów z aktywnym hostem sesji, co najmniej jedną grupą aplikacji klasycznych i dodano pakiet MSIX do puli hostów, możesz rozpocząć.

Aby opublikować aplikację z pakietu MSIX w grupie aplikacji, należy znaleźć jej nazwę, a następnie użyć tej nazwy w poleceniach cmdlet publikowania.

Aby opublikować aplikację:

Uruchom to polecenie cmdlet, aby wyświetlić listę wszystkich dostępnych grup aplikacji:

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

Po odnalezioniu nazwy grupy aplikacji, w której chcesz publikować aplikacje, użyj jej nazwy w tym polecenia cmdlet:

```powershell
$grName = "<AppGroupName>"
```

Na koniec musisz opublikować aplikację.

- Aby opublikować aplikację MSIX w grupie aplikacji klasycznych, uruchom to polecenie cmdlet:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Aby opublikować aplikację w grupie aplikacji zdalnej, uruchom to polecenie cmdlet:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Jeśli użytkownik jest przypisany zarówno do grupy aplikacji zdalnych, jak i grupy aplikacji klasycznych w tej samej puli hostów, po nawiąże połączenie ze swoim pulpitem zdalnym, zobaczy aplikacje MSIX z obu grup.

## <a name="next-steps"></a>Następne kroki

Zadaj naszemu społeczności pytania dotyczące tej funkcji na stronie [Windows Virtual Desktop TechCo w witrynie](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Opinię na ten temat możesz również Windows Virtual Desktop w [centrum Windows Virtual Desktop opinii.](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)

Oto kilka innych artykułów, które mogą okazać się przydatne:

- [Słownik dołączania aplikacji MSIX](app-attach-glossary.md)
- [Dołączanie aplikacji MSIX — często zadawane pytania](app-attach-faq.md)
