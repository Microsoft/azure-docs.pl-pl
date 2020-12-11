---
title: 'Szybki Start: Wstrzymywanie i wznawianie obliczeń w dedykowanej puli SQL za pośrednictwem Azure Portal'
description: Użyj Azure Portal, aby wstrzymać obliczenia dla dedykowanej puli SQL w celu oszczędności kosztów. Wznów obliczanie, gdy wszystko jest gotowe do korzystania z magazynu danych.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7d258243dae06461d21e9b5f0346e419f034eea9
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109701"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-via-the-azure-portal"></a>Szybki Start: Wstrzymywanie i wznawianie obliczeń w dedykowanej puli SQL za pośrednictwem Azure Portal

Za pomocą Azure Portal można wstrzymywać i wznawiać dedykowane zasoby obliczeniowe puli SQL. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Użyj instrukcji [Create i Connect-Portal](../quickstart-create-sql-pool-portal.md) , aby utworzyć dedykowaną pulę SQL o nazwie **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Wstrzymywanie obliczeń

Aby obniżyć koszty, możesz wstrzymywać i wznawiać zasoby obliczeniowe na żądanie. Na przykład jeśli baza danych nie będzie używana w porze nocnej i w weekendy, możesz ją wstrzymać w tych godzinach i wznowić ją w ciągu dnia.
 
>[!NOTE]
>Nie będzie naliczana opłata za zasoby obliczeniowe, gdy baza danych jest wstrzymana. Opłata za magazyn będzie jednak nadal naliczana. 

Wykonaj następujące kroki, aby wstrzymać dedykowaną pulę SQL:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do strony **dedykowanej puli SQL** , aby otworzyć pulę SQL. 
3. **Stan** powiadomienia jest w **trybie online**.

    ![Oblicz w trybie online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Aby wstrzymać dedykowaną pulę SQL, kliknij przycisk **Wstrzymaj** . 
5. Zostanie wyświetlone pytanie z prośbą o potwierdzenie, czy chcesz kontynuować. Kliknij przycisk **Yes** (Tak).
6. Poczekaj chwilę, a następnie sprawdź, czy **stan** jest **wstrzymywany**.

    ![Zrzut ekranu przedstawia Azure Portal dla przykładowego magazynu danych z wartością stanu wstrzymywanie.](./media/pause-and-resume-compute-portal/pausing.png)

7. Po zakończeniu operacji wstrzymania stan jest **wstrzymany** i przycisk opcji zostaje **wznowiony**.
8. Zasoby obliczeniowe dla dedykowanej puli SQL są teraz w trybie offline. Nie zostanie naliczona opłata za obliczenia, dopóki usługa nie zostanie wznowiona.

    ![Oblicz w trybie offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Wznów Obliczanie

Wykonaj następujące kroki, aby wznowić dedykowaną pulę SQL.

1. Przejdź do strony **dedykowanej puli SQL** , aby otworzyć pulę SQL.
3. Na stronie **MySampleDataWarehouse** **stan** powiadomienia jest **wstrzymany**.

    ![Oblicz w trybie offline](././media/pause-and-resume-compute-portal/compute-offline.png)

1. Aby wznowić pulę SQL, kliknij przycisk **Wznów**. 
1. Zostanie wyświetlone pytanie z prośbą o potwierdzenie, czy chcesz zacząć. Kliknij przycisk **Yes** (Tak).
1. Zauważ, że **stan** jest **wznawiany**.

    ![Zrzut ekranu przedstawia Azure Portal dla przykładowego magazynu danych z wybranym przyciskiem Start i wartością stanu wznawiania.](./media/pause-and-resume-compute-portal/resuming.png)

1. Gdy pula SQL jest w trybie online, stan jest w **trybie online** i przycisk opcji jest **wstrzymany**.
1. Zasoby obliczeniowe dla puli SQL są teraz w trybie online i można korzystać z tej usługi. Opłaty za obliczenia zostały wznowione.

    ![Oblicz w trybie online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w dedykowanej puli SQL. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno. 

- Jeśli chcesz zachować dane w magazynie, Wstrzymaj obliczenia.
- Jeśli chcesz usunąć przyszłe opłaty, możesz usunąć dedykowaną pulę SQL. 

Wykonaj następujące kroki, aby wyczyścić zasoby zgodnie z potrzebami.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i wybierz dedykowaną pulę SQL.

    ![Czyszczenie zasobów](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Aby wstrzymać obliczenia, kliknij przycisk **Wstrzymaj**. 

1. Aby usunąć dedykowaną pulę SQL, aby nie naliczać opłat za zasoby obliczeniowe i magazynowanie, kliknij przycisk **Usuń**.



## <a name="next-steps"></a>Następne kroki

Teraz wstrzymane i wznowione obliczenia dla dedykowanej puli SQL. Przejdź do następnego artykułu, aby dowiedzieć się więcej o tym, jak [załadować dane do dedykowanej puli SQL](load-data-from-azure-blob-storage-using-polybase.md). Aby uzyskać dodatkowe informacje na temat zarządzania możliwościami obliczeniowymi, zobacz artykuł [Zarządzanie obliczeniami obliczeniowymi](sql-data-warehouse-manage-compute-overview.md) . 

