---
ms.openlocfilehash: 35cb7ba4523cfbc88daf958fd972b127519385ce
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792396"
---
W tym artykule wymieniono wersje i funkcje agenta aprowizacji Azure Active Directory Connect, który został wystawiony. Zespół usługi Azure AD regularnie aktualizuje agenta aprowizacji za pomocą nowych funkcji i funkcjonalności. Agent aprowizacji jest automatycznie aktualizowany po wydaniu nowej wersji. 

Firma Microsoft zapewnia bezpośrednią pomoc techniczną dotyczącą najnowszej wersji agenta i jednej wersji.

## <a name="113540"></a>1.1.354.0

20 stycznia 2021: wydano do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
- Udoskonalenie środowiska GMSA, w tym obsługa wstępnie utworzonego niestandardowego konta GMSA
- [Polecenia cmdlet programu PowerShell](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md) obsługują konfigurację gMSA
- Obsługa instalacji agenta w [interfejsie wiersza polecenia](../articles/active-directory/cloud-sync/how-to-install-pshell.md) (instalacja dyskretna)
- Dodatkowa Diagnostyka dla problemów kwarantanny źródła agenta
- Poprawki błędów, które obejmują zmniejszenie użycia pamięci przez filtry zakresu jednostki organizacyjnej, z systemem PHS tylko dla użytkowników należących do zakresu, obsługę zagnieżdżonych obiektów w jednostce organizacyjnej w przypadku używania zakresu jednostek organizacyjnych itp. 


### <a name="fixed-issues"></a>Naprawione problemy
-    Zapobiegaj kwarantannie, gdy grupa określania zakresu jest poza zakresem
-   w przypadku skonfigurowania filtrów określania zakresu — zadanie PHS działa teraz tylko dla użytkowników należących do zakresu
-   Agent będzie zawieszał się w trakcie uaktualniania
-   Początkowa synchronizacja obiektów w zagnieżdżonych jednostkach organizacyjnych w przypadku używania określania zakresu jednostki organizacyjnej
-   Zwiększ niezawodność Repair-AADCloudSyncToolsAccount
-   Zmniejsz użycie dużych ilości pamięci przez filtry zakresu jednostki organizacyjnej
-   Sprawdzanie roli administratora kończy się niepowodzeniem, jeśli członkowie roli zawierają grupę zabezpieczeń
-   Rozwiąż problem z uprawnieniami do folderu GMSA, który uniemożliwia odnowienie certyfikatu agenta







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Stan wydania

23 listopada 2020: wydano do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Obsługa [gMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts)
* Obsługa grup o rozmiarze mniejszym niż 1500 członków w cyklu synchronizacji przyrostowej lub różnicowej. Ma to zastosowanie w przypadku korzystania z filtru określania zakresu grup
* Obsługa dużych grup z rozmiarem elementu członkowskiego do 15 tys.
* Ulepszenia synchronizacji początkowej
* Zaawansowane rejestrowanie pełne
* Wprowadzenie do [modułu AADCloudSyncTools PowerShell](../articles/active-directory/cloud-sync/reference-powershell.md)
* Stałe ograniczenia zezwalające na instalację agenta na serwerze innym niż angielski
* Obsługa filtrowania PHS tylko dla obiektów w zakresie (pierwotnie synchronizacja skrótów haseł dla wszystkich obiektów)
* Rozwiązano problem przecieku pamięci w agencie
* Udoskonalone dzienniki aprowizacji
* Obsługa konfigurowania [limitu czasu połączenia LDAP](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 
* Obsługa konfigurowania [kartach odwołań](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing) 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Stan wydania

4 grudnia 2019: wydano do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Obejmuje obsługę [Azure AD Connect synchronizacji w chmurze](../articles/active-directory/cloud-sync/what-is-cloud-sync.md) w celu synchronizowania danych użytkowników, kontaktów i grup z Active Directory lokalnych do usługi Azure AD


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


