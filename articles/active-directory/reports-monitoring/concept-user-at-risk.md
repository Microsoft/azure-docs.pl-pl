---
title: Raport o zabezpieczeniach dotyczący użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory | Microsoft Docs
description: Dowiedz się więcej o raporcie o zabezpieczeniach dotyczącym użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 894d8dfb7f870ec4a2a11f1d75ee0376b25d8c7f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74014446"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Użytkownicy oflagowani w związku z raportem dotyczącym narażonych użytkowników w witrynie Azure Portal

Usługa Azure Active Directory (Azure AD) wykrywa podejrzane akcje powiązane z kontami użytkowników. Dla każdej wykrytej akcji tworzony jest rekord nazywany [wykryciem ryzyka](concept-risk-events.md) .

Dostęp do raportów zabezpieczeń można uzyskać z poziomu witryny [Azure Portal](https://portal.azure.com), wybierając blok **Azure Active Directory**, a następnie przechodząc do sekcji **Zabezpieczenia**. 

Wykryte wykrywania ryzyka są używane do obliczania:

- **Ryzykowne logowania** — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika. 

- **Użytkownicy oflagowani w związku z ryzykiem** — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone. 

Aby dowiedzieć się, jak skonfigurować zasady wyzwalające te wykrycia ryzyka, zobacz [jak skonfigurować zasady ryzyka dla użytkowników](../identity-protection/howto-user-risk-policy.md). 

![Ryzykowne logowania](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do raportu dotyczącego narażonych użytkowników?  

Wszystkie wersje usługi Azure Active Directory zapewniają dostęp do raportów użytkowników oflagowanych w związku z ryzykiem. Jednak poziom szczegółowości raportu zależy od wersji: 

- W **usłudze Azure Active Directory w wersji Bezpłatna i Podstawowa** masz dostęp do listy użytkowników oflagowanych w związku z ryzykiem. 

- Ponadto wersja **Azure Active Directory — wersja Premium 1** umożliwia sprawdzenie niektórych podstawowych wykryć ryzyka, które zostały wykryte dla każdego raportu. 

- Wersja **Azure Active Directory — wersja Premium 2** zapewnia najbardziej szczegółowe informacje na temat wszystkich wykrytych zagrożeń, a także umożliwia konfigurowanie zasad zabezpieczeń, które automatycznie reagują na skonfigurowane poziomy ryzyka.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Raport dotyczący narażonych użytkowników dla usługi Azure AD w wersji Bezpłatna i Podstawowa

Raport dotyczący użytkowników oflagowanych w związku z ryzykiem w usłudze Azure AD w wersji Bezpłatna i Podstawowa zapewnia listę kont użytkowników, których bezpieczeństwo mogło zostać naruszone. 

![Ryzykowne logowania](./media/concept-user-at-risk/03.png)

Wybranie użytkownika powoduje podanie informacji logowania. W przypadku narażonego użytkownika można przejrzeć jego historię logowania i w razie potrzeby zresetować hasło.

To okno dialogowe oferuje opcję:

- Pobierania raportu
- Wyszukiwania użytkowników

    ![Ryzykowne logowania](./media/concept-user-at-risk/16.png)

Do uzyskania szczegółowych informacji jest wymagana licencja wersji Premium.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Raport dotyczący narażonych użytkowników dla usługi Azure AD w wersjach Premium

Raport dotyczący użytkowników oflagowanych w związku z ryzykiem w usłudze Azure AD w wersjach Premium zawiera następujące elementy:

- Lista kont użytkowników, których bezpieczeństwo mogło zostać naruszone 

- Zagregowane informacje o wykrytych [typach wykrycia ryzyka](concept-risk-events.md)

- Opcja pobrania raportu

- Skonfigurowanie [zasad podejmowania działań naprawczych dotyczących ryzyka związanego z użytkownikiem](../identity-protection/howto-user-risk-policy.md).  

![Ryzykowne logowania](./media/concept-user-at-risk/71.png)

Po wybraniu użytkownika jest dla niego wyświetlany szczegółowy widok raportu, który umożliwia wykonanie następujących czynności:

- Otwieranie widoku wszystkich logowań.

- Resetowanie hasła użytkownika.

- Odrzucanie wszystkich zdarzeń.

- Zbadaj zgłoszone wykrywania ryzyka dla użytkownika. 

![Ryzykowne logowania](./media/concept-user-at-risk/324.png)

Aby zbadać wykrywanie ryzyka, wybierz jeden z listy, aby otworzyć blok **szczegóły** tego wykrywania ryzyka. W bloku **szczegóły** jest dostępna opcja ręcznego zamknięcia wykrywania ryzyka lub ponownego aktywowania ręcznie zamkniętego wykrywania ryzyka. 

![Ryzykowne logowania](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Następne kroki

- [Sposób konfigurowania zasad ryzyka użytkowników](../identity-protection/howto-user-risk-policy.md)
- [Sposób konfigurowania zasad podejmowania działań naprawczych dotyczących ryzyka](../identity-protection/howto-user-risk-policy.md)
- [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)

