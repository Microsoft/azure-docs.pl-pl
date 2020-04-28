---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789204"
---
* Jeśli używasz Azure SQL Database, postępuj zgodnie z instrukcjami w sekcji [Połącz z Azure SQL Database](#connect-azure-sql-db).

* Jeśli używasz SQL Server, postępuj zgodnie z instrukcjami w sekcji [Połącz z SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Połącz z Azure SQL Database

Gdy wyzwalacz SQL lub akcja monituje o informacje o połączeniu, wykonaj następujące kroki, które działają dla obu wyzwalaczy i akcji.

1. W obszarze **Nazwa połączenia**Utwórz nazwę połączenia.

1. W obszarze **nazwa SQL Server**wybierz swój serwer Azure SQL. Gdy zostanie wyświetlona lista **nazwa SQL Database** , wybierz swoją bazę danych. Podaj nazwę użytkownika i hasło do serwera Azure SQL.

   Te informacje można również znaleźć w Azure Portal w obszarze właściwości bazy danych SQL lub w parametrach połączenia:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Utwórz połączenie z Azure SQL Database](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Po utworzeniu połączenia Kontynuuj [Dodawanie wyzwalacza SQL](#add-sql-trigger) lub [Dodaj akcję SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Ustanawianie połączenia z programem SQL Server

Gdy wyzwalacz SQL lub akcja monituje o informacje o połączeniu, wykonaj następujące kroki, które działają dla obu wyzwalaczy i akcji. W przypadku scenariuszy, które wymagają zainstalowania lokalnej [bramy danych](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) na komputerze lokalnym i [utworzenia zasobu usługi Azure Data Gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection), należy najpierw wykonać te wymagania. W przeciwnym razie zasób bramy nie będzie wyświetlany na liście bram podczas tworzenia połączenia.

Ponadto aby użyć uwierzytelniania systemu Windows z łącznikiem SQL Server w [środowisku usługi integracji (ISE)](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview), użyj wersji ISE łącznika i lokalnej bramy danych. ISE wersja nie obsługuje uwierzytelniania systemu Windows.

1. W obszarze **Nazwa połączenia**Utwórz nazwę połączenia.

1. W wyzwalaczu lub akcji wybierz opcję **Połącz za pośrednictwem lokalnej bramy danych** , aby wyświetlić opcje programu SQL Server.

1. W polu **Nazwa serwera SQL** i **Nazwa bazy danych SQL**Podaj adres serwera SQL i nazwę bazy danych. Podaj nazwę użytkownika **Password** **i hasło** dla serwera.

   Te informacje można również znaleźć w parametrach połączenia:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Utwórz połączenie z SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Jeśli program SQL Server używa uwierzytelniania systemu Windows lub uwierzytelnianie podstawowe, wybierz **Typ uwierzytelniania**.

1. W obszarze **bramy**wybierz subskrypcję platformy Azure, która jest skojarzona z wcześniej utworzoną lokalną bramą danych, a następnie wybierz nazwę lokalnej bramy danych.

   Jeśli Brama nie znajduje się na liście, sprawdź poprawność [konfiguracji bramy](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   ![Ukończono tworzenie połączenia SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Po utworzeniu połączenia Kontynuuj [Dodawanie wyzwalacza SQL](#add-sql-trigger) lub [Dodaj akcję SQL](#add-sql-action).
