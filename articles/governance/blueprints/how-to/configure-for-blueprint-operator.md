---
title: Konfigurowanie środowiska dla operatora tworzenia planów
description: Dowiedz się, jak skonfigurować środowisko platformy Azure do używania z wbudowaną rolą kontroli dostępu opartej na rolach (RBAC).
ms.date: 05/06/2020
ms.topic: how-to
ms.openlocfilehash: a61997f5e3247e5b42d5e2f07c0fc38cc1b5114d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82864015"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>Konfigurowanie środowiska dla operatora strategii

Zarządzanie definicjami planów i przypisaniami planów można przypisać do różnych zespołów. Jest ona wspólna dla architekta lub zespołu zarządzającego odpowiedzialnego za Zarządzanie definicjami planów, gdy zespół operacyjny jest odpowiedzialny za zarządzanie przypisaniami tych definicji centralnie kontrolowanych strategii.

Wbudowana kontrola dostępu **oparta na rolach** (RBAC) jest zaprojektowana specjalnie do użycia w scenariuszu tego typu. Rola umożliwia zespołom typu operacje zarządzanie przypisaniem definicji planów organizacji, ale nie pozwala na ich modyfikowanie. Wymaga to pewnej konfiguracji w środowisku platformy Azure, a w tym artykule opisano niezbędne kroki.

## <a name="grant-permission-to-the-blueprint-operator"></a>Udziel uprawnienia do operatora strategii

Pierwszym krokiem jest przyznanie roli **operatora** planu do konta lub grupy zabezpieczeń (zalecane), które będą przypisywać plany. Ta akcja powinna zostać wykonana na najwyższym poziomie w hierarchii grupy zarządzania, która obejmuje wszystkie grupy zarządzania i subskrypcje, do których zespół operacji powinien mieć dostęp do przypisania planu. Zalecane jest przestrzeganie zasad najniższych uprawnień podczas udzielania tych uprawnień.

1. Rekomendowane [Tworzenie grupy zabezpieczeń i Dodawanie członków](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. [Dodawanie przypisania roli](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) do **operatora** strategii do konta lub grupy zabezpieczeń

## <a name="user-assign-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

Definicja planu może korzystać z tożsamości zarządzanych przypisanych do systemu lub przypisanej przez użytkownika. Jednak w przypadku korzystania z roli **operatora** planu definicja strategii musi być skonfigurowana do korzystania z tożsamości zarządzanej przypisanej przez użytkownika. Ponadto konto lub grupa zabezpieczeń, do której należy rola **operatora** planu, musi mieć przypisaną rolę **operatora tożsamości zarządzanej** na zarządzanej tożsamości przypisanej do użytkownika. Bez tego uprawnienia przypisanie strategii kończy się niepowodzeniem z powodu braku uprawnień.

1. [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) do użycia przez przypisany plan

1. [Dodaj przypisanie roli](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) **operatora tożsamości zarządzanej** do konta lub grupy zabezpieczeń. Określ zakres przypisania roli do nowej tożsamości zarządzanej przypisanej przez użytkownika.

1. Jako **operator**planu [Przypisz plan](../create-blueprint-portal.md#assign-a-blueprint) , który używa nowej tożsamości zarządzanej przypisanej przez użytkownika.

## <a name="next-steps"></a>Następne kroki

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).