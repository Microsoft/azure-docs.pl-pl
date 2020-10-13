---
title: 'ML Studio (klasyczny): Tworzenie wielu & punktów końcowych modelu — platforma Azure'
description: Użyj programu PowerShell, aby utworzyć wiele modeli Machine Learning i punkty końcowe usługi sieci Web przy użyciu tego samego algorytmu, ale różnych zestawów danych szkoleniowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: ab14547ef5d9791728ce96fdf2c414945a46aab9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362490"
---
# <a name="create-multiple-web-service-endpoints-from-one-experiment-with-ml-studio-classic-and-powershell"></a>Tworzenie wielu punktów końcowych usługi sieci Web na podstawie jednego eksperymentu z ML Studio (klasyczny) i programu PowerShell

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  

Oto typowy problem uczenia maszynowego: chcesz utworzyć wiele modeli, które mają ten sam przepływ pracy szkoleniowej i korzystać z tego samego algorytmu. Ale chcesz, aby miały różne zestawy danych szkoleniowych jako dane wejściowe. W tym artykule pokazano, jak to zrobić na dużą skalę w Azure Machine Learning Studio (klasyczny) przy użyciu tylko jednego eksperymentu.

Załóżmy na przykład, że jesteś własnością firmy franchisingowej. Chcesz skompilować model regresji, aby przewidzieć zapotrzebowanie na wypożyczenie w oparciu o historyczne dane. Masz 1 000 lokalizacji dzierżawy na całym świecie i zebrano zestaw danych dla każdej lokalizacji. Obejmują one ważne funkcje, takie jak data, czas, Pogoda i ruch, które są specyficzne dla każdej lokalizacji.

Możesz nauczyć model, używając Scalonej wersji wszystkich zestawów danych we wszystkich lokalizacjach. Jednak każda lokalizacja ma unikatowe środowisko. Dlatego lepszym rozwiązaniem będzie uczenie modelu regresji oddzielnie przy użyciu zestawu danych dla każdej lokalizacji. W ten sposób każdy szkolony model może wziąć pod uwagę różne rozmiary sklepu, głośność, populację, otoczenie i wiele innych danych.

To rozwiązanie może być najlepszym rozwiązaniem, ale nie chcesz tworzyć 1 000 eksperymentów szkoleniowych w Azure Machine Learning Studio (klasyczny) z każdą z nich reprezentującą unikatową lokalizację. Oprócz przeciążania zadania wydaje się również niewydajne, ponieważ każdy eksperyment będzie miał wszystkie te same składniki, z wyjątkiem zestawu danych szkoleniowych.

Na szczęście można to zrobić za pomocą [interfejsu API ponownego szkolenia Azure Machine Learning Studio (klasycznego)](/azure/machine-learning/studio/retrain-machine-learning-model) i automatyzując zadanie przy użyciu [Azure Machine Learning Studio (klasycznego) programu PowerShell](powershell-module.md).

> [!NOTE]
> Aby przyspieszyć działanie przykładu, zmniejsz liczbę lokalizacji z przedziału od 1 000 do 10. Jednak te same zasady i procedury mają zastosowanie do 1 000 lokalizacji. Jeśli jednak chcesz przeprowadzić uczenie z zestawów danych 1 000, możesz uruchomić równolegle poniższe skrypty programu PowerShell. Jak to zrobić, wykraczające poza zakres tego artykułu, ale można znaleźć przykłady użycia wielowątkowego programu PowerShell w Internecie.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Konfigurowanie eksperymentu szkoleniowego
Skorzystaj z przykładowego [eksperymentu szkoleniowego](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) , który znajduje się w [Cortana Intelligence Gallery](https://gallery.azure.ai). Otwórz ten eksperyment w obszarze roboczym [Azure Machine Learning Studio (klasycznym)](https://studio.azureml.net) .

> [!NOTE]
> Aby wykonać czynności opisane w tym przykładzie, możesz chcieć użyć standardowego obszaru roboczego, a nie obszaru roboczego. Należy utworzyć jeden punkt końcowy dla każdego klienta — w sumie 10 punktów końcowych, który wymaga obszaru roboczego w warstwie Standardowa, ponieważ bezpłatny obszar roboczy jest ograniczony do 3 punktów końcowych.
> 
> 

Eksperyment używa modułu **Import danych** do importowania zestawu danych szkoleniowych *customer001.csv* z konta usługi Azure Storage. Załóżmy, że zebrano zbiory danych szkoleń ze wszystkich lokalizacji dzierżawy rowerów i zapisano je w tej samej lokalizacji magazynu obiektów blob z nazwami plików od *rentalloc001.csv* do *rentalloc10.csv*.

![Moduł czytnika importuje dane z obiektu blob platformy Azure](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Należy zauważyć, że moduł **danych wyjściowych usługi sieci Web** został dodany do modułu **uczenie modelu** .
Gdy ten eksperyment zostanie wdrożony jako usługa sieci Web, punkt końcowy skojarzony z tym wyjściem zwraca model przeszkolony w formacie pliku. ilearner.

Należy również pamiętać, że należy skonfigurować parametr usługi sieci Web, który określa adres URL, który jest wykorzystywany przez moduł **importu danych** . Dzięki temu można użyć parametru, aby określić indywidualne zestawy danych szkoleniowych do uczenia modelu dla każdej lokalizacji.
Można to zrobić na inne sposoby. Możesz użyć zapytania SQL z parametrem usługi sieci Web, aby pobrać dane z bazy danych w Azure SQL Database. Można też użyć modułu  **danych wejściowych usługi sieci Web** do przekazywania zestawu danych do usługi sieci Web.

![Przeszkolone dane wyjściowe modułu modelu do modułu wyjściowego usługi sieci Web](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Teraz Uruchommy ten eksperyment szkoleniowy przy użyciu wartości domyślnej *rental001.csv* jako zestawu danych szkoleniowych. Jeśli wyświetli się dane wyjściowe modułu **szacowania** (kliknij dane wyjściowe i wybierz polecenie **wizualizator**), zobaczysz znośnego wydajność *AUC* = 0,91. W tym momencie możesz przystąpić do wdrożenia usługi sieci Web z tego eksperymentu szkoleniowego.

## <a name="deploy-the-training-and-scoring-web-services"></a>Wdrażanie usług sieci Web szkoleń i oceniania
Aby wdrożyć usługę szkoleniową sieci Web, kliknij przycisk **Skonfiguruj usługę sieci Web** pod kanwą eksperymentu i wybierz pozycję **Wdróż usługę sieci Web**. Wywołaj tę usługę sieci Web "szkolenia z zakresu roweru".

Teraz należy wdrożyć usługę sieci Web oceniania.
Aby to zrobić, kliknij pozycję **Skonfiguruj usługę sieci Web** pod kanwą i wybierz opcję **predykcyjna usługa sieci Web**. Spowoduje to utworzenie eksperymentu oceniania.
Należy wprowadzić kilka drobnych zmian, aby działały jako usługa sieci Web. Usuń kolumnę Label "CNT" z danych wejściowych i Ogranicz dane wyjściowe tylko do identyfikatora wystąpienia i odpowiedniej wartości przewidywanej.

Aby zapisać to działanie, możesz otworzyć [eksperyment predykcyjny](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) w galerii, która została już przygotowana.

Aby wdrożyć usługę sieci Web, uruchom eksperyment predykcyjny, a następnie kliknij przycisk **Wdróż usługę sieci Web** pod kanwą. Nazwij usługa sieci Web oceniania "Ocena wypożyczenia rowerów".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Tworzenie 10 identycznych punktów końcowych usługi sieci Web przy użyciu programu PowerShell
Ta usługa sieci Web jest dołączona do domyślnego punktu końcowego. Ale nie masz zainteresowania domyślnego punktu końcowego, ponieważ nie można go zaktualizować. Co należy zrobić, aby utworzyć 10 dodatkowych punktów końcowych, po jednym dla każdej lokalizacji. Można to zrobić za pomocą programu PowerShell.

Najpierw należy skonfigurować środowisko programu PowerShell:

```powershell
Import-Module .\AzureMLPS.dll
# Assume the default configuration file exists and is properly set to point to the valid Workspace.
$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'
```

Następnie uruchom następujące polecenie programu PowerShell:

```powershell
# Create 10 endpoints on the scoring web service.
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    Write-Host ('adding endpoint ' + $endpointName + '...')
    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
}
```

Teraz utworzono 10 punktów końcowych i wszystkie zawierają ten sam szkolony model szkolony na *customer001.csv*. Można je wyświetlić w Azure Portal.

![Wyświetlanie listy modeli szkolonych w portalu](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Aktualizowanie punktów końcowych, aby używać oddzielnych zestawów danych szkoleniowych przy użyciu programu PowerShell
Następnym krokiem jest zaktualizowanie punktów końcowych o modele, które zostały jednoznacznie przeszkolone na poszczególnych klientach. Najpierw należy utworzyć te modele z poziomu usługi sieci Web **szkoleń rowerowych** . Wróćmy do usługi sieci Web **szkoleń rowerowych** . Musisz wywołać swój punkt końcowy BES 10 razy z 10 różnymi zestawami danych szkoleniowych, aby utworzyć 10 różnych modeli. Aby to zrobić, użyj polecenia cmdlet programu **InovkeAmlWebServiceBESEndpoint** PowerShell.

Musisz również podać poświadczenia dla konta usługi BLOB Storage w usłudze `$configContent` . Mianowicie, w polach, `AccountName` `AccountKey` i `RelativeLocation` . `AccountName`Może to być jedna z nazw kont, jak widać na **Azure Portal** (karta*Storage* ). Po kliknięciu konta magazynu `AccountKey` można je znaleźć, naciskając przycisk **Zarządzaj kluczami dostępu** u dołu i kopiując *podstawowy klucz dostępu*. `RelativeLocation`Jest ścieżką względną do magazynu, w którym będzie przechowywany nowy model. Na przykład ścieżka `hai/retrain/bike_rental/` w poniższym skrypcie wskazuje kontener o nazwie `hai` i `/retrain/bike_rental/` jest podfolderami. Obecnie nie można tworzyć podfolderów za pomocą interfejsu użytkownika portalu, ale istnieje [kilka eksploratorów usługi Azure Storage](../../storage/common/storage-explorers.md) , które umożliwiają wykonanie tej czynności. Zaleca się utworzenie nowego kontenera w magazynie do przechowywania nowych przeszkolonych modeli (plików iLearner) w następujący sposób: ze strony magazynu kliknij przycisk **Dodaj** u dołu i nadaj mu nazwę `retrain` . Podsumowując, niezbędne zmiany w następującym skrypcie odnoszą się do `AccountName` , `AccountKey` i `RelativeLocation` (: `"retrain/model' + $seq + '.ilearner"` ).

```powershell
# Invoke the retraining API 10 times
# This is the default (and the only) endpoint on the training web service
$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
$apiKey = $trainingSvcEp.PrimaryKey;
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
}
```

> [!NOTE]
> Punkt końcowy BES jest jedynym obsługiwanym trybem dla tej operacji. Rekordy zasobów nie mogą być używane do tworzenia przeszkolonych modeli.
> 
> 

Jak widać powyżej, zamiast konstruowania 10 różnych plików JSON konfiguracji zadań BES, zamiast tego należy dynamicznie utworzyć ciąg konfiguracyjny. Następnie podawanie go do parametru *jobConfigString* polecenia cmdlet **InvokeAmlWebServceBESEndpoint** . Nie ma potrzeby zachowywania kopii na dysku.

Jeśli wszystko przebiegnie prawidłowo, po czasie powinno zostać wyświetlone 10. iLearner plików z *model001. iLearner* do *model010. iLearner*w ramach konta usługi Azure Storage. Teraz możesz przystąpić do aktualizowania 10 punktów końcowych usługi sieci Web z tymi modelami za pomocą polecenia cmdlet **AmlWebServiceEndpoint** programu PowerShell. Należy pamiętać, że można tylko zastosować poprawki do innych niż domyślne punkty końcowe, które zostały wcześniej utworzone programowo.

```powershell
# Patch the 10 endpoints with respective .ilearner models
$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
$sasToken = '<my_blob_sas_token>'
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
    Write-Host ('Patching endpoint ' + $endpointName + '...');
    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
}
```

Powinno to działać dość szybko. Po zakończeniu wykonywania zostaną pomyślnie utworzone 10 predykcyjnych punktów końcowych usługi sieci Web. Każdy z nich będzie zawierać przeszkolony model, który został jednoznacznie przeszkolony na zestawie danych specyficznym dla lokalizacji dzierżawy, z jednego eksperymentu szkoleniowego. Aby to sprawdzić, możesz spróbować wywołać te punkty końcowe za pomocą polecenia cmdlet **InvokeAmlWebServiceRRSEndpoint** , podając te same dane wejściowe. Powinny być widoczne różne wyniki prognozowania, ponieważ modele są przeszkolone przy użyciu różnych zestawów szkoleniowych.

## <a name="full-powershell-script"></a>Pełny skrypt programu PowerShell
Poniżej przedstawiono listę pełnego kodu źródłowego:

```powershell
Import-Module .\AzureMLPS.dll
# Assume the default configuration file exists and properly set to point to the valid workspace.
$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

# Create 10 endpoints on the scoring web service
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    Write-Host ('adding endpoint ' + $endpontName + '...')
    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
}

# Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
$apiKey = $trainingSvcEp.PrimaryKey;
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
}

# Patch the 10 endpoints with respective .ilearner models
$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
$sasToken = '?test'
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
    Write-Host ('Patching endpoint ' + $endpointName + '...');
    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
}
```
