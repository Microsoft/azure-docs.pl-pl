---
title: Tworzenie maszyny wirtualnej z systemem Windows przy użyciu usługi Azure Image Builder (wersja zapoznawcza)
description: Utwórz maszynę wirtualną z systemem Windows przy użyciu usługi Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: b93d236d0b716bfaf7dfb45b21c9524ece75fcae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764571"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Wersja zapoznawcza: tworzenie maszyny wirtualnej z systemem Windows przy użyciu usługi Azure Image Builder

W tym artykule opisano, jak można utworzyć dostosowany obraz systemu Windows przy użyciu Azure VM Image Builder. W przykładzie w tym artykule do dostosowywania obrazu [użyto](../linux/image-builder-json.md#properties-customize) dostosowywania:
- PowerShell (ScriptUri) — pobierz i uruchom skrypt [programu PowerShell.](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)
- Ponowne uruchomienie systemu Windows — powoduje ponowne uruchomienie maszyny wirtualnej.
- Program PowerShell (w tekście) — uruchom określone polecenie. W tym przykładzie tworzy katalog na maszynie wirtualnej przy użyciu narzędzia `mkdir c:\\buildActions` .
- Plik — skopiuj plik z usługi GitHub na maszynę wirtualną. Ten przykład [kopiuje index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) `c:\buildArtifacts\index.html` do na maszynie wirtualnej.
- buildTimeoutInMinutes — zwiększ czas kompilacji, aby zezwolić na dłużej działające kompilacje, wartość domyślna to 240 minut i możesz zwiększyć czas kompilacji, aby umożliwić dłuższe działanie kompilacji.
- vmProfile — określanie właściwości vmSize i Network
- osDiskSizeGB — można zwiększyć rozmiar obrazu
- tożsamość — zapewnianie tożsamości dla usługi Azure Image Builder do użycia podczas kompilacji


Można również określić wartość `buildTimeoutInMinutes` . Wartość domyślna to 240 minut i można zwiększyć czas kompilacji, aby umożliwić dłuższe działanie kompilacji.

Do skonfigurowania obrazu będziemy używać przykładowego szablonu JSON. Plik json, z których korzystamy, znajduje się tutaj: [helloImageTemplateWin.jspliku .](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json) 


> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-the-features"></a>Rejestrowanie funkcji

Aby używać usługi Azure Image Builder w wersji zapoznawczej, należy zarejestrować nową funkcję.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Sprawdź stan rejestracji funkcji.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Sprawdź rejestrację.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

Jeśli użytkownik nie stwierdzi, że jest zarejestrowany, uruchom następujące czynności:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="set-variables"></a>Ustawianie zmiennych

Będziemy wielokrotnie używać niektórych informacji, dlatego utworzymy pewne zmienne do przechowywania tych informacji.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Utwórz zmienną dla identyfikatora subskrypcji. Można to uzyskać za pomocą narzędzia `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Ta grupa zasobów służy do przechowywania artefaktu szablonu konfiguracji obrazu i obrazu.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Tworzenie tożsamości przypisanej przez użytkownika i ustawianie uprawnień do grupy zasobów
Image Builder użyje [podanej tożsamości użytkownika,](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) aby wstrzyknąć obraz do grupy zasobów. W tym przykładzie utworzysz definicję roli platformy Azure, która zawiera szczegółowe akcje do wykonania podczas dystrybucji obrazu. Definicja roli zostanie następnie przypisana do tożsamości użytkownika.

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika i udzielanie uprawnień 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```



## <a name="download-the-image-configuration-template-example"></a>Pobieranie przykładowego szablonu konfiguracji obrazu

Do wypróbowania został utworzony szablon konfiguracji obrazu sparametryzowanego. Pobierz przykładowy plik json i skonfiguruj go przy użyciu ustawionych wcześniej zmiennych.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

Ten przykład można zmodyfikować w terminalu przy użyciu edytora tekstów, takiego jak `vi` .

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> W przypadku obrazu źródłowego należy zawsze [określić wersję](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version). Nie można użyć wartości `latest` .
> Jeśli dodasz lub zmienisz grupę zasobów, do której [](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group) jest dystrybuowany obraz, musisz ustawić uprawnienia dla grupy zasobów.
 
## <a name="create-the-image"></a>Tworzenie obrazu

Przesyłanie konfiguracji obrazu do usługi zarządzania maszyną Image Builder wirtualnej

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Po zakończeniu spowoduje to zwrócenie komunikatu o sukcesie z powrotem do konsoli i utworzenie w `Image Builder Configuration Template` . `$imageResourceGroup` Ten zasób można zobaczyć w grupie zasobów w Azure Portal, jeśli włączysz opcję "Pokaż ukryte typy".

W tle Image Builder również utworzyć przemieszczania grupy zasobów w ramach subskrypcji. Ta grupa zasobów jest używana do kompilacji obrazu. Będzie on w tym formacie: `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Tymczasowej grupy zasobów nie można usunąć bezpośrednio. Najpierw usuń artefakt szablonu obrazu, co spowoduje usunięcie tymczasowej grupy zasobów.

Jeśli usługa zgłasza błąd podczas przesyłania szablonu konfiguracji obrazu:
-  Zapoznaj się z [tymi krokami rozwiązywania](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) problemów. 
- Przed ponowieniem próby przesłania należy usunąć szablon przy użyciu poniższego fragmentu kodu.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Uruchamianie kompilacji obrazu
Rozpocznij proces budowania obrazu przy użyciu [narzędzia az resource invoke-action](/cli/azure/resource#az_resource_invoke_action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Poczekaj na zakończenie kompilacji. Może to potrwać około 15 minut.

Jeśli wystąpią jakiekolwiek błędy, zapoznaj się z tymi [krokami rozwiązywania](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors) problemów.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną przy użyciu sbudowaną przez Ciebie obrazu. Zastąp *\<password>* własnym hasłem dla maszyny `aibuser` wirtualnej.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Weryfikowanie dostosowania

Utwórz Pulpit zdalny z maszyną wirtualną przy użyciu nazwy użytkownika i hasła ustawionego podczas tworzenia maszyny wirtualnej. Wewnątrz maszyny wirtualnej otwórz wiersz polecenia i wpisz:

```console
dir c:\
```

Podczas dostosowywania obrazu powinny zostać utworzone następujące dwa katalogi:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Czyszczenie

Gdy wszystko będzie gotowe, usuń zasoby.

### <a name="delete-the-image-builder-template"></a>Usuwanie szablonu konstruktora obrazów

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>Usuń przypisanie roli, definicję roli i tożsamość użytkownika.
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>Usuwanie grupy zasobów obrazu

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat składników pliku JSON używanych w tym artykule, zobacz Odwołanie do szablonu [konstruktora obrazów](../linux/image-builder-json.md).
