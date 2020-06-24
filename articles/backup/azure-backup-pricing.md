---
title: Cennik usługi Azure Backup
description: Dowiedz się, jak oszacować koszty związane z budżetem Azure Backup.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: d88587cfdbb4f60d0da8641fc0362b8f763779ad
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84908165"
---
# <a name="azure-backup-pricing"></a>Cennik usługi Azure Backup

Aby dowiedzieć się więcej o Azure Backup cenach, odwiedź [stronę cennika Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Pobierz szczegółowe szacunki dotyczące cen za Azure Backup

Jeśli chcesz oszacować koszty związane z budżetem lub porównaniem kosztów, Pobierz szczegółowe [Azure Backup cenowe szacowania](https://aka.ms/AzureBackupCostEstimates).  

### <a name="what-does-the-estimator-contain"></a>Co zawiera szacowania?

Arkusz Azure Backup kosztów szacowania umożliwia oszacowanie wszystkich możliwych obciążeń, których kopia zapasowa ma być wykonana przy użyciu Azure Backup. Te obciążenia obejmują:

- Maszyny wirtualne platformy Azure
- Serwery lokalne
- SQL na maszynach wirtualnych platformy Azure
- SAP HANA na maszynach wirtualnych platformy Azure
- Udziały plików platformy Azure

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Oszacowanie kosztów tworzenia kopii zapasowych maszyn wirtualnych platformy Azure lub serwerów lokalnych

Aby oszacować koszty tworzenia kopii zapasowych maszyn wirtualnych platformy Azure lub serwerów lokalnych przy użyciu Azure Backup, potrzebne są następujące parametry:

- Rozmiar maszyn wirtualnych lub serwerów lokalnych, dla których próbujesz utworzyć kopię zapasową
  - Wprowadź "używany rozmiar" dysków lub serwerów wymaganych do utworzenia kopii zapasowej

- Liczba serwerów z tym rozmiarem

- Jaka jest oczekiwana ilość danych na tych serwerach?<br>
  Zmiany dotyczą ilości danych. Na przykład, jeśli masz maszynę wirtualną z 200 GB danych do utworzenia kopii zapasowej, a 10 GB zmiany jest codziennie, dzienne przebicie wynosi 5%.

  - Większe zmiany spowodują utworzenie kopii zapasowej większej ilości danych

  - Wybierz **niską** lub **średnią** dla serwerów plików i **wysoka** , jeśli używasz baz danych

  - Jeśli znasz wartość **procentową**zmian, możesz użyć opcji **wprowadź własny%**

- Wybieranie zasad kopii zapasowych

  - Jak długo należy zachować kopie zapasowe "dzienne"? (w dniach)

  - Jak długo chcesz zachować kopie zapasowe "cotygodniowe"? (w tygodniach)

  - Jak długo chcesz zachować kopie zapasowe "miesięcznie"? (w miesiącach)

  - Jak długo chcesz zachować kopie zapasowe "rocznie"? (w latach)

  - Jak długo chcesz zachować migawki "Natychmiastowe przywracanie"? (1-7 dni)

    - Ta opcja umożliwia przywrócenie z powrotem przez siedem dni w szybki sposób przy użyciu migawek przechowywanych na dyskach

- **Opcjonalne** — selektywne tworzenie kopii zapasowej dysku

  - Jeśli używasz opcji **tworzenia kopii zapasowej na dysku selektywnym** podczas tworzenia kopii zapasowych maszyn wirtualnych platformy Azure, wybierz opcję **Wyklucz dysk** i wprowadź wartość procentową dysków wykluczonych z kopii zapasowej w zakresie rozmiaru. Na przykład jeśli maszyna wirtualna jest połączona z trzema dyskami z 200 GB używanymi na każdym dysku i jeśli chcesz wykluczyć z nich dwa kopie zapasowe, wprowadź 66,7%.

- **Opcjonalne** — nadmiarowość magazynu kopii zapasowych

  - Wskazuje to nadmiarowość konta magazynu, do którego prowadzą dane kopii zapasowej. Zalecamy korzystanie z **GRS** w celu zapewnienia najwyższej dostępności. Ponieważ gwarantuje to, że kopia kopii zapasowej danych jest przechowywana w innym regionie, ułatwia spełnienie wielu standardów zgodności. Zmień nadmiarowość na **LRS** , jeśli tworzysz kopie zapasowe środowisk programistycznych lub testowych, które nie wymagają tworzenia kopii zapasowych na poziomie przedsiębiorstwa. Wybierz opcję **RAGRS** , jeśli chcesz włączyć **przywracanie między regionami** dla kopii zapasowych

- **Opcjonalne** — modyfikowanie cen regionalnych lub stosowanie stawek z rabatem

  - Jeśli chcesz sprawdzić oszacowania dla innego regionu lub stawek z rabatem, wybierz opcję **tak** dla opcji **Wypróbuj oszacowania dla innego regionu?** i wprowadź stawki, dla których chcesz uruchomić oszacowania.

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Oszacowanie kosztów tworzenia kopii zapasowych serwerów SQL na maszynach wirtualnych platformy Azure

Aby oszacować koszty tworzenia kopii zapasowych serwerów SQL działających na maszynach wirtualnych platformy Azure przy użyciu Azure Backup, potrzebne są następujące parametry:

- Rozmiar serwerów SQL, dla których próbujesz utworzyć kopię zapasową

- Liczba serwerów SQL o powyższym rozmiarze

- Jaka jest oczekiwana kompresja danych kopii zapasowej serwerów SQL?

  - Większość Azure Backup klienci widzą, że podczas **włączania**kompresji SQL dane kopii zapasowej mają kompresję o 80% w porównaniu do rozmiaru programu SQL Server.

  - Jeśli spodziewasz się, że ma zostać wyświetlona inna kompresja, wprowadź liczbę w tym polu

- Jaki jest oczekiwany rozmiar kopii zapasowych dziennika?

  - % Wskazuje dzienny rozmiar dziennika jako% rozmiaru programu SQL Server

- Jaka jest oczekiwana ilość dziennych zmian danych na tych serwerach?

  - Zwykle bazy danych mają "duże" zmiany

  - Jeśli znasz wartość **procentową**zmian, możesz użyć opcji **wprowadź własny%**

- Wybieranie zasad kopii zapasowych

  - Typ kopii zapasowej

    - Najbardziej skutecznymi zasadami, które można wybrać, jest **dzienne różnice** tygodniowe/comiesięczne/roczne pełne kopie zapasowe. Azure Backup można przywrócić z różnic za pomocą pojedynczego kliknięcia.

    - Możesz również wybrać opcję tworzenia zasad codziennie/co tydzień/miesięcznie/rocznie pełnych kopii zapasowych. Ta opcja spowoduje zużywanie nieco więcej miejsca niż w przypadku pierwszej opcji.

  - Jak długo należy zachować kopie zapasowe "log"? (w dniach) [1-35]

  - Jak długo należy zachować kopie zapasowe "dzienne"? (w dniach)

  - Jak długo chcesz zachować kopie zapasowe "cotygodniowe"? (w tygodniach)

  - Jak długo chcesz zachować kopie zapasowe "miesięcznie"? (w miesiącach)

  - Jak długo chcesz zachować kopie zapasowe "rocznie"? (w latach)

- **Opcjonalne** — nadmiarowość magazynu kopii zapasowych

  - Wskazuje to nadmiarowość konta magazynu, do którego prowadzą dane kopii zapasowej. Zalecamy korzystanie z **GRS** w celu zapewnienia najwyższej dostępności. Ponieważ gwarantuje to, że kopia kopii zapasowej danych jest przechowywana w innym regionie, ułatwia spełnienie wielu standardów zgodności. Zmień nadmiarowość na **LRS** , jeśli tworzysz kopie zapasowe środowisk programistycznych lub testowych, które nie wymagają tworzenia kopii zapasowych na poziomie przedsiębiorstwa.

- **Opcjonalne** — modyfikowanie cen regionalnych lub stosowanie stawek z rabatem

  - Jeśli chcesz sprawdzić oszacowania dla innego regionu lub stawek z rabatem, wybierz opcję **tak** dla opcji **Wypróbuj oszacowania dla innego regionu?** i wprowadź stawki, dla których chcesz uruchomić oszacowania.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Oszacowanie kosztów tworzenia kopii zapasowych serwerów SAP HANA na maszynach wirtualnych platformy Azure

Szacowanie kosztów tworzenia kopii zapasowych serwerów SAP HANA na maszynach wirtualnych platformy Azure jest podobne do oszacowania dla serwerów SQL. Możesz użyć tych samych zmiennych, jak wspomniano w poprzedniej sekcji, niezależnie od kompresji SQL.

## <a name="next-steps"></a>Następne kroki

[Co to jest usługa Azure Backup?](backup-overview.md)
