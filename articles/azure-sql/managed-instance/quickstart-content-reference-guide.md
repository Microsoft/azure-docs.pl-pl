---
title: Wprowadzenie do zawartości
titleSuffix: Azure SQL Managed Instance
description: 'Odwołanie do zawartości, która ułatwia rozpoczęcie pracy z wystąpieniem zarządzanym usługi Azure SQL. '
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
ms.openlocfilehash: ae2f2b8b9b6f3bc934321b13dcefeff46e43b089
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788166"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Wprowadzenie do wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Wystąpienie zarządzane Azure SQL](sql-managed-instance-paas-overview.md) tworzy bazę danych o prawie 100% zgodności z najnowszym aparatem bazy danych SQL Server (Enterprise Edition), zapewniając natywną implementację [sieci wirtualnej (VNET)](../../virtual-network/virtual-networks-overview.md) , która rozwiązuje typowe problemy związane z bezpieczeństwem, oraz [model biznesowy](https://azure.microsoft.com/pricing/details/sql-database/) preferowany dla istniejących SQL Server klientów.

W tym artykule znajdziesz odwołania do zawartości, która uczy Cię, jak szybko skonfigurować i utworzyć wystąpienie zarządzane SQL oraz przeprowadzić migrację baz danych.

## <a name="quickstart-overview"></a>Omówienie przewodnika Szybki start

Poniższe przewodniki szybki start umożliwiają szybkie tworzenie wystąpienia zarządzanego SQL, Konfigurowanie maszyny wirtualnej lub Nawiązywanie połączenia sieci VPN dla aplikacji klienta oraz przywracanie bazy danych do nowego wystąpienia zarządzanego SQL przy użyciu `.bak` pliku.

### <a name="configure-environment"></a>Konfigurowanie środowiska

Pierwszym krokiem jest utworzenie pierwszego wystąpienia zarządzanego SQL przy użyciu środowiska sieciowego, w którym zostaną umieszczone, i włączenie połączenia z komputera lub maszyny wirtualnej, w której są wykonywane zapytania do wystąpienia zarządzanego SQL. Możesz użyć następujących wytycznych:

- [Utwórz wystąpienie zarządzane SQL przy użyciu Azure Portal](instance-create-quickstart.md). W witrynie Azure Portal skonfigurujesz wymagane parametry (nazwa użytkownika, hasło, liczba rdzeni i maksymalna wielkość magazynu) i automatycznie utworzysz środowisko sieciowe platformy Azure bez znajomości szczegółów sieci i wymagań dotyczących infrastruktury. Wystarczy upewnić się, że masz [Typ subskrypcji](resource-limits.md#supported-subscription-types) , który jest obecnie uprawniony do tworzenia wystąpienia zarządzanego SQL. Jeśli używasz własnej sieci lub chcesz dostosować sieć, zobacz [Konfigurowanie istniejącej sieci wirtualnej dla wystąpienia zarządzanego usługi Azure SQL](vnet-existing-add-subnet.md) lub [Tworzenie sieci wirtualnej dla wystąpienia zarządzanego Azure SQL](virtual-network-subnet-create-arm-template.md).
- Wystąpienie zarządzane SQL jest tworzone we własnej sieci wirtualnej bez publicznego punktu końcowego. Na potrzeby dostępu aplikacji klienckiej możesz **utworzyć maszynę wirtualną w tej samej sieci wirtualnej (w innej podsieci)** lub **utworzyć połączenie sieci VPN typu punkt-lokacja z tą siecią wirtualną z komputera klienckiego** przy użyciu jednego z tych przewodników Szybki start:
  - Włącz [publiczny punkt końcowy](public-endpoint-configure.md) w wystąpieniu zarządzanym SQL, aby uzyskać dostęp do danych bezpośrednio ze środowiska.
  - Utwórz [maszynę wirtualną platformy Azure w sieci wirtualnej wystąpienia zarządzanego SQL](connect-vm-instance-configure.md) na potrzeby łączności aplikacji klienckiej, w tym SQL Server Management Studio.
  - Skonfiguruj [połączenie sieci VPN typu punkt-lokacja z wystąpieniem zarządzanym SQL](point-to-site-p2s-configure.md) z komputera klienckiego, na którym masz SQL Server Management Studio i inne aplikacje łączności klienta. Są to dwie opcje łączności z wystąpieniem zarządzanym SQL i jego siecią wirtualną.

  > [!NOTE]
  > - Możesz też użyć usługi ExpressRoute lub połączenia lokacja-lokacja z sieci lokalnej, lecz te podejścia są poza zakresem tych przewodników Szybki start.
  > - Jeśli zmienisz okres przechowywania z 0 (nieograniczony czas przechowywania) na inną wartość, należy pamiętać, że przechowywanie będzie dotyczyło tylko dzienników utworzonych po zmianie wartości przechowywania (dzienniki zapisane w okresie, gdy czas przechowywania był ustawiony na nieograniczony, nawet po włączeniu przechowywania).

Alternatywą dla ręcznego tworzenia wystąpienia zarządzanego SQL jest używanie [programu PowerShell](scripts/create-configure-managed-instance-powershell.md), [programu PowerShell z szablonem Menedżer zasobów](scripts/create-powershell-azure-resource-manager-template.md)lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/mi#az-sql-mi-create) do tworzenia skryptów i automatyzowania tego procesu.

### <a name="migrate-your-databases"></a>Migrowanie baz danych

Po utworzeniu wystąpienia zarządzanego SQL i skonfigurowaniu dostępu można rozpocząć migrację baz danych SQL Server. Migracja może zakończyć się niepowodzeniem, jeśli w źródłowej bazie danych znajdują się pewne Nieobsługiwane funkcje, które mają zostać zmigrowane. Aby uniknąć błędów i sprawdzania zgodności, można użyć [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) do analizowania baz danych na SQL Server i znaleźć wszelkie problemy, które mogą blokować migrację do wystąpienia zarządzanego SQL, takie jak obecność [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) lub wiele plików dziennika. W przypadku rozwiązania tych problemów bazy danych są gotowe do migracji do wystąpienia zarządzanego SQL. [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview) to kolejne przydatne narzędzie, które może rejestrować obciążenie na SQL Server i powtarzać je w wystąpieniu zarządzanym SQL, dzięki czemu można określić, czy podczas migracji do wystąpienia zarządzanego SQL zostaną nawiązane jakiekolwiek problemy z wydajnością.

Po upewnieniu się, że można przeprowadzić migrację bazy danych do wystąpienia zarządzanego SQL, można użyć funkcji przywracania natywnego SQL Server, aby przywrócić bazę danych do wystąpienia zarządzanego SQL z `.bak` pliku. Za pomocą tej metody można migrować bazy danych z aparatu bazy danych SQL Server zainstalowane lokalnie lub Virtual Machines platformy Azure. Aby zapoznać się z przewodnikiem Szybki Start, zobacz [przywracanie z kopii zapasowej do wystąpienia zarządzanego SQL](restore-sample-database-quickstart.md). W tym przewodniku Szybki start wykonasz przywracanie z pliku `.bak` przechowywanego w usłudze Azure Blob Storage przy użyciu polecenia języka Transact-SQL `RESTORE`.

> [!TIP]
> Aby utworzyć kopię zapasową bazy danych w usłudze Azure Blob Storage za pomocą polecenia języka Transact-SQL `BACKUP`, zobacz [SQL Server backup to URL (Tworzenie kopii zapasowej programu SQL Server pod określonym adresem URL)](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Te Przewodniki Szybki Start pozwalają szybko tworzyć, konfigurować i przywracać kopie zapasowe bazy danych do wystąpienia zarządzanego SQL. W niektórych scenariuszach należy dostosować lub zautomatyzować wdrażanie wystąpienia zarządzanego SQL i wymaganego środowiska sieciowego. Te scenariusze są opisane poniżej.

## <a name="customize-network-environment"></a>Dostosowywanie środowiska sieciowego

Mimo że sieci wirtualnej/podsieci można automatycznie skonfigurować podczas [tworzenia wystąpienia przy użyciu Azure Portal](instance-create-quickstart.md), warto je utworzyć przed rozpoczęciem tworzenia wystąpień w wystąpieniu zarządzanym SQL, ponieważ można skonfigurować parametry sieci wirtualnej i podsieci. Najprostszym sposobem tworzenia i konfigurowania środowiska sieciowego jest użycie szablonu [wdrażania zasobów platformy Azure](virtual-network-subnet-create-arm-template.md) , który tworzy i konfiguruje sieć oraz podsieć, w której zostanie umieszczone wystąpienie. Wystarczy nacisnąć przycisk wdrażania usługi Azure Resource Manager i podać parametry w formularzu.

Jako alternatywy możesz użyć [skryptu programu PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) do zautomatyzowania tworzenia sieci.

Jeśli dysponujesz już siecią wirtualną i podsiecią, w której chcesz wdrożyć wystąpienie zarządzane SQL, musisz upewnić się, że sieć wirtualna i podsiecia spełniają [wymagania sieciowe](connectivity-architecture-overview.md#network-requirements). Użyj tego [skryptu programu PowerShell, aby zweryfikować, czy podsieć jest poprawnie skonfigurowana](vnet-existing-add-subnet.md). Ten skrypt sprawdza poprawność sieci i zgłasza wszelkie problemy, informując o tym, co należy zmienić, a następnie oferuje możliwość wprowadzenia niezbędnych zmian w sieci wirtualnej/podsieci. Uruchom ten skrypt, jeśli nie chcesz ręcznie konfigurować sieci wirtualnej i podsieci. Należy go także uruchomić po każdej znacznej zmianie konfiguracji infrastruktury sieciowej. Jeśli chcesz utworzyć i skonfigurować własną sieć, zapoznaj się z artykułem [Architektura łączności](connectivity-architecture-overview.md) i ten [ostateczny Przewodnik dotyczący tworzenia i konfigurowania środowiska wystąpienia zarządzanego SQL](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-sql-managed-instance"></a>Migrowanie do wystąpienia zarządzanego SQL

Wcześniej wymienione Przewodniki Szybki Start umożliwiają szybkie skonfigurowanie wystąpienia zarządzanego SQL i przeniesienie baz danych przy użyciu funkcji natywnej `RESTORE` . Jest to dobry punkt wyjścia, jeśli chcesz zakończyć szybkie sprawdzanie koncepcji i sprawdzić, czy rozwiązanie może współpracować z wystąpieniem zarządzanym.

Jednak w celu migrowania produkcyjnej bazy danych, a nawet tworzenia/testowania baz danych, które mają być używane w przypadku niektórych testów wydajności, należy rozważyć użycie pewnych dodatkowych technik, takich jak:

- Testowanie wydajności — należy mierzyć Podstawowe metryki wydajności w wystąpieniu SQL Server źródłowym i porównać je z metrykami wydajności w docelowym wystąpieniu zarządzanym SQL, w którym została zmigrowana baza danych. Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących porównywania wydajności](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Migracja w trybie online — w przypadku kodu natywnego `RESTORE` opisanego w tym artykule należy zaczekać, aż bazy danych mają zostać przywrócone (i skopiowane do usługi Azure Blob Storage, jeśli jeszcze nie są przechowywane). Może to spowodować przestój aplikacji, szczególnie jeśli bazy danych są duże. Aby przenieść produkcyjną bazę danych, użyj usługi [Data Migration Service (DMS)](../../dms/tutorial-sql-server-to-managed-instance.md?toc=%252fazure%252fsql-database%252ftoc.json) w celu migrowania bazy danych z minimalnym przestojem. Usługa DMS osiąga to przez przyrostowe wypychanie zmian wprowadzonych w źródłowej bazie danych do przywracanej bazy danych wystąpienia zarządzanego SQL. Dzięki temu można szybko przełączać aplikację z lokalizacji źródłowej do docelowej bazy danych przy minimalnym przestoju.

Dowiedz się więcej o [zalecanym procesie migracji](migrate-to-instance-from-sql-server.md).

## <a name="next-steps"></a>Następne kroki

- Tutaj znajduje się [Lista obsługiwanych funkcji w wystąpieniu zarządzanym SQL](../database/features-comparison.md) oraz [szczegóły i znane problemy](transact-sql-tsql-differences-sql-server.md).
- Poznaj [techniczne parametry wystąpienia zarządzanego SQL](resource-limits.md#service-tier-characteristics).
- Dowiedz się więcej na temat [sposobu używania wystąpienia zarządzanego SQL](how-to-content-reference-guide.md).
- [Zidentyfikuj właściwą jednostkę SKU wystąpienia zarządzanego usługi Azure SQL dla lokalnej bazy danych](/sql/dma/dma-sku-recommend-sql-db/).