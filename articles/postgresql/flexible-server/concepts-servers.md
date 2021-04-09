---
title: Serwery w Azure Database for PostgreSQL — serwer elastyczny (wersja zapoznawcza)
description: Ten artykuł zawiera zagadnienia i wskazówki dotyczące konfigurowania serwera Azure Database for PostgreSQL-elastycznym i zarządzania nim.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 60edbcf97b43747246e84517cc65bb0dca4ff546
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626847"
---
# <a name="servers---azure-database-for-postgresql---flexible-server"></a>Serwery — serwer elastyczny Azure Database for PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

W tym artykule przedstawiono zagadnienia i wytyczne dotyczące pracy z serwerem elastycznym Azure Database for PostgreSQL.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Co to jest serwer usługi Azure Database for PostgreSQL?

Serwer w opcji wdrożenia serwera Azure Database for PostgreSQL-elastyczny jest centralnym punktem administracyjnym dla wielu baz danych. Jest to ta sama konstrukcja serwera PostgreSQL, która może być znana na świecie lokalnym. Usługa PostgreSQL jest zarządzana, zapewnia gwarancje wydajności, udostępnia dostęp i funkcje na poziomie serwera.

Serwer Azure Database for PostgreSQL:

- Jest tworzony w ramach subskrypcji platformy Azure.
- Jest zasobem nadrzędnym dla baz danych.
- Udostępnia przestrzeń nazw dla baz danych.
- Jest kontenerem z semantyką silnego okresu istnienia — usuwa serwer i usuwa zawarte bazy danych.
- Kolokacja zasobów w regionie.
- Zapewnia punkt końcowy połączenia dla dostępu do serwera i bazy danych
- Zapewnia zakres zasad zarządzania, które mają zastosowanie do swoich baz danych: logowania, zapory, użytkowników, ról, konfiguracji itp.
- Jest dostępny w wielu wersjach. Aby uzyskać więcej informacji, zobacz [obsługiwane wersje bazy danych PostgreSQL](concepts-supported-versions.md).
- Jest rozszerzalny przez użytkowników. Aby uzyskać więcej informacji, zobacz [rozszerzenia PostgreSQL](concepts-extensions.md).

Na serwerze Azure Database for PostgreSQL można utworzyć jedną lub wiele baz danych. Można wybrać opcję utworzenia jednej bazy danych na serwer w celu wykorzystania wszystkich zasobów. Można też utworzyć wiele baz danych, współdzielących zasoby. Cennik ma strukturę na serwer, na podstawie konfiguracji warstwy cenowej, rdzeni wirtualnych i magazynu (GB). Aby uzyskać więcej informacji, zobacz [Opcje obliczeń i magazynu](concepts-compute-storage.md).

## <a name="how-do-i-connect-and-authenticate-to-the-database-server"></a>Jak mogę nawiązać połączenia i uwierzytelniać się na serwerze bazy danych?

Następujące elementy pomagają zapewnić bezpieczny dostęp do bazy danych:

| Koncepcja zabezpieczeń | Opis |
| :-- | :-- |
| **Uwierzytelnianie i autoryzacja** | Serwer usługi Azure Database for PostgreSQL obsługuje natywne uwierzytelnianie bazy danych PostgreSQL. Aby nawiązać połączenie z serwerem i przeprowadzić uwierzytelnianie na serwerze, można użyć identyfikatora logowania administratora serwera. |
| **Protokół** | Usługa obsługuje protokół oparty na komunikatach używany przez PostgreSQL. |
| **TCP/IP** | Protokół jest obsługiwany za pośrednictwem protokołu TCP/IP i gniazd domen systemu UNIX. |
| **Zapora** | Aby zapewnić ochronę danych, reguła zapory zapobiega wszystkim dostępowi do serwera i jego baz danych do momentu określenia komputerów, które mają uprawnienia. Zobacz [reguły zapory serwera Azure Database for PostgreSQL](how-to-manage-firewall-portal.md). |

## <a name="managing-your-server"></a>Zarządzanie serwerem

Serwerami Azure Database for PostgreSQL można zarządzać przy użyciu [Azure Portal](https://portal.azure.com) lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/postgres).

Podczas tworzenia serwera należy skonfigurować poświadczenia dla użytkownika administratora. Administrator jest użytkownikiem o najwyższym poziomie uprawnień na serwerze. Należy do roli azure_pg_admin. Ta rola nie ma pełnych uprawnień administratora. 

Atrybut PostgreSQL administratora jest przypisany do azure_superuser, który należy do usługi zarządzanej. Nie masz dostępu do tej roli.

Serwer Azure Database for PostgreSQL ma domyślne bazy danych: 

- **Postgres** — domyślna baza danych, z którą można nawiązać połączenie po utworzeniu serwera.
- **azure_maintenance** — ta baza danych służy do rozdzielania procesów, które udostępniają usługę zarządzaną z akcji użytkownika. Nie masz dostępu do tej bazy danych.

## <a name="server-parameters"></a>Parametry serwera

Parametry serwera PostgreSQL określają konfigurację serwera. W Azure Database for PostgreSQL można wyświetlać i edytować listę parametrów przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure.

Jako usługa zarządzana dla Postgres, konfigurowalne parametry w Azure Database for PostgreSQL są podzbiorem parametrów w lokalnym wystąpieniu usługi Postgres (Aby uzyskać więcej informacji na temat Postgres parametrów, zobacz [dokumentację dotyczącą PostgreSQL](https://www.postgresql.org/docs/12/static/runtime-config.html)). Serwer Azure Database for PostgreSQL jest włączony z wartościami domyślnymi dla każdego parametru podczas tworzenia. Niektóre parametry, które wymagają ponownego uruchomienia serwera lub administratora dostępu do zmian, nie mogą zostać skonfigurowane przez użytkownika.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem usługi, zobacz [Azure Database for PostgreSQL Omówienie](overview.md).
- Aby uzyskać informacje na temat konkretnych przydziałów zasobów i ograniczeń na podstawie **konfiguracji**, zobacz [Opcje obliczeń i magazynu](concepts-compute-storage.md).
- Wyświetlanie i edytowanie parametrów serwera za pomocą [Azure Portal](howto-configure-server-parameters-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).
