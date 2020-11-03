---
title: Przywracanie Azure Portal-Azure Database for MySQL-elastyczny serwer
description: W tym artykule opisano sposób wykonywania operacji przywracania w Azure Database for MySQL przez Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 062d53fcb122ebacd004d7dca5e11f5a883354cd
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241960"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview"></a>Przywracanie do punktu w czasie na serwerze elastycznym Azure Database for MySQL (wersja zapoznawcza)


> [!IMPORTANT]
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Ten artykuł zawiera procedury krok po kroku umożliwiające odzyskiwanie do punktu w czasie na serwerze elastycznym przy użyciu kopii zapasowych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

-   Musisz mieć Azure Database for MySQL elastyczny serwer.

## <a name="restore-to-the-latest-restore-point"></a>Przywróć do najnowszego punktu przywracania

Wykonaj następujące kroki, aby przywrócić elastyczny serwer przy użyciu najwcześniejszej istniejącej kopii zapasowej.

1.  W [Azure Portal](https://portal.azure.com/)wybierz serwer elastyczny, z którego chcesz przywrócić kopię zapasową.

2.  Kliknij pozycję **Przegląd** w lewym panelu.

3.  Na stronie Przegląd kliknij przycisk **Przywróć** .

    Symbol

4.  Zostanie wyświetlona strona Przywróć z opcją wyboru między **najnowszym punktem przywracania** a niestandardowym punktem przywracania.

5.  Wybierz **najnowszy punkt przywracania** .


6.  Podaj nową nazwę serwera w polu **Przywróć do nowego serwera** .

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="Najwcześniejszy czas przywracania":::

8.  Kliknij pozycję **OK** .

9.  Zostanie wyświetlone powiadomienie z informacją o zainicjowaniu operacji przywracania.

## <a name="restoring-to-a-custom-restore-point"></a>Przywracanie do niestandardowego punktu przywracania

Wykonaj następujące kroki, aby przywrócić elastyczny serwer przy użyciu najwcześniejszej istniejącej kopii zapasowej.

1.  W [Azure Portal](https://portal.azure.com/)wybierz serwer elastyczny, z którego chcesz przywrócić kopię zapasową.

2.  Na stronie Przegląd kliknij przycisk **Przywróć** .

    Symbol

3.  Zostanie wyświetlona strona Przywróć z opcją wyboru między najwcześniejszym punktem przywracania i niestandardowym punktem przywracania.

4.  Wybierz **niestandardowy punkt przywracania** .

5.  Wybierz datę i godzinę.

6.  Podaj nową nazwę serwera w polu **Przywróć do nowego serwera** .

6.  Podaj nową nazwę serwera w polu **Przywróć do nowego serwera** . 
   
    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="Najwcześniejszy czas przywracania":::
 
7.  Kliknij pozycję **OK** .

8.  Zostanie wyświetlone powiadomienie z informacją o zainicjowaniu operacji przywracania.

## <a name="next-steps"></a>Następne kroki

Symbol zastępczy
