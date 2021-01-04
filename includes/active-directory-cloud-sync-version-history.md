---
ms.openlocfilehash: 77866fcb72515d5167d9f06b7d9ad91c59bc7945
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97700576"
---
W tym artykule wymieniono wersje i funkcje agenta aprowizacji Azure Active Directory Connect, który został wystawiony. Zespół usługi Azure AD regularnie aktualizuje agenta aprowizacji za pomocą nowych funkcji i funkcjonalności. Agent aprowizacji jest automatycznie aktualizowany po wydaniu nowej wersji. 

Firma Microsoft zapewnia bezpośrednią pomoc techniczną dotyczącą najnowszej wersji agenta i jednej wersji.

## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Stan wydania

23 listopada 2020: wydano do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Obsługa [gMSA](../articles/active-directory/cloud-provisioning/how-to-prerequisites.md#group-managed-service-accounts)
* Obsługa grup o rozmiarze mniejszym niż 1500 członków w cyklu synchronizacji przyrostowej lub różnicowej. Ma to zastosowanie w przypadku korzystania z filtru określania zakresu grup
* Obsługa dużych grup z rozmiarem elementu członkowskiego do 15 tys.
* Ulepszenia synchronizacji początkowej
* Zaawansowane rejestrowanie pełne
* Wprowadzenie do [modułu AADCloudSyncTools PowerShell](../articles/active-directory/cloud-provisioning/reference-powershell.md)
* Stałe ograniczenia zezwalające na instalację agenta na serwerze innym niż angielski
* Obsługa filtrowania PHS tylko dla obiektów w zakresie (pierwotnie synchronizacja skrótów haseł dla wszystkich obiektów)
* Rozwiązano problem przecieku pamięci w agencie
* Udoskonalone dzienniki aprowizacji
* Obsługa konfigurowania [limitu czasu połączenia LDAP](../articles/active-directory/cloud-provisioning/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 
* Obsługa konfigurowania [kartach odwołań](../articles/active-directory/cloud-provisioning/how-to-manage-registry-options.md#configure-referral-chasing) 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Stan wydania

4 grudnia 2019: wydano do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Obejmuje obsługę [Azure AD Connect aprowizacji w chmurze](../articles/active-directory/cloud-provisioning/what-is-cloud-provisioning.md) w celu synchronizowania danych użytkowników, kontaktów i grup z Active Directory lokalnych do usługi Azure AD


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


