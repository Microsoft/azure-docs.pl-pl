---
title: Przypisywanie etykiet czułości do grup — Azure AD | Microsoft Docs
description: Sposób tworzenia reguł członkostwa w celu automatycznego wypełniania grup i odwołania do reguły.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 02/24/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32dad942cf79933b2c44d3bc2d23581ead1238d6
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84732756"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Przypisywanie etykiet czułości do grup pakietu Office 365 w Azure Active Directory (wersja zapoznawcza)

Azure Active Directory (Azure AD) obsługuje stosowanie etykiet liter opublikowanych przez [Centrum zgodności Microsoft 365](https://sip.protection.office.com/homepage) do grup usługi Office 365. Etykiety czułości dotyczą grup różnych usług, takich jak Outlook, Microsoft Teams i SharePoint. Ta funkcja jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji o obsłudze aplikacji pakietu Office 365, zobacz temat [Obsługa etykiet o czułości przez pakiet office 365](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels).

> [!IMPORTANT]
> Aby skonfigurować tę funkcję, musi mieć co najmniej jedną aktywną licencję Azure Active Directory — wersja Premium P1 w organizacji usługi Azure AD.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Włącz obsługę etykiet czułości w programie PowerShell

Aby zastosować opublikowane etykiety do grup, należy najpierw włączyć tę funkcję. Te kroki umożliwiają włączenie funkcji w usłudze Azure AD.

1. Otwórz okno programu Windows PowerShell na komputerze. Można go otworzyć bez podwyższonego poziomu uprawnień.
1. Uruchom następujące polecenia, aby przygotować się do uruchomienia poleceń cmdlet.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    Na stronie **Zaloguj się do konta** Wprowadź konto administratora i hasło, aby połączyć się z usługą, a następnie wybierz pozycję **Zaloguj**.
1. Pobierz bieżące ustawienia grupy dla organizacji usługi Azure AD.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Jeśli nie utworzono żadnych ustawień grupy dla tej organizacji usługi Azure AD, musisz najpierw utworzyć ustawienia. Wykonaj kroki opisane w temacie [polecenia cmdlet Azure Active Directory, aby skonfigurować ustawienia grupy](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) w celu utworzenia ustawień grupy dla tej organizacji usługi Azure AD.

1. Następnie Wyświetl bieżące ustawienia grupy.

    ```PowerShell
    $Setting.Values
    ```

1. Następnie Włącz funkcję:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Następnie Zapisz zmiany i Zastosuj ustawienia:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

To wszystko. Włączono funkcję i można zastosować opublikowane etykiety do grup.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Przypisywanie etykiety do nowej grupy w Azure Portal

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com).
1. Wybierz pozycję **grupy**, a następnie wybierz pozycję **Nowa grupa**.
1. Na stronie **Nowa grupa** wybierz pozycję **Office 365**, a następnie Wypełnij wymagane informacje dla nowej grupy i wybierz z listy etykietę czułości.

   ![Przypisywanie etykiety czułości na stronie nowych grup](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Zapisz zmiany i wybierz pozycję **Utwórz**.

Zostanie utworzona grupa, a następnie zostaną automatycznie wymuszone ustawienia lokacji i grupy skojarzone z wybraną etykietą.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Przypisywanie etykiety do istniejącej grupy w Azure Portal

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora grup lub jako właściciela grupy.
1. Wybierz pozycję **grupy**.
1. Na stronie **wszystkie grupy** wybierz grupę, którą chcesz oznaczyć etykietą.
1. Na stronie wybranej grupy wybierz pozycję **Właściwości** , a następnie wybierz etykietę czułości z listy.

   ![Przypisywanie etykiety czułości na stronie przeglądu dla grupy](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Wybierz przycisk **Zapisz**, aby zapisać zmiany.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Usuń etykietę z istniejącej grupy w Azure Portal

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora globalnego lub grupy Administratorzy lub jako właściciela grupy.
1. Wybierz pozycję **grupy**.
1. Na stronie **wszystkie grupy** wybierz grupę, z której chcesz usunąć etykietę.
1. Na stronie **Grupa** wybierz pozycję **Właściwości**.
1. Wybierz pozycję **Usuń**.
1. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

## <a name="using-classic-azure-ad-classifications"></a>Używanie klasycznych klasyfikacji usługi Azure AD

Po włączeniu tej funkcji klasyfikacje "klasyczny" dla grup będą widoczne tylko dla istniejących grup i lokacji i należy używać ich w przypadku nowych grup tylko w przypadku tworzenia grup w aplikacjach, które nie obsługują etykiet czułości. Administrator może później przekonwertować je na etykiety czułe w razie potrzeby. Klasyfikacje klasyczne są starymi klasyfikacjami skonfigurowanymi przez definiowanie wartości dla `ClassificationList` Ustawienia w programie Azure AD PowerShell. Gdy ta funkcja jest włączona, te klasyfikacje nie zostaną zastosowane do grup.

## <a name="troubleshooting-issues"></a>Rozwiązywanie problemów

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Etykiety czułości nie są dostępne do przypisania w grupie

Opcja etykiety czułości jest wyświetlana tylko dla grup, gdy spełnione są wszystkie następujące warunki:

1. Etykiety są publikowane w centrum zgodności Microsoft 365 dla tej organizacji usługi Azure AD.
1. Funkcja jest włączona, EnableMIPLabels jest ustawiona na true w programie PowerShell.
1. Grupa jest grupą pakietu Office 365.
1. Organizacja ma aktywną licencję na Azure Active Directory — wersja Premium P1.
1. Bieżący zalogowany użytkownik ma wystarczające uprawnienia do przypisywania etykiet. Użytkownik musi być administratorem globalnym, administratorem grupy lub właścicielem grupy.

Upewnij się, że wszystkie warunki są spełnione, aby przypisać etykiety do grupy.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>Etykieta, którą chcesz przypisać, nie znajduje się na liście

Jeśli etykieta, której szukasz, nie znajduje się na liście, może to być spowodowane jedną z następujących przyczyn:

- Etykieta nie może zostać opublikowana w centrum zgodności Microsoft 365. Może to również dotyczyć etykiet, które nie są już publikowane. Skontaktuj się z administratorem, aby uzyskać więcej informacji.
- Etykieta może być opublikowana, ale nie jest dostępna dla użytkownika, który jest zalogowany. Skontaktuj się z administratorem, aby uzyskać więcej informacji na temat uzyskiwania dostępu do etykiety.

### <a name="how-to-change-the-label-on-a-group"></a>Jak zmienić etykietę w grupie

Etykiety można wymieniać w dowolnym momencie przy użyciu tych samych kroków co przypisanie etykiety do istniejącej grupy w następujący sposób:

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta globalnego lub administratora grupy lub jako właściciela grupy.
1. Wybierz pozycję **grupy**.
1. Na stronie **wszystkie grupy** wybierz grupę, którą chcesz oznaczyć etykietą.
1. Na stronie wybranej grupy wybierz pozycję **Właściwości** i wybierz nową etykietę czułości z listy.
1. Wybierz pozycję **Zapisz**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>Zmiany ustawień grupy dla opublikowanych etykiet nie są aktualizowane w grupach

Najlepszym rozwiązaniem jest, że nie zalecamy zmiany ustawień grupy dla etykiety, gdy etykieta zostanie zastosowana do grup. Po wprowadzeniu zmian w ustawieniach grupy skojarzonych z opublikowanymi etykietami w [Microsoft 365 Centrum zgodności](https://sip.protection.office.com/homepage)te zmiany zasad nie są automatycznie stosowane w grupach, których to dotyczy.

Jeśli musisz wprowadzić zmianę, użyj [skryptu programu PowerShell usługi Azure AD](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) , aby ręcznie zastosować aktualizacje do grup, których dotyczy problem. Ta metoda zapewnia, że wszystkie istniejące grupy wymuszają nowe ustawienie.

## <a name="next-steps"></a>Następne kroki

- [Używanie etykiet czułości z usługami Microsoft Teams, Office 365 i witryn programu SharePoint](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Ręcznie Aktualizuj grupy po zmianie zasad etykiet przy użyciu skryptu programu PowerShell usługi Azure AD](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Edytowanie ustawień grupy](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Zarządzanie grupami przy użyciu poleceń programu PowerShell](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
