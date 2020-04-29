---
title: Tworzenie maszyny wirtualnej z systemem Linux przy użyciu programu Azure Image Builder (wersja zapoznawcza)
description: Tworzenie maszyny wirtualnej z systemem Linux przy użyciu programu Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: 0d36d7db4d85ece8de77040925c535305951562b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066685"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Wersja zapoznawcza: Tworzenie maszyny wirtualnej z systemem Linux przy użyciu usługi Azure Image Builder

W tym artykule opisano sposób tworzenia dostosowanego obrazu systemu Linux przy użyciu narzędzia Azure Image Builder i interfejsu wiersza polecenia platformy Azure. W przykładzie w tym artykule są stosowane trzy różne [konfiguratorzy](image-builder-json.md#properties-customize) dostosowywania obrazu:

- Shell (ScriptUri) — pobiera i uruchamia [skrypt powłoki](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Powłoka (wbudowana) — uruchamia określone polecenia. W tym przykładzie polecenia wbudowane obejmują tworzenie katalogu i aktualizowanie systemu operacyjnego.
- Plik — kopiuje [plik z usługi GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) do katalogu na maszynie wirtualnej.

Można również określić `buildTimeoutInMinutes`. Wartość domyślna to 240 minut i można zwiększyć czas kompilacji, aby umożliwić dłuższe wykonywanie kompilacji.

Aby skonfigurować obraz, będziemy używać szablonu przykład. JSON. Używany plik JSON jest tutaj: [helloImageTemplateLinux. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

az provider show -n Microsoft.Storage | grep registrationState
```

Jeśli nie powiedzie się, uruchom następujące polecenie:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="setup-example-variables"></a>Przykładowe zmienne konfiguracji

Będziemy wielokrotnie używać niektórych informacji, więc utworzymy pewne zmienne do przechowywania tych informacji.


```console
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Utwórz zmienną dla identyfikatora subskrypcji. Można to zrobić za pomocą `az account show | grep id`polecenia.

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Utwórz grupę zasobów.
Służy do przechowywania artefaktu szablonu konfiguracji obrazu i obrazu.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Ustawianie uprawnień do grupy zasobów
Nadaj elementowi "Współautor" konstruktora obrazu uprawnienia do tworzenia obrazu w grupie zasobów. Bez odpowiednich uprawnień kompilacja obrazu zakończy się niepowodzeniem. 

`--assignee` Wartość to identyfikator rejestracji aplikacji dla usługi Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Pobierz przykład szablonu

Szablon konfiguracji przykładowego obrazu z parametrami został utworzony do użycia. Pobierz plik Sample. JSON i skonfiguruj go przy użyciu ustawionych wcześniej zmiennych.

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

W razie konieczności można zmodyfikować ten przykład. JSON. Na przykład można zwiększyć wartość, `buildTimeoutInMinutes` aby zezwolić na dłuższe kompilacje. Plik można edytować w Cloud Shell przy użyciu edytora tekstu, takiego jak `vi`.

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> W przypadku obrazu źródłowego należy zawsze [określić wersję](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), której nie można użyć `latest`.
>
> W przypadku dodania lub zmiany grupy zasobów, w której jest dystrybuowany obraz, należy upewnić się, że [uprawnienia zostały ustawione dla grupy zasobów](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Prześlij konfigurację obrazu
Prześlij konfigurację obrazu do usługi Konstruktor obrazów maszyn wirtualnych

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Jeśli zakończyło się pomyślnie, zwróci komunikat o powodzeniu i utworzysz artefakt szablonu konfiguracji konstruktora obrazów w $imageResourceGroup. Grupę zasobów można wyświetlić w portalu, jeśli zostanie włączona wartość "Pokaż ukryte typy".

Ponadto w tle Konstruktor obrazów tworzy tymczasową grupę zasobów w ramach subskrypcji. Konstruktor obrazów używa tymczasowej grupy zasobów dla kompilacji obrazu. Nazwa grupy zasobów będzie w tym formacie: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Nie usuwaj bezpośrednio tymczasowej grupy zasobów. Usunięcie artefaktu szablonu obrazu spowoduje automatyczne usunięcie grupy zasobów tymczasowych. Aby uzyskać więcej informacji, zobacz sekcję [oczyszczanie](#clean-up) na końcu tego artykułu.

Jeśli usługa zgłasza błąd podczas przekazywania szablonu konfiguracji obrazu, zobacz kroki [rozwiązywania problemów](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) . Należy również usunąć szablon przed ponowną próbą przesłania kompilację. Aby usunąć szablon:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Uruchom kompilację obrazu

Uruchom kompilację obrazu.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Poczekaj, aż kompilacja zostanie ukończona, w tym przykładzie może upłynąć do 10-15 minut.

W przypadku wystąpienia błędów zapoznaj się z tymi krokami [rozwiązywania problemów](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) .


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną przy użyciu skompilowanego obrazu.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Pobierz adres IP z danych wyjściowych tworzenia maszyny wirtualnej i użyj go do połączenia SSH z maszyną wirtualną.

```bash
ssh azureuser@<pubIp>
```

Obraz został dostosowany wraz z komunikatem dnia zaraz po nawiązaniu połączenia SSH.

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Wpisz `exit` po zakończeniu połączenia SSH.

## <a name="check-the-source"></a>Sprawdź Źródło

W szablonie konstruktora obrazów w obszarze "właściwości" pojawi się obraz źródłowy, uruchamiany skrypt dostosowania i miejsce, w którym jest dystrybuowany.

```bash
cat helloImageTemplateLinux.json
```

Aby uzyskać szczegółowe informacje na temat tego pliku JSON, zobacz [Dokumentacja szablonu konstruktora obrazów](image-builder-json.md)

## <a name="clean-up"></a>Czyszczenie

Gdy skończysz, możesz usunąć zasoby.

Usuń szablon Konstruktor obrazów.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Usuń grupę zasobów obrazu.

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o składnikach pliku JSON używanego w tym artykule, zobacz [Dokumentacja szablonu konstruktora obrazów](image-builder-json.md).
