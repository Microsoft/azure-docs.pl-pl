---
title: 'Szybki Start: Konfigurowanie izolacji obciążenia — Portal'
description: Użyj Azure Portal, aby skonfigurować izolację obciążenia.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 70ac4942c397e8ca5db2d1b5041d0d9d43ae7222
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82794056"
---
# <a name="quickstart-configure-synapse-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>Szybki Start: Konfigurowanie Synapse izolacji puli SQL przy użyciu grupy obciążeń w Azure Portal

W tym przewodniku szybki start skonfigurujesz [izolację obciążenia](sql-data-warehouse-workload-isolation.md) , tworząc grupę obciążeń na potrzeby rezerwowania zasobów.  Na potrzeby tego samouczka utworzymy grupę obciążeń na potrzeby ładowania danych `DataLoads`. Grupa obciążeń zarezerwuje 20% zasobów systemowych.  Dzięki obciążeniu o 20% w przypadku ładowania danych są one gwarantowane, dzięki czemu mogą trafiać do umowy SLA.  Po utworzeniu grupy obciążeń [Utwórz klasyfikator obciążeń](quickstart-create-a-workload-classifier-portal.md) , aby przypisać zapytania do tej grupy obciążeń.


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.


## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [portalu Azure](https://portal.azure.com/).

> [!NOTE]
> Utworzenie wystąpienia puli SQL w usłudze Azure Synapse Analytics może spowodować powstanie nowej usługi do obciążania.  Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku szybki start założono, że masz już wystąpienie puli SQL w Synapse SQL i że masz uprawnienia sterujące bazą danych. Jeżeli chcesz utworzyć taki magazyn, skorzystaj z przewodnika [Szybki start: tworzenie i łączenie — portal](create-data-warehouse-portal.md), aby utworzyć magazyn danych o nazwie **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Aby można było skonfigurować zarządzanie obciążeniami, Pula SQL musi być w trybie online. 

## <a name="configure-workload-isolation"></a>Konfigurowanie izolacji obciążenia
Zasoby puli SQL mogą być izolowane i zarezerwowane dla konkretnych obciążeń przez tworzenie grup obciążeń.  Zapoznaj się z dokumentacją dotyczącą pojęć związanych z [izolacją obciążenia](sql-data-warehouse-workload-isolation.md) , aby uzyskać więcej szczegółowych informacji o sposobie zarządzania obciążeniem przez grupy obciążeń.  Przewodnik Szybki Start dotyczący [tworzenia i nawiązywania połączenia z portalem](create-data-warehouse-portal.md) został utworzony przez **mySampleDataWarehouse** i zainicjowany z 400 jednostek dwu. Poniższe kroki tworzą grupę obciążeń w **mySampleDataWarehouse**.

Aby utworzyć grupę obciążeń z izolacją 20%:
1.  Kliknij pozycję **Azure Synapse Analytics (wcześniej SQL DW)** na lewej stronie Azure Portal.
2.  Wybierz pozycję **mySampleDataWarehouse** na stronie **usługi Azure Synapse Analytics (dawniej SQL DW)** . Zostanie otwarta Pula SQL.
3.  Kliknij pozycję **zarządzanie obciążeniami**.
4.  Kliknij pozycję **Nowa grupa obciążeń**.
5.  Kliknij pozycję **niestandardowy**.

    ![Kliknij pozycję niestandardowe](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  Wprowadź `DataLoads` dla **grupy obciążenia**.
7.  Wprowadź `20` wartość w obszarze **min. resources%**.
8.  Wprowadź `5` wartość w obszarze **min. resources% na żądanie**.
9.  Wprowadź `100` dla **zasobów Cap%**.
10.   Kliknij przycisk **Zapisz**.

   ![Klikanie pozycji Zapisz.](./media/quickstart-configure-workload-isolation-portal/configure-wg.png)

Podczas tworzenia grupy obciążeń pojawia się powiadomienie w portalu.  Zasoby grupy obciążeń są wyświetlane na wykresach poniżej skonfigurowanych wartości.

   ![Kliknij przycisk Final](./media/quickstart-configure-workload-isolation-portal/display-wg.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć grupę `DataLoads` obciążeń utworzoną w tym samouczku:
1. **`...`** Kliknij z prawej strony grupy `DataLoads` obciążenia.
2. Kliknij pozycję **Usuń grupę obciążeń**.
3. Kliknij przycisk **tak** po wyświetleniu monitu, aby potwierdzić usunięcie grupy obciążeń.
4. Kliknij pozycję **Zapisz**.

   ![Kliknij pozycję Usuń.](./media/quickstart-configure-workload-isolation-portal/delete-wg.png)



Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w magazynie danych. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz przechowywać dane w magazynie, możesz wstrzymać obliczenia, gdy nie korzystasz z magazynu danych. Przez wstrzymywanie obliczeń opłata jest naliczana tylko za magazyn danych. Gdy wszystko będzie gotowe do pracy z danymi, Wznów obliczenia.
- Aby uniknąć opłat w przyszłości, możesz usunąć magazyn danych.

Wykonaj następujące kroki, aby wyczyścić zasoby.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz pozycję Magazyn danych.

    ![Oczyszczanie zasobów](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, wybierz przycisk **Wstrzymaj** . Gdy magazyn danych jest wstrzymany, zobaczysz przycisk **Uruchom**.  Aby wznowić obliczenia, wybierz pozycję **Uruchom**.

3. Aby usunąć magazyn danych, aby nie naliczać opłat za zasoby obliczeniowe i magazynowanie, wybierz pozycję **Usuń**.

4. Aby usunąć utworzony serwer SQL, wybierz pozycję **sqlpoolservername.Database.Windows.NET** na poprzednim obrazie, a następnie wybierz pozycję **Usuń**.  Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, wybierz pozycję Moja **zasobów**, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

Aby użyć grupy `DataLoads` obciążenia, należy utworzyć [klasyfikator obciążenia](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) w celu kierowania żądań do grupy obciążeń.  Przejdź do samouczka [Tworzenie klasyfikatora obciążeń](quickstart-create-a-workload-classifier-portal.md) , aby utworzyć klasyfikator obciążeń dla `DataLoads`.

## <a name="see-also"></a>Zobacz także
Aby uzyskać szczegółowe informacje na temat monitorowania obciążeń związanych z zarządzaniem obciążeniem, zobacz artykuł How to [manage and monitoringing Management](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) .
