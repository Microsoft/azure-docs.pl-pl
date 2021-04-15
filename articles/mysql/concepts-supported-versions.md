---
title: Obsługiwane wersje — Azure Database for MySQL
description: Dowiedz się, które wersje serwera MySQL są obsługiwane w Azure Database for MySQL service.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 1be15c16a1897797326ea869c34c3590ffb07691
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363873"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Obsługiwane wersje serwera usługi Azure Database for MySQL

Azure Database for MySQL została opracowana z [programu MySQL Community Edition](https://www.mysql.com/products/community/)przy użyciu aparatu magazynu InnoDB. Usługa obsługuje wszystkie bieżące wersje główne obsługiwane przez społeczność, czyli MySQL 5.6, 5.7 i 8.0. Program MySQL używa schematu nazewnictwa X.Y.Z, gdzie X jest wersją główną, Y jest wersją pomocniczą, a Z jest wydaniem poprawki usterki. Aby uzyskać więcej informacji na temat schematu, zobacz [dokumentację programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).



## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>Nawiązywanie połączenia z węzłem bramy z określoną wersją programu MySQL

W opcji wdrożenia pojedynczego serwera brama służy do przekierowywania połączeń do wystąpień serwera. Po nawiązaniu połączenia klient oprogramowania MySQL wyświetla wersję oprogramowania MySQL ustawioną w bramie, a nie rzeczywistą wersję uruchomioną w wystąpieniu serwera MySQL. Aby określić wersję wystąpienia serwera MySQL, użyj polecenia `SELECT VERSION();` w wierszu polecenia MySQL. Zapoznaj [się z architekturą łączności,](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture#connectivity-architecture) aby dowiedzieć się więcej o bramach Azure Database for MySQL architektury usługi.

Ponieważ program Azure Database for MySQL obsługuje wersję główną 5.6, 5.7 i 8.0, domyślny port 3306 do łączenia się z usługą Azure Database for MySQL uruchamia klienta MySQL w wersji 5.6 (najmniej wspólny mianownik) w celu obsługi połączeń z serwerami ze wszystkimi 3 obsługiwanymi wersjami głównymi. Jeśli jednak aplikacja wymaga połączenia z określoną wersją główną, np. v5.7 lub v8.0, możesz to zrobić, zmieniając port w parametrów połączenia serwera.

W Azure Database for MySQL usługi węzły bramy nasłuchują na porcie 3308 dla klientów w wersji 5.7 i na porcie 3309 dla klientów w wersji 8.0. Innymi słowy, jeśli chcesz nawiązać połączenie z klientem bramy w wersji 5.7, użyj w pełni kwalifikowanej nazwy serwera i portu 3308, aby nawiązać połączenie z serwerem z aplikacji klienckiej. Podobnie, jeśli chcesz nawiązać połączenie z klientem bramy w wersji 8.0, możesz użyć w pełni kwalifikowanej nazwy serwera i portu 3309 w celu nawiązania połączenia z serwerem. Sprawdź poniższy przykład, aby uzyskać więcej informacji.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="Przykład nawiązywania połączenia za pośrednictwem różnych wersji programu mysql bramy":::


## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>Azure Database for MySQL obecnie obsługuje następujące główne i pomocnicze wersje programu MySQL:


| Wersja | [Pojedynczy serwer](overview.md) <br/> Bieżąca wersja pomocnicza |[Serwer elastyczny (wersja zapoznawcza)](/azure/mysql/flexible-server/overview) <br/> Bieżąca wersja pomocnicza  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL w wersji 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (wycofane) | Nieobsługiwane|
|MySQL, wersja 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL w wersji 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Zapoznaj się z zasadami obsługi wersji dla wycofanych wersji w [dokumentacji zasad obsługi wersji.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami
Usługa automatycznie zarządza poprawkami aktualizacji wersji poprawek. Na przykład z 5.7.20 do 5.7.21.  

Uaktualnienie wersji głównych jest obecnie obsługiwane przez usługę w przypadku uaktualnień z programu MySQL w wersji 5.6 do wersji 5.7. Aby uzyskać więcej informacji, zobacz [jak przeprowadzać uaktualnienia wersji głównych.](how-to-major-version-upgrade.md) Jeśli chcesz przeprowadzić uaktualnienie z wersji 5.7 do 8.0, zalecamy wykonanie zrzutu i przywrócenia na serwerze, który został utworzony przy użyciu nowej wersji aparatu. [](./concepts-migrate-dump-restore.md)

## <a name="next-steps"></a>Następne kroki

- Szczegółowe informacje dotyczące Azure Database for MySQL wersji można znaleźć w [tym dokumencie.](concepts-version-policy.md)
- Aby uzyskać informacje o limitach przydziałów i ograniczeniach określonych zasobów w zależności od **warstwy usług,** zobacz [Warstwy usług](./concepts-pricing-tiers.md)
