---
title: Tworzenie maszyny wirtualnej z systemem Windows przy użyciu programu Azure Image Builder (wersja zapoznawcza)
description: Tworzenie maszyny wirtualnej z systemem Windows przy użyciu programu Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: ca9f9ad0fb724884e865469ba118af4c7e6cea2f
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202848"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Wersja zapoznawcza: Tworzenie maszyny wirtualnej z systemem Windows przy użyciu usługi Azure Image Builder

W tym artykule przedstawiono sposób tworzenia niestandardowego obrazu systemu Windows przy użyciu narzędzia Azure VM Image Builder. W przykładzie w tym artykule opisano sposób dostosowywania obrazu przy użyciu [konfiguratorów](../linux/image-builder-json.md#properties-customize) :
- PowerShell (ScriptUri) — pobiera i uruchamia [skrypt programu PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Ponowne uruchomienie systemu Windows — ponowne uruchomienie maszyny wirtualnej.
- PowerShell (wbudowane) — Uruchom określone polecenie. W tym przykładzie tworzy katalog na maszynie wirtualnej przy użyciu `mkdir c:\\buildActions` .
- Skopiuj plik z witryny GitHub na maszynę wirtualną. Ten przykład kopiuje [index.MD](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) do `c:\buildArtifacts\index.html` maszyny wirtualnej.
- buildTimeoutInMinutes — Zwiększ czas kompilacji, aby umożliwić dłużej uruchomionych kompilacji, wartość domyślna to 240 minut i możesz zwiększyć czas kompilacji, aby umożliwić dłuższe wykonywanie kompilacji.
- vmProfile — Określanie właściwości vmSize i sieci
- osDiskSizeGB — można zwiększyć rozmiar obrazu
- tożsamość — dostarczanie tożsamości dla konstruktora obrazów platformy Azure do użycia podczas kompilacji


Można również określić `buildTimeoutInMinutes` . Wartość domyślna to 240 minut i można zwiększyć czas kompilacji, aby umożliwić dłuższe wykonywanie kompilacji.

Aby skonfigurować obraz, będziemy używać szablonu przykład. JSON. Używany plik JSON jest tutaj: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-the-features"></a>Rejestrowanie funkcji

Aby korzystać z usługi Azure Image Builder w wersji zapoznawczej, należy zarejestrować nową funkcję.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Sprawdź stan rejestracji funkcji.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Sprawdź swoją rejestrację.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
```

Jeśli nie powiedzie się, uruchom następujące polecenie:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```


## <a name="set-variables"></a>Ustaw zmienne

Będziemy wielokrotnie używać niektórych informacji, więc utworzymy pewne zmienne do przechowywania tych informacji.


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

Utwórz zmienną dla identyfikatora subskrypcji. Można to zrobić za pomocą polecenia `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Ta grupa zasobów służy do przechowywania artefaktu szablonu konfiguracji obrazu i obrazu.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Tworzenie tożsamości przypisanej do użytkownika i Ustawianie uprawnień do grupy zasobów
Konstruktor obrazów będzie używał podanej [tożsamości użytkownika](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) w celu dodania obrazu do grupy zasobów. W tym przykładzie utworzysz definicję roli platformy Azure, która zawiera szczegółowe akcje do wykonania dystrybucji obrazu. Definicja roli zostanie następnie przypisana do tożsamości użytkownika.

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika i przyznawanie uprawnień 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

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



## <a name="download-the-image-configuration-template-example"></a>Pobierz przykład szablonu konfiguracji obrazu

Utworzono szablon konfiguracji obrazu sparametryzowanego, który można wypróbować. Pobierz przykładowy plik JSON i skonfiguruj go przy użyciu ustawionych wcześniej zmiennych.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

Możesz zmodyfikować ten przykład w terminalu przy użyciu edytora tekstu, takiego jak `vi` .

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> W przypadku obrazu źródłowego należy zawsze [określić wersję](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version), której nie można użyć `latest` .
> W przypadku dodania lub zmiany grupy zasobów, do której jest dystrybuowany obraz, należy [ustawić uprawnienia](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group) dla grupy zasobów.
 
## <a name="create-the-image"></a>Tworzenie obrazu

Prześlij konfigurację obrazu do usługi Konstruktor obrazów maszyn wirtualnych

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Po zakończeniu spowoduje to zwrócenie komunikatu o powodzeniu z powrotem do konsoli i utworzenie `Image Builder Configuration Template` w `$imageResourceGroup` . Możesz zobaczyć ten zasób w grupie zasobów w Azure Portal, jeśli włączysz opcję "Pokaż ukryte typy".

W tle Konstruktor obrazów utworzy również tymczasową grupę zasobów w ramach subskrypcji. Ta grupa zasobów jest używana do kompilowania obrazu. Będzie w tym formacie: `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Nie można bezpośrednio usunąć grupy zasobów tymczasowych. Najpierw usuń artefakt szablonu obrazu, co spowoduje usunięcie tymczasowej grupy zasobów.

Jeśli usługa zgłasza błąd podczas przesłania szablonu konfiguracji obrazu:
-  Zapoznaj się z tymi krokami [rozwiązywania problemów](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) . 
- Przed ponownym przesłaniem należy usunąć szablon przy użyciu poniższego fragmentu kodu.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Uruchom kompilację obrazu
Rozpocznij proces kompilowania obrazu za pomocą polecenia [AZ Resource Invoke-Action](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Poczekaj, aż kompilacja zostanie ukończona. Może to potrwać około 15 minut.

W przypadku wystąpienia błędów zapoznaj się z tymi krokami [rozwiązywania problemów](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors) .


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną przy użyciu skompilowanego obrazu. Zastąp *\<password>* własnymi hasłami `aibuser` na maszynie wirtualnej.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Sprawdzanie dostosowania

Utwórz połączenie Pulpit zdalny z maszyną wirtualną przy użyciu nazwy użytkownika i hasła ustawione podczas tworzenia maszyny wirtualnej. Na maszynie wirtualnej Otwórz wiersz polecenia i wpisz:

```console
dir c:\
```

Powinny zostać wyświetlone te dwa katalogi, które zostały utworzone podczas dostosowywania obrazu:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Czyszczenie

Gdy wszystko będzie gotowe, Usuń zasoby.

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

Aby dowiedzieć się więcej o składnikach pliku JSON używanego w tym artykule, zobacz [Dokumentacja szablonu konstruktora obrazów](../linux/image-builder-json.md).
