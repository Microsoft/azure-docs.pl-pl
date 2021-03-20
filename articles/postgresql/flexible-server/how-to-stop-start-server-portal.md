---
title: Zatrzymywanie/uruchamianie Azure Portal-Azure Database for PostgreSQL elastyczny serwer
description: W tym artykule opisano sposób zatrzymania/uruchamiania operacji w Azure Database for PostgreSQL przez Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 4c393e0048a0058ebe0fbf2b0ee65f6ae2e184c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90938856"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview"></a>Zatrzymywanie/uruchamianie Azure Database for PostgreSQL-elastyczny serwer (wersja zapoznawcza)

> [!IMPORTANT]
> Serwer elastyczny Azure Database for PostgreSQL jest obecnie w wersji zapoznawczej.

Ten artykuł zawiera instrukcje krok po kroku dotyczące zatrzymywania i uruchamiania elastycznego serwera.

## <a name="pre-requisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

-   Musisz mieć Azure Database for PostgreSQL elastyczny serwer.

## <a name="stop-a-running-server"></a>Zatrzymaj uruchomiony serwer

1.  W [Azure Portal](https://portal.azure.com/)wybierz elastyczny serwer, który ma zostać zatrzymany.

2.  Na stronie **Przegląd** kliknij przycisk **Zatrzymaj** na pasku narzędzi.

> [!NOTE]
> Po zatrzymaniu serwera inne operacje zarządzania nie są dostępne dla serwera elastycznego.

Należy pamiętać, że zatrzymane serwery zostaną automatycznie uruchomione ponownie po upływie siedmiu dni. Wszystkie oczekujące aktualizacje konserwacji zostaną zastosowane po następnym uruchomieniu serwera.

## <a name="start-a-stopped-server"></a>Uruchom zatrzymany serwer

1.  W [Azure Portal](https://portal.azure.com/)wybierz elastyczny serwer, który chcesz uruchomić.

2.  Na stronie **Przegląd** kliknij przycisk **Start** na pasku narzędzi.

> [!NOTE]
> Po uruchomieniu serwera wszystkie operacje zarządzania będą teraz dostępne dla serwera elastycznego.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [opcjach obliczeniowych i magazynu w Azure Database for PostgreSQL elastycznym serwerze](./concepts-compute-storage.md).
