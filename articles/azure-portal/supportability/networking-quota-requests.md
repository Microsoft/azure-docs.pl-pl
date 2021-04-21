---
title: Zwiększanie limitu sieci
description: Zwiększanie limitu sieci
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 801f0424e7ec15fbde58f35975f4c7eca4c9a5de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775569"
---
# <a name="networking-limit-increase"></a>Zwiększanie limitu sieci

Użyj [Azure Portal,](https://portal.azure.com) aby zwiększyć limit przydziału sieci.

Aby wyświetlić bieżące użycie sieci i limity przydziału w programie Azure Portal, otwórz subskrypcję, a następnie wybierz pozycję **Użycie i przydziały.** Możesz również użyć poniższych opcji, aby wyświetlić użycie sieci i limity.

* [Interfejs wiersza polecenia użycia](/cli/azure/network#az_network_list_usages)
* [Program PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [Interfejs API użycia sieci](/rest/api/virtualnetwork/virtualnetworks/listusage)

Możesz zażądać zwiększenia, korzystając z pomocy **i obsługi technicznej** lub w **witrynie Usages + quotas** (Użycie i przydziały) w portalu.

> [!Note]
> Aby zmienić domyślny rozmiar prefiksów publicznych **adresów IP,** wybierz z listy rozwijanej pozycję Minimalna długość prefiksu międzysieciowego publicznego adresu **IP.**

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Żądanie zwiększenia limitu przydziału sieci na poziomie subskrypcji przy użyciu pomocy i obsługi technicznej

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć wniosek o pomoc techniczną, korzystając z pomocy **i obsługi technicznej** w Azure Portal.

1. Zaloguj się do Azure Portal , [a](https://portal.azure.com)następnie wybierz pozycję Pomoc i obsługa techniczna z menu Azure Portal lub wyszukaj i wybierz pozycję **Pomoc i obsługa techniczna.** 

    ![Pomoc i obsługa techniczna](./media/networking-quota-request/help-plus-support.png)

1. Wybierz pozycję **Nowe żądanie obsługi**.

    ![Nowy wniosek o pomoc techniczną](./media/networking-quota-request/new-support-request.png)

1. W **przypadku typu problemu** wybierz pozycję **Limity usługi i subskrypcji (limity przydziału).**

    ![Wybieranie limitów subskrypcji z listy rozwijanej typu problemu](./media/networking-quota-request/select-quota-issue-type.png)

1. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

    ![Wybierz subskrypcję newSR](./media/networking-quota-request/select-subscription-support-request.png)

1. W **obszarze Typ limitu przydziału** wybierz pozycję **Sieć.** Wybierz **pozycję Dalej: Rozwiązania.**

    ![Wybierz typ limitu przydziału](./media/networking-quota-request/select-quota-type-network.png)

1. W **szczegółach** problemu wybierz **pozycję Podaj szczegóły** i podaj dodatkowe informacje, aby ułatwić przetwarzanie żądania.

    ![Podaj szczegóły](./media/networking-quota-request/provide-details-link.png)

1. Na **panelu Szczegóły** limitu przydziału wybierz model wdrożenia, lokalizację i zasoby do dołączyć do żądania.

    ![Dm szczegóły limitu przydziału](./media/networking-quota-request/quota-details-network.png)

1. Wprowadź nowe limity w subskrypcji. Aby usunąć wiersz, usuń zaznaczenie zasobu z menu **Zasoby** lub wybierz ikonę odrzucania "x". Po wprowadzeniu limitu przydziału dla każdego zasobu wybierz pozycję **Zapisz** i kontynuuj tworzenie wniosku o pomoc techniczną.

    ![Nowe limity](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Żądanie zwiększenia limitu przydziału sieci na poziomie subskrypcji przy użyciu użycia i przydziałów

Postępuj zgodnie z tymi instrukcjami, aby utworzyć wniosek o pomoc techniczną przy użyciu **użycia i** limitu przydziału w Azure Portal.

1. W https://portal.azure.com skrypcie wyszukaj i wybierz **pozycję Subskrypcje.**

    ![Subskrypcje](./media/networking-quota-request/search-for-suscriptions.png)

1. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

    ![Wybieranie subskrypcji](./media/networking-quota-request/select-subscription-change-quota.png)

1. Wybierz **pozycję Użycie i limity przydziału**

    ![Wybieranie użycia i przydziałów](./media/networking-quota-request/select-usage-plus-quotas.png)

1. W prawym górnym rogu wybierz pozycję **Zażądaj zwiększenia**.

    ![Żądanie zwiększenia](./media/networking-quota-request/request-increase-from-subscription.png)

1. Wykonaj kroki rozpoczynające się od kroku 3 w te [tematu Request Networking quota increase at subscription level (Żądanie zwiększenia limitu przydziału sieci na poziomie subskrypcji).](#request-networking-quota-increase-at-subscription-level-using-help--support)

## <a name="about-networking-limits"></a>Informacje o limitach sieci

Aby dowiedzieć się więcej na temat limitów sieci, zobacz [sekcję Sieć](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) na stronie limitów lub nasze często zadawane pytania dotyczące limitów sieci.
