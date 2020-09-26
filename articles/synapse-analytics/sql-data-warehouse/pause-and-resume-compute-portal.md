---
title: Wstrzymywanie i wznawianie obliczeń w Synapse puli SQL za pośrednictwem Azure Portal
description: Użyj Azure Portal, aby wstrzymać obliczenia dla puli SQL w celu oszczędności kosztów. Wznów obliczanie, gdy wszystko jest gotowe do korzystania z magazynu danych.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: aa86161bce0b52eb1043d792fb2f4770df0992ee
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369420"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-via-the-azure-portal"></a>Szybki Start: Wstrzymywanie i wznawianie obliczeń w Synapse puli SQL za pośrednictwem Azure Portal

Za pomocą Azure Portal można wstrzymywać i wznawiać zasoby obliczeniowe puli SQL (Data Warehouse) Synapse. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Użyj instrukcji [Create i Connect-Portal](create-data-warehouse-portal.md) , aby utworzyć pulę SQL o nazwie **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Wstrzymywanie obliczeń

Aby obniżyć koszty, możesz wstrzymywać i wznawiać zasoby obliczeniowe na żądanie. Na przykład jeśli baza danych nie będzie używana w porze nocnej i w weekendy, możesz ją wstrzymać w tych godzinach i wznowić ją w ciągu dnia. 
>[!NOTE]
>Nie będzie naliczana opłata za zasoby obliczeniowe, gdy baza danych jest wstrzymana. Opłata za magazyn będzie jednak nadal naliczana. 

Wykonaj następujące kroki, aby wstrzymać pulę SQL:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Kliknij pozycję **Azure Synapse Analytics (wcześniej SQL DW)** na lewej stronie nawigacyjnej Azure Portal.
2. Wybierz pozycję **mySampleDataWarehouse** na stronie **usługi Azure Synapse Analytics (dawniej SQL DW)** , aby otworzyć pulę SQL. 
3. Na stronie **MySampleDataWarehouse** **stan** powiadomienia jest w **trybie online**.

    ![Oblicz w trybie online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Aby wstrzymać pulę SQL, kliknij przycisk **Wstrzymaj** . 
5. Zostanie wyświetlone pytanie z prośbą o potwierdzenie, czy chcesz kontynuować. Kliknij przycisk **Yes** (Tak).
6. Poczekaj chwilę, a następnie sprawdź, czy **stan** jest **wstrzymywany**.

    ![Zrzut ekranu przedstawia Azure Portal dla przykładowego magazynu danych z wartością stanu wstrzymywanie.](./media/pause-and-resume-compute-portal/pausing.png)

7. Po zakończeniu operacji wstrzymania stan jest **wstrzymany** i przycisk opcji zostaje **wznowiony**.
8. Zasoby obliczeniowe dla puli SQL są teraz w trybie offline. Nie zostanie naliczona opłata za obliczenia, dopóki usługa nie zostanie wznowiona.

    ![Oblicz w trybie offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Wznów Obliczanie

Wykonaj następujące kroki, aby wznowić pulę SQL.

1. Kliknij pozycję **Azure Synapse Analytics (wcześniej SQL DW)** na lewej stronie Azure Portal.
2. Wybierz pozycję **mySampleDataWarehouse** na stronie **usługi Azure Synapse Analytics (dawniej SQL DW)** , aby otworzyć stronę Pula SQL. 
3. Na stronie **MySampleDataWarehouse** **stan** powiadomienia jest **wstrzymany**.

    ![Oblicz w trybie offline](././media/pause-and-resume-compute-portal/compute-offline.png)

4. Aby wznowić pulę SQL, kliknij przycisk **Wznów**. 
5. Zostanie wyświetlone pytanie z prośbą o potwierdzenie, czy chcesz zacząć. Kliknij przycisk **Yes** (Tak).
6. Zauważ, że **stan** jest **wznawiany**.

    ![Zrzut ekranu przedstawia Azure Portal dla przykładowego magazynu danych z wybranym przyciskiem Start i wartością stanu wznawiania.](./media/pause-and-resume-compute-portal/resuming.png)

7. Gdy pula SQL jest w trybie online, stan jest w **trybie online** i przycisk opcji jest **wstrzymany**.
8. Zasoby obliczeniowe dla puli SQL są teraz w trybie online i można korzystać z tej usługi. Opłaty za obliczenia zostały wznowione.

    ![Oblicz w trybie online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w puli SQL. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno. 

- Jeśli chcesz zachować dane w magazynie, Wstrzymaj obliczenia.
- Jeśli chcesz usunąć przyszłe opłaty, możesz usunąć pulę SQL. 

Wykonaj następujące kroki, aby wyczyścić zasoby zgodnie z potrzebami.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i kliknij pulę SQL.

    ![Czyszczenie zasobów](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Aby wstrzymać obliczenia, kliknij przycisk **Wstrzymaj**. 

2. Aby usunąć pulę SQL, aby nie naliczać opłat za zasoby obliczeniowe i magazynowanie, kliknij przycisk **Usuń**.

3. Aby usunąć utworzony serwer SQL, kliknij pozycję **sqlpoolservername.Database.Windows.NET**, a następnie kliknij pozycję **Usuń**.  

   > [!CAUTION]
   > Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, kliknij pozycję **myResourceGroup**, a następnie kliknij pozycję **Usuń grupę zasobów**.


## <a name="next-steps"></a>Następne kroki

Teraz wstrzymane i wznowione obliczenia dla puli SQL. Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat [ładowania danych do puli SQL](load-data-from-azure-blob-storage-using-polybase.md). Aby uzyskać dodatkowe informacje na temat zarządzania możliwościami obliczeniowymi, zobacz artykuł [Zarządzanie obliczeniami obliczeniowymi](sql-data-warehouse-manage-compute-overview.md) . 

