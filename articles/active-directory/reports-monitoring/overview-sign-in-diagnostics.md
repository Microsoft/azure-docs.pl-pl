---
title: Co to jest Diagnostyka logowania w usłudze Azure AD? | Microsoft Docs
description: Zawiera ogólne omówienie diagnostyki logowania w usłudze Azure AD.
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
ms.openlocfilehash: e85de1edd94a0430a4b28b332d9e43b967afba76
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608922"
---
# <a name="what-is-sign-in-diagnostic-in-azure-ad"></a>Co to jest Diagnostyka logowania w usłudze Azure AD?

Usługa Azure AD zapewnia elastyczny model zabezpieczeń, który pozwala kontrolować, co użytkownicy mogą robić z zasobami zarządzanymi. Dostęp do tych zasobów nie jest kontrolowany **przez użytkownika, ale również przez** **użytkowników** . Zwykle elastyczność obejmuje określony stopień złożoności z powodu liczby posiadanych opcji konfiguracji. Złożoność ma możliwość zwiększenia ryzyka związanego z błędami.

Jako administrator IT potrzebujesz rozwiązania, które zapewnia odpowiedni poziom szczegółowych informacji o działaniach w systemie, dzięki czemu można łatwo diagnozować i rozwiązywać problemy, gdy wystąpią. Przykładem takiego rozwiązania jest Diagnostyka logowania dla usługi Azure AD. Użyj diagnostyki, aby przeanalizować, co się stało podczas logowania i jakie akcje można podjąć, aby rozwiązać problemy bez konieczności korzystania z pomocy technicznej firmy Microsoft.

Ten artykuł zawiera omówienie sposobu działania rozwiązania oraz sposobu jego użycia.


## <a name="requirements"></a>Wymagania

Diagnostyka logowania jest dostępna we wszystkich wersjach usługi Azure AD.<br> Musisz być administratorem globalnym w usłudze Azure AD, aby go używać.

## <a name="how-it-works"></a>Jak to działa

W usłudze Azure AD odpowiedź na próbę logowania jest związana z tym **, kto** i **jak** uzyskuje dostęp do dzierżawy. Na przykład administrator może zazwyczaj skonfigurować wszystkie aspekty dzierżawy po zalogowaniu się z sieci firmowej. Jednak po zalogowaniu się przy użyciu tego samego konta z niezaufanej sieci mogą być one nawet blokowane.
 
Ze względu na większą elastyczność systemu reagowanie na próbę logowania można zakończyć w scenariuszach, w których należy rozwiązać problemy z logowaniem. Diagnostyka logowania to funkcja, która:

- Analizuje dane z logowania. 

- Wyświetla informacje o tym, co się stało, i zalecenia dotyczące sposobu rozwiązywania problemów. 

Diagnostyka logowania dla usługi Azure AD została zaprojektowana w celu umożliwienia samodiagnostyki błędów logowania. Aby ukończyć proces diagnostyczny, należy wykonać następujące czynności:

![Proces diagnostyki logowania](./media/overview-sign-in-diagnostics/process.png)
 
1. **Zdefiniuj** zakres zdarzeń logowania, które Cię interesują

2. **Wybierz** logowanie, które chcesz przejrzeć

3. **Przejrzyj** wynik diagnostyki

4. **Wykonaj** akcje

 
### <a name="define-scope"></a>Zdefiniuj zakres

Celem tego kroku jest zdefiniowanie zakresu logowania, które chcesz zbadać. Zakres jest oparty na użytkowniku lub identyfikatorze (identyfikatorze korelacji) i przedziale czasu. Aby jeszcze bardziej zawęzić zakres, można również określić nazwę aplikacji. Usługa Azure AD korzysta z informacji o zakresie, aby znaleźć odpowiednie zdarzenia.  

### <a name="select-sign-in"></a>Wybierz pozycję Zaloguj  

Na podstawie kryteriów wyszukiwania usługa Azure AD pobiera wszystkie zgodne logowania i przedstawia je w widoku listy podsumowania uwierzytelniania. 

![Podsumowanie uwierzytelniania](./media/overview-sign-in-diagnostics/authentication-summary.png)
 
Można dostosować kolumny wyświetlane w tym widoku.

### <a name="review-diagnostic"></a>Przegląd diagnostyki 

W przypadku wybranego zdarzenia logowania usługa Azure AD udostępnia wynik diagnostyki. 

![Wyniki diagnostyki](./media/overview-sign-in-diagnostics/diagnostics-results.png)

 
Wynik rozpoczyna się od oceny. W wyniku oceny przedstawiono kilka zdań, co się stało. Wyjaśnienie pomaga zrozumieć zachowanie systemu. 

Następnym krokiem jest podsumowanie powiązanych zasad dostępu warunkowego, które zostały zastosowane do wybranego logowania. Ta część jest wykonywana przez zalecane kroki zaradcze w celu rozwiązania problemu. Ponieważ nie zawsze jest możliwe Rozwiązywanie problemów bez dodatkowej pomocy, zalecanym krokiem może być otwarcie biletu pomocy technicznej. 

### <a name="take-action"></a>Wykonywanie akcji 
W tym momencie należy uzyskać informacje potrzebne do rozwiązania problemu.


## <a name="scenarios"></a>Scenariusze

Ta sekcja zawiera omówienie scenariuszy diagnostyki objętych usługą. Zaimplementowane są następujące scenariusze: 
 
- Zablokowany przez dostęp warunkowy

- Niepowodzenie dostępu warunkowego

- Uwierzytelnianie wieloskładnikowe z dostępu warunkowego

- Uwierzytelnianie wieloskładnikowe od innych wymagań

- Wymagane jest potwierdzenie uwierzytelniania MFA

- Wymagane jest potwierdzenie uwierzytelniania MFA, ale próba logowania użytkownika nie pochodzi z bezpiecznej lokalizacji

- Pomyślne logowanie


### <a name="blocked-by-conditional-access"></a>Zablokowany przez dostęp warunkowy

Ten scenariusz jest oparty na logowaniu, które zostało zablokowane przez zasady dostępu warunkowego.

![Blokowanie dostępu](./media/overview-sign-in-diagnostics/block-access.png)

Sekcja Diagnostic w tym scenariuszu przedstawia szczegółowe informacje na temat logowania użytkownika i stosowanych zasad.


### <a name="failed-conditional-access"></a>Niepowodzenie dostępu warunkowego

Ten scenariusz jest zwykle wynikiem logowania, które nie powiodło się, ponieważ nie spełniono wymagań zasad dostępu warunkowego. Typowe przykłady:

![Wymagaj kontrolek](./media/overview-sign-in-diagnostics/require-controls.png)

- Wymagaj hybrydowego urządzenia dołączonego do usługi Azure AD

- Wymaganie zatwierdzonej aplikacji klienckiej

- Wymaganie zasad ochrony aplikacji   


Sekcja Diagnostic w tym scenariuszu przedstawia szczegółowe informacje na temat logowania użytkownika i stosowanych zasad.


### <a name="mfa-from-conditional-access"></a>Uwierzytelnianie wieloskładnikowe z dostępu warunkowego

Ten scenariusz jest oparty na zasadach dostępu warunkowego, które wymagają zalogowania się przy użyciu zestawu uwierzytelniania wieloskładnikowego.

![Wymagaj uwierzytelniania wieloskładnikowego](./media/overview-sign-in-diagnostics/require-mfa.png)

Sekcja Diagnostic w tym scenariuszu przedstawia szczegółowe informacje na temat logowania użytkownika i stosowanych zasad.



### <a name="mfa-from-other-requirements"></a>Uwierzytelnianie wieloskładnikowe od innych wymagań

Ten scenariusz jest oparty na wymaganiu uwierzytelniania wieloskładnikowego, który nie został wymuszony przez zasady dostępu warunkowego. Na przykład uwierzytelnianie wieloskładnikowe na poszczególnych użytkownikach.


![Wymagaj uwierzytelniania wieloskładnikowego na użytkownika](./media/overview-sign-in-diagnostics/mfa-per-user.png)


Celem tego scenariusza diagnostycznego jest dostarczenie dodatkowych informacji na temat:

- Źródło przerwania usługi uwierzytelnianie wieloskładnikowe. 
- Wynik interakcji z klientem.

Ponadto ta sekcja zawiera również wszystkie szczegółowe informacje o próbie logowania użytkownika. 


### <a name="mfa-proof-up-required"></a>Wymagane jest potwierdzenie uwierzytelniania MFA

Ten scenariusz jest oparty na logowaniach, które zostały przerwane przez żądania konfigurowania uwierzytelniania wieloskładnikowego. Ta konfiguracja jest również znana jako "test in".

Potwierdzenie uwierzytelniania wieloskładnikowego występuje, gdy użytkownik musi korzystać z uwierzytelniania wieloskładnikowego, ale nie został jeszcze skonfigurowany, lub administrator skonfigurował go tak, aby go skonfigurował.

Zamiarem tego scenariusza diagnostycznego jest zapewnienie wglądu w to, że przerwanie usługi uwierzytelnianie wieloskładnikowe miało na celu zapewnienie, że użytkownik ukończy proces weryfikacji.

### <a name="mfa-proof-up-required-from-a-risky-sign-in"></a>Wymagane jest potwierdzenie usługi MFA z ryzykownego logowania

Ten scenariusz skutkuje zalogowaniem, które zostało przerwane przez żądanie skonfigurowania uwierzytelniania wieloskładnikowego na podstawie ryzykownego logowania. 

Celem tego scenariusza diagnostycznego jest zapewnienie wglądu w to, że przerwanie usługi uwierzytelniania wieloskładnikowego było możliwe, aby zapewnić, że użytkownik zakończy proces weryfikacji, ale tak samo nie jest w lokalizacji sieciowej, która nie jest ryzykowna. Na przykład jeśli sieć firmowa jest zdefiniowana jako nazwana lokalizacja, spróbuj wykonać próbkę z sieci firmowej.


### <a name="successful-sign-in"></a>Pomyślne logowanie

Ten scenariusz jest oparty na logowaniach, które nie zostały przerwane przez dostęp warunkowy lub uwierzytelnianie wieloskładnikowe.

Celem tego scenariusza diagnostycznego jest zapewnienie wglądu w informacje o tym, co użytkownik podano podczas logowania, w przypadku istnienia zasad dostępu warunkowego lub zasad, które były stosowane, lub skonfigurowanego uwierzytelniania wieloskładnikowego, które było przerywane Logowanie użytkownika.



## <a name="next-steps"></a>Następne kroki

* [Co to są raporty usługi Azure Active Directory?](overview-reports.md)
* [Co to jest monitorowanie usługi Azure Active Directory?](overview-monitoring.md)
