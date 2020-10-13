---
title: Moduł programu PowerShell dla pulpitu wirtualnego systemu Windows — Azure
description: Jak zainstalować i skonfigurować moduł programu PowerShell dla pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2f01e2b58c997db08ad4427de7eef1ee3760c4a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323504"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Konfigurowanie modułu programu PowerShell dla pulpitu wirtualnego systemu Windows

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z integracją Azure Resource Manager.

Moduł programu PowerShell dla pulpitu wirtualnego systemu Windows jest zintegrowany z modułem Azure PowerShell. W tym artykule przedstawiono sposób konfigurowania modułu programu PowerShell, dzięki czemu można uruchamiać polecenia cmdlet dla pulpitu wirtualnego systemu Windows.

## <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell

Aby rozpocząć korzystanie z modułu, najpierw zainstaluj [najnowszą wersję programu PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core). Polecenia cmdlet pulpitu wirtualnego systemu Windows działają obecnie tylko z programem PowerShell Core.

Następnie należy zainstalować moduł DesktopVirtualization do użycia w sesji programu PowerShell.

Aby zainstalować moduł, uruchom następujące polecenie cmdlet programu PowerShell w trybie podniesionych uprawnień:

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> Jeśli to polecenie cmdlet nie działa, spróbuj uruchomić je ponownie z podniesionymi uprawnieniami.

Następnie uruchom następujące polecenie cmdlet, aby nawiązać połączenie z platformą Azure:

```powershell
Connect-AzAccount
```

>[!IMPORTANT]
>Jeśli łączysz się z portalem US Gov, Uruchom to polecenie cmdlet:
> 
> ```powershell
> Connect-AzAccount -EnvironmentName AzureUSGovernment
> ```

Zalogowanie się do konta platformy Azure wymaga kodu, który jest generowany po uruchomieniu polecenia cmdlet Connect. Aby się zalogować, przejdź do <https://microsoft.com/devicelogin> , wprowadź kod, a następnie zaloguj się przy użyciu poświadczeń administratora platformy Azure.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Spowoduje to zalogowanie się bezpośrednio do subskrypcji, która jest domyślna dla poświadczeń administratora.

## <a name="change-the-default-subscription"></a>Zmień domyślną subskrypcję

Jeśli chcesz zmienić domyślną subskrypcję po zalogowaniu, uruchom następujące polecenie cmdlet:

```powershell
Select-AzSubscription -Subscription <preferredsubscriptionname>
```

Możesz również wybrać jedną z listy za pomocą polecenia cmdlet Out-GridView:

```powershell
Get-AzSubscription | Out-GridView -PassThru | Select-AzSubscription
```

W przypadku wybrania nowej subskrypcji, która ma zostać użyta, nie trzeba określać jej identyfikatora w poleceniach cmdlet, które są uruchamiane w późniejszym czasie. Na przykład następujące polecenie cmdlet pobiera określonego hosta sesji bez potrzeby identyfikatora subskrypcji:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

Możesz również zmienić subskrypcje dla poszczególnych poleceń cmdlet, dodając żądaną nazwę subskrypcji jako parametr. Następne polecenie cmdlet jest takie samo jak w poprzednim przykładzie, z wyjątkiem identyfikatora subskrypcji dodanego jako parametr, aby zmienić subskrypcję używaną przez polecenie cmdlet.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>Pobierz lokalizacje

Parametr Location jest obowiązkowy dla wszystkich poleceń cmdlet **New-AzWVD** , które tworzą nowe obiekty.

Uruchom następujące polecenie cmdlet, aby uzyskać listę lokalizacji obsługiwanych przez subskrypcję:

```powershell
Get-AzLocation
```

Dane wyjściowe polecenia **Get-AzLocation** będą wyglądać następująco:

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

Gdy znasz lokalizację swojego konta, możesz go użyć w poleceniu cmdlet. Na przykład poniżej przedstawiono polecenie cmdlet, które tworzy pulę hostów w lokalizacji "southeastasia":

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu modułu programu PowerShell, możesz uruchomić polecenia cmdlet, aby wykonać wszystkie czynności w programie Virtual Desktop systemu Windows. Poniżej przedstawiono niektóre miejsca, w których można używać modułu:

- Uruchamiaj nasze [samouczki pulpitu wirtualnego systemu Windows]() , aby skonfigurować własne środowisko pulpitu wirtualnego systemu Windows.
- [Tworzenie puli hostów przy użyciu programu PowerShell](create-host-pools-powershell.md)
- [Konfigurowanie metody równoważenia obciążenia usługi Windows Virtual Desktop](configure-host-pool-load-balancing.md)
- [Konfigurowanie typu przypisania puli hostów pulpitu osobistego](configure-host-pool-personal-desktop-assignment-type.md)
- I wiele innych.

Jeśli napotkasz jakiekolwiek problemy, zapoznaj się [z artykułem dotyczącym rozwiązywania problemów](troubleshoot-powershell.md) z programem PowerShell.

