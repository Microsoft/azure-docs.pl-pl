---
title: Tworzenie & kwerendy Azure Data Lake Analytics — PowerShell
description: Tworzenie konta usługi Data Lake Analytics i przesyłanie zadania w języku U-SQL przy użyciu programu Azure PowerShell.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/04/2017
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 93a05231bc971737a08d74ad04150e5449dfc792
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220945"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Wprowadzenie do pracy z usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Dowiedz się, jak za pomocą programu Azure PowerShell tworzyć konta usługi Azure Data Lake Analytics, a następnie przesyłać i uruchamiać zadania U-SQL. Aby uzyskać więcej informacji na temat Data Lake Analytics, zobacz [Azure Data Lake Analytics Omówienie](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku musisz mieć poniższe:

* **Konto usługi Azure Data Lake Analytics**. Zobacz [Wprowadzenie do usługi Data Lake Analytics](./data-lake-analytics-get-started-portal.md).
* **Stacja robocza z programem Azure PowerShell**. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

W tym samouczku założono, że użytkownik wie już, jak korzystać z programu Azure PowerShell. W szczególności musi wiedzieć, jak zalogować się do platformy Azure. Jeśli potrzebujesz dodatkowych informacji zobacz temat [Rozpoczynanie pracy z programem Azure PowerShell](/powershell/azure/get-started-azureps).

Aby zalogować się przy użyciu nazwy subskrypcji:

```powershell
Connect-AzAccount -SubscriptionName "ContosoSubscription"
```

Podczas logowania zamiast nazwy subskrypcji można także użyć identyfikatora subskrypcji:

```powershell
Connect-AzAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

Jeśli to się powiedzie, dane wyjściowe tego polecenia mają mniej więcej taką formę:

```text
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>Przygotowanie do samouczka

We fragmentach kodu programu PowerShell w ramach tego samouczka do przechowywania tych informacji są używane następujące zmienne:

```powershell
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Uzyskiwanie informacji o koncie usługi Data Lake Analytics

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Przesyłanie zadania U-SQL

Utwórz zmienną środowiska PowerShell do przechowywania skryptu U-SQL.

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();

"@
```

Prześlij tekst skryptu przy użyciu polecenia cmdlet `Submit-AdlJob` i parametru `-Script`.

```powershell
$job = Submit-AdlJob -Account $adla -Name "My Job" -Script $script
```

Alternatywnie możesz przesłać plik skryptu przy użyciu parametru `-ScriptPath`:

```powershell
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" -ScriptPath $filename
```

Pobierz stan zadania przy użyciu polecenia `Get-AdlJob`. 

```powershell
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

Zamiast wielokrotnie wykonywać polecenie Get-AdlJob do momentu zakończenia zadania, użyj polecenia cmdlet `Wait-AdlJob`.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Pobierz plik wyjściowy przy użyciu polecenia `Export-AdlStoreItem`.

```powershell
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>Zobacz też

* Aby wyświetlić ten samouczek przy użyciu innych narzędzi, kliknij odpowiedni selektor karty w górnej części strony.
* Aby dowiedzieć się więcej o języku U-SQL, zobacz [Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Informacje o zadaniach zarządzania znajdziesz w artykule [Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal](data-lake-analytics-manage-use-portal.md).