---
title: Rozszerzona pomoc techniczna dla SQL Server 2008 & 2008 R2
description: Przedłuż wsparcie dla SQL Server 2008 i SQL Server 2008 R2 przez Migrowanie wystąpienia SQL Server do platformy Azure lub zakup rozszerzonej pomocy technicznej, aby zachować wystąpienia lokalnie.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 4c25adc16d14b4a5fb72ae0103ca05b193b40499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359170"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Rozszerzona pomoc techniczna dla SQL Server 2008 i SQL Server 2008 R2 z platformą Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

SQL Server 2008 i SQL Server 2008 R2 osiągnęły [koniec cyklu życia (EOS)](https://www.microsoft.com/sql-server/sql-server-2008). Ponieważ wielu klientów nadal używa obu wersji, udostępniamy kilka opcji, aby kontynuować pomoc techniczną. Lokalne wystąpienia SQL Server można migrować do usługi Azure Virtual Machines (VM), przeprowadzić migrację do Azure SQL Database lub zachować dostęp do lokalnych aktualizacji zabezpieczeń.

W przeciwieństwie do wystąpienia zarządzanego migracja do maszyny wirtualnej platformy Azure nie wymaga ponownej certyfikacji aplikacji. I w przeciwieństwie do lokalnego korzystania z usług lokalnych, migracja do maszyny wirtualnej platformy Azure spowoduje otrzymanie bezpłatnych rozszerzonych poprawek zabezpieczeń.

Pozostała część tego artykułu zawiera zagadnienia dotyczące migrowania wystąpienia SQL Server na maszynę wirtualną platformy Azure.

Aby uzyskać więcej informacji na temat końca opcji pomocy technicznej, zobacz [koniec obsługi](/sql/sql-server/end-of-support/sql-server-end-of-life-overview).

## <a name="provisioning"></a>Inicjowanie obsługi

Istnieje płatność zgodnie z rzeczywistym użyciem, **SQL Server 2008 R2 w obrazie systemu Windows Server 2008 R2** dostępnym w witrynie Azure Marketplace.

Klienci korzystający z SQL Server 2008 będą musieli samodzielnie zainstalować lub uaktualnić do SQL Server 2008 R2. Podobnie klienci korzystający z systemu Windows Server 2008 będą musieli wdrożyć maszynę wirtualną na podstawie niestandardowego wirtualnego dysku twardego lub uaktualnić do systemu Windows Server 2008 R2.

Obrazy wdrożone za pomocą witryny Azure Marketplace są dostarczane z wstępnie zainstalowanym rozszerzeniem SQL IaaS. Rozszerzenie SQL IaaS jest wymaganiem do elastycznego licencjonowania i zautomatyzowanego stosowania poprawek. Klienci, którzy wdrażają samoinstalujące się maszyny wirtualne, będą musieli ręcznie zainstalować rozszerzenie SQL IaaS. Rozszerzenie SQL IaaS nie jest obsługiwane w systemie Windows Server 2008.

> [!NOTE]
> Mimo że SQL Server **tworzenia** i **zarządzania** blokami będzie współdziałać z obrazem SQL Server 2008 R2 w Azure Portal, następujące funkcje _nie są obsługiwane_: automatyczne kopie zapasowe, integracja Azure Key Vault, usługi języka R i Konfiguracja magazynu.

## <a name="licensing"></a>Licencjonowanie
Wdrożenia z opcją płatność zgodnie z rzeczywistym użyciem SQL Server 2008 R2 mogą być konwertowane na [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Aby przekonwertować licencję opartą na oprogramowaniu Software Assurance (SA) na płatność zgodnie z rzeczywistym użyciem, klienci powinni zarejestrować się w [rozszerzeniu programu SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md). Po tej rejestracji typ licencji SQL będzie można zamiennie zmienić między Korzyść użycia hybrydowego platformy Azure i płatność zgodnie z rzeczywistym użyciem.

Na maszynie wirtualnej platformy Azure można zarejestrować samoinstalujące się SQL Server 2008 lub SQL Server 2008 R2, a następnie przekonwertować ich typ licencji na płatność zgodnie z rzeczywistym użyciem.

## <a name="migration"></a>Migracja
Można migrować wystąpienia EOS SQL Server na maszynę wirtualną platformy Azure przy użyciu metod ręcznego tworzenia kopii zapasowej/przywracania. Jest to najbardziej typowa Metoda migracji z lokalizacji lokalnej do maszyny wirtualnej platformy Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

W przypadku migracji zbiorczych zaleca się [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) usługi. Dzięki Azure Site Recovery klienci mogą replikować całą maszynę wirtualną, w tym SQL Server z lokalizacji lokalnej do maszyny wirtualnej platformy Azure.

Aby zagwarantować odzyskiwanie, SQL Server wymaga spójnych Azure Site Recovery migawek na poziomie aplikacji. Azure Site Recovery obsługuje migawki spójne z aplikacjami z co najmniej 1-godzinnym interwałem. Minimalny cel punktu odzyskiwania (RPO) możliwy dla SQL Server z Azure Site Recovery migracji wynosi 1 godzinę. Cel czasu odzyskiwania (RTO) to 2 godziny, a SQL Server czas odzyskiwania.

### <a name="database-migration-service"></a>Database Migration Service

[Azure Database Migration Service](../../../dms/dms-overview.md) jest opcją dla klientów w przypadku migrowania z lokalizacji lokalnej do maszyny wirtualnej platformy Azure przez uaktualnienie SQL Server do wersji 2012 lub nowszej.

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii

Rozwiązania do odzyskiwania po awarii dla EOS SQL Server na maszynie wirtualnej platformy Azure są następujące:

- **SQL Server kopii zapasowych**: Użyj Azure Backup, aby chronić EOS SQL Server 2008 i 2008 R2 przed wypadekm oprogramowania wymuszającego okup, przypadkowym usunięciem i uszkodzeniem z 15-minimalnym punktem odzyskiwania i odzyskiwaniem do punktu w czasie. Aby uzyskać więcej informacji, zobacz [ten artykuł](../../../backup/sql-support-matrix.md#scenario-support).
- **Wysyłanie dziennika**: możesz utworzyć replikę wysyłania dziennika w innej strefie lub regionie platformy Azure z ciągłymi przywracaniami, aby zmniejszyć RTO. Musisz ręcznie skonfigurować wysyłanie dziennika.
- **Azure Site Recovery**: można replikować maszynę wirtualną między strefami i regionami za poorednictwem replikacji Azure Site Recovery. SQL Server wymaga migawek spójnych z aplikacjami w celu zagwarantowania odzyskiwania w przypadku awarii. Azure Site Recovery oferuje co najmniej 1-godzinny cel punktu odzyskiwania oraz 2-godzinny (plus SQL Server) RTO na potrzeby odzyskiwania po awarii SQL Server.

## <a name="security-patching"></a>Stosowanie poprawek zabezpieczeń

Rozszerzone aktualizacje zabezpieczeń dla maszyn wirtualnych SQL Server są dostarczane za pośrednictwem kanałów Microsoft Update po zarejestrowaniu maszyny wirtualnej SQL Server z [rozszerzeniem agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md). Poprawki można pobrać ręcznie lub automatycznie.

Opcja *Automatyczne stosowanie poprawek* jest domyślnie włączona. Automatyczne stosowanie poprawek umożliwia platformie Azure automatyczne stosowanie poprawek programu SQL Server i systemu operacyjnego. Jeśli zainstalowano rozszerzenie IaaS SQL Server, można określić dzień tygodnia, godzinę i czas trwania okna obsługi. Platforma Azure stosuje poprawki w tym oknie obsługi. Harmonogram okna obsługi korzysta z ustawień regionalnych godziny maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [zautomatyzowane stosowanie poprawek dla SQL Server w usłudze Azure Virtual Machines](automated-patching.md).


## <a name="next-steps"></a>Następne kroki

Migrowanie maszyny wirtualnej SQL Server na platformę Azure:

* [Migrowanie bazy danych programu SQL Server do programu SQL Server na maszynie wirtualnej platformy Azure](migrate-to-vm-from-sql-server.md)

Rozpocznij pracę z usługą SQL Server na platformie Azure Virtual Machines:

* [Tworzenie maszyny wirtualnej z programem SQL Server w witrynie Azure Portal](sql-vm-create-portal-quickstart.md)

Uzyskaj odpowiedzi na często zadawane pytania dotyczące SQL Server maszyn wirtualnych:

* [Często zadawane pytania dotyczące SQL Server na platformie Azure Virtual Machines](frequently-asked-questions-faq.md)

Dowiedz się więcej na temat opcji końca obsługi oraz rozszerzone aktualizacje zabezpieczeń:

* [Koniec wsparcia](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)  &  [Rozszerzone aktualizacje zabezpieczeń](/sql/sql-server/end-of-support/sql-server-extended-security-updates)