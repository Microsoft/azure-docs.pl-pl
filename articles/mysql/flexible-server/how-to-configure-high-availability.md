---
title: Zarządzanie strefą nadmiarową o wysokiej dostępności — Azure Portal-Azure Database for MySQL elastyczny serwer
description: W tym artykule opisano sposób włączania lub wyłączania nadmiarowej wysokiej dostępności strefy w Azure Database for MySQL elastycznym serwerze przy użyciu Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: d65b074385311e74444929ef74901e402e29ec03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93241739"
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

1.  W [Azure Portal](https://portal.azure.com/)wybierz opcję elastyczny serwer i kliknij pozycję **Utwórz**.  Aby uzyskać szczegółowe informacje na temat wypełniania szczegółów, takich jak **subskrypcja**, **Grupa zasobów**, **Nazwa serwera**, **region** i inne pola, zobacz dokumentację dotyczącą tworzenia serwera.

2.  Kliknij pole wyboru dla **strefy nadmiarowa wysoka dostępność** w opcji dostępność.

3.  Jeśli chcesz zmienić domyślne obliczenia i magazyn, kliknij pozycję  **Konfiguruj serwer**.

4.  Jeśli opcja wysoka dostępność jest zaznaczona, warstwa do przetworzenia nie będzie dostępna do wybrania. Można wybrać warstwy obliczeń **ogólnego przeznaczenia** lub **zoptymalizowane pod kątem pamięci** .

    > [!IMPORTANT]
    > Firma Microsoft obsługuje tylko nadmiarową wysoką dostępność dla * warstwy cenowej ***ogólnego przeznaczenia** _ i _ *_zoptymalizowanej pod kątem pamięci_*.

5.  Wybierz wybrany **rozmiar obliczeń** z listy rozwijanej.

6.  Wybierz pozycję **Rozmiar magazynu** w GIB przy użyciu paska przesuwania i wybierz **okres przechowywania kopii zapasowych** w ciągu 7 dni i 35 dni.   

## <a name="disable-high-availability"></a>Wyłącz wysoką dostępność

Wykonaj następujące kroki, aby wyłączyć wysoką dostępność dla elastycznego serwera, który jest już skonfigurowany za pomocą nadmiarowości strefy.

1.  W [Azure Portal](https://portal.azure.com/)wybierz istniejący Azure Database for MySQL elastyczny serwer.

2.  Na stronie serwer elastyczny kliknij pozycję **wysoka dostępność** na panelu przednim, aby otworzyć stronę o wysokiej dostępności.

3.  Kliknij pole wyboru **strefa nadmiarowa wysoka dostępność** , aby wyłączyć opcję, a następnie kliknij przycisk **Zapisz** , aby zapisać zmiany.

4.  Zostanie wyświetlone okno dialogowe potwierdzenia, w którym można potwierdzić wyłączenie wysokiej dostępności.

5.  Kliknij przycisk **Wyłącz przycisk ha** , aby wyłączyć wysoką dostępność.

6.  Zostanie wyświetlone powiadomienie z informacją o wycofaniu wdrożenia o wysokiej dostępności.

## <a name="next-steps"></a>Następne kroki

-   Informacje o [ciągłości działania firmy](./concepts-business-continuity.md)
-   Więcej informacji na temat [strefy nadmiarowej wysokiej dostępności](./concepts-high-availability.md)
