---
title: Windows Virtual Desktop (klasyczny) PowerShell — Azure
description: Jak rozwiązywać problemy z programem PowerShell podczas konfigurowania środowiska dzierżawy pulpitu wirtualnego systemu Windows (klasycznego).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 08c9f7a6c5f8fd4a51c464018438bf6e7db119fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374902"
---
# <a name="windows-virtual-desktop-classic-powershell"></a>Windows Virtual Desktop (klasyczny) PowerShell

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows, zobacz [ten artykuł](../troubleshoot-powershell.md).

W tym artykule opisano błędy i problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows. Aby uzyskać więcej informacji na Usługi pulpitu zdalnego PowerShell, zobacz [Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview).

## <a name="provide-feedback"></a>Wyraź opinię

Odwiedź [społeczność Tech. pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , aby omówić usługę pulpitu wirtualnego systemu Windows z zespołem produktu i aktywnymi członkami społeczności.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Polecenia programu PowerShell używane podczas instalacji pulpitu wirtualnego systemu Windows

Ta sekcja zawiera listę poleceń programu PowerShell, które są zwykle używane podczas konfigurowania pulpitu wirtualnego systemu Windows i zapewnia sposoby rozwiązywania problemów, które mogą wystąpić podczas ich używania.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Błąd: Add-RdsAppGroupUser polecenie--określony element UserPrincipalName jest już przypisany do grupy aplikacji RemoteApp w określonej puli hostów

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Przyczyna:** Użyta nazwa użytkownika została już przypisana do grupy aplikacji innego typu. Użytkownicy nie mogą być przypisani zarówno do pulpitu zdalnego, jak i zdalnej grupy aplikacji w ramach tej samej puli hostów sesji.

**Poprawka:** Jeśli użytkownik potrzebuje zarówno aplikacji zdalnych, jak i pulpitu zdalnego, należy utworzyć różne pule hostów lub udzielić użytkownikowi dostępu do pulpitu zdalnego, co pozwoli na korzystanie z dowolnej aplikacji na maszynie wirtualnej hosta sesji.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Błąd: Add-RdsAppGroupUser polecenie — określony element UserPrincipalName nie istnieje w Azure Active Directory skojarzonym z dzierżawą Pulpit zdalny

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Przyczyna:** Nie można znaleźć użytkownika określonego przez-UserPrincipalName w Azure Active Directory powiązanym z dzierżawą pulpitu wirtualnego systemu Windows.

**Poprawka:** Potwierdź elementy z poniższej listy.

- Użytkownik jest zsynchronizowany do Azure Active Directory.
- Użytkownik nie jest powiązany z handlem firmy z klientem (B2C) ani z firmą B2B (Business-to-Business).
- Dzierżawa pulpitu wirtualnego systemu Windows jest powiązana z poprawnym Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Błąd: Get-RdsDiagnosticActivities — użytkownik nie ma uprawnień do wysyłania zapytań do usługi zarządzania

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Przyczyna:** -dzierżawca parametru

**Poprawka:** Wystaw Get-RdsDiagnosticActivities z-dzierżawcą \<TenantName> .

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Błąd: Get-RdsDiagnosticActivities — użytkownik nie ma uprawnień do wysyłania zapytań do usługi zarządzania

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Przyczyna:** Użycie przełącznika-Deployment.

**Naprawa:** przełącznik wdrożenia może być używany tylko przez administratorów wdrożenia. Ci Administratorzy są zwykle członkami zespołu pulpitów wirtualnych Usługi pulpitu zdalnego/Windows. Zastąp przełącznik-Deploymentname \<TenantName> .

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Błąd: New-RdsRoleAssignment — użytkownik nie ma uprawnień do wysyłania zapytań do usługi zarządzania

**Przyczyna 1:** Używane konto nie ma uprawnień właściciela Usługi pulpitu zdalnego w dzierżawie.

**Poprawka 1:** Użytkownik z uprawnieniami Usługi pulpitu zdalnego właściciela musi wykonać przypisanie roli.

**Przyczyna 2:** Używane konto ma Usługi pulpitu zdalnego uprawnienia właściciela, ale nie jest częścią Azure Active Directory dzierżawy lub nie ma uprawnień do wykonywania zapytań w Azure Active Directory, w którym znajduje się użytkownik.

**Poprawka 2:** Użytkownik z uprawnieniami Active Directory musi wykonać przypisanie roli.

>[!Note]
>New-RdsRoleAssignment nie może udzielić uprawnień użytkownikowi, który nie istnieje w Azure Active Directory (AD).

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview-2019.md).
- Aby rozwiązać problemy podczas tworzenia dzierżawy i puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie dzierżawy i puli hostów](troubleshoot-set-up-issues-2019.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration-2019.md).
- Aby rozwiązać problemy z połączeniami klienta pulpitu wirtualnego systemu Windows, zobacz [połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection-2019.md).
- Aby rozwiązać problemy z Pulpit zdalny klientami, zobacz [Rozwiązywanie problemów z klientem pulpit zdalny](../troubleshoot-client.md)
- Aby dowiedzieć się więcej na temat usługi, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup-2019.md).
- Aby przejść przez samouczek dotyczący rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji przy użyciu Menedżer zasobów](../../azure-resource-manager/management/view-activity-logs.md).
- Aby dowiedzieć się więcej o akcjach dotyczących określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](../../azure-resource-manager/templates/deployment-history.md).
