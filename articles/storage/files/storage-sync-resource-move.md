---
title: Azure File Sync przenoszenie zasobów i zmiany topologii
description: Dowiedz się, jak przenosić zasoby synchronizacji między grupami zasobów, subskrypcjami i dzierżawami Azure Active Directory (AAD).
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 3/10/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: acd5f9263a74d3273dc65522e77d83c90a719311
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555555"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Przenoszenie zasobów Azure File Sync do innej grupy zasobów, subskrypcji lub dzierżawy usługi AAD

W tym artykule opisano, jak wprowadzać zmiany w dzierżawie grupy zasobów, subskrypcji lub Azure Active Directory (AAD) dla Azure File Sync zasobów w chmurze i kont usługi Azure Storage.

Planując wprowadzanie zmian w zasobach w chmurze Azure File Sync, ważne jest, aby uwzględnić zasoby magazynu w tym samym czasie. Istnieją następujące zasoby:

**Zasoby Azure File Sync (w kolejności hierarchicznej)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: Usługa synchronizacji magazynu
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: Zarejestrowany serwer
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: Grupa synchronizacji
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: Punkt końcowy w chmurze
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: Punkt końcowy serwera

W Azure File Sync jedynym zasobem, który można przenieść, jest zasób usługi synchronizacji magazynu. Wszelkie podrzędne zasoby są powiązane z jej elementem nadrzędnym i nie można go przenieść do innej usługi synchronizacji magazynu.

**Zasoby usługi Azure Storage (w kolejności hierarchicznej)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: Konto magazynu
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: Udział plików

W usłudze Azure Storage jedynym zasobem możliwym do przeniesienia jest konto magazynu. Udział plików platformy Azure jako zasób nie może zostać przeniesiony do innego konta magazynu.

## <a name="supported-combinations"></a>Obsługiwane kombinacje

Planując przenoszenie zasobów, konto magazynu i zasób Azure File Sync najwyższego poziomu o nazwie *usługa synchronizacji magazynu* należy rozważyć razem.

Najlepszym rozwiązaniem jest, że usługa synchronizacji magazynu i konta magazynu, które synchronizują udziały plików, powinny zawsze znajdować się w tej samej subskrypcji. Te kombinacje są obsługiwane:

* Usługa synchronizacji magazynu i konta magazynu znajdują się w **różnych grupach zasobów** (w tej samej dzierżawie platformy Azure)
* Usługi synchronizacji magazynu i konta magazynu znajdują się w **różnych subskrypcjach** (w tej samej dzierżawie platformy Azure)

> [!IMPORTANT]
> Za pomocą różnych kombinacji przenoszenia usługa synchronizacji magazynu i konta magazynu mogą kończyć się w różnych subskrypcjach, które podlegają różnym dzierżawcom usługi AAD. Synchronizacja będzie nawet prawdopodobnie działać, ale nie jest to obsługiwana konfiguracja. Synchronizacja może zostać zatrzymana w przyszłości bez możliwości powrotu do stanu roboczego.

Podczas planowania przenoszenia zasobów istnieją różne zagadnienia dotyczące [przenoszenia w ramach tej samej dzierżawy usługi AAD](#move-within-the-same-azure-active-directory-tenant) i przechodzenia między [różnymi dzierżawcami usługi AAD](#move-to-a-new-azure-active-directory-tenant). Podczas przenoszenia dzierżawców usługi AAD zawsze przenoś zasoby synchronizacji i magazynu.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>Przenieś w ramach tej samej dzierżawy Azure Active Directory

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="Obraz przedstawiający Azure Portal dla zasobu usługi synchronizacji magazynu z rozwiniętym poleceniem Move. Zostanie wyświetlona opcja przenoszenie i przenoszenie subskrypcji grupy zasobów." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        Wygodnym sposobem przenoszenia zasobu usługi synchronizacji magazynu jest użycie Azure Portal. Przejdź do usługi synchronizacji magazynu, którą chcesz przenieść, a następnie wybierz pozycję **Przenieś** z paska poleceń. Te same kroki dotyczą przeniesienia konta magazynu. W ten sposób można również przenieść wszystkie zasoby w grupie zasobów. Przeniesienie całej grupy zasobów jest zalecane, jeśli masz usługę synchronizacji magazynu i wszystkie używane konta magazynu w tej grupie zasobów.
    :::column-end:::
:::row-end:::

> [!WARNING]
> Gdy przenosisz zasób konta magazynu, synchronizacja zostanie zatrzymana natychmiast. Musisz ręcznie autoryzować synchronizację, aby uzyskać dostęp do odpowiednich kont magazynu w nowej subskrypcji. W sekcji [Azure File Sync autoryzacji dostępu do magazynu](#azure-file-sync-storage-access-authorization) zostaną podane niezbędne kroki.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>Przenieś do nowej dzierżawy Azure Active Directory

Poszczególne zasoby, takie jak usługa synchronizacji magazynu lub konta magazynu, nie mogą być przenoszone przez siebie do innej dzierżawy usługi AAD. Tylko subskrypcje platformy Azure mogą przenosić dzierżawy usługi AAD. Zastanów się nad strukturą subskrypcji w nowej dzierżawie usługi AAD. Możesz użyć dedykowanej subskrypcji dla Azure File Sync. 

1. Utwórz subskrypcję platformy Azure (lub określ istniejącą w starej dzierżawie, która powinna zostać przeniesiona.
1. [Przeprowadź subskrypcję w ramach tej samej dzierżawy usługi AAD](#move-within-the-same-azure-active-directory-tenant) i wszystkich skojarzonych kont magazynu.
1. Synchronizacja zostanie zatrzymana. Ukończ dzierżawę Przenieś natychmiast lub [Przywróć możliwość synchronizacji w celu uzyskania dostępu do przeniesionych kont magazynu](#azure-file-sync-storage-access-authorization). Później możesz przejść do nowej dzierżawy usługi AAD.

Gdy wszystkie powiązane Azure File Sync zasoby zostały sequestered do swojej subskrypcji, możesz przystąpić do przenoszenia całej subskrypcji do docelowej dzierżawy usługi AAD. [Przewodnik po subskrypcji transferu](../../role-based-access-control/transfer-subscription.md) umożliwia planowanie i wykonywanie takich transferów.

> [!WARNING]
> Po przeniesieniu subskrypcji z jednej dzierżawy do innej synchronizacja zostanie zatrzymana natychmiast. Musisz ręcznie autoryzować synchronizację, aby uzyskać dostęp do odpowiednich kont magazynu w nowej subskrypcji. W sekcji [Azure File Sync autoryzacji dostępu do magazynu](#azure-file-sync-storage-access-authorization) zostaną podane niezbędne kroki.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="Obraz przedstawiający blok Azure Portal i subskrypcji, który wyróżnia polecenie Zmień katalog paska narzędzi w środku, u góry strony." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        Możesz rozpocząć migrację, gdy masz plan i wymagane uprawnienia:
        1. W Azure Portal przejdź do subskrypcji, bloku **Przegląd** .
        1. Wybierz opcję **Zmień katalog**
        1. Postępuj zgodnie z instrukcjami kreatora, aby przypisać nową dzierżawę usługi AAD.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Azure File Sync Autoryzacja dostępu do magazynu

Gdy konta magazynu są przenoszone do nowej subskrypcji lub są przenoszone w ramach subskrypcji do nowej dzierżawy usługi Azure Active Directory (AAD), synchronizacja zostanie zatrzymana. Dostęp oparty na rolach (RBAC) służy do autoryzowania Azure File Sync dostępu do konta magazynu, a te przydziały ról nie są migrowane z zasobami.

### <a name="azure-file-sync-service-principal"></a>Nazwa główna usługi Azure File Sync

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Obraz przedstawiający Azure Portal, zarządzanie subskrypcjami, zarejestrowani dostawcy zasobów." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        Aby można było autoryzować dostęp do synchronizacji do konta magazynu, nazwa główna usługi Azure File Sync musi znajdować się w dzierżawie AAD. </br></br> Po dzisiejszym utworzeniu nowej subskrypcji platformy Azure dostawca zasobów Azure File Sync *Microsoft. StorageSync* zostanie automatycznie zarejestrowany w ramach subskrypcji. Rejestracja dostawcy zasobów spowoduje, że jednostka *usługi* dla synchronizacji jest dostępna w dzierżawie Azure Active Directory, która zarządza subskrypcją. Nazwa główna usługi jest podobna do konta użytkownika w usłudze AAD. Za pomocą jednostki usługi Azure File Sync można autoryzować dostęp do zasobów za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Jedyna synchronizacja zasobów musi mieć dostęp do konta magazynu zawierającego udziały plików, które powinny być synchronizowane. *Pakiet Microsoft. StorageSync* musi być przypisany do wbudowanego czytnika ról **i dostępu do danych** na koncie magazynu. </br></br> To przypisanie jest wykonywane automatycznie przy użyciu kontekstu użytkownika zalogowanego użytkownika podczas dodawania udziału plików do grupy synchronizacji, a także do tworzenia punktu końcowego w chmurze. Po przeniesieniu konta magazynu do nowej subskrypcji lub dzierżawy usługi AAD to przypisanie roli zostaje utracone i [należy je ręcznie ponownie nawiązać](#establish-sync-access-to-a-storage-account).
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Jeśli docelowa subskrypcja platformy Azure nie została ostatnio utworzona, sprawdź, czy dostawca zasobów *Microsoft. StorageSync* jest zarejestrowany w ramach subskrypcji. Jeśli nie, Dodaj ją ręcznie do tego samego bloku portalu.

### <a name="establish-sync-access-to-a-storage-account"></a>Ustanów dostęp z synchronizacją do konta magazynu

Nazwa [główna usługi Azure File Sync](#azure-file-sync-service-principal) musi być używana do autoryzacji dostępu do konta magazynu za pośrednictwem kontroli dostępu opartej na ROLACH (RBAC). *Pakiet Microsoft. StorageSync* musi być przypisany do wbudowanego czytnika ról **i dostępu do danych** na koncie magazynu. 

To przypisanie jest zazwyczaj wykonywane automatycznie w kontekście użytkownika zalogowanego użytkownika podczas dodawania udziału plików do grupy synchronizacji lub innymi słowy, tworzysz punkt końcowy w chmurze. Jednak po przeniesieniu konta magazynu do nowej subskrypcji lub dzierżawy usługi AAD to przypisanie roli zostaje utracone i należy je ręcznie ponownie nawiązać.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="Obraz przedstawiający nazwę główną usługi Microsoft. StorageSync przypisaną do czytnika i roli dostępu do danych na koncie magazynu":::
    :::column-end:::
    :::column:::
        W Azure Portal przejdź do konta magazynu, aby ponownie autoryzować dostęp do synchronizacji. <ol><li>Wybierz pozycję **Kontrola dostępu (IAM)** po lewej stronie spisu treści.</li><li>Wybierz kartę przypisania roli, aby wyświetlić listę użytkowników i aplikacji (nazwy główne usługi), które mają dostęp do konta magazynu.</li><li>Wybierz pozycję **Dodaj**.</li><li>W **polu Rola** wybierz pozycję **czytnik i dostęp do danych**.</li><li>W **polu Wybierz** wpisz *Microsoft. StorageSync*, wybierz rolę, a następnie kliknij przycisk **Zapisz**.</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>Przechodzenie do innego regionu platformy Azure

*Usługa synchronizacji magazynu* zasobów Azure File Sync i konta magazynu zawierające synchronizowane udziały plików mają region platformy Azure, w którym są wdrażane. Ten region jest określany podczas tworzenia zasobu. Region usługi synchronizacji magazynu i zasobów konta magazynu musi być zgodny. Tych regionów nie można zmienić na żadnym z typów zasobów po ich utworzeniu.

Przypisywanie innego regionu do zasobu różni się od trybu failover w [regionie](#region-fail-over), który może być obsługiwany, w zależności od ustawienia nadmiarowości konta magazynu.

## <a name="region-fail-over"></a>Tryb failover w regionie

[Usługa Azure Storage oferuje opcje nadmiarowości geograficznej](../common/storage-redundancy.md#geo-redundant-storage) dla konta magazynu. Te opcje nadmiarowości mogą powodować problemy dotyczące kont magazynu używanych z Azure File Sync. Głównym powodem jest to, że replikacja między regionami oddalonymi geograficznie nie jest wykonywana przez Azure File Sync, ale przez technologię replikacji magazynu wbudowaną w podsystem magazynowania na platformie Azure. Nie może on zrozumieć stanu aplikacji, a Azure File Sync jest aplikacją z plikami synchronizowanymi do i z udziałów plików platformy Azure w danym momencie. Jeśli użytkownik zdecyduje się na dowolna z tych opcji nadmiarowości magazynu geograficznie, nie utracisz wszystkich danych w przypadku awarii o dużej skali. Należy jednak [przewidzieć utratę danych](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss).

> [!CAUTION]
> Przełączenie w tryb failover nigdy nie jest odpowiednim zamiennikiem do aprowizacji zasobów w poprawnym regionie platformy Azure. Jeśli zasoby znajdują się w regionie "niewłaściwe", należy rozważyć zatrzymanie synchronizacji i ustawienie synchronizacji ponownie z nowymi udziałami plików platformy Azure, które są wdrożone w żądanym regionie.

Regionalna praca awaryjna może zostać uruchomiona przez firmę Microsoft w przypadku wystąpienia krytycznego, które będzie renderować centra danych w regionie usługi Azure incapacitated przez dłuższy czas. Nieprzerwana zmiana firmy może być krótsza niż czas przygotowania firmy Microsoft do przejścia przed rozpoczęciem regionalnego trybu failover. W przypadku takiej sytuacji [Praca awaryjna może również zostać zainicjowana przez klientów](../common/storage-initiate-account-failover.md).

> [!IMPORTANT]
> W przypadku przełączenia w tryb failover należy zastanowić się nad zgłoszeniem biletu pomocy technicznej dotyczącej usług synchronizacji magazynu, których działanie nie działa poprawnie.

## <a name="see-also"></a>Zobacz też

- [Omówienie udziałów plików platformy Azure i przewodników migracji synchronizacji](storage-files-migration-overview.md)
- [Rozwiązywanie problemów z usługą Azure File Sync](storage-sync-files-troubleshoot.md)
- [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md)
