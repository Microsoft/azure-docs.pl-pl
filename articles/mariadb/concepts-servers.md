---
title: Serwery — Azure Database for MariaDB
description: W tym temacie przedstawiono zagadnienia i wytyczne dotyczące pracy z serwerami Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 444d7f1574cf1517b01250bcb9d810731030182d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "79527796"
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

## <a name="how-do-i-manage-a-server"></a>Jak mogę zarządzać serwerem?
Serwerami Azure Database for MariaDB można zarządzać przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="next-steps"></a>Następne kroki
- Aby zapoznać się z omówieniem usługi, zobacz [Azure Database for MariaDB przegląd](./overview.md)
- Aby uzyskać informacje na temat konkretnych przydziałów zasobów i ograniczeń na podstawie **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
