---
title: Przenoszenie aplikacji logiki między subskrypcjami, grupami zasobów lub regionami
description: Migrowanie aplikacji logiki lub kont integracji do innych subskrypcji platformy Azure, grup zasobów lub lokalizacji (regionów)
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: aca2c51ff14b99ba41b159cf32e59dc861de7a53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87826215"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>Przenoszenie zasobów aplikacji logiki do innych grup zasobów platformy Azure, regionów lub subskrypcji

Aby przeprowadzić migrację aplikacji logiki lub powiązanych zasobów do innej grupy zasobów platformy Azure, regionu lub subskrypcji, istnieją różne sposoby wykonywania tych zadań, takie jak Azure Portal, Azure PowerShell, interfejs wiersza polecenia platformy Azure i interfejsy API REST. Przed przeniesieniem zasobów zapoznaj się z następującymi kwestiami: 

* Można przenosić tylko [określone typy zasobów aplikacji logiki](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) między grupami zasobów platformy Azure lub subskrypcjami.

* Sprawdź [limity](../logic-apps/logic-apps-limits-and-config.md) liczby zasobów aplikacji logiki, które mogą znajdować się w ramach subskrypcji platformy Azure i w każdym regionie świadczenia usługi Azure. Te limity mają wpływ na to, czy można przenosić określone typy zasobów, gdy region pozostaje taki sam w obrębie subskrypcji lub grup zasobów. Na przykład dla każdego regionu platformy Azure w każdej subskrypcji platformy Azure można mieć tylko jedno konto integracji warstwy Bezpłatna.

* Podczas przenoszenia zasobów platforma Azure tworzy nowe identyfikatory zasobów. Upewnij się, że zamiast tego należy użyć nowych identyfikatorów i zaktualizować wszelkie skrypty lub narzędzia skojarzone z przeniesionymi zasobami.

* Po przeprowadzeniu migracji aplikacji logiki między subskrypcjami, grupami zasobów lub regionami należy ponownie utworzyć lub autoryzować wszystkie połączenia wymagające uwierzytelniania Open (OAuth).

* [Środowisko usługi integracji (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) można przenieść tylko do innej grupy zasobów, która istnieje w tym samym regionie świadczenia usługi Azure lub w ramach subskrypcji platformy Azure. Nie można przenieść ISE do grupy zasobów, która istnieje w innym regionie świadczenia usługi Azure lub w ramach subskrypcji platformy Azure. Ponadto po takim przeniesieniu należy zaktualizować wszystkie odwołania do ISE w przepływach pracy aplikacji logiki, kontach integracji, połączeniach i tak dalej.

## <a name="prerequisites"></a>Wymagania wstępne

* Ta sama subskrypcja platformy Azure, która została użyta do utworzenia aplikacji logiki lub konta integracji, które chcesz przenieść

* Uprawnienia właściciela zasobu do przenoszenia i konfigurowania zasobów, których chcesz użyć. Dowiedz się więcej [na temat kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/built-in-roles.md#owner).

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Przenoszenie zasobów między subskrypcjami

Aby przenieść zasób, taki jak aplikacja logiki lub konto integracji, do innej subskrypcji platformy Azure, można użyć Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST. Te kroki obejmują Azure Portal, których można użyć, gdy region zasobu pozostaje taki sam. Aby poznać inne kroki i ogólne przygotowania, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).

1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz zasób aplikacji logiki, który chcesz przenieść.

1. Na stronie **Przegląd** zasobu obok pozycji **subskrypcja**wybierz łącze **Zmień** .

1. Na stronie **przenoszenie zasobów** wybierz zasób aplikacji logiki i wszystkie powiązane zasoby, które chcesz przenieść.

1. Z listy **subskrypcja** wybierz subskrypcję docelową.

1. Z listy **Grupa zasobów** wybierz docelową grupę zasobów. Lub, aby utworzyć inną grupę zasobów, wybierz pozycję **Utwórz nową grupę**.

1. Aby potwierdzić zrozumienie, że wszelkie skrypty lub narzędzia skojarzone z przeniesionymi zasobami nie będą działały do momentu zaktualizowania ich przy użyciu nowych identyfikatorów zasobów, zaznacz pole potwierdzenia, a następnie wybierz przycisk **OK**.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Przenoszenie zasobów między grupami zasobów

Aby przenieść zasób, taki jak aplikacja logiki, konto integracji lub [środowisko usługi integracji (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), do innej grupy zasobów platformy Azure, można użyć Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST. Te kroki obejmują Azure Portal, których można użyć, gdy region zasobu pozostaje taki sam. Aby poznać inne kroki i ogólne przygotowania, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Przed rzeczywistym przeniesieniem zasobów między grupami można sprawdzić, czy można pomyślnie przenieść zasób do innej grupy. Aby uzyskać więcej informacji, zobacz [Weryfikowanie przenoszenia](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move).

1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz zasób aplikacji logiki, który chcesz przenieść.

1. Na stronie **Przegląd** zasobu obok pozycji **Grupa zasobów**wybierz łącze **Zmień** .

1. Na stronie **przenoszenie zasobów** wybierz zasób aplikacji logiki i wszystkie powiązane zasoby, które chcesz przenieść.

1. Z listy **Grupa zasobów** wybierz docelową grupę zasobów. Lub, aby utworzyć inną grupę zasobów, wybierz pozycję **Utwórz nową grupę**.

1. Aby potwierdzić zrozumienie, że wszelkie skrypty lub narzędzia skojarzone z przeniesionymi zasobami nie będą działały do momentu zaktualizowania ich przy użyciu nowych identyfikatorów zasobów, zaznacz pole potwierdzenia, a następnie wybierz przycisk **OK**.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Przenoszenie zasobów między regionami

Jeśli chcesz przenieść aplikację logiki do innego regionu, opcje są zależne od sposobu utworzenia aplikacji logiki. W oparciu o wybraną opcję należy ponownie utworzyć lub zatwierdzić połączenia w aplikacji logiki.

* W Azure Portal ponownie Utwórz aplikację logiki w nowym regionie i ponownie skonfiguruj ustawienia przepływu pracy. Aby zaoszczędzić czas, można skopiować źródłową definicję przepływu pracy i połączenia z aplikacji źródłowej do aplikacji docelowej. Aby wyświetlić "kod" za pomocą aplikacji logiki, na pasku narzędzi projektanta aplikacji logiki wybierz pozycję **Widok kodu**.

* Za pomocą programu Visual Studio i narzędzi Azure Logic Apps Tools for Visual Studio, możesz [otworzyć i pobrać aplikację logiki](../logic-apps/manage-logic-apps-with-visual-studio.md) z Azure Portal jako [szablon Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Ten szablon jest przede wszystkim gotowy do wdrożenia i zawiera definicje zasobów dla aplikacji logiki, w tym przepływ pracy oraz połączenia. Szablon deklaruje także parametry wartości, które mają być używane podczas wdrażania. Dzięki temu można łatwiej zmienić miejsce i sposób wdrażania aplikacji logiki na podstawie Twoich potrzeb. Aby określić lokalizację i inne informacje niezbędne do wdrożenia, można użyć oddzielnego pliku parametrów.

* Jeśli aplikacja logiki została utworzona i wdrożona przy użyciu narzędzi ciągłej integracji (CI) i ciągłe dostarczanie (CD), takich jak Azure Pipelines na platformie Azure DevOps, można wdrożyć aplikację w innym regionie przy użyciu tych narzędzi.

Aby uzyskać więcej informacji na temat szablonów wdrażania dla usługi Logic Apps, zobacz następujące tematy:

* [Przegląd: Automatyzowanie wdrażania Azure Logic Apps przy użyciu szablonów Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Znajdowanie, otwieranie i pobieranie aplikacji logiki z Azure Portal do programu Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Tworzenie Azure Resource Manager szablonów dla Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Wdrażanie szablonów usługi Azure Resource Manager dla usługi Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Powiązane zasoby

Niektóre zasoby platformy Azure, takie jak zasoby lokalnej bramy danych na platformie Azure, mogą znajdować się w regionie, który różni się od aplikacji logiki, które używają tych zasobów. Jednak inne zasoby platformy Azure, takie jak połączone konta integracji, muszą znajdować się w tym samym regionie, w którym znajdują się Aplikacje logiki. Na podstawie Twojego scenariusza upewnij się, że aplikacje logiki mogą uzyskać dostęp do zasobów, których aplikacje oczekują w tym samym regionie.

Na przykład aby połączyć aplikację logiki z kontem integracji, oba zasoby muszą znajdować się w tym samym regionie. W scenariuszach takich jak odzyskiwanie awaryjne zwykle mają być używane konta integracji, które mają taką samą konfigurację i artefakty. W innych scenariuszach mogą być potrzebne konta integracji z różnymi konfiguracjami i artefaktami.

Łączniki niestandardowe w Azure Logic Apps są widoczne dla autorów i użytkowników, którzy mają tę samą subskrypcję platformy Azure i tę samą dzierżawę Azure Active Directory. Te łączniki są dostępne w tym samym regionie, w którym są wdrażane aplikacje logiki. Aby uzyskać więcej informacji, zobacz [Share custom connectors in your organization (Udostępnianie łączników niestandardowych w organizacji)](/connectors/custom-connectors/share).

Szablon pobrany z programu Visual Studio zawiera tylko definicje zasobów dla aplikacji logiki i jej połączeń. Jeśli więc aplikacja logiki korzysta z innych zasobów, na przykład konta integracji i artefaktów B2B, takich jak partnerzy, umowy i schematy, należy wyeksportować szablon tego konta integracji przy użyciu Azure Portal. Ten szablon zawiera definicje zasobów dla konta integracji i artefaktów. Jednak szablon nie jest w pełni sparametryzowane. Dlatego należy ręcznie Sparametryzuj wartości, których chcesz użyć do wdrożenia.

### <a name="export-templates-for-integration-accounts"></a>Eksportowanie szablonów dla kont integracji

1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz konto integracji.

1. W menu konta integracji w obszarze **Ustawienia**wybierz pozycję **Eksportuj szablon**.

1. Na pasku narzędzi wybierz pozycję **Pobierz**i Zapisz szablon.

1. Otwórz i edytuj szablon, aby Sparametryzuj wymagane wartości dla wdrożenia.

## <a name="next-steps"></a>Następne kroki

[Przenoszenie zasobów platformy Azure do nowych grup zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md)
