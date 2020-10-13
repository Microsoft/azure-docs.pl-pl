---
title: Ponowne uruchomienie Azure Portal-Azure Database for PostgreSQL-elastyczny serwer
description: W tym artykule opisano sposób wykonywania operacji ponownego uruchamiania w Azure Database for PostgreSQL przez Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 952bd6dddf9f276ed1a4a18f03799147f1902198
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936933"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>Uruchom ponownie Azure Database for PostgreSQL — elastyczny serwer

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Ten artykuł zawiera procedurę krok po kroku umożliwiającą ponowne uruchomienie serwera elastycznego. Ta operacja jest przydatna do zastosowania wszelkich statycznych zmian parametrów, które wymagają ponownego uruchomienia serwera bazy danych. Procedura jest taka sama w przypadku serwerów skonfigurowanych z nadmiarową wysoką dostępnością strefy. 

> [!IMPORTANT]
> W przypadku skonfigurowania wysokiej dostępności zarówno serwery podstawowe, jak i rezerwowe są ponownie uruchamiane w tym samym czasie.

## <a name="pre-requisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

-   Musisz mieć elastyczny serwer.

## <a name="restart-your-flexible-server"></a>Uruchom ponownie elastyczny serwer

Wykonaj następujące kroki, aby ponownie uruchomić elastyczny serwer.

1.  W [Azure Portal](https://portal.azure.com/)wybierz elastyczny serwer, który chcesz ponownie uruchomić.

2.  Kliknij pozycję **Przegląd** w lewym panelu, a następnie kliknij pozycję **Uruchom ponownie**.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="Uruchom ponownie zaznaczenie":::

3.  Zostanie wyświetlony podręczny komunikat z potwierdzeniem.

4.  Kliknij przycisk **tak** , jeśli chcesz kontynuować.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="Uruchom ponownie zaznaczenie":::
 
6.  Zostanie wyświetlone powiadomienie z informacją o zainicjowaniu operacji ponownego uruchomienia.

## <a name="next-steps"></a>Następne kroki

-   Informacje o [ciągłości działania firmy](./concepts-business-continuity.md)
-   Więcej informacji na temat [strefy nadmiarowej wysokiej dostępności](./concepts-high-availability.md)
