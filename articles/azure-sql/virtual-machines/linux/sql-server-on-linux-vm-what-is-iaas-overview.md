---
title: Omówienie SQL Server w usłudze Azure Virtual Machines dla systemu Linux | Microsoft Docs
description: Dowiedz się, jak uruchamiać pełne wersje SQL Server w usłudze Azure Virtual Machines dla systemu Linux. Uzyskaj bezpośrednie linki do wszystkich obrazów maszyn wirtualnych programu SQL Server z systemem Linux i powiązanej zawartości.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41b74ed713485679576fdf7f4f0df54803b56caa
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192118"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Omówienie programu SQL Server na maszynach wirtualnych platformy Azure (system Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

SQL Server na platformie Azure Virtual Machines umożliwia korzystanie z pełnych wersji SQL Server w chmurze bez konieczności zarządzania sprzętem lokalnym. Maszyny wirtualne z programem SQL Server upraszczają również określanie kosztów licencjonowania w przypadku płatności zgodnie z rzeczywistym użyciem.

Maszyny wirtualne platformy Azure działają w wielu różnych [regionach geograficznych](https://azure.microsoft.com/regions/) na świecie. Oferują one także szereg różnych [rozmiarów maszyn](../../../virtual-machines/windows/sizes.md). Galeria obrazów maszyn wirtualnych umożliwia utworzenie maszyny wirtualnej z programem SQL Server we właściwej wersji i edycji oraz z odpowiednim systemem operacyjnym. Dzięki temu maszyny wirtualne to dobry wybór w przypadku wielu różnych obciążeń programu SQL Server. 

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a> Rozpoczynanie pracy z maszynami wirtualnymi SQL Server

Aby rozpocząć, wybierz obraz maszyny wirtualnej z programem SQL Server w wymaganej wersji i edycji oraz z odpowiednim systemem operacyjnym. Poniższe sekcje zawierają bezpośrednie linki do obrazów maszyn wirtualnych programu SQL Server w galerii witryny Azure Portal.

> [!TIP]
> Aby uzyskać więcej informacji na temat sposobu zrozumienia cen dla SQL Server obrazów, zobacz [stronę cennika dla maszyn wirtualnych z systemem Linux, na których działa program SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Wersja | System operacyjny | Wersja |
| --- | --- | --- |
| **SQL Server 2019** | Ubuntu 18.04 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804sqldev-ARM) | 
| **SQL Server 2019** | Red Hat Enterprise Linux (RHEL) 8 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8sqldev-ARM)|
| **SQL Server 2019** | SUSE Linux Enterprise Server (SLES) V12 z dodatkiem SP5 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5sqldev-ARM)|
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Aby wyświetlić dostępne SQL Server obrazy maszyn wirtualnych dla systemu Windows, zobacz [omówienie SQL Server na platformie Azure Virtual Machines (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

## <a name="installed-packages"></a><a id="packages"></a> Zainstalowane pakiety

Podczas konfigurowania SQL Server on Linux należy zainstalować pakiet aparat bazy danych, a następnie kilka opcjonalnych pakietów w zależności od wymagań. Obrazy maszyn wirtualnych z systemem Linux dla programu SQL Server automatycznie zainstalują większość pakietów. W poniższej tabeli przedstawiono pakiety instalowane dla poszczególnych dystrybucji.

| Dystrybucja | [Aparat bazy danych](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Narzędzia](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [Agent SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Wyszukiwanie pełnotekstowe](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Dodatek wysokiej dostępności](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |
| SLES | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![nie](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png)|
| Ubuntu | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![tak](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>Powiązane produkty i usługi

### <a name="linux-virtual-machines"></a>Maszyny wirtualne z systemem Linux

* [Omówienie usługi Azure Virtual Machines](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>Storage

* [Wprowadzenie do usługi Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Sieć

* [Przegląd Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [Adresy IP na platformie Azure](../../../virtual-network/public-ip-addresses.md)
* [Tworzenie w pełni kwalifikowanej nazwy domeny w witrynie Azure Portal](../../../virtual-machines/windows/portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [Dokumentacja SQL Server on Linux](https://docs.microsoft.com/sql/linux)
* [Porównanie usługi Azure SQL Database](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do SQL Server on Linux maszyn wirtualnych:

* [Tworzenie maszyny wirtualnej z programem SQL Server w witrynie Azure Portal](sql-vm-create-portal-quickstart.md)

Uzyskaj odpowiedzi na często zadawane pytania dotyczące SQL Server maszyn wirtualnych w systemie Linux:

* [Często zadawane pytania dotyczące programu SQL Server w usłudze Azure Virtual Machines](frequently-asked-questions-faq.md)
