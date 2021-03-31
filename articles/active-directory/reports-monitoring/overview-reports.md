---
title: Co to są raporty usługi Azure Active Directory? | Microsoft Docs
description: Zawiera ogólne omówienie raportów usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/30/2020
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f9a51c10a4f390e5627bccf35ab5dc74689e9c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91566826"
---
# <a name="what-are-azure-active-directory-reports"></a>Co to są raporty usługi Azure Active Directory?

Raporty usługi Azure Active Directory (Azure AD) zapewniają kompleksowy wgląd w działania w Twoim środowisku. Na podstawie udostępnionych danych można:

- Określać, jak usługi i aplikacje są wykorzystywane przez użytkowników
- Wykrywać potencjalne zagrożenia wpływające na kondycję środowiska
- Rozwiązywać problemy uniemożliwiające użytkownikom wykonywanie pracy  

Architektura raportów opiera się na dwóch głównych filarach:

- [Raporty dotyczące zabezpieczeń](#security-reports)
- [Raporty dotyczące działań](#activity-reports)

![Raportowanie](./media/overview-reports/01.png)


## <a name="security-reports"></a>Raporty dotyczące zabezpieczeń

Raporty dotyczące zabezpieczeń pomagają chronić tożsamości w organizacji. Istnieją dwa typy raportów dotyczących zabezpieczeń:

- **Użytkownicy oflagowani w związku z ryzykiem** — [raport zabezpieczeń dotyczący użytkowników oflagowanych w związku z ryzykiem](../identity-protection/overview-identity-protection.md) zawiera omówienie kont użytkowników, których zabezpieczenia mogły zostać naruszone.

- **Ryzykowne logowania** — [raport zabezpieczeń dotyczący ryzykownych logowań](../identity-protection/overview-identity-protection.md) jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika. 

### <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do raportu zabezpieczeń?  

Wszystkie wersje usługi Azure AD zapewniają dostęp do raportów użytkowników oflagowanych w związku z ryzykiem oraz ryzykownych logowań. Jednak poziom szczegółowości raportu zależy od wersji: 

- W **usłudze Azure Active Directory w wersji Bezpłatna i Podstawowa** masz dostęp do listy użytkowników oflagowanych w związku z ryzykiem i ryzykownych logowań. 

- Wersja **Azure Active Directory — wersja Premium 1** rozszerza ten model, umożliwiając także sprawdzenie niektórych podstawowych wykryć ryzyka, które zostały wykryte dla każdego raportu. 

- Wersja **Azure Active Directory — wersja Premium 2** zapewnia najbardziej szczegółowe informacje na temat podstawowych wykryć ryzyka, a także umożliwia konfigurowanie zasad zabezpieczeń, które automatycznie reagują na skonfigurowane poziomy ryzyka.


## <a name="activity-reports"></a>Raporty dotyczące działań

Raporty dotyczące działań ułatwiają zrozumienie zachowania użytkowników w organizacji. W usłudze Azure AD istnieją dwa typy raportów dotyczących działań:

- **Dzienniki inspekcji** — [raport działań dotyczący dzienników inspekcji](concept-audit-logs.md) zapewnia dostęp do historii wszystkich zadań wykonanych w dzierżawie.

- **Logowania** — przy użyciu [raportu działań dotyczącego logowań](concept-sign-ins.md) można określić, kto wykonał zadania zgłoszone w raporcie dzienników inspekcji.



> [!VIDEO https://www.youtube.com/embed/ACVpH6C_NL8]




### <a name="audit-logs-report"></a>Raport dotyczący dzienników inspekcji 

[Raport dzienników inspekcji](concept-audit-logs.md) dostarcza informacji na temat aktywności systemu pod kątem zgodności. Te dane pozwalają reagować na typowe scenariusze, takie jak:

- Ktoś w mojej dzierżawie uzyskał dostęp do grupy administratorów. Kto udzielił tej osobie prawa dostępu? 

- Chcę zobaczyć listę użytkowników, którzy logowali się do określonej aplikacji od momentu jej dołączenia oraz sprawdzić, jak działa aplikacja

- Chcę wiedzieć, ile operacji resetowania hasła jest wykonywanych w mojej dzierżawie


#### <a name="what-azure-ad-license-do-you-need-to-access-the-audit-logs-report"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do raportu dzienników inspekcji?  

Raport dzienników inspekcji jest dostępny w przypadku funkcji, do których masz licencje. Jeśli masz licencję określonej funkcji, masz również dostęp do informacji z dotyczącego jej dziennika inspekcji. Na [stronie cennika Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)można zobaczyć porównanie funkcji deatiled zgodnie z [różnymi typami licencji](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses) . Aby uzyskać więcej informacji, zobacz [Funkcje i możliwości usługi Azure Active Directory](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad).

### <a name="sign-ins-report"></a>Raport dotyczący logowań

[Raport dotyczący logowań](concept-sign-ins.md) zawiera odpowiedzi na pytania, takie jak:

- Co to jest wzorzec logowania użytkownika?
- Ilu użytkowników zalogowało się w ciągu tygodnia?
- Jaki jest stan tych logowań?

#### <a name="what-azure-ad-license-do-you-need-to-access-the-sign-ins-activity-report"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do raportu działań związanych z logowaniem?  

Aby uzyskać dostęp do raportu działań związanych z logowaniem, dzierżawa musi mieć skojarzoną licencję usługi Azure AD w wersji Premium.

## <a name="programmatic-access"></a>Dostęp programowy

Oprócz interfejsu użytkownika usługa Azure AD zapewnia [dostęp programowy](concept-reporting-api.md) do danych raportów za pomocą zestawu interfejsów API opartych na protokole REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania. 

## <a name="next-steps"></a>Następne kroki

- [Raport dotyczący ryzykownych logowań](../identity-protection/overview-identity-protection.md)
- [Raport dotyczący dzienników inspekcji](concept-audit-logs.md)
- [Raport dotyczący dzienników logowania](concept-sign-ins.md)