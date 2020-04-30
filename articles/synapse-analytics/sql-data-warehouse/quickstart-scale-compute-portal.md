---
title: Skalowanie obliczeń dla puli SQL Synapse (Azure Portal)
description: Obliczenia można skalować dla puli SQL Synapse (magazyn danych) przy użyciu Azure Portal.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/28/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 96c3c05fea912433731dc7a6a8ab705ab76d3058
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232227"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Szybki Start: skalowanie zasobów obliczeniowych dla puli SQL Synapse przy użyciu Azure Portal

Obliczenia można skalować dla puli SQL Synapse (magazyn danych) przy użyciu Azure Portal. [Skalowanie zasobów obliczeniowych w poziomie](sql-data-warehouse-manage-compute-overview.md) zapewnia lepszą wydajność, a zmniejszenie ich skali pozwala ograniczyć koszty. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Możesz skalować pulę SQL, która już istnieje, lub korzystać z [przewodnika Szybki Start: Tworzenie i łączenie — Portal](create-data-warehouse-portal.md) , aby utworzyć pulę SQL o nazwie **mySampleDataWarehouse**. W tym przewodniku Szybki start jest skalowana baza danych **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Pula SQL musi być w trybie online, aby można było skalować ją. 

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych

Zasoby obliczeniowe puli SQL można skalować przez zwiększenie lub zmniejszenie liczby jednostek magazynu danych. [Przewodnik Szybki Start: Tworzenie i łączenie — Portal](create-data-warehouse-portal.md) utworzony **mySampleDataWarehouse** i zainicjowany przy użyciu 400 jednostek dwu. Poniższe kroki umożliwiają dostosowanie liczby jednostek DWU dla bazy danych **mySampleDataWarehouse**.

Aby zmienić liczbę jednostek magazynu danych:

1. Kliknij pozycję **Azure Synapse Analytics (wcześniej SQL DW)** na lewej stronie Azure Portal.
2. Wybierz pozycję **mySampleDataWarehouse** na stronie **usługi Azure Synapse Analytics (dawniej SQL DW)** . Zostanie otwarta Pula SQL.
3. Kliknij przycisk **Skaluj**.

    ![Klikanie przycisku Skaluj](./media/quickstart-scale-compute-portal/click-scale.png)

2. Na panelu Skalowanie przesuń suwak w lewo lub w prawo, aby zmienić ustawienie jednostek DWU. Następnie wybierz pozycję Skala.

    ![Przesuwanie suwaka](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o puli SQL, przejdź do samouczka [ładowanie danych do puli SQL](load-data-from-azure-blob-storage-using-polybase.md) . 
