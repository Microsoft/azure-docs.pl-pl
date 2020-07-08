---
title: Tworzenie konta usługi Azure Media Services
description: Ten samouczek przeprowadzi Cię przez kroki tworzenia konta Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 2f1694825319ed8b8682c044e7e2282ed4c43dcd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79478801"
---
# <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services

Aby rozpocząć, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, a także zarządzanie nią, musisz utworzyć konto usługi Media Services. Konto usługi Media Services musi być skojarzone z co najmniej jednym kontem magazynu.

> [!NOTE]
> Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zdecydowanie zaleca się używanie kont magazynu znajdujących się w tej samej lokalizacji co konto usługi Media Services, aby uniknąć dodatkowych opóźnień i kosztów danych wychodzących.

W tym artykule opisano procedurę tworzenia nowego konta Azure Media Services. Wybierz jedną z następujących kart.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Azure Portal zapewnia sposób szybkiego tworzenia konta Azure Media Services. Korzystając z konta, możesz uzyskiwać dostęp do usługi Media Services, która umożliwia przechowywanie, szyfrowanie, kodowanie i przesyłanie strumieniowe zawartości oraz zarządzanie nią na platformie Azure.

Obecnie można użyć [Azure Portal](https://portal.azure.com/) , aby:

* Zarządzaj [zdarzeniami na żywo](live-events-outputs-concept.md)Media Services v3, 
* Wyświetl [zasoby](assets-concept.md)v3 (nie Zarządzaj), 
* [Uzyskaj informacje na temat uzyskiwania dostępu do interfejsów API](access-api-portal.md). 

W przypadku wszystkich innych zadań zarządzania (na przykład [transformacji i zadań](transforms-jobs-concept.md) oraz [ochrony zawartości](content-protection-overview.md)) należy użyć [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref)lub jednego z obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

W tym artykule przedstawiono sposób tworzenia konta usługi Media Services przy użyciu witryny Azure Portal.

### <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/).
1. Kliknij pozycję **+ Utwórz**  >  **nośnik**zasobów  >  **Media Services**.
1. W sekcji **Tworzenie konta Media Services** wprowadź wymagane wartości.
    
    | Nazwa | Opis |
    | ---|---|
    |**Nazwa konta**|Wprowadź nazwę nowego konta Media Services. Nazwa konta usługi Media Services składa się z małych liter i cyfr (bez spacji) i może zawierać od 3 do 24 znaków.|
    |**Subskrypcja**|Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z listy subskrypcji platformy Azure, do których masz dostęp.|
    |**Grupa zasobów**|Wybierz nowy lub istniejący zasób. Grupa zasobów jest kolekcją zasobów, które mają ten sam cykl życia, uprawnienia i zasady. Dowiedz się więcej [tutaj](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Lokalizacja**|Wybierz region geograficzny, który będzie używany do przechowywania multimediów i rekordów metadanych dla konta usługi Media Services. Ten region będzie służyć do przetwarzania i przesyłania strumieniowego multimediów. Na liście rozwijanej są wyświetlane tylko regiony dostępne w usłudze Media Services. |
    |**Konto magazynu**|Wybierz konto magazynu, aby dostarczyć magazyn obiektów BLOB zawartości multimedialnej z konta usługi Media Services. Można wybrać istniejące konto magazynu w tym samym regionie geograficznym co konto usługi Media Services albo utworzyć nowe konto magazynu. Nowe konto magazynu jest tworzone w tym samym regionie. Reguły dotyczące nazw kont magazynów są takie same, jak w przypadku kont usługi Media Services.<br/><br/>Musisz mieć jedno konto magazynu **podstawowego** i można mieć dowolną liczbę kont magazynu **pomocniczego** skojarzonych z Twoim kontem Media Services. Za pomocą Azure Portal można dodać konta magazynu pomocniczego. Aby uzyskać więcej informacji, zobacz [konta usługi Azure Storage z kontami Azure Media Services](storage-account-concept.md).<br/><br/>Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zdecydowanie zaleca się używanie kont magazynu znajdujących się w tej samej lokalizacji co konto usługi Media Services, aby uniknąć dodatkowych opóźnień i kosztów danych wychodzących.|
    
1. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**, aby wyświetlić postęp wdrażania konta.
1. Kliknij opcję **Utwórz** w dolnej części formularza.

    Po utworzeniu konta Media Services zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego w stanie **zatrzymanym** . Aby rozpocząć przesyłanie strumieniowe zawartości i korzystać z [dynamicznego tworzenia pakietów](dynamic-packaging-overview.md) i [szyfrowania dynamicznego](content-protection-overview.md), punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi być w stanie **uruchomienia** . 

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="set-the-azure-subscription"></a>Ustawianie subskrypcji platformy Azure

W poniższym poleceniu podaj identyfikator subskrypcji platformy Azure, który ma być używany dla konta usługi Media Services. Listę subskrypcji, do których masz dostęp, możesz wyświetlić, przechodząc do obszaru [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów przy użyciu poniższego polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów, takich jak konta usługi Azure Media Services i skojarzone konta usługi Storage, oraz zarządzania nimi.

Możesz podstawić `amsResourceGroup` swoją wartość.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. Aby uzyskać więcej informacji na temat używania kont magazynu w usłudze Media Services, zobacz [Konta magazynu](storage-account-concept.md).

Musisz mieć jedno **główne** konto magazynu i możesz mieć dowolną liczbę **dodatkowych** kont magazynu skojarzonych z Twoim kontem usługi Media Services. Usługa Media Services obsługuje konta **Ogólnego przeznaczenia, wersja 2** (GPv2) i **Ogólnego przeznaczenia, wersja 1** (GPv1). Konta tylko obiektów blob nie są dozwolone jako **główne**. Jeśli chcesz dowiedzieć się więcej o kontach magazynu, zobacz [Opcje konta usługi Azure Storage](../../storage/common/storage-account-options.md). 

W tym przykładzie utworzymy konto ogólnego przeznaczenia w wersji 2 magazynu LRS w warstwie Standardowa. Jeśli chcesz poeksperymentować z kontami magazynu, użyj parametru `--sku Standard_LRS`. Jednak podczas wybierania jednostki SKU dla środowiska produkcyjnego weź pod uwagę użycie parametru `--sku Standard_RAGRS`, co zapewnia replikację geograficzną na potrzeby zapewnienia ciągłości działania. Aby uzyskać więcej informacji, zobacz temat [Konta magazynu](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
Poniższe polecenie tworzy konto usługi Storage, które ma zostać skojarzone z kontem usługi Media Services. W poniższym skrypcie możesz zastąpić wartość `storageaccountforams` swoją wartością. `amsResourceGroup`musi być zgodna z wartością podaną dla grupy zasobów w poprzednim kroku. Nazwa konta magazynu musi mieć długość mniejszą niż 24.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services

Poniższe polecenie interfejsu wiersza polecenia platformy Azure tworzy nowe konto usługi Media Services. Można zastąpić następujące wartości: `amsaccount` `storageaccountforams` (musi być zgodna z wartością podaną dla konta magazynu) i `amsResourceGroup` (musi być zgodna z wartością podaną dla grupy zasobów).  

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```

### <a name="see-also"></a>Zobacz także

* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Dołączanie magazynu pomocniczego do konta Media Services](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Następne kroki

[Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
