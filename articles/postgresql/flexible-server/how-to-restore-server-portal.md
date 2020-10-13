---
title: Przywracanie Azure Portal-Azure Database for PostgreSQL-elastyczny serwer
description: W tym artykule opisano sposób wykonywania operacji przywracania w Azure Database for PostgreSQL przez Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e69bcb3d9e4dca4c45bf9a6fe8ed4d54e7f4a8cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90938866"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>Przywracanie do punktu w czasie dla elastycznego serwera

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Ten artykuł zawiera procedury krok po kroku umożliwiające odzyskiwanie do punktu w czasie na serwerze elastycznym przy użyciu kopii zapasowych. W okresie przechowywania można wykonać jedną z najstarszych punktów przywracania lub niestandardowy punkt przywracania.

## <a name="pre-requisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

-   Musisz mieć Azure Database for PostgreSQL-elastyczny serwer. Ta sama procedura dotyczy również elastycznego serwera z konfiguracją nadmiarowości stref.

## <a name="restoring-to-the-earliest-restore-point"></a>Przywracanie do najwcześniejszego punktu przywracania

Wykonaj następujące kroki, aby przywrócić elastyczny serwer przy użyciu najwcześniejszej istniejącej kopii zapasowej.

1.  W [Azure Portal](https://portal.azure.com/)wybierz serwer elastyczny, z którego chcesz przywrócić kopię zapasową.

2.  Kliknij pozycję **Przegląd** w panelu po lewej stronie, a następnie kliknij pozycję **Przywróć** .
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Przegląd przywracania":::

3.  Zostanie wyświetlona strona Przywróć z opcją wyboru między najwcześniejszym punktem przywracania i niestandardowym punktem przywracania.

4.  Wybierz **najwcześniejszy punkt przywracania** i podaj nową nazwę serwera w polu **Przywróć do nowego serwera** . Zostanie wyświetlona Najwcześniejsza sygnatura czasowa, do której można wykonać przywracanie. 
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-earliest.png" alt-text="Przegląd przywracania":::

5.  Kliknij przycisk **OK**.

6.  Zostanie wyświetlone powiadomienie z informacją o zainicjowaniu operacji przywracania.

## <a name="restoring-to-a-custom-restore-point"></a>Przywracanie do niestandardowego punktu przywracania

Wykonaj następujące kroki, aby przywrócić elastyczny serwer przy użyciu najwcześniejszej istniejącej kopii zapasowej.

1.  W [Azure Portal](https://portal.azure.com/)wybierz serwer elastyczny, z którego chcesz przywrócić kopię zapasową.

2.  Na stronie Przegląd kliknij przycisk **Przywróć**.
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Przegląd przywracania":::
    
3.  Zostanie wyświetlona strona Przywróć z opcją wyboru między najwcześniejszym punktem przywracania i niestandardowym punktem przywracania.

4.  Wybierz **niestandardowy punkt przywracania**.

5.  Wybierz datę i godzinę i podaj nową nazwę serwera w polu **Przywróć do nowego serwera** . 
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom.png" alt-text="Przegląd przywracania":::
 
6.  Kliknij przycisk **OK**.

7.  Zostanie wyświetlone powiadomienie z informacją o zainicjowaniu operacji przywracania.

## <a name="next-steps"></a>Następne kroki

-   Informacje o [ciągłości działania firmy](./concepts-business-continuity.md)
-   Więcej informacji na temat [strefy nadmiarowej wysokiej dostępności](./concepts-high-availability.md)
-   Informacje na temat [tworzenia kopii zapasowych i odzyskiwania](./concepts-backup-restore.md)
