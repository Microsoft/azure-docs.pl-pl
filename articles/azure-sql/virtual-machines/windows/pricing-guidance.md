---
title: Wskazówki dotyczące cen & zarządzanie kosztami
description: Zawiera najlepsze rozwiązania dotyczące wybierania odpowiednich SQL Server modelu cen maszyn wirtualnych.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: d980b92bc4effc58ef84ef6ec70c3908f575e484
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615605"
---
# <a name="pricing-guidance-for-sql-server-on-azure-vms"></a>Wskazówki dotyczące cen dla SQL Server na maszynach wirtualnych platformy Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ten artykuł zawiera wskazówki dotyczące cen [SQL Server w usłudze Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md). Istnieje kilka opcji, które mają wpływ na koszt i ważne jest wybranie odpowiedniego obrazu, który równoważy koszty związane z wymaganiami biznesowymi.

> [!TIP]
> Jeśli konieczne jest tylko znalezienie oszacowania kosztów dla określonej kombinacji rozmiaru SQL Server i maszyny wirtualnej (VM), zobacz stronę z cennikiem dla [systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) lub [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Wybierz platformę i wersję SQL Server z listy **system operacyjny/oprogramowanie** .
>
> ![Interfejs użytkownika na stronie cennika maszyny wirtualnej](./media/pricing-guidance/virtual-machines-pricing-ui.png)
>
> Aby dodać i skonfigurować maszynę wirtualną, użyj [kalkulatora cen](https://azure.microsoft.com/pricing/#explore-cost) . 

## <a name="free-licensed-sql-server-editions"></a>Bezpłatne licencjonowane wersje SQL Server

Jeśli chcesz opracowywać, testować lub kompilować weryfikację koncepcji, użyj bezpłatnie licencjonowanej **wersji SQL Server Developer**. Ta wersja zawiera wszystkie funkcje wersji SQL Server Enterprise, co pozwala na kompilowanie i testowanie aplikacji dowolnego typu. Nie można jednak uruchomić wersji Developer w środowisku produkcyjnym. Na maszynie wirtualnej SQL Server Developer Edition są naliczane opłaty za koszt maszyny wirtualnej, ponieważ nie ma żadnych powiązanych SQL Server kosztów licencjonowania.

Jeśli chcesz uruchomić lekkie obciążenie w środowisku produkcyjnym (<4 rdzenie, <1 GB pamięci, <10 GB/bazy danych), użyj swobodnie licencjonowanej **SQL Server Express Edition**. Na maszynie wirtualnej SQL Server Express Edition są również naliczane opłaty za koszt maszyny wirtualnej.

W przypadku takich obciążeń związanych z programowaniem/testowaniem i lekkimi produkcjami można także zaoszczędzić pieniądze, wybierając mniejszy rozmiar maszyny wirtualnej, który jest zgodny z tymi obciążeniami. DS1v2 może być dobrym wyborem w niektórych scenariuszach.

Aby utworzyć maszynę wirtualną platformy Azure z systemem SQL Server 2017 przy użyciu jednego z tych obrazów, zobacz następujące linki:

| Platforma | Bezpłatnie licencjonowane obrazy |
|---|---|
| Windows Server 2016 | [Maszyna wirtualna platformy Azure w SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[Maszyna wirtualna platformy Azure w SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [Maszyna wirtualna platformy Azure w SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[Maszyna wirtualna platformy Azure w SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Maszyna wirtualna platformy Azure w SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[Maszyna wirtualna platformy Azure w SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [Maszyna wirtualna platformy Azure w SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[Maszyna wirtualna platformy Azure w SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Płatne wersje SQL Server

W przypadku nielekkiego obciążenia produkcyjnego należy użyć jednej z następujących wersji SQL Server:

| Wydanie programu SQL Server | Obciążenie |
|-----|-----|
| Internet | Małe witryny sieci Web |
| Standardowa | Małe i średnie obciążenia |
| Przedsiębiorstwa | Duże lub krytyczne dla działalności obciążenia|

Dostępne są dwie opcje płacenia za SQL Server Licencjonowanie dla następujących wersji: *płatność za użycie* lub dostarczenie *własnej licencji (BYOL)*.

## <a name="pay-per-usage"></a>Płatność za użycie

**Płatność za licencję SQL Server na użycie** (znana także jako **płatność zgodnie z rzeczywistym** użyciem) oznacza, że koszt usługi SQL Server na sekundę jest określany jako koszt na korzystanie z maszyny wirtualnej platformy Azure. Cennik dla różnych wersji SQL Server (Web, standard, Enterprise) można zobaczyć na stronie cennika usługi Azure Virtual Machines dla [systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) lub [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

Koszt jest taki sam dla wszystkich wersji SQL Server (2012 SP3 do 2019). Koszt licencji na sekundę zależy od liczby procesorów wirtualnych vCPU maszyny wirtualnej.

Płatność SQL Server Licencjonowanie za użycie jest zalecana dla:

- **Obciążenia tymczasowe lub okresowe**. Na przykład aplikacja, która wymaga obsługi zdarzenia przez kilka miesięcy w każdym roku lub analiza biznesowa w poniedziałek.

- **Obciążenia z nieznanym okresem istnienia lub skalowaniem**. Na przykład aplikacja, która może nie być wymagana w ciągu kilku miesięcy lub która może wymagać większej lub mniejszej mocy obliczeniowej, zależy od popytu.

Aby utworzyć maszynę wirtualną platformy Azure z systemem SQL Server 2017 przy użyciu jednego z tych obrazów z opcją płatność zgodnie z rzeczywistym użyciem, zobacz następujące linki:

| Platforma | Licencjonowane obrazy |
|---|---|
| Windows Server 2016 | [Maszyna wirtualna w SQL Server 2017 sieci Web platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 standardową maszynę wirtualną platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[Maszyna wirtualna w SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [Maszyna wirtualna w SQL Server 2017 sieci Web platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 standardową maszynę wirtualną platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[Maszyna wirtualna w SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Maszyna wirtualna w SQL Server 2017 sieci Web platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 standardową maszynę wirtualną platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[Maszyna wirtualna w SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [Maszyna wirtualna w SQL Server 2017 sieci Web platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 standardową maszynę wirtualną platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[Maszyna wirtualna w SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Po utworzeniu SQL Serverj maszyny wirtualnej w Azure Portal okno **Wybieranie rozmiaru** pokazuje szacowany koszt. Należy zwrócić uwagę, że to oszacowanie dotyczy tylko kosztów obliczeniowych związanych z uruchamianiem maszyny wirtualnej wraz z wszelkimi kosztami licencjonowania systemu operacyjnego (system Windows lub systemy operacyjne innych firm).
>
> ![Blok Wybieranie rozmiaru maszyny wirtualnej](./media/pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Nie obejmują one dodatkowych kosztów licencjonowania SQL Server dla wersji Web, standard i Enterprise. Aby uzyskać najbardziej dokładne oszacowanie cen, wybierz system operacyjny i wersję SQL Server na stronie cennika dla [systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) lub [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

> [!NOTE]
> Teraz można zmienić model licencjonowania z opcji płatność za użycie, aby zapewnić własną licencję (BYOL) i z powrotem. Aby uzyskać więcej informacji, zobacz [How to Change the Licensing model for a SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md). 

## <a name="bring-your-own-license-byol"></a><a id="byol"></a> Bring your own license (BYOL)

Udostępnienie **własnej SQL Server licencji za pośrednictwem Przenośność licencji**, nazywanego również **BYOL**, oznacza użycie istniejącej licencji zbiorczej SQL Server z programem Software Assurance na maszynie wirtualnej platformy Azure. SQL Server maszyny wirtualnej korzystającej z BYOL opłaty są naliczane tylko za koszt działania maszyny wirtualnej, a nie do licencjonowania SQL Server, pod warunkiem, że licencje i program Software Assurance zostały już nabyte za pośrednictwem programu licencjonowania zbiorowego lub przez partnera rozwiązań w chmurze (CSP).

> [!NOTE]
> Obrazy BYOL są obecnie dostępne tylko dla maszyn wirtualnych z systemem Windows. Można jednak ręcznie zainstalować SQL Server na maszynie wirtualnej z systemem Linux. Zapoznaj się z wytycznymi w [SQL Server na maszynie wirtualnej z systemem Linux — często zadawane pytania](../linux/frequently-asked-questions-faq.md).

Udostępnienie własnej SQL Server licencjonowania za poorednictwem przenośność licencji jest zalecane w przypadku:

- **Obciążenia ciągłe**. Na przykład aplikacja, która musi obsługiwać operacje biznesowe 24x7.

- **Obciążenia o znanym okresie istnienia i skali**. Na przykład aplikacja, która jest wymagana przez cały rok, a zapotrzebowanie jest prognozowane.

Aby używać BYOL z maszyną wirtualną SQL Server, musisz mieć licencję na SQL Server Standard lub Enterprise i [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1), która jest wymagana w ramach niektórych programów licencjonowania zbiorowego i opcjonalnego zakupu z innymi. Poziom cen zapewniany za pośrednictwem programów licencjonowania zbiorowego różni się w zależności od typu umowy i ilości oraz zaangażowania do SQL Server. Jednak jako reguła kciuka, dostosowanie własnej licencji do ciągłego obciążeń produkcyjnych ma następujące zalety:

| Korzyść BYOL | Opis |
|-----|-----|
| **Redukcja kosztów** | [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) oferuje do 55% oszczędności. Aby uzyskać więcej informacji, zobacz [przełączanie modelu licencjonowania](licensing-model-azure-hybrid-benefit-ahb-change.md) |
| **Bezpłatna pasywna replika pomocnicza** | Kolejną zaletą korzystania z własnej licencji jest [bezpłatna Licencjonowanie jednej pasywnej repliki pomocniczej](https://azure.microsoft.com/pricing/licensing-faq/) na SQL Server w celu zapewnienia wysokiej dostępności. Spowoduje to odcięcie kosztu licencjonowania SQL Server wdrożenia o wysokiej dostępności (na przykład przy użyciu zawsze włączonych grup dostępności). Prawa do uruchamiania pasywnego serwera pomocniczego są dostępne za pomocą korzyści z programu Fail-Over Server Software Assurance. |

Aby utworzyć maszynę wirtualną platformy Azure z systemem SQL Server 2017 przy użyciu jednego z tych obrazów dołączenia do własnych licencji, zobacz maszyny wirtualne z prefiksem "{BYOL}":

- [Maszyna wirtualna w SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 standardową maszynę wirtualną platformy Azure](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Daj nam znać w ciągu 10 dni, jak wiele licencji SQL Server używanych na platformie Azure. Linki do poprzednich obrazów zawierają instrukcje, jak to zrobić.

> [!NOTE]
> Teraz można zmienić model licencjonowania z opcji płatność za użycie, aby zapewnić własną licencję (BYOL) i z powrotem. Aby uzyskać więcej informacji, zobacz [How to Change the Licensing model for a SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md). 



## <a name="reduce-costs"></a>Redukcja kosztów

Aby uniknąć niepotrzebnych kosztów, wybierz optymalny rozmiar maszyny wirtualnej i rozważ sporadyczne zamknięcia dla obciążeń nieciągłych.

### <a name="correctly-size-your-vm"></a><a id="machinesize"></a> Prawidłowe dopasowanie rozmiaru maszyny wirtualnej

Koszt licencjonowania SQL Server jest bezpośrednio związany z liczbą procesorów wirtualnych vCPU. Wybierz rozmiar maszyny wirtualnej odpowiadający oczekiwanym potrzebom dotyczącym przepustowości procesora CPU, pamięci, magazynu i operacji we/wy. Aby uzyskać pełną listę opcji rozmiaru maszyn, zobacz [rozmiary maszyn wirtualnych systemu Windows](../../../virtual-machines/sizes.md) i [rozmiary maszyn wirtualnych](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)z systemem Linux.

Istnieją nowe rozmiary maszyn, które dobrze sprawdzają się w przypadku niektórych typów obciążeń SQL Server. Te maszyny zajmują dużo poziomów pamięci, magazynu i przepustowości we/wy, ale mają mniejszą liczbę rdzeni zwirtualizowanych. Rozważmy na przykład następujący przykład:

| Rozmiar maszyny wirtualnej | Procesory wirtualne | Pamięć | Maksymalna liczba dysków | Maksymalna przepustowość operacji we/wy | SQL Server koszty licencjonowania | Łączne koszty (obliczeniowe + Licencjonowanie) |
|---|---|---|---|---|---|---|
| **Standardowa_DS14v2** | 16 | 112 GB | 32 | 51 200 operacji we/wy lub 768 MB/s | | |
| **Standardowa_DS14-4v2** | 4 | 112 GB | 32 | 51 200 operacji we/wy lub 768 MB/s | 75% niższy | 57% niższy |

> [!IMPORTANT]
> To jest przykład do punktu w czasie. Najnowsze specyfikacje można znaleźć w artykułach o rozmiarach maszyn i na stronie z cennikiem platformy Azure dla [systemów Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

W poprzednim przykładzie można zobaczyć, że specyfikacje dla **Standard_DS14v2** i **Standard_DS14-4v2** są identyczne, z wyjątkiem procesorów wirtualnych vCPU. Sufiks **-4v2** na końcu rozmiaru maszyny **Standard_DS14-4v2** wskazuje liczbę aktywnych procesorów wirtualnych vCPU. Ponieważ koszty licencjonowania SQL Server są powiązane z liczbą procesorów wirtualnych vCPU, znacznie zmniejsza to koszt maszyny wirtualnej w scenariuszach, w których dodatkowe procesorów wirtualnych vCPU nie są potrzebne. Jest to jeden przykład, a istnieje wiele rozmiarów maszyn z ograniczeniami procesorów wirtualnych vCPU, które są identyfikowane za pomocą tego wzorca sufiksu. Aby uzyskać więcej informacji, zapoznaj się z wpisem w blogu, który [ogłasza nowe rozmiary maszyn wirtualnych platformy Azure w celu uzyskania bardziej ekonomicznej pracy bazy danych](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Zamknij maszynę wirtualną, gdy jest to możliwe

Jeśli używasz wszelkich obciążeń, które nie działają w sposób ciągły, rozważ wyłączenie maszyny wirtualnej w nieaktywnych okresach. Płaci się wyłącznie za użyte zasoby.

Jeśli na przykład po prostu próbujesz wypróbować SQL Server na maszynie wirtualnej platformy Azure, nie chcesz naliczać opłat przez przypadkowe pozostawienie jej w tygodniach. Jednym z rozwiązań jest użycie [funkcji automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![SQL Server automatyczne zamykanie maszyny wirtualnej](./media/pricing-guidance/sql-vm-auto-shutdown.png)

Automatyczne zamykanie jest częścią większego zestawu podobnych funkcji zapewnianych przez [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

W przypadku innych przepływów pracy należy rozważyć automatyczne wyłączenie i ponowne uruchomienie maszyn wirtualnych platformy Azure przy użyciu rozwiązania do obsługi skryptów, takiego jak [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Wyłączenie i cofnięcie przydziału maszyny wirtualnej jest jedynym sposobem, aby uniknąć naliczania opłat. Po prostu zatrzymywanie lub korzystanie z opcji zasilacza do zamykania maszyny wirtualnej nadal wiąże się z opłatami za użycie.

## <a name="next-steps"></a>Następne kroki

Ogólne wskazówki dotyczące cen platformy Azure można znaleźć w temacie [zapobieganie nieoczekiwanym kosztom rozliczeń i zarządzania kosztami platformy Azure](../../../cost-management-billing/cost-management-billing-overview.md). Najnowsze ceny Virtual Machines platformy Azure, w tym SQL Server, można znaleźć na stronie cennika usługi Azure Virtual Machines dla maszyn [wirtualnych z systemem Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i [maszyn wirtualnych systemu Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Aby zapoznać się z omówieniem SQL Server w usłudze Azure Virtual Machines, zobacz następujące artykuły:

- [Omówienie SQL Server na maszynach wirtualnych z systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Przegląd SQL Server on Linux maszyn wirtualnych](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
