---
title: Tworzenie puli hostów usług pulpitu wirtualnego systemu Windows PowerShell — Azure
description: Jak utworzyć pulę hostów w programie Virtual Desktop systemu Windows przy użyciu poleceń cmdlet programu PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6b064c6e4107da5695e2a9945240e4276ac795b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85211854"
---
# <a name="create-a-host-pool-with-powershell"></a>Tworzenie puli hostów przy użyciu programu PowerShell

>[!IMPORTANT]
>Ta zawartość ma zastosowanie do aktualizacji wiosennej 2020 z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows, wykorzystaj wersję 2019 bez obiektów Azure Resource Manager, zobacz [ten artykuł](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md).
>
> Aktualizacja systemu Windows Virtual Desktop wiosna 2020 jest obecnie dostępna w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pule hostów są kolekcją co najmniej jednej identycznej maszyny wirtualnej w środowiskach dzierżawy usług pulpitu wirtualnego systemu Windows. Każda pula hostów może być skojarzona z wieloma grupami usługi RemoteApp, jedną grupą aplikacji klasycznymi i wieloma hostami sesji.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że wykonano już instrukcje podane w temacie [Konfigurowanie modułu programu PowerShell](powershell-module.md).

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Tworzenie puli hostów za pomocą klienta programu PowerShell

Uruchom następujące polecenie cmdlet, aby zalogować się do środowiska pulpitu wirtualnego systemu Windows:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname>
```

To polecenie cmdlet spowoduje utworzenie puli hostów, obszaru roboczego i grupy aplikacji klasycznych. Ponadto będzie ona rejestrować grupę aplikacji klasycznych w obszarze roboczym. Możesz utworzyć obszar roboczy za pomocą tego polecenia cmdlet lub użyć istniejącego obszaru roboczego.

Uruchom następne polecenie cmdlet, aby utworzyć token rejestracji służący do autoryzowania hosta sesji do przyłączenia do puli hostów i zapisania go w nowym pliku na komputerze lokalnym. Możesz określić, jak długo token rejestracji jest prawidłowy przy użyciu parametru-ExpirationHours.

>[!NOTE]
>Data wygaśnięcia tokenu nie może być krótsza niż godzina i nie więcej niż jeden miesiąc. Jeśli ustawisz parametr *-ExpirationTime* poza tym limitem, polecenie cmdlet nie utworzy tokenu.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Jeśli na przykład chcesz utworzyć token, który wygaśnie w ciągu dwóch godzin, uruchom następujące polecenie cmdlet:

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Następnie należy uruchomić to polecenie cmdlet, aby dodać Azure Active Directory użytkowników do domyślnej grupy aplikacji klasycznych dla puli hostów.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Uruchom to następne polecenie cmdlet, aby dodać Azure Active Directory grupy użytkowników do domyślnej grupy aplikacji klasycznych dla puli hostów:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+“-DAG”> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Uruchom następujące polecenie cmdlet, aby wyeksportować token rejestracji do zmiennej, która będzie używana później do [rejestrowania maszyn wirtualnych w puli hostów usług pulpitu wirtualnego systemu Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname>
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Tworzenie maszyn wirtualnych dla puli hostów

Teraz można utworzyć maszynę wirtualną platformy Azure, która może być przyłączona do puli hostów pulpitu wirtualnego systemu Windows.

Maszynę wirtualną można utworzyć na wiele sposobów:

- [Tworzenie maszyny wirtualnej na podstawie obrazu z galerii platformy Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Tworzenie maszyny wirtualnej na podstawie obrazu zarządzanego](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Tworzenie maszyny wirtualnej na podstawie obrazu niezarządzanego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>W przypadku wdrażania maszyny wirtualnej przy użyciu systemu Windows 7 jako systemu operacyjnego hosta proces tworzenia i wdrażania będzie nieco inny. Aby uzyskać więcej informacji, zobacz [Wdrażanie maszyny wirtualnej z systemem Windows 7 na pulpicie wirtualnym systemu Windows](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

Po utworzeniu maszyn wirtualnych hosta sesji należy [zastosować licencję systemu Windows na maszynę wirtualną hosta sesji](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) do uruchamiania maszyn wirtualnych z systemem Windows lub Windows Server bez płacenia za inną licencję.

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Przygotowywanie maszyn wirtualnych do instalacji agenta pulpitu wirtualnego systemu Windows

Przed zainstalowaniem agentów pulpitu wirtualnego systemu Windows i zarejestrowaniem maszyn wirtualnych w puli hostów Windows Virtual Desktop należy wykonać następujące czynności:

- Należy przyłączyć komputer do domeny. Dzięki temu Użytkownicy pulpitu wirtualnego systemu Windows mogą być zamapowane z konta Azure Active Directory na konto Active Directory i pomyślnie zezwalać na dostęp do maszyny wirtualnej.
- Jeśli na maszynie wirtualnej jest uruchomiony system operacyjny Windows Server, należy zainstalować rolę hosta sesji Pulpit zdalny. Rola hosta sesji usług pulpitu zdalnego umożliwia poprawne instalowanie agentów pulpitu wirtualnego systemu Windows.

Aby pomyślnie przyłączyć do domeny, wykonaj następujące czynności na każdej maszynie wirtualnej:

1. [Połącz się z maszyną wirtualną](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) przy użyciu poświadczeń podanych podczas tworzenia maszyny wirtualnej.
2. Na maszynie wirtualnej Uruchom **Panel sterowania** , a następnie wybierz pozycję **system**.
3. Wybierz pozycję **Nazwa komputera**, wybierz pozycję **Zmień ustawienia**, a następnie wybierz pozycję **Zmień.**
4. Wybierz pozycję **domena** , a następnie wprowadź domenę Active Directory w sieci wirtualnej.
5. Uwierzytelnianie przy użyciu konta domeny, które ma uprawnienia do komputerów przyłączania do domeny.

    >[!NOTE]
    > W przypadku dołączania maszyn wirtualnych do środowiska Azure Active Directory Domain Services (Azure AD DS) Upewnij się, że użytkownik przyłączania do domeny jest również członkiem [grupy Administratorzy kontrolera domeny usługi AAD](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Rejestrowanie maszyn wirtualnych w puli hostów usług pulpitu wirtualnego systemu Windows

Rejestrowanie maszyn wirtualnych w puli hostów usług pulpitu wirtualnego systemu Windows jest tak proste jak zainstalowanie agentów pulpitu wirtualnego systemu Windows.

Aby zarejestrować agentów pulpitu wirtualnego systemu Windows, wykonaj następujące czynności na każdej maszynie wirtualnej:

1. [Połącz się z maszyną wirtualną](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) przy użyciu poświadczeń podanych podczas tworzenia maszyny wirtualnej.
2. Pobierz i Zainstaluj agenta pulpitu wirtualnego systemu Windows.
   - Pobierz [agenta pulpitu wirtualnego systemu Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Uruchom instalatora. Gdy Instalator monituje o token rejestracji, wprowadź wartość uzyskaną z polecenia cmdlet **Get-AzWvdRegistrationInfo** .
3. Pobierz i zainstaluj program inicjujący agenta pulpitu wirtualnego systemu Windows.
   - Pobierz program [inicjujący agenta pulpitu wirtualnego systemu Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Uruchom instalatora.

>[!IMPORTANT]
>Aby zabezpieczyć środowisko pulpitu wirtualnego systemu Windows na platformie Azure, zalecamy, aby nie otwierać portu przychodzącego 3389 na maszynach wirtualnych. Pulpit wirtualny systemu Windows nie wymaga otwartego portu przychodzącego 3389 dla użytkowników w celu uzyskania dostępu do maszyn wirtualnych puli hostów. Jeśli musisz otworzyć port 3389 w celu rozwiązywania problemów, zalecamy użycie [dostępu just in Time do maszyny wirtualnej](../security-center/security-center-just-in-time.md). Zalecamy również, aby nie przypisywać maszyn wirtualnych do publicznego adresu IP.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy została utworzona Pula hostów, możesz ją wypełnić za pomocą usługi RemoteApp. Aby dowiedzieć się więcej o sposobach zarządzania aplikacjami w programie Virtual Desktop systemu Windows, zobacz samouczek zarządzanie grupami aplikacji.

> [!div class="nextstepaction"]
> [Samouczek zarządzania grupami aplikacji](./manage-app-groups.md)
