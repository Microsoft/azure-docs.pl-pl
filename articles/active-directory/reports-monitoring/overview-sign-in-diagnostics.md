---
title: Co to jest Diagnostyka logowania dla Azure Active Directory?
description: Zawiera ogólne omówienie diagnostyki logowania w Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/15/2020
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdef3e1f1a60c9eb0c751855837e9cbe77e015e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98572293"
---
# <a name="what-is-the-sign-in-diagnostic-in-azure-ad"></a>Co to jest Diagnostyka logowania w usłudze Azure AD?

Azure Active Directory (Azure AD) zapewnia elastyczny model zabezpieczeń, który pozwala kontrolować, co użytkownicy mogą robić z zasobami zarządzanymi. Dostęp do tych zasobów jest kontrolowany nie tylko przez *kogo* są, ale *również poprzez dostęp do nich.* Zwykle elastyczny model ma określony stopień złożoności ze względu na liczbę dostępnych opcji konfiguracji. Złożoność ma możliwość zwiększenia ryzyka związanego z błędami.

Jako administrator IT potrzebujesz rozwiązania umożliwiającego wgląd w działania w systemie. Ta widoczność może umożliwić diagnozowanie i rozwiązywanie problemów, gdy wystąpią. Przykładem takiego rozwiązania jest Diagnostyka logowania dla usługi Azure AD. Możesz użyć diagnostyki, aby przeanalizować, co się stało podczas próby logowania, i uzyskać zalecenia dotyczące rozwiązywania problemów bez konieczności korzystania z pomocy technicznej firmy Microsoft.

Ten artykuł zawiera omówienie sposobu działania rozwiązania oraz sposobu jego użycia.

## <a name="requirements"></a>Wymagania

Diagnostyka logowania jest dostępna we wszystkich wersjach usługi Azure AD.

Musisz być administratorem globalnym w usłudze Azure AD, aby go używać.

## <a name="how-it-works"></a>Jak to działa

W usłudze Azure AD odpowiedź na próbę logowania jest powiązana z *osobami, które* logują się i *jak* uzyskują dostęp do dzierżawy. Na przykład administrator może zwykle skonfigurować wszystkie aspekty dzierżawy po zalogowaniu się z sieci firmowej. Jednak ten sam użytkownik może zostać zablokowany po zalogowaniu się przy użyciu tego samego konta z niezaufanej sieci.

Ze względu na większą elastyczność systemu reagowanie na próbę logowania można zakończyć w scenariuszach, w których należy rozwiązać problemy z logowaniem. Diagnostyka logowania to funkcja, która:

- Analizuje dane z zdarzeń logowania.

- Pokazuje, co się stało.

- Zawiera zalecenia dotyczące sposobu rozwiązywania problemów.

Diagnostyka logowania dla usługi Azure AD została zaprojektowana w celu umożliwienia samodiagnostyki błędów logowania. Aby ukończyć proces diagnostyczny, należy wykonać następujące czynności:

![Diagram przedstawiający diagnostykę logowania.](./media/overview-sign-in-diagnostics/process.png)

1. Zdefiniuj zakres zdarzeń związanych z logowaniem.

2. Wybierz Logowanie, które chcesz przejrzeć.

3. Przejrzyj wyniki diagnostyki.

4. Wykonaj akcję.

### <a name="define-scope"></a>Zdefiniuj zakres

Celem tego kroku jest zdefiniowanie zakresu zdarzeń logowania do zbadania. Zakres jest oparty na użytkowniku lub w identyfikatorze (identyfikatorze korelacji) i przedziale czasu. Aby jeszcze bardziej zawęzić zakres, możesz określić nazwę aplikacji. Usługa Azure AD korzysta z informacji o zakresie, aby znaleźć odpowiednie zdarzenia.  

### <a name="select-sign-in"></a>Wybierz pozycję Zaloguj  

Na podstawie kryteriów wyszukiwania usługa Azure AD pobiera wszystkie zgodne zdarzenia logowania i przedstawia je w widoku listy podsumowania uwierzytelniania.

![Zrzut ekranu przedstawiający sekcję Podsumowanie uwierzytelniania.](./media/overview-sign-in-diagnostics/authentication-summary.png)

Można dostosować kolumny wyświetlane w tym widoku.

### <a name="review-diagnostic"></a>Przegląd diagnostyki

W przypadku wybranego zdarzenia logowania usługa Azure AD zapewnia wyniki diagnostyki.

![Zrzut ekranu przedstawiający sekcję wyniki diagnostyki.](./media/overview-sign-in-diagnostics/diagnostics-results.png)

Wyniki te zaczynają się od oceny, która wyjaśnia, co się stało w kilku zdaniach. Wyjaśnienie pomaga zrozumieć zachowanie systemu.

Następnie zostanie wyświetlone podsumowanie powiązanych zasad dostępu warunkowego, które zostały zastosowane do wybranego zdarzenia logowania. Wyniki diagnostyki obejmują również zalecane czynności zaradcze umożliwiające rozwiązanie problemu. Ponieważ nie zawsze jest możliwe Rozwiązywanie problemów bez dodatkowych pomocy, zalecanym krokiem może być otwarcie biletu pomocy technicznej.

### <a name="take-action"></a>Wykonywanie akcji

W tym momencie należy uzyskać informacje potrzebne do rozwiązania problemu.

## <a name="scenarios"></a>Scenariusze

Diagnostyka logowania obejmuje następujące scenariusze:

- Zablokowany przez dostęp warunkowy

- Niepowodzenie dostępu warunkowego

- Uwierzytelnianie wieloskładnikowe (MFA) z dostępu warunkowego

- Uwierzytelnianie wieloskładnikowe od innych wymagań

- Wymagane jest potwierdzenie uwierzytelniania MFA

- Wymagane jest potwierdzenie usługi MFA (ryzykowna lokalizacja logowania)

- Pomyślne logowanie

### <a name="blocked-by-conditional-access"></a>Zablokowany przez dostęp warunkowy

W tym scenariuszu próba logowania została zablokowana przez zasady dostępu warunkowego.

![Zrzut ekranu przedstawiający konfigurację dostępu z wybranym blokiem dostępu.](./media/overview-sign-in-diagnostics/block-access.png)

Sekcja Diagnostic w tym scenariuszu przedstawia szczegółowe informacje dotyczące zdarzenia logowania użytkownika i stosowane zasady.

### <a name="failed-conditional-access"></a>Niepowodzenie dostępu warunkowego

Ten scenariusz jest zwykle wynikiem próby logowania, która nie powiodła się, ponieważ nie spełniono wymagań zasad dostępu warunkowego. Typowe przykłady:

![Zrzut ekranu przedstawiający konfigurację dostępu ze wspólnymi przykładami zasad i udzielą wybranego dostępu.](./media/overview-sign-in-diagnostics/require-controls.png)

- Wymagaj hybrydowego urządzenia dołączonego do usługi Azure AD

- Wymaganie zatwierdzonej aplikacji klienckiej

- Wymaganie zasad ochrony aplikacji

Sekcja Diagnostic w tym scenariuszu zawiera szczegółowe informacje o próbie logowania użytkownika i zastosowaniu zasad.

### <a name="mfa-from-conditional-access"></a>Uwierzytelnianie wieloskładnikowe z dostępu warunkowego

W tym scenariuszu zasady dostępu warunkowego wymagają zalogowania się przy użyciu zestawu uwierzytelniania wieloskładnikowego.

![Zrzut ekranu przedstawiający konfigurację dostępu z wybranym wymaganiem uwierzytelniania wieloskładnikowego.](./media/overview-sign-in-diagnostics/require-mfa.png)

Sekcja Diagnostic w tym scenariuszu zawiera szczegółowe informacje o próbie logowania użytkownika i zastosowaniu zasad.

### <a name="mfa-from-other-requirements"></a>Uwierzytelnianie wieloskładnikowe od innych wymagań

W tym scenariuszu wymóg uwierzytelniania wieloskładnikowego nie został wymuszony przez zasady dostępu warunkowego. Na przykład uwierzytelnianie wieloskładnikowe na poszczególnych użytkowników.

![Zrzut ekranu przedstawiający uwierzytelnianie wieloskładnikowe na poszczególnych użytkownikach.](./media/overview-sign-in-diagnostics/mfa-per-user.png)

Celem tego scenariusza diagnostycznego jest dostarczenie dodatkowych informacji na temat:

- Źródło przerwania uwierzytelniania wieloskładnikowego
- Wynik interakcji z klientem

Możesz również wyświetlić wszystkie szczegóły próby logowania użytkownika.

### <a name="mfa-proof-up-required"></a>Wymagane jest potwierdzenie uwierzytelniania MFA

W tym scenariuszu próby logowania zostały przerwane przez żądania skonfigurowania uwierzytelniania wieloskładnikowego. Ta konfiguracja jest również znana jako weryfikacja.

Weryfikacja wieloskładnikowego uwierzytelniania odbywa się, gdy użytkownik musi korzystać z uwierzytelniania wieloskładnikowego, ale nie został jeszcze skonfigurowany, lub administrator wymaga od użytkownika skonfigurowania go.

Celem tego scenariusza diagnostycznego jest ujawnienie faktu, że przerwanie uwierzytelniania wieloskładnikowego było spowodowane brakiem konfiguracji użytkownika. Zalecane rozwiązanie jest przeznaczone dla użytkownika, aby ukończyć weryfikację.

### <a name="mfa-proof-up-required-risky-sign-in-location"></a>Wymagane jest potwierdzenie usługi MFA (ryzykowna lokalizacja logowania)

W tym scenariuszu próby logowania zostały przerwane przez żądanie skonfigurowania uwierzytelniania wieloskładnikowego z ryzykownej lokalizacji logowania.

Celem tego scenariusza diagnostycznego jest ujawnienie faktu, że przerwanie uwierzytelniania wieloskładnikowego było spowodowane brakiem konfiguracji użytkownika. Zalecane rozwiązanie jest przeznaczone dla użytkownika, aby ukończyć weryfikację, w odróżnieniu od lokalizacji sieciowej, która nie jest ryzykowna.

Na przykład jeśli sieć firmowa jest zdefiniowana jako lokalizacja nazwana, użytkownik powinien podjąć próbę sprawdzenia z sieci firmowej.

### <a name="successful-sign-in"></a>Pomyślne logowanie

W tym scenariuszu zdarzenia logowania nie zostały przerwane przez dostęp warunkowy lub uwierzytelnianie wieloskładnikowe.

Ten scenariusz diagnostyczny zawiera szczegółowe informacje o zdarzeniach logowania użytkowników, które powinny zostać przerwane z powodu zasad dostępu warunkowego lub uwierzytelniania wieloskładnikowego.

## <a name="next-steps"></a>Następne kroki

- [Co to są raporty usługi Azure Active Directory?](overview-reports.md)
- [Co to jest monitorowanie usługi Azure Active Directory?](overview-monitoring.md)
