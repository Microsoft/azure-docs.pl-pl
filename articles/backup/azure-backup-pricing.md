---
title: Cennik usługi Azure Backup
description: Dowiedz się, jak oszacować koszty, aby zaplanować budżet na usługę Azure Backup.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 03ec0076d3089562ddaace6db413fb3f1ba949a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88654535"
---
# <a name="azure-backup-pricing"></a>Cennik usługi Azure Backup

Aby uzyskać informacje na temat cen usługi Azure Backup, odwiedź [stronę cennika usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Pobieranie szczegółowych szacunków dotyczących cen usługi Azure Backup

Jeśli chcesz oszacować koszty na potrzeby planowania budżetu lub porównania cen, pobierz [narzędzie do szacowania cen usługi Azure Backup](https://aka.ms/AzureBackupCostEstimates).  

### <a name="what-does-the-estimator-contain"></a>Co zawiera narzędzie do szacowania?

Arkusz narzędzia do szacowania kosztów usługi Azure Backup umożliwia oszacowanie kosztów wszystkich możliwych obciążeń, dla których planujesz utworzyć kopię zapasową w usłudze Azure Backup. Te obciążenia obejmują:

- Maszyny wirtualne platformy Azure
- Serwery lokalne
- Serwery SQL na maszynach wirtualnych platformy Azure
- Serwery SAP HANA na maszynach wirtualnych platformy Azure
- Udziały plików platformy Azure

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Szacowanie kosztów tworzenia kopii zapasowych maszyn wirtualnych platformy Azure lub serwerów lokalnych

Aby oszacować koszty tworzenia kopii zapasowych maszyn wirtualnych platformy Azure lub serwerów lokalnych w usłudze Azure Backup, potrzebne są następujące parametry:

- Rozmiar maszyn wirtualnych lub serwerów lokalnych, dla których planujesz utworzyć kopię zapasową
  - Wprowadź „używany rozmiar” dysków lub serwerów, dla których będzie tworzona kopia zapasowa

- Liczba serwerów mających taki rozmiar

- Jaki jest przewidywany współczynnik zmian danych na tych serwerach?<br>
  Współczynnik ten oznacza ilość danych ulegających zmianie. Jeśli na przykład chcesz utworzyć kopię zapasową maszyny wirtualnej zawierającej 200 GB danych, a codziennie zmienia się 10 GB tych danych, dzienny współczynnik zmian to 5%.

  - Wyższy współczynnik zmian oznacza, że trzeba utworzyć kopię zapasową większej ilości danych

  - Wybierz opcję **Low** (Niski) lub **Moderate** (Średni) w przypadku serwerów plików, a **High** (Wysoki), jeśli obsługujesz bazy danych

  - Jeśli znasz dokładny **współczynnik zmian w %** , możesz użyć opcji **Enter your own%** (Wprowadź własny %)

- Wybierz zasady kopii zapasowych

  - Jak długo mają być przechowywane „dzienne” kopie zapasowe? (w dniach)

  - Jak długo mają być przechowywane „tygodniowe” kopie zapasowe? (w tygodniach)

  - Jak długo mają być przechowywane „miesięczne” kopie zapasowe? (w miesiącach)

  - Jak długo mają być przechowywane „roczne” kopie zapasowe? (w latach)

  - Jak długo mają być przechowywane „migawki natychmiastowego przywracania”? (1–5 dni)

    - Ta opcja umożliwia szybkie przywracanie danych nawet z ostatnich siedmiu dni przy użyciu migawek przechowywanych na dyskach.

- **Opcjonalnie** — Selektywne tworzenie kopii zapasowej dysku

  - Jeśli podczas tworzenia kopii zapasowych maszyn wirtualnych platformy Azure używasz opcji **Selektywne tworzenie kopii zapasowej dysku**, wybierz opcję **Exclude disk** (Wyklucz dysk) i określ w procentach rozmiar dysków wykluczony z tworzenia kopii zapasowych. Jeśli na przykład masz maszynę wirtualną połączoną z trzema dyskami, na których używane jest po 200 GB miejsca, a chcesz wykluczyć z tworzenia kopii zapasowych dwa takie dyski, wprowadź 66,7%.

- **Opcjonalnie** — Nadmiarowość magazynu kopii zapasowych

  - Ten parametr określa nadmiarowość konta magazynu, na którym zapisywane są dane kopii zapasowych. Zalecamy użycie opcji **GRS** (Magazyn geograficznie nadmiarowy) w celu zapewnienia maksymalnej dostępności. Ponieważ ta opcja gwarantuje przechowywanie kopii danych kopii zapasowej w innym regionie, ułatwia spełnienie wielu standardów zachowania zgodności. Zmień ustawienie nadmiarowości na **LRS** (Magazyn lokalnie nadmiarowy), jeśli tworzysz kopie zapasowe środowisk deweloperskich i testowych, które nie wymagają kopii zapasowej klasy korporacyjnej. Wybierz w arkuszu opcję **RARGS**, jeśli chcesz oszacować koszty w przypadku włączenia opcji [przywracania między regionami](backup-azure-arm-restore-vms.md#cross-region-restore) dla kopii zapasowych.

- **Opcjonalne** — Modyfikowanie cen w regionach lub stosowanie stawek z rabatem

  - Jeśli chcesz sprawdzić szacowane koszty z uwzględnieniem innego regionu lub stawek z rabatem, wybierz opcję **Yes** w obszarze **Try estimates for a different region?** (Sprawdzić szacowanie dla innego regionu?) i wprowadź stawki, na podstawie których chcesz oszacować koszty.

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Szacowanie kosztów tworzenia kopii zapasowych serwerów SQL na maszynach wirtualnych platformy Azure

Aby oszacować koszty tworzenia kopii zapasowych serwerów SQL działających na maszynach wirtualnych platformy Azure w usłudze Azure Backup, potrzebne są następujące parametry:

- Rozmiar serwerów SQL, dla których planujesz utworzyć kopię zapasową

- Liczba serwerów SQL o tym rozmiarze

- Jaka jest oczekiwana kompresja danych kopii zapasowej serwerów SQL?

  - W przypadku większości klientów usługi Azure Backup, jeśli kompresja SQL jest **włączona**, dane kopii zapasowej mają 80-procentową kompresję w porównaniu do rozmiaru serwera SQL.

  - Jeśli spodziewasz się innego współczynnika kompresji, wprowadź liczbę w tym polu

- Jaki jest oczekiwany rozmiar kopii zapasowych dzienników?

  - Ta wartość procentowa wskazuje dzienny rozmiar dzienników jako procent rozmiaru serwera SQL

- Jaki jest przewidywany dzienny współczynnik zmian danych na tych serwerach?

  - W przypadku baz danych na ogół wskazana jest opcja „High” (Wysoki)

  - Jeśli znasz dokładny **współczynnik zmian w %** , możesz użyć opcji **Enter your own%** (Wprowadź własny %)

- Wybierz zasady kopii zapasowych

  - Typ kopii zapasowej

    - Najbardziej wydajne zasady, które można wybrać, to **Daily differentials** (Codzienna kopia różnicowa) z pełną kopią zapasową w wariancie weekly/monthly/yearly (co tydzień/miesiąc/rok). Usługa Azure Backup umożliwia również przywracanie z różnicowych kopii zapasowych jednym kliknięciem.

    - Można też wybrać zasady tworzenia pełnej kopii zapasowej daily/weekly/monthly/yearly (codziennie/co tydzień/co miesiąc/co rok). Ta opcja powoduje użycie nieco większej ilości miejsca w magazynie niż ta pierwsza.

  - Jak długo mają być przechowywane kopie zapasowe dzienników? (w dniach) [7–35]

  - Jak długo mają być przechowywane „dzienne” kopie zapasowe? (w dniach)

  - Jak długo mają być przechowywane „tygodniowe” kopie zapasowe? (w tygodniach)

  - Jak długo mają być przechowywane „miesięczne” kopie zapasowe? (w miesiącach)

  - Jak długo mają być przechowywane „roczne” kopie zapasowe? (w latach)

- **Opcjonalnie** — Nadmiarowość magazynu kopii zapasowych

  - Ten parametr określa nadmiarowość konta magazynu, na którym zapisywane są dane kopii zapasowych. Zalecamy użycie opcji **GRS** (Magazyn geograficznie nadmiarowy) w celu zapewnienia maksymalnej dostępności. Ponieważ ta opcja gwarantuje przechowywanie kopii danych kopii zapasowej w innym regionie, ułatwia spełnienie wielu standardów zachowania zgodności. Zmień ustawienie nadmiarowości na **LRS** (Magazyn lokalnie nadmiarowy), jeśli tworzysz kopie zapasowe środowisk deweloperskich i testowych, które nie wymagają kopii zapasowej klasy korporacyjnej.

- **Opcjonalne** — Modyfikowanie cen w regionach lub stosowanie stawek z rabatem

  - Jeśli chcesz sprawdzić szacowane koszty z uwzględnieniem innego regionu lub stawek z rabatem, wybierz opcję **Yes** w obszarze **Try estimates for a different region?** (Sprawdzić szacowanie dla innego regionu?) i wprowadź stawki, na podstawie których chcesz oszacować koszty.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Szacowanie kosztów tworzenia kopii zapasowych serwerów SAP HANA na maszynach wirtualnych platformy Azure

Aby oszacować koszty tworzenia kopii zapasowych serwerów SAP HANA działających na maszynach wirtualnych platformy Azure w usłudze Azure Backup, potrzebne są następujące parametry:

- Łączny rozmiar baz danych SAP HANA, dla których planujesz utworzyć kopię zapasową. Powinna to być suma rozmiaru pełnych kopii zapasowych wszystkich baz danych, zgłaszana przez platformę SAP HANA.
- Liczba serwerów SAP HANA o tym rozmiarze
- Jaki jest oczekiwany rozmiar kopii zapasowych dzienników?
  
  - Ta wartość procentowa wskazuje średni dzienny rozmiar dzienników jako procent całkowitego rozmiaru baz danych SAP HANA z tego serwera SAP HANA, których kopie zapasowe chcesz tworzyć
- Jaki jest przewidywany dzienny współczynnik zmian danych na tych serwerach?
  - Ta wartość procentowa wskazuje średnią wielkość zmian danych jako procent całkowitego rozmiaru baz danych SAP HANA z tego serwera SAP HANA, których kopie zapasowe chcesz tworzyć
  - W przypadku baz danych na ogół wskazana jest opcja „High” (Wysoki)
  - Jeśli znasz dokładny **współczynnik zmian w %** , możesz użyć opcji **Enter your own%** (Wprowadź własny %)
- Wybierz zasady kopii zapasowych
  - Typ kopii zapasowej
    - Najbardziej wydajne zasady, które można wybrać, to **Daily differentials** (Codzienna kopia różnicowa) z pełną kopią zapasową w wariancie **weekly/monthly/yearly** (co tydzień/miesiąc/rok). Usługa Azure Backup umożliwia również przywracanie z różnicowych kopii zapasowych jednym kliknięciem.
    - Można też wybrać zasady tworzenia pełnej kopii zapasowej **daily/weekly/monthly/yearly** (codziennie/co tydzień/co miesiąc/co rok). Ta opcja powoduje użycie nieco większej ilości miejsca w magazynie niż ta pierwsza.
  - Jak długo mają być przechowywane kopie zapasowe dzienników? (w dniach) [7–35]
  - Jak długo mają być przechowywane „dzienne” kopie zapasowe? (w dniach)
  - Jak długo mają być przechowywane „tygodniowe” kopie zapasowe? (w tygodniach)
  - Jak długo mają być przechowywane „miesięczne” kopie zapasowe? (w miesiącach)
  - Jak długo mają być przechowywane „roczne” kopie zapasowe? (w latach)
- **Opcjonalnie** — Nadmiarowość magazynu kopii zapasowych
  
  - Ten parametr określa nadmiarowość konta magazynu, na którym zapisywane są dane kopii zapasowych. Zalecamy użycie opcji **GRS** (Magazyn geograficznie nadmiarowy) w celu zapewnienia maksymalnej dostępności. Ponieważ ta opcja gwarantuje przechowywanie kopii danych kopii zapasowej w innym regionie, ułatwia spełnienie wielu standardów zachowania zgodności. Zmień ustawienie nadmiarowości na **LRS** (Magazyn lokalnie nadmiarowy), jeśli tworzysz kopie zapasowe środowisk deweloperskich i testowych, które nie wymagają kopii zapasowej klasy korporacyjnej.
- **Opcjonalne** — Modyfikowanie cen w regionach lub stosowanie stawek z rabatem
  
  - Jeśli chcesz sprawdzić szacowane koszty z uwzględnieniem innego regionu lub stawek z rabatem, wybierz opcję **Yes** w obszarze **Try estimates for a different region?** (Sprawdzić szacowanie dla innego regionu?) i wprowadź stawki, na podstawie których chcesz oszacować koszty.
  
## <a name="estimate-costs-for-backing-up-azure-file-shares"></a>Szacowanie kosztów tworzenia kopii zapasowych udziałów plików platformy Azure

Aby oszacować koszty tworzenia kopii zapasowych udziałów plików platformy Azure przy użyciu oferowanego w usłudze Azure Backup [rozwiązania kopii zapasowej opartej na migawkach](azure-file-share-backup-overview.md), potrzebne są następujące parametry:

- Rozmiar (**w GB**) udziałów plików, dla których planujesz utworzyć kopię zapasową.

- Jeśli chcesz tworzyć kopię zapasową udziałów plików rozmieszczonych na wielu różnych kontach magazynu, określ liczbę kont magazynu, na których są hostowane udziały plików o tym rozmiarze.

- Oczekiwany współczynnik zmian danych w udziałach plików, dla których planujesz utworzyć kopię zapasową. <br>Współczynnik ten oznacza ilość danych ulegających zmianie i wpływa bezpośrednio na rozmiar migawki w magazynie. Jeśli na przykład chcesz utworzyć kopię zapasową udziału plików zawierającego 200 GB danych, a codziennie zmienia się 10 GB tych danych, dzienny współczynnik zmian to 5%.
  - Wyższy współczynnik zmian danych oznacza, że codziennie zmienia się duża część danych zawartych w udziale plików, a więc rozmiar migawki przyrostowej (zawierającej tylko zmiany danych) również będzie większy.
  - Wybierz opcję Low (niski — 1%), Moderate (średni — 3%) lub High (wysoki — 5%) w zależności od cech i sposobu użycia udziału plików.
  - Jeśli znasz dokładny **współczynnik zmian w %** dla udziału plików, możesz wybrać opcję **Enter your own%** (Wprowadź własny %) z listy rozwijanej. Określ (w %) wartości dziennego, tygodniowego, miesięcznego i rocznego współczynnika zmian danych.

- Typ konta magazynu (standardowe lub premium) oraz ustawienie nadmiarowości konta magazynu hostującego udział plików, dla których planujesz utworzyć kopię zapasową. <br>W bieżącym rozwiązaniu kopii zapasowej udziałów plików platformy Azure migawki są przechowywane na tym samym koncie magazynu, co udział plików, którego kopię zapasową tworzysz. W związku z tym koszt magazynu związany z przechowywaniem migawek jest rozliczany w ramach rachunku za usługę Azure Files, odpowiednio do cen migawek w przypadku danego typu konta i ustawienia nadmiarowości konta magazynu hostującego udział plików oraz jego migawki.

- Okres przechowywania dla różnych kopii zapasowych
  - Jak długo mają być przechowywane „dzienne” kopie zapasowe? (w dniach)
  - Jak długo mają być przechowywane „tygodniowe” kopie zapasowe? (w tygodniach)
  - Jak długo mają być przechowywane „miesięczne” kopie zapasowe? (w miesiącach)
  - Jak długo mają być przechowywane „roczne” kopie zapasowe? (w latach)

  Zapoznaj się z [matrycą obsługi udziałów plików platformy Azure](azure-file-share-support-matrix.md#retention-limits), aby określić maksymalne obsługiwane okresy przechowywania w każdej z tych kategorii.

- **Opcjonalne** — Modyfikowanie cen w regionach lub stosowanie stawek z rabatem.
  - Domyślne wartości kosztu magazynu migawek za GB i kosztu wystąpienia chronionego w narzędziu do szacowania dotyczą regionu Wschodnie stany USA. Jeśli chcesz sprawdzić szacowane koszty z uwzględnieniem innego regionu lub stawek z rabatem, wybierz opcję **Yes** w obszarze **Try estimates for a different region?** (Sprawdzić szacowanie dla innego regionu?) i wprowadź stawki, na podstawie których chcesz oszacować koszty.

## <a name="next-steps"></a>Następne kroki

[Co to jest usługa Azure Backup?](backup-overview.md)
