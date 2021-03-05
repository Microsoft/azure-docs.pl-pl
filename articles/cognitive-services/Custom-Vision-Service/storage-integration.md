---
title: Integracja usługi Azure Storage w celu otrzymywania powiadomień i tworzenia kopii zapasowej modelu
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zintegrować usługę Azure Storage w celu otrzymywania powiadomień wypychanych podczas uczenia lub eksportowania modeli Custom Vision. Można również zapisać kopię zapasową eksportowanych modeli.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: d889eab429b56a9f4e01684e03c67d394d33472b
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178018"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>Integrowanie usługi Azure Storage na potrzeby powiadomień i kopii zapasowych

Projekt Custom Vision można zintegrować z kolejką usługi Azure Blob Storage w celu uzyskania powiadomień wypychanych dotyczących działania szkolenia i eksportowania projektu oraz kopii zapasowych opublikowanych modeli. Ta funkcja jest przydatna, aby uniknąć ciągłego sondowania usługi pod kątem wyników, gdy długotrwałe operacje są uruchomione. Zamiast tego można zintegrować powiadomienia kolejki magazynu z przepływem pracy.

W tym przewodniku pokazano, jak używać tych interfejsów API REST z zwinięciem. Możesz również użyć usługi żądania HTTP, takiej jak program Poster, aby wydać żądania.

> [!NOTE]
> Powiadomienia wypychane są zależne od opcjonalnego parametru _notificationQueueUri_ w interfejsie API tworzenia **projektów** , a kopie zapasowe modelu wymagają również użycia opcjonalnego parametru _exportModelContainerUri_ . Ten przewodnik będzie używany do pełnego zestawu funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Zasób Custom Vision na platformie Azure. Jeśli go nie masz, przejdź do Azure Portal i [Utwórz nowy zasób Custom Vision](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true). Ta funkcja nie obsługuje obecnie zasobu usługi poznawczej (wszystko w jednym kluczu).
- Konto usługi Azure Storage z kontenerem obiektów BLOB. Jeśli potrzebujesz pomocy dotyczącej tego kroku, obserwuj [ćwiczenia 1 w laboratorium usługi Azure Storage](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) .
* Program [PowerShell w wersji 6.0](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7.1)lub podobnej aplikacji w wierszu polecenia.

## <a name="set-up-azure-storage-integration"></a>Konfigurowanie integracji usługi Azure Storage

Przejdź do zasobów szkoleniowych Custom Vision na Azure Portal, wybierz stronę **tożsamość** i Włącz tożsamość zarządzaną przypisaną przez system.

Następnie przejdź do zasobu magazynu w Azure Portal. Przejdź do strony **Kontrola dostępu (IAM)** i Dodaj przypisanie roli dla każdej funkcji integracji:
* Wybierz zasób szkolenia Custom Vision i przypisz rolę **współautor danych obiektów blob magazynu** , jeśli planujesz korzystanie z funkcji tworzenia kopii zapasowej modelu. 
* Następnie wybierz zasób szkolenia Custom Vision i przypisz **współautor danych kolejki magazynu** , jeśli planujesz użyć funkcji kolejki powiadomień.

> [!div class="mx-imgBorder"]
> ![Strona Dodawanie przypisania roli konta magazynu](./media/storage-integration/storage-access.png)

### <a name="get-integration-urls"></a>Pobierz adresy URL integracji

Następnie uzyskasz adresy URL, które umożliwiają zasobowi Custom Vision dostęp do tych punktów końcowych.

W polu adres URL integracji kolejki powiadomień przejdź do strony **kolejki** konta magazynu, Dodaj nową kolejkę i Zapisz jej adres URL w lokalizacji tymczasowej.

> [!div class="mx-imgBorder"]
> ![Strona kolejki usługi Azure Storage](./media/storage-integration/queue-url.png) 

W polu adres URL integracji kopii zapasowej modelu przejdź do strony **kontenery** na koncie magazynu i Utwórz nowy kontener. Następnie wybierz go i przejdź do strony **Właściwości** . Skopiuj adres URL do lokalizacji tymczasowej.
 
> [!div class="mx-imgBorder"]
> ![Strona właściwości kontenera usługi Azure Storage](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>Integracja Custom Vision projektu

Teraz, gdy masz adresy URL integracji, możesz utworzyć nowy projekt Custom Vision, który integruje funkcje usługi Azure Storage. Możesz również zaktualizować istniejący projekt, aby dodać funkcje.

### <a name="create-new-project"></a>Tworzenie nowego projektu

Po wywołaniu interfejsu API programu [Project](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) Dodaj parametry opcjonalne _exportModelContainerUri_ i _notificationQueueUri_. Przypisz wartości adresu URL, które zostały uzyskane w poprzedniej sekcji. 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

Jeśli otrzymasz `200/OK` odpowiedź, oznacza to, że adresy URL zostały pomyślnie skonfigurowane. Wartości adresów URL powinny być również widoczne w odpowiedzi JSON:

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>Aktualizowanie istniejącego projektu

Aby zaktualizować istniejący projekt przy użyciu integracji funkcji usługi Azure Storage, Wywołaj interfejs API [UpdateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1) przy użyciu identyfikatora projektu, który chcesz zaktualizować. 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

Ustaw treść żądania ( `body` ) na następujący format JSON, wypełniając odpowiednie wartości parametrów _ExportModelContainerUri_ i _notificationQueueUri_:

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

Jeśli otrzymasz `200/OK` odpowiedź, oznacza to, że adresy URL zostały pomyślnie skonfigurowane.

## <a name="verify-the-connection"></a>Weryfikowanie połączenia 

Wywołanie interfejsu API w poprzedniej sekcji powinno mieć już wyzwolone nowe informacje na koncie usługi Azure Storage. 

W wyznaczonej kontenerze powinien istnieć testowy obiekt BLOB w folderze **CustomVision-TestPermission** . Ten obiekt BLOB będzie istnieć tymczasowo.

W kolejce powiadomień powinna zostać wyświetlona powiadomienie testowe w następującym formacie:
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>Otrzymywanie powiadomień o zdarzeniach

Gdy wszystko będzie gotowe, Wywołaj interfejs API [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) w projekcie w celu wykonania zwykłej operacji szkoleniowej.

W kolejce powiadomień magazynu otrzymasz powiadomienie po zakończeniu szkolenia:

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

`"trainingStatus"`Pole może mieć wartość `"TrainingCompleted"` lub `"TrainingFailed"` . `"iterationId"`To pole jest identyfikatorem nauczonego modelu.

## <a name="get-model-export-backups"></a>Pobierz kopie zapasowe eksportu modelu

Gdy wszystko będzie gotowe, Wywołaj interfejs API [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece) , aby wyeksportować szkolony model do określonej platformy.

W wybranym kontenerze magazynu zostanie wyświetlona kopia zapasowa wyeksportowanego modelu. Nazwa obiektu BLOB będzie miała format:

```
{projectId} - {iterationId}.{platformType}
```

Powiadomienie zostanie również odebrane w kolejce po zakończeniu eksportowania. 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

`"exportStatus"`Pole może mieć wartość `"ExportCompleted"` lub `"ExportFailed"` . `"modelUri"`Pole będzie zawierać adres URL modelu kopii zapasowej przechowywanego w kontenerze, przy założeniu, że na początku zintegrowane powiadomienia o kolejkach. Jeśli nie, w `"modelUri"` polu zostanie wyświetlony adres URL sygnatury dostępu współdzielonego dla Custom Vision obiektu BLOB modelu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku przedstawiono sposób kopiowania i przenoszenia projektu między zasobami Custom Vision. Następnie zapoznaj się z dokumentacją dotyczącą interfejsów API, aby zobaczyć, co jeszcze można zrobić z Custom Vision.
* [Dokumentacja interfejsu API REST (szkolenie)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
* [Dokumentacja interfejsu API REST (przewidywania)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3)
