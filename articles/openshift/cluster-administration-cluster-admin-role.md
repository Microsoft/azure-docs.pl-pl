---
title: Rola administratora klastrów Red Hat OpenShift na platformie Azure | Microsoft Docs
description: Przypisanie i użycie roli administratora klastra Red Hat OpenShift platformy Azure
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: f4fc57ebe39a2169ea91423b295f4bc436746b29
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636248"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Rola Administrator klienta Red Hat OpenShift na platformie Azure

> [!IMPORTANT]
> Usługa Azure Red Hat OpenShift 3,11 zostanie wycofana 30 czerwca 2022. Obsługa tworzenia nowych klastrów usługi Azure Red Hat OpenShift 3,11 jest kontynuowana do 30 listopada 2020. Po wycofaniu pozostałe klastry usługi Azure Red Hat OpenShift 3,11 zostaną zamknięte, aby zapobiec występowaniu luk w zabezpieczeniach.
> 
> Postępuj zgodnie z tym przewodnikiem, aby [utworzyć klaster usługi Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Jeśli masz określone pytania, [skontaktuj się z nami](mailto:arofeedback@microsoft.com).

Jesteś administratorem klastra usługi Azure Red Hat OpenShift. Twoje konto ma zwiększone uprawnienia i dostęp do wszystkich projektów utworzonych przez użytkownika.

Jeśli dla konta jest powiązana rola autoryzacji klient-administrator-klaster, może ona automatycznie zarządzać projektem.

> [!Note] 
> Rola klastra klient-administrator-klaster nie jest taka sama jak rola klastra-administratora klastra.

Na przykład można wykonać akcje skojarzone z zestawem czasowników ( `create` ) do działania na zestawie nazw zasobów ( `templates` ). Aby wyświetlić szczegóły tych ról i ich zestawów zleceń i zasobów, uruchom następujące polecenie:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Nazwy zleceń nie zawsze są mapowane bezpośrednio do `oc` poleceń. Są one bardziej ogólne dla typów operacji interfejsu wiersza polecenia, które można wykonywać. 

Na przykład, `list` Jeśli zlecenie oznacza, że można wyświetlić listę wszystkich obiektów o nazwie zasobu ( `oc get` ). `get`Zlecenie oznacza, że można wyświetlić szczegóły konkretnego obiektu, jeśli znasz jego nazwę ( `oc describe` ).

## <a name="configure-the-customer-administrator-role"></a>Konfigurowanie roli administratora klienta

Rolę klastra klient-administrator-klaster można skonfigurować tylko podczas tworzenia klastra, dostarczając flagę `--customer-admin-group-id` . To pole nie jest obecnie konfigurowalne w Azure Portal. Aby dowiedzieć się, jak skonfigurować Azure Active Directory i grupę administratorów, zobacz [Azure Active Directory Integration for Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Potwierdzenie członkostwa w roli administratora klienta

Aby potwierdzić członkostwo w grupie administratorów klienta, spróbuj użyć poleceń interfejsu wiersza polecenia OpenShift `oc get nodes` lub `oc projects` . `oc get nodes` program wyświetli listę węzłów, jeśli masz rolę klient-administrator-klaster i błąd uprawnień, jeśli masz tylko rolę klient-administrator-projekt. `oc projects` pokaże wszystkie projekty w klastrze, a nie tylko projekty, w których pracujesz.

Aby dowiedzieć się więcej o rolach i uprawnieniach w klastrze, można użyć [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) polecenia.

## <a name="next-steps"></a>Następne kroki

Skonfiguruj rolę klastra klient-administrator-klaster:
> [!div class="nextstepaction"]
> [Azure Active Directory integrację z usługą Azure Red Hat OpenShift](howto-aad-app-configuration.md)
