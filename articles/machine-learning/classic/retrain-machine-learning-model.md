---
title: 'ML Studio (klasyczny): ponowne uczenie usługi sieci Web — Azure'
description: Dowiedz się, jak zaktualizować usługę sieci Web, aby korzystała z nowo przeszkolonego modelu uczenia maszynowego w Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: ff0378871139a038f096a44b9ee0c6af2cb67d73
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325828"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Ponowne uczenie i wdrażanie modelu uczenia maszynowego

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do. ](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Przeszkolenie jest jednym ze sposobów zapewnienia, że modele uczenia maszynowego są dokładne i oparte na najbardziej przydatnych dostępnych danych. W tym artykule pokazano, jak ponownie nauczyć i wdrożyć model uczenia maszynowego jako nową usługę sieci Web w programie Studio (klasyczna). Jeśli chcesz ponownie przeprowadzić uczenie klasycznej usługi sieci Web, [zapoznaj się z tym artykułem.](retrain-classic-web-service.md)

W tym artykule przyjęto założenie, że masz już wdrożoną predykcyjną usługę sieci Web. Jeśli nie masz jeszcze predykcyjnej usługi sieci Web, [Dowiedz się, jak wdrożyć w tym miejscu usługę sieci Web programu Studio (klasyczną).](deploy-a-machine-learning-web-service.md)

Wykonaj następujące kroki, aby ponownie przeprowadzić uczenie i wdrożyć nową usługę sieci Web uczenia maszynowego:

1. Wdróż **usługę sieci Web do przeszkolenia**
1. Uczenie nowego modelu przy użyciu **usługi sieci Web reszkoleniowej**
1. Zaktualizuj istniejący **eksperyment predykcyjny** , aby użyć nowego modelu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Wdrażanie usługi sieci Web reszkoleniowej

Usługa sieci Web do przeszkolenia umożliwia ponowne uczenie modelu z nowym zestawem parametrów, takimi jak nowe dane, i zapisywanie go w przyszłości. Po podłączeniu **danych wyjściowych usługi sieci Web**  do **modelu uczenia** eksperyment szkoleniowy wysyła nowy model do użycia.

Wykonaj następujące kroki, aby wdrożyć usługę sieci Web z ponownym uczeniem:

1. Połącz moduł **wejściowy usługi sieci Web** z danymi wejściowymi. Zazwyczaj chcesz mieć pewność, że dane wejściowe są przetwarzane w taki sam sposób jak oryginalne dane szkoleniowe.
1. Połącz moduł **wyjściowy usługi sieci Web** z danymi wyjściowymi **modelu szkolenia**.
1. Jeśli masz moduł **oceny modelu** , możesz połączyć moduł **wyjściowy usługi sieci Web** , aby uzyskać wyniki oceny
1. Uruchom eksperyment.

    Po uruchomieniu eksperymentu utworzony przepływ pracy powinien wyglądać podobnie jak na poniższym obrazie:

    ![Wynikający przepływ pracy](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Teraz należy wdrożyć eksperyment szkoleniowy jako usługę sieci Web służącą do reszkoleniowania, która umożliwia generowanie szkoleń modelu i oceny modelu.

1. W dolnej części kanwy eksperymentu kliknij pozycję **Skonfiguruj usługę sieci Web** .
1. Wybierz pozycję **Wdróż usługę sieci Web [New]**. Portal usług sieci Web Azure Machine Learning zostanie otwarty na stronie **wdrażanie usługi sieci Web** .
1. Wpisz nazwę usługi sieci Web i wybierz plan płatności.
1. Wybierz pozycję **Wdróż**.

## <a name="retrain-the-model"></a>Ponowne uczenie modelu

W tym przykładzie używamy języka C# do tworzenia aplikacji do ponownego szkolenia. Aby wykonać to zadanie, można także użyć przykładowego kodu Python lub R.

Wykonaj następujące kroki, aby wywołać interfejsy API ponownego uczenia:

1. Tworzenie aplikacji konsolowej w języku C# w programie Visual Studio: **Nowy**  >  **projekt**  >  **Visual C#**  >  **Windows Classic**  >  **Aplikacja konsolowa (.NET Framework)**.
1. Zaloguj się do portalu usług sieci Web Machine Learning.
1. Kliknij usługę sieci Web, z którą pracujesz.
1. Kliknij **pozycję** Użyj.
1. W dolnej części strony **Zużywaj** w sekcji **przykładowy kod** kliknij pozycję **Batch**.
1. Skopiuj przykładowy kod w języku C# na potrzeby wykonywania wsadowego i wklej go do pliku Program.cs. Upewnij się, że przestrzeń nazw pozostaje nienaruszona.

Dodaj pakiet NuGet Microsoft. AspNet. WebApi. Client, zgodnie z opisem w komentarzach. Aby dodać odwołanie do Microsoft.WindowsAzure.Storage.dll, może być konieczne zainstalowanie [biblioteki klienckiej dla usług Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage).

Poniższy zrzut ekranu przedstawia stronę **Korzystanie** z portalu usług sieci Web Azure Machine Learning.

![Użyj strony](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Aktualizowanie deklaracji apikey

Znajdź deklarację **apikey** :

```csharp
const string apiKey = "abc123"; // Replace this with the API key for the web service
```

W sekcji **podstawowe informacje o zużyciu** na stronie **Używanie** Znajdź klucz podstawowy i skopiuj go do deklaracji **apikey** .

### <a name="update-the-azure-storage-information"></a>Aktualizowanie informacji o usłudze Azure Storage

Przykładowy kod BES przekazuje plik z dysku lokalnego (na przykład "C:\temp\CensusInput.csv") do usługi Azure Storage, przetwarza go i zapisuje wyniki z powrotem do usługi Azure Storage.

1. Logowanie do witryny Azure Portal
1. W lewej kolumnie nawigacji kliknij pozycję **więcej usług** , Wyszukaj pozycję **konta magazynu** i wybierz ją.
1. Z listy kont magazynu wybierz jedną z nich, aby zachować ponownie przemieszczony model.
1. W lewej kolumnie nawigacji kliknij pozycję **klucze dostępu**.
1. Skopiuj i Zapisz **podstawowy klucz dostępu**.
1. W lewej kolumnie nawigacji kliknij pozycję **obiekty blob**.
1. Wybierz istniejący kontener lub Utwórz nowy, a następnie Zapisz nazwę.

Znajdź deklaracje *StorageAccountName* , *StorageAccountKey* i *StorageContainerName* , a następnie zaktualizuj wartości zapisane w portalu.

```csharp
const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name
```

Należy również upewnić się, że plik wejściowy jest dostępny w lokalizacji określonej w kodzie.

### <a name="specify-the-output-location"></a>Określ lokalizację wyjściową

Gdy określisz lokalizację wyjściową w ładunku żądania, rozszerzenie pliku, który jest określony w *RelativeLocation* , musi być określone jako `ilearner` .

```csharp
Outputs = new Dictionary<string, AzureBlobDataReference>() {
    {
        "output1",
        new AzureBlobDataReference()
        {
            ConnectionString = storageConnectionString,
            RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
        }
    },
```

Oto przykład reszkoleniowych danych wyjściowych:

![Dane wyjściowe ponownego uczenia](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Oceń wyniki ponownego szkolenia

Po uruchomieniu aplikacji dane wyjściowe obejmują adres URL i token sygnatur dostępu współdzielonego, które są niezbędne do uzyskania dostępu do wyników oceny.

Wyniki wydajności przeprowadzonego przez siebie modelu można zobaczyć, łącząc *BaseLocation* , *RelativeLocation* i *SasBlobToken* z wyników wyjściowych na potrzeby *output2* i wklejając pełny adres URL na pasku adresu przeglądarki.

Sprawdź wyniki, aby określić, czy nowo szkolony model wykonuje lepsze niż już istniejące.

Zapisz *BaseLocation* , *RelativeLocation* i *SasBlobToken* z wyników danych wyjściowych.

## <a name="update-the-predictive-experiment"></a>Aktualizowanie eksperymentu predykcyjnego

### <a name="sign-in-to-azure-resource-manager"></a>Zaloguj się do Azure Resource Manager

Najpierw Zaloguj się do konta platformy Azure z poziomu środowiska programu PowerShell przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

### <a name="get-the-web-service-definition-object"></a>Pobierz obiekt definicji usługi sieci Web

Następnie Pobierz obiekt definicji usługi sieci Web, wywołując polecenie cmdlet [Get-AzMlWebService](/powershell/module/az.machinelearning/get-azmlwebservice) .

```azurepowershell
$wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

Aby określić nazwę grupy zasobów istniejącej usługi sieci Web, uruchom polecenie cmdlet Get-AzMlWebService bez żadnych parametrów, aby wyświetlić usługi sieci Web w Twojej subskrypcji. Znajdź usługę sieci Web, a następnie sprawdź jej identyfikator usługi sieci Web. Nazwa grupy zasobów to czwarty element w IDENTYFIKATORze, po prostu po elemencie *resourceGroups* . W poniższym przykładzie nazwa grupy zasobów to Default-MachineLearning-SouthCentralUS.

```azurepowershell
Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
Name : RetrainSamplePre.2016.8.17.0.3.51.237
Location : South Central US
Type : Microsoft.MachineLearning/webServices
Tags : {}
```

Alternatywnie, aby określić nazwę grupy zasobów istniejącej usługi sieci Web, zaloguj się do portalu usług sieci Web Azure Machine Learning. Wybierz usługę sieci Web. Nazwa grupy zasobów jest piątym elementem adresu URL usługi sieci Web, po prostu po elemencie *resourceGroups* . W poniższym przykładzie nazwa grupy zasobów to Default-MachineLearning-SouthCentralUS.

`https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237`

### <a name="export-the-web-service-definition-object-as-json"></a>Eksportowanie obiektu definicji usługi sieci Web jako pliku JSON

Aby zmodyfikować definicję modelu przeszkolonego w celu użycia nowo przeszkolonego modelu, należy najpierw użyć polecenia cmdlet [Export-AzMlWebService](/powershell/module/az.machinelearning/export-azmlwebservice) w celu wyeksportowania go do pliku w formacie JSON.

```azurepowershell
Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-reference-to-the-ilearner-blob"></a>Aktualizowanie odwołania do obiektu BLOB ilearner

W obszarze zasoby Znajdź [przeszkolony model], zaktualizuj wartość *identyfikatora URI* w węźle *locationInfo* za pomocą identyfikatora URI obiektu BLOB ilearner. Identyfikator URI jest generowany przez połączenie *BaseLocation* i *RelativeLocation* z danych wyjściowych wywołania reszkoleniowego BES.

```json
"asset3": {
    "name": "Retrain Sample [trained model]",
    "type": "Resource",
    "locationInfo": {
        "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
    },
    "outputPorts": {
        "Results dataset": {
            "type": "Dataset"
        }
    }
},
```

### <a name="import-the-json-into-a-web-service-definition-object"></a>Zaimportuj kod JSON do obiektu definicji usługi sieci Web

Użyj polecenia cmdlet [Import-AzMlWebService](/powershell/module/az.machinelearning/import-azmlwebservice) , aby przekonwertować zmodyfikowany plik JSON z powrotem do obiektu definicji usługi sieci Web, którego można użyć do zaktualizowania eksperymentu predicative.

```azurepowershell
$wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-web-service"></a>Aktualizowanie usługi sieci Web

Na koniec użyj polecenia cmdlet [Update-AzMlWebService](/powershell/module/az.machinelearning/update-azmlwebservice) , aby zaktualizować eksperyment predykcyjny.

```azurepowershell
Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zarządzania usługami sieci Web lub śledzenia wielu przebiegów eksperymentów, zobacz następujące artykuły:

* [Eksplorowanie portalu usług sieci Web](manage-new-webservice.md)
* [Zarządzanie iteracjami eksperymentów](manage-experiment-iterations.md)