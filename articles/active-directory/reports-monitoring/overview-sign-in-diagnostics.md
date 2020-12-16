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
ms.openlocfilehash: e113753bee5c0a94fbec47a2ea14b98c1779a394
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97578136"
---
# <a name="what-are-sign-in-diagnostics-in-azure-ad"></a>Co to jest Diagnostyka logowania w usłudze Azure AD?

Usługa Azure AD zapewnia elastyczny model zabezpieczeń, który pozwala kontrolować, co użytkownicy mogą robić z zasobami zarządzanymi. Dostęp do tych zasobów nie jest kontrolowany przez użytkownika, ale również przez użytkowników. Zwykle elastyczność obejmuje określony stopień złożoności z powodu liczby posiadanych opcji konfiguracji. Złożoność ma możliwość zwiększenia ryzyka związanego z błędami.

Jako administrator IT potrzebujesz rozwiązania, które zapewnia odpowiedni poziom szczegółowych informacji o działaniach w systemie, dzięki czemu można łatwo diagnozować i rozwiązywać problemy, gdy wystąpią. Diagnostyka logowania dla usługi Azure AD to przykład tego narzędzia. Użyj diagnostyki, aby przeanalizować, co się stało podczas logowania i jakie akcje można podjąć, aby rozwiązać problemy bez konieczności korzystania z pomocy technicznej firmy Microsoft.

Ten artykuł zawiera omówienie tego narzędzia i sposobu jego użycia.


## <a name="requirements"></a>Wymagania

Diagnostyka logowania jest dostępna we wszystkich wersjach usługi Azure AD.<br> Aby korzystać z tego narzędzia, musisz być administratorem globalnym w usłudze Azure AD.

## <a name="how-it-works"></a>Jak to działa

W usłudze Azure AD odpowiedź na próbę logowania nie jest tylko związana z osobami, które są używane, ale również w celu uzyskania dostępu do dzierżawy. Na przykład administrator może mieć możliwość skonfigurowania wszystkich aspektów dzierżawy, gdy użytkownik jest zalogowany z sieci firmowej. Jednak po zalogowaniu się przy użyciu tego samego konta z niezaufanej sieci może być nawet zablokowany. Ze względu na większą elastyczność, którą system musi reagować na próbę logowania, możesz zakończyć w scenariuszach, w których konieczne jest rozwiązywanie problemów z logowaniem. Diagnostyka logowania to funkcja, która analizuje dane z logowania i przedstawia określone komunikaty o tym, co się stało, oraz Zalecenia dotyczące tego, co należy zrobić, aby rozwiązać problemy. Diagnostyka logowania dla usługi Azure AD została zaprojektowana w celu umożliwienia samodiagnostyki błędów logowania. Proces diagnostyki składa się z czterech głównych bloków konstrukcyjnych:

![Proces diagnostyki logowania](./media/overview-sign-in-diagnostics/process.png)
 
1. **Zdefiniuj** zakres dla zdarzeń logowania, które Cię interesują

2. **Wybierz** zdarzenie, które chcesz przejrzeć

3. **Przegląd** diagnostyki

4. **Wykonaj** akcje

 
### <a name="define-sign-ins"></a>Definiowanie logowań

Celem tego kroku jest zdefiniowanie zakresu zdarzeń logowania, które chcesz zbadać. Zakres jest oparty na użytkowniku lub identyfikatorze (identyfikatorze korelacji) i przedziale czasu. Ponadto można również określić nazwę aplikacji. Usługa Azure AD korzysta z informacji o zakresie, aby znaleźć odpowiednie zdarzenia.  

### <a name="select-sign-in-event"></a>Wybieranie zdarzenia logowania 

Na podstawie kryteriów wyszukiwania usługa Azure AD pobiera wszystkie zgodne zdarzenia logowania i przedstawia je w widoku listy podsumowania uwierzytelniania. 

![Podsumowanie uwierzytelniania](./media/overview-sign-in-diagnostics/authentication-summary.png)
 
Można dostosować kolumny wyświetlane w tym widoku.

### <a name="review-diagnosis"></a>Przegląd diagnostyki

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

Ten scenariusz jest spowodowany przez logowanie, które zostało zablokowane przez zasady dostępu warunkowego.

![Blokowanie dostępu](./media/overview-sign-in-diagnostics/block-access.png)

Sekcja Diagnostic zawiera szczegółowe informacje dotyczące logowania użytkownika i stosowane zasady.


### <a name="failed-conditional-access"></a>Niepowodzenie dostępu warunkowego

Ten scenariusz jest zwykle wynikiem logowania, które nie powiodło się, ponieważ nie spełniono wymagań zasad dostępu warunkowego. Typowe przykłady:

![Wymagaj kontrolek](./media/overview-sign-in-diagnostics/require-controls.png)

- Wymagaj hybrydowego urządzenia dołączonego do usługi Azure AD

- Wymaganie zatwierdzonej aplikacji klienckiej

- Wymaganie zasad ochrony aplikacji   


W diagnostyce przedstawiono szczegółowe informacje na temat logowania użytkownika i zasad lub zasad, które zostały zastosowane.


### <a name="mfa-from-conditional-access"></a>Uwierzytelnianie wieloskładnikowe z dostępu warunkowego

Ten scenariusz jest spowodowany przez zasady dostępu warunkowego, które wymagają zalogowania się przy użyciu zestawu uwierzytelniania wieloskładnikowego.

![Wymagaj uwierzytelniania wieloskładnikowego](./media/overview-sign-in-diagnostics/require-mfa.png)

Sekcja Diagnostic zawiera szczegółowe informacje dotyczące logowania użytkownika i stosowane zasady.



### <a name="mfa-from-other-requirements"></a>Uwierzytelnianie wieloskładnikowe od innych wymagań

Ten scenariusz występuje, gdy uwierzytelnianie wieloskładnikowe nie zostało wymuszone przez zasady dostępu warunkowego. Na przykład skonfigurowano uwierzytelnianie wieloskładnikowe dla poszczególnych użytkowników w dzierżawie.


![Wymagaj uwierzytelniania wieloskładnikowego na użytkownika](./media/overview-sign-in-diagnostics/mfa-per-user.png)


Celem tego scenariusza diagnostycznego jest dostarczenie dodatkowych informacji na temat:

- Źródło przerwania usługi uwierzytelnianie wieloskładnikowe. 
- Wynik interakcji z klientem.

Ponadto ta sekcja zawiera również wszystkie szczegółowe informacje o próbie logowania użytkownika. 


### <a name="mfa-proof-up-required"></a>Wymagane jest potwierdzenie uwierzytelniania MFA

Ten scenariusz wynika z logowań, które zostały przerwane przez żądania konfiguracji uwierzytelniania wieloskładnikowego. Ten proces instalacji jest również znany jako "test in".

Potwierdzenie uwierzytelniania wieloskładnikowego występuje, gdy użytkownik jest wymagany do korzystania z uwierzytelniania wieloskładnikowego, ale nigdy go nie ustawił wcześniej, lub administrator skonfigurował go tak, aby wymagał ustawienia go.

Zamiarem tego scenariusza diagnostycznego jest zapewnienie wglądu w to, że przerwanie usługi uwierzytelnianie wieloskładnikowe miało na celu zapewnienie, że użytkownik ukończy proces weryfikacji.

### <a name="mfa-proof-up-required-but-user-sign-in-attempt-is-not-from-secure-location"></a>Wymagane jest potwierdzenie uwierzytelniania MFA, ale próba logowania użytkownika nie pochodzi z bezpiecznej lokalizacji

Ten scenariusz wynika z logowań, które zostały przerwane przez żądanie skonfigurowania usługi uwierzytelniania wieloskładnikowego, ale zalogowanie zostało zaobserwowane jako ryzykowne. 

Potwierdzenie uwierzytelniania wieloskładnikowego występuje, gdy użytkownik jest wymagany do korzystania z uwierzytelniania wieloskładnikowego, ale nigdy go nie ustawił wcześniej, lub administrator skonfigurował go tak, aby wymagał ustawienia go.

Celem tego scenariusza diagnostycznego jest zapewnienie wglądu w to, że przerwanie usługi uwierzytelniania wieloskładnikowego było możliwe, aby zapewnić, że użytkownik zakończy proces weryfikacji, ale tak samo nie jest w lokalizacji sieciowej, która nie jest ryzykowna. Na przykład jeśli sieć firmowa jest zdefiniowana jako nazwana lokalizacja, spróbuj wykonać próbkę z sieci firmowej.


### <a name="successful-sign-in"></a>Pomyślne logowanie

Ten scenariusz obejmuje logowanie do usługi Azure AD bez przeszkód z dostępu warunkowego lub uwierzytelniania wieloskładnikowego.

Celem tego scenariusza diagnostycznego jest zapewnienie wglądu w informacje o tym, co użytkownik podano podczas logowania, w przypadku istnienia zasad dostępu warunkowego lub zasad, które były stosowane, lub skonfigurowanego uwierzytelniania wieloskładnikowego, które było przerywane Logowanie użytkownika.



## <a name="next-steps"></a>Następne kroki

* [Co to są raporty usługi Azure Active Directory?](overview-reports.md)
* [Co to jest monitorowanie usługi Azure Active Directory?](overview-monitoring.md)
