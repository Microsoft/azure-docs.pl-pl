---
title: Przenoszenie zasobów platformy Azure między grupami zasobów, subskrypcjami lub regionami.
description: Omówienie typów zasobów platformy Azure, które można przenosić między grupami zasobów, subskrypcjami lub regionami.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 646c0892ac3609fc531e91f4bfb9c1de6a61bc38
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730510"
---
# <a name="move-azure-resources-across-resource-groups-subscriptions-or-regions"></a>Przenoszenie zasobów platformy Azure między grupami zasobów, subskrypcjami lub regionami

Zasoby platformy Azure można przenosić do nowej grupy zasobów, subskrypcji lub regionów.

## <a name="move-resources-across-resource-groups-or-subscriptions"></a>Przenoszenie zasobów między grupami zasobów lub subskrypcjami

Zasoby platformy Azure można przenieść do innej subskrypcji platformy Azure lub innej grupy zasobów w ramach tej samej subskrypcji. Do przenoszenia zasobów możesz użyć witryny Azure Portal, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Aby dowiedzieć się więcej, [zobacz Move resources to a new resource group or subscription (Przenoszenie](move-resource-group-and-subscription.md)zasobów do nowej grupy zasobów lub subskrypcji).

### <a name="upgrade-a-subscription"></a>Uaktualnianie subskrypcji

Jeśli rzeczywiście chcesz podwyższyć subskrypcję platformy Azure (np. przełączyć się z bezpłatnej na płatność zgodnie z potrzebami), musisz przekonwertować subskrypcję.

- Aby podwyższyć bezpłatną wersję próbną, zobacz Uaktualnianie bezpłatnej wersji próbnej lub Microsoft Imagine platformy Azure do subskrypcji [z płatnością zgodnie z potrzebami.](../../cost-management-billing/manage/upgrade-azure-subscription.md)
- Aby zmienić konto z płatnością zgodnie z potrzebami, zobacz Zmienianie subskrypcji platformy Azure z płatnością zgodnie z potrzebami na [inną ofertę.](../../cost-management-billing/manage/switch-azure-offer.md)

Jeśli nie możesz przekonwertować subskrypcji, [utwórz wniosek o pomoc techniczną platformy Azure.](../../azure-portal/supportability/how-to-create-azure-support-request.md) Wybierz **pozycję Zarządzanie subskrypcjami** jako typ problemu.

## <a name="move-resources-across-regions"></a>Przenoszenie zasobów między regionami

Obszary geograficzne, regiony i strefy dostępności platformy Azure stanowią podstawę globalnej infrastruktury platformy Azure. Obszary [geograficzne platformy](https://azure.microsoft.com/global-infrastructure/geographies/) Azure zwykle zawierają co najmniej dwa regiony platformy [Azure.](https://azure.microsoft.com/global-infrastructure/regions/) Region to obszar w obrębie obszaru geograficznego zawierający Strefy dostępności i wiele centrów danych.

Po wdrożeniu zasobów w określonym regionie świadczenia usługi Azure istnieje wiele powodów, dla których warto przenieść zasoby do innego regionu.

- **Dopasuj do uruchomienia regionu:** Przenieś zasoby do nowo wprowadzonego regionu świadczenia usługi Azure, który nie był wcześniej dostępny.
- **Dopasowanie do usług/funkcji:** Przenieś zasoby, aby korzystać z usług lub funkcji dostępnych w określonym regionie.
- **Reagowanie na rozwój firmy:** Przenoszenie zasobów do regionu w odpowiedzi na zmiany biznesowe, takie jak fuzje lub przejęcia.
- **Wyrównaj w pobliżu:** Przenieś zasoby do regionu lokalnego dla swojej firmy.
- **Spełnianie wymagań dotyczących danych:** Przenoszenie zasobów w celu dostosowania ich do wymagań dotyczących rezydencji danych lub potrzeb klasyfikacji danych. [Dowiedz się więcej](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Reagowanie na wymagania dotyczące wdrażania:** Przenieś zasoby, które zostały wdrożone przez błąd, lub przenieś w odpowiedzi na potrzeby pojemności.
- **Reagowanie na likwidowanie:** Przenoszenie zasobów z powodu zlikwidowanych regionów.

### <a name="move-resources-with-resource-mover"></a>Przenoszenie zasobów za pomocą przenoszenia zasobów

Zasoby można przenosić do innego regionu za pomocą [Azure Resource Mover](../../resource-mover/overview.md). Resource Mover zapewnia:

- Pojedyncze centrum do przenoszenia zasobów między regionami.
- Skrócenie czasu i złożoności przenoszenia. Wszystko, czego potrzebujesz, znajduje się w jednej lokalizacji.
- Proste i spójne środowisko do przenoszenia różnych typów zasobów platformy Azure.
- Prosty sposób identyfikowania zależności między zasobami, które chcesz przenieść. Ta identyfikacja ułatwia przenoszenie powiązanych zasobów razem, dzięki czemu wszystko działa zgodnie z oczekiwaniami w regionie docelowym po zakończeniu przenoszenia.
- Automatyczne oczyszczanie zasobów w regionie źródłowym, jeśli chcesz je usunąć po zakończeniu przenoszenia.
- Testowania. Możesz wypróbować przeniesienie, a następnie odrzucić je, jeśli nie chcesz wykonać pełnego przeniesienia.

Zasoby można przenosić do innego regionu przy użyciu kilku różnych metod:

- **Rozpocznij przenoszenie zasobów z grupy zasobów:** ta metoda umożliwia rozpoczęcie przenoszenia regionu z grupy zasobów. Po wybraniu zasobów, które chcesz przenieść, proces jest kontynuowany w centrum usługi Resource Mover, aby sprawdzić zależności zasobów i aranżować proces przenoszenia. [Dowiedz się więcej](../../resource-mover/move-region-within-resource-group.md).
- Rozpocznij przenoszenie zasobów bezpośrednio z centrum **usługi Resource Mover:** za pomocą tej metody rozpoczynasz proces przenoszenia regionu bezpośrednio w centrum. [Dowiedz się więcej](../../resource-mover/tutorial-move-region-virtual-machines.md).

## <a name="next-steps"></a>Następne kroki

- Aby sprawdzić, czy typ zasobu obsługuje przenoszenie, zobacz [Obsługa operacji przenoszenia dla zasobów](move-support-resources.md).
- Aby dowiedzieć się więcej o procesie przenoszenia regionu, zobacz [About the move process (Informacje o procesie przenoszenia).](../../resource-mover/about-move-process.md)
