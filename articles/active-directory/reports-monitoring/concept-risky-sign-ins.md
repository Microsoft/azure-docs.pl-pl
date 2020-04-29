---
title: Raport dotyczący ryzykownych logowań w portalu | Microsoft Docs
description: Dowiedz się więcej o raporcie dotyczącym ryzykownych logowań w portalu usługi Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77486064139895799ac5a48327377154f75da6d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78273834"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Raport dotyczący ryzykownych logowań w portalu usługi Azure Active Directory

Azure Active Directory (Azure AD) wykrywa podejrzane akcje powiązane z kontami użytkowników. Dla każdej wykrytej akcji tworzony jest rekord nazywany **wykryciem ryzyka** . Aby uzyskać więcej informacji, zobacz [wykrywanie ryzyka w usłudze Azure AD](concept-risk-events.md). 

Dostęp do raportów zabezpieczeń można uzyskać z poziomu witryny [Azure Portal](https://portal.azure.com), wybierając blok **Azure Active Directory**, a następnie przechodząc do sekcji **Zabezpieczenia**. 

Istnieją dwa różne raporty dotyczące zabezpieczeń, które są obliczane na podstawie wykrycia ryzyka:

- **Ryzykowne logowania** — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.

- **Użytkownicy oflagowani w związku z ryzykiem** — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/10.png)

Aby dowiedzieć się, jak skonfigurować zasady wyzwalające te wykrycia ryzyka, zobacz [jak skonfigurować zasady ryzyka dla użytkowników](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Kto może uzyskać dostęp do raportu dotyczącego ryzykownych logowań?

Raporty dotyczące ryzykownych logowań są dostępne dla użytkowników w następujących rolach:

- Administrator zabezpieczeń
- Administrator globalny
- Czytelnik zabezpieczeń

Aby dowiedzieć się, jak przypisać role administracyjne do użytkownika w Azure Active Directory, zobacz [Wyświetlanie i przypisywanie ról administratorów w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do raportu zabezpieczeń?  

Wszystkie wersje usługi Azure AD zapewniają ryzykowne raporty logowania. Jednak poziom szczegółowości raportu zależy od wersji: 

- W **wersji Azure Active Directory — wersja bezpłatna**otrzymujesz listę ryzykownych logowań. 

- Ponadto wersja **Azure Active Directory — wersja Premium 1** umożliwia sprawdzenie niektórych podstawowych wykryć ryzyka, które zostały wykryte dla każdego raportu. 

- Wersja **Azure Active Directory — wersja Premium 2** zapewnia najbardziej szczegółowe informacje na temat wszystkich wykrytych zagrożeń, a także umożliwia konfigurowanie zasad zabezpieczeń, które automatycznie reagują na skonfigurowane poziomy ryzyka.

## <a name="risky-sign-ins-report-for-azure-ad-free-edition"></a>Raport dotyczący ryzykownych logowań dla bezpłatnej wersji usługi Azure AD

Wersja Bezpłatna usługi Azure AD udostępnia listę ryzykownych logowań wykrytych dla użytkowników. Każdy rekord zawiera następujące atrybuty:

- **User** — nazwa użytkownika, która była używana podczas operacji logowania.
- **IP** — adres IP urządzenia, który został użyty do nawiązania połączenia z Azure Active Directory.
- **Lokalizacja** — lokalizacja użyta do nawiązania połączenia z Azure Active Directory. Jest to najlepszy sposób zbliżania się na podstawie śladów, danych rejestru, wstecznego wyszukiwania i innych informacji.
- **Godzina logowania** — godzina, o której przeprowadzono logowanie
- **Stan** — stan logowania

![Ryzykowne logowania](./media/concept-risky-sign-ins/01.png)

W oparciu o badanie ryzykownego logowania można przekazać informacje zwrotne do usługi Azure AD, wykonując następujące czynności:

- Rozwiąż
- Oznacz jako wynik fałszywie dodatni
- Zignoruj
- Uaktywnij ponownie

![Ryzykowne logowania](./media/concept-risky-sign-ins/21.png)

Ten raport udostępnia również opcję:

- Wyszukiwania zasobów
- Pobierania danych raportu

![Ryzykowne logowania](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Raport dotyczący ryzykownych logowań dla wersji Premium usługi Azure AD

Raport dotyczący ryzykownych logowań w wersjach Premium usługi Azure AD oferuje następujące informacje:

- Zagregowane informacje na temat [typów wykrywania ryzyka](concept-risk-events.md) , które zostały wykryte. W przypadku **wersji Azure AD — wersja Premium P1**wykryte wykrywania, które nie są objęte licencją, są wyświetlane w przypadku wykrycia ryzyka podczas **logowania przy użyciu dodatkowego ryzyka**. Za pomocą **Azure AD — wersja Premium P2**można uzyskać najbardziej szczegółowe informacje na temat wszystkich podstawowych wykryć.

- Opcja pobrania raportu

![Ryzykowne logowania](./media/concept-risky-sign-ins/456.png)

Po wybraniu wykrywania ryzyka zostanie wyświetlony szczegółowy widok raportu dla tego wykrywania ryzyka, który umożliwia:

- Skonfigurowanie [zasad podejmowania działań naprawczych dotyczących ryzyka związanego z użytkownikiem](../identity-protection/howto-user-risk-policy.md).  

- Przejrzyj oś czasu wykrywania w celu wykrycia ryzyka  

- Przejrzyj listę użytkowników, dla których wykryto to wykrywanie ryzyka

- Ręczne zamykanie wykrywania ryzyka. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Czasami może się okazać, że wykrywanie ryzyka nie ma odpowiedniego wpisu logowania w [raporcie logowania](concept-sign-ins.md). Wynika to z faktu, że usługa Identity Protection szacuje ryzyko dla logowania **interaktywnego** i **nieinterakcyjnego** , podczas gdy raport logowania zawiera tylko interakcyjne logowania.

Po wybraniu użytkownika jest dla niego wyświetlany szczegółowy widok raportu, który umożliwia wykonanie następujących czynności:

- Otwieranie widoku wszystkich logowań.

- Resetowanie hasła użytkownika.

- Odrzucanie wszystkich zdarzeń.

- Zbadaj zgłoszone wykrywania ryzyka dla użytkownika. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/324.png)

Aby zbadać wykrywanie ryzyka, wybierz jeden z listy.  
Spowoduje to otwarcie bloku **szczegóły** tego wykrywania ryzyka. W bloku **szczegóły** jest dostępna opcja ręcznego zamknięcia wykrywania ryzyka lub ponownego aktywowania ręcznie zamkniętego wykrywania ryzyka. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Następne kroki

- [Sposób konfigurowania zasad ryzyka użytkowników](../identity-protection/howto-user-risk-policy.md)
- [Sposób konfigurowania zasad podejmowania działań naprawczych dotyczących ryzyka](../identity-protection/howto-user-risk-policy.md)
- [Typy wykrywania ryzyka](concept-risk-events.md)
