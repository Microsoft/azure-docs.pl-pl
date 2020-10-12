---
title: Zwiększenie limitu sieci | Microsoft Docs
description: Zwiększanie limitu sieci
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: acb05da0255445de31e08f2724dcb484a3e05b17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764269"
---
# <a name="networking-limit-increase"></a>Zwiększanie limitu sieci

Użyj [Azure Portal](https://portal.azure.com) , aby zwiększyć limit przydziału sieci.

Aby wyświetlić bieżące użycie sieci i limit przydziału w Azure Portal, Otwórz swoją subskrypcję, a następnie wybierz pozycję **użycie + przydziały**. Możesz również użyć poniższych opcji, aby wyświetlić użycie sieci i limity.

* [Interfejs wiersza polecenia użycia](/cli/azure/network#az-network-list-usages)
* [Program PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [Interfejs API użycia sieci](/rest/api/virtualnetwork/virtualnetworks/listusage)

Możesz poprosić o zwiększenie, korzystając z **pomocy i obsługi technicznej** oraz **przydziałów** w portalu.

> [!Note]
> Aby zmienić domyślny rozmiar **publicznych prefiksów adresów IP**, wybierz **minimalną długość prefiksu sieci publicznego adresu IP** z listy rozwijanej.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Żądaj zwiększenia przydziału sieci na poziomie subskrypcji przy użyciu pomocy i obsługi technicznej

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie obsługi przy użyciu **pomocy i obsługi technicznej** w Azure Portal.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie wybierz pozycję **Pomoc i obsługa techniczna** z menu Azure Portal lub Wyszukaj i wybierz pozycję **Pomoc i obsługa techniczna**.

    ![Pomoc i obsługa techniczna](./media/networking-quota-request/help-plus-support.png)

1. Wybierz pozycję **Nowe żądanie obsługi**.

    ![Nowy wniosek o pomoc techniczną](./media/networking-quota-request/new-support-request.png)

1. W obszarze **typ problemu**wybierz pozycję **usługi i limity subskrypcji (przydziały)**.

    ![Lista rozwijana z typem problemu wybierz limity subskrypcji](./media/networking-quota-request/select-quota-issue-type.png)

1. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

    ![Wybieranie Aktualności subskrypcji](./media/networking-quota-request/select-subscription-support-request.png)

1. W obszarze **Typ limitu przydziału**wybierz pozycję **Sieć**. Wybierz pozycję **Dalej: rozwiązania**.

    ![Wybierz typ limitu przydziału](./media/networking-quota-request/select-quota-type-network.png)

1. W obszarze **Szczegóły problemu**wybierz pozycję **Podaj szczegóły** i wprowadź dodatkowe informacje, aby pomóc w przetwarzaniu żądania.

    ![Podaj szczegóły](./media/networking-quota-request/provide-details-link.png)

1. W panelu **Szczegóły przydziału** wybierz model wdrażania, lokalizację i zasoby, które mają zostać uwzględnione w żądaniu.

    ![Szczegóły przydziału DM](./media/networking-quota-request/quota-details-network.png)

1. Wprowadź nowe limity, które chcesz wykonać w ramach subskrypcji. Aby usunąć wiersz, usuń zaznaczenie zasobu z menu **zasoby** lub wybierz ikonę Odrzuć "x". Po wprowadzeniu przydziału dla każdego zasobu wybierz pozycję **Zapisz i Kontynuuj** , aby kontynuować tworzenie żądania obsługi.

    ![Nowe limity](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Zażądaj zwiększenia przydziału sieci na poziomie subskrypcji przy użyciu zasobów użycia i przydziałów

Postępuj zgodnie z tymi instrukcjami, aby utworzyć żądanie pomocy technicznej przy użyciu usługi **Usage + limit przydziału** w Azure Portal.

1. W programie https://portal.azure.com Wyszukaj i wybierz pozycję **subskrypcje**.

    ![Subskrypcje](./media/networking-quota-request/search-for-suscriptions.png)

1. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

    ![Wybieranie subskrypcji](./media/networking-quota-request/select-subscription-change-quota.png)

1. Wybierz **użycie i przydziały**

    ![Wybierz użycie i przydziały](./media/networking-quota-request/select-usage-plus-quotas.png)

1. W prawym górnym rogu wybierz pozycję **Zwiększ liczbę żądań**.

    ![Zwiększ liczbę żądań](./media/networking-quota-request/request-increase-from-subscription.png)

1. Wykonaj kroki zaczynające się od kroku 3 w [Zażądaj zwiększenia przydziału sieci na poziomie subskrypcji](#request-networking-quota-increase-at-subscription-level-using-help--support).

## <a name="about-networking-limits"></a>Informacje o limitach sieci

Aby dowiedzieć się więcej na temat limitów sieci, zobacz sekcję dotyczącą [sieci](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) na stronie limity lub nasze często zadawane pytania dotyczące sieci.
