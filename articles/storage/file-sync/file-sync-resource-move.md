---
title: Azure File Sync zasobów i zmiany topologii
description: Dowiedz się, jak przenosić zasoby synchronizacji między grupami zasobów, subskrypcjami i Azure Active Directory dzierżawami (AAD).
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fb59b8d48d8411ade620304fbd143d86ac5ae919
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797281"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Przenoszenie Azure File Sync zasobów do innej grupy zasobów, subskrypcji lub dzierżawy usługi AAD

W tym artykule opisano sposób zmieniania grupy zasobów, subskrypcji lub dzierżawy Azure Active Directory (AAD) dla zasobów Azure File Sync w chmurze i kont usługi Azure Storage.

Podczas planowania zmian w zasobach Azure File Sync w chmurze należy wziąć pod uwagę zasoby magazynu w tym samym czasie. Istnieją następujące zasoby:

**Azure File Sync zasobów (w kolejności hierarchicznej)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: Usługa synchronizacji magazynu
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: Zarejestrowany serwer
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: Grupa synchronizacji
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: Punkt końcowy w chmurze
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: Punkt końcowy serwera

W Azure File Sync jedynym zasobem, który można przenosić, jest zasób usługi synchronizacji magazynu. Wszystkie podźródła są powiązane z elementem nadrzędnym i nie mogą zostać przekazane do innej usługi synchronizacji magazynu.

**Zasoby usługi Azure Storage (w kolejności hierarchicznej)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: Konto magazynu
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: Udział plików

W usłudze Azure Storage jedynym zasobem, który można przenosić, jest konto magazynu. Udział plików platformy Azure jako podźródło nie może zostać przenieść na inne konto magazynu.

## <a name="supported-combinations"></a>Obsługiwane kombinacje

Podczas planowania przenoszenia zasobów należy uwzględnić konto magazynu i zasób Azure File Sync najwyższego poziomu, nazywany usługą synchronizacji magazynu. 

Najlepszym rozwiązaniem jest to, że usługa synchronizacji magazynu i konta magazynu, które mają synchronizujące udziały plików, powinny zawsze znajdować się w tej samej subskrypcji. Obsługiwane są następujące kombinacje:

* Usługa synchronizacji magazynu i konta magazynu znajdują się w **różnych grupach zasobów** (tej samej dzierżawie platformy Azure)
* Usługa synchronizacji magazynu i konta magazynu znajdują się w **różnych subskrypcjach** (tej samej dzierżawie platformy Azure)

> [!IMPORTANT]
> Dzięki różnym kombinacjom przeniesień usługa synchronizacji magazynu i konta magazynu mogą znaleźć się w różnych subskrypcjach zarządzanych przez różne dzierżawy usługi AAD. Synchronizacja wydaje się nawet działać, ale nie jest to obsługiwana konfiguracja. Synchronizacja może zatrzymać się w przyszłości bez możliwości powrotu do stanu roboczego.

Podczas planowania przenoszenia zasobów istnieją różne zagadnienia dotyczące przenoszenia w ramach tej samej dzierżawy usługi [AAD](#move-within-the-same-azure-active-directory-tenant) i przenoszenia do innej [dzierżawy usługi AAD.](#move-to-a-new-azure-active-directory-tenant) Podczas przenoszenia dzierżaw usługi AAD należy zawsze przenosić zasoby synchronizacji i magazynu razem.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>Przenoszenie w ramach tej samej Azure Active Directory dzierżawy

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="Obraz przedstawiający Azure Portal zasobu usługi synchronizacji magazynu z rozwiniętym poleceniem Move. Pokazuje opcje przenoszenia grupy zasobów i przenoszenia subskrypcji." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        Wygodnym sposobem przenoszenia zasobu usługi synchronizacji magazynu jest użycie Azure Portal. Przejdź do usługi synchronizacji magazynu, którą chcesz przenieść, a następnie wybierz pozycję **Przenieś** na pasku poleceń. Te same kroki mają zastosowanie do przenoszenia konta magazynu. W ten sposób można również przenieść wszystkie zasoby w grupie zasobów. Przeniesienie całej grupy zasobów jest zalecane, jeśli masz usługę synchronizacji magazynu i wszystkie używane konta magazynu w tej grupie zasobów.
    :::column-end:::
:::row-end:::

> [!WARNING]
> Po przenoszeniu zasobu konta magazynu synchronizacja zostanie natychmiast zatrzymana. Musisz ręcznie autoryzować synchronizację, aby uzyskać dostęp do odpowiednich kont magazynu w nowej subskrypcji. Sekcja [Azure File Sync autoryzacji dostępu do magazynu](#azure-file-sync-storage-access-authorization) zawiera niezbędne kroki.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>Przejście do nowej dzierżawy Azure Active Directory dzierżawy

Pojedyncze zasoby, takie jak usługa synchronizacji magazynu lub konta magazynu, nie mogą być automatycznie przenoszone do innej dzierżawy usługi AAD. Tylko subskrypcje platformy Azure mogą przenosić dzierżawy usługi AAD. Pomyśl o strukturze subskrypcji w nowej dzierżawie usługi AAD. Możesz użyć dedykowanej subskrypcji na Azure File Sync. 

1. Utwórz subskrypcję platformy Azure (lub określ istniejącą subskrypcję w starej dzierżawie, która ma zostać przenieść.
1. [Wykonaj przeniesienie subskrypcji w ramach tej samej dzierżawy usługi AAD](#move-within-the-same-azure-active-directory-tenant) usługi synchronizacji magazynu i wszystkich skojarzonych kont magazynu.
1. Synchronizacja zostanie zatrzymana. Wykonaj natychmiastowe przeniesienie dzierżawy lub [przywróć](#azure-file-sync-storage-access-authorization)możliwość uzyskania dostępu do kont magazynu, które zostały przeniesione. Następnie możesz przejść do nowej dzierżawy usługi AAD później.

Gdy wszystkie powiązane Azure File Sync zasobów zostaną powiązane w ramach ich własnej subskrypcji, możesz przenieść całą subskrypcję do docelowej dzierżawy usługi AAD. Przewodnik [po przenoszeniu subskrypcji](../../role-based-access-control/transfer-subscription.md) umożliwia planowanie i wykonywanie takiego przeniesienia.

> [!WARNING]
> Po przeniesieniu subskrypcji z jednej dzierżawy do innej synchronizacja zostanie natychmiast zatrzymana. Musisz ręcznie autoryzować synchronizację, aby uzyskać dostęp do odpowiednich kont magazynu w nowej subskrypcji. Sekcja [Azure File Sync autoryzacji dostępu do magazynu](#azure-file-sync-storage-access-authorization) zawiera niezbędne kroki.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="Obraz przedstawiający blok Azure Portal Subskrypcji z wyróżnionem poleceniem Zmień pasek narzędzi katalogu w środku w górnej części strony." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        Możesz rozpocząć migrację, gdy masz plan i wymagane uprawnienia:
        1. W Azure Portal przejdź do subskrypcji, bloku **Przegląd.**
        1. Wybierz opcję **Zmień katalog**
        1. Wykonaj kroki kreatora, aby przypisać nową dzierżawę usługi AAD.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Azure File Sync autoryzacji dostępu do magazynu

Gdy konta magazynu zostaną przeniesione do nowej subskrypcji lub przeniesione w ramach subskrypcji do nowej dzierżawy usługi Azure Active Directory (AAD), synchronizacja zostanie zatrzymana. Dostęp oparty na rolach (RBAC) jest używany do autoryzowania Azure File Sync dostępu do konta magazynu, a te przypisania ról nie są migrowane z zasobami.

### <a name="azure-file-sync-service-principal"></a>Azure File Sync jednostki usługi

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Obraz przedstawiający usługę Azure Portal, zarządzanie subskrypcjami, zarejestrowanych dostawców zasobów." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        Aby można było autoryzować dostęp synchronizacji do konta magazynu, musi istnieć główna Azure File Sync usługi AAD. </br></br> Po utworzeniu nowej subskrypcji platformy Azure Azure File Sync zasobów *Microsoft.StorageSync* jest automatycznie rejestrowany w ramach subskrypcji. Rejestracja dostawcy zasobów udostępni jednostkę usługi *do* synchronizacji w dzierżawie Azure Active Directory, która zarządza subskrypcją. Nazwa główna usługi jest podobna do konta użytkownika w usłudze AAD. Możesz użyć jednostki usługi Azure File Sync, aby autoryzować dostęp do zasobów za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Jedyna synchronizacja zasobów wymaga dostępu do kont magazynu zawierających udziały plików, które mają być synchronizowane. *Program Microsoft.StorageSync* musi być przypisany do wbudowanej roli Czytelnik i **dostęp do danych** na koncie magazynu. </br></br> To przypisanie jest wykonywane automatycznie za pośrednictwem kontekstu zalogowanego użytkownika po dodaniu udziału plików do grupy synchronizacji lub, innymi słowy, utworzeniu punktu końcowego w chmurze. Gdy konto magazynu zostanie przeniesiony do nowej subskrypcji lub dzierżawy usługi AAD, to przypisanie roli zostanie utracone i musi zostać ręcznie ponownie [nawiązane.](#establish-sync-access-to-a-storage-account)
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Jeśli docelowa subskrypcja platformy Azure nie została niedawno utworzona, sprawdź, czy dostawca zasobów *Microsoft.StorageSync* jest zarejestrowany w subskrypcji. Jeśli tak nie jest, ręcznie dodaj go w tym samym bloku portalu.

### <a name="establish-sync-access-to-a-storage-account"></a>Ustanawianie dostępu synchronizacji do konta magazynu

Aby [autoryzować](#azure-file-sync-service-principal) dostęp do konta magazynu za pośrednictwem kontroli dostępu opartej na rolach (RBAC), należy użyć jednostki usługi Azure File Sync. *Program Microsoft.StorageSync* musi być przypisany do wbudowanej roli Czytelnik i dostęp do **danych** na koncie magazynu. 

To przypisanie jest zazwyczaj wykonywane automatycznie za pośrednictwem kontekstu zalogowanego użytkownika podczas dodawania udziału plików do grupy synchronizacji lub innymi słowy, tworzenia punktu końcowego w chmurze. Jeśli jednak konto magazynu zostanie przeniesiony do nowej subskrypcji lub dzierżawy usługi AAD, to przypisanie roli zostanie utracone i musi zostać ręcznie ponownie naniesiene.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="Obraz z wyświetloną jednostką usługi Microsoft.StorageSync przypisaną do roli Czytelnik i Dostęp do danych na koncie magazynu":::
    :::column-end:::
    :::column:::
        W Azure Portal przejdź do konta magazynu, do których musisz ponownie uwierzytelnić dostęp do synchronizacji. <ol><li>Wybierz **pozycję Kontrola dostępu (IAM)** w spisie treści po lewej stronie.</li><li>Wybierz kartę Przypisania ról, aby wyświetlić listę użytkowników i aplikacji (jednostki usługi), które mają dostęp do konta magazynu.</li><li>Wybierz pozycję **Dodaj**.</li><li>W polu **Rola wybierz** pozycję Czytelnik i Dostęp **do danych.**</li><li>W polu **Wybierz wpisz** *Microsoft.StorageSync,* wybierz rolę i kliknij przycisk **Zapisz.**</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>Przenoszenie do innego regionu świadczenia usługi Azure

Zasób Azure File Sync *usługi synchronizacji magazynu* i konta magazynu zawierające synchronizowane udziały plików mają region świadczenia usługi Azure, w których są wdrażane. Ten region jest określany podczas tworzenia zasobu. Region usługi synchronizacji magazynu i zasoby konta magazynu muszą być zgodne. Tych regionów nie można zmienić dla jednego z typów zasobów po ich utworzeniu.

Przypisywanie innego regionu do zasobu różni się od regionu pracy awaryjnej [,](#region-fail-over)co może być obsługiwane w zależności od ustawienia nadmiarowości konta magazynu.

## <a name="region-fail-over"></a>Region — fail over

[Usługa Azure Storage oferuje opcje nadmiarowości geograficznej](../common/storage-redundancy.md#geo-redundant-storage) dla konta magazynu. Te opcje nadmiarowości mogą stanowić problemy dla kont magazynu używanych z Azure File Sync. Głównym powodem jest to, że replikacja między regionami odległymi geograficznie nie jest wykonywana przez Azure File Sync, ale przez technologię replikacji magazynu wbudowaną w podsystem magazynowania na platformie Azure. Nie może ona w dowolnym momencie zrozumieć stanu aplikacji, a Azure File Sync jest aplikacją synchronizującą pliki z udziałami plików platformy Azure i z nich. Jeśli zdecydujesz się na dowolną z tych geograficznie rozsyłanych opcji nadmiarowości magazynu, nie utracisz wszystkich danych w przypadku awarii na dużą skalę. Należy jednak przewidywać [utratę danych.](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss)

> [!CAUTION]
> Aprowizowanie zasobów w odpowiednim regionie świadczenia usługi Azure nigdy nie jest właściwym zamiennikiem trybu fail-over. Jeśli zasoby znajdują się w regionie "niewłaściwym", należy rozważyć zatrzymanie synchronizacji i skonfigurowanie synchronizacji ponownie z nowymi udziałami plików platformy Azure, które są wdrażane w żądanym regionie.

Regionalne wprowadzenie w tryb fail over może zostać uruchomione przez firmę Microsoft w katastrofalnym zdarzeniu, które uniemożliwi korzystanie z centrów danych w regionie świadczenia usługi Azure przez dłuższy czas. Definicja przestojów, które firma może utrzymać w firmie, może być mniejsza niż czas przygotowania firmy Microsoft do odejścia przed rozpoczęciem regionalnej pracy awaryjnej. W takiej sytuacji klienci mogą również inicjować trybu [fail over.](../common/storage-initiate-account-failover.md)

> [!IMPORTANT]
> W przypadku pracy awaryjnej należy złożyć bilet pomocy technicznej, aby usługi synchronizacji magazynu, których to miało wpływ, działały ponownie.

## <a name="see-also"></a>Zobacz też

- [Omówienie przewodników migracji udziałów plików i synchronizacji platformy Azure](../files/storage-files-migration-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
- [Rozwiązywanie problemów z usługą Azure File Sync](file-sync-troubleshoot.md)
- [Planowanie wdrażania usługi Azure File Sync](file-sync-planning.md)