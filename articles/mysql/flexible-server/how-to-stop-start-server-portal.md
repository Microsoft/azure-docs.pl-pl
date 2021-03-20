---
title: Zatrzymywanie/uruchamianie Azure Portal-Azure Database for MySQL elastyczny serwer
description: W tym artykule opisano sposób zatrzymania/uruchamiania operacji w Azure Database for MySQL przez Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 15b08ea67afe0d307470b5a4fb0f7d26e0f4ea82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93241926"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Zatrzymywanie/uruchamianie Azure Database for MySQL-elastyczny serwer (wersja zapoznawcza)

> [!IMPORTANT]
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Ten artykuł zawiera procedury krok po kroku dotyczące przetrzymywania i uruchamiania elastycznego serwera.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

-   Musisz mieć Azure Database for MySQL elastyczny serwer.

## <a name="stop-a-running-server"></a>Zatrzymaj uruchomiony serwer

1.  W [Azure Portal](https://portal.azure.com/)wybierz elastyczny serwer, który ma zostać zatrzymany.

2.  Na stronie **Przegląd** kliknij przycisk **Zatrzymaj** na pasku narzędzi.
    
    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="Zatrzymaj elastyczny serwer."::: 

3.  Kliknij przycisk **tak** , aby potwierdzić zatrzymywanie serwera.

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="Upewnij się, że serwer elastyczny został zatrzymany."::: 

> [!NOTE]
> Po zatrzymaniu serwera inne operacje zarządzania nie są dostępne dla serwera elastycznego.

## <a name="start-a-stopped-server"></a>Uruchom zatrzymany serwer

1.  W [Azure Portal](https://portal.azure.com/)wybierz elastyczny serwer, który chcesz uruchomić.

2.  Na stronie **Przegląd** kliknij przycisk **Start** na pasku narzędzi.

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="Uruchom serwer elastyczny.":::  

> [!NOTE]
> Po uruchomieniu serwera wszystkie operacje zarządzania będą teraz dostępne dla serwera elastycznego.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [sieci w Azure Database for MySQL elastycznym serwerze](./concepts-networking.md)
- [Utwórz Azure Database for MySQL elastyczną sieć wirtualną serwera i zarządzaj nią przy użyciu Azure Portal](./how-to-manage-virtual-network-portal.md).

