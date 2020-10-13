---
title: Ustaw wygasanie dla grup Microsoft 365 — Azure Active Directory | Microsoft Docs
description: Jak skonfigurować wygasanie dla grup Microsoft 365 w Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb51f402cd7032358e6a9d2f9fad9aa64316b809
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90054555"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>Konfigurowanie zasad wygasania dla grup Microsoft 365

W tym artykule przedstawiono sposób zarządzania cyklem życia grup Microsoft 365 przez ustawienie dla nich zasad wygasania. Zasady wygasania można ustawić tylko dla grup Microsoft 365 w usłudze Azure Active Directory (Azure AD).

Po ustawieniu grupy do wygaśnięcia:

- Grupy z działaniami użytkownika są automatycznie odnawiane jako zbliżające się daty zakończenia.
- Właściciele grupy są powiadamiani o odnowieniu grupy, jeśli grupa nie została odnowiać.
- Wszystkie grupy, które nie są odnawiane, zostaną usunięte.
- Wszystkie usunięte grupy Microsoft 365 można przywrócić w ciągu 30 dni przez właścicieli grup lub administratora.

Obecnie można skonfigurować tylko jedną zasadę wygasania dla wszystkich grup Microsoft 365 w organizacji usługi Azure AD.

> [!NOTE]
> Konfigurowanie zasad wygasania dla grup Microsoft 365 i korzystanie z nich wymaga, aby nie przypisywać Azure AD — wersja Premium licencji dla elementów członkowskich wszystkich grup, do których zastosowano zasady wygasania.

Aby uzyskać informacje na temat pobierania i instalowania poleceń cmdlet programu PowerShell usługi Azure AD, zobacz [Azure Active Directory PowerShell dla programu Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Automatyczne odnawianie oparte na działaniach

W przypadku usługi Azure AD Intelligence grupy są teraz automatycznie odnawiane na podstawie tego, czy były ostatnio używane. Ta funkcja eliminuje konieczność wykonywania ręcznej akcji przez właścicieli grup, ponieważ jest ona oparta na aktywności użytkownika w grupach w ramach usług Microsoft 365, takich jak Outlook, SharePoint lub zespoły. Na przykład, jeśli właściciel lub członek grupy wykonuje coś takiego jak przekazywanie dokumentu w programie SharePoint, odwiedź kanał zespołów lub Wyślij wiadomość e-mail do grupy w programie Outlook, Grupa zostanie automatycznie odnowiona, a właściciel nie otrzyma żadnych powiadomień o odnowieniu.

### <a name="activities-that-automatically-renew-group-expiration"></a>Działania, które automatycznie odnawiają wygaśnięcie grupy

Następujące akcje użytkownika powodują automatyczne odnowienie grupy:

- SharePoint: wyświetlanie, edytowanie, pobieranie, przenoszenie, udostępnianie lub przekazywanie plików
- Outlook: Dołącz grupę, Odczytaj/Zapisz wiadomość grupy z obszaru grupy, tak jak w przypadku komunikatu (w programie Outlook Dostęp w sieci Web)
- Zespoły: odwiedź kanał zespołów

### <a name="auditing-and-reporting"></a>Inspekcja i raportowanie

Administratorzy mogą uzyskać listę automatycznie odnowionych grup z dzienników inspekcji aktywności w usłudze Azure AD.

![Automatyczne odnawianie grup na podstawie działania](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Role i uprawnienia

Poniżej znajdują się role, które umożliwiają konfigurowanie i używanie wygasania dla grup Microsoft 365 w usłudze Azure AD.

Rola | Uprawnienia
-------- | --------
Administrator globalny, administrator grupy lub administrator użytkownika | Może tworzyć, odczytywać, aktualizować i usuwać ustawienia zasad wygasania grup Microsoft 365<br>Może odnowić grupę Microsoft 365
Użytkownik | Może odnowić grupę Microsoft 365, do której się odnoszą<br>Może przywrócić grupę Microsoft 365, do której się odnoszą<br>Może odczytać ustawienia zasad wygasania

Aby uzyskać więcej informacji na temat uprawnień do przywracania usuniętej grupy, zobacz [przywracanie usuniętej grupy Microsoft 365 w Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Ustawianie czasu wygaśnięcia grup

1. Otwórz [Centrum administracyjne usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym w organizacji usługi Azure AD.

2. Wybierz pozycję **grupy**, a następnie wybierz pozycję **wygaśnięcie** , aby otworzyć ustawienia wygasania.
  
   ![Ustawienia wygasania dla grup](./media/groups-lifecycle/expiration-settings.png)

3. Na stronie **wygaśnięcie** możesz:

    - Ustaw okres istnienia grupy w dniach. Można wybrać jedną z wartości ustawień wstępnych lub wartość niestandardową (powinna być równa 30 dni lub więcej).
    - Określ adres e-mail, na który mają być wysyłane powiadomienia o odnowieniu i wygaśnięciu, gdy grupa nie ma właściciela.
    - Wybierz, które grupy Microsoft 365 wygasną. Możesz ustawić wygasanie dla:
      - **Wszystkie** Grupy Microsoft 365
      - Lista **wybranych** grup Microsoft 365
      - **Brak** , aby ograniczyć wygasanie dla wszystkich grup
    - Zapisz ustawienia po zakończeniu, wybierając pozycję **Zapisz**.

> [!NOTE]
> - Po pierwszym skonfigurowaniu wygaśnięcia wszystkie grupy, które są starsze niż interwał wygaśnięcia, są ustawiane na 35 dni do momentu wygaśnięcia, chyba że grupa zostanie automatycznie odnowiona lub właściciel ją odnowi.
> - Gdy grupa dynamiczna zostanie usunięta i przywrócona, jest ona traktowana jako nowa grupa i uzupełniana zgodnie z tą regułą. Ten proces może potrwać do 24 godzin.
> - Powiadomienia o wygaśnięciu dotyczące grup używanych w zespołach są wyświetlane w kanale właściciele zespołów.

## <a name="email-notifications"></a>Powiadomienia e-mail

Jeśli grupy nie są automatycznie odnawiane, powiadomienia e-mail, takie jak te, są wysyłane do Microsoft 365 właścicieli grupy 30 dni, 15 dni i 1 dzień przed wygaśnięciem grupy. Język poczty e-mail jest określany na podstawie preferowanego języka właściciela grup lub ustawienia języka usługi Azure AD. Jeśli właściciel grupy zdefiniował preferowany język lub wielu właścicieli ma ten sam preferowany język, używany jest ten język. We wszystkich innych przypadkach jest używane ustawienie języka usługi Azure AD.

![Powiadomienia e-mail o wygaśnięciu](./media/groups-lifecycle/expiration-notification.png)

W wiadomości e-mail z powiadomieniem o **odnowieniu grupy** właściciele grupy mogą bezpośrednio uzyskiwać dostęp do strony szczegółów grupy w [panelu dostępu](https://account.activedirectory.windowsazure.com/r#/applications). W tym miejscu użytkownicy mogą uzyskać więcej informacji na temat grupy, takiej jak jej opis, podczas ostatniego odnowienia, gdy zostanie ona wygaśnie, a także możliwość odnowienia grupy. Strona szczegóły grupy zawiera teraz również linki do zasobów grupy Microsoft 365, dzięki czemu Właściciel grupy może wygodnie wyświetlić zawartość i działanie w swojej grupie.

Po wygaśnięciu grupy Grupa jest usuwana jeden dzień po dacie wygaśnięcia. Powiadomienie e-mail takie jak ten jest wysyłane do właścicieli grup Microsoft 365 informujących o wygaśnięciu i następnym usunięciu grupy Microsoft 365.

![Powiadomienia e-mail o usunięciu grupy](./media/groups-lifecycle/deletion-notification.png)

Grupę można przywrócić w ciągu 30 dni od jej usunięcia, wybierając opcję **Przywróć grupę** lub używając poleceń cmdlet programu PowerShell, zgodnie z opisem w temacie [przywracanie usuniętej grupy Microsoft 365 w Azure Active Directory](groups-restore-deleted.md). Należy pamiętać, że 30-dniowy okres przywracania grupy nie jest dostosowywany.

Jeśli przywracana grupa zawiera dokumenty, witryny programu SharePoint lub inne trwałe obiekty, może upłynąć nawet 24 godziny, aby w pełni przywrócić grupę i jej zawartość.

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>Jak pobrać datę wygaśnięcia grupy Microsoft 365

Oprócz panelu dostępu, w którym użytkownicy mogą wyświetlać szczegóły grupy, w tym datę wygaśnięcia i datę ostatniego odnowienia, Data wygaśnięcia grupy Microsoft 365 można pobrać z interfejsu API REST Microsoft Graph beta. expirationDateTime jako właściwość grupy została włączona w Microsoft Graph wersji beta. Można go pobrać za pomocą żądania GET. Aby uzyskać więcej informacji, zapoznaj się z [tym przykładem](/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Aby zarządzać członkostwem w grupach w panelu dostępu, "Ogranicz dostęp do grup w panelu dostępu" musi mieć wartość "nie" w obszarze Ustawienia ogólne grup Azure Active Directory.

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Jak wygasanie grupy Microsoft 365 działa z skrzynką pocztową w ramach blokady z przyczyn prawnych

Gdy grupa wygaśnie i zostanie usunięta, 30 dni po usunięciu danych grupy z aplikacji, takich jak planista, witryny lub zespoły, zostanie trwale usunięta, ale Skrzynka pocztowa grupy, która znajduje się w blokadzie z przyczyn prawnych, jest zachowywana i nie jest trwale usuwana. Administrator może użyć poleceń cmdlet programu Exchange do przywrócenia skrzynki pocztowej w celu pobrania danych.

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>Jak działa wygaśnięcie grupy Microsoft 365 z zasadami przechowywania

Zasady przechowywania są konfigurowane przy użyciu Centrum zabezpieczeń i zgodności. W przypadku skonfigurowania zasad przechowywania dla grup Microsoft 365, gdy grupa wygaśnie i zostanie usunięta, konwersacje grupy w skrzynce pocztowej grupy i plikach w lokacji grupy są zachowywane w kontenerze przechowywania dla określonej liczby dni zdefiniowane w zasadach przechowywania. Użytkownicy nie będą widzieć grupy lub jej zawartości po wygaśnięciu, ale mogą odzyskać dane dotyczące lokacji i skrzynek pocztowych za pośrednictwem funkcji odnajdywania elektronicznego.

## <a name="powershell-examples"></a>Przykłady dla programu PowerShell

Poniżej przedstawiono przykłady użycia poleceń cmdlet programu PowerShell do konfigurowania ustawień wygasania dla grup Microsoft 365 w organizacji usługi Azure AD:

1. Zainstaluj moduł programu PowerShell 2.0 i zaloguj się w wierszu polecenia programu PowerShell:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Skonfiguruj ustawienia wygasania za pomocą polecenia cmdlet New-AzureADMSGroupLifecyclePolicy, aby ustawić okres istnienia dla wszystkich grup Microsoft 365 w organizacji usługi Azure AD na 365 dni. Powiadomienia o odnowieniu dla grup Microsoft 365 bez właścicieli zostaną wysłane do " emailaddress@contoso.com "
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Pobieranie istniejących zasad Get-AzureADMSGroupLifecyclePolicy: to polecenie cmdlet umożliwia pobranie bieżących skonfigurowanych ustawień wygasania grupy Microsoft 365. W tym przykładzie można zobaczyć:

   - Identyfikator zasad
   - Okres istnienia dla wszystkich grup Microsoft 365 w organizacji usługi Azure AD jest ustawiony na 365 dni
   - Powiadomienia o odnowieniu dla grup Microsoft 365 bez właścicieli zostaną wysłane do " emailaddress@contoso.com ."
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Aktualizowanie istniejącego zestawu zasad — AzureADMSGroupLifecyclePolicy: to polecenie cmdlet służy do aktualizowania istniejących zasad. W poniższym przykładzie okres istnienia grupy w istniejących zasadach został zmieniony z 365 dni na 180 dni.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Dodawanie określonych grup do zasad Add-AzureADMSLifecyclePolicyGroup: to polecenie cmdlet dodaje grupę do zasad cyklu życia. Przykład:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Usuń istniejące zasady Remove-AzureADMSGroupLifecyclePolicy: to polecenie cmdlet usuwa ustawienia wygaśnięcia grupy Microsoft 365, ale wymaga identyfikatora zasad. To polecenie cmdlet wyłącza wygasanie dla grup Microsoft 365.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Aby skonfigurować zasady bardziej szczegółowo, można użyć następujących poleceń cmdlet. Aby uzyskać więcej informacji, zobacz [dokumentację programu PowerShell](/powershell/module/azuread/?branch=master&view=azureadps-2.0-preview#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje dotyczące grup usługi Azure AD.

- [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Zarządzanie członkami grupy](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)