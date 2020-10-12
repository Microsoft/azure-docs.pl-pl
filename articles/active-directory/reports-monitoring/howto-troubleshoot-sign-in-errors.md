---
title: Jak rozwiązywać problemy z raportami o błędach logowania | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy związane z logowaniem przy użyciu raportów Azure Active Directory w Azure Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de9f5a21f38f741dcf65d285446d9482716d4f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85608130"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Instrukcje: Rozwiązywanie problemów z błędami logowania przy użyciu raportów Azure Active Directory

[Raport logowania](concept-sign-ins.md) w Azure Active Directory (Azure AD) umożliwia znalezienie odpowiedzi na pytania dotyczące zarządzania dostępem do aplikacji w organizacji, w tym:

- Co to jest wzorzec logowania użytkownika?
- Ilu użytkowników zalogowało się w ciągu tygodnia?
- Jaki jest stan tych logowań?


Ponadto raport logowania może również pomóc w rozwiązywaniu problemów z błędami logowania użytkowników w organizacji. W tym przewodniku dowiesz się, jak wyizolować błąd logowania w raporcie logowania i użyć go do zrozumienia głównej przyczyny błędu.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy:

* Dzierżawa usługi Azure AD z licencją premium (P1/P2). Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md).
* Użytkownik będący **administratorem globalnym**, **administratorem zabezpieczeń**, **czytelnikiem zabezpieczeń**lub rolą **czytelnika raportu** dla dzierżawy. Ponadto każdy użytkownik może uzyskiwać dostęp do informacji o swoim logowaniu. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Rozwiązywanie problemów z błędami logowania przy użyciu raportu logowania

1. Przejdź do witryny [Azure Portal](https://portal.azure.com) i wybierz swój katalog.
2. Wybierz pozycję **Azure Active Directory**, a następnie pozycję **Logowania** w sekcji **Monitorowanie**. 
3. Użyj dostarczonych filtrów, aby zawęzić awarię o nazwę użytkownika lub identyfikator obiektu, nazwę aplikacji lub datę. Ponadto wybierz opcję **Niepowodzenie** z listy rozwijanej **stan** , aby wyświetlić tylko nieudane logowania. 

    ![Filtrowanie wyników](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Zidentyfikuj nieudane logowanie, które chcesz zbadać. Wybierz go, aby otworzyć okno dodatkowe szczegóły z więcej informacji na temat nieudanego logowania. Zanotuj **Kod błędu logowania** i **przyczynę niepowodzenia**. 

    ![Wybierz rekord](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Te informacje można również znaleźć na karcie **Rozwiązywanie problemów i obsługa** w oknie Szczegóły.

    ![Rozwiązywanie problemów i pomoc techniczna](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. Przyczyna niepowodzenia dotyczy błędu. Na przykład w powyższym scenariuszu przyczyna niepowodzenia to **Nieprawidłowa nazwa użytkownika lub hasło albo nieprawidłowa lokalna nazwa użytkownika lub hasło**. Poprawka polega na ponownym zalogowaniu się przy użyciu prawidłowej nazwy użytkownika i hasła.

7. Możesz uzyskać dodatkowe informacje, w tym pomysły dotyczące korygowania, wyszukując kod błędu, **50126** w tym przykładzie [Informacje o kodach błędów logowania](reference-sign-ins-error-codes.md). 

8. Jeśli wszystkie inne osoby zakończą się niepowodzeniem lub problem będzie się powtarzał pomimo podjęcia zalecanego działania, [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) zgodnie z instrukcjami na karcie **Rozwiązywanie problemów i pomoc techniczna** . 

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja kodów błędów logowania](reference-sign-ins-error-codes.md)
* [Przegląd raportów logowania](concept-sign-ins.md)
