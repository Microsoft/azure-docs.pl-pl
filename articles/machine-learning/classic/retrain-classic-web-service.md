---
title: 'ML Studio (klasyczny): ponowne uczenie klasycznej usługi sieci Web — Azure'
description: Dowiedz się, jak ponownie nauczyć model i zaktualizować klasyczną usługę sieci Web, aby korzystała z nowo przeszkolonego modelu w Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: 90c968ee953e80238775639964cb09a25741b33d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100517573"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Ponowne uczenie i wdrażanie klasycznej usługi sieci Web (klasycznej)

**dotyczy:** ![ Zielony znacznik wyboru. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) ![ X wskazujący nie.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Przeszkolenie modeli uczenia maszynowego jest jednym ze sposobów upewnienia się, że są one dokładne i oparte na najbardziej przydatnych dostępnych danych. W tym artykule przedstawiono sposób ponownego uczenia klasycznej usługi sieci Web (klasycznej). Aby zapoznać się z przewodnikiem dotyczącym ponownego uczenia nowej usługi sieci Web programu Studio (klasycznej), [Zobacz ten artykuł.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz już eksperyment szkoleniowy i eksperyment predykcyjny. Te kroki są wyjaśnione w sekcji [ponowne uczenie i wdrażanie modelu uczenia maszynowego.](./retrain-machine-learning-model.md) Jednak zamiast wdrażania modelu uczenia maszynowego jako nowej usługi sieci Web, należy wdrożyć eksperyment predykcyjny jako klasyczną usługę sieci Web.
     
## <a name="add-a-new-endpoint"></a>Dodaj nowy punkt końcowy

Wdrożona usługa sieci Web predykcyjna zawiera domyślny punkt końcowy oceniania, który jest zsynchronizowany z pierwotnym modelem szkoleń i eksperymentów oceniania. Aby zaktualizować usługę sieci Web do programu przy użyciu nowego, nauczonego modelu, należy utworzyć nowy punkt końcowy oceniania.

Istnieją dwa sposoby dodawania nowego punktu końcowego do usługi sieci Web:

* Programistyczne
* Korzystanie z portalu usług sieci Web platformy Azure

> [!NOTE]
> Pamiętaj, aby dodać punkt końcowy do usługi predykcyjnej sieci Web, a nie usługi sieci Web szkoleniowej. Jeśli poprawnie wdrożono zarówno szkolenie, jak i predykcyjną usługę sieci Web, powinny zostać wyświetlone dwie oddzielne usługi sieci Web. Predykcyjna usługa sieci Web powinna kończyć się znakiem "[EXP predykcyjnym.]".
>

### <a name="programmatically-add-an-endpoint"></a>Programowe Dodawanie punktu końcowego

Punkty końcowe oceniania można dodawać przy użyciu przykładowego kodu podanego w tym [repozytorium GitHub](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Dodawanie punktu końcowego przy użyciu portalu usług sieci Web platformy Azure

1. W Machine Learning Studio (klasyczny), w lewej kolumnie nawigacyjnej kliknij pozycję usługi sieci Web.
1. W dolnej części pulpitu nawigacyjnego usługi sieci Web kliknij pozycję **Zarządzaj podglądami punktów końcowych**.
1. Kliknij pozycję **Dodaj**.
1. Wpisz nazwę i opis nowego punktu końcowego. Wybierz poziom rejestrowania i czy włączono przykładowe dane. Aby uzyskać więcej informacji na temat rejestrowania, zobacz [Włączanie rejestrowania dla usług sieci web Machine Learning](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Aktualizowanie przeszkolonego modelu dodanego punktu końcowego

### <a name="retrieve-patch-url"></a>Pobierz adres URL poprawki

Wykonaj następujące kroki, aby uzyskać prawidłowy adres URL poprawek przy użyciu portalu sieci Web:

1. Zaloguj się do portalu [usług sieci Web Azure Machine Learning](https://services.azureml.net/) .
1. Kliknij pozycję **usługi sieci Web** lub **klasyczne usługi sieci Web** u góry.
1. Kliknij usługę oceniania sieci Web, z którą pracujesz (jeśli nie zmodyfikujesz domyślnej nazwy usługi sieci Web, zostanie ona zakończona w "[Punktacja EXP.]").
1. Kliknij pozycję **+ Nowy**.
1. Po dodaniu punktu końcowego kliknij nazwę punktu końcowego.
1. W obszarze adres URL **poprawki** kliknij polecenie **Pomoc interfejsu API** , aby otworzyć stronę Pomoc dotycząca poprawek.

> [!NOTE]
> Jeśli punkt końcowy został dodany do usługi sieci Web szkoleń zamiast usługi sieci Web predykcyjnej, po kliknięciu linku **Aktualizuj zasób** zostanie wyświetlony następujący komunikat o błędzie: "Niestety, ale ta funkcja nie jest obsługiwana ani dostępna w tym kontekście. Ta usługa sieci Web nie ma zasobów aktualizowalnych. Przepraszamy za niedogodności i pracują nad ulepszaniem tego przepływu pracy ".
>

Strona Pomoc dotycząca poprawek zawiera adres URL poprawki, którego należy użyć, i zawiera przykładowy kod, którego można użyć do wywołania go.

![Adres URL poprawki.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Aktualizowanie punktu końcowego

Możesz teraz użyć przeszkolonego modelu, aby zaktualizować utworzony wcześniej punkt końcowy oceniania.

Poniższy przykładowy kod pokazuje, jak zaktualizować punkt końcowy przy użyciu adresu URL *BaseLocation*, *RelativeLocation*, *SasBlobToken* i patch.

```csharp
private async Task OverwriteModel()
{
    var resourceLocations = new
    {
        Resources = new[]
        {
            new
            {
                Name = "Census Model [trained model]",
                Location = new AzureBlobDataReference()
                {
                    BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                    RelativeLocation = "your endpoint relative location", //from the output, for example: "experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner"
                    SasBlobToken = "your endpoint SAS blob token" //from the output, for example: "?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl"
                }
            }
        }
    };

    using (var client = new HttpClient())
    {
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

        using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
        {
            request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
            HttpResponseMessage response = await client.SendAsync(request);

            if (!response.IsSuccessStatusCode)
            {
                await WriteFailedResponse(response);
            }

            // Do what you want with a successful response here.
        }
    }
}
```

*ApiKey* i *endpointUrl* dla wywołania można uzyskać z pulpitu nawigacyjnego punktu końcowego.

Wartość parametru *name* w *zasobach* powinna być zgodna z nazwą zasobu zapisanego przeszkolonego modelu w eksperymentie predykcyjnym. Aby uzyskać nazwę zasobu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu po lewej stronie kliknij pozycję **Machine Learning**.
1. W obszarze Nazwa kliknij swój obszar roboczy, a następnie kliknij pozycję **usługi sieci Web**.
1. W obszarze Nazwa kliknij pozycję **model spisu [EXP predykcyjny.]**.
1. Kliknij nowy punkt końcowy, który został dodany.
1. Na pulpicie nawigacyjnym punktu końcowego kliknij pozycję **Aktualizuj zasób**.
1. Na stronie Dokumentacja interfejsu API zasobów aktualizacji dla usługi sieci Web można znaleźć **nazwę zasobu** w obszarze **aktualizowalne zasoby**.

Jeśli token sygnatury dostępu współdzielonego wygaśnie przed ukończeniem aktualizacji punktu końcowego, należy wykonać pobieranie z IDENTYFIKATORem zadania w celu uzyskania nowego tokenu.

Po pomyślnym uruchomieniu kodu nowy punkt końcowy powinien zacząć korzystać z przeszkolnego modelu przez około 30 sekund.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zarządzania usługami sieci Web lub śledzenia wielu przebiegów eksperymentów, zobacz następujące artykuły:

* [Eksplorowanie portalu usług sieci Web](manage-new-webservice.md)
* [Zarządzanie iteracjami eksperymentów](manage-experiment-iterations.md)