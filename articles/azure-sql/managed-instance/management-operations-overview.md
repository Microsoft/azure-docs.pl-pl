---
title: Operacje zarządzania
titleSuffix: Azure SQL Managed Instance
description: Dowiedz się więcej o czasie trwania operacji zarządzania wystąpieniami zarządzanymi przez usługę Azure SQL i najlepszych rozwiązaniach.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, carlrab, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 5cff54b1f71d30f7932c4ead722d1dda0a7aec57
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531486"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Omówienie operacji zarządzania wystąpieniami zarządzanymi przez usługę Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

## <a name="what-are-management-operations"></a>Co to są operacje zarządzania?
Wystąpienie zarządzane usługi Azure SQL udostępnia operacje zarządzania, których można użyć do automatycznego wdrażania nowych wystąpień zarządzanych, aktualizowania właściwości wystąpienia i usuwania wystąpień, gdy nie są już potrzebne.

Aby obsługiwać [wdrożenia w ramach sieci wirtualnych platformy Azure](../../virtual-network/virtual-network-for-azure-services.md) i zapewnić izolację i bezpieczeństwo klientów, wystąpienie zarządzane SQL korzysta z [klastrów wirtualnych](connectivity-architecture-overview.md#high-level-connectivity-architecture). Klaster wirtualny reprezentuje dedykowany zestaw izolowanych maszyn wirtualnych wdrożonych w podsieci sieci wirtualnej klienta. Zasadniczo każde wdrożenie wystąpienia zarządzanego w pustej podsieci powoduje wystąpienie nowego klastra wirtualnego buildout.

Kolejne operacje na wdrożonych wystąpieniach zarządzanych mogą również mieć wpływ na źródłowy klaster wirtualny. Operacje te wpływają na czas trwania operacji zarządzania, ponieważ wdrożenie dodatkowych maszyn wirtualnych wiąże się z obciążeniem, które należy wziąć pod uwagę podczas planowania nowych wdrożeń lub aktualizacji istniejących wystąpień zarządzanych.

Wszystkie operacje zarządzania można klasyfikować w następujący sposób:

- Wdrożenie wystąpienia (nowe wystąpienie).
- Aktualizacja wystąpienia (zmiana właściwości wystąpienia, takich jak rdzeni wirtualnych lub magazyn zarezerwowany).
- Usuwanie wystąpienia.

## <a name="management-operations-duration"></a>Czas trwania operacji zarządzania
Zazwyczaj operacje w klastrach wirtualnych trwają najdłużej. Czas trwania operacji na klastrach wirtualnych zależy od tego, jakie wartości są zwykle oczekiwane na podstawie istniejących danych telemetrycznych usługi:

- **Tworzenie klastra wirtualnego**: tworzenie jest krokiem synchronicznym w operacjach zarządzania wystąpieniami. **90% operacji zakończonych w ciągu 4 godzin**.
- **Zmienianie rozmiaru klastra wirtualnego (rozszerzanie lub zmniejszanie)**: rozszerzanie jest krokiem synchronicznym, natomiast zmniejszanie jest wykonywane asynchronicznie (bez wpływu na czas trwania operacji zarządzania wystąpieniami). **90% rozszerzeń klastra kończy się w czasie krótszym niż 2,5 godzin**.
- **Usuwanie klastra wirtualnego**: usuwanie to krok asynchroniczny, ale można go również [zainicjować ręcznie](virtual-cluster-delete.md) w pustym klastrze wirtualnym, w tym przypadku jest wykonywane synchronicznie. **90% usunięć klastrów wirtualnych kończy się w 1,5 godz**.

Ponadto Zarządzanie wystąpieniami może również obejmować jedną z operacji w hostowanych bazach danych, co powoduje dłuższe czasy trwania:

- **Dołączanie plików bazy danych z usługi Azure Storage**: synchroniczny krok, taki jak COMPUTE (rdzeni wirtualnych) lub skalowanie w górę lub w dół w warstwie usług ogólnego przeznaczenia. **90% tych operacji kończy się za 5 minut**.
- **Wypełnianie grupy dostępności**— umieszczanie: synchroniczny krok, taki jak COMPUTE (rdzeni wirtualnych) lub skalowanie magazynu w warstwie usług krytyczne dla działania firmy oraz zmiana warstwy usługi z Ogólnego przeznaczenia na krytyczne dla działania firmy (lub odwrotnie). Czas trwania tej operacji jest proporcjonalny do całkowitego rozmiaru bazy danych, a także bieżącego działania bazy danych (liczba aktywnych transakcji). Działanie bazy danych podczas aktualizowania wystąpienia może wprowadzić znaczącą wariancję do całkowitego czasu trwania. **90% tych operacji wykonywanych o godz. 220 GB/godz. lub wyższych**.

W poniższej tabeli zestawiono operacje i typowe ogólne czasy trwania:

|Kategoria  |Operacja  |Segment długotrwały  |Szacowany czas trwania  |
|---------|---------|---------|---------|
|**Wdrożenie** |Pierwsze wystąpienie w pustej podsieci|Tworzenie klastra wirtualnego|90% operacji zakończonych w ciągu 4 godzin.|
|Wdrożenie |Pierwsze wystąpienie innej generacji sprzętu w niepustej podsieci (na przykład pierwsze wystąpienie generacji 5 w podsieci z wystąpieniami generacji 4)|Tworzenie klastra wirtualnego *|90% operacji zakończonych w ciągu 4 godzin.|
|Wdrożenie |Pierwsze utworzenie wystąpienia 4 rdzeni wirtualnych w pustej lub niepustej podsieci|Tworzenie klastra wirtualnego * *|90% operacji zakończonych w ciągu 4 godzin.|
|Wdrożenie |Kolejne Tworzenie wystąpienia w niepustej podsieci (drugi, trzeci itp. wystąpienie)|Zmienianie rozmiarów klastra wirtualnego|90% operacji zakończonych w ciągu 2,5 godzin.|
|**Aktualizowanie** |Zmiana właściwości wystąpienia (hasło administratora, logowanie do usługi Azure AD, flaga Korzyść użycia hybrydowego platformy Azure)|Nie dotyczy|Do 1 minuty.|
|Aktualizowanie |Skalowanie magazynu wystąpień w górę/w dół (Ogólnego przeznaczenia warstwy usług)|Dołączanie plików bazy danych|90% operacji zakończonych w ciągu 5 minut.|
|Aktualizowanie |Skalowanie magazynu wystąpień w górę/w dół (Krytyczne dla działania firmy warstwy usług)|— Zmienianie rozmiarów klastra wirtualnego<br>-Zawsze włączone Określanie rozrzutu grupy dostępności|90% operacji zakończonych w ciągu 2,5 godzin + Time do wypełniania wszystkich baz danych (220 GB/godz.).|
|Aktualizowanie |Skalowanie wystąpienia obliczeniowego (rdzeni wirtualnych) w górę i w dół (Ogólnego przeznaczenia)|— Zmienianie rozmiarów klastra wirtualnego<br>— Dołączanie plików bazy danych|90% operacji zakończonych w ciągu 2,5 godzin.|
|Aktualizowanie |Skalowanie wystąpienia obliczeniowego (rdzeni wirtualnych) w górę i w dół (Krytyczne dla działania firmy)|— Zmienianie rozmiarów klastra wirtualnego<br>-Zawsze włączone Określanie rozrzutu grupy dostępności|90% operacji zakończonych w ciągu 2,5 godzin + Time do wypełniania wszystkich baz danych (220 GB/godz.).|
|Aktualizowanie |Zmiana warstwy usługi wystąpienia (Ogólnego przeznaczenia do Krytyczne dla działania firmy i na odwrót)|— Zmienianie rozmiarów klastra wirtualnego<br>-Zawsze włączone Określanie rozrzutu grupy dostępności|90% operacji zakończonych w ciągu 2,5 godzin + Time do wypełniania wszystkich baz danych (220 GB/godz.).|
|**Usunięcie**|Usunięcie wystąpienia|Kopia zapasowa dziennika dla wszystkich baz danych|90% operacji zakończonych w ciągu do 1 minuty.<br>Uwaga: Jeśli ostatnie wystąpienie w podsieci zostanie usunięte, ta operacja spowoduje zaplanowanie usunięcia klastra wirtualnego po upływie 12 godzin. * *|
|Usunięcie|Usuwanie klastra wirtualnego (jako operacja zainicjowana przez użytkownika)|Usuwanie klastra wirtualnego|90% operacji zakończonych w maksymalnie 1,5 godzinach.|

\*Klaster wirtualny jest oparty na generowaniu sprzętu.

\*\*Bieżąca konfiguracja to 12 godzin, ale może ona ulec zmianie w przyszłości, dlatego nie należy na niej korzystać. Jeśli musisz usunąć klaster wirtualny wcześniej (aby zwolnić podsieć, na przykład), zobacz [usuwanie podsieci po usunięciu wystąpienia zarządzanego](virtual-cluster-delete.md).

## <a name="instance-availability-during-management-operations"></a>Dostępność wystąpienia podczas operacji zarządzania

Wystąpienie zarządzane SQL **jest dostępne podczas operacji aktualizacji**, z wyjątkiem krótkich przestojów spowodowanych przez pracę w trybie failover, które są wykonywane po zakończeniu aktualizacji. Zwykle trwa to 10 sekund nawet w przypadku przerwanych długotrwałych transakcji, dzięki czemu możliwe jest [szybsze odzyskiwanie bazy danych](../accelerated-database-recovery.md).

> [!IMPORTANT]
> Nie jest zalecane skalowanie zasobów obliczeniowych lub magazynu wystąpienia zarządzanego usługi Azure SQL ani zmiana warstwy usług w tym samym czasie z długotrwałymi transakcjami (Importowanie danych, zadania przetwarzania danych, ponowne kompilowanie indeksu itp.). Praca w trybie failover bazy danych, która zostanie wykonana na końcu operacji, spowoduje anulowanie wszystkich bieżących transakcji.

Wystąpienie zarządzane SQL nie jest dostępne dla aplikacji klienckich podczas operacji wdrażania i usuwania.

## <a name="management-operations-cross-impact"></a>Wpływ operacji zarządzania

Operacje zarządzania w wystąpieniu zarządzanym mogą mieć wpływ na inne operacje zarządzania wystąpieniami znajdującymi się w tym samym klastrze wirtualnym:

- **Długotrwałe operacje przywracania** w klastrze wirtualnym będą zawierać inne operacje tworzenia wystąpień lub skalowania w tej samej podsieci.<br/>**Przykład:** Jeśli istnieje długotrwała operacja przywracania i istnieje żądanie utworzenia lub skalowania w tej samej podsieci, żądanie będzie trwać dłużej, ponieważ będzie oczekiwać na ukończenie operacji przywracania przed kontynuowaniem.
    
- **Kolejne operacje tworzenia wystąpienia lub skalowania** są wstrzymane przez wcześniej zainicjowane wystąpienie lub skalowanie wystąpienia, które zainicjowało zmianę rozmiaru klastra wirtualnego.<br/>**Przykład:** Jeśli istnieje wiele żądań tworzenia i/lub skalowania w tej samej podsieci w ramach tego samego klastra wirtualnego, a jedna z nich inicjuje zmianę rozmiaru klastra wirtualnego, wszystkie żądania, które zostały przesłane 5 minut po tym, które wymagały zmiany rozmiaru klastra wirtualnego, będą trwać dłużej niż oczekiwano, ponieważ te żądania będą musiały poczekać na zakończenie zmiany rozmiaru przed wznowieniem.

- **Operacje tworzenia/skalowania przesłane w 5-minutowych oknach zostaną przetworzone** w partii i wykonane równolegle.<br/>**Przykład:** Dla wszystkich operacji przesyłanych w oknie 5-minutowe zostanie wykonane tylko jedno Zmienianie rozmiaru klastra wirtualnego (mierzenie od momentu wykonania pierwszego żądania operacji). Jeśli kolejne żądanie zostanie przesłane więcej niż 5 minut po przesłaniu pierwszego z nich, czeka na ukończenie zmiany rozmiaru klastra wirtualnego przed rozpoczęciem wykonywania.

> [!IMPORTANT]
> Operacje zarządzania, które zostały wstrzymane, ponieważ inna operacja, która jest w toku, zostanie automatycznie wznowiona po spełnieniu warunków do kontynuowania. Nie trzeba wykonywać żadnych czynności przez użytkownika, aby wznowić tymczasowe wstrzymanie operacji zarządzania.

## <a name="canceling-management-operations"></a>Anulowanie operacji zarządzania

Poniższa tabela zawiera podsumowanie możliwości anulowania określonych operacji zarządzania i typowych ogólnych czasów trwania:

Kategoria  |Operacja  |Można anulować  |Szacowany czas trwania anulowania  |
|---------|---------|---------|---------|
|Wdrożenie |Tworzenie wystąpienia |Nie |  |
|Aktualizowanie |Skalowanie magazynu wystąpień w górę/w dół (Ogólnego przeznaczenia) |Nie |  |
|Aktualizowanie |Skalowanie magazynu wystąpień w górę/w dół (Krytyczne dla działania firmy) |Tak |90% operacji zakończonych w ciągu 5 minut. |
|Aktualizowanie |Skalowanie wystąpienia obliczeniowego (rdzeni wirtualnych) w górę i w dół (Ogólnego przeznaczenia) |Tak |90% operacji zakończonych w ciągu 5 minut. |
|Aktualizowanie |Skalowanie wystąpienia obliczeniowego (rdzeni wirtualnych) w górę i w dół (Krytyczne dla działania firmy) |Tak |90% operacji zakończonych w ciągu 5 minut. |
|Aktualizowanie |Zmiana warstwy usługi wystąpienia (Ogólnego przeznaczenia do Krytyczne dla działania firmy i na odwrót) |Tak |90% operacji zakończonych w ciągu 5 minut. |
|Usuń |Usunięcie wystąpienia |Nie |  |
|Usuń |Usuwanie klastra wirtualnego (jako operacja zainicjowana przez użytkownika) |Nie |  |

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby anulować operację zarządzania, przejdź do bloku przegląd i kliknij pole powiadomień trwającej operacji. Po prawej stronie pojawi się ekran z trwającą operacją i zostanie wyświetlony przycisk anulowania operacji. Po pierwszym kliknięciu zostanie wyświetlony monit o ponowne kliknięcie i potwierdzenie, że chcesz anulować operację.

[![Anuluj operację](./media/management-operations-overview/canceling-operation.png)](./media/management-operations-overview/canceling-operation.png#lightbox)

Gdy żądanie anulowania zostało przesłane i przetworzone, otrzymasz powiadomienie, jeśli przesłanie anulowania zakończyło się pomyślnie.

Jeśli przesłane żądanie anulowania zakończyło się pomyślnie, operacja zarządzania zostanie anulowana w ciągu kilku minut, co spowoduje wystąpienie błędu.

![Anulowanie wyniku operacji](./media/management-operations-overview/canceling-operation-result.png)

Jeśli żądanie anulowania nie powiedzie się lub przycisk Anuluj nie jest aktywny, oznacza to, że operacja zarządzania przeszła w stan niekończący i zostanie ukończona w ciągu kilku minut. Operacja zarządzania będzie kontynuować wykonywanie, dopóki nie zostanie ukończona.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Jeśli nie masz jeszcze zainstalowanego Azure PowerShell, zobacz [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps).

Aby anulować operację zarządzania, należy określić nazwę operacji zarządzania. W związku z tym należy najpierw użyć polecenia Pobierz do pobrania listy operacji, a następnie anulować konkretną operację.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Aby zapoznać się ze szczegółowymi poleceniami, zobacz [Get-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation) i [stop-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Jeśli nie masz jeszcze zainstalowanego interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

Aby anulować operację zarządzania, należy określić nazwę operacji zarządzania. W związku z tym należy najpierw użyć polecenia Pobierz do pobrania listy operacji, a następnie anulować konkretną operację.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do
    az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Aby zapoznać się ze szczegółowymi poleceniami, zobacz [AZ SQL mi op](https://docs.microsoft.com/cli/azure/sql/mi/op?view=azure-cli-latest).

---

## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane, zobacz [Przewodnik Szybki Start](instance-create-quickstart.md).
- Aby zapoznać się z funkcjami i listą porównania, zobacz [funkcje wspólne SQL](../database/features-comparison.md).
- Aby uzyskać więcej informacji na temat konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego SQL](connectivity-architecture-overview.md).
- W przypadku szybkiego startu, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).
- Aby zapoznać się z samouczkiem dotyczącym korzystania z Azure Database Migration Service migracji, zobacz [migracja wystąpienia zarządzanego SQL przy użyciu Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
