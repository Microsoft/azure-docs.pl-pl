---
title: Avere vFXT — wymagania wstępne — Azure
description: Dowiedz się więcej o zadaniach do wykonania przed utworzeniem klastra w programie avere vFXT for Azure, w tym o korzystaniu z subskrypcji, przydziałów i punktów końcowych usługi Storage.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: d87f57873a4bb84b20df3da3880017d9ef2484a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008416"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Przygotowanie do utworzenia systemu Avere vFXT

W tym artykule opisano wstępnie wymagane zadania tworzenia klastra avere vFXT.

## <a name="create-a-new-subscription"></a>Utwórz nową subskrypcję

Zacznij od utworzenia nowej subskrypcji platformy Azure. Użyj oddzielnej subskrypcji dla każdego projektu avere vFXT, aby uprościć śledzenie i czyszczenie wydatków oraz upewnić się, że nie ma to wpływu na inne projekty, przydziały lub ograniczanie zasobów podczas aprowizacji przepływu pracy klastra.

Aby utworzyć nową subskrypcję platformy Azure w Azure Portal:

1. Przejdź do [bloku subskrypcje](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Kliknij przycisk **+ Dodaj** w górnej części strony
1. Zaloguj się, jeśli zostanie wyświetlony monit
1. Wybierz ofertę i zapoznaj się z instrukcjami, aby utworzyć nową subskrypcję

## <a name="configure-subscription-owner-permissions"></a>Konfigurowanie uprawnień właściciela subskrypcji

Użytkownik z uprawnieniami właściciela dla subskrypcji powinien utworzyć klaster vFXT. Tworzenie klastra wymaga od właściciela zaakceptowania warunków użytkowania oprogramowania i autoryzacji zmian w zasobach sieciowych i pamięci masowej.

Istnieją pewne obejścia, aby umożliwić innym firmom tworzenie avere vFXT dla klastra platformy Azure. Te scenariusze wymagają ograniczenia zasobów i przypisywania do twórcy dodatkowych ról platformy Azure. We wszystkich tych przypadkach właściciel subskrypcji również musi [zaakceptować warunki oprogramowania avere vFXT](#accept-software-terms) przed czasem.

| Scenariusz | Ograniczenia | Role dostępu wymagane do utworzenia klastra avere vFXT |
|----------|--------|-------|
| Administrator grupy zasobów tworzy vFXT | Sieć wirtualna, kontroler klastra i węzły klastra muszą być tworzone w obrębie grupy zasobów. | Rola [administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator) i [współautor](../role-based-access-control/built-in-roles.md#contributor) — zarówno w zakresie docelowej grupy zasobów. |
| Użyj istniejącej, zewnętrznej sieci wirtualnej | Kontroler klastra i węzły klastra są tworzone w ramach grupy zasobów vFXT, ale używają istniejącej sieci wirtualnej w innej grupie zasobów. | (1) [administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator) i role [współautor](../role-based-access-control/built-in-roles.md#contributor) zakresu do grupy zasobów vFXT; i (2) [współautor maszyny wirtualnej](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator)i role [współautor avere](../role-based-access-control/built-in-roles.md#avere-contributor) należące do grupy zasobów sieci wirtualnej. |
| Rola niestandardowa dla twórców klastrów | Brak ograniczeń umieszczania zasobów. Ta metoda daje znaczące uprawnienia niebędące właścicielami. | Właściciel subskrypcji tworzy rolę niestandardową platformy Azure, jak wyjaśniono w [tym artykule](avere-vfxt-non-owner.md). |

## <a name="quota-for-the-vfxt-cluster"></a>Przydział dla klastra vFXT

Sprawdź, czy masz wystarczający przydział dla następujących składników platformy Azure. W razie konieczności [Poproś o zwiększenie limitu przydziału](../azure-portal/supportability/resource-manager-core-quotas-request.md).

> [!NOTE]
> Maszyny wirtualne i składniki SSD wymienione w tym miejscu są przeznaczone dla samego klastra vFXT. Należy pamiętać, że wymagany jest również przydział dla maszyn wirtualnych i dysków SSD, które będą używane dla farmy obliczeniowej.
>
> Upewnij się, że przydział jest włączony dla regionu, w którym zamierzasz uruchomić przepływ pracy.

|Składnik platformy Azure|limit przydziału|
|----------|-----------|
|Maszyny wirtualne|3 lub więcej E32s_v3 (jeden na węzeł klastra) |
|Magazyn SSD w warstwie Premium|200 GB na system operacyjny i od 1 TB do 4 TB miejsca obszaru pamięci podręcznej na węzeł |
|Konto magazynu (opcjonalnie) |v2|
|Magazyn zaplecza danych (opcjonalnie) |Jeden nowy kontener obiektów BLOB LRS |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Zaakceptuj warunki użytkowania oprogramowania

> [!TIP]
> Pomiń ten krok, jeśli właściciel subskrypcji utworzy klaster avere vFXT.

Podczas tworzenia klastra należy zaakceptować warunki użytkowania oprogramowania avere vFXT. Jeśli nie jesteś właścicielem subskrypcji, mieć właściciela subskrypcji, który wcześniej akceptuje warunki.

Ten krok należy wykonać tylko raz dla każdej subskrypcji.

Aby zaakceptować z góry warunki oprogramowania:

1. Otwórz powłokę chmury w Azure Portal lub przechodząc do witryny <https://shell.azure.com> . Zaloguj się przy użyciu identyfikatora subskrypcji.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. Wydaj to polecenie, aby akceptować warunki korzystania z usługi i włączyć dostęp programistyczny dla obrazu oprogramowania avere vFXT for Azure:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Utwórz punkt końcowy usługi magazynu w sieci wirtualnej (w razie konieczności)

[Punkt końcowy usługi](../virtual-network/virtual-network-service-endpoints-overview.md) utrzymuje ruch obiektów blob platformy Azure jako lokalny zamiast routingu poza siecią wirtualną. Jest to zalecane w przypadku wszystkich avere vFXT dla klastra platformy Azure, które używają obiektów blob platformy Azure do przechowywania danych zaplecza.

Jeśli podczas tworzenia klastra zostanie utworzona nowa sieć wirtualna, punkt końcowy zostanie utworzony automatycznie. Jeśli postanowisz istniejącą sieć wirtualną, musi ona mieć punkt końcowy usługi magazynu firmy Microsoft, jeśli chcesz utworzyć nowy kontener magazynu obiektów BLOB podczas tworzenia klastra.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Pomiń ten krok, jeśli tworzysz nową sieć wirtualną w ramach tworzenia klastra.
>* Punkt końcowy jest opcjonalny, jeśli nie tworzysz magazynu obiektów BLOB podczas tworzenia klastra. W takim przypadku można utworzyć punkt końcowy usługi później, jeśli zdecydujesz się użyć obiektu blob platformy Azure.

Utwórz punkt końcowy usługi magazynu na podstawie Azure Portal.

1. W portalu Otwórz listę sieci wirtualnych.
1. Wybierz sieć wirtualną dla klastra.
1. Kliknij pozycję **punkty końcowe usługi** w menu po lewej stronie.
1. Kliknij przycisk **Dodaj** w górnej części ekranu.
1. Wybierz usługę ``Microsoft.Storage`` .
1. Wybierz podsieć klastra.
1. Kliknij przycisk **Dodaj** w dolnej części.

   ![Zrzut ekranu Azure Portal z adnotacjami dotyczącymi kroków tworzenia punktu końcowego usługi](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>Następne kroki

Po wykonaniu tych wymagań wstępnych można utworzyć klaster. Aby uzyskać instrukcje, zapoznaj [się z artykułem wdrażanie klastra vFXT](avere-vfxt-deploy.md) .