---
title: 'Szybki start: skalowanie zasobów obliczeniowych Synapse SQL puli (Azure Portal)'
description: Zasoby obliczeniowe dla puli Synapse SQL (magazyn danych) można skalować przy użyciu Azure Portal.
services: synapse-analytics
author: Antvgski
ms.author: anvang
manager: craigg
ms.reviewer: jrasnick
ms.date: 04/28/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: aa339274a1ff68764fa5573d9c031e84f290e57c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534224"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Szybki start: skalowanie zasobów obliczeniowych Synapse SQL puli przy użyciu Azure Portal

Zasoby obliczeniowe dla puli Synapse SQL (magazyn danych) można skalować przy użyciu Azure Portal. [Skalowanie zasobów obliczeniowych w poziomie](sql-data-warehouse-manage-compute-overview.md) zapewnia lepszą wydajność, a zmniejszenie ich skali pozwala ograniczyć koszty. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Możesz skalować już posiadaną pulę SQL lub użyć przewodnika Szybki start: tworzenie i łączenie — [portal,](create-data-warehouse-portal.md) aby utworzyć pulę SQL o nazwie **mySampleDataWarehouse.** W tym przewodniku Szybki start jest skalowana baza danych **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Pula SQL musi być w trybie online, aby można było skalować. 

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych

Zasoby obliczeniowe puli SQL można skalować przez zwiększenie lub zmniejszenie liczby jednostek magazynu danych. Przewodnik [Szybki start: tworzenie i łączenie — portal](create-data-warehouse-portal.md) utworzył magazyn **mySampleDataWarehouse** i zainicjował go z 400 magazynami DANYCH. Poniższe kroki umożliwiają dostosowanie liczby jednostek DWU dla bazy danych **mySampleDataWarehouse**.

Aby zmienić liczbę jednostek magazynu danych:

1. Kliknij **Azure Synapse Analytics (dawniej SQL DW)** na lewej stronie Azure Portal.
2. Wybierz **pozycję mySampleDataWarehouse** na **stronie Azure Synapse Analytics (dawniej SQL DW).** Zostanie otwarta pula SQL.
3. Kliknij pozycję **Skaluj.**

    ![Klikanie przycisku Skaluj](./media/quickstart-scale-compute-portal/click-scale.png)

2. Na panelu Skalowanie przesuń suwak w lewo lub w prawo, aby zmienić ustawienie jednostek DWU. Następnie wybierz pozycję Skaluj.

    ![Przesuwanie suwaka](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat puli SQL, przejdź do [samouczka Load data into SQL pool (Ładowanie danych do puli SQL).](./load-data-from-azure-blob-storage-using-copy.md)
