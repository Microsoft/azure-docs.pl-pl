---
title: Zarządzanie strefą nadmiarową o wysokiej dostępności — Azure Portal-Azure Database for PostgreSQL-elastyczny serwer
description: W tym artykule opisano sposób włączania lub wyłączania nadmiarowej wysokiej dostępności strefy w Azure Database for PostgreSQL-elastycznym serwerze przy użyciu Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: fc1bca1265139a438fad86bfce770026866d9a2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90937005"
---
# <a name="manage-zone-redundant-high-availability-in-flexible-server"></a>Zarządzanie strefowo nadmiarową wysoką dostępnością na serwerze elastycznym

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

W tym artykule opisano, jak można włączyć lub wyłączyć konfigurację nadmiarowej wysokiej dostępności strefy na serwerze elastycznym.

Funkcja wysokiej dostępności stanowi fizycznie oddzielną replikę podstawową i rezerwową w różnych strefach. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą pojęć o wysokiej dostępności](./concepts-high-availability.md). Możesz włączyć wysoką dostępność w czasie elastycznego tworzenia serwera lub po utworzeniu. Na tej stronie przedstawiono wskazówki dotyczące włączania lub wyłączania wysokiej dostępności. Ta operacja nie zmienia innych ustawień, takich jak konfiguracja sieci wirtualnej, ustawienia zapory i przechowywanie kopii zapasowych. Podobnie włączenie i wyłączenie wysokiej dostępności jest operacją online i nie ma wpływu na łączność i działanie aplikacji.

## <a name="pre-requisites"></a>Wymagania wstępne

Strefa nadmiarowa wysokiej dostępności jest dostępna tylko w regionach, w których obsługiwane są wiele stref. 

## <a name="enable-high-availability-during-server-creation"></a>Włącz wysoką dostępność podczas tworzenia serwera

Ta sekcja zawiera szczegółowe informacje dotyczące pól związanych z HA. Poniższe kroki umożliwiają wdrożenie wysokiej dostępności podczas tworzenia elastycznego serwera.

1.  W [Azure Portal](https://portal.azure.com/)wybierz opcję elastyczny serwer i kliknij pozycję Utwórz.  Aby uzyskać szczegółowe informacje na temat wypełniania szczegółów, takich jak **subskrypcja**, **Grupa zasobów**, **Nazwa serwera**, **region** i inne pola, zobacz dokumentację dotyczącą tworzenia serwera.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/subscription-region.png" alt-text="Wyświetl subskrypcję i region":::

2.  Wybierz **strefę dostępności**. Jest to przydatne, jeśli chcesz rozstawić swoją aplikację w tej samej strefie dostępności co baza danych w celu zmniejszenia opóźnień. Wybierz opcję **Brak preferencji** , jeśli chcesz, aby serwer elastyczny został wdrożony w dowolnej strefie dostępności.
    ![Opcja AZ zaznacz ]() :::image type="content" source="./media/how-to-manage-high-availability-portal/zone-selection.png" alt-text="strefę dostępności":::  

3.  Kliknij pole wyboru dla **strefy nadmiarowa wysoka dostępność** w opcji dostępność.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-checkbox.png" alt-text="Pole wyboru wysokiej dostępności":::

4.  Jeśli chcesz zmienić domyślne obliczenia i magazyn, kliknij pozycję  **Konfiguruj serwer**.
 
    :::image type="content" source="./media/how-to-manage-high-availability-portal/configure-server.png" alt-text="Konfigurowanie serwera — obliczenia i magazyn":::  

5.  Jeśli opcja wysoka dostępność jest zaznaczona, warstwa do przetworzenia nie będzie dostępna do wybrania. Można wybrać warstwy obliczeń **ogólnego przeznaczenia** lub **zoptymalizowane pod kątem pamięci** . Następnie możesz wybrać wybrany przez siebie **rozmiar** z listy rozwijanej.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/select-compute.png" alt-text="Wybór warstwy obliczeniowej":::  


6.  Wybierz pozycję **Rozmiar magazynu** w GIB przy użyciu paska przesuwania i wybierz **okres przechowywania kopii zapasowych** w ciągu 7 dni i 35 dni.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/storage-backup.png" alt-text="Kopia zapasowa magazynu"::: 

7. Kliknij pozycję **Zapisz**. 

## <a name="enable-high-availability-post-server-creation"></a>Włącz tworzenie serwera po wysokiej dostępności

Wykonaj następujące kroki, aby włączyć wysoką dostępność dla istniejącego serwera elastycznego.

1.  W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer elastyczny PostgreSQL.

2.  Na stronie serwer elastyczny kliknij pozycję **wysoka dostępność** w panelu po lewej stronie, aby otworzyć stronę o wysokiej dostępności.
   
     :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Lewy panel wyboru"::: 

3.  Kliknij pole wyboru **strefa nadmiarowa wysoka dostępność** , aby **włączyć** tę opcję, a następnie kliknij przycisk **Zapisz** , aby zapisać zmiany.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/enable-high-availability.png" alt-text="Włącz wysoką dostępność"::: 

4.  W oknie dialogowym potwierdzenia zostanie wyświetlona informacja, że dzięki włączeniu wysokiej dostępności koszt zostanie zwiększony ze względu na dodatkowe wdrożenie serwera i magazynu.

5.  Kliknij przycisk **Włącz ha** , aby włączyć wysoką dostępność.

6.  Zostanie wyświetlone powiadomienie z informacją o tym, że wdrożenie wysokiej dostępności jest w toku.

## <a name="disable-high-availability"></a>Wyłącz wysoką dostępność

Wykonaj następujące kroki, aby wyłączyć wysoką dostępność dla elastycznego serwera, który jest już skonfigurowany za pomocą nadmiarowości strefy.

1.  W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer Azure Database for PostgreSQL-elastyczny.

2.  Na stronie serwer elastyczny kliknij pozycję **wysoka dostępność** na panelu przednim, aby otworzyć stronę o wysokiej dostępności.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Lewy panel wyboru"::: 

3.  Kliknij pole wyboru **strefa nadmiarowa wysoka dostępność** , aby **wyłączyć** opcję. Następnie kliknij przycisk **Zapisz** , aby zapisać zmiany.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/disable-high-availability.png" alt-text="Wyłącz wysoką dostępność"::: 

4.  Zostanie wyświetlone okno dialogowe potwierdzenia, w którym można potwierdzić wyłączenie wysokiej dostępności.

5.  Kliknij przycisk **Wyłącz przycisk ha** , aby wyłączyć wysoką dostępność.

6.  Zostanie wyświetlone powiadomienie z informacją o wycofaniu wdrożenia o wysokiej dostępności.

## <a name="next-steps"></a>Następne kroki

-   Informacje o [ciągłości działania firmy](./concepts-business-continuity.md)
-   Więcej informacji na temat [strefy nadmiarowej wysokiej dostępności](./concepts-high-availability.md)
