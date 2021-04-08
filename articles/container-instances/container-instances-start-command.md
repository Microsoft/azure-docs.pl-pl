---
title: Przesłoń punkt wejścia w wystąpieniu kontenera
description: Ustaw wiersz polecenia, aby przesłonić punkt wejścia w obrazie kontenera podczas wdrażania wystąpienia kontenera platformy Azure
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 23221de3dc91c37c2e6fb96489539d3954efcd87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169633"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Ustaw wiersz polecenia w wystąpieniu kontenera w celu zastąpienia domyślnej operacji wiersza polecenia

Podczas tworzenia wystąpienia kontenera opcjonalnie należy określić polecenie, aby zastąpić domyślną instrukcję wiersza polecenia rozszerzania do obrazu kontenera. To zachowanie jest podobne do `--entrypoint` argumentu wiersza polecenia do `docker run` .

Podobnie jak w przypadku ustawiania [zmiennych środowiskowych](container-instances-environment-variables.md) dla wystąpień kontenerów, określenie uruchamiania wiersza polecenia jest przydatne w przypadku zadań wsadowych, w których konieczne jest przygotowanie każdego kontenera dynamicznie z konfiguracją specyficzną dla zadania.

## <a name="command-line-guidelines"></a>Wskazówki dotyczące wiersza polecenia

* Domyślnie wiersz polecenia określa *pojedynczy proces, który jest uruchamiany bez powłoki* w kontenerze. Na przykład wiersz polecenia może uruchomić skrypt w języku Python lub plik wykonywalny. Proces może określać dodatkowe parametry lub argumenty.

* Aby wykonać wiele poleceń, należy rozpocząć pracę z wierszem polecenia przez ustawienie środowiska powłoki obsługiwanego w systemie operacyjnym kontenera. Przykłady:

  |System operacyjny  |Powłoka domyślna  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Postępuj zgodnie z konwencjami powłoki, aby połączyć wiele poleceń do uruchomienia w sekwencji.

* W zależności od konfiguracji kontenera może być konieczne ustawienie pełnej ścieżki do pliku wykonywalnego lub argumentów wiersza polecenia.

* Ustaw odpowiednie [zasady ponownego uruchamiania](container-instances-restart-policy.md) dla wystąpienia kontenera, w zależności od tego, czy wiersz polecenia określa długotrwałe zadanie czy zadanie uruchamiania jednokrotnego. Na przykład zasady ponownego uruchamiania `Never` lub `OnFailure` są zalecane dla zadania uruchamiania jednokrotnego. 

* Jeśli potrzebujesz informacji o domyślnym zestawie EntryPoint w obrazie kontenera, użyj polecenia [Docker Image inspekcji](https://docs.docker.com/engine/reference/commandline/image_inspect/) .

## <a name="command-line-syntax"></a>Składnia wiersza polecenia

Składnia wiersza polecenia różni się w zależności od interfejsu API platformy Azure lub narzędzia używanego do tworzenia wystąpień. W przypadku określenia środowiska powłoki należy również przestrzegać Konwencji składni polecenia powłoki.

* [AZ Container Create][az-container-create] — polecenie: Przekaż ciąg z `--command-line` parametrem. Przykład: `--command-line "python myscript.py arg1 arg2"` ).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell polecenie cmdlet: Przekaż ciąg z `-Command` parametrem. Przykład: `-Command "echo hello"`.

* Azure Portal: w właściwości **zastąpienie polecenia** konfiguracji kontenera Podaj rozdzieloną przecinkami listę ciągów bez cudzysłowów. Przykład: `python, myscript.py, arg1, arg2` ). 

* Menedżer zasobów szablonu lub pliku YAML lub jednego z zestawów SDK platformy Azure: Określ właściwość wiersza polecenia jako tablicę ciągów. Przykład: tablica JSON `["python", "myscript.py", "arg1", "arg2"]` w szablonie Menedżer zasobów. 

  Jeśli znasz składnię [pliku dockerfile](https://docs.docker.com/engine/reference/builder/) , ten format jest podobny do formularza *exec* instrukcji cmd.

### <a name="examples"></a>Przykłady

|    |  Interfejs wiersza polecenia platformy Azure   | Portal | Template | 
| ---- | ---- | --- | --- |
| **Single — polecenie** | `--command-line "python myscript.py arg1 arg2"` | **Zastąpienie polecenia**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| **Wiele poleceń** | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Zastąpienie polecenia**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Przykład interfejsu wiersza polecenia platformy Azure

Na przykład zmodyfikuj zachowanie obrazu kontenera [Microsoft/ACI-WORDCOUNT][aci-wordcount] , który analizuje tekst w *Hamlet* Szekspira w celu znalezienia najczęściej występujących wyrazów. Zamiast analizować *Hamlet*, można ustawić wiersz polecenia, który wskazuje na inne źródło tekstu.

Aby wyświetlić dane wyjściowe kontenera [Microsoft/ACI-WORDCOUNT][aci-wordcount] podczas analizowania domyślnego tekstu, uruchom je za pomocą następującego polecenia [AZ Container Create][az-container-create] . Nie określono wiersza polecenia Start, więc zostanie uruchomione domyślne polecenie kontenera. W celach ilustracyjnych ten przykład ustawia [zmienne środowiskowe](container-instances-environment-variables.md) , aby znaleźć 3 pierwsze wyrazy o długości co najmniej pięciu znaków:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Gdy stan kontenera jest pokazywany jako *zakończony* (Użyj polecenia [AZ Container show][az-container-show] by Check State), Wyświetl dziennik przy użyciu polecenia [AZ Container Logs][az-container-logs] , aby wyświetlić dane wyjściowe.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Dane wyjściowe:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Teraz można skonfigurować drugi przykład kontenera do analizowania różnych tekstów, określając inny wiersz polecenia. Skrypt języka Python wykonywany przez kontener, *WORDCOUNT.py*, akceptuje adres URL jako argument i przetwarza zawartość tej strony zamiast wartości domyślnej.

Na przykład, aby określić 3 pierwsze wyrazy, które są co najmniej pięć liter w *Romeo i Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Po *zakończeniu kontenera* Wyświetl dane wyjściowe, wyświetlając dzienniki kontenera:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Dane wyjściowe:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Następne kroki

Scenariusze oparte na zadaniach, takie jak przetwarzanie wsadowe dużego zestawu danych z kilkoma kontenerami, mogą korzystać z niestandardowych wierszy poleceń w czasie wykonywania. Aby uzyskać więcej informacji na temat uruchamiania kontenerów opartych na zadaniach, zobacz [Uruchamianie zadań kontenera z zasadami ponownego uruchamiania](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
