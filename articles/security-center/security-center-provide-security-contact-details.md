---
title: Podaj szczegóły kontaktu zabezpieczeń w Azure Security Center | Microsoft Docs
description: W tym dokumencie pokazano, jak zapewnić szczegóły dotyczące kontaktu z zabezpieczeniami w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2019
ms.author: memildin
ms.openlocfilehash: 08ad761e81909e6ab23c7c07f5ce05865136bc47
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204104"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Podaj szczegóły kontaktu zabezpieczeń w Azure Security Center
Usługa Azure Security Center zaleci podanie szczegółowych informacji kontaktowych do spraw zabezpieczeń dla Twojej subskrypcji platformy Azure, jeśli nie zostały jeszcze podane. Te informacje będą używane przez firmę Microsoft do kontaktowania się z Tobą, gdy centrum Microsoft Security Response Center (MSRC) wykryje, że osoby nieupoważnione lub działające niezgodnie z prawem uzyskały dostęp do Twoich danych klienta. Centrum zabezpieczeń firmy Microsoft wybiera monitorowanie sieci i infrastruktury platformy Azure, a następnie odbiera dane analizy zagrożeń i skargi dotyczące zagrożeń od podmiotów zewnętrznych.

Powiadomienie e-mail jest wysyłane po pierwszym wystąpieniu alertu w ciągu dnia i tylko w przypadku alertów o wysokiej ważności. Preferencje poczty e-mail można konfigurować tylko dla zasad subskrypcji. Grupy zasobów w ramach subskrypcji będą dziedziczyć te ustawienia. Alerty są dostępne tylko w warstwie Standardowa Azure Security Center.

Powiadomienia e-mail o alertach są wysyłane:
- Do pojedynczego adresata wiadomości e-mail na każdy typ alertu dziennie  
- Nie więcej niż 3 wiadomości e-mail są wysyłane do pojedynczego odbiorcy w jednym dniu
- Każda wiadomość e-mail zawiera jeden alert, a nie agregację alertów
- Tylko w przypadku alertów o wysokiej ważności

> [!TIP]
> W przypadku alertów z innymi poziomami ważności Utwórz [automatyzację przepływu pracy](workflow-automation.md) , aby użyć aplikacji logiki, która będzie wysyłać wiadomości e-mail do odpowiedniego personelu.
 
Jeśli na przykład została już wysłana wiadomość e-mail z powiadomieniem o ataku przez protokół RDP, tego samego dnia nie otrzymasz kolejnej wiadomości e-mail o ataku przez protokół RDP, nawet jeśli zostanie wyzwolony kolejny alert. 

> [!IMPORTANT]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.

## <a name="set-up-email-notifications-for-alerts"></a>Konfigurowanie powiadomień e-mail dla alertów<a name="email"></a>

1. Jako użytkownik z administratorem roli lub właścicielem subskrypcji Otwórz stronę **powiadomienia e-mail** :

    - W obszarze alerty Otwórz pozycję **cennik & ustawienia**, wybierz odpowiednią subskrypcję, a następnie wybierz pozycję **powiadomienia e-mail**.

    - Jeśli wdrażasz zalecenie, w obszarze **rekomendacje**wybierz pozycję **Podaj szczegóły kontaktu zabezpieczeń**, wybierz subskrypcję platformy Azure, w której mają zostać wprowadzone informacje kontaktowe. Spowoduje to otwarcie **powiadomień e-mail**.

   ![Podawanie szczegółów dotyczących kontaktu ds. zabezpieczeń][2]

1. Wprowadź adres lub adresy e-mail kontaktu z zabezpieczeniami oddzielone przecinkami. Nie ma żadnego limitu liczby adresów e-mail, które można wprowadzić.

1. Aby otrzymywać wiadomości e-mail o alertach o wysokiej ważności, Włącz opcję **Wyślij mi wiadomości e-mail o alertach**. W przypadku innych poziomów ważności Użyj aplikacji logiki opisanej w temacie [Automatyzacja przepływu pracy](workflow-automation.md).

1. Powiadomienia e-mail mogą być wysyłane do właścicieli subskrypcji (administrator usługi klasycznej i współadministratorzy oraz rola właściciela RBAC w zakresie subskrypcji).

1. Aby zastosować informacje o kontakcie zabezpieczeń do subskrypcji, wybierz pozycję **Zapisz**.

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md) — Dowiedz się, jak zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
