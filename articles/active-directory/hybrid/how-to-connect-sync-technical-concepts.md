---
title: 'Azure AD Connect Sync: koncepcje techniczne | Microsoft Docs'
description: Wyjaśnia koncepcje techniczne synchronizacji Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01e53b30a4c27296e30e031ffb771697afa8e1e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87019679"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Synchronizacja programu Azure AD Connect: zagadnienia techniczne
Ten artykuł zawiera podsumowanie tematów dotyczących [architektury](how-to-connect-sync-technical-concepts.md).

Azure AD Connect synchronizacji są kompilacje na pełnej platformie synchronizacji z usługą SSD.
W poniższych sekcjach omówiono koncepcje synchronizacji katalogów w usłudze.
W przypadku kompilowania na MIIS (Microsoft Identity Integration Server), ILM (Menedżer cyklu życia tożsamości) i programu FIM (Forefront Identity Manager) usługi Azure Active Directory Sync Services zapewniają następną platformę do łączenia się ze źródłami danych, synchronizowanie danych między źródłami danych, a także Inicjowanie obsługi administracyjnej tożsamości.

![Zagadnienia techniczne](./media/how-to-connect-sync-technical-concepts/scenario.png)

Poniższe sekcje zawierają więcej informacji na temat następujących aspektów usługi synchronizacji FIM:

* Łącznik
* Przepływ atrybutów
* Przestrzeń łącznika
* Metaverse
* Inicjowanie obsługi

## <a name="connector"></a>Łącznik
Moduły kodu, które są używane do komunikacji z podłączonym katalogiem, są nazywane łącznikami (wcześniej znanymi jako agenci zarządzania (MAs)).

Są one zainstalowane na komputerze z uruchomioną Azure AD Connect synchronizacji. Łączniki zapewniają bezagentowi możliwość przełączenia z wykorzystaniem zdalnych protokołów systemowych zamiast polegania na wdrażaniu wyspecjalizowanych agentów. Oznacza to zmniejszenie czasu ryzyka i wdrożenia, szczególnie w przypadku pracy z krytycznymi aplikacjami i systemami.

Na powyższym rysunku łącznik jest równoznaczny z miejscem łącznika, ale obejmuje całą komunikację z systemem zewnętrznym.

Łącznik jest odpowiedzialny za wszystkie funkcje importu i eksportu do systemu i zwalnia deweloperów, którzy chcą dowiedzieć się, jak połączyć się z każdym systemem w sposób natywny w przypadku korzystania z aprowizacji deklaracyjnej w celu dostosowania transformacji danych.

Importowanie i eksportowanie odbywają się tylko wtedy, gdy są zaplanowane, co pozwala na dalsze izolacje zmian w systemie, ponieważ zmiany nie są automatycznie propagowane do połączonego źródła danych. Ponadto deweloperzy mogą również tworzyć własne łączniki do łączenia się z praktycznie dowolnym źródłem danych.

## <a name="attribute-flow"></a>Przepływ atrybutów
Metaverse to skonsolidowany widok wszystkich dołączonych tożsamości od sąsiednich miejsc łączników. Na powyższym rysunku przepływ atrybutu jest przedstawiany przez linie ze strzałkami dla przepływu przychodzącego i wychodzącego. Przepływ atrybutu jest procesem kopiowania lub przekształcania danych z jednego systemu do innego i przepływów atrybutów (ruchu przychodzącego lub wychodzącego).

Przepływ atrybutów występuje między obszarem łącznika a obiektem wielowierszowym, gdy zaplanowane jest wykonywanie operacji synchronizacji (pełnej lub różnicowej).

Przepływ atrybutu występuje tylko wtedy, gdy te synchronizacje są uruchamiane. Przepływy atrybutów są zdefiniowane w regułach synchronizacji. Mogą to być przychodzące (ISR na zdjęciu powyżej) lub wychodzące (OSR na powyższym rysunku).

## <a name="connected-system"></a>Połączony system
Połączony system (katalog połączony) odwołuje się do systemu zdalnego, Azure AD Connect synchronizacja połączyła się z i odczytuje i zapisuje dane tożsamości do i z.

## <a name="connector-space"></a>Przestrzeń łącznika
Każde połączone źródło danych jest reprezentowane jako Przefiltrowany podzbiór obiektów i atrybutów w obszarze łącznika.
Dzięki temu usługa synchronizacji może działać lokalnie bez konieczności kontaktowania się z systemem zdalnym podczas synchronizacji obiektów i ogranicza interakcje tylko do importowania i eksportowania.

Gdy źródło danych i łącznik mają możliwość udostępnienia listy zmian (import Delta), wydajność operacyjna wzrasta znacznie tak, jak tylko zmiany od ostatniego cyklu sondowania są wymieniane. Spacja łącznika izolowane jest od zmian, które są automatycznie propagowane, wymagając, aby łącznik zaplanował import i eksporty. To dodanie ubezpieczenia pozwala na poczucie pokoju podczas testowania, podglądu lub potwierdzenia następnej aktualizacji.

## <a name="metaverse"></a>Metaverse
Metaverse to skonsolidowany widok wszystkich dołączonych tożsamości od sąsiednich miejsc łączników.

Wraz z przypisaniem tożsamości i przypisywaniem do różnych atrybutów za pośrednictwem mapowania przepływu importu centralny obiekt Metaverse zaczyna agregowanie informacji z wielu systemów. W tym przepływie atrybutu obiektu mapowania zawierają informacje do systemów wychodzących.

Obiekty są tworzone, gdy autorytatywny system projektuje je w magazynie Metaverse. Po usunięciu wszystkich połączeń obiekt Metaverse zostanie usunięty.

Obiektów w magazynie Metaverse nie można edytować bezpośrednio. Wszystkie dane w obiekcie muszą być współtworzone przez przepływ atrybutów. Funkcja Metaverse zachowuje trwałe łączniki z każdym miejscem łącznika. Te łączniki nie wymagają ponownej oceny dla każdego przebiegu synchronizacji. Oznacza to, że synchronizacja Azure AD Connect nie musi zlokalizować pasującego obiektu zdalnego za każdym razem. Pozwala to uniknąć konieczności od kosztownych agentów do zapobiegania zmianom atrybutów, które zwykle są odpowiedzialne za skorelowanie obiektów.

Podczas odnajdywania nowych źródeł danych, które mogą mieć już istniejące obiekty, które muszą być zarządzane, Azure AD Connect Sync używa procesu zwanego regułą sprzężenia do oszacowania potencjalnych kandydatów, za pomocą których można nawiązać połączenie.
Po nawiązaniu połączenia Ta ocena nie następuje, a normalny przepływ atrybutów może wystąpić między zdalnym połączonym źródłem danych a Metaverse.

## <a name="provisioning"></a>Inicjowanie obsługi
Gdy autorytatywne projekty źródłowe są nowym obiektem do Metaverse, nowy obiekt przestrzeni łącznika można utworzyć w innym łączniku reprezentującym podrzędne źródło danych.

Z tego samego ustanawia łącze, a przepływ atrybutów może być przebiegać dwukierunkowo.

Za każdym razem, gdy reguła określa, że należy utworzyć nowy obiekt przestrzeni łącznika, jest on nazywany aprowizacji. Jednak ponieważ ta operacja odbywa się tylko w obrębie obszaru łącznika, nie jest przełączana do połączonego źródła danych, dopóki nie zostanie wykonane eksportowanie.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Synchronizacja Azure AD Connect: Dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
