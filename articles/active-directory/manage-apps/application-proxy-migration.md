---
title: Uaktualnianie do usługi Azure serwer proxy aplikacji usługi Azure AD | Microsoft Docs
description: Wybierz, które rozwiązanie proxy jest najlepsze w przypadku uaktualniania z programu Microsoft Forefront lub ujednoliconej bramy dostępu.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cccabaf069a3027e615892e36e218f865a6c983a
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706656"
---
# <a name="compare-remote-access-solutions"></a>Porównywanie rozwiązań dostępu zdalnego

Serwer proxy aplikacji usługi Azure Active Directory jest jednym z dwóch rozwiązań dostępu zdalnego oferowanych przez firmę Microsoft. Druga to serwer proxy aplikacji sieci Web, wersja lokalna. Te dwa rozwiązania zastępują wcześniejsze produkty oferowane przez firmę Microsoft: Microsoft Forefront Threat Management Gateway (TMG) i Unified Access Gateway (UAG). Skorzystaj z tego artykułu, aby zrozumieć, jak te cztery rozwiązania porównują ze sobą. Aby nadal korzystać z przestarzałych rozwiązań TMG lub UAG, Skorzystaj z tego artykułu w celu zaplanowania migracji do jednego z serwerów proxy aplikacji. 


## <a name="feature-comparison"></a>Porównanie funkcji

Skorzystaj z tej tabeli, aby zrozumieć, jak Brama zarządzania zagrożeniami (TMG), Unified Access Gateway (UAG), serwer proxy aplikacji sieci Web (WAP) i usługa Azure serwer proxy aplikacji usługi Azure AD (AP) porównują się ze sobą.

| Cechy | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Uwierzytelnianie certyfikatu | Yes | Yes | - | - |
| Selektywne publikowanie aplikacji przeglądarki | Yes | Yes | Yes | Yes |
| Wstępne uwierzytelnianie i logowanie jednokrotne | Yes | Yes | Yes | Yes | 
| Zapora warstwy 2/3 | Yes | Yes | - | - |
| Przekazywanie możliwości serwera proxy | Yes | - | - | - |
| Możliwości sieci VPN | Yes | Yes | - | - |
| Obsługa zaawansowanych protokołów | - | Yes | Tak, w przypadku uruchamiania za pośrednictwem protokołu HTTP | Tak, w przypadku uruchamiania za pośrednictwem protokołu HTTP lub za pośrednictwem bramy Pulpit zdalny |
| Służy jako serwer proxy usług ADFS | - | Yes | Yes | - |
| Jeden portal na potrzeby dostępu do aplikacji | - | Yes | - | Yes |
| Tłumaczenie łącza treści odpowiedzi | Yes | Yes | - | Yes | 
| Uwierzytelnianie z nagłówkami | - | Yes | - | Tak, z PingAccess | 
| Zabezpieczenia w skali chmury | - | - | - | Yes | 
| Dostęp warunkowy | - | Yes | - | Yes |
| Brak składników w strefie zdemilitaryzowana (DMZ) | - | - | - | Yes |
| Brak połączeń przychodzących | - | - | - | Yes |

W przypadku większości scenariuszy zalecamy korzystanie z platformy Azure serwer proxy aplikacji usługi Azure AD jako nowoczesnego rozwiązania. Serwer proxy aplikacji sieci Web jest preferowany tylko w scenariuszach, które wymagają serwera proxy dla AD FS i nie można używać domen niestandardowych w programie Azure Active Directory. 

Usługa Azure serwer proxy aplikacji usługi Azure AD oferuje unikatowe korzyści w porównaniu do podobnych produktów, w tym:

- Rozszerzanie usługi Azure AD do zasobów lokalnych
   - Zabezpieczenia i ochrona w skali chmury
   - Funkcje, takie jak dostęp warunkowy i Multi-Factor Authentication, można łatwo włączyć
- Brak składników w strefie zdemilitaryzowana
- Nie są wymagane żadne połączenia przychodzące
- Jedna Strona Moje aplikacje, do której użytkownicy mogą przejść dla wszystkich swoich aplikacji, w tym Microsoft 365, zintegrowane aplikacje SaaS usługi Azure AD i lokalne aplikacje sieci Web. 


## <a name="next-steps"></a>Następne kroki

- [Korzystanie z aplikacji usługi Azure AD w celu zapewnienia bezpiecznego dostępu zdalnego do aplikacji lokalnych](application-proxy.md)
