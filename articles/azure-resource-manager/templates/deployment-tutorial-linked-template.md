---
title: Samouczek — wdrażanie połączonego szablonu
description: Dowiedz się, jak wdrożyć połączony szablon
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 7a5872f94a2d267ed2a0e17815e84cec5b02c613
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368107"
---
# <a name="tutorial-deploy-a-linked-template"></a>Samouczek: wdrażanie połączonego szablonu

W [poprzednich samouczkach](./deployment-tutorial-local-template.md)przedstawiono sposób wdrażania szablonu, który jest przechowywany na komputerze lokalnym. Aby wdrożyć złożone rozwiązania, możesz przerwać szablon do wielu szablonów i wdrożyć te szablony za pomocą szablonu głównego. W tym samouczku dowiesz się, jak wdrożyć szablon główny, który zawiera odwołanie do połączonego szablonu. Po wdrożeniu głównego szablonu są wywoływane wdrożenia szablonów połączonych. Dowiesz się również, jak przechowywać i zabezpieczyć połączony szablon przy użyciu tokenu SAS. Ukończenie może potrwać około **12 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy wykonanie poprzedniego samouczka, ale nie jest to wymagane.

## <a name="review-template"></a>Przejrzyj szablon

W poprzednich samouczkach wdrażasz szablon, który tworzy konto magazynu, App Service plan i aplikację sieci Web. Użyty szablon:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Utwórz połączony szablon

Zasób konta magazynu można rozdzielić na połączony szablon:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

Następujący szablon jest głównym szablonem. Wyróżniony `Microsoft.Resources/deployments` obiekt pokazuje, jak wywołać połączony szablon. Połączony szablon nie może być przechowywany jako plik lokalny ani plik, który jest dostępny tylko w sieci lokalnej. Można podać tylko wartość identyfikatora URI, która zawiera `HTTP` lub `HTTPS` . Menedżer zasobów musi mieć możliwość uzyskania dostępu do szablonu. Jedną z opcji jest umieszczenie powiązanego szablonu na koncie magazynu i użycie identyfikatora URI dla tego elementu. Identyfikator URI jest przesyłany do szablonu za pomocą parametru. Zobacz definicję wyróżnionego parametru.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Zapisz kopię głównego szablonu na komputerze lokalnym z rozszerzeniem _JSON_ , na przykład _azuredeploy.json_. Nie musisz zapisywać kopii połączonego szablonu. Połączony szablon zostanie skopiowany z repozytorium GitHub do konta magazynu.

## <a name="store-the-linked-template"></a>Zapisz połączony szablon

Poniższy skrypt programu PowerShell tworzy konto magazynu, tworzy kontener i kopiuje połączony szablon z repozytorium GitHub do kontenera. Kopia połączonego szablonu jest przechowywana w serwisie [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json).

Wybierz pozycję **Wypróbuj** , aby otworzyć Cloud Shell, wybierz pozycję **Kopiuj** , aby skopiować skrypt programu PowerShell, a następnie kliknij prawym przyciskiem myszy okienko powłoki, aby wkleić skrypt:

> [!IMPORTANT]
> Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków i używać tylko cyfr i małych liter. Nazwa musi być unikatowa. W szablonie nazwa konta magazynu to nazwa projektu z dołączonym **magazynem** , a nazwa projektu musi zawierać od 3 do 11 znaków. Nazwa projektu musi być zgodna z wymaganiami dotyczącymi nazwy konta magazynu i ma mniej niż 11 znaków.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Wdrażanie szablonu

Aby wdrożyć szablon prywatny na koncie magazynu, wygeneruj token sygnatury dostępu współdzielonego i umieść go w identyfikatorze URI dla szablonu. Ustaw czas wygaśnięcia, aby zapewnić wystarczającą ilość czasu na ukończenie wdrożenia. Obiekt BLOB zawierający szablon jest dostępny tylko dla właściciela konta. Jednak podczas tworzenia tokenu sygnatury dostępu współdzielonego dla obiektu BLOB obiekt BLOB jest dostępny dla wszystkich użytkowników o tym identyfikatorze URI. Jeśli inny użytkownik przechwytuje identyfikator URI, ten użytkownik będzie mógł uzyskać dostęp do szablonu. Token SAS to dobry sposób ograniczania dostępu do szablonów, ale nie należy uwzględniać poufnych danych, takich jak hasła bezpośrednio w szablonie.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> W poniższym kodzie interfejsu wiersza polecenia platformy Azure `date` parametr `-d` jest nieprawidłowym argumentem w macOS. Tak więc macOS użytkowników, aby dodać 2 godziny do bieżącego czasu w terminalu na macOS, którego należy użyć `-v+2H` .

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
  --verbose
```

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak wdrożyć połączony szablon. W następnym samouczku dowiesz się, jak utworzyć potok DevOps, aby wdrożyć szablon.

> [!div class="nextstepaction"]
> [Tworzenie potoku](./deployment-tutorial-pipeline.md)
