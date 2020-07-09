---
title: Pojęcia dotyczące serwera — Azure Database for MySQL
description: W tym temacie przedstawiono zagadnienia i wytyczne dotyczące pracy z serwerami Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 300470b2e8fb10fda7cfc59517cef00c07bf0632
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79537010"
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

Na serwerze usługi Azure Database for MySQL można utworzyć jedną lub wiele baz danych. Możesz wybrać opcję tworzenia pojedynczej bazy danych na serwerze w celu używania wszystkich zasobów lub tworzenia wielu baz danych w celu udostępniania zasobów. Cennik ma strukturę na serwer, na podstawie konfiguracji warstwy cenowej, rdzeni wirtualnych i magazynu (GB). Aby uzyskać więcej informacji, zobacz [warstwy cenowe](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Jak mogę połączyć się i uwierzytelnić na serwerze Azure Database for MySQL?

Poniższe elementy zapewniają bezpieczny dostęp do bazy danych programu.

|     |     |
| :-- | :-- |
| **Uwierzytelnianie i autoryzacja** | Serwer Azure Database for MySQL obsługuje natywne uwierzytelnianie MySQL. Można nawiązać połączenie i uwierzytelnić się na serwerze z identyfikatorem logowania administratora serwera. |
| **Protokół** | Usługa obsługuje protokół oparty na komunikatach używany przez MySQL. |
| **TCP/IP** | Protokół jest obsługiwany za pośrednictwem protokołu TCP/IP i gniazd domen systemu UNIX. |
| **Zapora** | Aby zapewnić ochronę danych, reguła zapory zapobiega wszystkim dostępowi do serwera bazy danych, dopóki nie określisz, które komputery mają uprawnienia. Zobacz [reguły zapory serwera Azure Database for MySQL](./concepts-firewall-rules.md). |
| **Protokół SSL** | Usługa obsługuje wymuszanie połączeń SSL między aplikacjami a serwerem bazy danych.  Zobacz [Konfigurowanie łączności SSL w aplikacji w celu bezpiecznego nawiązywania połączeń z usługą Azure Database for MySQL](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Jak mogę zarządzać serwerem?

Serwerami Azure Database for MySQL można zarządzać przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem usługi, zobacz [Azure Database for MySQL przegląd](./overview.md)
- Aby uzyskać informacje na temat konkretnych przydziałów zasobów i ograniczeń na podstawie **warstwy usług**, zobacz [warstwy usług](./concepts-service-tiers.md)
- Aby uzyskać informacje na temat nawiązywania połączenia z usługą, zobacz [biblioteki połączeń dla Azure Database for MySQL](./concepts-connection-libraries.md).
