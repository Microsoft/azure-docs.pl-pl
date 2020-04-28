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
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: b7a8f7136d0899ab3afe04bce2d25bc2e7f8d3e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75981727"
---
# <a name="pricing-guidance-for-azure-sql-server-vms"></a>Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server

Ten artykuł zawiera wskazówki dotyczące cen dla [SQL Server maszyn wirtualnych](virtual-machines-windows-sql-server-iaas-overview.md) na platformie Azure. Istnieje kilka opcji, które mają wpływ na koszt i ważne jest wybranie odpowiedniego obrazu, który równoważy koszty związane z wymaganiami biznesowymi.

> [!TIP]
> Jeśli potrzebujesz tylko oszacowania kosztów dla konkretnej kombinacji wersji SQL Server i rozmiaru maszyny wirtualnej, zobacz stronę z cennikiem dla [systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) lub [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Wybierz platformę i wersję SQL Server z listy **system operacyjny/oprogramowanie** .
>
> ![Interfejs użytkownika na stronie cennika maszyny wirtualnej](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Aby dodać i skonfigurować maszynę wirtualną, użyj [kalkulatora cen](https://azure.microsoft.com/pricing/#explore-cost) . 

## <a name="free-licensed-sql-server-editions"></a>Bezpłatne licencjonowane wersje SQL Server

Jeśli chcesz opracowywać, testować lub kompilować weryfikację koncepcji, użyj bezpłatnie licencjonowanej **wersji SQL Server Developer**. Ta wersja zawiera wszystkie funkcje wersji SQL Server Enterprise, co pozwala na kompilowanie i testowanie aplikacji dowolnego typu. Nie można jednak uruchomić wersji Developer w środowisku produkcyjnym. Na maszynie wirtualnej SQL Server Developer Edition są naliczane opłaty za koszt maszyny wirtualnej, ponieważ nie ma żadnych powiązanych SQL Server kosztów licencjonowania.

Jeśli chcesz uruchomić lekkie obciążenie w środowisku produkcyjnym (<4 rdzenie, <1 GB pamięci, <10 GB/bazy danych), użyj swobodnie licencjonowanej **SQL Server Express Edition**. Na maszynie wirtualnej SQL Server Express Edition są również naliczane opłaty za koszt maszyny wirtualnej.

W przypadku takich obciążeń związanych z programowaniem/testowaniem i lekkimi produkcjami można także zaoszczędzić pieniądze, wybierając mniejszy rozmiar maszyny wirtualnej, który jest zgodny z tymi obciążeniami. DS1v2 może być dobrym wyborem w niektórych scenariuszach.

Aby utworzyć maszynę wirtualną z systemem SQL Server 2017 przy użyciu jednego z tych obrazów, zobacz następujące linki:

| Platforma | Bezpłatnie licencjonowane obrazy |
|---|---|
| Windows Server 2016 | [Maszyna wirtualna platformy Azure w SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[Maszyna wirtualna platformy Azure w SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [Maszyna wirtualna platformy Azure w SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[Maszyna wirtualna platformy Azure w SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Maszyna wirtualna platformy Azure w SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[Maszyna wirtualna platformy Azure w SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [Maszyna wirtualna platformy Azure w SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[Maszyna wirtualna platformy Azure w SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Płatne wersje SQL Server

W przypadku nielekkiego obciążenia produkcyjnego należy użyć jednej z następujących wersji SQL Server:

| SQL Server Edition | Obciążenie |
|-----|-----|
| sieć Web | Małe witryny sieci Web |
| Standardowa | Małe i średnie obciążenia |
| Enterprise | Duże lub krytyczne dla działalności obciążenia|

Dostępne są dwie opcje płacenia za SQL Server Licencjonowanie dla następujących wersji: *płatność za użycie* lub dostarczenie *własnej licencji (BYOL)*.

## <a name="pay-per-usage"></a>Płatność za użycie

**Płatność za licencję SQL Server na użycie** (znana także jako **płatność zgodnie z rzeczywistym**użyciem) oznacza, że koszt usługi SQL Server na sekundę jest określany jako koszt na korzystanie z maszyny wirtualnej platformy Azure. Cennik dla różnych wersji SQL Server (Web, standard, Enterprise) można zobaczyć na stronie cennika maszyny wirtualnej platformy Azure dla [systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) lub [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

Koszt jest taki sam dla wszystkich wersji SQL Server (2012 SP3 do 2017). Koszt licencji na sekundę zależy od liczby procesorów wirtualnych vCPU maszyny wirtualnej.

Płatność SQL Server Licencjonowanie za użycie jest zalecana dla:

- **Obciążenia tymczasowe lub okresowe**. Na przykład aplikacja, która wymaga obsługi zdarzenia przez kilka miesięcy w każdym roku lub analiza biznesowa w poniedziałek.

- **Obciążenia z nieznanym okresem istnienia lub skalowaniem**. Na przykład aplikacja, która może nie być wymagana w ciągu kilku miesięcy lub która może wymagać większej lub mniejszej mocy obliczeniowej, zależy od popytu.

Aby utworzyć maszynę wirtualną z systemem SQL Server 2017 przy użyciu jednego z tych obrazów z opcją płatność zgodnie z rzeczywistym użyciem, zobacz następujące linki:

| Platforma | Licencjonowane obrazy |
|---|---|
| Windows Server 2016 | [Maszyna wirtualna w SQL Server 2017 sieci Web platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 standardową maszynę wirtualną platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[Maszyna wirtualna w SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [Maszyna wirtualna w SQL Server 2017 sieci Web platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 standardową maszynę wirtualną platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[Maszyna wirtualna w SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Maszyna wirtualna w SQL Server 2017 sieci Web platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 standardową maszynę wirtualną platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[Maszyna wirtualna w SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [Maszyna wirtualna w SQL Server 2017 sieci Web platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 standardową maszynę wirtualną platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[Maszyna wirtualna w SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> W przypadku tworzenia SQL Server maszyny wirtualnej w portalu okno **Wybieranie rozmiaru** zawiera szacowany koszt. Należy zwrócić uwagę, że to oszacowanie dotyczy tylko kosztów obliczeniowych związanych z uruchamianiem maszyny wirtualnej wraz z wszelkimi kosztami licencjonowania systemu operacyjnego (system Windows lub systemy operacyjne innych firm).
>
> ![Blok Wybieranie rozmiaru maszyny wirtualnej](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Nie obejmują one dodatkowych kosztów licencjonowania SQL Server dla wersji Web, standard i Enterprise. Aby uzyskać najbardziej dokładne oszacowanie cen, wybierz system operacyjny i wersję SQL Server na stronie cennika dla [systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) lub [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

> [!NOTE]
> Teraz można zmienić model licencjonowania z opcji płatność za użycie, aby zapewnić własną licencję (BYOL) i z powrotem. Aby uzyskać więcej informacji, zobacz [Jak zmienić model licencjonowania dla maszyny wirtualnej SQL](virtual-machines-windows-sql-ahb.md). 

## <a name="bring-your-own-license-byol"></a><a id="byol"></a>Bring your own license (BYOL)

Udostępnienie **własnej SQL Server licencji za pośrednictwem Przenośność licencji**, nazywanego również **BYOL**, oznacza użycie istniejącej licencji zbiorczej SQL Server z programem Software Assurance na maszynie wirtualnej platformy Azure. Maszyna wirtualna w SQL Server przy użyciu usługi BYOL nalicza opłaty tylko za koszt działania maszyny wirtualnej, a nie do licencjonowania SQL Server, zważywszy, że licencje i program Software Assurance zostały już nabyte za pośrednictwem programu licencjonowania zbiorowego.

> [!IMPORTANT]
> Obrazy BYOL wymagają Umowa Enterprise z programem Software Assurance. W tej chwili nie są dostępne jako część partnera rozwiązań w chmurze (CSP) platformy Azure. Klienci korzystający z programu CSP mogą przenieść własną licencję przez wdrożenie obrazu z opcją płatność zgodnie z rzeczywistym użyciem, a następnie włączenie [korzyść użycia hybrydowego platformy Azure](virtual-machines-windows-sql-ahb.md).

> [!NOTE]
> Obrazy BYOL są obecnie dostępne tylko dla maszyn wirtualnych z systemem Windows. Można jednak ręcznie zainstalować SQL Server na maszynie wirtualnej z systemem Linux. Zapoznaj się z wytycznymi dotyczącymi [często zadawanych pytań dotyczących maszyn wirtualnych systemu Linux](../../linux/sql/sql-server-linux-faq.md)

Udostępnienie własnego licencjonowania SQL za przenośność licencji jest zalecane w przypadku:

- **Obciążenia ciągłe**. Na przykład aplikacja, która musi obsługiwać operacje biznesowe 24x7.

- **Obciążenia o znanym okresie istnienia i skali**. Na przykład aplikacja, która jest wymagana przez cały rok, a zapotrzebowanie jest prognozowane.

Aby używać BYOL z maszyną wirtualną SQL Server, musisz mieć licencję na SQL Server Standard lub Enterprise i [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1), która jest wymagana w ramach niektórych programów licencjonowania zbiorowego i opcjonalnego zakupu z innymi. Poziom cen zapewniany za pośrednictwem programów licencjonowania zbiorowego różni się w zależności od typu umowy i ilości oraz zaangażowania do SQL Server. Jednak jako reguła kciuka, dostosowanie własnej licencji do ciągłego obciążeń produkcyjnych ma następujące zalety:

| Korzyść BYOL | Opis |
|-----|-----|
| **Redukcja kosztów** | [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) oferuje do 55% oszczędności. Aby uzyskać więcej informacji, zobacz [przełączanie modelu licencjonowania](virtual-machines-windows-sql-ahb.md) |
| **Bezpłatna pasywna replika pomocnicza** | Kolejną zaletą korzystania z własnej licencji jest [bezpłatna Licencjonowanie jednej pasywnej repliki pomocniczej](https://azure.microsoft.com/pricing/licensing-faq/) na SQL Server w celu zapewnienia wysokiej dostępności. Spowoduje to odcięcie kosztu licencjonowania SQL Server wdrożenia o wysokiej dostępności (na przykład przy użyciu zawsze włączonych grup dostępności). Prawa do uruchamiania pasywnego serwera pomocniczego są dostępne za pomocą wsparcia Software Assurance w trybie failover. |

Aby utworzyć maszynę wirtualną z systemem SQL Server 2017 przy użyciu jednego z tych obrazów dołączenia do własnych licencji, zobacz maszyny wirtualne z prefiksem "{BYOL}":

- [Maszyna wirtualna w SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 standardową maszynę wirtualną platformy Azure](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Daj nam znać w ciągu 10 dni, jak wiele licencji SQL Server używanych na platformie Azure. Linki do poprzednich obrazów zawierają instrukcje, jak to zrobić.

> [!NOTE]
> Teraz można zmienić model licencjonowania z opcji płatność za użycie, aby zapewnić własną licencję (BYOL) i z powrotem. Aby uzyskać więcej informacji, zobacz [Jak zmienić model licencjonowania dla maszyny wirtualnej SQL](virtual-machines-windows-sql-ahb.md). 



## <a name="reduce-costs"></a>Redukcja kosztów

Aby uniknąć niepotrzebnych kosztów, wybierz optymalny rozmiar maszyny wirtualnej i rozważ sporadyczne zamknięcia dla obciążeń nieciągłych.

### <a name="correctly-size-your-vm"></a><a id="machinesize"></a>Prawidłowe dopasowanie rozmiaru maszyny wirtualnej

Koszt licencjonowania SQL Server jest bezpośrednio związany z liczbą procesorów wirtualnych vCPU. Wybierz rozmiar maszyny wirtualnej odpowiadający oczekiwanym potrzebom dotyczącym przepustowości procesora CPU, pamięci, magazynu i operacji we/wy. Aby uzyskać pełną listę opcji rozmiaru maszyn, zobacz [rozmiary maszyn wirtualnych systemu Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) i [rozmiary maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)z systemem Linux.

Istnieją nowe rozmiary maszyn, które dobrze sprawdzają się w przypadku niektórych typów obciążeń SQL Server. Te maszyny zajmują dużo poziomów pamięci, magazynu i przepustowości we/wy, ale mają mniejszą liczbę rdzeni zwirtualizowanych. Rozważmy na przykład następujący przykład:

| Rozmiar maszyny wirtualnej | Procesory wirtualne | Memory (Pamięć) | Maksymalna liczba dysków | Maksymalna przepływność we/wy | Koszty licencji SQL | Łączne koszty (obliczeniowe + Licencjonowanie) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51 200 operacji we/wy lub 768 MB/s | | |
| **Standardowa_DS14-4v2** | 4 | 112 GB | 32 | 51 200 operacji we/wy lub 768 MB/s | 75% niższy | 57% niższy |

> [!IMPORTANT]
> To jest przykład do punktu w czasie. Najnowsze specyfikacje można znaleźć w artykułach o rozmiarach maszyn i na stronie z cennikiem platformy Azure dla [systemów Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

W poprzednim przykładzie można zobaczyć, że specyfikacje dla **Standard_DS14v2** i **Standard_DS14-4v2** są identyczne, z wyjątkiem procesorów wirtualnych vCPU. Sufiks **-4v2** na końcu rozmiaru maszyny **Standard_DS14-4v2** wskazuje liczbę aktywnych procesorów wirtualnych vCPU. Ponieważ koszty licencjonowania SQL Server są powiązane z liczbą procesorów wirtualnych vCPU, znacznie zmniejsza to koszt maszyny wirtualnej w scenariuszach, w których dodatkowe procesorów wirtualnych vCPU nie są potrzebne. Jest to jeden przykład, a istnieje wiele rozmiarów maszyn z ograniczeniami procesorów wirtualnych vCPU, które są identyfikowane za pomocą tego wzorca sufiksu. Aby uzyskać więcej informacji, zapoznaj się z wpisem w blogu, który [ogłasza nowe rozmiary maszyn wirtualnych platformy Azure w celu uzyskania bardziej ekonomicznej pracy bazy danych](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Zamknij maszynę wirtualną, gdy jest to możliwe

Jeśli używasz wszelkich obciążeń, które nie działają w sposób ciągły, rozważ wyłączenie maszyny wirtualnej w nieaktywnych okresach. Płaci się wyłącznie za użyte zasoby.

Jeśli na przykład po prostu próbujesz wypróbować SQL Server na maszynie wirtualnej platformy Azure, nie chcesz naliczać opłat przez przypadkowe pozostawienie jej w tygodniach. Jednym z rozwiązań jest użycie [funkcji automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Automatyczne zamykanie maszyny wirtualnej SQL](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Automatyczne zamykanie jest częścią większego zestawu podobnych funkcji zapewnianych przez [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

W przypadku innych przepływów pracy należy rozważyć automatyczne wyłączenie i ponowne uruchomienie maszyn wirtualnych platformy Azure przy użyciu rozwiązania do obsługi skryptów, takiego jak [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Wyłączenie i cofnięcie przydziału maszyny wirtualnej jest jedynym sposobem, aby uniknąć naliczania opłat. Po prostu zatrzymywanie lub korzystanie z opcji zasilacza do zamykania maszyny wirtualnej nadal wiąże się z opłatami za użycie.

## <a name="next-steps"></a>Następne kroki

Ogólne wskazówki dotyczące cen platformy Azure można znaleźć w temacie [zapobieganie nieoczekiwanym kosztom rozliczeń i zarządzania kosztami platformy Azure](../../../cost-management-billing/manage/getting-started.md). Najnowsze ceny Virtual Machines, w tym SQL Server, znajdują się na stronie cennika usługi Azure VM na platformie Azure dla maszyn wirtualnych i maszyn wirtualnych z [systemem](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Aby zapoznać się z omówieniem SQL Server uruchomionym w usłudze Azure Virtual Machines, zobacz następujące artykuły:

- [Omówienie SQL Server na maszynach wirtualnych z systemem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
- [Przegląd SQL Server on Linux maszyn wirtualnych](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
