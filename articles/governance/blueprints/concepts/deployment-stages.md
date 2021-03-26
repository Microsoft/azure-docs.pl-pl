---
title: Etapy wdrażania strategii
description: Zapoznaj się z zabezpieczeniami i krokami związanymi z artefaktem, które są wykonywane przez usługi platformy Azure podczas tworzenia przypisania planu.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 473f8c7957994401ea6000ecc0d8023a89f8c349
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560153"
---
# <a name="stages-of-a-blueprint-deployment"></a>Etapy wdrażania strategii

Gdy plan zostanie wdrożony, do wdrożenia zasobów zdefiniowanych w planie jest wykonywana seria akcji podejmowana przez usługę plany platformy Azure. Ten artykuł zawiera szczegółowe informacje o tym, co obejmuje każdy krok.

Wdrożenie planu jest wyzwalane przez przypisanie planu do subskrypcji lub [zaktualizowanie istniejącego przypisania](../how-to/update-existing-assignments.md). Podczas wdrażania plany platformy Azure podejmują następujące czynności wysokiego poziomu:

> [!div class="checklist"]
> - Plany platformy Azure z prawami właściciela
> - Tworzony jest obiekt przypisania strategii
> - Opcjonalne — plany platformy Azure tworzą tożsamość zarządzaną **przypisaną przez system**
> - Tożsamość zarządzana wdraża artefakty strategii
> - Odwołania do usługi planów platformy Azure i **przypisane do systemu** prawa tożsamości zarządzane są odwoływane

## <a name="azure-blueprints-granted-owner-rights"></a>Plany platformy Azure z prawami właściciela

Nazwa główna usługi planów platformy Azure ma uprawnienia właściciela przypisanej subskrypcji lub subskrypcji, gdy zostanie użyta tożsamość zarządzana [tożsamości zarządzanej przez system](../../../active-directory/managed-identities-azure-resources/overview.md) . Przyznana rola pozwala planom platformy Azure na tworzenie, a później odwoływanie tożsamości zarządzanej **przypisanej do systemu** . Jeśli jest używana tożsamość zarządzana **przypisana przez użytkownika** , jednostka usługi Azure Planes nie jest pobierana i nie wymaga praw właściciela subskrypcji.

Prawa są przyznawane automatycznie, jeśli przypisanie odbywa się za pomocą portalu. Jednak jeśli przypisanie odbywa się za pomocą interfejsu API REST, przyznanie praw musi zostać wykonane przy użyciu oddzielnego wywołania interfejsu API. Identyfikator AppId platformy Azure planuje się `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` , ale jednostka usługi jest zależna od dzierżawy. Aby uzyskać nazwę główną usługi, użyj [Azure Active Directory interfejs API programu Graph](/graph/migrate-azure-ad-graph-planning-checklist) i [serviceprincipals](/graph/api/resources/serviceprincipal) punktu końcowego. Następnie należy przydzielić roli _właściciela_ na platformę Azure za pomocą [portalu](../../../role-based-access-control/role-assignments-portal.md), [interfejsu](../../../role-based-access-control/role-assignments-rest.md) [wiersza polecenia platformy Azure](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md)lub [szablonu Azure Resource Manager](../../../role-based-access-control/role-assignments-template.md).

Usługa Azure Plans nie wdraża bezpośrednio zasobów.

## <a name="the-blueprint-assignment-object-is-created"></a>Tworzony jest obiekt przypisania strategii

Użytkownik, Grupa lub jednostka usługi przypisuje plan do subskrypcji. Obiekt przypisania istnieje na poziomie subskrypcji, do którego przypisano plan. Zasoby utworzone przez wdrożenie nie są wykonywane w kontekście wdrożenia jednostki.

Podczas tworzenia przypisania planu jest wybierany typ [tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md) . Wartością domyślną jest tożsamość zarządzana **przypisana przez system** . Można wybrać tożsamość zarządzaną **przypisaną przez użytkownika** . W przypadku korzystania z tożsamości zarządzanej **przypisanej przez użytkownika** przed utworzeniem przypisania strategii należy zdefiniować i przyznać uprawnienia. Role wbudowanego operatora [i](../../../role-based-access-control/built-in-roles.md#owner) [operator](../../../role-based-access-control/built-in-roles.md#blueprint-operator) planu mają odpowiednie `blueprintAssignment/write` uprawnienia do tworzenia przypisania, które korzysta z tożsamości zarządzanej **przypisanej przez użytkownika** .

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>Opcjonalne — plany platformy Azure tworzą tożsamość zarządzaną przypisaną przez system

Gdy podczas przypisywania zostanie wybrana [tożsamość zarządzana przypisana przez system](../../../active-directory/managed-identities-azure-resources/overview.md) , usługa Azure planuje tożsamość i przyznaje zarządzanej tożsamości rolę [właściciela](../../../role-based-access-control/built-in-roles.md#owner) . Jeśli [istniejące przypisanie zostanie uaktualnione](../how-to/update-existing-assignments.md), plany platformy Azure korzystają z wcześniej utworzonej tożsamości zarządzanej.

Zarządzana tożsamość związana z przypisaniem strategii służy do wdrażania lub ponownego wdrażania zasobów zdefiniowanych w planie. Ten projekt pozwala uniknąć nieumyślnego zakłócania przypisań.
Ten projekt obsługuje również funkcję [blokowania zasobów](./resource-locking.md) , kontrolując zabezpieczenia poszczególnych wdrożonych zasobów z planu.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>Tożsamość zarządzana wdraża artefakty strategii

Tożsamość zarządzana następnie wyzwala Menedżer zasobów wdrożenia artefaktów w ramach planu w zdefiniowanej [kolejności sekwencjonowania](./sequencing-order.md). Kolejność można dostosować, aby upewnić się, że artefakty zależne od innych artefaktów są wdrożone w odpowiedniej kolejności.

Niepowodzenie dostępu przez wdrożenie jest często wynikiem poziomu dostępu udzielonego tożsamości zarządzanej. Usługa planów platformy Azure zarządza cyklem życia zabezpieczeń zarządzanej tożsamości **przypisanej do systemu** . Jednak użytkownik jest odpowiedzialny za zarządzanie prawami i cyklem życia tożsamości zarządzanej **przypisanej przez użytkownika** .

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Uprawnienia do usługi strategii i zarządzanej tożsamości przypisane do systemu są odwoływane

Po zakończeniu wdrożeń usługa Azure planuje odwołuje prawa zarządzanej tożsamości **przypisanej do systemu** z subskrypcji. Następnie usługa plany platformy Azure odwołuje swoje prawa z subskrypcji. Usuwanie praw zapobiega stałym właścicielom subskrypcji przez plany platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](./parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](./sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](./resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).