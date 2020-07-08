---
title: Reguły zapory — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób używania reguł zapory do nawiązywania połączenia z serwerem Azure Database for PostgreSQL-pojedynczym.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5d462be1caa3787cb7ff9a455be595ec5784eefe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76157274"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Reguły zapory w Azure Database for PostgreSQL-pojedynczym serwerze
Zapora serwera Azure Database for PostgreSQL uniemożliwia dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Zapora przyznaje dostęp do serwera na podstawie źródłowego adresu IP każdego żądania.
Aby skonfigurować zaporę, należy utworzyć reguły zapory określające zakresy dopuszczalnych adresów IP. Reguły zapory można tworzyć na poziomie serwera.

**Reguły zapory:** Te reguły umożliwiają klientom dostęp do całego serwera Azure Database for PostgreSQL, czyli wszystkich baz danych na tym samym serwerze logicznym. Reguły zapory na poziomie serwera można skonfigurować za pomocą Azure Portal lub przy użyciu poleceń interfejsu wiersza polecenia platformy Azure. Aby utworzyć reguły zapory na poziomie serwera, musisz być właścicielem subskrypcji lub współautorem subskrypcji.

## <a name="firewall-overview"></a>Omówienie zapory
Domyślnie dostęp do serwera Azure Database for PostgreSQL jest blokowany przez zaporę. Aby rozpocząć korzystanie z serwera z innego komputera, należy określić co najmniej jedną regułę zapory na poziomie serwera, aby umożliwić dostęp do serwera. Użyj reguł zapory, aby określić, które zakresy adresów IP z Internetu mają być dozwolone. Reguły zapory nie wpływają na dostęp do samej witryny sieci Web Azure Portal.
Próby połączenia z Internetu i platformy Azure muszą być przekazywane przez zaporę przed uzyskaniem dostępu do bazy danych PostgreSQL, jak pokazano na poniższym diagramie:

![Przykładowy przepływ działania zapory](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Łączenie się z Internetu
Reguły zapory na poziomie serwera mają zastosowanie do wszystkich baz danych na tym samym serwerze Azure Database for PostgreSQL. Jeśli adres IP żądania należy do jednego z zakresów określonych w regułach zapory na poziomie serwera, ustanawiane jest połączenie.
Jeśli adres IP żądania nie należy do zakresów określonych w regułach zapory na poziomie serwera, żądanie połączenia kończy się niepowodzeniem.
Jeśli na przykład aplikacja nawiązuje połączenie z sterownikiem JDBC dla PostgreSQL, może wystąpić błąd podczas próby nawiązania połączenia, gdy Zapora blokuje połączenie.
> java.util.concurrent.ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: KRYTYCZNy: brak PG \_ HBA. conf wpis dla hosta "123.45.67.890", użytkownika "AdminUser", bazy danych "PostgreSQL", SSL

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Zalecane jest, aby znaleźć wychodzący adres IP dowolnej aplikacji lub usługi i jawnie zezwolić na dostęp do tych pojedynczych adresów IP lub zakresów. Na przykład można znaleźć wychodzący adres IP Azure App Service lub użyć publicznego adresu IP powiązanego z maszyną wirtualną lub innym zasobem (zobacz poniżej, aby uzyskać informacje na temat nawiązywania połączenia z prywatnym adresem IP maszyny wirtualnej za pośrednictwem punktów końcowych usługi). 

Jeśli stały wychodzący adres IP nie jest dostępny dla usługi platformy Azure, możesz rozważyć włączenie połączeń ze wszystkich adresów IP centrum danych platformy Azure. To ustawienie można włączyć z poziomu Azure Portal, ustawiając opcję **Zezwalaj na dostęp do usług platformy Azure** na **wartość z** okienka **zabezpieczenia połączenia** i naciskając polecenie **Zapisz**. W interfejsie wiersza polecenia platformy Azure ustawienie reguły zapory z adresem początkowym i końcowym równym 0.0.0.0 jest równoważne. Jeśli próba połączenia nie jest dozwolona, żądanie nie dociera do serwera Azure Database for PostgreSQL.

> [!IMPORTANT]
> Opcja **Zezwalaj na dostęp do usług platformy Azure** umożliwia skonfigurowanie zapory w taki sposób, aby zezwalała na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

![Konfigurowanie zezwalania na dostęp do usług platformy Azure w portalu](media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Łączenie z sieci wirtualnej
Aby bezpiecznie połączyć się z serwerem Azure Database for PostgreSQL z sieci wirtualnej, należy rozważyć użycie [punktów końcowych usługi sieci wirtualnej](./concepts-data-access-and-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Programowe zarządzanie regułami zapory
Oprócz Azure Portal reguły zapory można zarządzać programowo przy użyciu interfejsu wiersza polecenia platformy Azure.
Zobacz też [Tworzenie reguł zapory Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure i zarządzanie nimi](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Rozwiązywanie problemów z zaporą
Należy wziąć pod uwagę następujące kwestie, gdy dostęp do usługi Microsoft Azure Database for PostgreSQL Server nie zadziała zgodnie z oczekiwaniami:

* **Zmiany na liście dozwolonych nie zostały jeszcze zastosowane:** Aby zmiany w konfiguracji zapory serwera Azure Database for PostgreSQL zaczęły obowiązywać, może wystąpić nawet pięć minut.

* **Logowanie nie jest autoryzowane lub użyto nieprawidłowego hasła:** Jeśli logowanie nie ma uprawnień na serwerze Azure Database for PostgreSQL lub użyte hasło jest nieprawidłowe, nastąpiło odmowa połączenia z serwerem Azure Database for PostgreSQL. Utworzenie ustawienia zapory zapewnia klientom możliwość próby nawiązania połączenia z serwerem; Każdy klient musi nadal podawać niezbędne poświadczenia zabezpieczeń.

   Na przykład przy użyciu klienta JDBC może pojawić się następujący błąd.
   > java.util.concurrent.ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: KRYTYCZNy: uwierzytelnianie hasła dla użytkownika "yourUserName" nie powiodło się

* **Dynamiczny adres IP:** jeśli używane jest połączenie internetowe za pomocą dynamicznego adresowania IP i występują problemy z przejściem przez zaporę, można wypróbować jedno z poniższych rozwiązań:

   * Poproszenie usługodawcy internetowego (ISP) o zakres adresów IP przypisany do komputerów klienckich, które uzyskują dostęp do serwera Azure Database for PostgreSQL, a następnie Dodaj zakres adresów IP jako regułę zapory.

   * Zamiast tego należy pobrać statyczne adresy IP dla komputerów klienckich, a następnie dodać statyczny adres IP jako regułę zapory.

* **Adres IP serwera wydaje się być publiczny:** Połączenia z serwerem Azure Database for PostgreSQL są kierowane za pomocą publicznie dostępnej bramy platformy Azure. Rzeczywisty adres IP serwera jest jednak chroniony przez zaporę. Aby uzyskać więcej informacji, zapoznaj się z [artykułem dotyczącym architektury łączności](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z artykułami dotyczącymi tworzenia reguł zapory na poziomie serwera i na poziomie bazy danych, zobacz:
* [Tworzenie reguł zapory Azure Database for PostgreSQL i zarządzanie nimi za pomocą Azure Portal](howto-manage-firewall-using-portal.md)
* [Tworzenie reguł zapory Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure i zarządzanie nimi](howto-manage-firewall-using-cli.md)
- [Punkty końcowe usługi sieci wirtualnej w Azure Database for PostgreSQL](./concepts-data-access-and-security-vnet.md)
