---
title: Powiadomienia e-mail w programie PIM — Azure Active Directory | Microsoft Docs
description: Opisuje powiadomienia e-mail w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/28/2020
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe57a44a5a6fa9a631604d92419fd8f5ebcce50a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394734"
---
# <a name="email-notifications-in-pim"></a>Powiadomienia e-mail w usłudze PIM

Privileged Identity Management (PIM) informuje o tym, kiedy ważne zdarzenia wystąpią w organizacji Azure Active Directory (Azure AD), np. podczas przypisywania lub aktywowania roli. Privileged Identity Management informować użytkownika, wysyłając powiadomienia e-mail i innych uczestników. Te wiadomości e-mail mogą również zawierać linki do odpowiednich zadań, takich jak aktywowanie lub odnawianie roli. W tym artykule opisano, jak wyglądają te wiadomości e-mail, gdy są one wysyłane i kto je otrzymuje.

## <a name="sender-email-address-and-subject-line"></a>Adres e-mail nadawcy i wiersz tematu

Wiadomości e-mail wysyłane z Privileged Identity Management dla ról usługi Azure AD i Azure Resource mają następujący adres e-mail nadawcy:

- Adres e-mail:  **Azure-noreply \@ Microsoft.com**
- Nazwa wyświetlana: Microsoft Azure

Te wiadomości e-mail zawierają prefiks **PIM** w wierszu tematu. Oto przykład:

- PIM: Alain Charon ma trwale przypisaną rolę czytnika kopii zapasowych

## <a name="notifications-for-azure-ad-roles"></a>Powiadomienia dla ról usługi Azure AD

Privileged Identity Management wysyła wiadomości e-mail po wystąpieniu następujących zdarzeń dla ról usługi Azure AD:

- Po oczekiwaniu na zatwierdzenie przez uprzywilejowaną rolę
- Po ukończeniu żądania aktywacji roli uprzywilejowanej
- Gdy Azure AD Privileged Identity Management jest włączona

Kto odbiera te wiadomości e-mail dla ról usługi Azure AD zależy od roli, zdarzenia i ustawienia powiadomienia:

| Użytkownik | Aktywacja roli oczekuje na zatwierdzenie | Żądanie aktywacji roli zostało zakończone | Jest włączony PIM |
| --- | --- | --- | --- |
| Administrator ról uprzywilejowanych</br>(Aktywowane/kwalifikujące się) | Tak</br>(tylko jeśli nie określono jawnych osób zatwierdzających) | Tak* | Tak |
| Administrator zabezpieczeń</br>(Aktywowane/kwalifikujące się) | Nie | Tak* | Tak |
| Administrator globalny</br>(Aktywowane/kwalifikujące się) | Nie | Tak* | Tak |

\*Jeśli ustawienie [ **powiadomienia**](pim-how-to-change-default-settings.md#notifications) jest ustawione na wartość **Włącz**.

Poniżej przedstawiono przykładową wiadomość e-mail, która jest wysyłana, gdy użytkownik aktywuje rolę usługi Azure AD dla fikcyjnej organizacji contoso.

![Nowa Privileged Identity Management e-mail dla ról usługi Azure AD](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Tygodniowy adres e-mail Privileged Identity Management Digest dla ról usługi Azure AD

Cotygodniowe Privileged Identity Management podsumowanie wiadomości e-mail dla ról usługi Azure AD jest wysyłane do administratorów ról uprzywilejowanych, administratorów zabezpieczeń i administratorów globalnych z włączonymi Privileged Identity Management. Ten tygodniowy adres e-mail zawiera migawkę Privileged Identity Management działań dla tego tygodnia oraz przypisań ról uprzywilejowanych. Jest on dostępny tylko dla organizacji usługi Azure AD w chmurze publicznej. Oto przykład wiadomości e-mail:

![Tygodniowy adres e-mail Privileged Identity Management Digest dla ról usługi Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

Wiadomość e-mail zawiera cztery kafelki:

| Kafelek | Opis |
| --- | --- |
| **Aktywowani użytkownicy** | Liczba przypadków, w których użytkownicy aktywowali odpowiednie role w organizacji. |
| **Użytkownicy z stałymi** | Liczba przypadków, w których użytkownicy z uprawnionym przypisaniem są trwałe. |
| **Przypisania ról w Privileged Identity Management** | Liczba użytkowników, którym przypisano kwalifikującą się rolę w Privileged Identity Management. |
| **Przypisania ról poza usługą PIM** | Liczba użytkowników, którym przypisano stałą rolę poza Privileged Identity Management (w ramach usługi Azure AD). |

Sekcja **najważniejsze role** zawiera pięć najważniejszych ról w organizacji na podstawie całkowitej liczby stałych i kwalifikujących się administratorów dla każdej roli. Link **podejmij działanie** powoduje otwarcie [Kreatora PIM](pim-security-wizard.md) , w którym można skonwertować stałych administratorów do uprawnionych administratorów w partiach.

## <a name="email-timing-for-activation-approvals"></a>Chronometraż wiadomości e-mail dla zatwierdzeń aktywacji

Gdy użytkownicy aktywują swoją rolę, a ustawienie roli wymaga zatwierdzenia, osoby zatwierdzające będą otrzymywać dwie wiadomości e-mail dla każdego zatwierdzenia:

- Żądanie zatwierdzenia lub odmowy żądania aktywacji użytkownika (wysyłane przez aparat zatwierdzania żądań)
- Żądanie użytkownika zostało zatwierdzone (wysłane przez aparat zatwierdzania żądań)

Ponadto administratorzy globalni i Administratorzy ról uprzywilejowanych otrzymają wiadomość e-mail przy każdym zatwierdzeniu:

- Rola użytkownika jest aktywowana (wysłana przez Privileged Identity Management)

Pierwsze dwie wiadomości e-mail wysyłane przez aparat zatwierdzania żądań mogą być opóźnione. Obecnie 90% wiadomości e-mail trwa od trzech do dziesięciu minut, ale 1% klientów może być o wiele dłużej, do 15 minut.

Jeśli żądanie zatwierdzenia zostanie zatwierdzone w Azure Portal przed wysłaniem pierwszej wiadomości e-mail, pierwsza wiadomość e-mail nie zostanie już wyzwolona, a inne osoby zatwierdzające nie będą powiadamiane pocztą e-mail o żądaniu zatwierdzenia. Może się wydawać, jakby nie dotarł do Ciebie wiadomość e-mail, ale jest to oczekiwane zachowanie.

## <a name="pim-emails-for-azure-resource-roles"></a>Wiadomości e-mail PIM dla ról zasobów platformy Azure

Privileged Identity Management wysyła wiadomości e-mail do właścicieli i administratorów dostępu użytkowników w przypadku wystąpienia następujących zdarzeń dla ról zasobów platformy Azure:

- Gdy przypisanie roli oczekuje na zatwierdzenie
- Po przypisaniu roli
- Gdy rola wkrótce wygaśnie
- Gdy rola może zostać rozszerzona
- Gdy rola jest odnawiana przez użytkownika końcowego
- Po ukończeniu żądania aktywacji roli

Privileged Identity Management wysyła wiadomości e-mail do użytkowników końcowych w przypadku wystąpienia następujących zdarzeń dla ról zasobów platformy Azure:

- Gdy rola jest przypisana do użytkownika
- Po wygaśnięciu roli użytkownika
- Gdy rola użytkownika jest rozszerzona
- Po ukończeniu żądania aktywacji roli użytkownika

Poniżej przedstawiono przykładową wiadomość e-mail, która jest wysyłana, gdy użytkownik ma przypisaną rolę zasobów platformy Azure dla fikcyjnej organizacji contoso.

![Nowa Privileged Identity Management e-mail dla ról zasobów platformy Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli usługi Azure AD w Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Zatwierdzanie lub odrzucanie żądań dla ról usługi Azure AD w Privileged Identity Management](azure-ad-pim-approval-workflow.md)
