---
title: Ustawianie wygaśnięcia dla Microsoft 365 — Azure Active Directory | Microsoft Docs
description: Jak skonfigurować wygasanie dla Microsoft 365 w Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8af1a5e73592dc1c3392f0bc1fecfe6139a54710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529837"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>Konfigurowanie zasad wygasania dla Microsoft 365 grupy

W tym artykule opisano sposób zarządzania cyklem życia grup Microsoft 365 przez ustawienie dla nich zasad wygasania. Zasady wygasania można ustawić tylko dla Microsoft 365 w usłudze Azure Active Directory (Azure AD).

Po skonfigurowaniu wygasania grupy:

- Grupy z działaniami użytkownika są automatycznie odnawiane w przypadku zbliżania się wygaśnięcia.
- Właściciele grupy są powiadamiani o odnowieniu grupy, jeśli grupa nie jest odnawiana automatycznie.
- Każda grupa, która nie zostanie odnowiona, zostanie usunięta.
- Każda Microsoft 365 grupy, która zostanie usunięta, może zostać przywrócona w ciągu 30 dni przez właścicieli grupy lub administratora.

Obecnie dla wszystkich grup Microsoft 365 w organizacji usługi Azure AD można skonfigurować tylko jedne zasady wygasania.

> [!NOTE]
> Konfigurowanie i używanie zasad wygasania dla grup Microsoft 365 wymaga posiadania, ale niekoniecznie przypisania licencji usługi Azure AD — wersja Premium do członków wszystkich grup, do których zastosowano zasady wygasania.

Aby uzyskać informacje na temat pobierania i instalowania poleceń cmdlet programu PowerShell usługi Azure AD, zobacz [Azure Active Directory PowerShell for Graph 2.0.0.137 (Program PowerShell dla programu Graph 2.0.0.137).](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)

## <a name="activity-based-automatic-renewal"></a>Automatyczne odnawianie oparte na działaniach

Dzięki analizie usługi Azure AD grupy są teraz automatycznie odnawiane w zależności od tego, czy były ostatnio używane. Ta funkcja eliminuje konieczność ręcznego działania przez właścicieli grup, ponieważ jest oparta na aktywności użytkowników w grupach w różnych usługach Microsoft 365, takich jak Outlook, SharePoint lub Teams. Jeśli na przykład właściciel lub członek grupy przekaże dokument w programie SharePoint, odwiedzi kanał usługi Teams lub wyśle wiadomość e-mail do grupy w programie Outlook, grupa zostanie automatycznie odnowiona około 35 dni przed wygaśnięciem grupy, a właściciel nie otrzyma żadnych powiadomień o odnowieniu.

### <a name="activities-that-automatically-renew-group-expiration"></a>Działania, które automatycznie odnawiają wygaśnięcie grupy

Następujące akcje użytkownika powodują automatyczne odnawianie grup:

- SharePoint: wyświetlanie, edytowanie, pobieranie, przenoszenie, udostępnianie lub przekazywanie plików
- Outlook: dołącz do grupy, odczyt/zapis komunikat grupy z obszaru grupy, Podobnie jak komunikat (w programie Outlook Dostęp w sieci Web)
- Zespoły: odwiedź kanał teamsu

### <a name="auditing-and-reporting"></a>Inspekcja i raportowanie

Administratorzy mogą uzyskać listę automatycznie odnawianych grup z dzienników inspekcji aktywności w usłudze Azure AD.

![Automatyczne odnawianie grup na podstawie działania](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Role i uprawnienia

Poniżej przedstawiono role, które mogą konfigurować wygasanie grup Microsoft 365 w usłudze Azure AD i używać ich.

Rola | Uprawnienia
-------- | --------
administrator globalny, administrator grupy lub administrator użytkowników | Może tworzyć, odczytywać, aktualizować lub usuwać Microsoft 365 wygasania grup<br>Może odnowić dowolną Microsoft 365 grupę
Użytkownik | Może odnowić Microsoft 365, do których należy<br>Może przywrócić Microsoft 365 grupy zasobów, do których należy<br>Może odczytywać ustawienia zasad wygasania

Aby uzyskać więcej informacji na temat uprawnień do przywracania usuniętej grupy, zobacz [Restore a deleted Microsoft 365 group in Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Ustawianie czasu wygaśnięcia grup

1. Otwórz centrum [administracyjne usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora globalnego w organizacji usługi Azure AD.

2. Wybierz **pozycję Grupy,** a następnie wybierz **pozycję Wygaśnięcie,** aby otworzyć ustawienia wygasania.
  
   ![Ustawienia wygasania dla grup](./media/groups-lifecycle/expiration-settings.png)

3. Na stronie **Wygaśnięcie** możesz:

    - Ustaw okres istnienia grupy w dniach. Możesz wybrać jedną z wstępnie ustawionych wartości lub wartość niestandardową (powinna to być co najmniej 30 dni).
    - Określ adres e-mail, na który mają być wysyłane powiadomienia dotyczące odnawiania i wygasania, gdy grupa nie ma właściciela.
    - Wybierz, Microsoft 365 wygasają. Możesz ustawić wygaśnięcie dla:
      - **Wszystkie** Microsoft 365 grup
      - Lista wybranych **grup** Microsoft 365 grupy
      - **Brak w** celu ograniczenia wygasania dla wszystkich grup
    - Gdy wszystko będzie gotowe, zapisz ustawienia, wybierając pozycję **Zapisz.**

> [!NOTE]
> - Po pierwszym skonfigurowaniu wygasania wszystkie grupy, które są starsze niż interwał wygaśnięcia, są ustawiane na 35 dni do wygaśnięcia, chyba że grupa zostanie automatycznie odnowiona lub właściciel ją odnowi.
> - Po usunięciu i przywróceniu grupy dynamicznej jest ona postrzegana jako nowa grupa i wypełniana ponownie zgodnie z regułą. Ten proces może potrwać do 24 godzin.
> - Powiadomienia o wygaśnięciu dla grup używanych w zespołów są wyświetlane w kanale informacyjnym Właściciele zespołów.
> - Po włączeniu wygasania dla wybranych grup można dodać do 500 grup do listy. Jeśli musisz dodać więcej niż 500 grup, możesz włączyć wygasanie dla wszystkich grup. W tym scenariuszu ograniczenie liczby 500 grup nie ma zastosowania.

## <a name="email-notifications"></a>Powiadomienia e-mail

Jeśli grupy nie są odnawiane automatycznie, powiadomienia e-mail takie jak ta są wysyłane do właścicieli grupy usługi Microsoft 365 30 dni, 15 dni i 1 dzień przed wygaśnięciem grupy. Język wiadomości e-mail jest określany przez preferowany język właściciela grupy lub ustawienie języka usługi Azure AD. Jeśli właściciel grupy zdefiniuje preferowany język lub wielu właścicieli ma ten sam preferowany język, ten język jest używany. We wszystkich innych przypadkach używane jest ustawienie języka usługi Azure AD.

![Powiadomienia e-mail dotyczące wygaśnięcia](./media/groups-lifecycle/expiration-notification.png)

W wiadomości **e-mail** z powiadomieniem o odnowieniu grupy właściciele grupy mogą bezpośrednio uzyskać dostęp do strony szczegółów grupy w [Panel dostępu](https://account.activedirectory.windowsazure.com/r#/applications). W tym miejscu użytkownicy mogą uzyskać więcej informacji o grupie, takich jak jej opis, czas jej ostatniej odnowienia, termin wygaśnięcia, a także możliwość odnowienia grupy. Strona szczegółów grupy zawiera teraz również linki do zasobów grupy Microsoft 365, dzięki czemu właściciel grupy może wygodnie wyświetlać zawartość i działanie w swojej grupie.

Gdy grupa wygaśnie, grupa zostanie usunięta jeden dzień po dacie wygaśnięcia. Powiadomienie e-mail takie jak to jest wysyłane do właścicieli grupy Microsoft 365 z powiadomieniem o wygaśnięciu i późniejszym usunięciu Microsoft 365 grupy.

![Powiadomienia e-mail dotyczące usuwania grup](./media/groups-lifecycle/deletion-notification.png)

Grupę można przywrócić w ciągu 30  dni od jej usunięcia, wybierając pozycję Przywróć grupę lub używając poleceń cmdlet programu PowerShell, zgodnie z opisem w części Przywracanie usuniętej grupy Microsoft 365 w [programie Azure Active Directory](groups-restore-deleted.md). Należy pamiętać, że 30-dniowy okres przywracania grup nie można dostosowywać.

Jeśli przywracana grupa zawiera dokumenty, witryny programu SharePoint lub inne trwałe obiekty, pełne przywrócenie grupy i jej zawartości może potrwać do 24 godzin.

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>Jak pobrać datę wygaśnięcia Microsoft 365 grupy

Oprócz możliwości Panel dostępu, w których użytkownicy mogą wyświetlać szczegóły grupy, w tym datę wygaśnięcia i datę ostatniego odnowienia, datę wygaśnięcia grupy usługi Microsoft 365 można pobrać z usługi Microsoft Graph API REST Beta. właściwość expirationDateTime jako właściwość grupy została włączona w programie Microsoft Graph Beta. Można go pobrać za pomocą żądania GET. Aby uzyskać więcej informacji, zapoznaj się z [tym przykładem](/graph/api/group-get?view=graph-rest-beta#example&preserve-view=true).

> [!NOTE]
> Aby można było zarządzać członkostwem w grupach na Panel dostępu, w ustawieniach ogólnych grup Panel dostępu należy ustawić wartość Azure Active Directory "Nie".

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Jak Microsoft 365 wygasania grup w przypadku wstrzymania ze skrzynki pocztowej ze prawem

Gdy grupa wygaśnie i zostanie usunięta, 30 dni po usunięciu danych grupy z aplikacji, takich jak Planner, Witryny lub Teams, zostanie trwale usunięta, ale skrzynka pocztowa grupy, która jest wstrzymana ze strony prawnej, zostanie zachowana i nie zostanie trwale usunięta. Administrator może użyć polecenia cmdlet programu Exchange, aby przywrócić skrzynkę pocztową w celu pobrania danych.

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>Jak Microsoft 365 wygasania grup działa z zasadami przechowywania

Zasady przechowywania są konfigurowane za pomocą Centrum zabezpieczeń i zgodności. Jeśli zostały ustawione zasady przechowywania dla grup programu Microsoft 365, gdy grupa wygaśnie i zostanie usunięta, konwersacje grup w skrzynce pocztowej grupy i plikach w lokacji grupy zostaną zachowane w kontenerze przechowywania przez określoną liczbę dni zdefiniowaną w zasadach przechowywania. Użytkownicy nie będą widzieć grupy ani jej zawartości po wygaśnięciu, ale mogą odzyskać dane witryny i skrzynki pocztowej za pośrednictwem odnajdywania elektronicznego.

## <a name="powershell-examples"></a>Przykłady programu PowerShell

Poniżej przedstawiono przykłady konfigurowania ustawień wygasania dla grup Microsoft 365 w organizacji usługi Azure AD za pomocą poleceń cmdlet programu PowerShell:

1. Zainstaluj moduł PowerShell w wersji 2.0 i zaloguj się w wierszu polecenia programu PowerShell:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Konfigurowanie ustawień wygasania Użyj polecenia cmdlet New-AzureADMSGroupLifecyclePolicy, aby ustawić okres istnienia dla wszystkich grup Microsoft 365 w organizacji usługi Azure AD na 365 dni. Powiadomienia o odnowieniu Microsoft 365 bez właścicieli będą wysyłane do " emailaddress@contoso.com "
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Pobierz istniejące zasady Get-AzureADMSGroupLifecyclePolicy: to polecenie cmdlet pobiera Microsoft 365 ustawień wygasania grup, które zostały skonfigurowane. W tym przykładzie można zobaczyć:

   - Identyfikator zasad
   - Okres istnienia dla wszystkich grup Microsoft 365 w organizacji usługi Azure AD jest ustawiony na 365 dni
   - Powiadomienia o odnawianiu Microsoft 365 grupy bez właścicieli będą wysyłane do emailaddress@contoso.com "."
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Zaktualizuj istniejące zasady Set-AzureADMSGroupLifecyclePolicy: to polecenie cmdlet służy do aktualizowania istniejących zasad. W poniższym przykładzie okres istnienia grupy w istniejących zasadach został zmieniony z 365 dni na 180 dni.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Dodaj określone grupy do zasad Add-AzureADMSLifecyclePolicyGroup: to polecenie cmdlet dodaje grupę do zasad cyklu życia. Przykład:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Usuń istniejące zasady Remove-AzureADMSGroupLifecyclePolicy: to polecenie cmdlet usuwa ustawienia wygasania Microsoft 365 grupy, ale wymaga identyfikatora zasad. To polecenie cmdlet wyłącza wygasanie Microsoft 365 grupy.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Poniższe polecenia cmdlet mogą służyć do bardziej szczegółowego konfigurowania zasad. Aby uzyskać więcej informacji, zobacz [dokumentację programu PowerShell.](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#groups)

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje na temat grup usługi Azure AD.

- [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Zarządzanie członkami grupy](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)
