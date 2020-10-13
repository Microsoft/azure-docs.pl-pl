---
title: Zarządzanie strefą nadmiarową o wysokiej dostępności — Azure Portal-Azure Database for MySQL elastyczny serwer
description: W tym artykule opisano sposób włączania lub wyłączania nadmiarowej wysokiej dostępności strefy w Azure Database for MySQL elastycznym serwerze przy użyciu Azure Portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: 09cd7428519cbf84c785efa16b61b9507a3c0b94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940507"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>Zarządzanie strefą nadmiarową wysoką dostępność w Azure Database for MySQL elastycznym serwerze (wersja zapoznawcza)

W tym artykule opisano, jak można włączyć lub wyłączyć konfigurację nadmiarowej wysokiej dostępności strefy na serwerze elastycznym.

Funkcja wysokiej dostępności stanowi fizycznie oddzielną replikę podstawową i rezerwową w różnych strefach. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą pojęć o wysokiej dostępności](./concepts/../concepts-high-availability.md). 

> [!IMPORTANT]
> Można włączyć tylko nadmiarową wysoką dostępność strefy podczas tworzenia elastycznego serwera.

Na tej stronie przedstawiono wskazówki dotyczące włączania lub wyłączania wysokiej dostępności. Ta operacja nie zmienia innych ustawień, takich jak konfiguracja sieci wirtualnej, ustawienia zapory i przechowywanie kopii zapasowych. Podobnie wyłączenie wysokiej dostępności jest operacją online i nie ma wpływu na łączność i działanie aplikacji.

> [!IMPORTANT]
> Strefa "nadmiarowa wysoka dostępność" jest dostępna w ograniczonej liczbie regionów: Azja Południowo-Wschodnia, zachodnie 2, Europa Zachodnia i Wschodnie stany USA.  

## <a name="enable-high-availability-during-server-creation"></a>Włącz wysoką dostępność podczas tworzenia serwera

Ta sekcja zawiera szczegółowe informacje dotyczące pól związanych z HA. Poniższe kroki umożliwiają wdrożenie wysokiej dostępności podczas tworzenia elastycznego serwera.

1.  W [Azure Portal](https://portal.azure.com/)wybierz opcję elastyczny serwer i kliknij pozycję **Utwórz**.  Aby uzyskać szczegółowe informacje na temat wypełniania szczegółów, takich jak **subskrypcja**, **Grupa zasobów**, **Nazwa serwera**, **region**i inne pola, zobacz dokumentację dotyczącą tworzenia serwera.

2.  Kliknij pole wyboru dla **strefy nadmiarowa wysoka dostępność** w opcji dostępność.

3.  Jeśli chcesz zmienić domyślne obliczenia i magazyn, kliknij pozycję  **Konfiguruj serwer**.

4.  Jeśli opcja wysoka dostępność jest zaznaczona, warstwa do przetworzenia nie będzie dostępna do wybrania. Można wybrać warstwy obliczeń **ogólnego przeznaczenia** lub **zoptymalizowane pod kątem pamięci** .

    > [!IMPORTANT]
    > Firma Microsoft obsługuje tylko nadmiarową wysoką dostępność strefy dla warstwy cenowej ***ogólnego przeznaczenia*** i ***zoptymalizowanej pod kątem pamięci*** .

5.  Wybierz wybrany **rozmiar obliczeń** z listy rozwijanej.

6.  Wybierz pozycję **Rozmiar magazynu** w GIB przy użyciu paska przesuwania i wybierz **okres przechowywania kopii zapasowych** w ciągu 7 dni i 35 dni.   

## <a name="disable-high-availability"></a>Wyłącz wysoką dostępność

Wykonaj następujące kroki, aby wyłączyć wysoką dostępność dla elastycznego serwera, który jest już skonfigurowany za pomocą nadmiarowości strefy.

1.  W [Azure Portal](https://portal.azure.com/)wybierz istniejący Azure Database for MySQL elastyczny serwer.

2.  Na stronie serwer elastyczny kliknij pozycję **wysoka dostępność** na panelu przednim, aby otworzyć stronę o wysokiej dostępności.

3.  Kliknij pole wyboru **strefa nadmiarowa wysoka dostępność** , aby wyłączyć opcję, a następnie kliknij przycisk **Zapisz**,   Aby zapisać zmiany.

4.  Zostanie wyświetlone okno dialogowe potwierdzenia, w którym można potwierdzić wyłączenie wysokiej dostępności.

5.  Kliknij przycisk **Wyłącz przycisk ha** , aby wyłączyć wysoką dostępność.

6.  Zostanie wyświetlone powiadomienie z informacją o wycofaniu wdrożenia o wysokiej dostępności.

## <a name="next-steps"></a>Następne kroki

-   Informacje o [ciągłości działania firmy](./concepts-business-continuity.md)
-   Więcej informacji na temat [strefy nadmiarowej wysokiej dostępności](./concepts-high-availability.md)
