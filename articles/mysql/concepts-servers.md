---
title: Pojęcia dotyczące serwera — Azure Database for MySQL
description: W tym temacie przedstawiono zagadnienia i wytyczne dotyczące pracy z serwerami Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5477aa0c4eb2a66af816b287a8b6d7da8a53952f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94534895"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Pojęcia dotyczące serwerów w Azure Database for MySQL

W tym artykule przedstawiono zagadnienia i wytyczne dotyczące pracy z serwerami Azure Database for MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Co to jest serwer Azure Database for MySQL?

Serwer Azure Database for MySQL jest centralnym punktem administracyjnym dla wielu baz danych. Jest to ta sama konstrukcja serwera MySQL, która może być znana na świecie lokalnym. Usługa Azure Database for MySQL jest zarządzana, zapewnia gwarancje wydajności i udostępnia dostęp i funkcje na poziomie serwera.

Serwer Azure Database for MySQL:

- Jest tworzony w ramach subskrypcji platformy Azure.
- Jest zasobem nadrzędnym dla baz danych.
- Udostępnia przestrzeń nazw dla baz danych.
- Jest kontenerem z semantyką silnego okresu istnienia — usuwa serwer i usuwa zawarte bazy danych.
- Kolokacja zasobów w regionie.
- Udostępnia punkt końcowy połączenia serwera i dostępu do bazy danych.
- Zapewnia zakres zasad zarządzania, które mają zastosowanie do swoich baz danych: logowania, zapory, użytkowników, ról, konfiguracji itp.
- Jest dostępny w wielu wersjach. Aby uzyskać więcej informacji, zobacz [obsługiwane wersje bazy danych Azure Database for MySQL](./concepts-supported-versions.md).

Na serwerze usługi Azure Database for MySQL można utworzyć jedną lub wiele baz danych. Możesz wybrać opcję tworzenia pojedynczej bazy danych na serwerze w celu używania wszystkich zasobów lub tworzenia wielu baz danych w celu udostępniania zasobów. Cennik ma strukturę na serwer, na podstawie konfiguracji warstwy cenowej, rdzeni wirtualnych i magazynu (GB). Aby uzyskać więcej informacji, zobacz [warstwy cenowe](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Jak mogę połączyć się i uwierzytelnić na serwerze Azure Database for MySQL?

Poniższe elementy zapewniają bezpieczny dostęp do bazy danych programu.

|     |     |
| :-- | :-- |
| **Uwierzytelnianie i autoryzacja** | Serwer Azure Database for MySQL obsługuje natywne uwierzytelnianie MySQL. Można nawiązać połączenie i uwierzytelnić się na serwerze z identyfikatorem logowania administratora serwera. |
| **Protokół** | Usługa obsługuje protokół oparty na komunikatach używany przez MySQL. |
| **TCP/IP** | Protokół jest obsługiwany za pośrednictwem protokołu TCP/IP i gniazd domen systemu UNIX. |
| **Zapora** | Aby zapewnić ochronę danych, reguła zapory zapobiega wszystkim dostępowi do serwera bazy danych, dopóki nie określisz, które komputery mają uprawnienia. Zobacz [reguły zapory serwera Azure Database for MySQL](./concepts-firewall-rules.md). |
| **Protokół SSL** | Usługa obsługuje wymuszanie połączeń SSL między aplikacjami a serwerem bazy danych.  Zobacz [Konfigurowanie łączności SSL w aplikacji w celu bezpiecznego nawiązywania połączeń z usługą Azure Database for MySQL](./howto-configure-ssl.md). |

## <a name="stopstart-an-azure-database-for-mysql-preview"></a>Zatrzymywanie/uruchamianie Azure Database for MySQL (wersja zapoznawcza)

Azure Database for MySQL daje możliwość **zatrzymania** serwera, gdy nie jest używany, i **uruchamiania** serwera podczas wznawiania działania. Ma to na celu zaoszczędzenie kosztów na serwerach bazy danych i płatność tylko za zasób, gdy jest używany. Jest to jeszcze ważniejsze w przypadku obciążeń deweloperskich i testowych, a w przypadku korzystania z serwera tylko dla części dnia. Po zatrzymaniu serwera wszystkie aktywne połączenia zostaną usunięte. Później, jeśli chcesz przywrócić serwer do trybu online, możesz użyć [Azure Portal](how-to-stop-start-server.md) lub [interfejsu wiersza polecenia](how-to-stop-start-server.md).

Gdy serwer jest w stanie **zatrzymania** , obliczenia serwera nie są naliczane. Magazyn jest jednak nadal rozliczany jako magazyn serwera, aby zapewnić dostępność plików danych po ponownym uruchomieniu serwera.

> [!IMPORTANT]
> **Zatrzymanie** serwera pozostanie w tym stanie w ciągu następnych 7 dni w rozciągnięciu. Jeśli nie **uruchomisz** go ręcznie w tym czasie, serwer zostanie automatycznie uruchomiony po upływie 7 dni. Jeśli nie używasz serwera, możesz go **zatrzymać** ponownie.

Gdy serwer czasu zostanie zatrzymany, na serwerze nie można wykonać żadnych operacji zarządzania. Aby zmienić ustawienia konfiguracji na serwerze, należy [uruchomić serwer](how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>Ograniczenia operacji zatrzymania/uruchamiania
- Nieobsługiwane w przypadku konfiguracji repliki odczytu (źródła i repliki).

## <a name="how-do-i-manage-a-server"></a>Jak mogę zarządzać serwerem?

Serwerami Azure Database for MySQL można zarządzać przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem usługi, zobacz [Azure Database for MySQL przegląd](./overview.md)
- Aby uzyskać informacje na temat konkretnych przydziałów zasobów i ograniczeń na podstawie **warstwy cenowej** , zobacz [warstwy cenowe](./concepts-pricing-tiers.md)
- Aby uzyskać informacje na temat nawiązywania połączenia z usługą, zobacz [biblioteki połączeń dla Azure Database for MySQL](./concepts-connection-libraries.md).
