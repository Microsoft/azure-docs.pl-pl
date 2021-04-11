---
title: Konfigurowanie powiadomień e-mail dla alertów Azure Security Center
description: Dowiedz się, jak dostosować typy wiadomości e-mail wysyłanych przez Azure Security Center na potrzeby alertów zabezpieczeń.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 04/07/2021
ms.author: memildin
ms.openlocfilehash: 96a389a581a9ecaddfc418824b3ebe9c780e6bd1
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011591"
---
# <a name="configure-email-notifications-for-security-alerts"></a>Konfigurowanie powiadomień e-mail dla alertów zabezpieczeń 

Alerty zabezpieczeń muszą dotrzeć do odpowiednich osób w organizacji. Domyślnie właściciele subskrypcji Security Center wiadomości e-mail przy każdym wyzwoleniu alertu o wysokiej ważności dla swojej subskrypcji. Na tej stronie wyjaśniono, jak dostosować te powiadomienia.

Aby zdefiniować własne preferencje dla wiadomości e-mail z powiadomieniami, na stronie ustawień **powiadomień e-mail** Azure Security Center można wybrać następujące opcje:

- ***kto* powinien otrzymywać powiadomienia** — wiadomości e-mail mogą być wysyłane do wybranych osób lub do każdej osoby, która ma określoną rolę platformy Azure dla subskrypcji. 
- ***co* należy powiadomić o** zmianach poziomów ważności, dla których Security Center powinny wysyłać powiadomienia.

Aby uniknąć zmęczenia alertów, Security Center ogranicza ilość wychodzących wiadomości e-mail. Dla każdej subskrypcji Security Center wysyła:

- maksymalnie jedna wiadomość e-mail na **6 godzin** (4 wiadomości e-mail dziennie) dla alertów **o wysokiej ważności**
- maksymalnie jedna wiadomość e-mail na **12 godzin** (2 wiadomości e-mail dziennie) dla alertów o **średniej ważności**
- maksymalnie jedna wiadomość e-mail na **24 godziny** dla alertów **o niskiej ważności**

:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="Konfigurowanie szczegółów kontaktu, który będzie otrzymywać wiadomości e-mail o alertach zabezpieczeń." :::
 
## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólna dostępność (GA)|
|Wpisaną|Bezpłatna|
|Wymagane role i uprawnienia:|**Administrator zabezpieczeń**<br>**Właściciel subskrypcji** |
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||


## <a name="customize-the-security-alerts-email-notifications-via-the-portal"></a>Dostosuj powiadomienia e-mail dotyczące alertów zabezpieczeń za pośrednictwem portalu<a name="email"></a>
Powiadomienia e-mail mogą być wysyłane do osób indywidualnych lub wszystkich użytkowników z określonymi rolami platformy Azure.

1. W obszarze **ustawienia & cennika** Security Center wybierz odpowiednią subskrypcję, a następnie wybierz pozycję **powiadomienia e-mail**.

1. Zdefiniuj adresatów powiadomień przy użyciu jednej lub obu tych opcji:

    - Z listy rozwijanej wybierz pozycję spośród dostępnych ról.
    - Wprowadź określone adresy e-mail rozdzielone przecinkami. Nie ma żadnego limitu liczby adresów e-mail, które można wprowadzić.

1. Aby zastosować informacje o kontakcie zabezpieczeń do subskrypcji, wybierz pozycję **Zapisz**.

## <a name="customize-the-alerts-email-notifications-through-the-api"></a>Dostosuj powiadomienia e-mail dotyczące alertów za pośrednictwem interfejsu API
Powiadomienia e-mail można także zarządzać za pomocą podanego interfejsu API REST. Aby uzyskać pełne szczegóły, zobacz [dokumentację interfejsu API SecurityContacts](/rest/api/securitycenter/securitycontacts).

Jest to Przykładowa treść żądania dla żądania PUT podczas tworzenia konfiguracji kontaktu z zabezpieczeniami:

Identyfikator URI: https://management.azure.com/subscriptions/ <SubscriptionId> /providers/Microsoft.Security/securityContacts/default? API-Version = 2020-01 -01 — wersja zapoznawcza

```json
{
    "properties": {
        "emails": admin@contoso.com;admin2@contoso.com,
        "notificationsByRole": {
            "state": "On",
            "roles": ["AccountAdmin", "Owner"]
        },
        "alertNotifications": {
            "state": "On",
            "minimalSeverity": "Medium"
        },
        "phone": ""
    }
}
```


## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat alertów zabezpieczeń, zobacz następujące strony:

- [Alerty zabezpieczeń — Przewodnik referencyjny](alerts-reference.md)— informacje na temat alertów zabezpieczeń, które mogą zostać wyświetlone w module ochrony przed zagrożeniami w Azure Security Center
- [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md)— Dowiedz się, jak zarządzać alertami zabezpieczeń i odpowiadać na nie
- [Automatyzacja przepływu pracy](workflow-automation.md)— Automatyzowanie odpowiedzi na alerty za pomocą niestandardowej logiki powiadomień