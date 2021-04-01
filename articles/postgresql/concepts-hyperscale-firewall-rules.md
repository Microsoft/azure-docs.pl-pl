---
title: Reguły zapory-skalowanie (Citus) — Azure Database for PostgreSQL
description: W tym artykule opisano reguły zapory dla Azure Database for PostgreSQL-Citus.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 559c5eca6fa8a6eceb37ade003d4f1983c0a1a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90902093"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Reguły zapory w Azure Database for PostgreSQL-Citus
Zapora serwera Azure Database for PostgreSQL zapobiega wszystkim dostępowi do węzła koordynatora w skali (Citus) do momentu określenia komputerów, które mają uprawnienia. Zapora przyznaje dostęp do serwera na podstawie źródłowego adresu IP każdego żądania.
Aby skonfigurować zaporę, należy utworzyć reguły zapory określające zakresy dopuszczalnych adresów IP. Reguły zapory można tworzyć na poziomie serwera.

**Reguły zapory:** Te reguły umożliwiają klientom dostęp do węzła koordynatora Citus, czyli wszystkich baz danych na tym samym serwerze logicznym. Reguły zapory na poziomie serwera można skonfigurować za pomocą Azure Portal. Aby utworzyć reguły zapory na poziomie serwera, musisz być właścicielem subskrypcji lub współautorem subskrypcji.

## <a name="firewall-overview"></a>Omówienie zapory
Domyślnie dostęp do węzła koordynatora jest blokowany przez zaporę. Aby rozpocząć korzystanie z serwera z innego komputera, należy określić co najmniej jedną regułę zapory na poziomie serwera, aby umożliwić dostęp do serwera. Użyj reguł zapory, aby określić, które zakresy adresów IP z Internetu mają być dozwolone. Reguły zapory nie wpływają na dostęp do samej witryny sieci Web Azure Portal.
Próby połączenia z Internetu i platformy Azure muszą być przekazywane przez zaporę przed uzyskaniem dostępu do bazy danych PostgreSQL, jak pokazano na poniższym diagramie:

:::image type="content" source="media/concepts-hyperscale-firewall-rules/1-firewall-concept.png" alt-text="Przykładowy przepływ działania zapory":::

## <a name="connecting-from-the-internet-and-from-azure"></a>Łączenie z Internetu i z platformy Azure

Zapora grupy serwerów ze skalą (Citus) kontroluje, kto może połączyć się z węzłem koordynatora grupy. Zapora określa dostęp przez zapoznanie się z konfigurowalną listą reguł. Każda reguła jest adresem IP lub zakresem adresów, które są dozwolone w.

Gdy Zapora blokuje połączenia, może spowodować błędy aplikacji. Na przykład za pomocą sterownika PostgreSQL JDBC wywołuje błąd podobny do tego:

> java.util.concurrent.ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: KRYTYCZNy: brak PG \_ HBA. conf wpis dla hosta "123.45.67.890", użytkownika "Citus", bazy danych "Citus", SSL

Aby dowiedzieć się, jak są zdefiniowane reguły, zobacz [Tworzenie reguł zapory i zarządzanie nimi](howto-hyperscale-manage-firewall-using-portal.md) .

## <a name="troubleshooting-the-database-server-firewall"></a>Rozwiązywanie problemów z zaporą serwera bazy danych
Gdy dostęp do usługi Microsoft Azure Database for PostgreSQL-Citus) nie zachowuje się zgodnie z oczekiwaniami, należy wziąć pod uwagę następujące kwestie:

* **Zmiany na liście dozwolonych nie zostały jeszcze zastosowane:** Aby zmiany w konfiguracji zapory Citus (deskaling) zaczęły obowiązywać, może wystąpić nawet pięć minut.

* **Użytkownik nie ma autoryzacji lub użyto nieprawidłowego hasła:** Jeśli użytkownik nie ma uprawnień na serwerze lub użyte hasło jest nieprawidłowe, nastąpiło odmowa połączenia z serwerem. Utworzenie ustawienia zapory zapewnia klientom możliwość próby nawiązania połączenia z serwerem; Każdy klient musi nadal podawać niezbędne poświadczenia zabezpieczeń.

Na przykład przy użyciu klienta JDBC może pojawić się następujący błąd.
> java.util.concurrent.ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: KRYTYCZNy: uwierzytelnianie hasła dla użytkownika "yourUserName" nie powiodło się

* **Dynamiczny adres IP:** jeśli używane jest połączenie internetowe za pomocą dynamicznego adresowania IP i występują problemy z przejściem przez zaporę, można wypróbować jedno z poniższych rozwiązań:

* Poproszenie usługodawcy internetowego (ISP) o zakres adresów IP przypisany do komputerów klienckich, które uzyskują dostęp do węzła koordynatora ze skalowaniem (Citus), a następnie Dodaj zakres adresów IP jako regułę zapory.

* Zamiast tego należy pobrać statyczne adresy IP dla komputerów klienckich, a następnie dodać statyczny adres IP jako regułę zapory.

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z artykułami dotyczącymi tworzenia reguł zapory na poziomie serwera i na poziomie bazy danych, zobacz:
* [Tworzenie reguł zapory Azure Database for PostgreSQL i zarządzanie nimi za pomocą Azure Portal](howto-hyperscale-manage-firewall-using-portal.md)
