---
title: Użytkownicy oflagowani w celu ryzyka w portalu Azure Active Directory | Microsoft Docs
description: Dowiedz się więcej o raporcie o zabezpieczeniach dotyczącym użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08305b6aec7dcd5906f4316767c4c512d3a0535e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90600504"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Rozwiązywanie problemów dotyczących użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory

Raporty dotyczące zabezpieczeń w Azure Active Directory (Azure AD) pozwalają ocenić prawdopodobieństwo naruszenia zabezpieczeń kont użytkowników w danym środowisku. Użytkownik oflagowany w związku z ryzykiem jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Firma Microsoft dokłada starań, aby pomóc zapewnić bezpieczne środowisko. W ramach tego zobowiązania firma Microsoft stale monitoruje działania, które są nietypowe lub zgodne ze znanymi wzorcami ataków. 

W przypadku wykrycia nietypowych działań, które mogą wskazywać na nieautoryzowany dostęp do niektórych kont użytkowników, zostaną wyświetlone powiadomienia, które umożliwiają podjęcie odpowiednich działań. Nie oznacza to, że systemy firmy Microsoft zostały naruszone.

## <a name="access-the-users-flagged-for-risk-report"></a>Uzyskiwanie dostępu do raportu dotyczącego użytkowników oflagowanych w związku z ryzykiem

Można sprawdzić, czy użytkownicy oflagowani w ramach tego ryzyka przez raport narażonych [użytkowników](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) w Azure Portal. Jeśli nie masz usługi Azure AD, możesz utworzyć bezpłatne konto w usłudze [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD) . 

Raport dotyczący użytkowników oflagowanych w ramach ryzyka można wykonać następujące czynności dla każdego użytkownika:

- Wygenerować hasło tymczasowe
- Wymagać od użytkownika bezpiecznego zresetowania hasła przy następnym logowaniu
- Odrzucić ryzyko związane z użytkownikiem bez podejmowania żadnej akcji korygowania.

Aby uzyskać więcej informacji, zobacz Raport dotyczący [użytkowników oflagowanych w celu zabezpieczenia](../identity-protection/overview-identity-protection.md).

### <a name="azure-ad-subscription-for-microsoft-365-customers"></a>Subskrypcja usługi Azure AD dla klientów Microsoft 365

Możesz również użyć poświadczeń Microsoft 365, aby uzyskać dostęp do **Centrum administracyjnego platformy Azure**. Po uaktywnieniu dostępu do usługi Azure AD nastąpi przekierowanie do portalu usługi Azure AD. Na poziomie subskrypcji podstawowej ilość szczegółów udostępnianych w raportach jest ograniczona. Dodatkowe dane i analizy będą dostępne dla subskrybentów platformy Azure w wersji Premium.

Aby uzyskać dostęp do raportów o **użytkowniku oflagowane w celu** zgłaszania ryzyka, w centrum administracyjnym Microsoft 365:

1.  W menu nawigacji po lewej stronie wybierz pozycję **centra administracyjne**. 
2.  Wybierz pozycję **Azure AD**.
3.  Zaloguj się do **Centrum administracyjnego usługi Azure Active Directory**.
4.  Jeśli w górnej części strony zostanie wyświetlony transparent z informacją, że **zapoznaj się z nowym portalem**, wybierz link.
4.  W menu nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**. 
5.  W okienku nawigacji wybierz pozycję **Użytkownicy oflagowani w celu uzyskania ryzyka** z sekcji **zabezpieczenia** .

## <a name="remediation-actions"></a>Akcje naprawcze

Wykonaj następujące akcje, aby pomóc usunąć zagrożenie z kont i zabezpieczyć swoje środowisko:

1.  [Sprawdź](https://aka.ms/MFAValid) poprawność informacji dotyczących uwierzytelniania wieloskładnikowego i samoobsługowego resetowania hasła. 
2.  [Włącz uwierzytelnianie wieloskładnikowe](https://aka.ms/MFAuth) dla wszystkich użytkowników. 
3.  Użyj tego [skryptu korygującego](https://aka.ms/remediate) dla każdego konta, którego dotyczy problem, aby automatycznie wykonać następujące czynności: 

    a. Zresetuj hasło, aby zabezpieczyć konto i Wykasuj aktywne sesje.

    b. Usunąć delegatów skrzynki pocztowej.

    c. Wyłączyć reguły przekazywania poczty do domen zewnętrznych.

    d. Usunąć globalną właściwość przekazywania poczty w skrzynce pocztowej.

    e. Włączyć uwierzytelnianie wieloskładnikowe na koncie użytkownika.

    f. Ustawić złożoność hasła dla konta tak, aby była wysoka.

    przykład Włączyć inspekcję skrzynki pocztowej.

    h. Utworzenie dziennika inspekcji do przejrzenia przez administratora.

4. Zbadaj dzierżawę Microsoft 365 i inną infrastrukturę IT, w tym przegląd wszystkich ustawień dzierżawy, kont użytkowników i ustawień konfiguracji poszczególnych użytkowników w celu ewentualnej modyfikacji. Sprawdź, czy wskaźniki metod trwałości oraz wskaźniki intruza mogą korzystać z początkowego stopnia w celu uzyskania poświadczeń sieci VPN lub dostępu do innych zasobów organizacji. 

5.  W ramach badania należy wziąć pod uwagę, czy należy powiadomić władze rządowe, w tym przepisy prawne.

Ponadto wykonaj następujące czynności:

- Przeczytaj i zaimplementuj te [wskazówki dotyczące rozwiązywania nietypowych działań](https://aka.ms/fixaccount). 
- [Włącz potok inspekcji](https://aka.ms/improvesecurity) , aby ułatwić Analizowanie aktywności w dzierżawie. Po zakończeniu magazyn inspekcji rozpoczyna zapełnianie przy użyciu dzienników aktywności. W tym momencie można również skorzystać z [zasobów wyszukiwania i badania Centrum zabezpieczeń i zgodności](https://aka.ms/sccsearch). 
- Przejrzyj reguły delegowania uprawnień i przekazywania poczty dla wszystkich skrzynek pocztowych. Do wykonania tego zadania możesz użyć tego [skryptu programu PowerShell](https://aka.ms/delegateforwardrules). 

## <a name="next-steps"></a>Następne kroki

* [Ochrona tożsamości w usłudze Azure Active Directory](../identity-protection/overview-identity-protection.md)
* [Użytkownicy oflagowani w związku z ryzykiem](../identity-protection/overview-identity-protection.md)