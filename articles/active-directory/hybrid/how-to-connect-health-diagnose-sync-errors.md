---
title: Azure AD Connect Health — Diagnozowanie błędów synchronizacji zduplikowanych atrybutów | Microsoft Docs
description: W tym dokumencie opisano proces diagnostyki zduplikowanych błędów synchronizacji atrybutów i potencjalną poprawkę scenariuszy oddzielonych obiektów bezpośrednio z Azure Portal.
services: active-directory
documentationcenter: ''
author: billmath
manager: maheshu
editor: billmath
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fe6af43c9ca44095c328356e8171da10717875e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728241"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnozowanie i naprawianie błędów synchronizacji zduplikowanego atrybutu

## <a name="overview"></a>Omówienie
Wykonanie jednego kroku w celu wyróżnienia błędów synchronizacji powoduje, że w programie Azure Active Directory (Azure AD) Connect Health wprowadzono korekty samoobsługowe. Rozwiązywanie problemów z duplikowaniem błędów synchronizacji atrybutów i poprawianie obiektów, które są oddzielone z usługi Azure AD.
Funkcja diagnostyki ma następujące zalety:
- Zawiera procedurę diagnostyczną, która zawęża zduplikowane błędy synchronizacji atrybutów. I zawiera określone poprawki.
- Stosuje poprawkę dla dedykowanych scenariuszy z usługi Azure AD w celu rozwiązania błędu w jednym kroku.
- Aby włączyć tę funkcję, uaktualnienie lub konfiguracja nie jest wymagana.
Aby uzyskać więcej informacji na temat usługi Azure AD, zobacz [Synchronizacja tożsamości i zduplikowanie odporności na atrybuty](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Problemy
### <a name="a-common-scenario"></a>Typowy scenariusz
Gdy wystąpią błędy synchronizacji **QuarantinedAttributeValueMustBeUnique** i **AttributeValueMustBeUnique** , często są wyświetlane adresy **userPrincipalName** lub **proxy** w usłudze Azure AD. Błędy synchronizacji można rozwiązać przez zaktualizowanie obiektu źródłowego powodującego konflikt po stronie lokalnej. Błąd synchronizacji zostanie rozwiązany po następnej synchronizacji. Na przykład ten obraz wskazuje, że dwóch użytkowników ma konflikt ich **userPrincipalName**. Oba są Jan **. J \@ contoso.com**. Obiekty powodujące konflikt są poddane kwarantannie w usłudze Azure AD.

![Diagnozuj typowy scenariusz błędu synchronizacji](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scenariusz oddzielonego obiektu
Czasami może się okazać, że istniejący użytkownik utraci **zakotwiczenie źródła**. Usuwanie obiektu źródłowego zaszło do Active Directory lokalnego. Jednak zmiana sygnału usuwania nigdy nie została zsynchronizowana z usługą Azure AD. Ta utrata odbywa się z powodów takich jak problemy z aparatem synchronizacji lub Migracja domeny. Gdy ten sam obiekt jest przywracany lub odtwarzany logicznie, istniejący użytkownik powinien być zsynchronizowany z **kotwicą źródłową**. 

Jeśli istniejący użytkownik jest obiektem opartym tylko na chmurze, można także zobaczyć, że konflikt użytkownika jest synchronizowany z usługą Azure AD. Nie można dopasować użytkownika do istniejącego obiektu. Nie ma bezpośredniego sposobu mapowania **zakotwiczenia źródła**. Zobacz więcej na temat [istniejącej bazy wiedzy](https://support.microsoft.com/help/2647098). 

Na przykład istniejący obiekt w usłudze Azure AD zachowuje licencję Jan. Nowo zsynchronizowany obiekt z inną **kotwicą źródła** występuje w zduplikowanym stanie atrybutu w usłudze Azure AD. Zmiany dla Janusza w Active Directory lokalnym nie będą stosowane do oryginalnego użytkownika (istniejącego obiektu) Janusza w usłudze Azure AD.  

![Diagnozowanie scenariusza oddzielonych obiektów z błędami synchronizacji](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Kroki diagnostyki i rozwiązywania problemów w programie Connect Health 
Funkcja Diagnozuj obsługuje obiekty użytkownika z następującymi zduplikowanymi atrybutami:

| Nazwa atrybutu | Typy błędów synchronizacji|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique lub AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique lub AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Aby można było uzyskać dostęp do tej funkcji, uprawnienia **administratora globalnego** lub uprawnienia **współautora** z usługi Azure RBAC, jest wymagana.
>

Postępuj zgodnie z instrukcjami w Azure Portal, aby zawęzić szczegóły błędu synchronizacji i udostępnić bardziej szczegółowe rozwiązania:

![Kroki diagnostyki błędu synchronizacji](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

W Azure Portal wykonaj kilka kroków, aby zidentyfikować określone scenariusze fixable:  
1.  Sprawdź kolumnę **stan diagnozy** . Stan wskazuje, czy istnieje możliwy sposób naprawienia błędu synchronizacji bezpośrednio z Azure Active Directory. Innymi słowy, istnieje przepływ rozwiązywania problemów, który może zawęzić przypadek błędu i potencjalnie rozwiązać ten problem.

| Stan | Co to oznacza? |
| ------------------ | -----------------|
| Nie uruchomiono | Ten proces diagnostyki nie został odwiedzony. W zależności od wyniku diagnostyki istnieje potencjalne rozwiązanie błędu synchronizacji bezpośrednio z portalu. |
| Wymagana ręczna Naprawa | Błąd nie spełnia kryteriów dostępnych poprawek z portalu. Konfliktowe typy obiektów nie są użytkownikami lub zostały już wykonane kroki diagnostyczne, a w portalu nie jest dostępne żadne rozwiązanie. W tym drugim przypadku poprawka ze strony lokalnej jest nadal jednym z rozwiązań. [Przeczytaj więcej na temat poprawek lokalnych](https://support.microsoft.com/help/2647098). | 
| Oczekiwanie na synchronizację | Zastosowano poprawkę. Portal czeka na następny cykl synchronizacji w celu wyczyszczenia błędu. |

  >[!IMPORTANT]
  > Kolumna stan diagnostyki zostanie zresetowana po każdym cyklu synchronizacji. 
  >

1. Wybierz przycisk **Diagnozuj** w obszarze Szczegóły błędu. Odpowiedzmy kilka pytań i określisz szczegóły błędu synchronizacji. Odpowiedzi na pytania ułatwiają zidentyfikowanie oddzielonego przypadku obiektu.

1. Jeśli na końcu diagnostyki zostanie wyświetlony przycisk **Zamknij** , nie jest dostępna szybka poprawka z poziomu portalu w zależności od odpowiedzi. Zapoznaj się z rozwiązaniem przedstawionym w ostatnim kroku. Poprawki w środowisku lokalnym są nadal rozwiązaniami. Wybierz przycisk **Zamknij** . Stan bieżącego błędu synchronizacji jest **wymagany do ręcznego naprawienia**. Stan pozostaje w trakcie bieżącego cyklu synchronizacji.

1. Po zidentyfikowaniu oddzielonego przypadku obiektu można naprawić zduplikowane atrybuty błędy synchronizacji bezpośrednio w portalu. Aby wyzwolić ten proces, wybierz przycisk **Zastosuj poprawkę** . Stan bieżących aktualizacji błędów synchronizacji na **oczekujące synchronizację**.

1. Po kolejnym cyklu synchronizacji błąd powinien zostać usunięty z listy.

## <a name="how-to-answer-the-diagnosis-questions"></a>Jak odpowiedzieć na pytania diagnostyczne 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Czy użytkownik istnieje w Active Directory lokalnym?

To pytanie próbuje zidentyfikować obiekt źródłowy istniejącego użytkownika na podstawie Active Directory lokalnego.  
1. Sprawdź, czy Azure Active Directory ma obiekt z podanym elementem **userPrincipalName**. Jeśli nie, odpowiedź **nie**.
2. Jeśli tak jest, sprawdź, czy obiekt nadal jest w zasięgu do synchronizowania.  
   - Wyszukaj w obszarze łącznika usługi Azure AD przy użyciu nazwy wyróżniającej.
   - Jeśli obiekt znajduje się w stanie **oczekiwania na dodanie** , odpowiedź **nie**. Azure AD Connect nie może połączyć obiektu z prawidłowym obiektem usługi Azure AD.
   - Jeśli nie odnaleziono obiektu, Odpowiedz **tak**.

W tych przykładach Pytanie próbuje sprawdzić, czy w Active Directory lokalnym istnieje niezależna **metoda Jacksona** .
W przypadku **typowego scenariusza** zarówno użytkownicy Jan **Johnsonem** , jak i Jan **jacksona** są obecni w Active Directory lokalnych. Obiekty poddane kwarantannie są dwoma różnymi użytkownikami.

![Diagnozuj typowy scenariusz błędu synchronizacji](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

W przypadku **scenariusza oddzielonego obiektu** tylko pojedynczy użytkownik Jan **Johnsonem** jest obecny w Active Directory lokalnym:

![Diagnozuj oddzielony obiekt o błędzie synchronizacji * czy użytkownik istnieje * scenariusz](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Czy oba te konta należą do tego samego użytkownika?
To pytanie sprawdza przychodzącego użytkownika wywołującego konflikt i istniejącego obiektu użytkownika w usłudze Azure AD, aby sprawdzić, czy należą do tego samego użytkownika.  
1. Obiekt powodujący konflikt jest nowo synchronizowany do Azure Active Directory. Porównaj atrybuty obiektów:  
   - Nazwa wyświetlana
   - Nazwa główna użytkownika
   - Identyfikator obiektu
2. Jeśli nie będzie można porównać usługi Azure AD, sprawdź, czy Active Directory zawierają obiekty o podanych elementach **userPrincipalName**. Odpowiedź **nie** w przypadku znalezienia obu tych wartości.

W poniższym przykładzie dwa obiekty należą do tego samego użytkownika Jan **Johnsonem**.

![Diagnozuj oddzielony obiekt o błędach synchronizacji * ten sam użytkownik * scenariusz](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Co się stanie po zastosowaniu poprawki w scenariuszu oddzielonego obiektu
Na podstawie odpowiedzi na powyższe pytania zobaczysz przycisk **Zastosuj poprawkę** , gdy jest dostępna poprawka z usługi Azure AD. W takim przypadku obiekt lokalny jest synchronizowany z nieoczekiwanym obiektem usługi Azure AD. Dwa obiekty są mapowane przy użyciu **kotwicy źródłowej**. Zmiana **zastosowania poprawki** obejmuje następujące kroki:
1. Aktualizuje **kotwicę źródłową** do poprawnego obiektu w usłudze Azure AD.
2. Usuwa obiekt powodujący konflikt w usłudze Azure AD, jeśli jest obecny.

![Diagnozuj błąd synchronizacji po naprawie](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> Zmiana **poprawki** dotyczy tylko przypadków oddzielonych obiektów.
>

Po wykonaniu powyższych kroków użytkownik może uzyskać dostęp do oryginalnego zasobu, który jest łączem do istniejącego obiektu. Wartość **stan diagnozy** w widoku listy umożliwia aktualizowanie **oczekujących synchronizacji**. Błąd synchronizacji zostanie rozwiązany po następnej synchronizacji. Program Connect Health nie będzie już pokazywał rozwiązanego błędu synchronizacji w widoku listy.

## <a name="failures-and-error-messages"></a>Błędy i komunikaty o błędach
**Użytkownik z atrybutem powodującym konflikt jest usuwany w Azure Active Directory. Upewnij się, że użytkownik jest trwale usunięty przed ponowną próbą.**  
Użytkownik z atrybutem powodującym konflikt w usłudze Azure AD powinien zostać oczyszczony, aby można było zastosować poprawkę. Zapoznaj [się z tematem jak trwale usunąć użytkownika w usłudze Azure AD](../fundamentals/active-directory-users-restore.md) przed ponowną próbą naprawienia. Użytkownik zostanie również automatycznie usunięty trwale po upływie 30 dni w stanie nietrwałego usunięcia. 

**Aktualizowanie zakotwiczenia źródła do użytkownika opartego na chmurze w dzierżawie nie jest obsługiwane.**  
Użytkownik oparty na chmurze w usłudze Azure AD nie powinien mieć kotwicy źródłowej. Aktualizacja kotwicy źródła nie jest obsługiwana w tym przypadku. Poprawka ręczna jest wymagana w środowisku lokalnym. 

**Zaktualizowanie wartości przez proces naprawy nie powiodło się.**
Określone ustawienia, takie jak [UserWriteback w Azure AD Connect](./how-to-connect-preview.md#user-writeback) nie są obsługiwane. Wyłącz ustawienia. 

## <a name="faq"></a>Często zadawane pytania
**PYTANIE:** Co się stanie w przypadku niepowodzenia wykonania **zastosowania poprawki** ?  
**Z.** Jeśli wykonanie nie powiedzie się, istnieje możliwość, że Azure AD Connect jest uruchomiony błąd eksportu. Odśwież stronę portalu i ponów próbę po następnej synchronizacji. Domyślny cykl synchronizacji to 30 minut. 


**Pytania.** Co zrobić, jeśli **istniejący obiekt** powinien być usunięty?  
**Z.** Jeśli **istniejący obiekt** powinien zostać usunięty, proces nie obejmuje zmiany **kotwicy źródłowej**. Zazwyczaj można rozwiązać ten problem z poziomu Active Directory lokalnego. 


**Pytania.** Jakie uprawnienie musi wykonać użytkownik, aby zastosować poprawkę?  
**Z.** **Administrator globalny** lub **współautor** z usługi Azure RBAC ma uprawnienia dostępu do procesu diagnostyki i rozwiązywania problemów.


**Pytania.** Czy muszę skonfigurować Azure AD Connect lub zaktualizować agenta Azure AD Connect Health dla tej funkcji?  
**Z.** Nie, proces diagnostyki to kompletna funkcja oparta na chmurze.


**Pytania.** Jeśli istniejący obiekt jest usunięty z nietrwałego, czy proces diagnostyki ponownie zmieni obiekt?  
**Z.** Nie, poprawka nie aktualizuje atrybutów obiektu innych niż **kotwice źródłowe**.