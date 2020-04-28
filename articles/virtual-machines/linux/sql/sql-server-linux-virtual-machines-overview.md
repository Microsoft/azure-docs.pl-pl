---
title: Omówienie programu SQL Server na maszynach wirtualnych platformy Azure z systemem Linux | Microsoft Docs
description: Dowiedz się, jak uruchomić pełne wersje programu SQL Server na maszynach wirtualnych platformy Azure z systemem Linux. Uzyskaj bezpośrednie linki do wszystkich obrazów maszyn wirtualnych programu SQL Server z systemem Linux i powiązanej zawartości.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d376672336845958fb6434a78177f42aca938229
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "70081970"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Omówienie programu SQL Server na maszynach wirtualnych platformy Azure (system Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

Program SQL Server na maszynach wirtualnych platformy Azure pozwala na używanie pełnych wersji programu SQL Server w chmurze bez konieczności zarządzania sprzętem lokalnym. Maszyny wirtualne z programem SQL Server upraszczają również określanie kosztów licencjonowania w przypadku płatności zgodnie z rzeczywistym użyciem.

Maszyny wirtualne platformy Azure działają w wielu różnych [regionach geograficznych](https://azure.microsoft.com/regions/) na świecie. Oferują one także szereg różnych [rozmiarów maszyn](../sizes.md). Galeria obrazów maszyn wirtualnych umożliwia utworzenie maszyny wirtualnej z programem SQL Server we właściwej wersji i edycji oraz z odpowiednim systemem operacyjnym. Dzięki temu maszyny wirtualne to dobry wybór w przypadku wielu różnych obciążeń programu SQL Server.

## <a name="get-started-with-sql-vms"></a><a id="create"></a>Wprowadzenie do maszyn wirtualnych SQL

Aby rozpocząć, wybierz obraz maszyny wirtualnej z programem SQL Server w wymaganej wersji i edycji oraz z odpowiednim systemem operacyjnym. Poniższe sekcje zawierają bezpośrednie linki do obrazów maszyn wirtualnych programu SQL Server w galerii witryny Azure Portal.

> [!TIP]
> Aby uzyskać więcej informacji umożliwiających zrozumienie cennika obrazów SQL, zobacz [stronę cen dla maszyn wirtualnych z programem SQL Server i systemem Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Wersja | System operacyjny | Wersja |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Aby wyświetlić dostępne obrazy maszyn wirtualnych z programem SQL Server i systemem Windows, zobacz [Overview of SQL Server on Azure Virtual Machines (Windows) (Omówienie programu SQL Server na maszynach wirtualnych platformy Azure [system Windows])](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

## <a name="installed-packages"></a><a id="packages"></a> Zainstalowane pakiety

Po skonfigurowaniu programu SQL Server w systemie Linux zainstaluj pakiet aparatu bazy danych, a następnie kilka opcjonalnych pakietów w zależności od swoich wymagań. Obrazy maszyn wirtualnych z systemem Linux dla programu SQL Server automatycznie zainstalują większość pakietów. W poniższej tabeli przedstawiono pakiety instalowane dla poszczególnych dystrybucji.

| Dystrybucja | [Aparat bazy danych](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Narzędzia](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [Program SQL Server Agent](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Wyszukiwanie pełnotekstowe](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Dodatek wysokiej dostępności](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![nie](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![nie](./media/sql-server-linux-virtual-machines-overview/no.png) | ![nie](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="related-products-and-services"></a>Powiązane produkty i usługi

### <a name="linux-virtual-machines"></a>Maszyny wirtualne z systemem Linux

* [Omówienie maszyn wirtualnych](../overview.md)

### <a name="storage"></a>Magazyn

* [Wprowadzenie do usługi Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Networking

* [Przegląd Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [Adresy IP na platformie Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Tworzenie w pełni kwalifikowanej nazwy domeny w witrynie Azure Portal](../portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [Dokumentacja SQL Server on Linux](https://docs.microsoft.com/sql/linux)
* [Porównanie usługi Azure SQL Database](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do programu SQL Server na maszynach wirtualnych platformy Azure z systemem Linux:

* [Tworzenie maszyny wirtualnej z programem SQL Server w witrynie Azure Portal](provision-sql-server-linux-virtual-machine.md)

Uzyskaj odpowiedzi na często zadawane pytania dotyczące maszyn wirtualnych SQL z systemem Linux:

* [Często zadawane pytania dotyczące programu SQL Server na maszynach wirtualnych platformy Azure z systemem Linux](sql-server-linux-faq.md)
