---
title: Odnajdź wystąpienia SQL Server w istniejącym projekcie Azure Migrate
description: Dowiedz się, jak odnajdywać wystąpienia SQL Server w istniejącym Azure Migrate projekcie.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 2de60880b511e43ffb2949a15fec2cf2a94f62fa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567156"
---
# <a name="discover-sql-server-instances-in-an-existing-project"></a>Odnajdywanie wystąpień SQL Server w istniejącym projekcie 

W tym artykule opisano sposób odnajdywania SQL Server wystąpień i baz danych w projekcie [Azure Migrate](./migrate-services-overview.md) , który został utworzony przed rozpoczęciem korzystania z wersji zapoznawczej funkcji oceny usługi Azure SQL.

Odnajdywanie wystąpień SQL Server i baz danych działających na maszynach lokalnych pomaga identyfikować i dostosowywać ścieżkę migracji do usługi Azure SQL. Urządzenie Azure Migrate wykonuje to odnajdywanie przy użyciu poświadczeń domeny lub poświadczeń uwierzytelniania SQL Server, które mają dostęp do wystąpień SQL Server i baz danych uruchomionych na serwerach kierowanych. Ten proces odnajdywania jest niezależny od agenta, co oznacza, że nic nie jest zainstalowane na serwerach docelowych.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Upewnij się, że wykonano następujące czynności:
    - Utworzono [projekt Azure Migrate](./create-manage-projects.md) przed ogłoszeniem funkcji oceny SQL w Twoim regionie
    - Dodano [Azure Migrate: narzędzie odnajdywania i oceny](./how-to-assess.md) do projektu
- Przejrzyj temat [Obsługa i wymagania dotyczące odnajdywania aplikacji](./migrate-support-matrix-vmware.md#vmware-requirements).
-  Upewnij się, że serwery, na których działa program App-Discovery, mają zainstalowany program PowerShell w wersji 2,0 lub nowszej, a zainstalowano narzędzia VMware (nowsze niż 10.2.0).
- Sprawdź [wymagania](./migrate-appliance.md) dotyczące wdrażania urządzenia Azure Migrate.
- Sprawdź, czy masz [wymagane role](./create-manage-projects.md#verify-permissions) w subskrypcji, aby utworzyć zasoby.
- Upewnij się, że urządzenie ma dostęp do Internetu

## <a name="enable-discovery-of-sql-server-instances-and-databases"></a>Włącz odnajdywanie wystąpień SQL Server i baz danych

1. W projekcie Azure Migrate
    - Wybierz pozycję **nie włączono** na kafelku centrum lub   :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="Azure Migrate kafelka centrum z niewłączonym odnajdywaniem SQL":::
    - Wybierz pozycję **nie włączone** w żadnym wpisie na stronie Odnajdywanie serwera w obszarze Azure Migrate kolumny wystąpienia SQL, w której znajduje   :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="się blok odnalezionych serwerów z niewłączonym odnajdywaniem SQL":::
2. W obszarze odkryj SQL Server wystąpienia i bazy danych postępuj zgodnie z pożądanymi krokami:
    - Wybierz pozycję **Uaktualnij**, aby utworzyć wymagany zasób.
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Przycisk służący do uaktualniania urządzenia Azure Migrate":::
    - Sprawdź, czy usługi działające na urządzeniu zostały zaktualizowane do najnowszej wersji. Aby to zrobić, uruchom Menedżera konfiguracji urządzenia z serwera urządzenia i wybierz pozycję Wyświetl usługi urządzenia w panelu wymagania wstępne Instalatora.
        - Urządzenie i jego składniki są automatycznie aktualizowane :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="Sprawdź wersję urządzenia":::
    - W panelu zarządzanie poświadczeniami i źródłami odnajdywania w Menedżerze konfiguracji urządzenia Dodaj poświadczenia uwierzytelniania domeny lub SQL Server, które mają dostęp administratora systemu w wystąpieniu SQL Server i bazach danych, które mają zostać odnalezione.
    Możesz użyć funkcji automatycznego mapowania poświadczeń urządzenia lub ręcznie zmapować poświadczenia na odpowiedni serwer, jak zostało to wyróżnione w [tym miejscu](./tutorial-discover-vmware.md#start-continuous-discovery).

    Niektóre punkty do uwagi:
    - Upewnij się, że Spis oprogramowania jest już włączony, lub podaj poświadczenia domeny lub niedomenowe, aby włączyć takie samo. Aby odnajdywać wystąpienia SQL Server, należy wykonać Spis oprogramowania.
    - Urządzenie podejmie próbę zweryfikowania poświadczeń domeny z usługą AD, gdy zostaną dodane. Upewnij się, że serwer urządzeń ma wgląd w sieć, do serwera usługi AD skojarzonego z poświadczeniami. Poświadczenia skojarzone z uwierzytelnianiem SQL Server nie są weryfikowane.

3. Po dodaniu żądanych poświadczeń wybierz pozycję Rozpocznij odnajdywanie, aby rozpocząć skanowanie.

> [!Note]
>Przed rozpoczęciem tworzenia ocen dla usługi Azure SQL Zezwól na działanie funkcji odnajdywania SQL. Jeśli nie można ukończyć odnajdywania wystąpień SQL Server i baz danych, odpowiednie wystąpienia są oznaczane jako **nieznane** w raporcie oceny.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak utworzyć [ocenę usługi Azure SQL](./how-to-create-azure-sql-assessment.md)
- Dowiedz się więcej na temat [ocen usługi Azure SQL](./concepts-azure-sql-assessment-calculation.md)