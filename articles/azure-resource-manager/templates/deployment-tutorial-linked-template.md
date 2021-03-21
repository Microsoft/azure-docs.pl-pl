---
title: Samouczek — wdrażanie połączonego szablonu
description: Dowiedz się, jak wdrożyć połączony szablon
ms.date: 02/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8f2bbd327adca6eef62d5e79f422f61d460ea7a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589277"
---
# <a name="tutorial-deploy-a-linked-template"></a>Samouczek: wdrażanie połączonego szablonu

W [poprzednich samouczkach](./deployment-tutorial-local-template.md)przedstawiono sposób wdrażania szablonu, który jest przechowywany na komputerze lokalnym. Aby wdrożyć złożone rozwiązania, możesz przerwać szablon do wielu szablonów i wdrożyć te szablony za pomocą szablonu głównego. W tym samouczku dowiesz się, jak wdrożyć szablon główny, który zawiera odwołanie do połączonego szablonu. Po wdrożeniu głównego szablonu są wywoływane wdrożenia szablonów połączonych. Dowiesz się również, jak przechowywać szablony i zabezpieczać je za pomocą tokenu SAS. Ukończenie może potrwać około **12 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy wykonanie poprzedniego samouczka, ale nie jest to wymagane.

## <a name="review-template"></a>Przejrzyj szablon

W poprzednich samouczkach wdrażasz szablon, który tworzy konto magazynu, App Service plan i aplikację sieci Web. Użyty szablon:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Utwórz połączony szablon

Zasób konta magazynu można rozdzielić na połączony szablon:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

Następujący szablon jest głównym szablonem. Wyróżniony `Microsoft.Resources/deployments` obiekt pokazuje, jak wywołać połączony szablon. Połączony szablon nie może być przechowywany jako plik lokalny ani plik, który jest dostępny tylko w sieci lokalnej. Możesz podać wartość identyfikatora URI połączonego szablonu, który zawiera HTTP lub HTTPS lub użyć właściwości _RelativePath_ do wdrożenia zdalnego szablonu połączonego w lokalizacji względnej dla szablonu nadrzędnego. Jedną z opcji jest umieszczenie szablonu głównego i połączonego szablonu na koncie magazynu.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="34-52":::

## <a name="store-the-linked-template"></a>Zapisz połączony szablon

Oba szablony główne i połączone szablony są przechowywane w witrynie GitHub:

Poniższy skrypt programu PowerShell tworzy konto magazynu, tworzy kontener i kopiuje dwa szablony z repozytorium GitHub do kontenera. Te dwa szablony są następujące:

- Główny szablon: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
- Połączony szablon: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Wybierz pozycję **Wypróbuj** , aby otworzyć Cloud Shell, wybierz pozycję **Kopiuj** , aby skopiować skrypt programu PowerShell, a następnie kliknij prawym przyciskiem myszy okienko powłoki, aby wkleić skrypt:

> [!IMPORTANT]
> Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków i używać tylko cyfr i małych liter. Nazwa musi być unikatowa. W szablonie nazwa konta magazynu to nazwa projektu z dołączonym **magazynem** , a nazwa projektu musi zawierać od 3 do 11 znaków. Nazwa projektu musi być zgodna z wymaganiami dotyczącymi nazwy konta magazynu i ma mniej niż 11 znaków.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$mainTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json"
$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json"

$mainFileName = "azuredeploy.json" # A file name used for downloading and uploading the main template.Add-PSSnapin
$linkedFileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the templates
Invoke-WebRequest -Uri $mainTemplateURL -OutFile "$home/$mainFileName"
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$linkedFileName"

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

# Upload the templates
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$mainFileName" `
    -Blob $mainFileName `
    -Context $context

Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$linkedFileName" `
    -Blob $linkedFileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Wdrażanie szablonu

Aby wdrożyć szablony na koncie magazynu, wygeneruj token sygnatury dostępu współdzielonego i podaj go do parametru _-QueryString_ . Ustaw czas wygaśnięcia, aby zapewnić wystarczającą ilość czasu na ukończenie wdrożenia. Obiekty blob zawierające szablony są dostępne tylko dla właściciela konta. Jednak podczas tworzenia tokenu sygnatury dostępu współdzielonego dla obiektu BLOB obiekt BLOB jest dostępny dla wszystkich użytkowników z tokenem SAS. Jeśli inny użytkownik przechwytuje identyfikator URI i token sygnatury dostępu współdzielonego, ten użytkownik będzie mógł uzyskać dostęp do szablonu. Token SAS to dobry sposób ograniczania dostępu do szablonów, ale nie należy uwzględniać poufnych danych, takich jak hasła bezpośrednio w szablonie.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> W poniższym kodzie interfejsu wiersza polecenia platformy Azure `date` parametr `-d` jest nieprawidłowym argumentem w macOS. Tak więc macOS użytkowników, aby dodać 2 godziny do bieżącego czasu w terminalu na macOS, którego należy użyć `-v+2H` .

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive

$projectName = Read-Host -Prompt "Enter the same project name:"   # This name is used to generate names for Azure resources, such as storage account name.

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

$mainTemplateUri = $context.BlobEndPoint + "$containerName/azuredeploy.json"
$sasToken = New-AzStorageContainerSASToken `
    -Context $context `
    -Container $containerName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0)
$newSas = $sasToken.substring(1)


New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri $mainTemplateUri `
  -QueryString $newSas `
  -projectName $projectName `
  -verbose

Write-Host "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
echo "Enter a project name that is used to generate resource names:" &&
read projectName &&

resourceGroupName="${projectName}rg" &&
storageAccountName="${projectName}store" &&
containerName="templates" &&

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv) &&

sasToken=$(az storage container generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --name $containerName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'`) &&
sasToken=$(echo $sasToken | sed 's/"//g')&&

blobUri=$(az storage account show -n $storageAccountName -g $resourceGroupName -o tsv --query primaryEndpoints.blob) &&
templateUri="${blobUri}${containerName}/azuredeploy.json" &&

az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-uri $templateUri \
  --parameters projectName=$projectName \
  --query-string $sasToken \
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