---
title: Serwery — Azure Database for MariaDB
description: W tym temacie przedstawiono zagadnienia i wytyczne dotyczące pracy z serwerami Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: abe17556d9ff62c44a33bfe4c4546a284785522e
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664133"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Pojęcia dotyczące serwerów w Azure Database for MariaDB
W tym artykule przedstawiono zagadnienia i wytyczne dotyczące pracy z serwerami Azure Database for MariaDB.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Co to jest serwer Azure Database for MariaDB?

Serwer Azure Database for MariaDB jest centralnym punktem administracyjnym dla wielu baz danych. Jest to ta sama konstrukcja serwera MariaDB, która może być znana na świecie lokalnym. Usługa Azure Database for MariaDB jest zarządzana, zapewnia gwarancje wydajności i udostępnia dostęp i funkcje na poziomie serwera.

Serwer Azure Database for MariaDB:

- Jest tworzony w ramach subskrypcji platformy Azure.
- Jest zasobem nadrzędnym dla baz danych.
- Udostępnia przestrzeń nazw dla baz danych.
- Jest kontenerem z semantyką silnego okresu istnienia — usuwa serwer i usuwa zawarte bazy danych.
- Kolokacja zasobów w regionie.
- Udostępnia punkt końcowy połączenia serwera i dostępu do bazy danych.
- Zapewnia zakres zasad zarządzania, które mają zastosowanie do swoich baz danych: logowania, zapory, użytkowników, ról, konfiguracji itp.
- Jest dostępny w aparacie MariaDB w wersji 10,2. Aby uzyskać więcej informacji, zobacz [obsługiwane wersje bazy danych Azure Database for MariaDB](./concepts-supported-versions.md).

Na serwerze usługi Azure Database for MariaDB można utworzyć jedną lub wiele baz danych. Możesz wybrać opcję tworzenia pojedynczej bazy danych na serwerze w celu używania wszystkich zasobów lub tworzenia wielu baz danych w celu udostępniania zasobów. Cennik ma strukturę na serwer, na podstawie konfiguracji warstwy cenowej, rdzeni wirtualnych i magazynu (GB). Aby uzyskać więcej informacji, zobacz [warstwy cenowe](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Jak mogę zabezpieczyć serwer Azure Database for MariaDB?

Poniższe elementy zapewniają bezpieczny dostęp do bazy danych programu.

|||
| :--| :--|
| **Uwierzytelnianie i autoryzacja** | Serwer Azure Database for MariaDB obsługuje natywne uwierzytelnianie MySQL. Można nawiązać połączenie i uwierzytelnić się na serwerze z identyfikatorem logowania administratora serwera. |
| **Protokół** | Usługa obsługuje protokół oparty na komunikatach używany przez MySQL. |
| **TCP/IP** | Protokół jest obsługiwany za pośrednictwem protokołu TCP/IP i gniazd domen systemu UNIX. |
| **Zapora** | Aby zapewnić ochronę danych, reguła zapory zapobiega wszystkim dostępowi do serwera bazy danych, dopóki nie określisz, które komputery mają uprawnienia. Zobacz [reguły zapory serwera Azure Database for MariaDB](./concepts-firewall-rules.md). |
| **Protokół SSL** | Usługa obsługuje wymuszanie połączeń SSL między aplikacjami a serwerem bazy danych. Zobacz [Konfigurowanie łączności SSL w aplikacji, aby bezpiecznie połączyć się z Azure Database for MariaDB](./howto-configure-ssl.md). |

## <a name="stopstart-an-azure-database-for-mariadb-preview"></a>Zatrzymywanie/uruchamianie Azure Database for MariaDB (wersja zapoznawcza)
Azure Database for MariaDB daje możliwość **zatrzymania** serwera, gdy nie jest używany, i **uruchamiania** serwera podczas wznawiania działania. Ma to na celu zaoszczędzenie kosztów na serwerach bazy danych i płatność tylko za zasób, gdy jest używany. Jest to jeszcze ważniejsze w przypadku obciążeń deweloperskich i testowych, a w przypadku korzystania z serwera tylko dla części dnia. Po zatrzymaniu serwera wszystkie aktywne połączenia zostaną usunięte. Później, jeśli chcesz przywrócić serwer do trybu online, możesz użyć [Azure Portal](../mysql/how-to-stop-start-server.md) lub [interfejsu wiersza polecenia](../mysql/how-to-stop-start-server.md).

Gdy serwer jest w stanie **zatrzymania** , obliczenia serwera nie są naliczane. Magazyn jest jednak nadal rozliczany jako magazyn serwera, aby zapewnić dostępność plików danych po ponownym uruchomieniu serwera.

> [!IMPORTANT]
> **Zatrzymanie** serwera pozostanie w tym stanie w ciągu następnych 7 dni w rozciągnięciu. Jeśli nie **uruchomisz** go ręcznie w tym czasie, serwer zostanie automatycznie uruchomiony po upływie 7 dni. Jeśli nie używasz serwera, możesz go **zatrzymać** ponownie.

Gdy serwer czasu zostanie zatrzymany, na serwerze nie można wykonać żadnych operacji zarządzania. Aby zmienić ustawienia konfiguracji na serwerze, należy [uruchomić serwer](../mysql/how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>Ograniczenia operacji zatrzymania/uruchamiania
- Nieobsługiwane w przypadku konfiguracji repliki odczytu (źródła i repliki).

## <a name="how-do-i-manage-a-server"></a>Jak mogę zarządzać serwerem?
Serwerami Azure Database for MariaDB można zarządzać przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="next-steps"></a>Następne kroki
- Aby zapoznać się z omówieniem usługi, zobacz [Azure Database for MariaDB przegląd](./overview.md)
- Aby uzyskać informacje na temat konkretnych przydziałów zasobów i ograniczeń na podstawie **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
