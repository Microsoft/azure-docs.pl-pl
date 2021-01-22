---
title: 'Szybki Start: Konfigurowanie izolacji obciążenia — Portal'
description: Użyj Azure Portal, aby skonfigurować izolację obciążenia dla dedykowanej puli SQL.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 5773691852ddc723ec84503edee37c678bbbcfd9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677508"
---
# <a name="quickstart-configure-dedicated-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>Szybki Start: Skonfiguruj dedykowaną izolację obciążenia puli SQL przy użyciu grupy obciążeń w Azure Portal

W tym przewodniku szybki start skonfigurujesz [izolację obciążenia](sql-data-warehouse-workload-isolation.md) , tworząc grupę obciążeń na potrzeby rezerwowania zasobów.  Na potrzeby tego samouczka utworzymy grupę obciążeń na potrzeby ładowania danych `DataLoads` . Grupa obciążeń zarezerwuje 20% zasobów systemowych.  Dzięki obciążeniu o 20% w przypadku ładowania danych są one gwarantowane, dzięki czemu mogą trafiać do umowy SLA.  Po utworzeniu grupy obciążeń [Utwórz klasyfikator obciążeń](quickstart-create-a-workload-classifier-portal.md) , aby przypisać zapytania do tej grupy obciążeń.


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.


## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

> [!NOTE]
> Tworzenie dedykowanego wystąpienia puli SQL w usłudze Azure Synapse Analytics może spowodować powstanie nowej usługi do obciążania.  Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku szybki start założono, że masz już dedykowane wystąpienie puli SQL w Synapse SQL i że masz uprawnienia sterujące bazą danych. Jeśli trzeba ją utworzyć, Skorzystaj z [przewodnika Szybki Start: Tworzenie dedykowanej puli SQL — Portal](../quickstart-create-sql-pool-portal.md) , aby utworzyć magazyn danych o nazwie **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Dedykowana Pula SQL musi być w trybie online, aby można było skonfigurować zarządzanie obciążeniami. 

## <a name="configure-workload-isolation"></a>Konfigurowanie izolacji obciążenia

Dedykowane zasoby puli SQL mogą być izolowane i zarezerwowane dla konkretnych obciążeń przez tworzenie grup obciążeń.  Zapoznaj się z dokumentacją dotyczącą pojęć związanych z [izolacją obciążenia](sql-data-warehouse-workload-isolation.md) , aby uzyskać więcej szczegółowych informacji o sposobie zarządzania obciążeniem przez grupy obciążeń.  Przewodnik Szybki Start dotyczący [tworzenia i nawiązywania połączenia z portalem](create-data-warehouse-portal.md) został utworzony przez **mySampleDataWarehouse** i zainicjowany na DW100c. Poniższe kroki tworzą grupę obciążeń w **mySampleDataWarehouse**.

Aby utworzyć grupę obciążeń z izolacją 20%:
1.  Przejdź do strony dedykowanej puli SQL **mySampleDataWarehouse** .
1.  Wybierz pozycję  **zarządzanie obciążeniami**.
1.  Wybierz pozycję **Nowa grupa obciążeń**.
1.  wybierz pozycję **Niestandardowe**.

    ![Kliknij pozycję niestandardowe](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  Wprowadź `DataLoads` dla **grupy obciążenia**.
7.  Wprowadź wartość `20` w obszarze **min. resources%**.
8.  Wprowadź wartość `5` w obszarze **min. resources% na żądanie**.
9.  Wprowadź `100` dla **zasobów Cap%**.
10. Wprowadź **Zapisz**.

   ![Klikanie pozycji Zapisz.](./media/quickstart-configure-workload-isolation-portal/configure-wg.png)

Podczas tworzenia grupy obciążeń pojawia się powiadomienie w portalu.  Zasoby grupy obciążeń są wyświetlane na wykresach poniżej skonfigurowanych wartości.

   ![Kliknij przycisk Final](./media/quickstart-configure-workload-isolation-portal/display-wg.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć `DataLoads` grupę obciążeń utworzoną w tym samouczku:
1. Kliknij z **`...`** prawej strony `DataLoads` grupy obciążenia.
2. Kliknij pozycję **Usuń grupę obciążeń**.
3. Kliknij przycisk **tak** po wyświetleniu monitu, aby potwierdzić usunięcie grupy obciążeń.
4. Kliknij pozycję **Zapisz**.

   ![Kliknij pozycję Usuń.](./media/quickstart-configure-workload-isolation-portal/delete-wg.png)



Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w magazynie danych. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz przechowywać dane w magazynie, możesz wstrzymać obliczenia, gdy nie korzystasz z magazynu danych. Przez wstrzymywanie obliczeń opłata jest naliczana tylko za magazyn danych. Gdy wszystko będzie gotowe do pracy z danymi, Wznów obliczenia.
- Aby uniknąć opłat w przyszłości, możesz usunąć magazyn danych.

Wykonaj następujące kroki, aby wyczyścić zasoby.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz dedykowaną pulę SQL.

    ![Czyszczenie zasobów](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, wybierz przycisk **Wstrzymaj** . Gdy magazyn danych jest wstrzymany, zobaczysz przycisk **Uruchom**.  Aby wznowić obliczenia, wybierz pozycję **Uruchom**.

3. Aby usunąć magazyn danych, aby nie naliczać opłat za zasoby obliczeniowe i magazynowanie, wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Aby użyć `DataLoads` grupy obciążenia, należy utworzyć [klasyfikator obciążenia](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) w celu kierowania żądań do grupy obciążeń.  Przejdź do samouczka [Tworzenie klasyfikatora obciążeń](quickstart-create-a-workload-classifier-portal.md) , aby utworzyć klasyfikator obciążeń dla `DataLoads` .

## <a name="see-also"></a>Zobacz też
Aby uzyskać szczegółowe informacje na temat monitorowania obciążeń związanych z zarządzaniem obciążeniem, zobacz artykuł How to [manage and monitoringing Management](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) .
