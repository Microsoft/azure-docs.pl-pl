---
title: Zasoby DTU ograniczają pojedyncze bazy danych
description: Ta strona zawiera opis niektórych typowych limitów zasobów DTU dla pojedynczych baz danych w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: 099094338deba63f678337b7ea13dd9ce9800084
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517692"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model---azure-sql-database"></a>Limity zasobów dla pojedynczych baz danych korzystających z modelu zakupu jednostek DTU — Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ten artykuł zawiera szczegółowe limity zasobów dla Azure SQL Database pojedynczych baz danych przy użyciu modelu zakupu jednostek DTU.

W przypadku limitów zasobów modelu zakupu jednostek DTU dla pul elastycznych zobacz [limity zasobów jednostek DTU — pule elastyczne](resource-limits-dtu-elastic-pools.md). Aby uzyskać limity zasobów rdzeń wirtualny, zobacz [limity zasobów rdzeń wirtualny — pojedyncze bazy danych](resource-limits-vcore-single-databases.md) i [limity zasobów rdzeń wirtualny — pule elastyczne](resource-limits-vcore-elastic-pools.md). Aby uzyskać więcej informacji na temat różnych modeli zakupów, zobacz [modele zakupów i warstwy usług](purchasing-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Pojedyncza baza danych: rozmiary magazynu i rozmiary obliczeń

W poniższych tabelach przedstawiono zasoby dostępne dla pojedynczej bazy danych w każdej warstwie usług i rozmiarze obliczeniowym. Możesz ustawić warstwę usług, rozmiar obliczeń i ilość miejsca do magazynowania dla pojedynczej bazy danych, korzystając z [Azure Portal](single-database-manage.md#the-azure-portal), [Transact-SQL](single-database-manage.md#transact-sql-t-sql), [PowerShell](single-database-manage.md#powershell), interfejsu [wiersza polecenia platformy Azure](single-database-manage.md#the-azure-cli)lub [API REST](single-database-manage.md#rest-api).

> [!IMPORTANT]
> Aby uzyskać wskazówki dotyczące skalowania i zagadnienia, zobacz [skalowanie pojedynczej bazy danych](single-database-scale.md)

### <a name="basic-service-tier"></a>Warstwa usługi Podstawowa

| **Rozmiar obliczeń** | **Podstawowa** |
| :--- | --: |
| Maksymalna liczba jednostek DTU | 5 |
| Uwzględniony magazyn (GB) | 2 |
| Maksymalna liczba opcji magazynu (GB) | 2 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) |Nie dotyczy |
| Maksymalna liczba współbieżnych procesów roboczych (żądań) | 30 |
| Maksymalna liczba współbieżnych sesji | 300 |
|||

> [!IMPORTANT]
> Podstawowa warstwa usług zapewnia mniej niż jeden rdzeń wirtualny (CPU).  W przypadku obciążeń intensywnie korzystających z procesora CPU zaleca się użycie warstwy usług S3 lub wyższej. 
>
>W przypadku magazynu danych podstawowa warstwa usług jest umieszczana na stronach standardowych obiektów BLOB. Standardowe obiekty blob stronicowe korzystają z nośników magazynowania opartych na dyskach twardych i najlepiej nadają się do tworzenia, testowania i innych rzadko używanych obciążeń, które są mniej wrażliwe na zmienności wydajności.
>

### <a name="standard-service-tier"></a>Warstwa usługi Standardowa

| **Rozmiar obliczeń** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maksymalna liczba jednostek DTU | 10 | 20 | 50 | 100 |
| Uwzględniony magazyn (GB) | 250 | 250 | 250 | 250 |
| Maksymalna liczba opcji magazynu (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy |
| Maksymalna liczba współbieżnych procesów roboczych (żądań)| 60 | 90 | 120 | 200 |
| Maksymalna liczba współbieżnych sesji |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> Warstwy Standardowa S0, S1 i S2 zapewniają mniej niż jeden rdzeń wirtualny (CPU).  W przypadku obciążeń intensywnie korzystających z procesora CPU zaleca się użycie warstwy usług S3 lub wyższej. 
>
>W przypadku magazynu danych standardowe warstwy usług S0 i S1 są umieszczane na stronach standardowych obiektów BLOB. Standardowe obiekty blob stronicowe korzystają z nośników magazynowania opartych na dyskach twardych i najlepiej nadają się do tworzenia, testowania i innych rzadko używanych obciążeń, które są mniej wrażliwe na zmienności wydajności.
>

### <a name="standard-service-tier-continued"></a>Standardowa warstwa usług (ciąg dalszy)

| **Rozmiar obliczeń** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maksymalna liczba jednostek DTU | 200 | 400 | 800 | 1600 | 3000 |
| Uwzględniony magazyn (GB) | 250 | 250 | 250 | 250 | 250 |
| Maksymalna liczba opcji magazynu (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy |Nie dotyczy |
| Maksymalna liczba współbieżnych procesów roboczych (żądań)| 400 | 800 | 1600 | 3200 |6000 |
| Maksymalna liczba współbieżnych sesji |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Warstwa usługi Premium

| **Rozmiar obliczeń** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maksymalna liczba jednostek DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Uwzględniony magazyn (GB) | 500 | 500 | 500 | 500 | 4096 * | 4096 * |
| Maksymalna liczba opcji magazynu (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 * | 4096 * |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maksymalna liczba współbieżnych procesów roboczych (żądań)| 200 | 400 | 800 | 1600 | 2800 | 6400 |
| Maksymalna liczba współbieżnych sesji | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\*Od 1024 GB do 4096 GB w przyrostach wynoszących 256 GB

> [!IMPORTANT]
> Więcej niż 1 TB magazynu w warstwie Premium jest obecnie dostępne we wszystkich regionach z wyjątkiem: Chiny Wschodnie, Chiny Północne, Niemcy środkowe, Niemcy północno-zachodnie stany USA, regiony US DoD oraz centralne stany USA. W tych regionach maksymalna wielkość magazynu w warstwie Premium jest ograniczona do 1 TB.  Aby uzyskać więcej informacji, zobacz [bieżące ograniczenia poziomów P11–P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> Aby uzyskać `tempdb` limity, zobacz [limity tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać limity zasobów rdzeń wirtualny dla pojedynczej bazy danych, zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu rdzeń wirtualny](resource-limits-vcore-single-databases.md)
- Aby uzyskać limity zasobów rdzeń wirtualny dla pul elastycznych, zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu rdzeń wirtualny](resource-limits-vcore-elastic-pools.md)
- W przypadku limitów zasobów jednostek DTU dla pul elastycznych zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu jednostek DTU](resource-limits-dtu-elastic-pools.md) .
- W przypadku limitów zasobów dla wystąpień zarządzanych w wystąpieniu zarządzanym usługi Azure SQL zobacz [limity zasobów wystąpienia zarządzanego SQL](../managed-instance/resource-limits.md).
- Aby uzyskać informacje na temat ogólnych limitów platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Aby uzyskać informacje na temat limitów zasobów na serwerze logicznym programu SQL Server, zobacz [Omówienie limitów zasobów na serwerze logicznym programu SQL Server](resource-limits-logical-server.md) , aby uzyskać informacje na temat limitów na poziomach serwera i subskrypcji.
