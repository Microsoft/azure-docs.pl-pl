---
title: Włączanie Pulpit zdalny dla roli przy użyciu programu PowerShell
description: Jak skonfigurować aplikację usługi w chmurze platformy Azure przy użyciu programu PowerShell, aby zezwolić na połączenia pulpitu zdalnego
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 5b1650edb575de8fd59ad2495dafcd628a717c02
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102610403"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-powershell"></a>Włączanie Podłączanie pulpitu zdalnego roli na platformie Azure Cloud Services (klasyczny) przy użyciu programu PowerShell

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Program PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Pulpit zdalny umożliwia dostęp do pulpitu roli działającej na platformie Azure. Połączenia Pulpit zdalny można użyć do rozwiązywania problemów i diagnozowania problemów z aplikacją, gdy jest ona uruchomiona.

W tym artykule opisano sposób włączania pulpitu zdalnego w rolach usługi w chmurze przy użyciu programu PowerShell. Zapoznaj się z tematem [Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/) wymagań wstępnych dotyczących tego artykułu. Program PowerShell używa rozszerzenia Pulpit zdalny, aby można było włączyć Pulpit zdalny po wdrożeniu aplikacji.

## <a name="configure-remote-desktop-from-powershell"></a>Konfigurowanie Pulpit zdalny przy użyciu programu PowerShell
Polecenie cmdlet [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) umożliwia włączenie pulpit zdalny w określonych rolach lub wszystkich rolach wdrożenia usługi w chmurze. Polecenie cmdlet pozwala określić nazwę użytkownika i hasło dla użytkownika pulpitu zdalnego za pomocą parametru *Credential* , który akceptuje obiekt PSCredential.

Jeśli używasz programu PowerShell interaktywnie, możesz łatwo ustawić obiekt PSCredential, wywołując polecenie cmdlet [Get-Credentials](/powershell/module/microsoft.powershell.security/get-credential) .

```powershell
$remoteusercredentials = Get-Credential
```

To polecenie wyświetla okno dialogowe, w którym można bezpiecznie wprowadzić nazwę użytkownika i hasło dla użytkownika zdalnego.

Ponieważ program PowerShell ułatwia scenariusze automatyzacji, można także skonfigurować obiekt **PSCredential** w sposób, który nie wymaga interakcji z użytkownikiem. Najpierw należy skonfigurować bezpieczne hasło. Zacznij od określenia hasła w postaci zwykłego tekstu, konwertując go na bezpieczny ciąg za pomocą [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring). Następnie należy przekonwertować ten bezpieczny ciąg na zaszyfrowany ciąg standardowy przy użyciu [ConvertFrom-SecureString](/powershell/module/microsoft.powershell.security/convertfrom-securestring). Teraz można zapisać ten szyfrowany ciąg standardowy do pliku przy użyciu polecenia [Set-Content](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176959(v=technet.10)).

Możesz również utworzyć bezpieczny plik hasła, aby nie trzeba było wpisywać hasła za każdym razem. Ponadto bezpieczny plik hasła jest lepszy niż zwykły plik tekstowy. Użyj następującego programu PowerShell, aby utworzyć bezpieczny plik hasła:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Podczas ustawiania hasła upewnij się, że spełniasz [wymagania dotyczące złożoności](/previous-versions/windows/it-pro/windows-server-2003/cc786468(v=ws.10)).

Aby utworzyć obiekt Credential z bezpiecznego hasła, należy odczytać zawartość pliku i przekonwertować ją z powrotem na bezpieczny ciąg za pomocą [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring).

Polecenie cmdlet [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) akceptuje również parametr *wygaśnięcia* , który określa **datę i godzinę** wygaśnięcia konta użytkownika. Można na przykład ustawić, że konto wygaśnie kilka dni od bieżącej daty i godziny.

Ten przykład programu PowerShell pokazuje, jak ustawić rozszerzenie Pulpit zdalny w usłudze w chmurze:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Opcjonalnie można również określić miejsce wdrożenia i role, dla których chcesz włączyć pulpit zdalny. Jeśli te parametry nie zostaną określone, polecenie cmdlet włącza pulpit zdalny na wszystkich rolach w **produkcyjnym** miejscu wdrożenia.

Rozszerzenie Pulpit zdalny jest skojarzone ze wdrożeniem. Jeśli tworzysz nowe wdrożenie dla usługi, musisz włączyć pulpit zdalny dla tego wdrożenia. Jeśli chcesz, aby pulpit zdalny był włączony, należy rozważyć integrację skryptów programu PowerShell z przepływem pracy wdrożenia.

## <a name="remote-desktop-into-a-role-instance"></a>Pulpit zdalny do wystąpienia roli

Polecenie cmdlet [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) jest używane do zdalnego pulpitu w określonym wystąpieniu roli usługi w chmurze. Aby pobrać plik RDP lokalnie, można użyć parametru *LocalPath* . Można też użyć parametru *uruchamiania* , aby bezpośrednio uruchomić okno dialogowe Podłączanie pulpitu zdalnego, aby uzyskać dostęp do wystąpienia roli usługi w chmurze.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Sprawdź, czy w usłudze jest włączone rozszerzenie Pulpit zdalny

Polecenie cmdlet [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) wyświetla, że pulpit zdalny jest włączony lub wyłączony w ramach wdrożenia usługi. Polecenie cmdlet zwraca nazwę użytkownika pulpitu zdalnego i role, dla których włączono rozszerzenie pulpitu zdalnego. Domyślnie dzieje się to w miejscu wdrożenia i można wybrać zamiast niego użycie miejsca przejściowego.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Usuwanie rozszerzenia Pulpit zdalny z usługi

Jeśli włączono już rozszerzenie pulpitu zdalnego we wdrożeniu i musisz zaktualizować ustawienia pulpitu zdalnego, najpierw Usuń rozszerzenie. I włącz ją ponownie przy użyciu nowych ustawień. Na przykład jeśli chcesz ustawić nowe hasło dla konta użytkownika zdalnego lub konto wygasło. Jest to wymagane w przypadku istniejących wdrożeń z włączonym rozszerzeniem pulpitu zdalnego. W przypadku nowych wdrożeń można po prostu bezpośrednio zastosować rozszerzenie.

Aby usunąć rozszerzenie pulpitu zdalnego z wdrożenia, można użyć polecenia cmdlet [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/remove-azureserviceremotedesktopextension) . Opcjonalnie możesz również określić miejsce wdrożenia i rolę, z której chcesz usunąć rozszerzenie pulpitu zdalnego.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Aby całkowicie usunąć konfigurację rozszerzenia, należy wywołać polecenie cmdlet *Remove* z parametrem **UninstallConfiguration** .
>
> Parametr **UninstallConfiguration** Odinstalowuje wszystkie konfiguracje rozszerzeń, które są stosowane do usługi. Każda konfiguracja rozszerzenia jest skojarzona z konfiguracją usługi. Wywołanie polecenia cmdlet *Remove* bez **UninstallConfiguration** powoduje skojarzenie <mark>wdrożenia</mark> z konfiguracją rozszerzenia, co skutecznie usuwa rozszerzenie. Jednak konfiguracja rozszerzenia pozostaje skojarzona z usługą.

## <a name="additional-resources"></a>Dodatkowe zasoby

[Jak skonfigurować Cloud Services](cloud-services-how-to-configure-portal.md)