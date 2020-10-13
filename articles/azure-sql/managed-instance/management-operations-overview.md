---
title: Przegląd operacji zarządzania
titleSuffix: Azure SQL Managed Instance
description: Dowiedz się więcej o czasie trwania operacji zarządzania wystąpieniami zarządzanymi przez usługę Azure SQL i najlepszych rozwiązaniach.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: overview
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 2da7311e61aa39be69a6a0a29eff686baaad7ebf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323196"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Omówienie operacji zarządzania usługi Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Wystąpienie zarządzane usługi Azure SQL udostępnia operacje zarządzania, których można użyć do automatycznego wdrażania nowych wystąpień zarządzanych, aktualizowania właściwości wystąpienia i usuwania wystąpień, gdy nie są już potrzebne.

## <a name="what-are-management-operations"></a>Co to są operacje zarządzania?

Wszystkie operacje zarządzania można klasyfikować w następujący sposób:

- Wdrożenie wystąpienia (utworzenie nowego wystąpienia)
- Aktualizacja wystąpienia (zmiana właściwości wystąpienia, takich jak rdzeni wirtualnych lub magazyn zarezerwowany)
- Usunięcie wystąpienia

Aby obsługiwać [wdrożenia w ramach sieci wirtualnych platformy Azure](../../virtual-network/virtual-network-for-azure-services.md) i zapewnić izolację i bezpieczeństwo klientów, wystąpienie zarządzane SQL korzysta z [klastrów wirtualnych](connectivity-architecture-overview.md#high-level-connectivity-architecture). Klaster wirtualny reprezentuje dedykowany zestaw izolowanych maszyn wirtualnych wdrożonych w podsieci sieci wirtualnej klienta. Zasadniczo każde wystąpienie zarządzane wdrożone w pustej podsieci powoduje wystąpienie nowego klastra wirtualnego buildout.

Kolejne operacje zarządzania wystąpieniami zarządzanymi mogą mieć wpływ na źródłowy klaster wirtualny. Zmiany wpływające na odpowiedni klaster wirtualny mogą wpłynąć na czas trwania operacji zarządzania, ponieważ wdrożenie dodatkowych maszyn wirtualnych wiąże się z obciążeniem, które należy wziąć pod uwagę podczas planowania nowych wdrożeń lub aktualizacji istniejących wystąpień zarządzanych.


## <a name="duration"></a>Czas trwania

Czas trwania operacji w klastrze wirtualnym może się różnić, ale zazwyczaj jest to najdłuższy czas trwania. 

Poniżej znajdują się wartości, których można zwykle oczekiwać na podstawie istniejących danych telemetrycznych usługi:

- **Tworzenie klastra wirtualnego**: tworzenie jest krokiem synchronicznym w operacjach zarządzania wystąpieniami. <br/> **90% operacji zakończonych w ciągu 4 godzin**.
- **Zmienianie rozmiaru klastra wirtualnego (rozszerzanie lub zmniejszanie)**: rozszerzanie jest krokiem synchronicznym, natomiast zmniejszanie jest wykonywane asynchronicznie (bez wpływu na czas trwania operacji zarządzania wystąpieniami). <br/>**90% rozszerzeń klastra kończy się w czasie krótszym niż 2,5 godzin**.
- **Usuwanie klastra wirtualnego**: usuwanie to krok asynchroniczny, ale można go również [zainicjować ręcznie](virtual-cluster-delete.md) w pustym klastrze wirtualnym, w tym przypadku jest wykonywane synchronicznie. <br/>**90% usunięć klastrów wirtualnych kończy się w 1,5 godz**.

Ponadto Zarządzanie wystąpieniami może również obejmować jedną z operacji w hostowanych bazach danych, co powoduje dłuższe czasy trwania:

- **Dołączanie plików bazy danych z usługi Azure Storage**: synchroniczny krok, taki jak skalowanie obliczeniowe (rdzeni wirtualnych) lub magazyn w górę lub w dół w warstwie usług ogólnego przeznaczenia. <br/>**90% tych operacji kończy się za 5 minut**.
- **Wypełnianie grupy dostępności**— umieszczanie: synchroniczny krok, taki jak COMPUTE (rdzeni wirtualnych) lub skalowanie magazynu w warstwie usług krytyczne dla działania firmy oraz zmiana warstwy usługi z Ogólnego przeznaczenia na krytyczne dla działania firmy (lub odwrotnie). Czas trwania tej operacji jest proporcjonalny do całkowitego rozmiaru bazy danych, a także bieżącego działania bazy danych (liczba aktywnych transakcji). Działanie bazy danych podczas aktualizowania wystąpienia może wprowadzić znaczącą wariancję do całkowitego czasu trwania. <br/>**90% tych operacji wykonywanych o godz. 220 GB/godz. lub wyższych**.

Poniższe tabele podsumowują operacje i typowe ogólne czasy trwania w oparciu o kategorię operacji:

**Kategoria: wdrażanie**

|Operacja  |Segment długotrwały  |Szacowany czas trwania  |
|---------|---------|---------|
|Pierwsze wystąpienie w pustej podsieci|Tworzenie klastra wirtualnego|90% operacji zakończonych w ciągu 4 godzin.|
|Pierwsze wystąpienie innej generacji sprzętu w niepustej podsieci (na przykład pierwsze wystąpienie generacji 5 w podsieci z wystąpieniami generacji 4)|Tworzenie klastra wirtualnego<sup>1</sup>|90% operacji zakończonych w ciągu 4 godzin.|
|Kolejne Tworzenie wystąpienia w niepustej podsieci (drugi, trzeci itp. wystąpienie)|Zmienianie rozmiarów klastra wirtualnego|90% operacji zakończonych w ciągu 2,5 godzin.|
| | | 

<sup>1</sup> klaster wirtualny jest zbudowany na wygenerowanie sprzętu.

**Kategoria: Aktualizacja**

|Operacja  |Segment długotrwały  |Szacowany czas trwania  |
|---------|---------|---------|
|Zmiana właściwości wystąpienia (hasło administratora, logowanie do usługi Azure AD, flaga Korzyść użycia hybrydowego platformy Azure)|Nie dotyczy|Do 1 minuty.|
|Skalowanie magazynu wystąpień w górę/w dół (Ogólnego przeznaczenia warstwy usług)|Dołączanie plików bazy danych|90% operacji zakończonych w ciągu 5 minut.|
|Skalowanie magazynu wystąpień w górę/w dół (Krytyczne dla działania firmy warstwy usług)|— Zmienianie rozmiarów klastra wirtualnego<br>-Zawsze włączone Określanie rozrzutu grupy dostępności|90% operacji zakończonych w ciągu 2,5 godzin + Time do wypełniania wszystkich baz danych (220 GB/godz.).|
|Skalowanie wystąpienia obliczeniowego (rdzeni wirtualnych) w górę i w dół (Ogólnego przeznaczenia)|— Zmienianie rozmiarów klastra wirtualnego<br>— Dołączanie plików bazy danych|90% operacji zakończonych w ciągu 2,5 godzin.|
|Skalowanie wystąpienia obliczeniowego (rdzeni wirtualnych) w górę i w dół (Krytyczne dla działania firmy)|— Zmienianie rozmiarów klastra wirtualnego<br>-Zawsze włączone Określanie rozrzutu grupy dostępności|90% operacji zakończonych w ciągu 2,5 godzin + Time do wypełniania wszystkich baz danych (220 GB/godz.).|
|Zmiana warstwy usługi wystąpienia (Ogólnego przeznaczenia do Krytyczne dla działania firmy i na odwrót)|— Zmienianie rozmiarów klastra wirtualnego<br>-Zawsze włączone Określanie rozrzutu grupy dostępności|90% operacji zakończonych w ciągu 2,5 godzin + Time do wypełniania wszystkich baz danych (220 GB/godz.).|
| | | 

**Kategoria: usuwanie**

|Operacja  |Segment długotrwały  |Szacowany czas trwania  |
|---------|---------|---------|
|Usunięcie wystąpienia|Kopia zapasowa dziennika dla wszystkich baz danych|90% operacji zakończonych w ciągu do 1 minuty.<br>Uwaga: Jeśli ostatnie wystąpienie w podsieci zostanie usunięte, ta operacja spowoduje zaplanowanie usunięcia klastra wirtualnego po upływie 12 godzin. <sup>1</sup>|
|Usuwanie klastra wirtualnego (jako operacja zainicjowana przez użytkownika)|Usuwanie klastra wirtualnego|90% operacji zakończonych w maksymalnie 1,5 godzinach.|
| | | 

Bieżąca konfiguracja to <sup>1</sup>12 godzin, ale może ulec zmianie w przyszłości. Jeśli musisz usunąć klaster wirtualny wcześniej (aby zwolnić podsieć, na przykład), zobacz [usuwanie podsieci po usunięciu wystąpienia zarządzanego](virtual-cluster-delete.md).

## <a name="instance-availability"></a>Dostępność wystąpienia

Wystąpienie zarządzane SQL **jest dostępne podczas operacji aktualizacji**, z wyjątkiem krótkich przestojów spowodowanych przez pracę w trybie failover, które są wykonywane po zakończeniu aktualizacji. Zwykle trwa to 10 sekund nawet w przypadku przerwanych długotrwałych transakcji, dzięki czemu można [przyspieszyć odzyskiwanie bazy danych](../accelerated-database-recovery.md).

Wystąpienie zarządzane SQL nie jest dostępne dla aplikacji klienckich podczas operacji wdrażania i usuwania.

> [!IMPORTANT]
> Nie jest zalecane skalowanie zasobów obliczeniowych lub magazynu wystąpienia zarządzanego usługi Azure SQL ani zmiana warstwy usług w tym samym czasie co długotrwałe transakcje (Importowanie danych, zadania przetwarzania danych, ponowne kompilowanie indeksu itp.). Przełączenie w tryb failover bazy danych na końcu operacji spowoduje anulowanie wszystkich bieżących transakcji. 

## <a name="management-operations-steps"></a>Kroki operacji zarządzania

Operacje związane z zarządzaniem składają się z wielu kroków. W [interfejsie API operacji wprowadzono](management-operations-monitor.md) następujące kroki są dostępne dla podzbioru operacji (wdrożenie i aktualizacja). Operacja wdrażania składa się z trzech kroków, podczas gdy operacja aktualizacji jest wykonywana w sześciu krokach. Aby uzyskać szczegółowe informacje na temat czasu trwania operacji, zobacz sekcję [czas trwania operacji zarządzania](#duration) . Kroki są wyświetlane według kolejności wykonywania.

### <a name="managed-instance-deployment-steps"></a>Kroki wdrażania wystąpienia zarządzanego

|Nazwa kroku  |Opis kroku  |
|----|---------|
|Żądaj weryfikacji |Przesłane parametry zostały zweryfikowane. W przypadku nieprawidłowej konfiguracji operacja nie powiedzie się z powodu błędu. |
|Zmienianie rozmiarów/tworzenia klastra wirtualnego |W zależności od stanu podsieci klaster wirtualny przechodzi do tworzenia lub zmiany rozmiarów. |
|Nowe uruchomienie wystąpienia programu SQL |Proces SQL został uruchomiony w wdrożonym klastrze wirtualnym. |

### <a name="managed-instance-update-steps"></a>Kroki aktualizacji wystąpienia zarządzanego

|Nazwa kroku  |Opis kroku  |
|----|---------|
|Żądaj weryfikacji | Przesłane parametry zostały zweryfikowane. W przypadku nieprawidłowej konfiguracji operacja nie powiedzie się z powodu błędu. |
|Zmienianie rozmiarów/tworzenia klastra wirtualnego |W zależności od stanu podsieci klaster wirtualny przechodzi do tworzenia lub zmiany rozmiarów. |
|Nowe uruchomienie wystąpienia programu SQL | Proces SQL został uruchomiony w wdrożonym klastrze wirtualnym. |
|Umieszczanie plików bazy danych/Dołączanie plików bazy danych |W zależności od typu operacji aktualizacji jest wykonywane napełnianie lub Dołączanie plików bazy danych. |
|Przygotowywanie trybu failover i trybu failover |Po odłączeniu danych lub przypisaniu plików bazy danych system jest przygotowywany do pracy w trybie failover. Gdy wszystko jest ustawione, tryb failover jest wykonywany **z krótkim przestojem**. |
|Czyszczenie starego wystąpienia SQL |Usuwanie starego procesu SQL z klastra wirtualnego |

> [!NOTE]
> W wyniku skalowania wystąpień źródłowy klaster wirtualny przejdzie przez proces zwalniania nieużywanej pojemności i możliwej fragmentacji pojemności, co może mieć wpływ na wystąpienia, które nie uczestniczyły w operacjach tworzenia/skalowania. 


## <a name="management-operations-cross-impact"></a>Wpływ operacji zarządzania

Operacje zarządzania w wystąpieniu zarządzanym mogą mieć wpływ na inne operacje zarządzania wystąpieniami znajdującymi się w tym samym klastrze wirtualnym:

- **Długotrwałe operacje przywracania** w klastrze wirtualnym będą zawierać inne operacje tworzenia wystąpień lub skalowania w tej samej podsieci.<br/>**Przykład:** Jeśli istnieje długotrwała operacja przywracania i istnieje żądanie utworzenia lub skalowania w tej samej podsieci, żądanie będzie trwać dłużej, ponieważ czeka na ukończenie operacji przywracania przed kontynuowaniem.

- **Kolejne operacje tworzenia wystąpienia lub skalowania** są przechowywane przez wcześniej zainicjowane wystąpienie lub skalowanie wystąpienia, które zainicjowało zmianę rozmiaru klastra wirtualnego.<br/>**Przykład:** Jeśli istnieje wiele żądań utworzenia i/lub skalowania w tej samej podsieci w ramach tego samego klastra wirtualnego, a jedna z nich inicjuje zmianę rozmiaru klastra wirtualnego, wszystkie żądania, które zostały przesłane 5 + minuty po początkowym żądaniu operacji, będą trwać dłużej niż oczekiwano, ponieważ te żądania będą musiały poczekać na zakończenie zmiany rozmiaru przed wznowieniem.

- **Operacje tworzenia/skalowania przesłane w 5-minutowych oknach zostaną przetworzone** w partii i wykonane równolegle.<br/>**Przykład:** Dla wszystkich operacji przesyłanych w oknie 5-minutowe zostanie wykonane tylko jedno Zmienianie rozmiaru klastra wirtualnego (mierzenie od momentu wykonania pierwszego żądania operacji). Jeśli kolejne żądanie zostanie przesłane więcej niż 5 minut po przesłaniu pierwszego z nich, czeka na ukończenie zmiany rozmiaru klastra wirtualnego przed rozpoczęciem wykonywania.

> [!IMPORTANT]
> Operacje zarządzania, które zostały wstrzymane, ponieważ inna operacja, która jest w toku, zostanie automatycznie wznowiona po spełnieniu warunków do kontynuowania. Nie jest wymagane wykonanie jakiejkolwiek czynności przez użytkownika w celu wznowienia czasowo wstrzymanych operacji zarządzania.

## <a name="monitoring-management-operations"></a>Monitorowanie operacji zarządzania

Aby dowiedzieć się, jak monitorować postęp operacji zarządzania i stan, zobacz [monitorowanie operacji zarządzania](management-operations-monitor.md).

## <a name="canceling-management-operations"></a>Anulowanie operacji zarządzania

Aby dowiedzieć się, jak anulować operację zarządzania, zobacz [anulowanie operacji zarządzania](management-operations-cancel.md).


## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane, zobacz [Przewodnik Szybki Start](instance-create-quickstart.md).
- Aby zapoznać się z funkcjami i listą porównawczą, zobacz [Common SQL Features](../database/features-comparison.md).
- Aby uzyskać więcej informacji na temat konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego SQL](connectivity-architecture-overview.md).
- W przypadku szybkiego startu, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).
- Aby zapoznać się z samouczkiem dotyczącym korzystania z Azure Database Migration Service migracji, zobacz [migracja wystąpienia zarządzanego SQL przy użyciu Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
