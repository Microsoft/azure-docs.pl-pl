---
title: Dodawanie użytkowników z bezpośrednimi licencjami do licencjonowania grupowego — Azure AD | Microsoft Docs
description: Jak przeprowadzić migrację z licencji poszczególnych użytkowników do licencjonowania opartego na grupach przy użyciu Azure Active Directory
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: adc80cf579ce3086abd4171b065f859acd0b9294
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546509"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Jak migrować użytkowników z licencjami indywidualnymi do grup na potrzeby licencjonowania

Możesz mieć istniejące licencje wdrożone dla użytkowników w organizacjach za pośrednictwem bezpośredniego przypisywania. oznacza to, że za pomocą skryptów programu PowerShell lub innych narzędzi do przypisywania licencji poszczególnych użytkowników. Przed rozpoczęciem korzystania z licencjonowania opartego na grupach w celu zarządzania licencjami w organizacji można użyć tego planu migracji, aby bezproblemowo zamienić istniejące rozwiązania na Licencjonowanie oparte na grupach.

Najważniejszym czynnikiem, które należy wziąć pod uwagę, jest uniknięcie sytuacji, w której migracja do licencjonowania opartego na grupach spowoduje tymczasowe utratę aktualnie przypisanych licencji. Wszelkie procesy, które mogą skutkować usunięciem licencji, powinny być nieuniknione w celu usunięcia ryzyka utraty dostępu do usług i ich danych.

## <a name="recommended-migration-process"></a>Zalecany proces migracji

1. Masz już istniejącą automatyzację (na przykład program PowerShell), która zarządza przypisaniem licencji i usuwaniem dla użytkowników. Pozostaw to działanie w systemie.

1. Utwórz nową grupę licencjonowania (lub Zdecyduj, które istniejące grupy mają być używane) i upewnij się, że wszyscy wymagani użytkownicy są dodawani jako członkowie.

1. Przypisywanie wymaganych licencji do tych grup; Celem powinna być odzwierciedlenie tego samego stanu licencjonowania, który posiada istniejąca Automatyzacja (na przykład program PowerShell) dla tych użytkowników.

1. Sprawdź, czy licencje zostały zastosowane do wszystkich użytkowników w tych grupach. Tę aplikację można wykonać, sprawdzając stan przetwarzania poszczególnych grup i sprawdzając dzienniki inspekcji.

   - Możesz sprawdzać poszczególnych użytkowników, przeglądając ich szczegóły licencji. Zobaczysz, że mają te same licencje przypisane "bezpośrednio" i "Odziedziczone" z grup.

   - Można uruchomić skrypt programu PowerShell, aby [sprawdzić, w jaki sposób licencje są przypisane do użytkowników](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Gdy ta sama licencja produktu jest przypisana do użytkownika zarówno bezpośrednio, jak i za pomocą grupy, użytkownik korzysta z tylko jednej licencji. Z tego powodu żadne dodatkowe licencje nie są wymagane do przeprowadzenia migracji.

1. Sprawdź, czy żadne przypisania licencji nie powiodły się, sprawdzając każdą grupę dla użytkowników w stanie błąd. Aby uzyskać więcej informacji, zobacz [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy](licensing-groups-resolve-problems.md).

Rozważ usunięcie oryginalnych przypisań bezpośrednich. Zalecamy jej stopniowe i monitorowanie wyników najpierw w podzbiorze użytkowników. Jeśli możesz opuścić oryginalne bezpośrednie przypisania dla użytkowników, ale gdy użytkownicy opuściją licencjonowane grupy, zachowają przypisane bezpośrednio licencje, które mogą nie być potrzebne.

## <a name="an-example"></a>Przykład

Organizacja ma 1 000 użytkowników. Wszyscy użytkownicy wymagają licencji dla pakietu Office 365 Enterprise E3. Obecnie organizacja ma skrypt programu PowerShell uruchomiony w środowisku lokalnym, dodając i usuwając licencje od użytkowników. Jednak organizacja chce zastąpić skrypt przy użyciu licencjonowania opartego na grupach, dzięki czemu licencje mogą być zarządzane automatycznie przez usługę Azure AD.

Oto, jak może wyglądać proces migracji:

1. Za pomocą Azure Portal Przypisz licencję pakietu Office 365 E3 do grupy **Wszyscy użytkownicy** w usłudze Azure AD.

1. Upewnij się, że ukończono przypisanie licencji dla wszystkich użytkowników. Przejdź do strony przegląd dla grupy, wybierz pozycję **licencje** i sprawdź stan przetwarzania w górnej części bloku **licencje** .

   - Wyszukaj "najnowsze zmiany licencji zostały zastosowane do wszystkich użytkowników" w celu potwierdzenia przetworzenia.

   - Wyszukaj powiadomienie na temat informacji o użytkownikach, dla których licencje mogły nie zostać pomyślnie przypisane. Czy w przypadku niektórych użytkowników zabrakło licencji? Czy niektórzy użytkownicy mają sprzeczne plany licencji, które uniemożliwiają dziedziczenie licencji grupowych?

1. Sprawdź, czy niektórzy użytkownicy sprawdzają, czy mają zastosowane zarówno licencje bezpośrednie, jak i grupy. Przejdź do strony profilu użytkownika, wybierz pozycję **licencje** i przejrzyj stan licencji.

   - Jest to oczekiwany stan użytkownika podczas migracji:

      ![oczekiwany stan użytkownika podczas migracji](./media/licensing-groups-migrate-users/expected-user-state.png)

     Jest to potwierdzenie, że użytkownik ma zarówno licencje bezpośrednie, jak i dziedziczone. Widzimy, że pakiet Office 365 E3 jest przypisany.

   - Wybierz każdą licencję, aby zobaczyć, które usługi są włączone. Aby sprawdzić, czy licencje bezpośredniego i grupowe włączą dokładnie te same usługi dla użytkownika, wybierz pozycję **przypisania**.

1. Po potwierdzeniu, że zarówno licencje bezpośrednie, jak i grupy są równoważne, można rozpocząć usuwanie bezpośrednich licencji od użytkowników. Można to przetestować, usuwając je dla poszczególnych użytkowników w portalu, a następnie uruchamiając skrypty automatyzacji, aby zostały usunięte zbiorczo. Oto przykład tego samego użytkownika z bezpośrednimi licencjami, które zostały usunięte za pośrednictwem portalu. Należy zauważyć, że stan licencji pozostaje niezmieniony, ale nie są już wyświetlane bezpośrednie przypisania.

   ![Potwierdź, że licencje bezpośrednie są usuwane](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat innych scenariuszy zarządzania licencjami grupy:

- [Co to jest Licencjonowanie oparte na grupach w Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Przypisywanie licencji do grupy w usłudze Azure Active Directory](licensing-groups-assign.md)
- [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy w usłudze Azure Active Directory](licensing-groups-resolve-problems.md)
- [Jak migrować użytkowników między licencjami produktu przy użyciu licencjonowania opartego na grupach w programie Azure Active Directory](licensing-groups-change-licenses.md)
- [Dodatkowe scenariusze licencjonowania opartego na grupach w usłudze Azure Active Directory](licensing-group-advanced.md)
- [Przykłady programu PowerShell dla licencjonowania opartego na grupach w Azure Active Directory](licensing-ps-examples.md)
