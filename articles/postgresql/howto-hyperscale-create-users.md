---
title: Tworzenie użytkowników-skalowanie (Citus) — Azure Database for PostgreSQL
description: W tym artykule opisano, jak można tworzyć nowe konta użytkowników w celu współdziałania z Azure Database for PostgreSQL-Citus.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/8/2019
ms.openlocfilehash: 3d23ee6119b625e11ce44bb9ad11ce4b3ee0280d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91295740"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Tworzenie użytkowników w Azure Database for PostgreSQL-Citus

> [!NOTE]
> Termin "Użytkownicy" odnosi się do użytkowników w grupie serwerów wieloskalowania (Citus). Aby dowiedzieć się więcej o użytkownikach subskrypcji platformy Azure i ich uprawnieniach, odwiedź [artykuł kontrola dostępu oparta na rolach na platformie Azure (RBAC)](../role-based-access-control/built-in-roles.md) lub zapoznaj [się z tematem Dostosowywanie ról](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Konto administratora serwera

Aparat PostgreSQL używa [ról](https://www.postgresql.org/docs/current/sql-createrole.html) w celu kontrolowania dostępu do obiektów bazy danych, a nowo utworzona grupa serwerów w postaci Citusej zawiera kilka wstępnie zdefiniowanych ról:

* [Domyślne role PostgreSQL](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Ze względu na to, że funkcja Citus jest zarządzaną usługą PaaS, tylko firma Microsoft może zalogować się przy użyciu `postgres` roli administratora. W przypadku ograniczonego dostępu administracyjnego funkcja przeskalowania (Citus) zapewnia `citus` rolę.

Uprawnienia dla `citus` roli:

* Odczytaj wszystkie zmienne konfiguracyjne, nawet zmienne zwykle widoczne tylko dla użytkowników.
* Odczytywanie wszystkich \_ \_ \* widoków informacji o widoku PG i korzystanie z różnych rozszerzeń związanych z statystyką — nawet widoki lub rozszerzenia są zwykle widoczne tylko dla użytkowników.
* Wykonywanie funkcji monitorowania, które mogą mieć dostęp do blokad udostępniania w tabelach, potencjalnie przez długi czas.
* [Utwórz rozszerzenia PostgreSQL](concepts-hyperscale-extensions.md) (ponieważ rola jest członkiem `azure_pg_admin` ).

W szczególności `citus` rola ma pewne ograniczenia:

* Nie można utworzyć ról
* Nie można utworzyć baz danych

## <a name="how-to-create-additional-user-roles"></a>Jak utworzyć dodatkowe role użytkowników

Jak wspomniano wcześniej, `citus` konto administratora nie ma uprawnień do tworzenia dodatkowych użytkowników. Aby dodać użytkownika, użyj interfejsu Azure Portal.

1. Przejdź do strony **role** dla grupy serwerów moja skala (Citus), a następnie kliknij pozycję **+ Dodaj**:

   :::image type="content" source="media/howto-hyperscale-create-users/1-role-page.png" alt-text="Strona role":::

2. Wprowadź nazwę i hasło roli. Kliknij pozycję **Zapisz**.

   :::image type="content" source="media/howto-hyperscale-create-users/2-add-user-fields.png" alt-text="Dodaj rolę":::

Użytkownik zostanie utworzony w węźle koordynatora grupy serwerów i rozpropagowany do wszystkich węzłów procesu roboczego. Role utworzone za pośrednictwem Azure Portal mają `LOGIN` atrybutu, co oznacza, że są one prawdziwe użytkownikom, którzy mogą logować się do bazy danych.

## <a name="how-to-modify-privileges-for-user-role"></a>Jak zmodyfikować uprawnienia dla roli użytkownika

Nowe role użytkowników są często używane do zapewniania dostępu do bazy danych z ograniczonymi uprawnieniami. Aby zmodyfikować uprawnienia użytkownika, użyj standardowych poleceń PostgreSQL przy użyciu narzędzia, takiego jak PgAdmin lub PSQL. (Zobacz [nawiązywanie połączenia z usługą PSQL](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) na stronie Szybki Start skalowania (Citus).

Na przykład, aby zezwolić `db_user` na odczytywanie `mytable` , Udziel uprawnienia:

```sql
GRANT SELECT ON mytable TO db_user;
```

Funkcja wieloskal (Citus) propaguje instrukcje GRANTu jednej tabeli w całym klastrze, stosując je we wszystkich węzłach procesu roboczego. Propaguje także dotacje dla całego systemu (np. dla wszystkich tabel w schemacie):

```sql
-- applies to the coordinator node and propagates to workers
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Jak usunąć rolę użytkownika lub zmienić hasło

Aby zaktualizować użytkownika, odwiedź stronę **role** dla grupy serwerów moja skala (Citus), a następnie kliknij przycisk wielokropka **...** obok użytkownika. Wielokropek spowoduje otwarcie menu w celu usunięcia użytkownika lub zresetowania hasła.

   :::image type="content" source="media/howto-hyperscale-create-users/edit-role.png" alt-text="Edytowanie roli":::

`citus`Rola jest uprzywilejowana i nie można jej usunąć.

## <a name="next-steps"></a>Następne kroki

Otwórz Zaporę dla adresów IP nowych maszyn użytkowników, aby umożliwić im łączenie: [Utwórz reguły zapory Citus (i zarządzaj nimi) przy użyciu Azure Portal](howto-hyperscale-manage-firewall-using-portal.md).

Aby uzyskać więcej informacji na temat zarządzania kontami użytkowników bazy danych, zobacz dokumentację produktu PostgreSQL:

* [Role i uprawnienia bazy danych](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Składnia GRANTu](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Uprawnienia](https://www.postgresql.org/docs/current/static/ddl-priv.html)
