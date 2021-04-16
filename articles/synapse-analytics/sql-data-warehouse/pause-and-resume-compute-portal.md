---
title: 'Szybki start: wstrzymywanie i wznawianie obliczeń w dedykowanej puli SQL za pośrednictwem Azure Portal'
description: Skorzystaj z Azure Portal, aby wstrzymać obliczenia dla dedykowanej puli SQL, aby zaoszczędzić koszty. Wznawianie obliczeń, gdy wszystko będzie gotowe do korzystania z magazynu danych.
services: synapse-analytics
author: gaursa
ms.author: gaursa
manager: craigg
ms.reviewer: igorstan
ms.date: 11/23/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: 21e0fa7bb96dcb96cae054f853a94a757dd44d46
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536348"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-via-the-azure-portal"></a>Szybki start: wstrzymywanie i wznawianie obliczeń w dedykowanej puli SQL za pośrednictwem Azure Portal

Możesz użyć tej Azure Portal, aby wstrzymać i wznowić dedykowane zasoby obliczeniowe puli SQL. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Użyj [polecenia Utwórz i połącz — portal,](../quickstart-create-sql-pool-portal.md) aby utworzyć dedykowaną pulę SQL o nazwie **mySampleDataWarehouse.** 

## <a name="pause-compute"></a>Wstrzymywanie zasobów obliczeniowych

Aby zmniejszyć koszty, możesz wstrzymać i wznowić zasoby obliczeniowe na żądanie. Jeśli na przykład nie będziesz używać bazy danych w nocy i w weekendy, możesz ją wstrzymać w tych godzinach i wznowić ją w ciągu dnia.
 
>[!NOTE]
>Nie zostaną naliczone opłaty za zasoby obliczeniowe, gdy baza danych jest wstrzymana. Jednak nadal będą naliczane opłaty za magazyn. 

Wykonaj następujące kroki, aby wstrzymać dedykowaną pulę SQL:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do strony **Dedykowana pula SQL,** aby otworzyć pulę SQL. 
3. Zwróć **uwagę, że stan** to **Online.**

    ![Obliczenia w trybie online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Aby wstrzymać dedykowaną pulę SQL, kliknij przycisk **Wstrzymaj.** 
5. Zostanie wyświetlone pytanie z potwierdzeniem z pytaniem, czy chcesz kontynuować. Kliknij przycisk **Yes** (Tak).
6. Zaczekaj chwilę, a następnie zwróć uwagę, że **stan** ma stan **Wsieć**.

    ![Zrzut ekranu Azure Portal przykładowego magazynu danych z wartością Stan Wsieć.](./media/pause-and-resume-compute-portal/pausing.png)

7. Po zakończeniu operacji wstrzymania stan to **Wstrzymano,** a przycisk opcji to **Wznów.**
8. Zasoby obliczeniowe dla dedykowanej puli SQL są teraz w trybie offline. Opłata za obliczenia nie zostanie naliczona do czasu wznowienia usługi.

    ![Obliczenia w trybie offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Wznawianie obliczeń

Wykonaj następujące kroki, aby wznowić dedykowaną pulę SQL.

1. Przejdź do strony **Dedykowana pula SQL,** aby otworzyć pulę SQL.
3. Na stronie **mySampleDataWarehouse** zwróć uwagę, że **stan** to **Wstrzymano.**

    ![Obliczenia w trybie offline](././media/pause-and-resume-compute-portal/compute-offline.png)

1. Aby wznowić działanie puli SQL, kliknij pozycję **Wznów.** 
1. Zostanie wyświetlone pytanie z potwierdzeniem z pytaniem, czy chcesz rozpocząć. Kliknij przycisk **Yes** (Tak).
1. Zwróć **uwagę, że stan** **to Wznawianie.**

    ![Zrzut ekranu Azure Portal przykładowego magazynu danych z wybranym przyciskiem Uruchom i wartością Stan Wznawianie.](./media/pause-and-resume-compute-portal/resuming.png)

1. Gdy pula SQL powróci do trybu online, stan ma stan **Online,** a przycisk opcji to **Wstrzymaj**.
1. Zasoby obliczeniowe dla puli SQL są teraz w trybie online i można z nich korzystać. Opłaty za zasoby obliczeniowe zostały wznowione.

    ![Obliczenia w trybie online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w dedykowanej puli SQL. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno. 

- Jeśli chcesz przechowywać dane w magazynie, wstrzymaj obliczenia.
- Jeśli chcesz usunąć przyszłe opłaty, możesz usunąć dedykowaną pulę SQL. 

Wykonaj następujące kroki, aby wyczyścić zasoby zgodnie z potrzebami.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i wybierz dedykowaną pulę SQL.

    ![Czyszczenie zasobów](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Aby wstrzymać obliczenia, kliknij przycisk **Wstrzymaj**. 

1. Aby usunąć dedykowaną pulę SQL, aby nie były naliczane opłaty za zasoby obliczeniowe lub magazyn, kliknij pozycję **Usuń.**



## <a name="next-steps"></a>Następne kroki

Wstrzymano i wznowiono obliczenia dla dedykowanej puli SQL. Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat sposobu ładowania [danych do dedykowanej puli SQL.](./load-data-from-azure-blob-storage-using-copy.md) Aby uzyskać dodatkowe informacje na temat zarządzania możliwościami obliczeniowymi, zobacz [artykuł Manage compute overview (Omówienie zarządzania obliczeniami).](sql-data-warehouse-manage-compute-overview.md)
