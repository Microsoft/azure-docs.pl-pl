---
title: Zezwalaj lub Blokuj zaproszenia do określonych organizacji — Azure AD
description: Pokazuje, w jaki sposób administrator może użyć Azure Portal lub programu PowerShell do ustawienia listy dostępu lub Odmów, aby zezwalać na dostęp użytkowników B2B do określonych domen lub je blokować.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb5a785cba4a560fc15963363d4e5f6b811af97a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99548835"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Zezwalanie na zaproszenia dla użytkowników B2B z określonych organizacji i blokowanie ich

Możesz użyć listy dozwolonych lub listy zablokowanych, aby zezwalać na zaproszenia użytkownikom B2B lub je blokować z określonych organizacji. Jeśli na przykład chcesz zablokować domeny osobistych adresów e-mail, możesz skonfigurować listę Odmów zawierającą domeny, takie jak Gmail.com i Outlook.com. Jeśli firma ma partnerstwo z innymi firmami, takimi jak Contoso.com, Fabrikam.com i Litware.com, i chcesz ograniczyć zaproszenia tylko do tych organizacji, możesz dodać Contoso.com, Fabrikam.com i Litware.com do listy dozwolonych.
  
## <a name="important-considerations"></a>Istotne zagadnienia

- Można utworzyć listę dozwolonych lub listę Odmów. Nie można skonfigurować obu typów list. Domyślnie, niezależnie od tego, gdzie domeny nie ma na liście dozwolonych, znajdują się na liście Odmów i na odwrót. 
- Można utworzyć tylko jedną zasadę dla każdej organizacji. Zasady można zaktualizować w celu uwzględnienia większej liczby domen lub można je usunąć, aby utworzyć nowe. 
- Liczba domen, które można dodać do listy dozwolonych lub zablokowanych, jest ograniczona tylko rozmiarem zasad. Ten limit dotyczy liczby znaków, dzięki czemu można mieć więcej krótszych domen lub mniej domen. Maksymalny rozmiar wszystkich zasad to 25 KB (25 000 znaków), które obejmują listę dozwolonych lub listę Odmów oraz inne parametry skonfigurowane dla innych funkcji.
- Ta lista działa niezależnie od usług OneDrive dla firm i SharePoint online na listach dozwolonych/zablokowanych. Jeśli chcesz ograniczyć udostępnianie poszczególnych plików w usłudze SharePoint Online, musisz skonfigurować listę dozwolonych lub zablokowanych dla usług OneDrive dla firm i SharePoint Online. Aby uzyskać więcej informacji, zobacz [udostępnianie domen z ograniczeniami w usłudze SharePoint Online i OneDrive dla firm](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Lista nie ma zastosowania do użytkowników zewnętrznych, którzy już skorzystali z zaproszenia. Lista zostanie wymuszona po skonfigurowaniu listy. Jeśli zaproszenie użytkownika jest w stanie oczekiwania i ustawisz zasady, które blokują swoją domenę, próba zrealizowania zaproszenia przez użytkownika zakończy się niepowodzeniem.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Ustawianie zasad listy dozwolonych lub zablokowanych w portalu

Domyślnie ustawienie Zezwalaj na **wysyłanie zaproszeń do dowolnej domeny (najbardziej włącznie)** jest włączone. W takim przypadku można zaprosić użytkowników B2B z dowolnej organizacji.

### <a name="add-a-deny-list"></a>Dodawanie listy Odmów

Jest to najbardziej typowy scenariusz, w którym organizacja chce współpracować z niemal każdą organizacją, ale chce uniemożliwić zaproszenie użytkowników z określonych domen jako użytkowników B2B.

Aby dodać listę Odmów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję  >    >  **Ustawienia użytkownika** Azure Active Directory użytkownicy.
3. W obszarze **użytkownicy zewnętrzni** wybierz pozycję **Zarządzaj ustawieniami współpracy zewnętrznej**.
4. W obszarze **ograniczenia dotyczące współpracy** wybierz pozycję **Odmów zaproszeń do określonych domen**.
5. W obszarze **domeny docelowe** wprowadź nazwę jednej z domen, które chcesz zablokować. W przypadku wielu domen wprowadź każdą domenę w nowym wierszu. Na przykład:

   ![Wyświetla opcję Odmów z dodanymi domenami](./media/allow-deny-list/DenyListSettings.png)
 
6. Gdy skończysz, kliknij przycisk **Zapisz**.

Po ustawieniu zasad, jeśli spróbujesz zaprosić użytkownika z zablokowanej domeny, zostanie wyświetlony komunikat z informacją, że domena użytkownika jest obecnie zablokowana przez zasady dotyczące zaproszenia.
 
### <a name="add-an-allow-list"></a>Dodawanie listy dozwolonych

Jest to bardziej restrykcyjna konfiguracja, w której można ustawić określone domeny na liście dozwolonych i ograniczyć zaproszenia do innych organizacji lub domen, które nie są wymienione. 

Jeśli chcesz użyć listy dozwolonych, upewnij się, że poświęcasz czas na całkowite oszacowanie potrzeb firmy. Jeśli te zasady są zbyt restrykcyjne, użytkownicy mogą zdecydować się na wysyłanie dokumentów za pośrednictwem poczty e-mail lub wyszukać inne zaakceptowane oficjalnie inne warunki współpracy.


Aby dodać listę dozwolonych:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję  >    >  **Ustawienia użytkownika** Azure Active Directory użytkownicy.
3. W obszarze **użytkownicy zewnętrzni** wybierz pozycję **Zarządzaj ustawieniami współpracy zewnętrznej**.
4. W obszarze **ograniczenia dotyczące współpracy** wybierz opcję **Zezwalaj na zaproszenia tylko do określonych domen (najbardziej restrykcyjne)**.
5. W obszarze **domeny docelowe** wprowadź nazwę jednej z domen, na które chcesz zezwolić. W przypadku wielu domen wprowadź każdą domenę w nowym wierszu. Na przykład:

   ![Wyświetla opcję Zezwalaj z dodanymi domenami](./media/allow-deny-list/AllowListSettings.png)
 
6. Gdy skończysz, kliknij przycisk **Zapisz**.

Po ustawieniu zasad, jeśli spróbujesz zaprosić użytkownika z domeny, która nie znajduje się na liście dozwolonych, zostanie wyświetlony komunikat z informacją, że domena użytkownika jest obecnie zablokowana przez zasady dotyczące zaproszenia.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Przełącz z listy Zezwalaj na odmowa i odwrotnie 

W przypadku przełączenia z jednej zasady na drugą spowoduje to odrzucenie istniejącej konfiguracji zasad. Przed przełączeniem należy wykonać kopię zapasową szczegółów konfiguracji. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Ustawianie zasad listy Zezwalaj lub Odmów przy użyciu programu PowerShell

### <a name="prerequisite"></a>Wymaganie wstępne

> [!Note]
> Moduł AzureADPreview nie jest w pełni obsługiwanym modułem, ponieważ jest w wersji zapoznawczej. 

Aby ustawić listę dozwolonych lub zablokowanych za pomocą programu PowerShell, należy zainstalować wersję zapoznawczą modułu Azure Active Directory dla środowiska Windows PowerShell. W tym celu należy zainstalować moduł AzureADPreview w wersji 2.0.0.98 lub nowszej.

Aby sprawdzić wersję modułu (i sprawdzić, czy jest on zainstalowany):
 
1. Otwórz program Windows PowerShell jako użytkownik z podwyższonym poziomem uprawnień (Uruchom jako administrator). 
2. Uruchom następujące polecenie, aby sprawdzić, czy na komputerze nie zainstalowano żadnych wersji modułu Azure Active Directory dla programu Windows PowerShell:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Jeśli moduł nie jest zainstalowany lub nie masz wymaganej wersji, wykonaj jedną z następujących czynności:

- Jeśli nie zostaną zwrócone żadne wyniki, uruchom następujące polecenie, aby zainstalować najnowszą wersję modułu AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Jeśli w wynikach jest wyświetlany tylko moduł AzureAD, uruchom następujące polecenia, aby zainstalować moduł AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Jeśli w wynikach zostanie wyświetlony tylko moduł AzureADPreview, ale wersja jest mniejsza niż 2.0.0.98, uruchom następujące polecenia, aby je zaktualizować: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Jeśli w wynikach są wyświetlane moduły AzureAD i AzureADPreview, ale wersja modułu AzureADPreview jest mniejsza niż 2.0.0.98, uruchom następujące polecenia, aby je zaktualizować: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Aby skonfigurować zasady, Użyj poleceń cmdlet AzureADPolicy

Aby utworzyć listę dozwolonych lub zablokowanych, użyj polecenia cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) . Poniższy przykład pokazuje, jak ustawić listę Odmów, która blokuje domenę "live.com".

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Poniżej pokazano ten sam przykład, ale z definicją zasad w tekście.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Aby ustawić zasady listy dozwolonych lub zablokowanych, należy użyć polecenia cmdlet [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) . Na przykład:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

Aby uzyskać zasady, należy użyć polecenia cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) . Na przykład:

```powershell
$currentpolicy = Get-AzureADPolicy -All $true | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

Aby usunąć zasady, należy użyć polecenia cmdlet [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) . Na przykład:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem usługi Azure AD B2B, zobacz [co to jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- Informacje o dostępie warunkowym i współpracy B2B można znaleźć w temacie [dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md).
