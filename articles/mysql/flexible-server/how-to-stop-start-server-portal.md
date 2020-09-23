---
title: Zatrzymywanie/uruchamianie Azure Portal-Azure Database for MySQL elastyczny serwer
description: W tym artykule opisano sposób zatrzymania/uruchamiania operacji w Azure Database for MySQL przez Azure Portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: b9f406035d32a9af9ba2f5b085bcaca1b51e9d92
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937281"
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

> [!NOTE]
> Po zatrzymaniu serwera inne operacje zarządzania nie są dostępne dla serwera elastycznego.

## <a name="start-a-stopped-server"></a>Uruchom zatrzymany serwer

1.  W [Azure Portal](https://portal.azure.com/)wybierz elastyczny serwer, który chcesz uruchomić.

2.  Na stronie **Przegląd** kliknij przycisk **Zatrzymaj** na pasku narzędzi.

> [!NOTE]
> Po uruchomieniu serwera wszystkie operacje zarządzania będą teraz dostępne dla serwera elastycznego.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [sieci w Azure Database for MySQL elastycznym serwerze](./concepts-networking.md)
- [Utwórz Azure Database for MySQL elastyczną sieć wirtualną serwera i zarządzaj nią przy użyciu Azure Portal](./how-to-manage-virtual-network-portal.md).

