---
title: Jak przenieść zasób usługi między regionami
titleSuffix: Azure Cognitive Search
description: W tym artykule przedstawiono sposób przenoszenia zasobów Wyszukiwanie poznawcze platformy Azure z jednego regionu do innego w chmurze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/10/2020
ms.openlocfilehash: 774b605859df41e0b71ee82c38a6b08bdf5b9c49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91629815"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Przenoszenie usługi Wyszukiwanie poznawcze platformy Azure do innego regionu platformy Azure

Czasami klienci pytają o przeniesienie usługi wyszukiwania do innego regionu. Obecnie nie ma wbudowanego mechanizmu ani narzędzi, które ułatwiają wykonanie tego zadania, ale ten artykuł może pomóc w zrozumieniu ręcznych kroków w celu osiągnięcia tego samego wyniku.

> [!NOTE]
> W Azure Portal wszystkie usługi mają polecenie **eksportu szablonu** . W przypadku usługi Azure Wyszukiwanie poznawcze to polecenie tworzy podstawową definicję usługi (nazwę, lokalizację, warstwę, replikę i liczbę partycji), ale nie rozpoznaje zawartości usługi ani nie przeprowadzi z kluczy, ról ani dzienników. Chociaż to polecenie istnieje, nie zaleca się używania go do przeniesienia usługi wyszukiwania.

## <a name="prerequisites"></a>Wymagania wstępne

+ Upewnij się, że usługi i funkcje używane przez konto są obsługiwane w regionie docelowym.

+ W przypadku funkcji w wersji zapoznawczej upewnij się, że subskrypcja została zatwierdzona dla regionu docelowego.

## <a name="prepare-and-move"></a>Przygotowywanie i przenoszenie

1. Zidentyfikuj zależności i powiązane usługi, aby zrozumieć pełny wpływ zmiany lokalizacji usługi na wypadek, gdyby trzeba było przenieść więcej niż tylko Wyszukiwanie poznawcze platformy Azure.

   Usługa Azure Storage służy do rejestrowania, tworzenia magazynu wiedzy i jest powszechnie używanym zewnętrznym źródłem danych na potrzeby wzbogacania i indeksowania systemu AI. Cognitive Services jest zależność w wzbogacaniu AI. Zarówno Cognitive Services, jak i usługa wyszukiwania, muszą znajdować się w tym samym regionie, jeśli używasz wzbogacania AI.

1. Utwórz spis wszystkich obiektów w usłudze, aby dowiedzieć się, co przenieść: indeksy, mapy synonimów, indeksatory, źródła danych, umiejętności. W przypadku włączenia rejestrowania należy utworzyć i zarchiwizować wszystkie raporty, które mogą być potrzebne w rekordzie historycznym.

1. Sprawdź ceny i dostępność w nowym regionie, aby zapewnić dostępność systemu Azure Wyszukiwanie poznawcze i wszystkie powiązane usługi w nowym regionie. Większość funkcji jest dostępna we wszystkich regionach, ale niektóre funkcje w wersji zapoznawczej mają ograniczoną dostępność.

1. Utwórz usługę w nowym regionie i ponownie Opublikuj z kodu źródłowego wszystkie istniejące indeksy, mapy synonimów, indeksatory, źródła danych i umiejętności. Należy pamiętać, że nazwy usług muszą być unikatowe, aby nie można było użyć istniejącej nazwy. Sprawdź każdy zestawu umiejętności, aby sprawdzić, czy połączenia z Cognitive Services są nadal ważne w warunkach tego samego regionu. Ponadto jeśli są tworzone magazyny wiedzy, sprawdź parametry połączenia dla usługi Azure Storage, jeśli używasz innej usługi.

1. Załaduj ponownie indeksy i magazyny wiedzy, jeśli ma to zastosowanie. Użyjesz kodu aplikacji, aby wypchnąć dane JSON do indeksu, lub ponownie uruchomić indeksatory, aby ściągnąć dokumenty ze źródeł zewnętrznych. 

1. Włącz rejestrowanie i jeśli są używane, Utwórz ponownie role zabezpieczeń.

1. Zaktualizuj aplikacje klienckie i zestawy testów, aby użyć nowej nazwy usługi i kluczy interfejsu API, i przetestuj wszystkie aplikacje.

## <a name="discard-or-clean-up"></a>Odrzucanie lub czyszczenie

Usuń starą usługę, gdy nowa usługa zostanie w pełni przetestowana i działa. Usunięcie usługi spowoduje automatyczne usunięcie całej zawartości skojarzonej z usługą.

## <a name="next-steps"></a>Następne kroki

Poniższe linki mogą pomóc w znalezieniu dodatkowych informacji podczas wykonywania kroków opisanych powyżej.

+ [Ceny i regiony Wyszukiwanie poznawcze platformy Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Wybieranie warstwy](search-sku-tier.md)
+ [Tworzenie usługi wyszukiwania](search-create-service-portal.md)
+ [Załaduj dokumenty do przeszukiwania](search-what-is-data-import.md)
+ [Włącz rejestrowanie](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](./knowledge-store-concept-intro.md), [incremental enrichment](./cognitive-search-incremental-indexing-conceptual.md), and [private endpoint](./service-create-private-endpoint.md).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](../storage/common/storage-account-move.md?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](../storage/common/storage-account-move.md?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](./search-get-started-portal.md)

[Create a skillset](./cognitive-search-quickstart-blob.md)

[Create a knowledge store](./knowledge-store-create-portal.md) -->