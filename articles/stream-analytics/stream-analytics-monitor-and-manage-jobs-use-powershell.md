---
title: Monitorowanie zadań zarządzania Azure Stream Analytics za pomocą programu PowerShell
description: W tym artykule opisano sposób używania Azure PowerShell i polecenia cmdlet do monitorowania Azure Stream Analytics zadań.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/28/2017
ms.openlocfilehash: 15b2e5ef5873ea48c6c3f2c790619392f622fb66
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870143"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitorowanie zadań zarządzania Stream Analytics za pomocą Azure PowerShell cmdlet
Dowiedz się, jak monitorować zasoby Stream Analytics i zarządzać nimi za pomocą poleceń cmdlet programu Azure PowerShell i skryptów programu PowerShell, które wykonują podstawowe Stream Analytics zadań.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Wymagania wstępne dotyczące uruchamiania Azure PowerShell cmdlet dla Stream Analytics
* Utwórz grupę zasobów platformy Azure w ramach subskrypcji. Poniżej przedstawiono przykładowy skrypt Azure PowerShell skrypt. Aby uzyskać Azure PowerShell informacji, zobacz [Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/);  

Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1.0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription -SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Stream Analytics zadania utworzone programowo nie mają domyślnie włączonego monitorowania.  Monitorowanie można włączyć ręcznie w witrynie Azure Portal, przechodząc do strony Monitorowanie zadania i klikając przycisk Włącz. Możesz też to zrobić programowo, korzystając z procedury [Azure Stream Analytics](stream-analytics-monitor-jobs.md)— Programowe monitorowanie zadań Stream Analytics.
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell cmdlet dla Stream Analytics
Następujące polecenia Azure PowerShell cmdlet mogą służyć do monitorowania zadań Azure Stream Analytics zarządzania nimi. Należy pamiętać, Azure PowerShell ma różne wersje. 
**W przykładach wymienionych pierwsze polecenie jest dla Azure PowerShell 0.9.8, drugie polecenie jest dla Azure PowerShell 1.0.** Polecenia Azure PowerShell 1.0 zawsze będą mieć w poleceniu "Az".

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Wyświetla listę Stream Analytics zdefiniowanych w subskrypcji platformy Azure lub określonej grupie zasobów albo pobiera informacje o konkretnym zadaniu w grupie zasobów.

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob
```

To polecenie programu PowerShell zwraca informacje o wszystkich zadaniach Stream Analytics w subskrypcji platformy Azure.

**Przykład 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

To polecenie programu PowerShell zwraca informacje o wszystkich zadaniach Stream Analytics w grupie zasobów StreamAnalytics-Default-Central-US.

**Przykład 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

To polecenie programu PowerShell zwraca informacje o zadaniu Stream Analytics StreamingJob w grupie zasobów StreamAnalytics-Default-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Wyświetla listę wszystkich danych wejściowych, które są zdefiniowane w określonym Stream Analytics lub pobiera informacje o określonych danych wejściowych.

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

To polecenie programu PowerShell zwraca informacje o wszystkich danych wejściowych zdefiniowanych w zadaniu StreamingJob.

**Przykład 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

To polecenie programu PowerShell zwraca informacje o danych wejściowych o nazwie EntryStream zdefiniowanych w zadaniu StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Wyświetla listę wszystkich danych wyjściowych, które są zdefiniowane w określonym Stream Analytics lub pobiera informacje o określonych danych wyjściowych.

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

To polecenie programu PowerShell zwraca informacje o danych wyjściowych zdefiniowanych w zadaniu StreamingJob.

**Przykład 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

To polecenie programu PowerShell zwraca informacje o danych wyjściowych o nazwie Output zdefiniowanych w zadaniu StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Pobiera informacje o limitach przydziału jednostek przesyłania strumieniowego w określonym regionie.

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

To polecenie programu PowerShell zwraca informacje o limitach przydziału i użyciu jednostek przesyłania strumieniowego w regionie Środkowe usa.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Pobiera informacje o określonym przekształceniu zdefiniowanym w Stream Analytics zadania.

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

To polecenie programu PowerShell zwraca informacje o przekształceniu o nazwie StreamingJob w zadaniu StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Tworzy nowe dane wejściowe w ramach Stream Analytics lub aktualizuje istniejące określone dane wejściowe.

Nazwę danych wejściowych można określić w pliku json lub w wierszu polecenia. Jeśli są określone, nazwa w wierszu polecenia musi być taka sama jak w pliku.

Jeśli określisz dane wejściowe, które już istnieją i nie określisz parametru -Force, polecenie cmdlet zapyta, czy zastąpić istniejące dane wejściowe.

Jeśli określisz parametr -Force i określisz istniejącą nazwę wejściową, dane wejściowe zostaną zastąpione bez potwierdzenia.

Aby uzyskać szczegółowe informacje na temat struktury i zawartości pliku JSON, zapoznaj się z sekcją Tworzenie danych wejściowych [(Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] w bibliotece referencyjnej interfejsu API REST usługi [Stream Analytics Management.][stream.analytics.rest.api.reference]

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

To polecenie programu PowerShell tworzy nowe dane wejściowe z pliku, Input.jsna. Jeśli istniejące dane wejściowe o nazwie określonej w pliku definicji danych wejściowych są już zdefiniowane, polecenie cmdlet zapyta, czy ma je zastąpić.

**Przykład 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

To polecenie programu PowerShell tworzy nowe dane wejściowe w zadaniu o nazwie EntryStream. Jeśli istniejące dane wejściowe o tej nazwie są już zdefiniowane, polecenie cmdlet zapyta, czy je zastąpić.

**Przykład 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

To polecenie programu PowerShell zastępuje definicję istniejącego źródła danych wejściowych o nazwie EntryStream definicją z pliku.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Tworzy nowe zadanie Stream Analytics w Microsoft Azure lub aktualizuje definicję istniejącego określonego zadania.

Nazwę zadania można określić w pliku json lub w wierszu polecenia. Jeśli są określone, nazwa w wierszu polecenia musi być taka sama jak w pliku.

Jeśli określisz nazwę zadania, która już istnieje i nie określisz parametru -Force, polecenie cmdlet zapyta, czy zastąpić istniejące zadanie.

Jeśli określisz parametr -Force i określisz istniejącą nazwę zadania, definicja zadania zostanie zastąpiona bez potwierdzenia.

Aby uzyskać szczegółowe informacje na temat struktury i zawartości pliku JSON, zapoznaj się z sekcją [Tworzenie][msdn-rest-api-create-stream-analytics-job] zadania Stream Analytics w bibliotece referencyjnej interfejsu API REST usługi [Stream Analytics Management.][stream.analytics.rest.api.reference]

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

To polecenie programu PowerShell tworzy nowe zadanie na podstawie definicji w JobDefinition.jsna. Jeśli istniejące zadanie o nazwie określonej w pliku definicji zadania jest już zdefiniowane, polecenie cmdlet zapyta, czy je zastąpić.

**Przykład 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

To polecenie programu PowerShell zastępuje definicję zadania StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Tworzy nowe dane wyjściowe w ramach Stream Analytics lub aktualizuje istniejące dane wyjściowe.  

Nazwę danych wyjściowych można określić w pliku json lub w wierszu polecenia. Jeśli są określone, nazwa w wierszu polecenia musi być taka sama jak w pliku.

Jeśli określisz dane wyjściowe, które już istnieją i nie określisz parametru -Force, polecenie cmdlet zapyta, czy zastąpić istniejące dane wyjściowe.

Jeśli określisz parametr -Force i określisz istniejącą nazwę danych wyjściowych, dane wyjściowe zostaną zastąpione bez potwierdzenia.

Aby uzyskać szczegółowe informacje na temat struktury i zawartości plików JSON, zapoznaj się z sekcją Tworzenie danych wyjściowych [(Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] w bibliotece referencyjnej interfejsu API REST usługi [Stream Analytics Management.][stream.analytics.rest.api.reference]

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

To polecenie programu PowerShell tworzy nowe dane wyjściowe o nazwie "output" w zadaniu StreamingJob. Jeśli istniejące dane wyjściowe o tej nazwie są już zdefiniowane, polecenie cmdlet zapyta, czy je zastąpić.

**Przykład 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

To polecenie programu PowerShell zastępuje definicję "output" w zadaniu StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Tworzy nowe przekształcenie w ramach Stream Analytics lub aktualizuje istniejące przekształcenie.

Nazwę przekształcenia można określić w pliku json lub w wierszu polecenia. Jeśli są określone, nazwa w wierszu polecenia musi być taka sama jak w pliku.

Jeśli określisz przekształcenie, które już istnieje, i nie określisz parametru -Force, polecenie cmdlet zapyta, czy zastąpić istniejące przekształcenie.

Jeśli określisz parametr -Force i określisz istniejącą nazwę przekształcenia, przekształcenie zostanie zastąpione bez potwierdzenia.

Aby uzyskać szczegółowe informacje na temat struktury i zawartości plików JSON, zapoznaj się z sekcją Tworzenie przekształcenia [(Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] w bibliotece referencyjnej interfejsu API REST usługi [Stream Analytics Management.][stream.analytics.rest.api.reference]

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

To polecenie programu PowerShell tworzy nowe przekształcenie o nazwie StreamingJobTransform w zadaniu StreamingJob. Jeśli istniejące przekształcenie jest już zdefiniowane przy użyciu tej nazwy, polecenie cmdlet zapyta, czy ma je zastąpić.

**Przykład 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 To polecenie programu PowerShell zastępuje definicję streamingJobTransform w zadaniu StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Asynchronicznie usuwa określone dane wejściowe z zadania Stream Analytics w Microsoft Azure.  
W przypadku określenia parametru -Force dane wejściowe zostaną usunięte bez potwierdzenia.

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

To polecenie programu PowerShell usuwa wejście EventStream w zadaniu StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Asynchronicznie usuwa określone zadanie Stream Analytics w Microsoft Azure.  
W przypadku określenia parametru -Force zadanie zostanie usunięte bez potwierdzenia.

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

To polecenie programu PowerShell usuwa zadanie StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Asynchronicznie usuwa określone dane wyjściowe z zadania Stream Analytics w Microsoft Azure.  
W przypadku określenia parametru -Force dane wyjściowe zostaną usunięte bez potwierdzenia.

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

To polecenie programu PowerShell usuwa dane wyjściowe w zadaniu StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Asynchronicznie wdraża i uruchamia zadanie Stream Analytics w Microsoft Azure.

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

To polecenie programu PowerShell uruchamia zadanie StreamingJob z niestandardowym czasem rozpoczęcia danych wyjściowych ustawionym na 12 grudnia 2012 r., 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Asynchronicznie zatrzymuje zadanie Stream Analytics uruchomione w Microsoft Azure i conajmuje zasoby, które były używane. Definicje i metadane zadania pozostaną dostępne w ramach subskrypcji za pośrednictwem interfejsów API Azure Portal i zarządzania, tak aby zadanie można było edytować i uruchamiać ponownie. Nie zostaną naliczone opłaty za zadanie w stanie zatrzymania.

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

To polecenie programu PowerShell zatrzymuje zadanie StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Testuje możliwość nawiązywania Stream Analytics z określonymi wejściami.

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

To polecenie programu PowerShell sprawdza stan połączenia wejściowego EntryStream w za pomocą polecenia StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Testuje możliwość łączenia Stream Analytics z określonymi danych wyjściowych.

**Przykład 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

To polecenie programu PowerShell sprawdza stan połączenia danych wyjściowych w za pomocą polecenia StreamingJob.  

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej
Aby uzyskać dalszą pomoc, wypróbuj naszą stronę pytania pytania&[Microsoft Q,](/answers/topics/azure-stream-analytics.html)aby uzyskać Azure Stream Analytics . 

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)

[msdn-switch-azuremode]: /previous-versions/azure/dn722470(v=azure.100)
[powershell-install]: /powershell/azure/
[msdn-rest-api-create-stream-analytics-job]: ./stream-analytics-quick-create-portal.md
[msdn-rest-api-create-stream-analytics-input]: ./stream-analytics-define-inputs.md
[msdn-rest-api-create-stream-analytics-output]: ./stream-analytics-define-outputs.md
[msdn-rest-api-create-stream-analytics-transformation]: /cli/azure/stream-analytics/transformation

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/