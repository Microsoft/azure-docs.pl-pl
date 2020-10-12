---
title: Wyszukiwanie i filtrowanie członków i właścicieli grup (wersja zapoznawcza) — Azure Active Directory | Microsoft Docs
description: Wyszukiwanie i filtrowanie grup członków i właścicieli w Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92674ed9d39ea9e84d477d19aadbaeda6da6f32c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84728269"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Wyszukaj grupy i członków (wersja zapoznawcza) w Azure Active Directory

W tym artykule opisano, jak wyszukiwać członków i właścicieli grupy oraz jak używać filtrów wyszukiwania w ramach wersji zapoznawczej ulepszeń grup w portalu Azure Active Directory (Azure AD). Istnieją wiele ulepszeń w zakresie grup, które ułatwiają zarządzanie grupami, w tym członków i właścicieli, szybko i łatwo. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

Zmiany w tej wersji zapoznawczej obejmują:

- Nowe grupy możliwości wyszukiwania, takie jak wyszukiwanie podciągów w nazwach grup
- Nowe opcje filtrowania i sortowania na listach elementów członkowskich i właścicieli
- Nowe możliwości wyszukiwania dla list elementów członkowskich i właścicieli
- Dokładniejsze liczby grup dla dużych grup

## <a name="enabling-and-managing-the-preview"></a>Włączanie wersji zapoznawczej i zarządzanie nią

Dowiesz się, jak łatwo dołączyć do wersji zapoznawczej:

  1. Zaloguj się do [portalu usługi Azure AD](https://portal.azure.com), a następnie wybierz pozycję **grupy**.
  2. Na stronie grupy — wszystkie grupy wybierz transparent w górnej części strony, aby dołączyć do wersji zapoznawczej.

Najnowsze funkcje i ulepszenia można także sprawdzić, wybierając pozycję **Podgląd informacji** na stronie **wszystkie grupy** . Po dołączeniu do wersji zapoznawczej można zobaczyć znacznik podglądu na wszystkich stronach grup, które mają ulepszenia i są częścią wersji zapoznawczej. Nie każda Strona grup została zaktualizowana w ramach tej wersji zapoznawczej.

Jeśli masz jakieś problemy, możesz przełączyć się ze starszego środowiska, wybierając transparent w górnej części strony **wszystkie grupy** . Dziękujemy za Twoją opinię, dzięki czemu możemy ulepszyć nasze środowisko.

## <a name="group-search-and-sorting"></a>Wyszukiwanie i sortowanie grup

Przeszukiwanie listy grup zostało ulepszone, dzięki czemu w przypadku wprowadzenia ciągu wyszukiwania, wyszukiwanie automatycznie wykonuje `startswith` Wyszukiwanie i podciągi na liście nazw grup. Wyszukiwanie podciągów jest wykonywane tylko na całych słowach i nie zawiera znaków specjalnych. W wyszukiwaniu podciągów jest rozróżniana wielkość liter.

![nowe przeszukiwania podciągów na stronie wszystkie grupy](./media/groups-members-owners-search/groups-search-preview.png)

Na przykład wyszukiwanie "zasady" spowoduje teraz zwrócenie obu "zasad zarządzania urządzeniami przenośnymi – zachodnich" i "grupy zasad". Nie można zwrócić grupy o nazwie "New_policy".

- Możesz również wykonać te same wyszukiwania na listach członkostwa w grupach.
- Teraz można sortować listę grup według nazwy przy użyciu strzałek na prawo od nagłówka kolumny Name, aby posortować listę w kolejności rosnącej lub malejącej.

## <a name="group-member-search-and-filtering"></a>Wyszukiwanie i Filtrowanie elementów członkowskich grupy

### <a name="search-group-member-and-owner-lists"></a>Lista elementów członkowskich i właścicieli grup wyszukiwania

Teraz można przeszukiwać elementy członkowskie określonej grupy według nazwy i wykonywać te same wyszukiwania na liście właścicieli grupy. W nowym środowisku, jeśli wprowadzisz ciąg w polu wyszukiwania, wyszukiwanie StartsWith zostanie wykonane automatycznie. Na przykład wyszukiwanie "Scott" zwróci wartość Scott Wilkinson.

![nowe przeszukiwania podciągów na listach członków i właścicieli grupy](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Filtruj członków i listę właścicieli

Oprócz wyszukiwania możesz teraz filtrować listę elementów członkowskich i właścicieli według typu użytkownika. Te informacje znajdują się w kolumnie Typ użytkownika listy. Dlatego można filtrować listę według członków i Gości, aby określić, czy w grupie znajdują się wszyscy Goście.

### <a name="view-and-manage-membership"></a>Wyświetl członkostwo i zarządzaj nimi

Oprócz wyświetlania bezpośrednich członków określonej grupy, można teraz wyświetlić listę wszystkich członków grupy na stronie Członkowie. Lista członków obejmuje wszystkich unikatowych członków grupy, w tym wszelkich członków przechodnich.

Możesz również wyszukiwać i filtrować listę bezpośrednich członków i listę wszystkie elementy członkowskie pojedynczo. Filtrowanie listy wszystkie elementy członkowskie nie wpływa na filtry zastosowane na liście bezpośrednich elementów członkowskich.

## <a name="improved-group-member-counts"></a>Ulepszone liczby elementów członkowskich grupy

Ulepszono stronę **przeglądu** grup, aby zapewnić liczbę członków grupy dla grup wszystkich rozmiarów. Liczby elementów członkowskich można zobaczyć nawet dla grup mających więcej niż 1 000 członków. Teraz można zobaczyć łączną liczbę bezpośrednich elementów członkowskich dla grupy i całkowitą sumę członkostwa (wszystkie unikatowe elementy członkowskie grupy, łącznie z przechodnimi elementami członkowskimi) na stronie **Przegląd** .

![Większa dokładność w liczbie członkostwa w grupie](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje na temat pracy z grupami w usłudze Azure AD.

- [Wyświetlanie grup i elementów członkowskich](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-create-rule.md)
- [Edytowanie ustawień grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Zarządzanie dostępem do zasobów przy użyciu grup](../fundamentals/active-directory-manage-groups.md)
- [Zarządzanie dostępem do aplikacji SaaS przy użyciu grup](groups-saasapps.md)
- [Zarządzanie grupami przy użyciu poleceń programu PowerShell](groups-settings-v2-cmdlets.md)
- [Dodaj subskrypcję platformy Azure do Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
