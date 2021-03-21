---
title: 'Azure AD Connect: Wybierz typ instalacji | Microsoft Docs'
description: W tym temacie omówiono sposób wybierania typu instalacji do użycia w Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7275d115210100bdd4a3a2eb683c867a6a4a4f4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996614"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Wybieranie typu instalacji do użycia dla programu Azure AD Connect
Azure AD Connect ma dwa typy instalacji dla nowej instalacji: Express i dostosowane. Ten temat ułatwia określenie opcji, która ma być używana podczas instalacji.

## <a name="express"></a>Express
Express jest najbardziej powszechną opcją i jest używany przez około 90% wszystkich nowych instalacji. Została zaprojektowana w celu zapewnienia konfiguracji, która działa w przypadku najczęściej spotykanych scenariuszy klientów.

Przyjęto założenie:

- Istnieje pojedynczy las Active Directory lokalnie.
- Masz konto administratora przedsiębiorstwa, którego możesz użyć do instalacji.
- W Active Directory lokalnym znajdują się mniej niż 100 000 obiektów.

Uzyskasz:

- [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md) z lokalnego do usługi Azure AD w celu logowania jednokrotnego.
- Konfiguracja, która synchronizuje [użytkowników, grupy, kontakty i komputery z systemem Windows 10](concept-azure-ad-connect-sync-default-configuration.md).
- Synchronizacja wszystkich kwalifikujących się obiektów we wszystkich domenach i we wszystkich jednostkach organizacyjnych.
- [Automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md) jest włączone, aby upewnić się, że zawsze używasz najnowszej dostępnej wersji.

Opcje, w których można nadal korzystać z programu Express:

- Jeśli nie chcesz synchronizować wszystkich jednostek organizacyjnych, nadal możesz użyć programu Express i na ostatniej stronie, usuń zaznaczenie * * Rozpocznij proces synchronizacji... * * *. Następnie ponownie uruchom Kreatora instalacji i Zmień jednostki organizacyjne w [opcjach konfiguracji](how-to-connect-installation-wizard.md#customize-synchronization-options) i Włącz zaplanowaną synchronizację.
- Chcesz włączyć jedną z funkcji w Azure AD — wersja Premium, takich jak zapisywanie zwrotne haseł. Najpierw przejdź przez Express, aby zakończyć instalację początkową. Następnie ponownie uruchom Kreatora instalacji i Zmień [Opcje konfiguracji](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Niestandardowy
Dostosowana ścieżka umożliwia korzystanie z większej liczby opcji niż Express. Powinna być używana we wszystkich przypadkach, w których konfiguracja opisana w poprzedniej sekcji dla Express nie jest reprezentatywna dla organizacji.

Zastosowania:

- Nie masz dostępu do konta administratora przedsiębiorstwa w Active Directory.
- Masz więcej niż jeden las lub planujesz zsynchronizowanie więcej niż jednego lasu w przyszłości.
- Domeny w lesie nie są dostępne z serwera Connect.
- Zamierzasz używać uwierzytelniania federacyjnego lub przekazującego na potrzeby logowania użytkownika.
- Masz więcej niż 100 000 obiektów i musisz użyć pełnego SQL Server.
- Planujesz użycie filtrowania opartego na grupach i nie tylko filtrowania opartego na domenie lub jednostce organizacyjnej.

## <a name="upgrade-from-dirsync"></a>Uaktualnianie przy użyciu narzędzia DirSync
Jeśli używasz obecnie narzędzia DirSync, postępuj zgodnie z instrukcjami w sekcji [Uaktualnianie z narzędzia DirSync](how-to-dirsync-upgrade-get-started.md) , aby uaktualnić istniejącą konfigurację. Dostępne są dwie różne opcje uaktualniania:

- Uaktualnianie w miejscu do instalacji programu Connect na tym samym serwerze.
- Wdrożenie równoległe w celu zainstalowania połączenia na nowym serwerze, gdy nadal działa istniejący serwer DirSync.

## <a name="upgrade-from-azure-ad-sync"></a>Uaktualnij z Azure AD Sync
Jeśli obecnie używasz Azure AD Sync, możesz wykonać te [same czynności](how-to-upgrade-previous-version.md) co w przypadku uaktualniania z jednej wersji programu do nowszej. Dostępne są dwie różne opcje uaktualniania:

- Uaktualnianie w miejscu do instalacji programu Connect na tym samym serwerze.
- Zasięg — migracja w celu zainstalowania połączenia na nowym serwerze, gdy istniejący serwer Azure AD Sync nadal działa.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migrowanie z FIM2010 lub programie MIM2016
Jeśli obecnie używasz programu Forefront Identity Manager 2010 lub Microsoft Identity Manager 2016 z łącznikiem usługi Azure AD, jedyną opcją jest migracja. Wykonaj kroki opisane w sekcji [zasięg migracji](how-to-upgrade-previous-version.md#swing-migration). W tych krokach Zastąp wszelkie wzmianki Azure AD Sync z FIM2010/programie MIM2016.

## <a name="next-steps"></a>Następne kroki
W zależności od opcji wybranej do użycia Użyj spisu treści po lewej stronie, aby znaleźć artykuł z szczegółowymi krokami.
