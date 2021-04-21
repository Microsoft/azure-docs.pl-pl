---
title: Zagadnienia dotyczące magazynu dla Azure Functions
description: Dowiedz się więcej o wymaganiach dotyczących magazynu Azure Functions o szyfrowaniu przechowywanych danych.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: 5faa85a4fac9fc0b8639f33c475283f4f043c627
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779259"
---
# <a name="storage-considerations-for-azure-functions"></a>Zagadnienia dotyczące magazynu dla Azure Functions

Azure Functions wymaga konta usługi Azure Storage podczas tworzenia wystąpienia aplikacji funkcji. Aplikacja funkcji może używać następujących usług magazynu:


|Usługa Magazynu  | Użycie funkcji  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | Obsługa powiązań kluczy stanu i funkcji.  <br/>Są również używane [przez centra zadań w Durable Functions](durable/durable-functions-task-hubs.md). |
| [Azure Files](../storage/files/storage-files-introduction.md)  | Udział plików używany do przechowywania i uruchamiania kodu aplikacji funkcji w [planach Zużycie i](consumption-plan.md) [Premium.](functions-premium-plan.md) |
| [Azure Queue Storage](../storage/queues/storage-queues-introduction.md)     | Używane przez [centra zadań w programie Durable Functions](durable/durable-functions-task-hubs.md).   |
| [Azure Table storage](../storage/tables/table-storage-overview.md)  |  Używane przez [centra zadań w programie Durable Functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> W przypadku korzystania z planu hostingu Zużycie/Premium kod funkcji i pliki konfiguracji powiązania są przechowywane w usłudze Azure File Storage na głównym koncie magazynu. Po usunięciu głównego konta magazynu ta zawartość zostanie usunięta i nie będzie można jej odzyskać.

## <a name="storage-account-requirements"></a>Wymagania konta magazynu

Podczas tworzenia aplikacji funkcji należy utworzyć lub połączyć konto usługi Azure Storage ogólnego przeznaczenia, które obsługuje usługi Blob Storage, Queue Storage i Table Storage. Jest to spowodowane tym, że usługa Functions korzysta z usługi Azure Storage w przypadku operacji, takich jak zarządzanie wyzwalaczami i rejestrowanie wykonań funkcji. Niektóre konta magazynu nie obsługują kolejek i tabel. Te konta obejmują konta magazynu tylko dla obiektów blob i konta usługi Azure Premium Storage.

Aby dowiedzieć się więcej na temat typów kont magazynu, zobacz [Wprowadzenie do usług Azure Storage](../storage/common/storage-introduction.md#core-storage-services). 

Chociaż z aplikacją funkcji można używać istniejącego konta magazynu, należy się upewnić, że spełnia ono te wymagania. Konta magazynu utworzone w ramach przepływu tworzenia aplikacji funkcji w chmurze Azure Portal te wymagania dotyczące konta magazynu. W portalu nieobsługiwane konta są filtrowane podczas wybierania istniejącego konta magazynu podczas tworzenia aplikacji funkcji. W tym przepływie możesz wybrać tylko istniejące konta magazynu w tym samym regionie co tworzymy aplikację funkcji. Aby dowiedzieć się więcej, zobacz [Lokalizacja konta magazynu.](#storage-account-location)

<!-- JH: Does using a Premium Storage account improve perf? -->

## <a name="storage-account-guidance"></a>Wskazówki dotyczące konta magazynu

Każda aplikacja funkcji wymaga konta magazynu do działania. Jeśli to konto zostanie usunięte, aplikacja funkcji nie zostanie uruchomiony. Aby rozwiązać problemy związane z magazynem, zobacz [Jak rozwiązywać problemy związane z magazynem.](functions-recover-storage-account.md) Poniższe dodatkowe zagadnienia dotyczą konta usługi Storage używanego przez aplikacje funkcji.

### <a name="storage-account-location"></a>Lokalizacja konta magazynu

Aby uzyskać najlepszą wydajność, aplikacja funkcji powinna używać konta magazynu w tym samym regionie, co zmniejsza opóźnienie. Ten Azure Portal wymusza to najlepsze rozwiązanie. Jeśli z jakiegoś powodu musisz użyć konta magazynu w regionie innym niż aplikacja funkcji, musisz utworzyć aplikację funkcji poza portalem. 

### <a name="storage-account-connection-setting"></a>Ustawienie połączenia konta magazynu

Połączenie konta magazynu jest utrzymywane w ustawieniu [aplikacji AzureWebJobsStorage.](./functions-app-settings.md#azurewebjobsstorage) 

Podczas ponownego generowania kluczy magazynu należy zaktualizować parametrów połączenia konta magazynu. [Więcej informacji na temat zarządzania kluczami magazynu można uzyskać tutaj.](../storage/common/storage-account-create.md)

### <a name="shared-storage-accounts"></a>Konta magazynu udostępnionego

Istnieje możliwość, że wiele aplikacji funkcji będzie współużytkować to samo konto magazynu bez żadnych problemów. Na przykład w Visual Studio można tworzyć wiele aplikacji przy użyciu emulatora usługi Azure Storage. W takim przypadku emulator działa jak pojedyncze konto magazynu. Do przechowywania danych aplikacji można również użyć tego samego konta magazynu, które jest używane przez aplikację funkcji. Jednak takie podejście nie zawsze jest dobrym pomysłem w środowisku produkcyjnym.

### <a name="optimize-storage-performance"></a>Optymalizowanie wydajności magazynu

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Szyfrowanie danych magazynu

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

### <a name="in-region-data-residency"></a>Rezydencja danych w regionie

Jeśli wszystkie dane klienta muszą pozostać w jednym regionie, konto magazynu skojarzone z aplikacją funkcji musi być kontem z [nadmiarowością w regionie](../storage/common/storage-redundancy.md). Konto magazynu nadmiarowego w regionie musi być również używane z [usługą Azure Durable Functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection).

Inne dane klientów zarządzane przez platformę są przechowywane tylko w regionie podczas hostowania w wewnętrznej aplikacji ze zrównoważonym obciążeniem App Service Environment (ASE). Aby dowiedzieć się więcej, zobacz [Nadmiarowość strefy ase.](../app-service/environment/zone-redundancy.md#in-region-data-residency)

## <a name="mount-file-shares"></a>Zainstaluj udziały plików

_Ta funkcja jest obecnie dostępna tylko w przypadku uruchamiania w systemie Linux._ 

Istniejące udziały Azure Files można zainstalować w aplikacjach funkcji systemu Linux. Instalowanie udziału w aplikacji funkcji systemu Linux umożliwia wykorzystanie istniejących modeli uczenia maszynowego lub innych danych w funkcjach. Możesz użyć polecenia [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) , aby zainstalować istniejący udział w aplikacji funkcji systemu Linux. 

W tym poleceniu jest nazwą istniejącego udziału Azure Files i może być dowolnym ciągiem, który jednoznacznie definiuje udział po jego zamontowaniu `share-name` `custom-id` w aplikacji funkcji. Ponadto jest `mount-path` to ścieżka, z której jest uzyskiwany dostęp do udziału w aplikacji funkcji. `mount-path` musi mieć format i nie może zaczynać się `/dir-name` od `/home` .

Aby uzyskać kompletny przykład, zobacz skrypty w te [tematu Create a Python function app and mount a Azure Files share (Tworzenie](scripts/functions-cli-mount-files-storage-linux.md)aplikacji funkcji w języku Python i Azure Files udziału). 

Obecnie obsługiwany jest `storage-type` `AzureFiles` tylko jeden z nich. W danej aplikacji funkcji można zainstalować tylko pięć udziałów. Instalowanie udziału plików może zwiększyć zimny czas rozpoczęcia o co najmniej 200–300 m lub nawet więcej, gdy konto magazynu znajduje się w innym regionie.

Zainstalowany udział jest dostępny dla kodu funkcji o `mount-path` określonej wartości. Na przykład w przypadku wartości , można uzyskać dostęp do katalogu docelowego za pomocą interfejsów API systemu `mount-path` `/path/to/mount` plików, jak w poniższym przykładzie w języku Python:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Azure Functions hostingu.

> [!div class="nextstepaction"]
> [Skalowanie i hosting usługi Azure Functions](functions-scale.md)
