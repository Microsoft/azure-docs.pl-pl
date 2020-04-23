---
title: Tworzenie maszyny Wirtualnej systemu Windows za pomocą programu Azure Image Builder (wersja zapoznawcza)
description: Utwórz maszynę wirtualną systemu Windows za pomocą programu Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 269b2f4674f2c99fc438c1a7be65e5660ca58d08
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869510"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Wersja zapoznawcza: tworzenie maszyny Wirtualnej systemu Windows za pomocą programu Azure Image Builder

W tym artykule pokazano, jak można utworzyć dostosowany obraz systemu Windows przy użyciu konstruktora obrazów maszyny Wirtualnej platformy Azure. W tym artykule użyto [konfiguratorów](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) do dostosowywania obrazu:
- PowerShell (ScriptUri) - pobierz i uruchom [skrypt programu PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Ponowne uruchomienie systemu Windows — ponownie uruchamia maszynę wirtualną.
- Program PowerShell (wbudowany) — uruchom określone polecenie. W tym przykładzie tworzy katalog na maszynie wirtualnej przy użyciu `mkdir c:\\buildActions`.
- Plik - skopiuj plik z gitHub na maszynę wirtualną. W tym przykładzie kopiuje [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) `c:\buildArtifacts\index.html` na maszynie wirtualnej.

Można również określić plik `buildTimeoutInMinutes`. Wartość domyślna to 240 minut i można zwiększyć czas kompilacji, aby umożliwić dłuższe uruchamianie kompilacji.

Będziemy używać przykładowego szablonu .json do skonfigurowania obrazu. Plik .json, z których korzystamy, jest tutaj: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-the-features"></a>Zarejestruj funkcje

Aby korzystać z usługi Azure Image Builder podczas podglądu, należy zarejestrować nową funkcję.

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

az provider show -n Microsoft.Storage | grep registrationState
```

Jeśli nie mówią zarejestrowane, uruchom następujące czynności:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables"></a>Ustawianie zmiennych

Będziemy wielokrotnie wykorzystywać niektóre informacje, więc utworzymy pewne zmienne do przechowywania tych informacji.


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

Utwórz zmienną dla identyfikatora subskrypcji. Można to uzyskać `az account show | grep id`za pomocą programu .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Ta grupa zasobów służy do przechowywania artefaktu szablonu konfiguracji obrazu i obrazu.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Ustawianie uprawnień do grupy zasobów

Nadaj kreatorowi obrazów uprawnienie "współautor" do utworzenia obrazu w grupie zasobów. Bez tego kompilacja obrazu zakończy się niepowodzeniem. 

Wartość `--assignee` jest identyfikatorem rejestracji aplikacji dla usługi Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>Pobierz przykład szablonu konfiguracji obrazu

Do wypróbowania został utworzony szablon konfiguracji obrazu sparametryzowany. Pobierz przykładowy plik .json i skonfiguruj go przy tak z ustawionymi wcześniej zmiennymi.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

W tym przykładzie można zmodyfikować w `vi`terminalu za pomocą edytora tekstu, takiego jak .

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> W przypadku obrazu [źródłowego](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)należy zawsze określić `latest`wersję , której nie można użyć .
> Jeśli dodasz lub zmienisz grupę zasobów, do której obraz jest dystrybuowany, należy ustawić [uprawnienia](#set-permissions-on-the-resource-group) w grupie zasobów.
 
## <a name="create-the-image"></a>Tworzenie obrazu

Przesyłanie konfiguracji obrazu do usługi Konstruktora obrazów maszyn wirtualnych

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Po zakończeniu spowoduje to powrót wiadomości o powodach `Image Builder Configuration Template` do `$imageResourceGroup`konsoli i utworzenie w pliku . Ten zasób można zobaczyć w grupie zasobów w witrynie Azure portal, jeśli włączysz "Pokaż ukryte typy".

W tle Konstruktor obrazów utworzy również grupę zasobów przejściowych w ramach subskrypcji. Ta grupa zasobów jest używana dla kompilacji obrazu. Będzie w tym formacie:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Nie można usunąć bezpośrednio grupy zasobów przemieszczania. Najpierw usuń artefakt szablonu obrazu, co spowoduje usunięcie grupy zasobów przemieszczania.

Jeśli usługa zgłasza błąd podczas przesyłania szablonu konfiguracji obrazu:
-  Zapoznaj się z tymi krokami [rozwiązywania problemów.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) 
- Przed ponowieniem próbą przesłania należy usunąć szablon przy użyciu następującego fragmentu kodu.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Uruchamianie kompilacji obrazu
Rozpocznij proces tworzenia obrazu przy użyciu [az resource invoke-action](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Poczekaj, aż kompilacja zostanie ukończona. Może to potrwać około 15 minut.

Jeśli wystąpią jakiekolwiek błędy, zapoznaj się z tymi krokami [rozwiązywania problemów.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną przy użyciu utworzonego obrazu. Zastąp * \<hasło>* własnym hasłem `aibuser` na maszynie wirtualnej.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Weryfikowanie dostosowywania

Utwórz połączenie pulpitu zdalnego z maszyną wirtualną przy użyciu nazwy użytkownika i hasła ustawionego podczas tworzenia maszyny Wirtualnej. Wewnątrz maszyny Wirtualnej otwórz monit cmd i wpisz:

```console
dir c:\
```

Powinny być widoczne te dwa katalogi utworzone podczas dostosowywania obrazu:
- buildActions (akcją)
- buildArtifacts

## <a name="clean-up"></a>Czyszczenie

Po zakończeniu usuń zasoby.

### <a name="delete-the-image-builder-template"></a>Usuwanie szablonu konstruktora obrazów

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>Usuwanie grupy zasobów obrazu

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o składnikach pliku .json użytego w tym artykule, zobacz [Odwołanie do szablonu konstruktora obrazów](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
