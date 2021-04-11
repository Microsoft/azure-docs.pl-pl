---
title: Zarządzanie replikami odczytu — Azure Portal-Azure Database for PostgreSQL-ze skalowaniem (Citus)
description: Dowiedz się, jak zarządzać replikami odczytów Azure Database for PostgreSQL-Citus z Azure Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 4d3a88692da6d1fc78e96a6261d42d3ca97b0dd0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024035"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---hyperscale-citus-from-the-azure-portal"></a>Tworzenie replik odczytu i zarządzanie nimi w Azure Database for PostgreSQL-Citus) z Azure Portal

> [!IMPORTANT]
> Odczytaj repliki w funkcji Citus) są obecnie dostępne w wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
>
> Pełną listę innych nowych funkcji można znaleźć w artykule [dotyczącym funkcji Citus (wersja zapoznawcza)](hyperscale-preview-features.md).

W tym artykule dowiesz się, jak tworzyć repliki odczytu i zarządzać nimi w Citus z poziomu Azure Portal. Aby dowiedzieć się więcej o replikach odczytu, zobacz [Omówienie](concepts-hyperscale-read-replicas.md).


## <a name="prerequisites"></a>Wymagania wstępne

[Grupa serwerów ze skalowaniem (Citus)](quickstart-create-hyperscale-portal.md) jako podstawowa.

## <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu

Aby utworzyć replikę odczytu, wykonaj następujące kroki:

1. Wybierz istniejącą grupę serwerów Azure Database for PostgreSQL, która ma być używana jako podstawowa. 

2. Na pasku bocznym grupy serwerów, w obszarze **Zarządzanie grupą serwerów**, wybierz pozycję **replikacja**.

3. Wybierz pozycję **Dodaj replikę**.

4. Wprowadź nazwę repliki do odczytu. 

5. Wybierz **przycisk OK** , aby potwierdzić utworzenie repliki.

Po utworzeniu repliki odczytu można ją wyświetlić w oknie **replikacja** .

> [!IMPORTANT]
>
> Zapoznaj się z [sekcją zagadnienia w temacie Omówienie repliki odczytu](concepts-hyperscale-read-replicas.md#considerations).
>
> Przed zaktualizowaniem ustawienia podstawowej grupy serwerów do nowej wartości należy zaktualizować ustawienie repliki na wartość równą lub większą. Ta akcja ułatwia przeprowadzenie replikacji ze wszystkimi zmianami wprowadzonymi do wzorca.

## <a name="delete-a-primary-server-group"></a>Usuwanie podstawowej grupy serwerów

Aby usunąć podstawową grupę serwerów, należy wykonać te same czynności co w przypadku usuwania autonomicznej grupy serwerów Citus. 

> [!IMPORTANT]
>
> Po usunięciu podstawowej grupy serwerów replikacja do wszystkich replik odczytu zostanie zatrzymana. Repliki odczytu stają się autonomicznymi grupami serwerów, które teraz obsługują operacje odczytu i zapisu.

Aby usunąć grupę serwerów z Azure Portal, wykonaj następujące kroki:

1. W Azure Portal wybierz grupę serwerów Azure Database for PostgreSQL podstawowej.

2. Otwórz stronę **Przegląd** dla grupy serwerów. Wybierz pozycję **Usuń**.
 
3. Wprowadź nazwę głównej grupy serwerów do usunięcia. Wybierz pozycję **Usuń** , aby potwierdzić usunięcie podstawowej grupy serwerów.
 

## <a name="delete-a-replica"></a>Usuwanie repliki

Replikę odczytu można usunąć podobnie jak w przypadku usuwania podstawowej grupy serwerów.

- W Azure Portal Otwórz stronę **Przegląd** repliki odczytu. Wybierz pozycję **Usuń**.
 
Możesz również usunąć replikę odczytu z okna **replikacji** , wykonując następujące czynności:

1. W Azure Portal wybierz grupę serwerów podstawowej Citus.

2. W menu Grupa serwerów, w obszarze **Zarządzanie grupą serwerów**, wybierz opcję **replikacja**.

3. Wybierz replikę do odczytania do usunięcia.
 
4. Wybierz pozycję **Usuń replikę**.
 
5. Wprowadź nazwę repliki do usunięcia. Wybierz pozycję **Usuń** , aby potwierdzić usunięcie repliki.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [na temat odczytu replik w Azure Database for PostgreSQL-Citus](concepts-hyperscale-read-replicas.md).
