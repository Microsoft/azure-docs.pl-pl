---
title: 'Szybki start: wstrzymywanie i wznawianie obliczeń w dedykowanej puli SQL za pośrednictwem Azure Portal'
description: Użyj tej Azure Portal, aby wstrzymać obliczenia dla dedykowanej puli SQL, aby zaoszczędzić koszty. Wznawianie obliczeń, gdy wszystko będzie gotowe do korzystania z magazynu danych.
services: synapse-analytics
author: julieMSFT
ms.author: jrasnick
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
ms.openlocfilehash: 27587f436013bcaccbacd668ce62f1d939f89e48
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566821"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-via-the-azure-portal"></a>Szybki start: wstrzymywanie i wznawianie obliczeń w dedykowanej puli SQL za pośrednictwem Azure Portal

Za pomocą tej Azure Portal wstrzymywać i wznawiać dedykowane zasoby obliczeniowe puli SQL. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Użyj [polecenia Utwórz i połącz — portal,](../quickstart-create-sql-pool-portal.md) aby utworzyć dedykowaną pulę SQL o nazwie **mySampleDataWarehouse.** 

## <a name="pause-compute"></a>Wstrzymywanie obliczeń

Aby zmniejszyć koszty, możesz wstrzymać i wznowić zasoby obliczeniowe na żądanie. Jeśli na przykład nie będziesz używać bazy danych w nocy i w weekendy, możesz ją wstrzymać w tych godzinach i wznowić ją w ciągu dnia.
 
>[!NOTE]
>Opłaty za zasoby obliczeniowe nie będą naliczane, gdy baza danych jest wstrzymana. Jednak nadal będą naliczane opłaty za magazyn. 

Wykonaj następujące kroki, aby wstrzymać dedykowaną pulę SQL:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do strony **Dedykowana pula SQL,** aby otworzyć pulę SQL. 
3. Zwróć **uwagę, że** stan to **Online.**

    ![Obliczenia w trybie online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Aby wstrzymać dedykowaną pulę SQL, kliknij przycisk **Wstrzymaj.** 
5. Zostanie wyświetlone pytanie z potwierdzeniem z pytaniem, czy chcesz kontynuować. Kliknij przycisk **Yes** (Tak).
6. Zaczekaj chwilę, a następnie zwróć uwagę, że **stan** ma stan **Wsad.**

    ![Zrzut ekranu Azure Portal przykładowego magazynu danych z wartością Stan wsadu.](./media/pause-and-resume-compute-portal/pausing.png)

7. Po zakończeniu operacji wstrzymania stan to **Wstrzymano,** a przycisk opcji to **Wznów.**
8. Zasoby obliczeniowe dla dedykowanej puli SQL są teraz w trybie offline. Opłaty za zasoby obliczeniowe nie zostaną naliczone, dopóki usługa nie zostanie wznowiona.

    ![Obliczenia w trybie offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Wznawianie obliczeń

Wykonaj następujące kroki, aby wznowić dedykowaną pulę SQL.

1. Przejdź do strony **Dedykowana pula SQL,** aby otworzyć pulę SQL.
3. Na **stronie mySampleDataWarehouse** zwróć uwagę, że **stan** to **Wstrzymano.**

    ![Obliczenia w trybie offline](././media/pause-and-resume-compute-portal/compute-offline.png)

1. Aby wznowić działanie puli SQL, kliknij pozycję **Wznów.** 
1. Zostanie wyświetlone pytanie z potwierdzeniem z pytaniem, czy chcesz rozpocząć. Kliknij przycisk **Yes** (Tak).
1. Zwróć **uwagę, że stan** **to Wznawianie.**

    ![Zrzut ekranu Azure Portal przykładowego magazynu danych z wybranym przyciskiem Uruchom i wartością Stan Wznawianie.](./media/pause-and-resume-compute-portal/resuming.png)

1. Gdy pula SQL jest ponownie w trybie online, stan ma stan **Online,** a przycisk opcji to **Wstrzymaj**.
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

1. Aby usunąć dedykowaną pulę SQL, aby nie ponosić opłat za zasoby obliczeniowe lub magazynowe, kliknij pozycję **Usuń.**



## <a name="next-steps"></a>Następne kroki

Wstrzymano i wznowiono obliczenia dla dedykowanej puli SQL. Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat sposobu ładowania [danych do dedykowanej puli SQL.](./load-data-from-azure-blob-storage-using-copy.md) Aby uzyskać dodatkowe informacje na temat zarządzania możliwościami obliczeniowymi, zobacz [artykuł Manage compute overview (Omówienie zarządzania obliczeniami).](sql-data-warehouse-manage-compute-overview.md)
