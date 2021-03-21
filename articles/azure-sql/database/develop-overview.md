---
title: Omówienie tworzenia aplikacji
description: Informacje o dostępnych bibliotekach łączności i najlepsze praktyki dotyczące aplikacji łączących się z usługą SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.custom: sqldbrb=2
ms.openlocfilehash: 7ea3456dbd1d0942cba48c97d70982ae088d73f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92782981"
---
# <a name="application-development-overview---sql-database--sql-managed-instance"></a>Przegląd opracowywania aplikacji — SQL Database & wystąpienie zarządzane SQL

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

W tym artykule omówiono podstawowe zagadnienia, które należy znać deweloper podczas pisania kodu w celu nawiązania połączenia z bazą danych na platformie Azure. Ten artykuł ma zastosowanie do Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL.

## <a name="language-and-platform"></a>Język i platforma

Możesz użyć różnych [języków programowania i platform](connect-query-content-reference-guide.md) do nawiązywania połączeń i Azure SQL Database zapytań. Możesz znaleźć [przykładowe aplikacje](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) , których można użyć do nawiązania połączenia z bazą danych.

Możesz korzystać z narzędzi typu "open source", takich jak [Cheetah](https://github.com/wunderlist/cheetah), [SQL-CLI](https://www.npmjs.com/package/sql-cli), [vs Code](https://code.visualstudio.com/). Ponadto usługa Azure SQL Database współpracuje z narzędziami firmy Microsoft, takimi jak [Visual Studio](https://www.visualstudio.com/downloads/) i [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms). Możesz również użyć interfejsów API Azure Portal, PowerShell i REST, aby zwiększyć produktywność.

## <a name="authentication"></a>Authentication

Dostęp do Azure SQL Database jest chroniony za pomocą nazw logowania i zapór. Azure SQL Database obsługuje zarówno użytkowników uwierzytelniania SQL Server, jak i [Azure Active Directory](authentication-aad-overview.md) logowania. Identyfikatory logowania Azure Active Directory są dostępne tylko w wystąpieniu zarządzanym SQL. 

Dowiedz się więcej na temat [zarządzania dostępem do bazy danych i logowaniem](logins-create-manage.md).

## <a name="connections"></a>Połączenia

W logice połączenia klienta zastąp domyślny limit czasu wartością 30 sekund. Domyślna wartość 15 sekund jest zbyt mała w przypadku połączeń zależnych od Internetu.

Jeśli korzystasz z [puli połączeń](/dotnet/framework/data/adonet/sql-server-connection-pooling), pamiętaj o zamknięciu połączenia, gdy tylko Twój program nie korzysta z niego aktywnie i nie przygotowuje się do jego ponownego użycia.

Unikaj długotrwałych transakcji, ponieważ Jakakolwiek awaria infrastruktury lub połączenia może spowodować wycofanie transakcji. Jeśli to możliwe, Podziel transakcję w wielu mniejszych transakcjach i użyj operacji [wsadowych w celu zwiększenia wydajności](../performance-improve-use-batching.md).

## <a name="resiliency"></a>Odporność

Azure SQL Database to usługa w chmurze, w której można oczekiwać błędów przejściowych występujących w podstawowej infrastrukturze lub komunikacji między jednostkami w chmurze. Choć Azure SQL Database jest odporny na awarie infrastruktury przechodniej, te błędy mogą mieć wpływ na łączność. Gdy wystąpi błąd przejściowy podczas nawiązywania połączenia z SQL Database, kod powinien [ponowić próbę wywołania](troubleshoot-common-connectivity-issues.md). Zalecamy, aby logika ponawiania używała logiki wycofywania, więc nie spowoduje to przeciążenia usługi wieloma klientami jednocześnie. Logika ponawiania zależy od [komunikatów o błędach dla SQL Database programów klienckich](troubleshoot-common-errors-issues.md).

Aby uzyskać więcej informacji o sposobach przygotowania do planowanych zdarzeń konserwacji na Azure SQL Database, zobacz [Planowanie zdarzeń konserwacji platformy Azure w programie Azure SQL Database](planned-maintenance.md).

## <a name="network-considerations"></a>Kwestie dotyczące sieci

- Upewnij się, że zapora na komputerze hostującym program kliencki zezwala na wychodzącą komunikację TCP na porcie 1433.  Więcej informacji: [Konfigurowanie zapory Azure SQL Database](firewall-configure.md).
- Jeśli program kliencki nawiązuje połączenie z SQL Database, gdy klient działa na maszynie wirtualnej platformy Azure (VM), należy otworzyć niektóre zakresy portów na maszynie wirtualnej. Więcej informacji: [porty przekraczające 1433 dla ADO.NET 4,5 i SQL Database](adonet-v12-develop-direct-route-ports.md).
- Połączenia klientów z Azure SQL Database czasami pomijają serwer proxy i współpracują bezpośrednio z bazą danych. Porty inne niż 1433 nabierają znaczenia. Aby uzyskać więcej informacji, [Azure SQL Database architekturze](connectivity-architecture.md) i [portów łączności ponad 1433 dla ADO.NET 4,5 i SQL Database](adonet-v12-develop-direct-route-ports.md).
- Aby uzyskać konfigurację sieci dla wystąpienia wystąpienia zarządzanego SQL, zobacz [Konfiguracja sieci dla wystąpienia zarządzanego SQL](../managed-instance/how-to-content-reference-guide.md#network-configuration).

## <a name="next-steps"></a>Następne kroki

Poznaj wszystkie możliwości [SQL Database](sql-database-paas-overview.md) i [wystąpienia zarządzanego SQL](../managed-instance/sql-managed-instance-paas-overview.md).

Aby rozpocząć, zapoznaj się z przewodnikami dla [Azure SQL Database](quickstart-content-reference-guide.md) i [wystąpieniami zarządzanymi usługi Azure SQL](../managed-instance/quickstart-content-reference-guide.md).