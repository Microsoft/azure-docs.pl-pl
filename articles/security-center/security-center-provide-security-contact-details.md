---
title: Konfigurowanie powiadomień e-mail dla alertów Azure Security Center
description: Dowiedz się, jak dostosować typy wiadomości e-mail wysyłanych przez Azure Security Center na potrzeby alertów zabezpieczeń.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: memildin
ms.openlocfilehash: 6b54f02b39e233dcf35f0d18682ca102883d76c3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791872"
---
# <a name="configure-email-notifications-for-security-alerts"></a>Konfigurowanie powiadomień e-mail dla alertów zabezpieczeń 

Alerty zabezpieczeń muszą dotrzeć do odpowiednich osób w organizacji. Domyślnie właściciele subskrypcji Security Center wiadomości e-mail przy każdym wyzwoleniu alertu o wysokiej ważności dla swojej subskrypcji. Na tej stronie wyjaśniono, jak dostosować te powiadomienia.

Aby zdefiniować własne preferencje dla wiadomości e-mail z powiadomieniami, na stronie ustawień **powiadomień e-mail** Azure Security Center można wybrać następujące opcje:

- **_kto_ powinien otrzymywać powiadomienia** — wiadomości e-mail mogą być wysyłane do wybranych osób lub do każdej osoby, która ma określoną rolę platformy Azure dla subskrypcji. 
- **_co_ należy powiadomić o** zmianach poziomów ważności, dla których Security Center powinny wysyłać powiadomienia.

Aby uniknąć zmęczenia alertów, Security Center ogranicza ilość wychodzących wiadomości e-mail. Dla każdej subskrypcji Security Center wysyła:

- maksymalnie **cztery** wiadomości e-mail dziennie dla alertów **o wysokiej ważności**
- maksymalnie **dwa** wiadomości e-mail dziennie dla alertów o **średniej ważności**
- maksymalnie **jedna** wiadomość e-mail na dzień w przypadku alertów **o niskim znaczeniu**

:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="Konfigurowanie szczegółów kontaktu, który będzie otrzymywać wiadomości e-mail o alertach zabezpieczeń." :::
 
## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna (GA)|
|Wpisaną|Bezpłatna|
|Wymagane role i uprawnienia:|**Administrator zabezpieczeń**<br>**Właściciel subskrypcji** |
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||


## <a name="customize-the-security-alerts-email-notifications"></a>Dostosuj powiadomienia e-mail dotyczące alertów zabezpieczeń<a name="email"></a>

Powiadomienia e-mail mogą być wysyłane do osób indywidualnych lub wszystkich użytkowników z określonymi rolami platformy Azure.

1. W obszarze **ustawienia & cennika** Security Center wybierz odpowiednią subskrypcję, a następnie wybierz pozycję **powiadomienia e-mail** .

1. Zdefiniuj adresatów powiadomień przy użyciu jednej lub obu tych opcji:

    - Z listy rozwijanej wybierz pozycję spośród dostępnych ról.
    - Wprowadź określone adresy e-mail oddzielone przecinkami. Nie ma żadnego limitu liczby adresów e-mail, które można wprowadzić.

1. Aby zastosować informacje o kontakcie zabezpieczeń do subskrypcji, wybierz pozycję **Zapisz** .


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat alertów zabezpieczeń, zobacz następujące strony:

- [Alerty zabezpieczeń — Przewodnik referencyjny](alerts-reference.md)— informacje na temat alertów zabezpieczeń, które mogą zostać wyświetlone w module ochrony przed zagrożeniami w Azure Security Center
- [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md)— Dowiedz się, jak zarządzać alertami zabezpieczeń i odpowiadać na nie
- [Automatyzowanie odpowiedzi na wyzwalacze Security Center](workflow-automation.md)— użycie usługi Automation w celu reagowania na wyzwalacze Security Center za pomocą niestandardowej logiki powiadomień