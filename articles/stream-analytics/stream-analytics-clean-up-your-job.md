---
title: Czyszczenie zadania Azure Stream Analytics
description: W tym artykule przedstawiono różne metody usuwania zadań Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 124d2cf4380153bd3ea68de9233f6a1b5eb6ba78
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130701"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Zatrzymywanie lub usuwanie zadania Azure Stream Analytics

Zadania Azure Stream Analytics można łatwo zatrzymać lub usunąć za pomocą Azure Portal, Azure PowerShell, zestawu Azure SDK dla platformy .NET lub interfejsu API REST. Nie można odzyskać zadania Stream Analytics po jego usunięciu.

>[!NOTE] 
>Po zatrzymaniu zadania Stream Analytics dane będą przechowywane tylko w magazynie danych wejściowych i wyjściowych, takich jak Event Hubs lub Azure SQL Database. Jeśli konieczne jest usunięcie danych z platformy Azure, należy postępować zgodnie z procesem usuwania dla zasobów wejściowych i wyjściowych zadania Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Zatrzymaj zadanie w Azure Portal

Po zatrzymaniu zadania zasoby są cofane i zatrzymują przetwarzanie zdarzeń. Opłaty związane z tym zadaniem również są zatrzymane. Jednak cała konfiguracja jest zachowywana, a zadanie można uruchomić ponownie później 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Znajdź uruchomione zadanie Stream Analytics i wybierz je.

3. Na stronie zadanie Stream Analytics wybierz pozycję **Zatrzymaj** , aby zatrzymać zadanie. 

   ![Zatrzymaj zadanie Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Usuwanie zadania w Azure Portal

>[!WARNING] 
>Nie można odzyskać zadania Stream Analytics po jego usunięciu.

1. Zaloguj się w witrynie Azure Portal. 

2. Znajdź istniejące zadanie Stream Analytics i wybierz je.

3. Na stronie Stream Analytics zadania wybierz pozycję **Usuń** , aby usunąć zadanie. 

   ![Usuwanie zadania Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Zatrzymywanie lub usuwanie zadania przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby zatrzymać zadanie przy użyciu programu PowerShell, użyj polecenia cmdlet [stop-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) . Aby usunąć zadanie przy użyciu programu PowerShell, użyj polecenia cmdlet [Remove-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) .

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Zatrzymywanie lub usuwanie zadania przy użyciu zestawu Azure SDK dla platformy .NET

Aby zatrzymać zadanie przy użyciu zestawu Azure SDK dla platformy .NET, należy użyć metody [StreamingJobsOperationsExtensions. BeginStop](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) . Aby usunąć zadanie przy użyciu zestawu Azure SDK dla platformy .NET, metody [StreamingJobsOperationsExtensions. BeginDelete](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) .

## <a name="stop-or-delete-a-job-using-rest-api"></a>Zatrzymywanie lub usuwanie zadania przy użyciu interfejsu API REST

Aby zatrzymać zadanie przy użyciu interfejsu API REST, zapoznaj się z [zatrzymywaniem](/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob#stop) metody. Aby usunąć zadanie przy użyciu interfejsu API REST, zobacz metodę [delete](/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob#delete) .