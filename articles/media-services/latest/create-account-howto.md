---
title: Tworzenie konta usługi Azure Media Services
description: Ten samouczek przeprowadzi Cię przez kroki tworzenia konta Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 49cac230363750e481e165712bf4f619e5cba7ae
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017849"
---
# <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Aby rozpocząć, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, a także zarządzanie nią, musisz utworzyć konto usługi Media Services. Konto usługi Media Services musi być skojarzone z co najmniej jednym kontem magazynu. W tym artykule opisano procedurę tworzenia nowego konta Azure Media Services.

> [!NOTE]
> Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zdecydowanie zaleca się używanie kont magazynu znajdujących się w tej samej lokalizacji co konto usługi Media Services, aby uniknąć dodatkowych opóźnień i kosztów danych wychodzących.

 Do utworzenia konta Media Services można użyć Azure Portal lub interfejsu wiersza polecenia. Wybierz kartę dla metody, której chcesz użyć.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portal](#tab/portal/)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->
Azure Portal zapewnia sposób szybkiego tworzenia konta Azure Media Services. Korzystając z konta, możesz uzyskiwać dostęp do usługi Media Services, która umożliwia przechowywanie, szyfrowanie, kodowanie i przesyłanie strumieniowe zawartości oraz zarządzanie nią na platformie Azure.

Obecnie można użyć [Azure Portal](https://portal.azure.com/) , aby:

* Zarządzaj [zdarzeniami na żywo](live-events-outputs-concept.md)Media Services v3, 
* Wyświetl [zasoby](assets-concept.md)v3 (nie Zarządzaj), 
* [Uzyskaj informacje na temat uzyskiwania dostępu do interfejsów API](./access-api-howto.md). 

W przypadku wszystkich innych zadań zarządzania (na przykład [transformacji i zadań](transforms-jobs-concept.md) oraz [ochrony zawartości](content-protection-overview.md)) należy użyć [interfejsu API REST](/rest/api/media/accountfilters), [interfejsu wiersza polecenia](/cli/azure/ams)lub jednego z obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).
<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->

### <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Użyj Azure Portal, aby utworzyć konto Media Services

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

## <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli/)

## <a name="use-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

### <a name="set-the-azure-subscription"></a>Ustawianie subskrypcji platformy Azure

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

### <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

### <a name="see-also"></a>Zobacz też

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/ams?view=azure-cli-latest)
* [Dołączanie magazynu pomocniczego do konta Media Services](/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Następne kroki

[Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)