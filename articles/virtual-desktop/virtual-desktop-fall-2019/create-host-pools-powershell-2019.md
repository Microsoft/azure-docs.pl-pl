---
title: Tworzenie puli hostów systemu Windows Virtual Desktop (klasycznej) programu PowerShell — Azure
description: Jak utworzyć pulę hostów w systemie Windows Virtual Desktop (klasyczny) przy użyciu poleceń cmdlet programu PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c035a7fbafe9b3a42fbd16e3f8377014010ddd49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88003550"
---
# <a name="create-a-host-pool-in-windows-virtual-desktop-classic-with-powershell"></a>Tworzenie puli hostów w systemie Windows Virtual Desktop (klasyczny) przy użyciu programu PowerShell

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows, zobacz [ten artykuł](../create-host-pools-powershell.md).

Pule hostów są kolekcją co najmniej jednej identycznej maszyny wirtualnej w środowiskach dzierżawy usług pulpitu wirtualnego systemu Windows. Każda pula hostów może zawierać grupę aplikacji, z którą użytkownicy mogą wchodzić w pracę, tak jak na pulpicie fizycznym.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Tworzenie puli hostów za pomocą klienta programu PowerShell

Najpierw [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono.

Uruchom następujące polecenie cmdlet, aby zalogować się do środowiska pulpitu wirtualnego systemu Windows

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Następnie Uruchom to polecenie cmdlet, aby utworzyć nową pulę hostów w dzierżawie pulpitu wirtualnego systemu Windows:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Uruchom następne polecenie cmdlet, aby utworzyć token rejestracji służący do autoryzowania hosta sesji do przyłączenia do puli hostów i zapisania go w nowym pliku na komputerze lokalnym. Możesz określić, jak długo token rejestracji jest prawidłowy przy użyciu parametru-ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Następnie należy uruchomić to polecenie cmdlet, aby dodać Azure Active Directory użytkowników do domyślnej grupy aplikacji klasycznych dla puli hostów.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

Polecenie cmdlet **Add-RdsAppGroupUser** nie obsługuje dodawania grup zabezpieczeń i dodaje tylko jednego użytkownika jednocześnie do grupy aplikacji. Jeśli chcesz dodać wielu użytkowników do grupy aplikacji, uruchom ponownie polecenie cmdlet z odpowiednimi nazwami główną użytkownika.

Uruchom następujące polecenie cmdlet, aby wyeksportować token rejestracji do zmiennej, która będzie używana później do [rejestrowania maszyn wirtualnych w puli hostów usług pulpitu wirtualnego systemu Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Tworzenie maszyn wirtualnych dla puli hostów

Teraz można utworzyć maszynę wirtualną platformy Azure, która może być przyłączona do puli hostów pulpitu wirtualnego systemu Windows.

Maszynę wirtualną można utworzyć na wiele sposobów:

- [Tworzenie maszyny wirtualnej na podstawie obrazu z galerii platformy Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Tworzenie maszyny wirtualnej na podstawie obrazu zarządzanego](../../virtual-machines/windows/create-vm-generalized-managed.md)
- [Tworzenie maszyny wirtualnej na podstawie obrazu niezarządzanego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

>[!NOTE]
>W przypadku wdrażania maszyny wirtualnej przy użyciu systemu Windows 7 jako systemu operacyjnego hosta proces tworzenia i wdrażania będzie nieco inny. Aby uzyskać więcej informacji, zobacz [Wdrażanie maszyny wirtualnej z systemem Windows 7 na pulpicie wirtualnym systemu Windows](deploy-windows-7-virtual-machine.md).

Po utworzeniu maszyn wirtualnych hosta sesji należy [zastosować licencję systemu Windows na maszynę wirtualną hosta sesji](../apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) do uruchamiania maszyn wirtualnych z systemem Windows lub Windows Server bez płacenia za inną licencję.

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Przygotowywanie maszyn wirtualnych do instalacji agenta pulpitu wirtualnego systemu Windows

Przed zainstalowaniem agentów pulpitu wirtualnego systemu Windows i zarejestrowaniem maszyn wirtualnych w puli hostów Windows Virtual Desktop należy wykonać następujące czynności:

- Należy przyłączyć komputer do domeny. Dzięki temu Użytkownicy pulpitu wirtualnego systemu Windows mogą być zamapowane z konta Azure Active Directory na konto Active Directory i pomyślnie zezwalać na dostęp do maszyny wirtualnej.
- Jeśli na maszynie wirtualnej jest uruchomiony system operacyjny Windows Server, należy zainstalować rolę hosta sesji Pulpit zdalny. Rola hosta sesji usług pulpitu zdalnego umożliwia poprawne instalowanie agentów pulpitu wirtualnego systemu Windows.

Aby pomyślnie przyłączyć do domeny, wykonaj następujące czynności na każdej maszynie wirtualnej:

1. [Połącz się z maszyną wirtualną](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) przy użyciu poświadczeń podanych podczas tworzenia maszyny wirtualnej.
2. Na maszynie wirtualnej Uruchom **Panel sterowania** , a następnie wybierz pozycję **system**.
3. Wybierz pozycję **Nazwa komputera**, wybierz pozycję **Zmień ustawienia**, a następnie wybierz pozycję **Zmień.**
4. Wybierz pozycję **domena** , a następnie wprowadź domenę Active Directory w sieci wirtualnej.
5. Uwierzytelnianie przy użyciu konta domeny, które ma uprawnienia do komputerów przyłączania do domeny.

    >[!NOTE]
    > W przypadku dołączania maszyn wirtualnych do środowiska Azure Active Directory Domain Services (Azure AD DS) Upewnij się, że użytkownik przyłączania do domeny jest również członkiem [grupy Administratorzy kontrolera domeny usługi AAD](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Rejestrowanie maszyn wirtualnych w puli hostów usług pulpitu wirtualnego systemu Windows

Rejestrowanie maszyn wirtualnych w puli hostów usług pulpitu wirtualnego systemu Windows jest tak proste jak zainstalowanie agentów pulpitu wirtualnego systemu Windows.

Aby zarejestrować agentów pulpitu wirtualnego systemu Windows, wykonaj następujące czynności na każdej maszynie wirtualnej:

1. [Połącz się z maszyną wirtualną](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) przy użyciu poświadczeń podanych podczas tworzenia maszyny wirtualnej.
2. Pobierz i Zainstaluj agenta pulpitu wirtualnego systemu Windows.
   - Pobierz [agenta pulpitu wirtualnego systemu Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Kliknij prawym przyciskiem myszy pobrany Instalator, wybierz polecenie **Właściwości**, wybierz opcję **odblokowywanie**, a następnie wybierz **przycisk OK**. Pozwoli to systemowi na zaufanie Instalatora.
   - Uruchom instalatora. Gdy Instalator monituje o token rejestracji, wprowadź wartość uzyskaną z polecenia cmdlet **Export-RdsRegistrationInfo** .
3. Pobierz i zainstaluj program inicjujący agenta pulpitu wirtualnego systemu Windows.
   - Pobierz program [inicjujący agenta pulpitu wirtualnego systemu Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Kliknij prawym przyciskiem myszy pobrany Instalator, wybierz polecenie **Właściwości**, wybierz opcję **odblokowywanie**, a następnie wybierz **przycisk OK**. Pozwoli to systemowi na zaufanie Instalatora.
   - Uruchom instalatora.

>[!IMPORTANT]
>Aby zabezpieczyć środowisko pulpitu wirtualnego systemu Windows na platformie Azure, zalecamy, aby nie otwierać portu przychodzącego 3389 na maszynach wirtualnych. Pulpit wirtualny systemu Windows nie wymaga otwartego portu przychodzącego 3389 dla użytkowników w celu uzyskania dostępu do maszyn wirtualnych puli hostów. Jeśli musisz otworzyć port 3389 w celu rozwiązywania problemów, zalecamy użycie [dostępu just in Time do maszyny wirtualnej](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy została utworzona Pula hostów, możesz ją wypełnić za pomocą usługi RemoteApp. Aby dowiedzieć się więcej o sposobach zarządzania aplikacjami w programie Virtual Desktop systemu Windows, zobacz samouczek zarządzanie grupami aplikacji.

> [!div class="nextstepaction"]
> [Samouczek zarządzania grupami aplikacji](../manage-app-groups.md)
