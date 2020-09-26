---
title: 'Uwierzytelnianie przekazywane przez usługę Azure AD: historia wersji | Microsoft Docs'
description: W tym artykule wymieniono wszystkie wersje agenta uwierzytelniania przekazywanego przez usługę Azure AD
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 854931273ffebd653615a44dbfa9ce1b846655c2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333430"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Agent uwierzytelniania Pass-through usługi Azure AD: historia wersji 
 
Agenci zainstalowani lokalnie, które włączają uwierzytelnianie przekazywane, są regularnie aktualizowane, aby zapewnić nowe możliwości. W tym artykule wymieniono wersje i funkcje, które są dodawane po wprowadzeniu nowych funkcji. Agenci uwierzytelniania przekazywanego są automatycznie aktualizowani po wydaniu nowej wersji. 

Poniżej przedstawiono tematy pokrewne: 

- [Logowanie użytkownika przy użyciu uwierzytelniania przekazywanego za pomocą usługi Azure AD](how-to-connect-pta.md) 
- [Instalacja agenta uwierzytelniania przekazywanego przez usługę Azure AD](how-to-connect-pta-quick-start.md) 

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>Stan wydania: 
04/09/2020: wydano do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

- Dodano obsługę określania docelowych środowisk w chmurze podczas instalacji. Pakiet może być przypięty do danego środowiska chmury.



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Stan wydania 
1/22/2019: wydano do pobrania  
### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 
- Dodano obsługę Service Bus niezawodnych kanałów, aby dodać kolejną warstwę odporności połączeń dla połączeń wychodzących 
- Wymuś protokół TLS 1,2 podczas rejestracji agenta 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Stan wydania 
4/10/2018: wydano do pobrania  
### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 
- Obsługa połączenia z gniazdem internetowym 
- Ustaw protokół TLS 1,2 jako domyślny dla agenta 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Stan wydania 
1/31/2018: wydano do pobrania  
### <a name="fixed-issues"></a>Naprawione problemy 
- Naprawiono usterkę, która spowodowała wystąpienie przecieków pamięci w agencie. 
- Zaktualizowano wersję Azure Service Bus, która obejmuje rozwiązywanie problemów z przekroczeniem limitu czasu łącznika. 
### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 
- Dodano obsługę połączeń opartych na protokole WebSocket między agentem a usługami Azure AD w celu zwiększenia odporności połączenia

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Stan wydania 
11/25/2017: wydano do pobrania  
### <a name="fixed-issues"></a>Naprawione problemy 
- Naprawione usterki związane z pamięcią podręczną DNS dla domyślnych scenariuszy serwera proxy 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Stan wydania 
10/17/2017: wydano do pobrania  
### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 
- Dodano funkcję pamięci podręcznej DNS dla połączeń wychodzących w celu dodania odporności z błędów DNS 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Stan wydania 
08/31/2017: wydano do pobrania  
### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 
- Wersja GA agenta uwierzytelniania przekazującego usługi Azure AD 

## <a name="next-steps"></a>Następne kroki

- [Logowanie użytkownika przy użyciu uwierzytelniania przekazywanego usługi Azure Active Directory](how-to-connect-pta.md)
