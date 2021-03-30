---
title: Monitorowanie Azure Data Lake Analytics — Azure Portal
description: W tym artykule opisano sposób korzystania z Azure Portal w celu rozwiązywania problemów z zadaniami Azure Data Lake Analytics.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 12/05/2016
ms.openlocfilehash: ddcf41a333d261bd9c5f669fde724a25eeba670e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220282"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Monitor jobs in Azure Data Lake Analytics using the Azure Portal (Monitorowanie zadań w usłudze Azure Data Lake Analytics przy użyciu witryny Azure Portal)

## <a name="to-see-all-the-jobs"></a>Aby wyświetlić wszystkie zadania

1. W Azure Portal kliknij pozycję **Microsoft Azure** w lewym górnym rogu.

2. Kliknij kafelek z nazwą konta usługi Data Lake Analytics.  Podsumowanie zadania jest wyświetlane na kafelku **Zarządzanie zadaniami** .

   ![Azure Data Lake Analytics zarządzanie zadaniami](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Zarządzanie zadaniami umożliwia szybkie wyświetlenie stanu zadania. Zwróć uwagę na to, że zadanie nie powiodło się.
3. Kliknij kafelek **Zarządzanie zadaniami** , aby wyświetlić zadania. Zadania są pogrupowane w **uruchomione**, **kolejkowane** i **zakończone**. Zadanie zakończone niepowodzeniem jest wyświetlane w sekcji **zakończenie** . Musi ona znajdować się na liście. W przypadku dużej liczby zadań można kliknąć pozycję **Filtr** , aby ułatwić lokalizowanie zadań.

   ![Zadania filtrowania Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)

4. Kliknij zadanie zakończone niepowodzeniem z listy, aby otworzyć Szczegóły zadania:

   ![Zadanie zakończone niepowodzeniem Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Zwróć uwagę na przycisk **Prześlij ponownie** . Po rozwiązaniu problemu można ponownie przesłać zadanie.

5. Kliknij pozycję wyróżniona część na poprzednim zrzucie ekranu, aby otworzyć Szczegóły błędu.  Zobaczysz coś podobnego do:

   ![Szczegóły zadania zakończonego niepowodzeniem Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

   Informuje o tym, że folder źródłowy nie został znaleziony.

6. Kliknij pozycję **Duplikuj skrypt**.

7. Zaktualizuj ścieżkę **ze** ścieżki do: `/Samples/Data/SearchLog.tsv`

8. Kliknij przycisk **Prześlij zadanie**.

## <a name="next-steps"></a>Następne kroki

* [Przegląd Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Wprowadzenie do pracy z usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Zarządzanie Azure Data Lake Analytics przy użyciu Azure Portal](data-lake-analytics-manage-use-portal.md)
