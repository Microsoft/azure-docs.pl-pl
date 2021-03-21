---
title: 'Azure AD Connect Sync: zrozumienie użytkowników, grup i kontaktów | Microsoft Docs'
description: Wyjaśnia użytkowników, grupy i kontakty w Azure AD Connect synchronizacji.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e015f7937db6788aa4473a8a04434121299901e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861786"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Azure AD Connect Sync: zrozumienie użytkowników, grup i kontaktów
Istnieje kilka różnych powodów, dla których można korzystać z wielu Active Directory lasów i istnieje kilka różnych topologii wdrażania. Typowe modele obejmują wdrożenie zasobów konta i sync'ed po utworzeniu przez & scalanie. Jednak nawet jeśli istnieją czyste modele, modele hybrydowe są powszechnie używane. Konfiguracja domyślna w Azure AD Connect synchronizacji nie zakłada żadnego konkretnego modelu, ale w zależności od sposobu wybrania dopasowania użytkownika w podręczniku instalacji można zaobserwować różne zachowania.

W tym temacie opisano sposób zachowania konfiguracji domyślnej w niektórych topologiach. Przejdziemy przez konfigurację, a Edytor reguł synchronizacji może służyć do przeglądania konfiguracji.

Istnieje kilka ogólnych reguł, których konfiguracja zakłada:
* Niezależnie od tego, która kolejność została zaimportowana z źródłowych aktywnych katalogów, wynik końcowy powinien być zawsze taki sam.
* Aktywne konto będzie zawsze współtworzyć informacje logowania, w tym **userPrincipalName** i **sourceAnchor**.
* Wyłączone konto będzie współtworzyć wartość userPrincipalName i sourceAnchor, chyba że jest to połączona Skrzynka pocztowa, jeśli nie będzie można znaleźć aktywnego konta.
* Konto z połączoną skrzynką pocztową nigdy nie będzie używane dla userPrincipalName i sourceAnchor. Przyjęto założenie, że aktywne konto zostanie znalezione później.
* Obiekt Contact może zostać zainicjowany do usługi Azure AD jako osoba kontaktowa lub jako użytkownik. Nie wiadomo, że wszystkie źródłowe Active Directory lasy nie zostaną przetworzone.

## <a name="groups"></a>Grupy
Ważne kwestie, o których należy pamiętać podczas synchronizowania grup z Active Directory do usługi Azure AD:

* Azure AD Connect wyklucza wbudowane grupy zabezpieczeń z synchronizacji katalogów.

* Azure AD Connect nie obsługuje synchronizowania [członkostwa w grupie podstawowej](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771489(v=ws.11)) z usługą Azure AD.

* Azure AD Connect nie obsługuje synchronizowania [członkostw dynamicznej grupy dystrybucyjnej](/Exchange/recipients/dynamic-distribution-groups/dynamic-distribution-groups) z usługą Azure AD.

* Aby zsynchronizować grupę Active Directory z usługą Azure AD jako grupę z włączoną obsługą poczty:

    * Jeśli atrybut *ProxyAddress* grupy jest pusty, jego atrybut *mail* musi mieć wartość

    * Jeśli atrybut *ProxyAddress* grupy nie jest pusty, musi zawierać co najmniej jedną wartość adresu serwera proxy SMTP. Oto kilka przykładów:
    
      * Grupa Active Directory, której atrybut proxyAddress ma wartość *{"X500:/0 = contoso. com/OU = users/CN = test grupy"}* nie będzie włączona w usłudze Azure AD. Nie ma adresu SMTP.
      
      * Grupa Active Directory, której atrybut proxyAddress ma wartości *{"X500:/0 = contoso. com/OU = users/CN = test grupy", "SMTP: jankowalski \@ contoso.com"},* będzie dostępna w usłudze Azure AD.
      
      * Grupa Active Directory, której atrybut proxyAddress ma wartości *{"X500:/0 = contoso. com/OU = users/CN = test grupy", "SMTP: jankowalski \@ contoso.com"}* , będzie również dostępna w usłudze Azure AD.

## <a name="contacts"></a>Kontakty
Kontakty reprezentujące użytkownika w innym lesie są wspólne po &ie przez scalanie, gdzie rozwiązanie GALSync ma mostkowanie co najmniej dwóch lasów programu Exchange. Obiekt Contact jest zawsze łączący się z przestrzeni łącznika do Metaverse przy użyciu atrybutu mail. Jeśli istnieje już obiekt Contact lub obiekt użytkownika o tym samym adresie pocztowym, obiekty są sprzężone ze sobą. Ta konfiguracja jest skonfigurowana w ramach reguły **z usługi AD — Dołącz do kontaktów**. Istnieje również reguła o nazwie **z usługi AD — kontakt wspólny** z przepływem atrybutu do atrybutu Metaverse **sourceObjectType** za pomocą stałej **osoby kontaktowej**. Ta reguła ma bardzo niski priorytet, dlatego jeśli dowolny obiekt użytkownika jest przyłączony do tego samego obiektu metaverse, a następnie reguła **z usługi AD — wspólne przez użytkownika** będzie współtworzyć użytkownika wartości dla tego atrybutu. W przypadku tej reguły ten atrybut będzie miał wartość kontakt, jeśli nie został dołączony żaden użytkownik i użytkownik wartości, jeśli zostanie znaleziony co najmniej jeden użytkownik.

W przypadku aprowizacji obiektu w usłudze Azure AD Reguła ruchu wychodzącego **do usługi AAD — sprzężenie kontaktu** spowoduje utworzenie obiektu Contact, jeśli atrybut **sourceObjectType** jest ustawiony jako **Contact**. Jeśli ten atrybut jest ustawiony na **użytkownika**, reguła do usługi **AAD — dołączenie do użytkownika** spowoduje utworzenie obiektu użytkownika.
Istnieje możliwość, że obiekt został podwyższony od kontaktu do użytkownika, gdy zostanie zaimportowana i zsynchronizowana większa źródłowa usługa Active Directory.

Na przykład w topologii GALSync będzie można znaleźć obiekty Contact dla wszystkich w drugim lesie podczas importowania pierwszego lasu. Spowoduje to przemieszczenie nowych obiektów kontaktów w łączniku usługi AAD. Podczas późniejszego importowania i synchronizowania drugiego lasu znajdziesz rzeczywistych użytkowników i Dołącz je do istniejących obiektów Metaverse. Następnie usuniemy obiekt Contact w usłudze AAD i utworzysz nowy obiekt użytkownika.

Jeśli masz topologię, w której użytkownicy są reprezentowani jako kontakty, upewnij się, że wybrano opcję dopasowania użytkowników w atrybucie poczty w podręczniku instalacji. Jeśli wybierzesz inną opcję, będzie dostępna konfiguracja zależna od kolejności. Obiekty Contact zawsze będą łączyć się z atrybutem mail, ale obiekty użytkownika będą łączyć się tylko z atrybutem mail, jeśli ta opcja została wybrana w podręczniku instalacji. Następnie można zakończyć z dwoma różnymi obiektami w obiekcie Metaverse przy użyciu tego samego atrybutu mail, jeśli obiekt Contact został zaimportowany przed obiektem użytkownika. Podczas eksportowania do usługi Azure AD zostanie wygenerowany błąd. To zachowanie jest zgodne z projektem i wskazuje na złe dane lub że topologia nie została prawidłowo zidentyfikowana podczas instalacji.

## <a name="disabled-accounts"></a>Wyłączone konta
Wyłączone konta są również synchronizowane z usługą Azure AD. Wyłączone konta są wspólne do reprezentowania zasobów w programie Exchange, na przykład pokojów konferencyjnych. Wyjątkiem są użytkownicy z połączoną skrzynką pocztową; Jak wspomniano wcześniej, nigdy nie będzie można zainicjować obsługi konta w usłudze Azure AD.

Przyjęto założenie, że jeśli zostanie znalezione wyłączone konto użytkownika, kolejne aktywne konto nie zostanie znalezione później, a obiekt zostanie zainicjowany do usługi Azure AD z znalezionymi elementem userPrincipalName i sourceAnchor. W przypadku, gdy inne aktywne konto zostanie połączone z tym samym obiektem Metaverse, zostanie użyta jego wartość userPrincipalName i sourceAnchor.

## <a name="changing-sourceanchor"></a>Zmiana sourceAnchor
Gdy obiekt został wyeksportowany do usługi Azure AD, nie będzie można już zmienić sourceAnchor. Po wyeksportowaniu obiektu atrybut Metaverse **cloudSourceAnchor** jest ustawiany z wartością **sourceAnchor** akceptowaną przez usługę Azure AD. Jeśli **sourceAnchor** został zmieniony i nie jest zgodny z **cloudSourceAnchor**, reguła **do usługi AAD — dołączenie do użytkownika** spowoduje zgłoszenie błędu **sourceAnchor atrybutu**. W takim przypadku należy skorygować konfigurację lub dane, aby te same sourceAnchor były obecne w magazynie Metaverse, zanim będzie można ponownie zsynchronizować obiekt.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Synchronizacja Azure AD Connect: Dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
