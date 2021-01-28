---
title: Rozwiązywanie problemów z chmurą wiosenną platformy Azure w sieci wirtualnej
description: Przewodnik rozwiązywania problemów z siecią wirtualną w chmurze ze sprężyną Azure.
author: mikedodaro
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 8491eb02fd61f291904d57990b3785ce93239964
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935267"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>Rozwiązywanie problemów z chmurą wiosenną platformy Azure w sieciach wirtualnych

Ten dokument pomoże Ci rozwiązać różne problemy, które mogą wystąpić w przypadku korzystania z chmury Azure wiosny w sieciach wirtualnych.

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Wystąpił problem podczas tworzenia wystąpienia usługi w chmurze ze sprężyną Azure

Aby utworzyć wystąpienie chmury wiosennej platformy Azure, musisz mieć wystarczające uprawnienia do wdrażania wystąpienia w sieci wirtualnej.  Wystąpienie usługi w chmurze sprężynowej musi [nadawać uprawnienia usługi w chmurze Azure wiosny do sieci wirtualnej](spring-cloud-tutorial-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network).

Jeśli używasz Azure Portal do skonfigurowania wystąpienia usługi w chmurze Azure wiosny, Azure Portal sprawdzi uprawnienia.

Aby skonfigurować wystąpienie usługi w chmurze ze sprężyną Azure przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), sprawdź, czy:

- Subskrypcja jest aktywna.
- Lokalizacja jest obsługiwana przez chmurę wiosenną platformy Azure.
- Grupa zasobów dla wystąpienia została już utworzona.
- Nazwa zasobu jest zgodna z regułą nazewnictwa. Musi zawierać tylko małe litery, cyfry i łączniki. Pierwszy znak musi być literą. Ostatni znak musi być literą lub cyfrą. Wartość musi zawierać od 2 do 32 znaków.

Aby skonfigurować wystąpienie usługi w chmurze ze sprężyną Azure przy użyciu szablonu Menedżer zasobów, zapoznaj się z tematem [Opis struktury i składni Azure Resource Manager szablonów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates).

### <a name="common-creation-issues"></a>Typowe problemy z tworzeniem

| Komunikat o błędzie | Jak rozwiązać problem |
|------|------|
| Zasoby utworzone przez chmurę wiosenną platformy Azure zostały niedozwolone przez zasady. | Zasoby sieciowe zostaną utworzone podczas wdrażania chmury Azure wiosennej we własnej sieci wirtualnej. Sprawdź, czy masz zdefiniowane [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) , aby zablokować te operacje tworzenia. Nie można utworzyć zasobów w komunikacie o błędzie. |
| Podane podsieci są skojarzone z tabelami tras, Usuń ich skojarzenia. | Obecnie nie jest obsługiwane wdrożenie chmury Azure wiosennej w podsieci skojarzonej z istniejącymi tabelami tras, Usuń ich skojarzenie i spróbuj ponownie. |
| Wymagany ruch nie jest allowlisted. | Zapoznaj się z [odpowiedzialnością klienta w zakresie uruchamiania chmury wiosennej Azure w sieci wirtualnej](spring-cloud-vnet-customer-responsibilities.md) , aby upewnić się, że wymagany ruch jest allowlisted. |

## <a name="my-application-cant-be-registered"></a>Nie można zarejestrować mojej aplikacji

Ten problem występuje, gdy sieć wirtualna jest skonfigurowana przy użyciu niestandardowych ustawień DNS. W takim przypadku prywatna strefa DNS używana przez chmurę wiosenną platformy Azure jest nieskuteczna. Dodaj Azure DNS IP 168.63.129.16 jako nadrzędny Serwer DNS na niestandardowym serwerze DNS.

## <a name="other-issues"></a>Inne problemy

[Rozwiązywanie typowych problemów z chmurą wiosenną platformy Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-troubleshoot).