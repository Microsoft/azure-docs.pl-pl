---
title: Uruchamianie zadań na koniec do końca przy użyciu szablonów
description: Korzystając tylko z poleceń interfejsu wiersza polecenia, można utworzyć pulę, przekazać dane wejściowe, utworzyć zadania i skojarzone zadania i pobrać wynikowe dane wyjściowe.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 845a32c2feda5a5a3b8d44d237c62db94cae1779
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91848725"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Użyj Azure Batch szablonów interfejsu wiersza polecenia i transferu plików

Korzystając z rozszerzenia usługi Batch w interfejsie wiersza polecenia platformy Azure, możliwe jest uruchamianie zadań wsadowych bez pisania kodu.

Twórz i używaj plików szablonów JSON przy użyciu interfejsu wiersza polecenia platformy Azure, aby tworzyć pule, zadania i zadania usługi Batch. Użyj poleceń rozszerzenia interfejsu wiersza polecenia, aby łatwo przekazywać pliki wejściowe zadań do konta magazynu skojarzonego z kontem wsadowym, a następnie pobierać pliki wyjściowe zadania.

> [!NOTE]
> Pliki JSON nie obsługują tych samych funkcji co [Azure Resource Manager szablonów](../azure-resource-manager/templates/template-syntax.md). Są one przeznaczone do formatowania jako pierwotna treść żądania REST. Rozszerzenie interfejsu wiersza polecenia nie zmienia żadnych istniejących poleceń, ale ma podobną opcję szablonu, która dodaje częściową funkcję szablonu Azure Resource Manager. Zobacz [rozszerzenia interfejsu wiersza polecenia Azure Batch dla systemów Windows, Mac i Linux](https://github.com/Azure/azure-batch-cli-extensions).

## <a name="overview"></a>Omówienie

Rozszerzenie interfejsu wiersza polecenia platformy Azure umożliwia kompleksowe przetwarzanie wsadowe przez użytkowników, którzy nie są deweloperami. Korzystając tylko z poleceń interfejsu wiersza polecenia, można utworzyć pulę, przekazać dane wejściowe, utworzyć zadania i skojarzone zadania i pobrać wynikowe dane wyjściowe. Żaden dodatkowy kod nie jest wymagany. Bezpośrednie uruchamianie poleceń interfejsu wiersza polecenia lub integrowanie ich ze skryptami.

Szablony wsadowe kompilują w istniejącej obsłudze usługi Batch w [interfejsie wiersza polecenia platformy Azure](batch-cli-get-started.md#json-files-for-resource-creation) dla plików JSON, aby określić wartości właściwości podczas tworzenia pul, zadań, zadań i innych elementów. Szablony wsadowe dodają następujące możliwości:

-   Parametry można zdefiniować. Gdy szablon jest używany, tylko wartości parametrów są określone, aby utworzyć element, z wartościami właściwości innych elementów określonymi w treści szablonu. Użytkownik, który rozumie zadanie wsadowe i aplikacje, które mają być uruchamiane przez zadanie wsadowe, może tworzyć szablony, określać wartości właściwości puli, zadania i zadania. Użytkownik mniej zaznajomiony z usługą Batch i/lub aplikacjami musi określić wartości dla zdefiniowanych parametrów.

-   Fabryki zadań zadania tworzą jedno lub więcej zadań skojarzonych z zadaniem, unikając potrzeby tworzenia wielu definicji zadań i znacząco upraszczają przesyłanie zadań.


Zadania zwykle wykorzystują pliki danych wejściowych i generują pliki danych wyjściowych. Konto magazynu jest domyślnie skojarzone z każdym kontem w usłudze Batch. Transferowanie plików do i z tego konta magazynu przy użyciu interfejsu wiersza polecenia bez kodowania i bez poświadczeń magazynu.

Na przykład [Narzędzia FFmpeg](https://ffmpeg.org/) jest popularną aplikacją, która przetwarza pliki audio i wideo. Oto kroki z interfejsem wiersza polecenia Azure Batch, aby wywoływać narzędzia FFmpeg do transkodowanie źródłowych plików wideo do różnych rozwiązań.

-   Utwórz szablon puli. Użytkownik tworzący szablon wie, jak wywołać aplikację narzędzia FFmpeg i jej wymagania; określają one odpowiedni system operacyjny, rozmiar maszyny wirtualnej, sposób instalacji narzędzia ffmpeg (z pakietu aplikacji lub przy użyciu Menedżera pakietów, na przykład) i innych wartości właściwości puli. Parametry są tworzone, dlatego w przypadku użycia szablonu należy określić tylko Identyfikator puli i liczbę maszyn wirtualnych.

-   Utwórz szablon zadania. Użytkownik tworzący szablon wie, w jaki sposób narzędzia FFmpeg musi zostać wywołana do transkodowanie źródłowego wideo do innej rozdzielczości i określa wiersz polecenia zadania; wiedzą również, że istnieje folder zawierający źródłowe pliki wideo z wymaganym zadaniem na plik wejściowy.

-   Użytkownik końcowy z zestawem plików wideo do transkodowanie najpierw tworzy pulę przy użyciu szablonu puli, określając tylko Identyfikator puli i wymaganą liczbę maszyn wirtualnych. Mogą następnie przekazać pliki źródłowe do transkodowanie. Zadanie można następnie przesłać przy użyciu szablonu zadania, określając tylko Identyfikator puli i lokalizację przekazanych plików źródłowych. Zadanie wsadowe jest tworzone przy użyciu jednego zadania na generowany plik wejściowy. Na koniec można pobrać transkodowane pliki wyjściowe.

## <a name="installation"></a>Instalacja

Aby zainstalować rozszerzenie interfejsu wiersza polecenia Azure Batch, najpierw [Zainstaluj interfejs wiersza polecenia platformy azure 2,0](/cli/azure/install-azure-cli)lub Uruchom interfejs wiersza polecenia platformy Azure w programie [Azure Cloud Shell](../cloud-shell/overview.md).

Zainstaluj najnowszą wersję rozszerzenia usługi Batch, korzystając z następującego polecenia platformy Azure:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Aby uzyskać więcej informacji o rozszerzeniu interfejsu wiersza polecenia usługi Batch i dodatkowych opcjach instalacji, zobacz [repozytorium GitHub](https://github.com/Azure/azure-batch-cli-extensions).


Aby korzystać z funkcji rozszerzenia interfejsu wiersza polecenia, musisz mieć konto Azure Batch i, w przypadku poleceń, które przesyłają pliki do i z magazynu, połączonego konta magazynu.

Aby zalogować się do konta usługi Batch za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Zarządzanie zasobami usługi Batch za pomocą interfejsu wiersza polecenia platformy Azure](batch-cli-get-started.md).

## <a name="templates"></a>Szablony

Szablony Azure Batch są podobne do szablonów Azure Resource Manager, w funkcji i składni. Są to pliki JSON, które zawierają nazwy właściwości elementów i wartości, ale Dodaj następujące podstawowe koncepcje:

-   **Parametry**

    -   Zezwalaj na określenie wartości właściwości w sekcji treści, z uwzględnieniem tylko wartości parametrów, które mają być dostarczone, gdy szablon jest używany. Na przykład pełna definicja puli może zostać umieszczona w treści i tylko jeden parametr zdefiniowany dla `poolId` ; w związku z tym należy podać tylko ciąg identyfikatora puli, aby utworzyć pulę.

    -   Treść szablonu może zostać utworzona przez kogoś z wiedzą na temat partii i aplikacji, które mają być uruchamiane przez program Batch; gdy szablon jest używany, należy podać tylko wartości parametrów zdefiniowanych przez autora. Użytkownik bez szczegółowej wiedzy o partiach i/lub aplikacji może korzystać z szablonów.

-   **Zmienne**

    -   Zezwalaj na określenie prostych lub złożonych wartości parametrów w jednym miejscu i używanych w co najmniej jednym miejscu w treści szablonu. Zmienne mogą uprościć i zmniejszyć rozmiar szablonu, a także zwiększyć jego łatwość obsługi, mając jedną lokalizację do zmiany właściwości.

-   **Konstrukcje wyższego poziomu**

    -   Niektóre konstrukcje wyższego poziomu są dostępne w szablonie, które nie są jeszcze dostępne w interfejsach API przetwarzania wsadowego. Na przykład fabryka zadań może być zdefiniowana w szablonie zadania, który tworzy wiele zadań dla zadania, przy użyciu wspólnej definicji zadania. Te konstrukcje nie wymagają kodu do dynamicznego tworzenia wielu plików JSON, takich jak jeden plik na zadanie, a także do tworzenia plików skryptów w celu instalowania aplikacji za pośrednictwem Menedżera pakietów.

    -   W pewnym momencie te konstrukcje mogą zostać dodane do usługi Batch i dostępne w usłudze Batch API, interfejsów użytkownika itp.

### <a name="pool-templates"></a>Szablony pul

Szablony pul obsługują standardowe możliwości szablonów parametrów i zmiennych. Obsługują one również następującą konstrukcję wyższego poziomu:

-   **Odwołania do pakietu**

    -   Opcjonalnie zezwala na kopiowanie oprogramowania do węzłów puli przy użyciu menedżerów pakietów. Określono menedżera pakietów i identyfikator pakietu. Deklarując co najmniej jeden pakiet, można uniknąć tworzenia skryptu pobierającego wymagane pakiety, instalowania skryptu i uruchamiania skryptu w każdym węźle puli.

Poniżej przedstawiono przykładowy szablon, który tworzy pulę maszyn wirtualnych z systemem Linux z zainstalowanym programem narzędzia FFmpeg. Aby go użyć, podaj tylko ciąg identyfikatora puli i liczbę maszyn wirtualnych w puli:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "taskSlotsPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Jeśli plik szablonu ma nazwę _pool-ffmpeg.json_, następnie Wywołaj szablon w następujący sposób:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

Interfejs wiersza polecenia poprosi o podanie wartości `poolId` parametrów i `nodeCount` . Możesz również podać parametry w pliku JSON. Na przykład:

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

Jeśli plik JSON parametrów miał nazwę *pool-parameters.json*, następnie Wywołaj szablon w następujący sposób:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Szablony zadań

Szablony zadań obsługują standardowe możliwości szablonów parametrów i zmiennych. Obsługują one również następującą konstrukcję wyższego poziomu:

-   **Fabryka zadań**

    -   Tworzy wiele zadań dla zadania z jednej definicji zadania. Obsługiwane są trzy typy fabryk zadań — czyszczenie parametrów, zadanie na plik oraz zbieranie zadań.

Poniżej przedstawiono przykładowy szablon, który tworzy zadanie do transkodowanie plików wideo MP4 z narzędzia FFmpeg do jednego z dwóch mniejszych rozdzielczości. Tworzy jedno zadanie na źródłowym pliku wideo. Zobacz [grupy plików i transfer plików,](#file-groups-and-file-transfer) Aby uzyskać więcej informacji na temat grup plików wejściowych i wyjściowych zadań.

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": {
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Jeśli plik szablonu ma nazwę _job-ffmpeg.json_, następnie Wywołaj szablon w następujący sposób:

```azurecli
az batch job create --template job-ffmpeg.json
```

Tak jak wcześniej, interfejs wiersza polecenia wyświetli komunikat z prośbą o podanie wartości parametrów. Możesz również podać parametry w pliku JSON.

### <a name="use-templates-in-batch-explorer"></a>Używanie szablonów w Batch Explorer

Szablon interfejsu wiersza polecenia w usłudze Batch można przekazać do aplikacji klasycznej [Batch Explorer](https://github.com/Azure/BatchExplorer) (zwanej wcześniej BatchLabs) w celu utworzenia puli lub zadania wsadowego. Możesz również wybrać ze wstępnie zdefiniowanej puli i szablonów zadań w galerii Batch Explorer.

Aby przekazać szablon:

1. W Batch Explorer wybierz kolejno pozycje **Galeria**  >  **lokalne szablony**.

2. Wybierz lub przeciągnij i upuść, lokalną pulę lub szablon zadania.

3. Wybierz pozycję **Użyj tego szablonu** i postępuj zgodnie z monitami wyświetlanymi na ekranie.

## <a name="file-groups-and-file-transfer"></a>Grupy plików i transfer plików

Większość zadań i zadań wymaga plików wejściowych i wygenerowania plików wyjściowych. Zazwyczaj pliki wejściowe i pliki wyjściowe są przesyłane z klienta do węzła lub z węzła do klienta programu. Rozszerzenie interfejsu wiersza polecenia Azure Batch służy do wyodrębniania transferu plików i korzystania z konta magazynu, które można skojarzyć z poszczególnymi kontami w usłudze Batch.

Grupa plików jest równa kontenerowi tworzonego na koncie usługi Azure Storage. Grupa plików może mieć podfoldery.

Rozszerzenie interfejsu wiersza polecenia usługi Batch umożliwia wysyłanie plików z klienta do określonej grupy plików i pobieranie plików z określonej grupy plików na klienta.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Szablony puli i zadań umożliwiają określenie plików przechowywanych w grupach plików do kopiowania do węzłów puli lub węzłów puli z powrotem do grupy plików. Na przykład w określonym wcześniej szablonie zadania Grupa plików *Narzędzia FFmpeg-Input* jest określona dla fabryki zadań jako lokalizacja źródłowych plików wideo skopiowanych do węzła w celu transkodowania. Grupa plików *Narzędzia FFmpeg-Output* jest lokalizacją, w której pliki wyjściowe transkodowane są kopiowane z węzła, w którym uruchomiono każde zadanie.

## <a name="summary"></a>Podsumowanie

Obsługa szablonu i transferu plików została obecnie dodana tylko do interfejsu wiersza polecenia platformy Azure. Celem jest rozwijanie odbiorców, którzy mogą korzystać z usługi Batch dla użytkowników, którzy nie muszą opracowywać kodu przy użyciu interfejsów API usługi Batch, takich jak badacze i użytkownicy IT. Bez kodowania użytkownicy z wiedzą na temat platformy Azure, usługi Batch i aplikacji uruchamianych przez usługę Batch mogą tworzyć szablony dla puli i tworzenia zadań. Za pomocą parametrów szablonu użytkownicy nie znają szczegółowych informacji o usłudze Batch, a aplikacje mogą korzystać z szablonów.

Wypróbuj rozszerzenie Batch dla interfejsu wiersza polecenia platformy Azure i prześlij nam opinię lub sugestie w komentarzach dotyczących tego artykułu albo za pośrednictwem [repozytorium społeczności usługi Batch](https://github.com/Azure/Batch).

## <a name="next-steps"></a>Następne kroki

- Szczegółowa dokumentacja dotycząca instalacji i użycia, przykłady i kod źródłowy są dostępne w [repozytorium GitHub systemu Azure](https://github.com/Azure/azure-batch-cli-extensions).

- Dowiedz się więcej o korzystaniu z [Batch Explorer](https://github.com/Azure/BatchExplorer) do tworzenia zasobów usługi Batch i zarządzania nimi.
