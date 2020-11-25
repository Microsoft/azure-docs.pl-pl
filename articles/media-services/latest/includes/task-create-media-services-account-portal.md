---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: 72857564a6b195353e7bf2a27c8369004d52d3f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971349"
---
<!-- Use the portal to create a media services account. -->

## <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/).
1. Kliknij pozycję **+ Utwórz**  >  **nośnik** zasobów  >  **Media Services**.
1. W sekcji **Tworzenie konta Media Services** wprowadź wymagane wartości.

    | Nazwa | Opis |
    | ---|---|
    |**Nazwa konta**|Wprowadź nazwę nowego konta Media Services. Nazwa konta usługi Media Services składa się z małych liter i cyfr (bez spacji) i może zawierać od 3 do 24 znaków.|
    |**Subskrypcja**|Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z listy subskrypcji platformy Azure, do których masz dostęp.|
    |**Grupa zasobów**|Wybierz nowy lub istniejący zasób. Grupa zasobów jest kolekcją zasobów, które mają ten sam cykl życia, uprawnienia i zasady. Dowiedz się więcej [tutaj](../../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Lokalizacja**|Wybierz region geograficzny, który będzie używany do przechowywania multimediów i rekordów metadanych dla konta usługi Media Services. Ten region będzie służyć do przetwarzania i przesyłania strumieniowego multimediów. Na liście rozwijanej są wyświetlane tylko regiony dostępne w usłudze Media Services. |
    |**Konto magazynu**|Wybierz konto magazynu, aby dostarczyć magazyn obiektów BLOB zawartości multimedialnej z konta usługi Media Services. Można wybrać istniejące konto magazynu w tym samym regionie geograficznym co konto usługi Media Services albo utworzyć nowe konto magazynu. Nowe konto magazynu jest tworzone w tym samym regionie. Reguły dotyczące nazw kont magazynów są takie same, jak w przypadku kont usługi Media Services.<br/><br/>Musisz mieć jedno konto magazynu **podstawowego** i można mieć dowolną liczbę kont magazynu **pomocniczego** skojarzonych z Twoim kontem Media Services. Za pomocą Azure Portal można dodać konta magazynu pomocniczego. Aby uzyskać więcej informacji, zobacz [konta usługi Azure Storage z kontami Azure Media Services](../storage-account-concept.md).<br/><br/>Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zdecydowanie zaleca się używanie kont magazynu znajdujących się w tej samej lokalizacji co konto usługi Media Services, aby uniknąć dodatkowych opóźnień i kosztów danych wychodzących.|
    |**Ustawienia zaawansowane**| Konto można utworzyć przy użyciu tożsamości zarządzanej przez system, wybierając przycisk radiowy **zarządzany przez system** .  Wybranie tej opcji umożliwi korzystanie z kluczy zarządzanych przez klienta lub dostarczenie własnego klucza (BYOK) i Media Services, aby włączyć zaufany magazyn.  Więcej informacji o kluczach zarządzanych przez klienta znajduje [się w temacie Przenoszenie własnego klucza (kluczy zarządzanych przez klienta) za pomocą Media Services](../concept-use-customer-managed-keys-byok.md). Ponadto [tożsamości zarządzane](../concept-managed-identities.md) również będą włączane.

1. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**, aby wyświetlić postęp wdrażania konta.
1. Kliknij opcję **Utwórz** w dolnej części formularza.
