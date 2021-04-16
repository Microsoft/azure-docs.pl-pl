---
title: 'Szybki start: konfigurowanie izolacji obciążeń — portal'
description: Użyj Azure Portal, aby skonfigurować izolację obciążenia dla dedykowanej puli SQL.
services: synapse-analytics
author: ronortloff
ms.author: rortloff
manager: craigg
ms.reviewer: jrasnick
ms.date: 05/04/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- azure-synapse
- mode-portal
ms.openlocfilehash: c15f21064012c195315a7f91908b3160591dc6f8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534255"
---
# <a name="quickstart-configure-dedicated-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>Szybki start: konfigurowanie izolacji obciążeń dedykowanej puli SQL przy użyciu grupy obciążeń w Azure Portal

W tym przewodniku Szybki start skonfigurujesz izolację [obciążeń,](sql-data-warehouse-workload-isolation.md) tworząc grupę obciążeń na potrzeby rezerwowania zasobów.  Na potrzeby tego samouczka utworzymy grupę obciążeń do ładowania danych o nazwie `DataLoads` . Grupa obciążeń zarezerwuje 20% zasobów systemowych.  W przypadku 20% izolacji ładowania danych mają one zagwarantowane zasoby, które umożliwiają im dojrzecie do sla.  Po utworzeniu grupy obciążeń utwórz [klasyfikator obciążenia,](quickstart-create-a-workload-classifier-portal.md) aby przypisać zapytania do tej grupy obciążeń.


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.


## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

> [!NOTE]
> Utworzenie dedykowanego wystąpienia puli SQL w Azure Synapse Analytics może spowodować utworzenie nowej rozliczanej usługi.  Aby uzyskać więcej informacji, [zobacz Azure Synapse Analytics cennika.](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku Szybki start założono, że masz już dedykowane wystąpienie puli SQL Synapse SQL i że masz uprawnienia CONTROL DATABASE. Jeśli musisz go utworzyć, użyj przewodnika [Szybki start:](../quickstart-create-sql-pool-portal.md) tworzenie dedykowanej puli SQL — portal, aby utworzyć magazyn danych o nazwie **mySampleDataWarehouse.**

>[!IMPORTANT] 
>Dedykowana pula SQL musi być w trybie online, aby skonfigurować zarządzanie obciążeniami. 

## <a name="configure-workload-isolation"></a>Konfigurowanie izolacji obciążeń

Dedykowane zasoby puli SQL można odizolować i zarezerwowane dla określonych obciążeń, tworząc grupy obciążeń.  Zobacz [dokumentację koncepcji izolacji](sql-data-warehouse-workload-isolation.md) obciążeń, aby uzyskać więcej informacji na temat sposobu, w jaki grupy obciążeń ułatwiają zarządzanie obciążeniem.  W [przewodniku Szybki](create-data-warehouse-portal.md) start Tworzenie i łączenie — portal utworzono **magazyn mySampleDataWarehouse** i zainicjowano go w bazie danych DW100c. Poniższe kroki tworzą grupę obciążeń w bazie **danych mySampleDataWarehouse.**

Aby utworzyć grupę obciążeń z izolacją 20%:
1.  Przejdź do strony dedykowanej puli SQL **mySampleDataWarehouse.**
1.  Wybierz **pozycję Zarządzanie obciążeniami.**
1.  Wybierz **pozycję Nowa grupa obciążeń.**
1.  wybierz pozycję **Niestandardowe**.

    ![Kliknij pozycję Niestandardowy](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  Wprowadź `DataLoads` wartość dla grupy **obciążenia**.
7.  Wprowadź `20` wartość w wartości Procent **minimalnych zasobów.**
8.  Wprowadź `5` wartość w przypadku **minimalnej wartości % zasobów na żądanie.**
9.  Wprowadź `100` wartość w wartości % zasobów **limitu.**
10. Wprowadź **wartość Zapisz**.

   ![Klikanie pozycji Zapisz.](./media/quickstart-configure-workload-isolation-portal/configure-wg.png)

Po utworzeniu grupy obciążeń zostanie wyświetlone powiadomienie w portalu.  Zasoby grupy obciążeń są wyświetlane na wykresach poniżej skonfigurowanych wartości.

   ![Kliknij pozycję Final (Końcowa)](./media/quickstart-configure-workload-isolation-portal/display-wg.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć `DataLoads` grupę obciążeń utworzoną w tym samouczku:
1. Kliknij pozycję **`...`** po prawej stronie grupy `DataLoads` obciążeń.
2. Kliknij pozycję **Usuń grupę obciążeń.**
3. Po **wyświetleniu** monitu o potwierdzenie usunięcia grupy obciążeń kliknij przycisk Tak.
4. Kliknij pozycję **Zapisz**.

   ![Kliknij pozycję Usuń.](./media/quickstart-configure-workload-isolation-portal/delete-wg.png)



Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w magazynie danych. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz przechowywać dane w magazynie, możesz wstrzymać obliczenia, gdy nie korzystasz z magazynu danych. Wstrzymanie obliczeń pozwala na naliczanie opłat tylko za magazyn danych. Gdy wszystko będzie gotowe do pracy z danymi, wznów obliczenia.
- Aby uniknąć opłat w przyszłości, możesz usunąć magazyn danych.

Wykonaj następujące kroki, aby wyczyścić zasoby.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz dedykowaną pulę SQL.

    ![Czyszczenie zasobów](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, wybierz przycisk **Wstrzymaj.** Gdy magazyn danych jest wstrzymany, zobaczysz przycisk **Uruchom**.  Aby wznowić obliczenia, wybierz pozycję **Uruchom.**

3. Aby usunąć magazyn danych, aby nie ponosić opłat za zasoby obliczeniowe lub magazynowe, wybierz pozycję **Usuń.**

## <a name="next-steps"></a>Następne kroki

Aby użyć `DataLoads` grupy obciążeń, należy [utworzyć klasyfikator](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) obciążenia w celu przekierowania żądań do grupy obciążeń.  Przejdź do [samouczka tworzenia klasyfikatora obciążenia,](quickstart-create-a-workload-classifier-portal.md) aby utworzyć klasyfikator obciążenia dla klasyfikatora `DataLoads` .

## <a name="see-also"></a>Zobacz też
Aby uzyskać szczegółowe informacje na temat monitorowania obciążeń na potrzeby zarządzania obciążeniami, zobacz artykuł Zarządzanie [obciążeniami](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) i monitorowanie go.
