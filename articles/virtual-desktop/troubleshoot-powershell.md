---
title: Windows Virtual Desktop PowerShell — Azure
description: Jak rozwiązywać problemy z programem PowerShell podczas konfigurowania środowiska pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 03b6da1d35247749d8ec2c6459c8ddee69bfccb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88002281"
---
# <a name="windows-virtual-desktop-powershell"></a>Program PowerShell dla usługi Windows Virtual Desktop

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md).

W tym artykule opisano błędy i problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows. Aby uzyskać więcej informacji na Usługi pulpitu zdalnego PowerShell, zobacz [Windows Virtual Desktop PowerShell](/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Przekazywanie opinii

Odwiedź [społeczność Tech. pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , aby omówić usługę pulpitu wirtualnego systemu Windows z zespołem produktu i aktywnymi członkami społeczności.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Polecenia programu PowerShell używane podczas instalacji pulpitu wirtualnego systemu Windows

Ta sekcja zawiera listę poleceń programu PowerShell, które są zwykle używane podczas konfigurowania pulpitu wirtualnego systemu Windows i zapewnia sposoby rozwiązywania problemów, które mogą wystąpić podczas ich używania.

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>Błąd: New-AzRoleAssignment: podane informacje nie są mapowane na identyfikator obiektu usługi AD.

```powershell
New-AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

**Przyczyna:** Nie można znaleźć użytkownika określonego przez parametr *-SignInName* w Azure Active Directory powiązanym ze środowiskiem pulpitu wirtualnego systemu Windows.

**Poprawka:** Pamiętaj o następujących kwestiach.

- Użytkownik powinien zostać zsynchronizowany z Azure Active Directory.
- Użytkownik nie powinien być powiązany z handlem typu "Business-to-Consumer" (B2C) ani "Business-to-Business" (B2B).
- Środowisko pulpitu wirtualnego systemu Windows powinno być powiązane z poprawnym Azure Active Directory.

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>Błąd: New-AzRoleAssignment: "klient z identyfikatorem obiektu nie ma autoryzacji do wykonania akcji względem zakresu (kod: AuthorizationFailed)"

**Przyczyna 1:** Używane konto nie ma uprawnień właściciela do subskrypcji.

**Poprawka 1:** Użytkownik z uprawnieniami właściciela musi wykonać przypisanie roli. Alternatywnie użytkownik musi być przypisany do roli administratora dostępu użytkownika, aby przypisać użytkownika do grupy aplikacji.

**Przyczyna 2:** Używane konto ma uprawnienia właściciela, ale nie jest częścią Azure Active Directory środowiska lub nie ma uprawnień do wykonywania zapytań dotyczących Azure Active Directory, w którym znajduje się użytkownik.

**Poprawka 2:** Użytkownik z uprawnieniami Active Directory musi wykonać przypisanie roli.

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>Błąd: New-AzWvdHostPool — lokalizacja jest niedostępna dla typu zasobu

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'.
```

Przyczyna: pulpit wirtualny systemu Windows obsługuje Wybieranie lokalizacji pul hostów, grup aplikacji i obszarów roboczych w celu przechowywania metadanych usługi w określonych lokalizacjach. Opcje są ograniczone do miejsca, w którym ta funkcja jest dostępna. Ten błąd oznacza, że funkcja nie jest dostępna w wybranej lokalizacji.

Poprawka: w komunikacie o błędzie zostanie opublikowana Lista obsługiwanych regionów. Zamiast tego użyj jednego z obsługiwanych regionów.

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>Błąd: New-AzWvdApplicationGroup musi znajdować się w tej samej lokalizacji co Pula hostów

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**Przyczyna:** Niezgodność lokalizacji. Wszystkie pule hostów, grupy aplikacji i obszary robocze mają lokalizację do przechowywania metadanych usługi. Wszystkie obiekty, które tworzysz, są skojarzone ze sobą, muszą znajdować się w tej samej lokalizacji. Na przykład, jeśli Pula hostów znajduje się w programie `eastus` , należy również utworzyć grupy aplikacji w programie `eastus` . Jeśli utworzysz obszar roboczy, aby zarejestrować te grupy aplikacji do, ten obszar roboczy musi być również w tym obszarze `eastus` .

**Poprawka:** Pobierz lokalizację, w której utworzono pulę hostów, a następnie Przypisz grupę aplikacji, którą tworzysz, do tej samej lokalizacji.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązać problemy podczas konfigurowania środowiska pulpitu wirtualnego systemu Windows i pul hostów, zobacz [Tworzenie puli środowiska i hosta](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy z połączeniami klienta pulpitu wirtualnego systemu Windows, zobacz [połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md).
- Aby rozwiązać problemy z Pulpit zdalny klientami, zobacz [Rozwiązywanie problemów z klientem pulpit zdalny](troubleshoot-client.md)
- Aby dowiedzieć się więcej na temat usługi, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji przy użyciu Menedżer zasobów](../azure-resource-manager/management/view-activity-logs.md).
- Aby dowiedzieć się więcej o akcjach dotyczących określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](../azure-resource-manager/templates/deployment-history.md).
