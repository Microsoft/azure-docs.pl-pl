---
title: Operacje skalowania — Azure Portal-Azure Database for PostgreSQL-elastyczny serwer
description: W tym artykule opisano sposób wykonywania operacji skalowania w Azure Database for PostgreSQL przez Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 1542bba53b51ffdf2129953a81e5d13975ade434
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939246"
---
# <a name="scale-operations-in-flexible-server"></a>Operacje skalowania na serwerze elastycznym

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Ten artykuł zawiera kroki umożliwiające wykonywanie operacji skalowania na potrzeby obliczeń i magazynu. Możesz zmienić warstwy obliczeń między jednostkami obliczeniowymi, przeznaczeniem ogólnym i zoptymalizowanymi pod kątem pamięci, w tym wybierając liczbę rdzeni wirtualnych, która jest odpowiednia do uruchamiania aplikacji. Możesz również skalować w górę magazyn. Oczekiwane liczby IOPS są wyświetlane na podstawie warstwy obliczeniowej, rdzeni wirtualnych i pojemności magazynu. Oszacowanie kosztów jest również wyświetlane na podstawie dokonanego wyboru.

> [!IMPORTANT]
> Nie można skalować w dół magazynu.

## <a name="pre-requisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

-   Musisz mieć Azure Database for PostgreSQL-elastyczny serwer. Ta sama procedura dotyczy również elastycznego serwera z konfiguracją nadmiarowości stref.
> [!IMPORTANT]
> W przypadku skonfigurowania wysokiej dostępności nie można wybrać jednostki SKU do pojemności. Podczas operacji skalowania rezerwa jest najpierw skalowana do żądanego rozmiaru, serwer podstawowy jest przełączona w tryb failover, a podstawowa jest skalowana. 

## <a name="scaling-the-compute-tier-and-size"></a>Skalowanie warstwy obliczeniowej i rozmiaru

Wykonaj następujące kroki, aby wybrać warstwę obliczeniową.
 
1.  W [Azure Portal](https://portal.azure.com/)wybierz serwer elastyczny, z którego chcesz przywrócić kopię zapasową.

2.  Kliknij pozycję **obliczeniowe + magazyn**.

3.  Zostanie wyświetlona strona z bieżącymi ustawieniami.
 :::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="Widok obliczeniowy + magazyn":::

4.  Można wybrać klasę obliczeniową między warstwami, przeznaczeniem ogólnym i zoptymalizowanymi pod kątem pamięci.
   :::image type="content" source="./media/how-to-scale-compute-storage-portal/list-compute-tiers.png" alt-text="Widok obliczeniowy + magazyn":::


5.  Jeśli chcesz mieć domyślne rozmiary rdzeni wirtualnych i pamięci, możesz pominąć następny krok.

6.  Jeśli chcesz zmienić liczbę rdzeni wirtualnych, możesz kliknąć listę rozwijaną **rozmiar obliczeń** i kliknąć żądaną liczbę rdzeni wirtualnych/pamięci z listy.
    
    - Warstwa :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-burstable-dropdown.png" alt-text="Widok obliczeniowy + magazyn"::: do przetworzenia

    - Warstwa obliczeniowa ogólnego zastosowania: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-general-purpose-dropdown.png" alt-text="Widok obliczeniowy + magazyn":::

    - Warstwa obliczeniowa zoptymalizowana pod kątem pamięci: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-memory-optimized-dropdown.png" alt-text="Widok obliczeniowy + magazyn":::

7.  Kliknij przycisk **Zapisz**. 
8.  Zostanie wyświetlony komunikat z potwierdzeniem. Kliknij przycisk **OK** , aby wykonać operację. 
9.  Powiadomienie o trwającej operacji skalowania.


## <a name="scaling-storage-size"></a>Skalowanie rozmiaru magazynu

Wykonaj następujące kroki, aby zwiększyć rozmiar magazynu.

1.  W [Azure Portal](https://portal.azure.com/)wybierz elastyczny serwer, dla którego chcesz zwiększyć rozmiar magazynu.
2.  Kliknij pozycję **obliczeniowe + magazyn**.

3.  Zostanie wyświetlona strona z bieżącymi ustawieniami.
   
:::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="Widok obliczeniowy + magazyn":::
4.  **Rozmiar magazynu pól w GIB** z paskiem slajdu jest wyświetlany z bieżącym rozmiarem.

5.  Przesuń pasek do żądanego rozmiaru. Pokazany jest odpowiedni numer operacji we/wy. Liczba operacji we/wy zależy od warstwy obliczeń i rozmiaru. Wyświetlane są również informacje o kosztach. 

 :::image type="content" source="./media/how-to-scale-compute-storage-portal/storage-scaleup.png" alt-text="Widok obliczeniowy + magazyn":::

6.  Jeśli masz dobry rozmiar magazynu, kliknij przycisk **Zapisz**. 
7.  Zostanie wyświetlony komunikat z potwierdzeniem. Kliknij przycisk **OK** , aby wykonać operację. 
8.  Powiadomienie o trwającej operacji skalowania.

## <a name="next-steps"></a>Następne kroki

-   Informacje o [ciągłości działania firmy](./concepts-business-continuity.md)
-   Więcej informacji o [wysokiej dostępności](./concepts-high-availability.md)
-   Informacje na temat [tworzenia kopii zapasowych i odzyskiwania](./concepts-backup-restore.md)
