---
title: Zawieszone domeny w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się więcej o różnych stanach kondycji dla domeny zarządzanej AD DS platformy Azure i sposobach przywracania zawieszonej domeny.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: b0b48e7ad494386052e6d94c32d7215b3f4f0202
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618828"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Informacje o Stanach kondycji i rozwiązywaniu zawieszonych domen w Azure Active Directory Domain Services

Gdy Azure Active Directory Domain Services (Azure AD DS) nie jest w stanie obsłużyć domeny zarządzanej przez długi czas, spowoduje to przełączenie domeny zarządzanej do stanu wstrzymania. Jeśli zarządzana domena pozostaje w stanie wstrzymania, zostanie ona automatycznie usunięta. Aby zapewnić, że platforma Azure AD DS zarządzana w dobrej kondycji i unikaj zawieszania, Rozwiąż wszelkie alerty tak szybko, jak to możliwe.

W tym artykule wyjaśniono, dlaczego domeny zarządzane są zawieszone i jak odzyskać zawieszoną domenę.

## <a name="overview-of-managed-domain-states"></a>Przegląd Stanów domeny zarządzanej

W cyklu życia domeny zarządzanej istnieją różne stany, które wskazują jego kondycję. Jeśli zarządzana domena zgłasza problem, szybko rozwiązać podstawową przyczynę, aby zatrzymać stan z ciągłego obniżenia poziomu.

![Postęp w stanie, w którym domena zarządza ma na celu zawieszenie](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Domena zarządzana może być w jednym z następujących stanów:

* [Uruchomienie](#running-state)
* [Wymaga uwagi](#needs-attention-state)
* [Suspended](#suspended-state)
* [Usunięta](#deleted-state)

## <a name="running-state"></a>Stan działania

Domena zarządzana, która jest skonfigurowana prawidłowo i bez problemów, jest w stanie *uruchomienia* . Jest to żądany stan dla domeny zarządzanej.

### <a name="what-to-expect"></a>Czego oczekiwać

* Platforma Azure umożliwia regularne monitorowanie kondycji domeny zarządzanej.
* Kontrolery domeny dla domeny zarządzanej są poprawiane i regularnie aktualizowane.
* Zmiany z Azure Active Directory są regularnie synchronizowane z domeną zarządzaną.
* Regularne kopie zapasowe są tworzone dla domeny zarządzanej.

## <a name="needs-attention-state"></a>Wymaga stanu uwagi

Domena zarządzana z co najmniej jednym zagadnieniem, które należy naprawić, jest w stanie *wymaga uwagi* . Na stronie kondycji dla domeny zarządzanej znajduje się lista alertów i wskazuje, gdzie występuje problem.

Niektóre alerty są przejściowe i automatycznie rozwiązywane przez platformę Azure. W przypadku innych alertów można rozwiązać ten problem, wykonując podane kroki rozwiązania. Występuje alert krytyczny, [Otwórz żądanie pomocy technicznej platformy Azure][azure-support] , aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

Przykładem alertu jest nierestrykcyjna sieciowa Grupa zabezpieczeń. W tej konfiguracji platforma platformy Azure może nie być w stanie zaktualizować i monitorować domeny zarządzanej. Generowany jest alert, a stan zmieni się na *wymaga uwagi*.

Aby uzyskać więcej informacji, zobacz [Jak rozwiązywać problemy z alertami dla domeny zarządzanej][resolve-alerts].

### <a name="what-to-expect"></a>Czego oczekiwać

Gdy domena zarządzana jest w stanie *wymaga uwagi* , platforma Azure może nie być w stanie regularnie monitorować, poprawiać, aktualizować ani tworzyć kopii zapasowych danych. W niektórych przypadkach, takich jak nieprawidłowa konfiguracja sieci, kontrolery domeny dla domeny zarządzanej mogą być nieosiągalne.

* Domena zarządzana jest w złej kondycji, a ciągłe monitorowanie kondycji może zostać zatrzymane do momentu rozwiązania alertu.
* Nie można uaktualnić ani zaktualizować kontrolerów domeny dla domeny zarządzanej.
* Zmiany z Azure Active Directory mogą nie być zsynchronizowane z domeną zarządzaną.
* Nie można wykonać kopii zapasowych dla domeny zarządzanej.
* W przypadku rozwiązywania alertów niekrytycznych, które mają wpływ na domenę zarządzaną, kondycja powinna powrócić do stanu *uruchomienia* .
* Alerty krytyczne są wyzwalane w przypadku problemów z konfiguracją, w których platforma Azure nie może nawiązać połączenia z kontrolerami domeny. Jeśli te alerty krytyczne nie zostaną rozwiązane w ciągu 15 dni, domena zarządzana przejdzie do stanu *wstrzymania* .

## <a name="suspended-state"></a>Stan wstrzymania

Domena zarządzana przechodzi do stanu **wstrzymania** z jednego z następujących powodów:

* Nie rozwiązano co najmniej jednego alertu krytycznego w ciągu 15 dni.
    * Alerty krytyczne mogą być spowodowane przez nieprawidłową konfigurację, która blokuje dostęp do zasobów, które są wymagane przez usługę Azure AD DS. Na przykład alert [AADDS104: błąd sieci][alert-nsg] został rozwiązany przez ponad 15 dni w domenie zarządzanej.
* Wystąpił problem z rozliczeniami związany z subskrypcją platformy Azure lub subskrypcja platformy Azure wygasła.

Domeny zarządzane są zawieszane, gdy platforma Azure nie może zarządzać, monitorować, poprawiać ani tworzyć kopii zapasowej domeny. Domena zarządzana pozostaje w stanie *wstrzymania* przez 15 dni. Aby zapewnić dostęp do domeny zarządzanej, należy natychmiast rozwiązać alerty krytyczne.

### <a name="what-to-expect"></a>Czego oczekiwać

W przypadku, gdy domena zarządzana jest w stanie *wstrzymania* , wystąpią następujące działania:

* Kontrolery domeny dla domeny zarządzanej są nieobsługiwane i nie są dostępne w sieci wirtualnej.
* Secure LDAP dostęp do domeny zarządzanej za pośrednictwem Internetu, jeśli ta funkcja jest włączona, przestanie działać.
* Występują błędy uwierzytelniania w domenie zarządzanej, logowanie do przyłączonych do domeny maszyn wirtualnych lub łączenie za pośrednictwem protokołu LDAP/LDAPs.
* Nie są już wykonywane kopie zapasowe dla domeny zarządzanej.
* Synchronizacja z usługą Azure AD zostaje zatrzymana.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Jak sprawdzić, czy Twoja domena zarządzana jest zawieszona?

Na Azure Portal stronie usługi Azure AD DS Health zostanie wyświetlony [alert][resolve-alerts] z informacją o wstrzymaniu domeny. Stan domeny jest również wyświetlany jako *zawieszony*.

### <a name="restore-a-suspended-domain"></a>Przywracanie zawieszonej domeny

Aby przywrócić kondycję domeny zarządzanej, która jest w stanie *wstrzymania* , wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **usługi domenowe**.
1. Wybierz domenę zarządzaną z listy, na przykład *aaddscontoso.com*, a następnie wybierz pozycję **kondycja**.
1. Wybierz Alert, taki jak *AADDS503* lub *AADDS504*, w zależności od przyczyny zawieszenia.
1. Wybierz łącze rozwiązania, które jest dostępne w alercie, a następnie postępuj zgodnie z instrukcjami, aby rozwiązać ten problem.

Domenę zarządzaną można przywrócić tylko do daty ostatniej kopii zapasowej. Data ostatniej kopii zapasowej jest wyświetlana na stronie **kondycja** domeny zarządzanej. Wszelkie zmiany, które wystąpiły po utworzeniu ostatniej kopii zapasowej, nie zostaną przywrócone. Kopie zapasowe dla domeny zarządzanej są przechowywane przez maksymalnie 30 dni. Usuwane są kopie zapasowe starsze niż 30 dni.

Po rozwiązaniu alertów, gdy domena zarządzana jest w stanie *wstrzymania* , [Otwórz żądanie pomocy technicznej platformy Azure][azure-support] , aby powrócić do stanu dobrej kondycji. Jeśli istnieje kopia zapasowa starsza niż 30 dni, pomoc techniczna platformy Azure może przywrócić domenę zarządzaną.

## <a name="deleted-state"></a>Stan usunięcia

Jeśli zarządzana domena pozostaje w stanie *wstrzymania* przez 15 dni, zostanie usunięta. Ten proces nie jest możliwy do odzyskania.

### <a name="what-to-expect"></a>Czego oczekiwać

Gdy domena zarządzana przejdzie do stanu *usunięte* , zostanie wyświetlona następująca wartość:

* Wszystkie zasoby i kopie zapasowe dla domeny zarządzanej są usuwane.
* Nie można przywrócić domeny zarządzanej. Aby ponownie użyć usługi Azure AD DS, należy utworzyć zastępczą domenę zarządzaną.
* Po jego usunięciu nie są naliczane opłaty za domenę zarządzaną.

## <a name="next-steps"></a>Następne kroki

Aby zachować kondycję domeny zarządzanej i zminimalizować ryzyko jej wstrzymania, Dowiedz się, jak [rozwiązywać alerty dla domeny zarządzanej][resolve-alerts].

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
