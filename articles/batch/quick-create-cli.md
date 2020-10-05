---
title: Szybki Start — uruchamianie pierwszego zadania usługi Batch za pomocą interfejsu wiersza polecenia platformy Azure
description: Szybka nauka tworzenia konta usługi Batch i uruchamiania zadania wsadowego za pomocą interfejsu wiersza polecenia platformy Azure.
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8824d4485167955dd1b928bc57381b2e6b672c5d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88213102"
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>Szybki start: uruchamianie pierwszego zadania usługi Batch za pomocą interfejsu wiersza polecenia platformy Azure

Rozpocznij pracę z Azure Batch przy użyciu interfejsu wiersza polecenia platformy Azure w celu utworzenia konta usługi Batch, puli węzłów obliczeniowych (maszyn wirtualnych) i zadania, które uruchamiają zadania w puli. Każde przykładowe zadanie podrzędne uruchamia podstawowe polecenie w jednym z węzłów puli.

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. Po ukończeniu tego przewodnika Szybki start będziesz rozumieć kluczowe pojęcia związane z usługą Batch, co pozwoli na wypróbowanie tej usługi z bardziej realistycznymi obciążeniami na większą skalę.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.20 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie *QuickstartBatch-RG* w lokalizacji *eastus2* .

```azurecli-interactive
az group create \
    --name QuickstartBatch-rg \
    --location eastus2
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Konto usługi Azure Storage możesz połączyć z kontem usługi Batch. Chociaż nie jest ono wymagane na potrzeby tego przewodnika Szybki start, konto magazynu jest przydatne do wdrażania aplikacji oraz przechowywania danych wejściowych i wyjściowych w przypadku większości rzeczywistych obciążeń. Utwórz konto magazynu w swojej grupie zasobów przy użyciu polecenia [az storage account create](/cli/azure/storage/account#az-storage-account-create).

```azurecli-interactive
az storage account create \
    --resource-group QuickstartBatch-rg \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>Tworzenie konta usługi Batch

Utwórz konto usługi Batch przy użyciu polecenia [az batch account create](/cli/azure/batch/account#az-batch-account-create). Konto jest wymagane, aby tworzyć zasoby obliczeniowe (pule węzłów obliczeniowych) i zadania usługi Batch.

Poniższy przykład tworzy konto wsadowe o nazwie *mybatchaccount* w *QuickstartBatch-RG*i łączy utworzone konto magazynu.  

```azurecli-interactive
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group QuickstartBatch-rg \
    --location eastus2
```

Aby tworzyć pule obliczeniowe i zadania oraz zarządzać nimi, należy uwierzytelnić się w usłudze Batch. Zaloguj się do konta za pomocą polecenia [az batch account login](/cli/azure/batch/account#az-batch-account-login). Po zalogowaniu Twoje polecenia `az batch` będą wykonywane w kontekście tego konta.

```azurecli-interactive
az batch account login \
    --name mybatchaccount \
    --resource-group QuickstartBatch-rg \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>Tworzenie puli węzłów obliczeniowych

Teraz, gdy masz już konto usługi Batch, utwórz przykładową pulę węzłów obliczeniowych z systemem Linux przy użyciu polecenia [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create). Poniższy przykład tworzy pulę o nazwie *mypool* składającą się z 2 węzłów o rozmiarze *Standard_A1_v2* z systemem Ubuntu 16.04 LTS. Sugerowany rozmiar węzłów oferuje dobry kompromis między wydajnością a kosztem na potrzeby tego krótkiego przykładu.
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

Usługa Batch tworzy pulę natychmiast, ale przydzielenie i uruchomienie węzłów obliczeniowych może potrwać kilka minut. W tym czasie pula będzie miał stan `resizing`. Aby wyświetlić stan puli, uruchom polecenie [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show). To polecenie powoduje wyświetlenie wszystkich właściwości puli i umożliwia wykonywanie zapytań o określone właściwości. Następujące polecenie pobiera stan alokacji puli:

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

Wykonuj kolejne kroki, aby utworzyć zadanie i zadania podrzędne, w czasie gdy stan puli ulega zmianie. Pula jest gotowa do uruchamiania zadań podrzędnych, gdy stan alokacji to `steady` i wszystkie węzły są uruchomione.

## <a name="create-a-job"></a>Tworzenie zadania

Teraz, gdy masz już pulę, utwórz zadanie, które zostanie w niej uruchomione. Zadanie usługi Batch to logiczna grupa zawierająca co najmniej jedno zadanie podrzędne. Zadanie uwzględnia wspólne ustawienia zadań podrzędnych, takie jak priorytet i pula, w której zadania podrzędne mają być uruchamiane. Utwórz zadanie usługi Batch za pomocą polecenia [az batch job create](/cli/azure/batch/job#az-batch-job-create). Poniższy przykład tworzy zadanie *myjob* w puli *mypool*. Początkowo zadanie nie zawiera zadań podrzędnych.

```azurecli-interactive
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>Tworzenie zadań

Teraz za pomocą polecenia [az batch task create](/cli/azure/batch/task#az-batch-task-create) utwórz kilka zadań podrzędnych, które zostaną uruchomione w ramach zadania. W tym przykładzie utworzysz cztery identyczne zadania podrzędne. Każde zadanie podrzędne uruchamia polecenie `command-line` w celu wyświetlenia zmiennych środowiskowych usługi Batch w węźle obliczeniowym, a następnie czeka 90 sekund. Podczas korzystania z usługi Batch aplikację lub skrypt określa się w tym wierszu polecenia. Usługa Batch udostępnia kilka sposobów wdrażania aplikacji i skryptów w węzłach obliczeniowych.

Poniższy skrypt powłoki Bash tworzy 4 równoległe zadania podrzędne (od *mytask1* do *mytask4*).

```azurecli-interactive
for i in {1..4}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c 'printenv | grep AZ_BATCH; sleep 90s'"
done
```

Dane wyjściowe polecenia zawierają ustawienia dla każdego zadania podrzędnego. Usługa Batch dystrybuuje zadania podrzędne do węzłów obliczeniowych.

## <a name="view-task-status"></a>Wyświetlanie stanu zadania podrzędnego

Po utworzeniu zadania podrzędnego usługa Batch umieszcza je w kolejce w celu uruchomienia w puli. Zadanie podrzędne zostanie uruchomione, gdy odpowiedni węzeł stanie się dostępny.

Stan zadań podrzędnych usługi Batch możesz wyświetlić za pomocą polecenia [az batch task show](/cli/azure/batch/task#az-batch-task-show). Poniższy przykład spowoduje wyświetlenie szczegółowych informacji o zadaniu podrzędnym *mytask1* uruchomionym w jednym z węzłów puli.

```azurecli-interactive
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

Dane wyjściowe polecenia zawierają wiele informacji, ale zwróć uwagę na kod `exitCode` wiersza polecenia zadania podrzędnego oraz identyfikator `nodeId`. Kod `exitCode` równy 0 wskazuje, że wiersz polecenia zadania podrzędnego zakończył się pomyślnie. Wartość `nodeId` wskazuje identyfikator węzła puli, w którym uruchomiono zadanie podrzędne.

## <a name="view-task-output"></a>Wyświetlanie danych wyjściowych zadania podrzędnego

Aby wyświetlić listę plików utworzonych przez zadanie podrzędne w węźle obliczeniowym, użyj polecenia [az batch task file list](/cli/azure/batch/task). Następujące polecenie wyświetla listę plików utworzonych przez zadanie podrzędne *mytask1*:

```azurecli-interactive
az batch task file list \
    --job-id myjob \
    --task-id mytask1 \
    --output table
```

Dane wyjściowe będą podobne do następujących:

```
Name        URL                                                                                         Is Directory      Content Length
----------  ------------------------------------------------------------------------------------------  --------------  ----------------
stdout.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stdout.txt  False                  695
certs       https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/certs       True
wd          https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/wd          True
stderr.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stderr.txt  False                     0

```

Aby pobrać jeden z plików wyjściowych do katalogu lokalnego, użyj polecenia [az batch task file download](/cli/azure/batch/task). W tym przykładzie dane wyjściowe zadania podrzędnego znajdują się w pliku `stdout.txt`.

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

Zawartość pliku `stdout.txt` możesz wyświetlić w edytorze tekstów. Zawartość obejmuje zmienne środowiskowe usługi Azure Batch, które są ustawione w węźle. Podczas tworzenia własnych zadań usługi Batch możesz odwoływać się do tych zmiennych środowiskowych w wierszach polecenia zadań podrzędnych oraz w aplikacjach i skryptach uruchamianych przez wiersze polecenia. Na przykład:

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2.batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjobl
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-257509324_2-20180703t215033z
AZ_BATCH_POOL_ID=mypool
AZ_BATCH_TASK_ID=mytask1
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz kontynuować naukę z samouczkami i przykładami usługi Batch, skorzystaj z konta usługi Batch i powiązanego konta magazynu, które zostały utworzone w tym przewodniku Szybki start. Za samo konto usługi Batch nie są naliczane opłaty.

Opłaty za pule są naliczane, dopóki węzły działają, nawet jeśli nie zostały zaplanowane żadne zadania. Jeśli pula nie jest już potrzebna, usuń ją za pomocą polecenia [az batch pool delete](/cli/azure/batch/pool#az-batch-pool-delete). W przypadku usunięcia puli usuwane są również wszystkie dane wyjściowe zadań podrzędnych w węzłach.

```azurecli-interactive
az batch pool delete --pool-id mypool
```

Za pomocą polecenia [az group delete](/cli/azure/group#az-group-delete) można usunąć grupę zasobów, konto usługi Batch, pule oraz wszystkie pokrewne zasoby, gdy nie będą już potrzebne. Usuń zasoby w następujący sposób:

```azurecli-interactive
az group delete --name QuickstartBatch-rg
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono konto usługi Batch, pulę usługi Batch i zadanie usługi Batch. Zadanie uruchomiło przykładowe zadania podrzędne i wyświetlono dane wyjściowe utworzone w jednym z węzłów. Teraz, gdy już rozumiesz kluczowe pojęcia związane z usługą Batch, możesz wypróbować tę usługę z bardziej realistycznymi obciążeniami na większą skalę. Aby dowiedzieć się więcej o usłudze Azure Batch, kontynuuj naukę w ramach samouczków usługi Azure Batch.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Batch](./tutorial-parallel-dotnet.md)
