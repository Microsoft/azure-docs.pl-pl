---
title: Obsługiwane wersje — Azure Database for MySQL
description: Dowiedz się, które wersje serwera MySQL są obsługiwane w usłudze Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: d84f56e5ae0f3c364a0fd3a08ccb173d7c65a5e2
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121765"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Obsługiwane wersje serwera usługi Azure Database for MySQL

Azure Database for MySQL został opracowany z firmy [MySQL Community Edition](https://www.mysql.com/products/community/)przy użyciu aparatu magazynu InnoDB. Usługa obsługuje wszystkie bieżące wersje główne obsługiwane przez społeczność, mianowicie MySQL 5,6, 5,7 i 8,0. Baza danych MySQL używa schematu nazewnictwa X. Y, gdzie X jest wersją główną, Y jest wersją pomocniczą, a Z Z to Poprawka błędu. Aby uzyskać więcej informacji na temat schematu, zobacz [dokumentację programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).



## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>Nawiązywanie połączenia z węzłem bramy z uruchomioną określoną wersją programu MySQL

W przypadku opcji wdrożenia pojedynczego serwera Brama jest używana do przekierowywania połączeń z wystąpieniami serwera. Po nawiązaniu połączenia klient oprogramowania MySQL wyświetla wersję oprogramowania MySQL ustawioną w bramie, a nie rzeczywistą wersję uruchomioną w wystąpieniu serwera MySQL. Aby określić wersję wystąpienia serwera MySQL, użyj polecenia `SELECT VERSION();` w wierszu polecenia MySQL. Przejrzyj [architekturę łączności](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture#connectivity-architecture) , aby dowiedzieć się więcej na temat bram w Azure Database for MySQL architekturze usług.

Ponieważ Azure Database for MySQL obsługuje wersję główną wersji 3.0, v 5.7 i v 8.0, domyślny port 3306 do łączenia się z Azure Database for MySQL uruchamia klienta MySQL w wersji 5,6 (najmniejszy typowy mianownik) do obsługi połączeń z serwerami wszystkich 3 obsługiwanych wersji głównych. Jeśli jednak aplikacja jest wymagana do nawiązania połączenia z konkretną wersją główną 3.0 lub 8.0, można to zrobić, zmieniając port w parametrach połączenia z serwerem.

W ramach usługi Azure Database for MySQL węzły bramy nasłuchuje na porcie 3308 dla klientów v 5.7 i portów 3309 dla klientów v 8.0. Innymi słowy, jeśli chcesz nawiązać połączenie z klientem bramy v 5.7, należy użyć w pełni kwalifikowanej nazwy serwera i portu 3308 do łączenia się z serwerem z aplikacji klienckiej. Podobnie, jeśli chcesz nawiązać połączenie z klientem bramy programu v 8.0, możesz użyć w pełni kwalifikowanej nazwy serwera i portu 3309, aby nawiązać połączenie z serwerem. Zapoznaj się z poniższym przykładem w celu dalszej przejrzystości.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="Przykład łączenia przy użyciu różnych wersji programu MySQL bramy":::


## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>Azure Database for MySQL obecnie obsługuje następujące główne i pomocnicze wersje programu MySQL:


| Wersja | [Pojedynczy serwer](overview.md) <br/> Bieżąca wersja pomocnicza |[Serwer elastyczny (wersja zapoznawcza)](/../flexible-server/overview.md) <br/> Bieżąca wersja pomocnicza  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|Baza danych MySQL w wersji 5,6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (wycofane) | Nieobsługiwane|
|Baza danych MySQL w wersji 5,7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|Baza danych MySQL w wersji 8,0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Zapoznaj się z zasadami obsługi wersji dla wycofanych wersji w [dokumentacji zasad obsługi wersji.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami
Usługa automatycznie zarządza poprawkami do poprawek błędów wersji. Na przykład 5.7.20 do 5.7.21.  

Uaktualnianie wersji głównej jest obecnie obsługiwane przez usługę do uaktualnień z programu MySQL v 5.6 do wersji 5.7. Aby uzyskać więcej informacji, zobacz [Jak przeprowadzić uaktualnienia wersji głównej](how-to-major-version-upgrade.md). Jeśli chcesz przeprowadzić uaktualnienie z 5,7 do 8,0, zalecamy wykonanie [zrzutu i przywrócenia](./concepts-migrate-dump-restore.md) na serwerze, który został utworzony przy użyciu nowej wersji aparatu.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje na temat zasad dotyczących wersji Azure Database for MySQL, zobacz [ten dokument](concepts-version-policy.md).
- Aby uzyskać informacje na temat konkretnych przydziałów zasobów i ograniczeń na podstawie **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md)
