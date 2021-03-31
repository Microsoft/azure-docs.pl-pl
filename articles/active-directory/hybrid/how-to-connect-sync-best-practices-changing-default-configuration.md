---
title: 'Azure AD Connect Sync: zmiana konfiguracji domyślnej | Microsoft Docs'
description: Zapewnia najlepsze rozwiązania dotyczące zmiany konfiguracji domyślnej Azure AD Connect synchronizacji.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/29/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70e91ff8fa3666a2dfc5aaad07be7927852b08bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85357702"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect Sync: najlepsze rozwiązania dotyczące zmieniania konfiguracji domyślnej
Celem tego tematu jest opisywanie obsługiwanych i nieobsługiwanych zmian w Azure AD Connect synchronizacji.

Konfiguracja utworzona przez Azure AD Connect działa tak jak w przypadku większości środowisk, które synchronizują Active Directory lokalne z usługą Azure AD. Jednak w niektórych przypadkach konieczne jest zastosowanie pewnych zmian do konfiguracji w celu spełnienia określonego zapotrzebowania lub wymagania.

## <a name="changes-to-the-service-account"></a>Zmiany konta usługi
Azure AD Connect synchronizacja jest uruchomiona w ramach konta usługi utworzonego przez Kreatora instalacji. To konto usługi zawiera klucze szyfrowania do bazy danych używanej przez synchronizację. Jest on tworzony z 127 znakami długiego hasła, a hasło jest ustawione na wartość nie wygasa.

* Zmiana lub zresetowanie hasła konta usługi nie jest **obsługiwane** . Spowoduje to zniszczenie kluczy szyfrowania, a usługa nie będzie mogła uzyskać dostępu do bazy danych i nie będzie można jej uruchomić.

## <a name="changes-to-the-scheduler"></a>Zmiany w harmonogramie
Począwszy od wersji z kompilacji 1,1 (luty 2016), [harmonogram](how-to-connect-sync-feature-scheduler.md) można skonfigurować tak, aby miał inny cykl synchronizacji niż domyślny 30 minut.

## <a name="changes-to-synchronization-rules"></a>Zmiany reguł synchronizacji
Kreator instalacji zapewnia konfigurację, która powinna współpracować z najbardziej typowymi scenariuszami. Jeśli trzeba wprowadzić zmiany w konfiguracji, należy wykonać poniższe reguły, aby nadal mieć obsługiwaną konfigurację.

> [!WARNING]
> Jeśli wprowadzisz zmiany w domyślnych regułach synchronizacji, te zmiany zostaną nadpisywane przy następnym aktualizowaniu Azure AD Connect, co skutkuje nieoczekiwanymi i prawdopodobnie niepożądanymi wynikami synchronizacji.

* Można [zmienić przepływy atrybutów](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) , jeśli domyślny bezpośredni przepływy atrybutów nie są odpowiednie dla Twojej organizacji.
* Jeśli chcesz nie przetworzyć [atrybutu](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) i usunąć wszelkie istniejące wartości atrybutów w usłudze Azure AD, musisz utworzyć regułę dla tego scenariusza.
* [Wyłączenie niepożądanej reguły synchronizacji](#disable-an-unwanted-sync-rule) zamiast jej usuwania. Usunięta reguła zostanie odtworzona podczas uaktualniania.
* Aby [zmienić regułę spoza pola](#change-an-out-of-box-rule), należy utworzyć kopię oryginalnej reguły i wyłączyć regułę out-of-box. Edytor reguł synchronizacji pojawia się i pomaga.
* Wyeksportuj niestandardowe reguły synchronizacji przy użyciu edytora reguł synchronizacji. Edytor udostępnia skrypt programu PowerShell, którego można użyć do łatwego ponownego tworzenia ich w scenariuszu odzyskiwania po awarii.

> [!WARNING]
> Reguły synchronizacji poza ramką mają odcisk palca. Jeśli wprowadzisz zmiany w tych regułach, odcisk palca nie będzie już dopasowywany. Podczas próby zastosowania nowej wersji Azure AD Connect mogą wystąpić problemy w przyszłości. Wprowadzać zmiany tylko w sposób opisany w tym artykule.

### <a name="disable-an-unwanted-sync-rule"></a>Wyłączanie niepożądanej reguły synchronizacji
Nie usuwaj reguły synchronizacji poza ramką. Jest on ponownie tworzony podczas następnego uaktualnienia.

W niektórych przypadkach Kreator instalacji wygenerował konfigurację, która nie działa dla topologii. Na przykład jeśli masz topologię lasu zasobów konta, ale schemat został rozszerzony w lesie kont z schematem programu Exchange, wówczas reguły dla programu Exchange są tworzone dla lasu kont i lasu zasobów. W takim przypadku należy wyłączyć regułę synchronizacji dla programu Exchange.

![Reguła synchronizacji wyłączona](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na powyższym rysunku Kreator instalacji znalazł stary schemat programu Exchange 2003 w lesie konta. To rozszerzenie schematu zostało dodane przed wprowadzeniem lasu zasobów w środowisku fabrikam. Aby upewnić się, że żadne atrybuty ze starej implementacji programu Exchange nie są zsynchronizowane, reguła synchronizacji powinna być wyłączona, jak pokazano.

### <a name="change-an-out-of-box-rule"></a>Zmiana wbudowanej reguły
Tylko wtedy, gdy należy zmienić regułę dołączania, jest konieczna zmiana reguły sprzężenia. Jeśli trzeba zmienić przepływ atrybutu, należy utworzyć regułę synchronizacji mającą wyższy priorytet niż reguły wbudowane. Jedyną regułą, która jest praktycznie potrzebna do klonowania, jest reguła **z przyłączania do użytkownika w usłudze AD**. Wszystkie inne reguły można zastąpić regułą o wyższym priorytecie.

Jeśli konieczne jest wprowadzenie zmian w regule out-of-Box, należy utworzyć kopię reguły out-of-Box i wyłączyć oryginalną regułę. Następnie wprowadź zmiany w sklonowanej regule. Edytor reguł synchronizacji pomaga wykonać te kroki. Po otwarciu reguły gotowej do użycia wyświetlane jest okno dialogowe:  
![Ostrzeżenie o regule z pola](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Wybierz pozycję **tak** , aby utworzyć kopię reguły. Sklonowana reguła jest następnie otwarta.  
![Sklonowana reguła](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

Dla tej sklonowanej reguły Wprowadź wszelkie niezbędne zmiany zakresu, przyłączenia i transformacji.

## <a name="next-steps"></a>Następne kroki
**Tematy dotyczące omówienia**

* [Synchronizacja programu Azure AD Connect: omówienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
