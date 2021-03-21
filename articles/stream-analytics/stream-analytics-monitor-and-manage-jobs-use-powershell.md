---
title: Monitorowanie zadań Azure Stream Analytics i zarządzanie nimi za pomocą programu PowerShell
description: W tym artykule opisano sposób używania Azure PowerShell i poleceń cmdlet do monitorowania zadań Azure Stream Analytics i zarządzania nimi.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/28/2017
ms.openlocfilehash: 6b404516c513dea0888974ffb4fa3d8d43db6c44
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015186"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitoruj Stream Analytics zadania i zarządzaj nimi za pomocą poleceń cmdlet Azure PowerShell
Dowiedz się, jak monitorować zasoby Stream Analytics i zarządzać nimi za pomocą poleceń cmdlet Azure PowerShell i skryptów programu PowerShell, które wykonują podstawowe Stream Analyticse zadania.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Wymagania wstępne dotyczące uruchamiania Azure PowerShell poleceń cmdlet dla Stream Analytics
* Utwórz grupę zasobów platformy Azure w ramach subskrypcji. Poniżej znajduje się przykładowy skrypt Azure PowerShell. Aby uzyskać Azure PowerShell informacji, zobacz [Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/);  

0.9.8 Azure PowerShell:  

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

Azure PowerShell 1,0:

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
> Zadania Stream Analytics utworzone programowo nie mają domyślnie włączonego monitorowania.  Monitorowanie można włączyć ręcznie w witrynie Azure Portal, przechodząc do strony monitorowanie zadania i klikając przycisk Włącz. można to zrobić programowo, wykonując kroki opisane w sekcji [Azure Stream Analytics — Monitoruj zadania Stream Analytics](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Polecenia cmdlet Azure PowerShell dla Stream Analytics
Następujące polecenia cmdlet Azure PowerShell mogą służyć do monitorowania zadań Azure Stream Analytics i zarządzania nimi. Należy pamiętać, że Azure PowerShell mają różne wersje. 
**W przykładach wymienionych po pierwszym poleceniu jest dla Azure PowerShell 0.9.8, drugie polecenie jest dla Azure PowerShell 1,0.** Polecenia Azure PowerShell 1,0 zawsze będą mieć "AZ" w poleceniu.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Wyświetla listę wszystkich zadań Stream Analytics zdefiniowanych w ramach subskrypcji platformy Azure lub określonej grupy zasobów lub pobiera informacje o zadaniach dotyczących określonego zadania w grupie zasobów.

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob
```

To polecenie programu PowerShell zwraca informacje o wszystkich zadaniach Stream Analytics w ramach subskrypcji platformy Azure.

**Przykład 2**

0.9.8 Azure PowerShell:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

To polecenie programu PowerShell zwraca informacje o wszystkich zadaniach Stream Analytics w grupie zasobów StreamAnalytics — domyślnie — środkowe stany USA.

**Przykład 3**

0.9.8 Azure PowerShell:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

To polecenie programu PowerShell zwraca informacje dotyczące zadania Stream Analytics StreamingJob w grupie zasobów StreamAnalytics — domyślne-środkowe stany USA.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Wyświetla wszystkie dane wejściowe, które są zdefiniowane w określonym Stream Analytics zadania lub pobiera informacje o konkretnym wejściu.

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

To polecenie programu PowerShell zwraca informacje o wszystkich danych wejściowych zdefiniowanych w StreamingJob zadań.

**Przykład 2**

0.9.8 Azure PowerShell:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

To polecenie programu PowerShell zwraca informacje o danych wejściowych o nazwie EntryStream zdefiniowanej w StreamingJob zadania.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Wyświetla listę wszystkich danych wyjściowych, które są zdefiniowane w określonym Stream Analytics zadania, lub pobiera informacje o konkretnym wyjściu.

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

To polecenie programu PowerShell zwraca informacje dotyczące danych wyjściowych zdefiniowanych w StreamingJob zadań.

**Przykład 2**

0.9.8 Azure PowerShell:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

To polecenie programu PowerShell zwraca informacje o danych wyjściowych o nazwie dane wyjściowe zdefiniowane w StreamingJob zadania.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Pobiera informacje o przydziale jednostek przesyłania strumieniowego w określonym regionie.

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

To polecenie programu PowerShell zwraca informacje o przydziale i użyciu jednostek przesyłania strumieniowego w regionie centralnym USA.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Pobiera informacje o konkretnym przekształceniu zdefiniowanym w zadaniu Stream Analytics.

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

To polecenie programu PowerShell zwraca informacje o przekształceniu o nazwie StreamingJob w StreamingJob zadania.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Tworzy nowe dane wejściowe w ramach zadania Stream Analytics lub aktualizuje istniejące dane wejściowe.

Nazwę danych wejściowych można określić w pliku JSON lub w wierszu polecenia. Jeśli oba są określone, nazwa w wierszu polecenia musi być taka sama jak w pliku.

Jeśli określisz dane wejściowe, które już istnieją i nie określisz parametru-Force, polecenie cmdlet wyświetli monit z pytaniem, czy zastąpić istniejące dane wejściowe.

Jeśli określisz parametr-Force i określisz istniejącą nazwę wejściową, dane wejściowe zostaną zastąpione bez potwierdzenia.

Aby uzyskać szczegółowe informacje na temat struktury i zawartości pliku JSON, zapoznaj się z sekcją [Tworzenie danych wejściowych (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] w [bibliotece dokumentacja interfejsu API REST zarządzania Stream Analytics][stream.analytics.rest.api.reference].

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

To polecenie programu PowerShell tworzy nowe dane wejściowe z pliku Input.jsna. Jeśli istniejące dane wejściowe o nazwie określonej w pliku definicji wejściowej są już zdefiniowane, polecenie cmdlet wyświetli monit z pytaniem, czy zastąpić go.

**Przykład 2**

0.9.8 Azure PowerShell:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

To polecenie programu PowerShell tworzy nowe dane wejściowe w zadaniu o nazwie EntryStream. Jeśli istniejące dane wejściowe o tej nazwie są już zdefiniowane, polecenie cmdlet wyświetli monit z pytaniem, czy zastąpić go.

**Przykład 3**

0.9.8 Azure PowerShell:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

To polecenie programu PowerShell zastępuje definicję istniejącego źródła danych wejściowych o nazwie EntryStream z definicją z pliku.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Tworzy nowe zadanie Stream Analytics w programie Microsoft Azure lub aktualizuje definicję istniejącego określonego zadania.

Nazwę zadania można określić w pliku JSON lub w wierszu polecenia. Jeśli oba są określone, nazwa w wierszu polecenia musi być taka sama jak w pliku.

Jeśli określisz nazwę zadania, która już istnieje i nie określisz parametru-Force, polecenie cmdlet wyświetli monit z pytaniem, czy zastąpić istniejące zadanie.

Jeśli określisz parametr-Force i określisz istniejącą nazwę zadania, definicja zadania zostanie zastąpiona bez potwierdzenia.

Aby uzyskać szczegółowe informacje na temat struktury i zawartości pliku JSON, zapoznaj się z sekcją [tworzenie Stream Analytics zadania][msdn-rest-api-create-stream-analytics-job] w [bibliotece dokumentacja interfejsu API REST zarządzania Stream Analytics][stream.analytics.rest.api.reference].

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

To polecenie programu PowerShell tworzy nowe zadanie z definicji w JobDefinition.json. Jeśli istniejące zadanie o nazwie określonej w pliku definicji zadania jest już zdefiniowane, polecenie cmdlet wyświetli komunikat z pytaniem, czy zastąpić go.

**Przykład 2**

0.9.8 Azure PowerShell:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

To polecenie programu PowerShell zastępuje definicję zadania dla StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Tworzy nowe dane wyjściowe w ramach zadania Stream Analytics lub aktualizuje istniejące dane wyjściowe.  

Nazwę danych wyjściowych można określić w pliku JSON lub w wierszu polecenia. Jeśli oba są określone, nazwa w wierszu polecenia musi być taka sama jak w pliku.

Jeśli określisz dane wyjściowe, które już istnieją i nie określisz parametru-Force, polecenie cmdlet wyświetli monit z pytaniem, czy zastąpić istniejące dane wyjściowe.

Jeśli określisz parametr-Force i określisz istniejącą nazwę wyjściową, dane wyjściowe zostaną zastąpione bez potwierdzenia.

Aby uzyskać szczegółowe informacje na temat struktury i zawartości pliku JSON, zapoznaj się z sekcją [Tworzenie danych wyjściowych (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] w [bibliotece referencyjnej interfejsu API REST zarządzania Stream Analytics][stream.analytics.rest.api.reference].

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

To polecenie programu PowerShell tworzy nowe dane wyjściowe o nazwie "output" w StreamingJob zadania. Jeśli istniejące dane wyjściowe o tej nazwie są już zdefiniowane, polecenie cmdlet wyświetli komunikat z pytaniem, czy zastąpić go.

**Przykład 2**

0.9.8 Azure PowerShell:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

To polecenie programu PowerShell zastępuje definicję elementu "output" w StreamingJob zadania.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Tworzy nową transformację w ramach zadania Stream Analytics lub aktualizuje istniejącą transformację.

Nazwę przekształcenia można określić w pliku JSON lub w wierszu polecenia. Jeśli oba są określone, nazwa w wierszu polecenia musi być taka sama jak w pliku.

Jeśli określisz transformację, która już istnieje, i nie określisz parametru-Force, polecenie cmdlet wyświetli monit z pytaniem, czy zastąpić istniejące przekształcenie.

Jeśli określisz parametr-Force i określisz istniejącą nazwę przekształcenia, transformacja zostanie zamieniona bez potwierdzenia.

Aby uzyskać szczegółowe informacje na temat struktury i zawartości pliku JSON, zapoznaj się z sekcją [Tworzenie transformacji (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] w [bibliotece referencyjnej interfejsu API REST zarządzania Stream Analytics][stream.analytics.rest.api.reference].

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

To polecenie programu PowerShell tworzy nowe przekształcenie o nazwie StreamingJobTransform w StreamingJob zadania. Jeśli istniejące przekształcenie jest już zdefiniowane przy użyciu tej nazwy, polecenie cmdlet wyświetli pytanie, czy ma zostać zastąpione.

**Przykład 2**

0.9.8 Azure PowerShell:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 To polecenie programu PowerShell zastępuje definicję elementu StreamingJobTransform w StreamingJob zadania.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Asynchronicznie usuwa określone dane wejściowe z zadania Stream Analytics w Microsoft Azure.  
W przypadku określenia parametru-Force dane wejściowe zostaną usunięte bez potwierdzenia.

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

To polecenie programu PowerShell usuwa EventStream wejścia w StreamingJob zadania.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Asynchronicznie usuwa określone zadanie Stream Analytics w Microsoft Azure.  
W przypadku określenia parametru-Force zadanie zostanie usunięte bez potwierdzenia.

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

To polecenie programu PowerShell usuwa StreamingJob zadania.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Asynchronicznie usuwa określone dane wyjściowe z zadania Stream Analytics w Microsoft Azure.  
W przypadku określenia parametru-Force dane wyjściowe zostaną usunięte bez potwierdzenia.

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

To polecenie programu PowerShell usuwa wyjściowe dane wyjściowe w StreamingJob zadania.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Asynchronicznie wdraża i uruchamia zadanie Stream Analytics w Microsoft Azure.

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1,0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

To polecenie programu PowerShell uruchamia zadanie StreamingJob z niestandardowym czasem rozpoczęcia wyjściowego ustawionym na 12 grudnia 2012, 12:12:12 czasu UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Asynchronicznie uniemożliwia wykonywanie zadania Stream Analytics w programie Microsoft Azure i cofa alokowanie zasobów, które były używane. Definicja zadania i metadane pozostaną dostępne w ramach subskrypcji za pomocą interfejsów API Azure Portal i zarządzania, aby można było edytować i ponownie uruchomić zadanie. Nie zostanie naliczona opłata za zadanie w stanie zatrzymanym.

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1,0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

To polecenie programu PowerShell powoduje zatrzymanie zadania StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Testuje zdolność Stream Analytics do nawiązywania połączeń z określonym danymi wejściowymi.

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

To polecenie programu PowerShell sprawdza stan połączenia danych wejściowych EntryStream w StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Testuje zdolność Stream Analytics do nawiązywania połączenia z określonymi danymi wyjściowymi.

**Przykład 1**

0.9.8 Azure PowerShell:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1,0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

To polecenie programu PowerShell testuje stan połączenia wyjściowe wyjściowe w StreamingJob.  

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej
Aby uzyskać dalszą pomoc, Wypróbuj naszą [stronę pytań firmy&Microsoft dotyczącą Azure Stream Analytics](/answers/topics/azure-stream-analytics.html). 

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
[msdn-rest-api-create-stream-analytics-transformation]: /cli/azure/ext/stream-analytics/stream-analytics/transformation

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/