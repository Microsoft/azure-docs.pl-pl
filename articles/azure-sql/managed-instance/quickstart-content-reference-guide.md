---
title: Wprowadzenie do zawartości
titleSuffix: Azure SQL Managed Instance
description: 'Odwołanie do zawartości, która ułatwia rozpoczynanie pracy z Azure SQL Managed Instance. '
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 07/11/2019
ms.openlocfilehash: ec767e5f3a88c52f9686eec8f7e458ab517ee35f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784339"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Wprowadzenie do Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) tworzy bazę danych o niemal 100% zgodności z najnowszym aparatem bazy danych SQL Server (Enterprise Edition), zapewniając natywną implementację sieci [](https://azure.microsoft.com/pricing/details/sql-database/) wirtualnej [(VNet),](../../virtual-network/virtual-networks-overview.md) która rozwiązuje typowe problemy dotyczące zabezpieczeń, oraz model biznesowy pozytywny dla istniejących klientów z SQL Server.

W tym artykule znajdziesz odwołania do zawartości, które uczą Cię, jak szybko skonfigurować i utworzyć SQL Managed Instance i migrować bazy danych.

## <a name="quickstart-overview"></a>Omówienie przewodnika Szybki start

Poniższe przewodniki Szybki start umożliwiają szybkie utworzenie serwera SQL Managed Instance, skonfigurowanie maszyny wirtualnej lub połączenia sieci VPN typu punkt-lokacja dla aplikacji klienckiej oraz przywrócenie bazy danych do nowej SQL Managed Instance przy użyciu `.bak` pliku.

### <a name="configure-environment"></a>Konfigurowanie środowiska

W pierwszym kroku należy utworzyć pierwszy serwer SQL Managed Instance ze środowiskiem sieciowym, w którym zostanie umieszczony, i włączyć połączenie z komputera lub maszyny wirtualnej, na której są wykonywane zapytania w celu SQL Managed Instance. Możesz użyć następujących wytycznych:

- [Utwórz SQL Managed Instance przy użyciu Azure Portal](instance-create-quickstart.md). W witrynie Azure Portal skonfigurujesz wymagane parametry (nazwa użytkownika, hasło, liczba rdzeni i maksymalna wielkość magazynu) i automatycznie utworzysz środowisko sieciowe platformy Azure bez znajomości szczegółów sieci i wymagań dotyczących infrastruktury. Wystarczy upewnić się, że masz typ [subskrypcji,](resource-limits.md#supported-subscription-types) który obecnie może utworzyć SQL Managed Instance. Jeśli masz własną sieć, której chcesz użyć, lub chcesz dostosować sieć, zobacz konfigurowanie istniejącej sieci wirtualnej dla usługi [Azure SQL Managed Instance](vnet-existing-add-subnet.md) lub tworzenie sieci wirtualnej dla usługi [Azure SQL Managed Instance](virtual-network-subnet-create-arm-template.md).
- Punkt SQL Managed Instance jest tworzony we własnej sieci wirtualnej bez publicznego punktu końcowego. Na potrzeby dostępu aplikacji klienckiej możesz **utworzyć maszynę wirtualną w tej samej sieci wirtualnej (w innej podsieci)** lub **utworzyć połączenie sieci VPN typu punkt-lokacja z tą siecią wirtualną z komputera klienckiego** przy użyciu jednego z tych przewodników Szybki start:
  - Włącz [publiczny punkt końcowy](public-endpoint-configure.md) na SQL Managed Instance, aby uzyskać dostęp do danych bezpośrednio ze środowiska.
  - Utwórz [maszynę wirtualną platformy Azure w SQL Managed Instance wirtualnej na](connect-vm-instance-configure.md) celu łączność aplikacji klienckich, w tym SQL Server Management Studio.
  - Skonfiguruj połączenie [sieci VPN typu punkt-lokacja](point-to-site-p2s-configure.md) z serwerem SQL Managed Instance komputerze klienckim, na którym SQL Server Management Studio i inne aplikacje łączności klienta. Jest to druga z dwóch opcji łączności z siecią SQL Managed Instance z siecią wirtualną.

  > [!NOTE]
  > - Możesz też użyć usługi ExpressRoute lub połączenia lokacja-lokacja z sieci lokalnej, lecz te podejścia są poza zakresem tych przewodników Szybki start.
  > - Jeśli zmienisz okres przechowywania z 0 (nieograniczone przechowywanie) na dowolną inną wartość, pamiętaj, że okres przechowywania będzie miał zastosowanie tylko do dzienników zapisywanych po zmianie wartości przechowywania (dzienniki zapisane w okresie, w którym okres przechowywania został ustawiony na nieograniczone, są zachowywane nawet po włączeniu przechowywania).

Alternatywą dla ręcznego tworzenia SQL Managed Instance jest użycie programu [PowerShell,](scripts/create-configure-managed-instance-powershell.md)programu [PowerShell](./create-template-quickstart.md)z szablonem Resource Manager lub interfejsu wiersza polecenia platformy [Azure](/cli/azure/sql/mi#az_sql_mi_create) w celu utworzenia skryptu i zautomatyzowania tego procesu.

### <a name="migrate-your-databases"></a>Migrowanie baz danych

Po utworzeniu bazy SQL Managed Instance i skonfigurowaniu dostępu możesz rozpocząć migrowanie baz SQL Server danych. Migracja może się nie powieść, jeśli w źródłowej bazie danych, która ma zostać zmigrowana, znajdują się nieobsługiwane funkcje. Aby uniknąć błędów i sprawdzić zgodność, można użyć narzędzia [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) do przeanalizowania baz danych na platformie SQL Server i znalezienia dowolnego problemu, który mógłby zablokować migrację do usługi SQL Managed Instance, na przykład istnienia pliku [FileStream](/sql/relational-databases/blob/filestream-sql-server) lub wielu plików dziennika. Jeśli te problemy zostaną rozwiązane, bazy danych będą gotowe do migracji do SQL Managed Instance. [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview) jest innym przydatnym narzędziem, które może rejestrować obciążenie na platformie SQL Server i odtwarzać je na SQL Managed Instance, aby określić, czy w przypadku migracji do serwera SQL Managed Instance będą się pojawiać jakiekolwiek problemy z SQL Managed Instance.

Po upewnienie się, że można migrować bazę danych do serwera SQL Managed Instance, możesz użyć natywnych funkcji przywracania bazy danych SQL Server, aby przywrócić bazę danych do SQL Managed Instance z `.bak` pliku. Za pomocą tej metody można migrować bazy danych z SQL Server bazy danych zainstalowanej lokalnie lub na platformie Azure Virtual Machines. Aby uzyskać przewodnik Szybki start, zobacz [Przywracanie z kopii zapasowej do SQL Managed Instance.](restore-sample-database-quickstart.md) W tym przewodniku Szybki start wykonasz przywracanie z pliku `.bak` przechowywanego w usłudze Azure Blob Storage przy użyciu polecenia języka Transact-SQL `RESTORE`.

> [!TIP]
> Aby utworzyć kopię zapasową bazy danych w usłudze Azure Blob Storage za pomocą polecenia języka Transact-SQL `BACKUP`, zobacz [SQL Server backup to URL (Tworzenie kopii zapasowej programu SQL Server pod określonym adresem URL)](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Te przewodniki Szybki start umożliwiają szybkie tworzenie, konfigurowanie i przywracanie kopii zapasowej bazy danych do SQL Managed Instance. W niektórych scenariuszach należy dostosować lub zautomatyzować wdrażanie SQL Managed Instance i wymaganego środowiska sieciowego. Te scenariusze są opisane poniżej.

## <a name="customize-network-environment"></a>Dostosowywanie środowiska sieciowego

Mimo że sieć wirtualną/podsieć można skonfigurować automatycznie podczas tworzenia wystąpienia przy użyciu sieci [Azure Portal,](instance-create-quickstart.md)warto ją utworzyć przed rozpoczęciem tworzenia wystąpień w programie SQL Managed Instance, ponieważ można skonfigurować parametry sieci wirtualnej i podsieci. Najprostszym sposobem utworzenia i skonfigurowania środowiska sieciowego jest użycie szablonu wdrażania zasobów platformy [Azure,](virtual-network-subnet-create-arm-template.md) który tworzy i konfiguruje sieć i podsieć, w której zostanie umieszczone wystąpienie. Wystarczy nacisnąć przycisk wdrażania usługi Azure Resource Manager i podać parametry w formularzu.

Jako alternatywy możesz użyć [skryptu programu PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) do zautomatyzowania tworzenia sieci.

Jeśli masz już sieć wirtualną i podsieć, w której chcesz wdrożyć usługę SQL Managed Instance, upewnij się, że sieć wirtualna i podsieć spełniają [wymagania dotyczące sieci.](connectivity-architecture-overview.md#network-requirements) Użyj tego [skryptu programu PowerShell, aby zweryfikować, czy podsieć jest poprawnie skonfigurowana](vnet-existing-add-subnet.md). Ten skrypt weryfikuje sieć i zgłasza wszelkie problemy, informując o tym, co należy zmienić, a następnie oferuje możliwość zmiany niezbędnych zmian w sieci wirtualnej/podsieci. Uruchom ten skrypt, jeśli nie chcesz ręcznie konfigurować sieci wirtualnej i podsieci. Należy go także uruchomić po każdej znacznej zmianie konfiguracji infrastruktury sieciowej. Jeśli chcesz utworzyć i skonfigurować własną [](connectivity-architecture-overview.md) sieć, zapoznaj się z architekturą łączności i tym ostatecznym przewodnikiem tworzenia i konfigurowania [środowiska SQL Managed Instance sieci.](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)

## <a name="migrate-to-a-sql-managed-instance"></a>Migrowanie do SQL Managed Instance

Wymienione wcześniej przewodniki Szybki start umożliwiają szybkie skonfigurowanie bazy SQL Managed Instance i przeniesienie baz danych przy użyciu `RESTORE` natywnej możliwości. Jest to dobry punkt wyjścia, jeśli chcesz szybko zweryfikować koncepcje i sprawdzić, czy Twoje rozwiązanie może działać w wystąpieniu zarządzanym.

Jednak w celu zmigrowania produkcyjnej bazy danych, a nawet baz danych tworzenia i testowania, których chcesz użyć na potrzeby niektórych testów wydajnościowych, należy rozważyć użycie pewnych dodatkowych technik, takich jak:

- Testowanie wydajności — należy zmierzyć podstawowe metryki wydajności w wystąpieniu źródłowym usługi SQL Server i porównać je z metrykami wydajności w lokalizacji docelowej SQL Managed Instance w której zmigrowana jest baza danych. Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących porównywania wydajności.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)
- Migracja online — zgodnie z natywną procedurą opisaną w tym artykule musisz poczekać na przywrócenie baz danych (i skopiowanie ich do usługi Azure Blob Storage, jeśli nie są `RESTORE` tam jeszcze przechowywane). Może to spowodować przestój aplikacji, szczególnie jeśli bazy danych są duże. Aby przenieść produkcyjną bazę danych, użyj usługi [Data Migration Service (DMS)](../../dms/tutorial-sql-server-to-managed-instance.md?toc=%2fazure%2fsql-database%2ftoc.json) w celu migrowania bazy danych z minimalnym przestojem. W tym celu dms przyrostowo wypycha zmiany wprowadzone w źródłowej bazie danych do przywracanych SQL Managed Instance bazy danych. Dzięki temu można szybko przełączyć aplikację ze źródłowej do docelowej bazy danych przy minimalnych przestojach.

Dowiedz się więcej o [zalecanym procesie migracji.](migrate-to-instance-from-sql-server.md)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać [szczegółowe informacje o obsługiwanych funkcjach,](../database/features-comparison.md) zobacz tutaj SQL Managed Instance szczegółowe [informacje i znane problemy.](transact-sql-tsql-differences-sql-server.md)
- Dowiedz się więcej [o cechach technicznych SQL Managed Instance.](resource-limits.md#service-tier-characteristics)
- Bardziej zaawansowane how-to's in [how to use a SQL Managed Instance](how-to-content-reference-guide.md)(Jak korzystać z SQL Managed Instance).
- [Określ właściwą Azure SQL Managed Instance SKU dla lokalnej bazy danych.](/sql/dma/dma-sku-recommend-sql-db/)