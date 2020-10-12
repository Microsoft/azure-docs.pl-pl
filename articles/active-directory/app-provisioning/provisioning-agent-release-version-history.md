---
title: 'Azure AD Connect agenta aprowizacji: historia wersji | Microsoft Docs'
description: W tym artykule wymieniono wszystkie wersje agenta aprowizacji Azure AD Connect i opisano nowe funkcje oraz naprawione problemy
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: 9e05d1a85f17800ddf4d77e4e4acba6396a8da47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84781569"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect agenta aprowizacji: historia wersji

W tym artykule wymieniono wersje i funkcje agenta aprowizacji Azure Active Directory Connect, który został wystawiony. Zespół usługi Azure AD regularnie aktualizuje agenta aprowizacji za pomocą nowych funkcji i funkcjonalności. Agent aprowizacji jest automatycznie aktualizowany po wydaniu nowej wersji. 

Firma Microsoft zapewnia bezpośrednią pomoc techniczną dotyczącą najnowszej wersji agenta i jednej wersji.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Stan wydania

4 grudnia 2019: wydano do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Obejmuje obsługę [Azure AD Connect aprowizacji w chmurze](../cloud-provisioning/what-is-cloud-provisioning.md) w celu synchronizowania danych użytkowników, kontaktów i grup z Active Directory lokalnych do usługi Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Stan wydania

9 września 2019: wydano do aktualizacji AutoUpdate

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Możliwość skonfigurowania dodatkowego śledzenia i rejestrowania problemów z agentami aprowizacji
* Możliwość pobrania tylko tych atrybutów usługi Azure AD, które są skonfigurowane w mapowaniu, aby zwiększyć wydajność synchronizacji

### <a name="fixed-issues"></a>Naprawione problemy

* Rozwiązano problem polegający na tym, że agent przeszedł w stan nieodpowiadający, jeśli wystąpiły problemy z błędami połączenia z usługą Azure AD
* Rozwiązano błąd, który spowodował problemy, gdy odczytano dane binarne z Azure Active Directory
* Rozwiązano problem polegający na tym, że Agent nie mógł odnowić relacji zaufania z usługą tożsamości hybrydowej w chmurze

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Stan wydania

23 stycznia 2019: wydano do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Odnowionych agenta aprowizacji i architektury łącznika w celu zapewnienia lepszej wydajności, stabilności i niezawodności 
* Uproszczona konfiguracja agenta aprowizacji przy użyciu Kreatora instalacji opartej na interfejsie użytkownika 
* Dodano obsługę automatycznych aktualizacji agenta

