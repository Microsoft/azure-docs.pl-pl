---
title: Azure Defender for SQL
description: Dowiedz się więcej o funkcji zarządzania lukami w bazie danych i wykrywaniu nietypowych działań, które mogą wskazywać na zagrożenie dla bazy danych w Azure SQL Database, wystąpieniu zarządzanym Azure SQL lub Azure Synapse.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: d567876e0210c025fa34c5b82791eafe4cdff561
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372157"
---
# <a name="azure-defender-for-sql"></a>Azure Defender for SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Usługa Azure Defender for SQL to ujednolicony pakiet na potrzeby zaawansowanych funkcji zabezpieczeń SQL. Usługa Azure Defender jest dostępna do Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i usługi Azure Synapse Analytics. Obejmuje to funkcję wykrywania i klasyfikowania danych poufnych, uwidacznianie i korygowanie potencjalnych luk w zabezpieczeniach bazy danych oraz wykrywanie nietypowych działań, które mogą wskazywać, że baza danych jest zagrożona. Zapewnia ona pojedynczą lokalizację, w której można włączać te możliwości i zarządzać nimi.

## <a name="overview"></a>Omówienie

Usługa Azure Defender oferuje zestaw zaawansowanych funkcji zabezpieczeń SQL, w tym ocenę luk w zabezpieczeniach SQL i zaawansowaną ochronę przed zagrożeniami.
- [Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md) to prosta w konfiguracji usługa, która umożliwia odnajdywanie, śledzenie i rozwiązywanie problemów z potencjalnymi lukami w zabezpieczeniach bazy danych. Zapewnia wgląd w stan zabezpieczeń i obejmuje czynności do wykonania w celu rozwiązywania problemów z zabezpieczeniami i ulepszania bazy danych FORTIFICATIONS.
- Usługa [Advanced Threat Protection](threat-detection-overview.md) wykrywa nietypowe działania wskazujące na nieprawidłowe i potencjalnie szkodliwe próby uzyskania dostępu do bazy danych lub wykorzystania jej. Ciągle monitoruje bazę danych pod kątem podejrzanych działań i zapewnia natychmiastowe alerty zabezpieczeń dotyczące potencjalnych luk w zabezpieczeniach, ataki iniekcji SQL Azure oraz nietypowe wzorce dostępu do bazy danych. Alerty usługi Advanced Threat Protection zawierają szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia.

Włącz usługę Azure Defender dla programu SQL raz, aby włączyć wszystkie te funkcje. Po jednym kliknięciu można włączyć usługę Azure Defender dla wszystkich baz danych na [serwerze](logical-servers.md) na platformie Azure lub w wystąpieniu zarządzanym SQL. Włączanie ustawień usługi Azure Defender lub zarządzanie nimi wymaga przynależności do roli [programu SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) lub jednej z ról administratora bazy danych lub serwera.

Aby uzyskać więcej informacji na temat cennika usługi Azure Defender for SQL, zobacz [stronę z cennikiem Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-azure-defender"></a>Wprowadzenie do usługi Azure Defender

Poniższe kroki ułatwiają rozpoczęcie pracy z usługą Azure Defender.

## <a name="enable-azure-defender"></a>Włączanie usługi Azure Defender

Dostęp do usługi Azure Defender można uzyskać za pomocą [Azure Portal](https://portal.azure.com). Włącz usługę Azure Defender, przechodząc do usługi **Security Center** w obszarze nagłówka **zabezpieczenia** dla serwera lub wystąpienia zarządzanego.

> [!NOTE]
> Konto magazynu jest automatycznie tworzone i konfigurowane do przechowywania wyników skanowania **oceny luk w zabezpieczeniach** . Jeśli usługa Azure Defender została już włączona dla innego serwera w tej samej grupie zasobów i regionie, używane jest istniejące konto magazynu.
>
> Koszt usługi Azure Defender jest wyrównany przy Azure Security Center użyciu cen warstwy Standardowa na węzeł, gdzie węzeł jest całym serwerem lub wystąpieniem zarządzanym. W ten sposób płacisz tylko raz na ochronę wszystkich baz danych na serwerze lub w wystąpieniu zarządzanym za pomocą usługi Azure Defender. Możesz wstępnie wypróbować usługę Azure Defender, korzystając z bezpłatnej wersji próbnej.

## <a name="start-tracking-vulnerabilities-and-investigating-threat-alerts"></a>Rozpocznij śledzenie luk w zabezpieczeniach i badanie alertów dotyczących zagrożeń

Kliknij kartę **Ocena luk w zabezpieczeniach** , aby wyświetlić i zarządzać skanowaniem i raportami luk w zabezpieczeniach oraz śledzić schemacie zabezpieczeń. Jeśli alerty zabezpieczeń zostały odebrane, kliknij kartę **Zaawansowana ochrona przed zagrożeniami** , aby wyświetlić szczegóły alertów i wyświetlić skonsolidowany raport dotyczący wszystkich alertów w ramach subskrypcji platformy Azure za pośrednictwem strony alerty zabezpieczeń Azure Security Center.

## <a name="manage-azure-defender-settings"></a>Zarządzanie ustawieniami usługi Azure Defender

Aby wyświetlić ustawienia usługi Azure Defender i zarządzać nimi, przejdź do usługi **Security Center** w nagłówku **zabezpieczeń** serwera lub wystąpienia zarządzanego. Na tej stronie można włączyć lub wyłączyć usługę Azure Defender oraz zmodyfikować ustawienia oceny luk w zabezpieczeniach i zaawansowanej ochrony przed zagrożeniami dla całego serwera lub wystąpienia zarządzanego.

## <a name="manage-azure-defender-settings-for-a-database"></a>Zarządzanie ustawieniami usługi Azure Defender dla bazy danych

Aby zastąpić ustawienia usługi Azure Defender dla konkretnej bazy danych, zaznacz pole wyboru **Włącz usługę Azure Defender dla programu SQL na poziomie bazy danych** . Tej opcji należy używać tylko w przypadku, gdy istnieje szczególny wymóg otrzymywania osobnych alertów dotyczących zaawansowanej ochrony przed zagrożeniami lub oceny luk w zabezpieczeniach dla pojedynczej bazy danych zamiast alertów i odebranych wyników dla wszystkich baz danych na serwerze lub w zarządzanym wystąpieniu.

Po zaznaczeniu pola wyboru można skonfigurować odpowiednie ustawienia dla tej bazy danych.

Ustawienia usługi Azure Defender dla serwera lub wystąpienia zarządzanego można również uzyskać z okienka bazy danych usługi Azure Defender. Kliknij pozycję **Ustawienia** w głównym okienku usługi Azure Defender, a następnie kliknij pozycję **Wyświetl ustawienia usługi Azure Defender dla programu SQL Server**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [oceny luk w zabezpieczeniach](sql-vulnerability-assessment.md)
- Dowiedz się więcej na temat [zaawansowanej ochrony przed zagrożeniami](threat-detection-configure.md)
- Dowiedz się więcej o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
