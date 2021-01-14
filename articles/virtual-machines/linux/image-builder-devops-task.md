---
title: Zadanie DevOps usługi Azure Image Builder
description: Zadanie DevOps platformy Azure umożliwiające wstrzyknięcie artefaktów kompilacji do obrazu maszyny wirtualnej, dzięki czemu można zainstalować i skonfigurować aplikację oraz system operacyjny.
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 43447454b82b74c10b1d53c41c7883b0b9bef242
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196507"
---
# <a name="azure-image-builder-service-devops-task"></a>Zadanie DevOps usługi Azure Image Builder

W tym artykule pokazano, jak za pomocą zadania usługi Azure DevOps wstrzyknąć artefakty kompilacji do obrazu maszyny wirtualnej, aby można było zainstalować i skonfigurować aplikację oraz system operacyjny.

## <a name="devops-task-versions"></a>Wersje zadań DevOps
Istnieją dwa zadania usługi Azure VM Image Builder (AIB) DevOps:

* ["Stabilne" AIB zadanie](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder), dzięki czemu można wdrożyć najnowsze aktualizacje i funkcje, zezwolić klientom na ich testowanie przed przekazaniem ich do zadania "stabilnego", co około 1 tydzień później. 


* [Zadanie AIB "niestabilne"](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary)pozwala nam wprowadzić najnowsze aktualizacje i funkcje, zezwolić klientom na ich testowanie przed podwyższeniem poziomu do zadania "stabilne". Jeśli nie występują żadne zgłoszone problemy, a nasze dane telemetryczne nie wyświetlają żadnych problemów, w ciągu 1 tygodnia później zostanie podwyższyć poziom kodu zadania do "stabilnych". 

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [stabilne zadanie DevOps z Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder).
* Musisz mieć konto VSTS DevOps i utworzyć potok kompilacji
* Zarejestruj i Włącz wymagania funkcji konstruktora obrazów w subskrypcji używanej przez potoki:
    * [AZ PowerShell](../windows/image-builder-powershell.md#register-features)
    * [AZ CLI](../windows/image-builder.md#register-the-features)
    
* Tworzenie standardowego konta usługi Azure Storage w grupie zasobów obrazu źródłowego, można użyć innych kont grupy zasobów/magazynu. Konto magazynu służy do przenoszenia artefaktów kompilacji z zadania DevOps do obrazu.

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>Dodaj zadanie do potoku wydania

Wybieranie opcji  >  **Przeprowadź edycję** potoku wydania

Na agencie użytkownika wybierz opcję, *+* Aby dodać, a następnie wyszukaj **konstruktora obrazu**. Wybierz pozycję **Dodaj**.

Ustaw następujące właściwości zadania:

### <a name="azure-subscription"></a>Subskrypcja platformy Azure

Z menu rozwijanego wybierz subskrypcję, która ma być uruchamiana przez program Image Builder. Użyj tej samej subskrypcji, w której znajdują się obrazy źródłowe, i lokalizację, w której mają być dystrybuowane obrazy. Musisz autoryzować dostęp współautora konstruktora obrazów do subskrypcji lub grupy zasobów.

### <a name="resource-group"></a>Grupa zasobów

Użyj grupy zasobów, w której będzie przechowywany artefakt szablonu obrazu tymczasowego. Podczas tworzenia artefaktu szablonu tworzona jest dodatkowa grupa zasobów konstruktora obrazów tymczasowych `IT_<DestinationResourceGroup>_<TemplateName>_guid` . Tymczasowa Grupa zasobów przechowuje metadane obrazu, takie jak skrypty. Na końcu zadania jest usuwany artefakt szablonu obrazu i Grupa zasobów programu Temporary Image Builder.
 
### <a name="location"></a>Lokalizacja

Lokalizacja to region, w którym zostanie uruchomiony Konstruktor obrazów. Obsługiwane są tylko zestawy [regionów](../windows/image-builder-overview.md#regions) . Obrazy źródłowe muszą znajdować się w tej lokalizacji. Na przykład jeśli używasz galerii obrazów udostępnionych, replika musi znajdować się w tym regionie.

### <a name="managed-identity-required"></a>Tożsamość zarządzana (wymagana)
Konstruktor obrazów wymaga tożsamości zarządzanej, która używa do odczytywania źródłowych obrazów niestandardowych, łączenia się z usługą Azure Storage i tworzenia obrazów niestandardowych. Więcej informacji można znaleźć [tutaj](./image-builder-overview.md#permissions).

### <a name="vnet-support"></a>Obsługa sieci wirtualnej

Obecnie zadanie DevOps nie obsługuje określania istniejącej podsieci, ale jest to zaplanowanie, ale jeśli chcesz używać istniejącej sieci wirtualnej, możesz użyć szablonu ARM z szablonem konstruktora obrazów zagnieżdżonym w programie, zobacz przykłady szablonów konstruktora obrazów systemu Windows na tym, jak to możliwe, lub użyj polecenia [AZ AIB PowerShell](../windows/image-builder-powershell.md).

### <a name="source"></a>Element źródłowy

Obrazy źródłowe muszą być obsługiwane w programie Image Builder OSs. Możesz wybrać istniejące obrazy niestandardowe w tym samym regionie, w którym jest uruchomiony Konstruktor obrazów:
* Obraz zarządzany — należy przekazać identyfikator zasobu, na przykład:
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Galeria udostępnionych obrazów platformy Azure — należy przekazać identyfikator resourceId wersji obrazu, na przykład:
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    Jeśli potrzebujesz uzyskać najnowszą wersję galerii obrazów udostępnionych, możesz mieć zadanie AZ PowerShell lub AZ CLI, aby uzyskać najnowszą wersję i ustawić zmienną DevOps. Użyj zmiennej w zadaniu AZ VM Image Builder DevOps. Aby uzyskać więcej informacji, zobacz [przykłady](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery).

* Transakcji Obraz podstawowy istnieje lista rozwijana popularnych obrazów, w których zawsze będzie używana najnowsza wersja obsługiwanego systemu operacyjnego. 

    Jeśli obraz podstawowy nie znajduje się na liście, możesz określić dokładny obraz przy użyciu `Publisher:Offer:Sku` .

    Wersja obrazu podstawowego (opcjonalnie) — możesz podać wersję obrazu, który ma być używany, wartość domyślna to `latest` .

### <a name="customize"></a>Dostosowywanie

#### <a name="provisioner"></a>Provisioner

Początkowo obsługiwane są dwa narzędzia do dostosowywania — **Shell** i **PowerShell**. Obsługiwane są tylko wbudowane. Jeśli chcesz pobrać skrypty, możesz przekazać polecenia wbudowane, aby to zrobić.

W przypadku systemu operacyjnego wybierz opcję PowerShell lub Shell.

#### <a name="windows-update-task"></a>Windows Update, zadanie

W przypadku tylko systemu Windows zadanie jest uruchamiane Windows Update na końcu dostosowań. Obsługuje wymagane ponownych uruchomień.

Następująca Windows Update konfiguracja jest wykonywana:
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
Instaluje ważne i zalecane aktualizacje systemu Windows, które nie są w wersji zapoznawczej.

#### <a name="handling-reboots"></a>Obsługa ponownych uruchomień
Obecnie zadanie DevOps nie obsługuje ponownego uruchamiania kompilacji systemu Windows, jeśli spróbujesz ponownie uruchomić program przy użyciu kodu programu PowerShell, kompilacja zakończy się niepowodzeniem. Można jednak użyć kodu, aby ponownie uruchomić kompilacje systemu Linux.

#### <a name="build-path"></a>Ścieżka kompilacji

Zadanie zostało zaprojektowane, aby można było wstrzyknąć do obrazu artefakty wydania DevOps Build. Aby to zrobić, należy skonfigurować potok kompilacji. W instalatorze potoku wydania należy dodać repozytorium artefaktów kompilacji.

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="Wybierz pozycję Dodaj artefakt w potoku wydania.":::

Wybierz przycisk **ścieżka kompilacji** , aby wybrać folder kompilacji, który ma zostać umieszczony na obrazie. Zadanie Konstruktor obrazów kopiuje wszystkie pliki i katalogi w ramach tej funkcji. Gdy obraz jest tworzony, Konstruktor obrazów wdraża pliki i katalogi w różnych ścieżkach, w zależności od systemu operacyjnego.

> [!IMPORTANT]
> Podczas dodawania artefaktu repozytorium może zostać znaleziony katalog poprzedzony podkreśleniem *_*. Podkreślenie może powodować problemy z poleceniami wbudowanymi. Użyj odpowiednich cudzysłowów w poleceniach.
> 

W poniższym przykładzie wyjaśniono, jak to działa:

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="Struktura katalogów pokazująca hierarchię.":::


* System Windows — pliki znajdują się w `C:\` . Tworzony jest katalog o nazwie, `buildArtifacts` który zawiera `webapp` katalog.

* System Linux — pliki istnieją w systemie  `/tmp` . `webapp`Zostanie utworzony katalog obejmujący wszystkie pliki i katalogi. Pliki należy przenieść z tego katalogu. W przeciwnym razie zostaną usunięte, ponieważ znajdują się w katalogu tymczasowym.

#### <a name="inline-customization-script"></a>Wbudowany skrypt dostosowania

* Windows — możesz wprowadzić polecenia wbudowane programu PowerShell oddzielone przecinkami. Jeśli chcesz uruchomić skrypt w katalogu kompilacji, możesz użyć:

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

* Komputery z systemem Linux — w systemie Linux artefakty kompilacji są umieszczane w `/tmp` katalogu. Jednak w wielu systemach OSs systemu Linux po ponownym uruchomieniu zostanie usunięta zawartość katalogu/tmp. Jeśli chcesz, aby artefakty istniały w obrazie, należy utworzyć inny katalog i skopiować je.  Na przykład:

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    Jeśli jesteś prawidłowym użyciem katalogu "/tmp", możesz użyć poniższego kodu do wykonania skryptu.
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>Co się stanie z artefaktami kompilacji po skompilowaniu obrazu?

> [!NOTE]
> Konstruktor obrazów nie usuwa automatycznie artefaktów kompilacji, dlatego zdecydowanie zaleca się, aby zawsze mieć kod do usuwania artefaktów kompilacji.
> 

* Windows-Image Builder służy do wdrażania plików w `c:\buildArtifacts` katalogu. Katalog jest utrwalany należy usunąć katalog. Można go usunąć w skrypcie wykonywanym przez użytkownika. Na przykład:

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux — artefakty kompilacji są umieszczane w `/tmp` katalogu. Jednak w wielu systemach OSs systemu Linux po ponownym uruchomieniu `/tmp` zawartość katalogu zostanie usunięta. Zalecane jest, aby usunąć zawartość z kodu, a nie polegać na systemie operacyjnym, aby usunąć zawartość. Na przykład:

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>Łączna długość kompilacji obrazu

Nie można jeszcze zmienić długości łącznej w zadaniu potoku DevOps. Używa domyślnie 240 minut. Jeśli chcesz zwiększyć [buildTimeoutInMinutes](./image-builder-json.md#properties-buildtimeoutinminutes), możesz użyć polecenia AZ CLI w potoku wydania. Skonfiguruj zadanie, aby skopiować szablon i przesłać go. Aby zapoznać się z przykładem, zobacz to [rozwiązanie](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder)lub użyj polecenia AZ PowerShell.


#### <a name="storage-account"></a>Konto magazynu

Wybierz konto magazynu utworzone w ramach wymagań wstępnych. Jeśli nie widzisz go na liście, Konstruktor obrazów nie ma uprawnień do tego.

Po rozpoczęciu kompilacji, Konstruktor obrazów utworzy kontener o nazwie `imagebuilder-vststask` . Kontener to miejsce, w którym są przechowywane artefakty kompilacji z repozytorium.

> [!NOTE]
> Należy ręcznie usunąć konto magazynu lub kontener po każdej kompilacji.
>

### <a name="distribute"></a>Rozłóż

Istnieją 3 typy dystrybucji obsługiwane.

#### <a name="managed-image"></a>Obraz zarządzany

* Identyfikator
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* Lokalizacje

#### <a name="azure-shared-image-gallery"></a>Galeria udostępnionych obrazów systemu Azure

Galeria obrazów udostępnionych **musi** już istnieć.

* Identyfikator 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* Regiony: lista regionów, rozdzielonych przecinkami. Na przykład Zachodnie, wschodnie, środkowe

#### <a name="vhd"></a>VHD

Nie można przekazać żadnych wartości do tego, Konstruktor obrazów emituje dysk VHD do grupy zasobów konstruktora obrazów tymczasowych, `IT_<DestinationResourceGroup>_<TemplateName>` w kontenerze *VHD* . Po uruchomieniu kompilacji wydania Konstruktor obrazów emituje dzienniki. Po zakończeniu zostanie wyemitowany adres URL dysku VHD.

### <a name="optional-settings"></a>Ustawienia opcjonalne

* [Rozmiar maszyny wirtualnej](image-builder-json.md#vmprofile) — rozmiar maszyny wirtualnej można zastąpić wartością domyślną *Standard_D1_v2*. Można przesłonić, aby zmniejszyć łączny czas dostosowania lub utworzyć obrazy, które zależą od określonych rozmiarów maszyn wirtualnych, takich jak GPU/HPC itd.

## <a name="how-it-works"></a>Jak to działa

Podczas tworzenia wydania zadanie tworzy kontener na koncie magazynu o nazwie *imagebuilder-vststask*. Zips i przekazuje artefakty kompilacji i tworzy token sygnatury dostępu współdzielonego dla pliku zip.

Zadanie używa właściwości przesłanych do zadania w celu utworzenia artefaktu szablonu konstruktora obrazu. Zadanie wykonuje następujące czynności:
* Pobiera plik zip artefaktu kompilacji i wszystkie inne skojarzone skrypty. Pliki są zapisywane na koncie magazynu w tymczasowej grupie zasobów konstruktora obrazu `IT_<DestinationResourceGroup>_<TemplateName>` .
* Tworzy wstępnie ustalony szablon *T_* i 10-cyfrowy monotoniczny Integer. Szablon jest zapisywany w wybranej grupie zasobów. Szablon istnieje na czas trwania kompilacji w grupie zasobów. 

Przykładowe dane wyjściowe:

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

Po uruchomieniu kompilacji obrazu stan przebiegu jest raportowany w dziennikach wydań:

```text
starting run template...
```

Po zakończeniu kompilacji obrazu zobaczysz dane wyjściowe podobne do następującego:

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

Szablon obrazu i `IT_<DestinationResourceGroup>_<TemplateName>` został usunięty.

Możesz pobrać zmienną VSTS "$ (imageUri)" i użyć jej w następnym zadaniu lub po prostu użyć wartości i utworzyć maszynę wirtualną.

## <a name="output-devops-variables"></a>Wyjściowe zmienne DevOps

Zapisana/oferta/jednostka SKU/wersja obrazu źródłowej witryny Marketplace:
* $ (pirPublisher)
* $ (pirOffer)
* $ (pirSku)
* $ (pirVersion)

URI obrazu — identyfikator zasobu rozproszonego obrazu:
* $ (imageUri)

## <a name="faq"></a>Często zadawane pytania

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>Czy mogę użyć istniejącego szablonu obrazu, który został już utworzony, poza DevOps?

Obecnie nie.

### <a name="can-i-specify-the-image-template-name"></a>Czy mogę określić nazwę szablonu obrazu?

Nie. Zostanie użyta unikatowa nazwa szablonu, a następnie usunięta.

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>Konstruktor obrazów nie powiódł się. Jak mogę rozwiązać problem?

Jeśli wystąpi błąd kompilacji, zadanie DevOps nie usuwa tymczasowej grupy zasobów. Możesz uzyskać dostęp do tymczasowej grupy zasobów zawierającej dziennik dostosowania kompilacji.

Zostanie wyświetlony komunikat o błędzie w dzienniku DevOps zadania konstruktora obrazów maszyn wirtualnych i zapoznaj się z lokalizacją dostosowywać. log. Na przykład:

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="Przykładowy błąd zadania DevOps, który zawiera błąd.":::

Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz [Rozwiązywanie problemów z usługą Azure Image Builder](image-builder-troubleshoot.md). 

Po zbadaniu błędu można usunąć grupę zasobów tymczasowych. Najpierw usuń artefakt zasobu szablonu obrazu. Artefakt ma prefiks *T_* i znajduje się w dzienniku kompilacji zadań DevOps:

```text
...
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
...
template name:  t_1556938436xxx
...

```

Artefakt zasobu szablonu obrazu znajduje się w grupie zasobów określonej początkowo w zadaniu. Po zakończeniu rozwiązywania problemów Usuń artefakt. W przypadku usuwania przy użyciu Azure Portal w grupie zasobów wybierz pozycję **Pokaż ukryte typy**, aby wyświetlić artefakt.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Image Builder](image-builder-overview.md).