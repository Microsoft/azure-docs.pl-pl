---
title: Przesłanianie punktu wejścia w wystąpieniu kontenera
description: Ustawianie wiersza polecenia w celu zastąpienia punktu wejścia w obrazie kontenera podczas wdrażania wystąpienia kontenera platformy Azure
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 5898decbf4108d48bb9e84019d659075b18fd043
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771087"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Ustawianie wiersza polecenia w wystąpieniu kontenera w celu zastąpienia domyślnej operacji wiersza polecenia

Podczas tworzenia wystąpienia kontenera opcjonalnie określ polecenie w celu zastąpienia domyślnej instrukcji wiersza polecenia w obrazie kontenera. To zachowanie jest podobne do `--entrypoint` argumentu wiersza polecenia . `docker run`

Podobnie jak [w przypadku](container-instances-environment-variables.md) ustawiania zmiennych środowiskowych dla wystąpień kontenerów, określenie początkowego wiersza polecenia jest przydatne w przypadku zadań wsadowych, w których należy dynamicznie przygotować każdy kontener przy użyciu konfiguracji specyficznej dla zadania.

## <a name="command-line-guidelines"></a>Wskazówki dotyczące wiersza polecenia

* Domyślnie wiersz polecenia określa pojedynczy *proces,* który uruchamia się bez powłoki w kontenerze. Na przykład wiersz polecenia może uruchamiać skrypt języka Python lub plik wykonywalny. Proces może określać dodatkowe parametry lub argumenty.

* Aby wykonać wiele poleceń, rozpocznij wiersz polecenia, ustawiając środowisko powłoki, które jest obsługiwane w systemie operacyjnym kontenera. Przykłady:

  |System operacyjny  |Powłoka domyślna  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Postępuj zgodnie z konwencjami powłoki, aby połączyć wiele poleceń do uruchomienia w sekwencji.

* W zależności od konfiguracji kontenera może być konieczne ustawienie pełnej ścieżki do pliku wykonywalnego wiersza polecenia lub argumentów.

* Ustaw odpowiednie zasady [ponownego uruchamiania](container-instances-restart-policy.md) dla wystąpienia kontenera w zależności od tego, czy wiersz polecenia określa zadanie długotrwałe, czy zadanie uruchamiane raz. Na przykład zasady ponownego uruchamiania programu `Never` lub są zalecane w przypadku zadania `OnFailure` uruchamianego raz. 

* Jeśli potrzebujesz informacji o domyślnym punkcie wejścia ustawionym w obrazie kontenera, użyj polecenia [docker image inspect.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

## <a name="command-line-syntax"></a>Składnia wiersza polecenia

Składnia wiersza polecenia różni się w zależności od interfejsu API platformy Azure lub narzędzia używanego do tworzenia wystąpień. W przypadku określenia środowiska powłoki należy również przestrzegać konwencji składni poleceń powłoki.

* [az container create][az-container-create] command: przekaż ciąg z `--command-line` parametrem . Przykład: `--command-line "python myscript.py arg1 arg2"` ).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell cmdlet: przekaż ciąg z `-Command` parametrem . Przykład: `-Command "echo hello"`.

* Azure Portal: we właściwości **przesłonięcia** polecenia konfiguracji kontenera podaj rozdzielaną przecinkami listę ciągów bez cudzysłowów. Przykład: `python, myscript.py, arg1, arg2` ). 

* Resource Manager szablonu lub pliku YAML albo jednego z zestawów Azure SDK: określ właściwość wiersza polecenia jako tablicę ciągów. Przykład: tablica JSON `["python", "myscript.py", "arg1", "arg2"]` w szablonie Resource Manager szablonu. 

  Jeśli znasz składnię pliku [Dockerfile,](https://docs.docker.com/engine/reference/builder/) ten format jest podobny do formatu *exec* instrukcji CMD.

### <a name="examples"></a>Przykłady

|    |  Interfejs wiersza polecenia platformy Azure   | Portal | Template | 
| ---- | ---- | --- | --- |
| **Pojedyncze polecenie** | `--command-line "python myscript.py arg1 arg2"` | **Przesłonięcie polecenia:**`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| **Wiele poleceń** | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Przesłonięcie polecenia:**`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Przykład interfejsu wiersza polecenia platformy Azure

Na przykład zmodyfikuj zachowanie obrazu [kontenera microsoft/aci-wordcount,][aci-wordcount] który analizuje tekst w *hamletze* Trzmyka, aby znaleźć najczęściej występujące wyrazy. Zamiast analizować *hamlet*, można ustawić wiersz polecenia, który wskazuje na inne źródło tekstu.

Aby wyświetlić dane wyjściowe kontenera [microsoft/aci-wordcount][aci-wordcount] podczas analizowania tekstu domyślnego, uruchom go za pomocą następującego polecenia [az container create.][az-container-create] Nie określono wiersza polecenia uruchamiania, więc uruchamiane jest domyślne polecenie kontenera. W celach ilustratora w tym przykładzie zmienne [środowiskowe](container-instances-environment-variables.md) są ustawiane tak, aby znaleźć 3 pierwsze wyrazy, które mają co najmniej pięć liter:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Gdy stan kontenera będzie wyświetlany jako *Terminated* (Zakończony) (użyj az [container show,][az-container-show] aby sprawdzić stan), wyświetl dziennik za pomocą dziennika [az container logs,][az-container-logs] aby wyświetlić dane wyjściowe.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Dane wyjściowe:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Teraz skonfiguruj drugi przykładowy kontener, aby przeanalizować inny tekst, określając inny wiersz polecenia. Skrypt języka Python wykonywany przez kontener *wordcount.py* akceptuje adres URL jako argument i przetwarza zawartość tej strony zamiast wartości domyślnej.

Na przykład, aby określić 3 pierwsze wyrazy, które mają co najmniej pięć liter w *listach Poto i Juliet:*

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Ponownie, gdy kontener zostanie *zakończony,* wyświetl dane wyjściowe, wyświetlając dzienniki kontenera:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Dane wyjściowe:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Następne kroki

Scenariusze oparte na zadaniach, takie jak przetwarzanie wsadowe dużego zestawu danych z kilkoma kontenerami, mogą korzystać z niestandardowych wierszy poleceń w czasie wykonywania. Aby uzyskać więcej informacji na temat uruchamiania kontenerów opartych na zadaniach, zobacz [Uruchamianie konteneryzowanych zadań przy użyciu zasad ponownego uruchamiania.](container-instances-restart-policy.md)

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
