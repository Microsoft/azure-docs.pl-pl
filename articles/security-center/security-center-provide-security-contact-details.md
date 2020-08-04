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
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: 5a2410a5ccaa10867e94b3a2ec10228a006fbe20
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534638"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>Konfigurowanie powiadomień e-mail dla alertów zabezpieczeń 

Aby zapewnić właściwym osobom w organizacji powiadomienia o alertach zabezpieczeń w Twoim środowisku, wprowadź ich adresy e-mail na stronie ustawień **powiadomień e-mail** .

Podczas konfigurowania powiadomień możesz skonfigurować wiadomości e-mail do wysłania do określonych osób lub do wszystkich użytkowników z określoną rolą platformy Azure dla subskrypcji. 

Aby uniknąć zmęczenia alertów, Security Center ogranicza ilość wychodzących wiadomości e-mail. Dla każdej subskrypcji Security Center wysyła:

- maksymalnie **cztery** wiadomości e-mail dziennie dla alertów **o wysokiej ważności**
- maksymalnie **dwa** wiadomości e-mail dziennie dla alertów o **średniej ważności**
- maksymalnie **jedna** wiadomość e-mail na dzień w przypadku alertów **o niskim znaczeniu**

## <a name="availability"></a>Dostępność

- Stan wydania: **ogólnie dostępny**
- Wymagane role: **administrator zabezpieczeń** lub **właściciel subskrypcji** 
- Chmury: ✔ chmury komercyjne ✔ US Gov (częściowa) ✘ Narodowy/suwerenny (Chiny gov, inne gov)


## <a name="set-up-email-notifications-for-alerts"></a>Konfigurowanie powiadomień e-mail dla alertów<a name="email"></a>

Powiadomienia e-mail mogą być wysyłane do osób indywidualnych lub wszystkich użytkowników z określonymi rolami platformy Azure.

1. W obszarze **ustawienia & cennika** Security Center, odpowiedniej subskrypcji i wybierz opcję **powiadomienia e-mail**.

1. Zdefiniuj adresatów powiadomień:

    - Z listy rozwijanej wybierz pozycję spośród dostępnych ról.
    - I/lub wprowadź określone adresy e-mail oddzielone przecinkami. Nie ma żadnego limitu liczby adresów e-mail, które można wprowadzić.

1. Aby zastosować informacje o kontakcie zabezpieczeń do subskrypcji, wybierz pozycję **Zapisz**.


## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat alertów zabezpieczeń, zobacz następujące tematy:

* [Alerty zabezpieczeń — Przewodnik referencyjny](alerts-reference.md) — informacje na temat alertów zabezpieczeń, które mogą zostać wyświetlone w module ochrony przed zagrożeniami w Azure Security Center
* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak zarządzać alertami zabezpieczeń i odpowiadać na nie