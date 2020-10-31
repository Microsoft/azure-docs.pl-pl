---
title: Renderowanie sceny w chmurze
description: Samouczek — Renderowanie sceny programu Autodesk 3ds Max przy użyciu programu Arnold, usługi Batch Rendering Service oraz interfejsu wiersza polecenia platformy Azure
ms.topic: tutorial
ms.date: 03/05/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fbaa56ab444b9d686e5054a3668604bd40f7a262
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097708"
---
# <a name="tutorial-render-a-scene-with-azure-batch"></a>Samouczek: renderowanie sceny w usłudze Azure Batch 

Usługa Azure Batch oferuje możliwości renderowania w skali chmury z opłatami za użycie. Usługa Azure Batch obsługuje aplikacje służące do renderowania, w tym programy Autodesk Maya, 3ds Max, Arnold i V-Ray. W tym samouczku przedstawiono procedurę renderowania niewielkiej sceny przy użyciu usługi Batch i interfejsu wiersza polecenia platformy Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Przekazywanie sceny do usługi Azure Storage
> * Tworzenie puli usługi Batch na potrzeby renderowania
> * Renderowanie sceny z jedną ramką
> * Skalowanie puli i renderowanie sceny z wieloma ramkami
> * Pobieranie wyniku renderowania

Ten samouczek obejmuje renderowanie sceny programu 3ds Max przy użyciu usługi Batch oraz programu renderującego metodą śledzenia promieni, [Arnold](https://www.autodesk.com/products/arnold/overview). Pula usługi Batch używa obrazu witryny Azure Marketplace z wstępnie zainstalowanymi aplikacjami graficznymi i renderującymi, które udostępniają licencjonowanie na zasadzie płatności za użycie.

## <a name="prerequisites"></a>Wymagania wstępne

 - Aby korzystać z aplikacji renderujących w usłudze Batch z opłatami za użycie, potrzebujesz subskrypcji z płatnością zgodnie z rzeczywistym użyciem lub innej opcji zakupu platformy Azure. **Licencjonowanie na zasadzie płatności za użycie nie jest obsługiwane, jeśli korzystasz z bezpłatnej oferty platformy Azure, w ramach której otrzymujesz środki pieniężne.**

 - Przykładową scenę programu 3ds Max na potrzeby tego samouczka, przykładowy skrypt powłoki Bash oraz pliki konfiguracyjne w formacie JSON znajdziesz w witrynie [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/batch/render-scene). Scenę programu 3ds Max zawierają [pliki przykładowe dla programu Autodesk 3ds Max](https://download.autodesk.com/us/support/files/3dsmax_sample_files/2017/Autodesk_3ds_Max_2017_English_Win_Samples_Files.exe). (Pliki przykładowe programu Autodesk 3ds Max są dostępne w ramach licencji Creative Commons Attribution-NonCommercial-Share Alike. Informacje o prawach autorskich &copy; , Inc.)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten samouczek wymaga wersji 2.0.20 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-batch-account"></a>Tworzenie konta usługi Batch

Utwórz grupę zasobów, konto usługi Batch oraz połączone konto magazynu w ramach swojej subskrypcji, jeśli te elementy nie zostały jeszcze utworzone. 

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus2* .

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

Utwórz konto usługi Azure Storage w swojej grupie zasobów przy użyciu polecenia [az storage account create](/cli/azure/storage/account#az-storage-account-create). W ramach tego samouczka użyjesz konta magazynu do przechowywania wejściowej sceny programu 3ds Max oraz wyniku renderowania.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```
Utwórz konto usługi Batch przy użyciu polecenia [az batch account create](/cli/azure/batch/account#az-batch-account-create). Poniższy przykład tworzy konto usługi Batch o nazwie *mybatchaccount* w grupie zasobów *myResourceGroup* i łączy je z utworzonym kontem magazynu.  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

Aby tworzyć pule obliczeniowe i zadania oraz zarządzać nimi, należy uwierzytelnić się w usłudze Batch. Zaloguj się do konta za pomocą polecenia [az batch account login](/cli/azure/batch/account#az-batch-account-login). Po zalogowaniu Twoje polecenia `az batch` będą wykonywane w kontekście tego konta. W poniższym przykładzie zastosowano metodę uwierzytelniania klucza wspólnego, w której używana jest nazwa konta usługi Batch wraz z kluczem. Program Batch obsługuje również uwierzytelnianie za [Azure Active Directory](batch-aad-auth.md), aby uwierzytelniać poszczególnych użytkowników lub nienadzorowane aplikacje.

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```
## <a name="upload-a-scene-to-storage"></a>Przekazywanie sceny do usługi Azure Storage

Aby przekazać wejściową scenę do usługi Storage, należy najpierw uzyskać dostęp do konta magazynu i utworzyć docelowy kontener dla obiektów blob. Aby uzyskać dostęp do konta usługi Azure Storage, wyeksportuj zmienne środowiskowe `AZURE_STORAGE_KEY` i `AZURE_STORAGE_ACCOUNT`. W pierwszym poleceniu powłoki Bash pierwszy klucz konta jest pobierany przy użyciu polecenia [az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list). Po ustawieniu tych zmiennych środowiskowych Twoje polecenia usługi Storage będą wykonywane w kontekście tego konta.

```azurecli-interactive
export AZURE_STORAGE_KEY=$(az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup -o tsv --query [0].value)

export AZURE_STORAGE_ACCOUNT=mystorageaccount
```

Teraz należy utworzyć kontener obiektów blob na koncie magazynu, w którym przechowywane będą pliki sceny. W poniższym przykładzie użyto polecenia [az storage container create](/cli/azure/storage/container#az-storage-container-create) w celu utworzenia kontenera obiektów blob o nazwie *scenefiles* dostępnego publicznie do odczytu.

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name scenefiles
```

Pobierz scenę `MotionBlur-Dragon-Flying.max` z witryny [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max) do lokalnego katalogu roboczego. Przykład:

```azurecli-interactive
wget -O MotionBlur-DragonFlying.max https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max
```

Przekaż plik sceny z lokalnego katalogu roboczego do kontenera obiektów blob. W poniższym przykładzie użyto polecenia [az storage blob upload-batch](/cli/azure/storage/blob#az-storage-blob-upload-batch) umożliwiającego przekazanie wielu plików:

```azurecli-interactive
az storage blob upload-batch \
    --destination scenefiles \
    --source ./
```

## <a name="create-a-rendering-pool"></a>Tworzenie puli na potrzeby renderowania

Utwórz pulę usługi Batch na potrzeby renderowania za pomocą polecenia [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create). W tym przykładzie ustawienia puli są określane w pliku JSON. W bieżącej powłoce utwórz plik o nazwie *mypool.json* , a następnie skopiuj i wklej poniższą zawartość. Sprawdź, czy cały tekst został poprawnie skopiowany. Możesz też pobrać ten plik z witryny [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/mypool.json).


```json
{
  "id": "myrenderpool",
  "vmSize": "standard_d2_v2",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "batch",
      "offer": "rendering-windows2016",
      "sku": "rendering",
      "version": "1.3.8"
    },
    "nodeAgentSKUId": "batch.node.windows amd64"
  },
  "targetDedicatedNodes": 0,
  "targetLowPriorityNodes": 1,
  "enableAutoScale": false,
  "applicationLicenses":[
         "3dsmax",
         "arnold"
      ],
  "enableInterNodeCommunication": false 
}
```
Funkcja Batch obsługuje węzły dedykowane i węzły [o niskim priorytecie](batch-low-pri-vms.md) , a także można używać obu tych pul. Węzły dedykowane są zarezerwowane dla Twojej puli. Węzły o niskim priorytecie są oferowane w obniżonej cenie i korzystają z nadwyżek pojemności maszyn wirtualnych na platformie Azure. Węzły o niskim priorytecie staną się niedostępne, jeśli pojemność platformy Azure będzie niewystarczająca. 

Skonfigurowana pula zawiera jeden węzeł o niskim priorytecie z uruchomionym obrazem systemu Windows Server i oprogramowaniem wymaganym przez usługę Batch Rendering Service. Ta pula ma licencję umożliwiającą renderowanie z użyciem programów 3ds Max i Arnold. W kolejnym kroku pula będzie skalowana w celu zwiększenia liczby węzłów.

Utwórz pulę, przekazując plik JSON do polecenia `az batch pool create`:

```azurecli-interactive
az batch pool create \
    --json-file mypool.json
``` 
Udostępnienie puli może potrwać kilka minut. Aby wyświetlić stan puli, uruchom polecenie [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show). Następujące polecenie pobiera stan alokacji puli:

```azurecli-interactive
az batch pool show \
    --pool-id myrenderpool \
    --query "allocationState"
```

Wykonuj kolejne kroki, aby utworzyć zadanie i zadania podrzędne, w czasie gdy stan puli ulega zmianie. Pula jest całkowicie dostępna, gdy stan alokacji to `steady` i wszystkie węzły są uruchomione.  

## <a name="create-a-blob-container-for-output"></a>Tworzenie kontenera obiektów blob dla plików wyjściowych

W przykładach w tym samouczku każde zadanie podrzędne w ramach zadania renderowania tworzy plik wyjściowy. Przed zaplanowaniem zadania utwórz na koncie magazynu docelowy kontener obiektów blob dla plików wyjściowych. W poniższym przykładzie użyto polecenia [az storage container create](/cli/azure/storage/container#az-storage-container-create) w celu utworzenia kontenera o nazwie *job-myrenderjob* dostępnego publicznie do odczytu. 

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name job-myrenderjob
```

Do zapisania plików wyjściowych w kontenerze usługa Batch wymaga tokenu sygnatury dostępu współdzielonego (SAS). Utwórz ten token za pomocą polecenia [az storage account generate-sas](/cli/azure/storage/account#az-storage-account-generate-sas). Ten przykład tworzy token do zapisu do dowolnego kontenera obiektów BLOB na koncie, a token wygasa 15 listopada 2020:

```azurecli-interactive
az storage account generate-sas \
    --permissions w \
    --resource-types co \
    --services b \
    --expiry 2020-11-15
```

Zanotuj token zwrócony przez polecenie, który będzie podobny do następującego. Użyjesz tego tokenu w późniejszym kroku.

```
se=2020-11-15&sp=rw&sv=2019-09-24&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## <a name="render-a-single-frame-scene"></a>Renderowanie sceny z jedną ramką

### <a name="create-a-job"></a>Tworzenie zadania

Utwórz zadanie renderowania do uruchomienia w puli za pomocą polecenia [az batch job create](/cli/azure/batch/job#az-batch-job-create). Początkowo zadanie nie zawiera zadań podrzędnych.

```azurecli-interactive
az batch job create \
    --id myrenderjob \
    --pool-id myrenderpool
```

### <a name="create-a-task"></a>Tworzenie zadania podrzędnego

Za pomocą polecenia [az batch task create](/cli/azure/batch/task#az-batch-task-create) utwórz zadanie podrzędne w ramach zadania. W tym przykładzie ustawienia zadania podrzędnego są określane w pliku JSON. W bieżącej powłoce utwórz plik o nazwie *myrendertask.json* , a następnie skopiuj i wklej poniższą zawartość. Sprawdź, czy cały tekst został poprawnie skopiowany. Możesz też pobrać ten plik z witryny [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask.json).

To zadanie podrzędne zawiera polecenie programu 3ds Max, obejmujące renderowanie jednej ramki sceny *MotionBlur-DragonFlying.max* .

Zmodyfikuj elementy `blobSource` i `containerURL` w pliku JSON tak, aby zawierały nazwę Twojego konta magazynu i Twój token SAS. 

> [!TIP]
> Wartość `containerURL` zawiera na końcu token SAS i jest podobna do następującej:
> 
> ```
> https://mystorageaccount.blob.core.windows.net/job-myrenderjob/$TaskOutput?se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
> ```

```json
{
  "id": "myrendertask",
  "commandLine": "cmd /c \"%3DSMAX_2018%3dsmaxcmdio.exe -secure off -v:5 -rfw:0 -start:1 -end:1 -outputName:\"dragon.jpg\" -w 400 -h 300 MotionBlur-DragonFlying.max\"",
  "resourceFiles": [
    {
        "httpUrl": "https://mystorageaccount.blob.core.windows.net/scenefiles/MotionBlur-DragonFlying.max",
        "filePath": "MotionBlur-DragonFlying.max"
    }
  ],
    "outputFiles": [
        {
            "filePattern": "dragon*.jpg",
            "destination": {
                "container": {
                    "containerUrl": "https://mystorageaccount.blob.core.windows.net/job-myrenderjob/myrendertask/$TaskOutput?Add_Your_SAS_Token_Here"
                }
            },
            "uploadOptions": {
                "uploadCondition": "TaskSuccess"
            }
        }
    ],
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonAdmin"
    }
  }
}
```

Dodaj zadanie podrzędne do zadania, używając następującego polecenia:

```azurecli-interactive
az batch task create \
    --job-id myrenderjob \
    --json-file myrendertask.json
```

Usługa Batch zaplanuje to zadanie podrzędne, które zostanie uruchomione, gdy tylko będzie dostępny węzeł w puli.


### <a name="view-task-output"></a>Wyświetlanie danych wyjściowych zadania podrzędnego

Wykonanie zadania podrzędnego zajmuje kilka minut. Możesz wyświetlić szczegółowe informacje o zadaniu podrzędnym, używając polecenia [az batch task show](/cli/azure/batch/task#az-batch-task-show).

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id myrendertask
```

Zadanie podrzędne generuje plik *dragon0001.jpg* w węźle obliczeniowym i przekazuje go do kontenera *job-myrenderjob* na koncie magazynu. Aby wyświetlić plik wyjściowy, pobierz go z usługi Storage na komputer lokalny za pomocą polecenia [az storage blob download](/cli/azure/storage/blob#az-storage-blob-download).

```azurecli-interactive
az storage blob download \
    --container-name job-myrenderjob \
    --file dragon.jpg \
    --name dragon0001.jpg

```

Otwórz plik *dragon.jpg* na komputerze. Wyrenderowany obraz wygląda następująco:

![Wyrenderowana ramka 1 sceny dragon](./media/tutorial-rendering-cli/dragon-frame.png) 


## <a name="scale-the-pool"></a>Skalowanie puli

Teraz zmodyfikujesz pulę na potrzeby większego zadania renderowania, obejmującego większą liczbę ramek. Usługa Batch oferuje kilka sposobów skalowania zasobów obliczeniowych, w tym [skalowanie automatyczne](batch-automatic-scaling.md), umożliwiające dodawanie lub usuwanie węzłów stosownie do potrzeb. Na potrzeby tego prostego przykładu użyj polecenia [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize), aby zwiększyć liczbę węzłów o niskim priorytecie w puli do *6* :

```azurecli-interactive
az batch pool resize --pool-id myrenderpool --target-dedicated-nodes 0 --target-low-priority-nodes 6
```

Zmiana rozmiaru puli zajmuje kilka minut. Podczas trwania tego procesu skonfiguruj kolejne zadania podrzędne do uruchomienia w ramach istniejącego zadania renderowania.

## <a name="render-a-multiframe-scene"></a>Renderowanie sceny z wieloma ramkami

Tak jak w przykładzie z jedną ramką użyj polecenia [az batch task create](/cli/azure/batch/task#az-batch-task-create) w celu utworzenia zadań podrzędnych renderowania w ramach zadania *myrenderjob* . Tym razem określisz ustawienia zadań podrzędnych w pliku JSON o nazwie *myrendertask_multi.json* . (Plik można pobrać z witryny [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask_multi.json)). Każdy z sześciu zadań określa wiersz polecenia Arnold w celu renderowania jednej ramki z maksymalną sceną *MotionBlur-DragonFlying. Max* .

W bieżącej powłoce utwórz plik o nazwie *myrendertask_multi.json* , a następnie skopiuj i wklej zawartość pobranego pliku. Zmodyfikuj elementy `blobSource` i `containerURL` w pliku JSON, wprowadzając w nich nazwę Twojego konta magazynu i Twój token SAS. Pamiętaj o zmianie ustawień dla każdego z sześciu zadań podrzędnych. Zapisz plik, a następnie uruchom następujące polecenie w celu dodania zadań podrzędnych do kolejki:

```azurecli-interactive
az batch task create --job-id myrenderjob --json-file myrendertask_multi.json
```

### <a name="view-task-output"></a>Wyświetlanie danych wyjściowych zadania podrzędnego

Wykonanie zadania podrzędnego zajmuje kilka minut. Stan zadań podrzędnych możesz wyświetlić za pomocą polecenia [az batch task list](/cli/azure/batch/task#az-batch-task-list). Przykład:

```azurecli-interactive
az batch task list \
    --job-id myrenderjob \
    --output table
```

Możesz wyświetlić szczegółowe informacje o poszczególnych zadaniach podrzędnych, używając polecenia [az batch task show](/cli/azure/batch/task#az-batch-task-show). Przykład:

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id mymultitask1
```
 
Zadania generują pliki wyjściowe o nazwie *dragon0002.jpg*  -  *dragon0007.jpg* w węzłach obliczeniowych i przekazują je do kontenera *Job-myrenderjob* na koncie magazynu. Aby wyświetlić pliki wyjściowe, pobierz te pliki do folderu na komputerze lokalnym za pomocą polecenia [az storage blob download-batch](/cli/azure/storage/blob). Przykład:

```azurecli-interactive
az storage blob download-batch \
    --source job-myrenderjob \
    --destination .
```

Otwórz jeden z plików na komputerze. Wyrenderowana ramka 6 wygląda następująco:

![Wyrenderowana ramka 6 sceny dragon](./media/tutorial-rendering-cli/dragon-frame6.png) 


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Za pomocą polecenia [az group delete](/cli/azure/group#az-group-delete) można usunąć grupę zasobów, konto usługi Batch, pule oraz wszystkie pokrewne zasoby, gdy nie będą już potrzebne. Usuń zasoby w następujący sposób:

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przekazywanie scen do usługi Azure Storage
> * Tworzenie puli usługi Batch na potrzeby renderowania
> * Renderowanie sceny z jedną ramką przy użyciu programu Arnold
> * Skalowanie puli i renderowanie sceny z wieloma ramkami
> * Pobieranie wyniku renderowania

Aby dowiedzieć się więcej na temat renderowania w skali chmury, zobacz opcje usługi Batch Rendering Service. 

> [!div class="nextstepaction"]
> [Usługa renderowania dla usługi Batch](batch-rendering-service.md)
