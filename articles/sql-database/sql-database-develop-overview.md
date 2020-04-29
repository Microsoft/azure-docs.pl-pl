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
ms.openlocfilehash: 741906bbe9de68459b2e4a704a243fde4771b3a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80067325"
---
# <a name="sql-database-application-development-overview"></a>Omówienie tworzenia aplikacji SQL Database

W tym artykule przedstawiono podstawowe zagadnienia, jakie powinien mieć na uwadze programista podczas pisania kodu nawiązującego połączenie z usługą Azure SQL Database. Ten artykuł dotyczy wszystkich modeli wdrażania Azure SQL Database (pojedyncza baza danych, pule elastyczne, wystąpienie zarządzane).

> [!TIP]
> Zapoznaj się z przewodnikami wprowadzenie dla [pojedynczych baz danych](sql-database-single-database-quickstart-guide.md) i [wystąpień zarządzanych](sql-database-managed-instance-quickstart-guide.md) , jeśli musisz skonfigurować Azure SQL Database.
>

## <a name="language-and-platform"></a>Język i platforma

Możesz użyć różnych [języków programowania i platform](sql-database-connect-query.md) do nawiązywania połączeń i Azure SQL Database zapytań. Możesz znaleźć [przykładowe aplikacje](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) , których można użyć do nawiązania połączenia z Azure SQL Database.

Możesz korzystać z narzędzi typu "open source", takich jak [Cheetah](https://github.com/wunderlist/cheetah), [SQL-CLI](https://www.npmjs.com/package/sql-cli), [vs Code](https://code.visualstudio.com/). Ponadto usługa Azure SQL Database współpracuje z narzędziami firmy Microsoft, takimi jak [Visual Studio](https://www.visualstudio.com/downloads/) i [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Możesz również użyć interfejsów API Azure Portal, PowerShell i REST, aby zwiększyć produktywność.

## <a name="authentication"></a>Uwierzytelnianie

Dostęp do Azure SQL Database jest chroniony za pomocą nazw logowania i zapór. Azure SQL Database obsługuje zarówno użytkowników uwierzytelniania SQL Server i [Azure Active Directory (AAD)](sql-database-aad-authentication.md) , jak i logowania. Nazwy logowania usługi AAD są dostępne tylko w wystąpieniu zarządzanym. 

Dowiedz się więcej na temat [zarządzania dostępem do bazy danych i logowaniem](sql-database-manage-logins.md).

## <a name="connections"></a>Połączenia

W logice połączenia klienta zastąp domyślny limit czasu wartością 30 sekund. Domyślna wartość 15 sekund jest zbyt mała w przypadku połączeń zależnych od Internetu.

Jeśli korzystasz z [puli połączeń](https://msdn.microsoft.com/library/8xx3tyca.aspx), pamiętaj o zamknięciu połączenia, gdy tylko Twój program nie korzysta z niego aktywnie i nie przygotowuje się do jego ponownego użycia.

Unikaj długotrwałych transakcji, ponieważ Jakakolwiek awaria infrastruktury lub połączenia może spowodować wycofanie transakcji. Jeśli to możliwe, Podziel transakcję w wielu mniejszych transakcjach i użyj operacji [wsadowych w celu zwiększenia wydajności](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Odporność

Azure SQL Database to usługa w chmurze, w której można oczekiwać błędów przejściowych występujących w podstawowej infrastrukturze lub komunikacji między jednostkami w chmurze. Choć Azure SQL Database jest odporny na awarie infrastruktury przechodniej, te błędy mogą mieć wpływ na łączność. Gdy wystąpi błąd przejściowy podczas nawiązywania połączenia z SQL Database, kod powinien [ponowić próbę wywołania](sql-database-connectivity-issues.md). Zalecamy, aby logika ponawiania korzystała z logiki wycofywania, co pozwoli uniknąć przeciążenia usługi SQL Database z powodu jednoczesnych ponownych prób ze strony wielu klientów. Logika ponawiania zależy od [komunikatów o błędach dla SQL Database programów klienckich](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md).

Aby uzyskać więcej informacji o sposobach przygotowania do planowanych zdarzeń konserwacji w usłudze Azure SQL Database, zobacz [Planowanie zdarzeń konserwacji platformy Azure w Azure SQL Database](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Kwestie dotyczące sieci

- Upewnij się, że zapora na komputerze hostującym program kliencki zezwala na wychodzącą komunikację TCP na porcie 1433.  Więcej informacji: [Konfigurowanie zapory Azure SQL Database](sql-database-configure-firewall-settings.md).
- Jeśli program kliencki nawiązuje połączenie z SQL Database, gdy klient działa na maszynie wirtualnej platformy Azure (VM), należy otworzyć niektóre zakresy portów na maszynie wirtualnej. Więcej informacji: [porty przekraczające 1433 dla ADO.NET 4,5 i SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Połączenia klientów z Azure SQL Database czasami pomijają serwer proxy i współpracują bezpośrednio z bazą danych. Porty inne niż 1433 nabierają znaczenia. Aby uzyskać więcej informacji, [Azure SQL Database architekturze](sql-database-connectivity-architecture.md) i [portów łączności ponad 1433 dla ADO.NET 4,5 i SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Aby uzyskać konfigurację sieci dla wystąpienia zarządzanego, zapoznaj się z tematem [Konfiguracja sieci dla wystąpień zarządzanych](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Następne kroki

Poznaj wszystkie [możliwości SQL Database](sql-database-technical-overview.md).
