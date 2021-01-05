---
title: Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal
description: Dowiedz się, jak ograniczyć ataki za pomocą standardu Azure DDoS Protection.
services: ddos-protection
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: fd154ec40ed4d1650e44a67fd5d362a2bc0c519d
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813942"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>Szybki Start: Tworzenie i Konfigurowanie Azure DDoS Protection Standard

Rozpocznij pracę ze standardem Azure DDoS Protection przy użyciu Azure Portal. 

Plan ochrony DDoS definiuje zbiór sieci wirtualnych, dla których włączono standard ochrony DDoS w ramach subskrypcji. Można skonfigurować jeden plan ochrony DDoS dla Twojej organizacji i połączyć sieci wirtualne z wielu subskrypcji w ramach tego samego planu. 

W tym przewodniku szybki start utworzysz plan ochrony DDoS i połączysz go z siecią wirtualną. 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com. Upewnij się, że Twoje konto jest przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie działania wymienione w przewodniku jak uzyskać [uprawnienia](manage-permissions.md).

## <a name="create-a-ddos-protection-plan"></a>Utwórz plan ochrony DDoS

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu Azure Portal.
2. Przeszukaj termin *DDoS*. Po pojawieniu się **planu ochrony DDoS** w wynikach wyszukiwania zaznacz je.
3. Wybierz przycisk **Utwórz**.
4. Wprowadź lub wybierz następujące wartości, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie        |Wartość                                              |
    |---------      |---------                                          |
    |Nazwa           | Wprowadź _MyDdosProtectionPlan_.                     |
    |Subskrypcja   | Wybierz subskrypcję.                         |
    |Grupa zasobów | Wybierz pozycję **Utwórz nową** i wprowadź nazwę _zasobu_.|
    |Lokalizacja       | Wprowadź _Wschodnie stany USA_.                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Włącz ochronę DDoS dla sieci wirtualnej

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Włącz ochronę DDoS dla nowej sieci wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu Azure Portal.
2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Sieć wirtualna**.
3. Wprowadź lub wybierz następujące wartości, zaakceptuj pozostałe ustawienia domyślne, a następnie wybierz pozycję **Utwórz**:

    | Ustawienie         | Wartość                                           |
    | ---------       | ---------                                       |
    | Nazwa            | Wprowadź _MyVnet_.                                 |
    | Subskrypcja    | Wybierz subskrypcję.                                    |
    | Grupa zasobów  | Wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję Moja **resourceName** . |
    | Lokalizacja        | Wprowadź _Wschodnie stany USA_                                                    |
    | Usługa DDoS Protection w warstwie Standardowa | Wybierz pozycję **Włącz**. Wybrany plan może być w tej samej lub innej subskrypcji niż sieć wirtualna, ale obie subskrypcje muszą być skojarzone z tą samą dzierżawą Azure Active Directory.|

Nie można przenieść sieci wirtualnej do innej grupy zasobów lub subskrypcji, gdy dla sieci wirtualnej jest włączony Standard DDoS. Jeśli musisz przenieść sieć wirtualną z włączonym standardem DDoS, wyłącz najpierw pozycję DDoS Standard, Przenieś sieć wirtualną, a następnie Włącz Standard DDoS. Po przeniesieniu wartości progowe dla wszystkich chronionych publicznych adresów IP w sieci wirtualnej zostaną zresetowane.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Włącz ochronę DDoS dla istniejącej sieci wirtualnej

1. Utwórz plan ochrony DDoS, wykonując kroki opisane w temacie [Tworzenie planu ochrony DDoS](#create-a-ddos-protection-plan), jeśli nie masz istniejącego planu ochrony DDoS.
2. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu Azure Portal.
3. Wprowadź nazwę sieci wirtualnej, dla której chcesz włączyć DDoS Protection Standard dla w **polu Wyszukaj zasoby, usługi i dokumenty** w górnej części portalu. Gdy nazwa sieci wirtualnej pojawi się w wynikach wyszukiwania, wybierz ją.
4. W obszarze **Ustawienia** wybierz pozycję **Ochrona DDoS**.
5. Wybierz pozycję **Standardowy**. W obszarze **Plan ochrony DDoS** wybierz istniejący plan ochrony DDoS lub plan, który został utworzony w kroku 1, a następnie wybierz pozycję **Zapisz**. Wybrany plan może być w tej samej lub innej subskrypcji niż sieć wirtualna, ale obie subskrypcje muszą być skojarzone z tą samą dzierżawą Azure Active Directory.

### <a name="enable-ddos-protection-for-all-virtual-networks"></a>Włącz ochronę DDoS dla wszystkich sieci wirtualnych

Te [zasady](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Policy%20-%20Virtual%20Networks%20should%20be%20associated%20with%20an%20Azure%20DDoS%20Protection%20Standard%20plan) wykryją sieci wirtualne w zdefiniowanym zakresie, w którym nie włączono standardu DDoS Protection, a następnie opcjonalnie utworzysz zadanie korygowania, które utworzy skojarzenie służące do ochrony sieci wirtualnej. Szczegółowe instrukcje krok po kroku dotyczące wdrażania tych zasad znajdują się w temacie https://aka.ms/ddosvnetpolicy-techcommunity .

## <a name="validate-and-test"></a>Weryfikuj i Testuj

Najpierw zapoznaj się ze szczegółami planu ochrony DDoS:

1. Wybierz pozycję **wszystkie usługi** u góry, po lewej stronie portalu.
2. Wprowadź *DDoS* w polu **Filtr** . Gdy **plany ochrony DDoS** są wyświetlane w wynikach, zaznacz je.
3. Wybierz z listy plan ochrony DDoS.

Powinna zostać wyświetlona Sieć wirtualna _MyVnet_ . 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz zachować zasoby dla następnego samouczka. Jeśli nie jest już potrzebne, Usuń grupę _zasobów zasobu._ Po usunięciu grupy zasobów należy również usunąć plan ochrony DDoS i wszystkie powiązane z nim zasoby. Jeśli nie zamierzasz korzystać z tego planu ochrony DDoS, Usuń zasoby, aby uniknąć niepotrzebnych opłat.

   >[!WARNING]
   >Ta akcja to irreversable.

1. W Azure Portal Wyszukaj i wybierz **grupy zasobów** lub wybierz pozycję **grupy zasobów** z menu Azure Portal.

2. Odfiltruj lub przewiń w dół _, aby znaleźć grupę zasobów zasobu_ .

3. Wybierz grupę zasobów, a następnie wybierz pozycję **Usuń grupę zasobów**.

4. Wpisz nazwę grupy zasobów do zweryfikowania, a następnie wybierz pozycję **Usuń**.

Aby wyłączyć ochronę DDoS dla sieci wirtualnej: 

1. W **polu Wyszukaj zasoby, usługi i dokumenty** w górnej części portalu wprowadź nazwę sieci wirtualnej, dla której chcesz wyłączyć usługę DDoS Protection w warstwie Standardowa. Gdy nazwa sieci wirtualnej pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Wyłącz** **w obszarze DDoS Protection Standard**.

W celu usunięcia planu ochrony DDoS należy najpierw usunąć skojarzenie wszystkich sieci wirtualnych. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wyświetlać i konfigurować dane telemetryczne dla planu ochrony DDoS, przejdź do samouczków.

> [!div class="nextstepaction"]
> [Wyświetlanie i konfigurowanie telemetrii ochrony przed atakami DDoS](telemetry.md)
