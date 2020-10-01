---
title: Co to jest kontrola dostępu oparta na rolach na platformie Azure (Azure RBAC)?
description: Zapoznaj się z omówieniem kontroli dostępu opartej na rolach (Azure RBAC). Sterowanie dostępem do zasobów platformy Azure za pomocą przypisań ról.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: overview
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.custom: contperfq1, azuread-video-2020
ms.openlocfilehash: b61da9710b51ad4802b46cae7625d6ba9a66e86c
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91595779"
---
# <a name="what-is-azure-role-based-access-control-azure-rbac"></a>Co to jest kontrola dostępu oparta na rolach na platformie Azure (Azure RBAC)?

Zarządzanie dostępem do zasobów w chmurze to kluczowa funkcja dla każdej organizacji korzystającej z chmury. Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie, kto ma dostęp do zasobów platformy Azure, co można zrobić z tymi zasobami i jakie obszary mają do nich dostęp.

Azure RBAC to system autoryzacji oparty na [Azure Resource Manager](../azure-resource-manager/management/overview.md) , który zapewnia precyzyjne zarządzanie dostępem do zasobów platformy Azure.

Ten film wideo zawiera krótkie omówienie RBAC platformy Azure.

>[!VIDEO https://www.youtube.com/embed/Dzhm-garKBM]

## <a name="what-can-i-do-with-azure-rbac"></a>Co mogę zrobić przy użyciu usługi Azure RBAC?

Poniżej przedstawiono kilka przykładów tego, co można zrobić za pomocą usługi Azure RBAC:

- Zezwalanie jednemu użytkownikowi na zarządzanie maszynami wirtualnymi w ramach subskrypcji, a innemu na zarządzanie sieciami wirtualnymi
- Zezwalanie grupie administratorów baz danych na zarządzanie bazami danych SQL w ramach subskrypcji.
- Zezwalanie użytkownikowi na zarządzanie wszystkimi zasobami w grupie zasobów, w tym maszynami wirtualnymi, witrynami internetowymi i podsieciami
- Zezwalanie aplikacji na dostęp do wszystkich zasobów w grupie zasobów

## <a name="how-azure-rbac-works"></a>Jak działa usługa Azure RBAC

Sposób kontrolowania dostępu do zasobów przy użyciu funkcji RBAC platformy Azure polega na tworzeniu przypisań ról. Jest to kluczowe koncepcje, które należy zrozumieć — jak są wymuszane uprawnienia. Przypisanie roli składa się z trzech elementów: podmiotu zabezpieczeń, definicji roli i zakresu.

### <a name="security-principal"></a>Podmiot zabezpieczeń

*Podmiot zabezpieczeń* to obiekt reprezentujący użytkownika, grupę, jednostkę usługi lub tożsamość zarządzaną, które żądają dostępu do zasobów na platformie Azure. Rolę można przypisać do dowolnego z tych podmiotów zabezpieczeń.

![Podmiot zabezpieczeń w przypisaniu roli](./media/shared/rbac-security-principal.png)

### <a name="role-definition"></a>Definicja roli

*Definicja roli* to kolekcja uprawnień. Zwykle jest nazywana *rolą*. Definicja roli określa dozwolone operacje, na przykład odczyt, zapis, czy usuwanie. Role mogą być ogólne, na przykład „właściciel”, lub szczegółowe, na przykład „czytelnik maszyny wirtualnej”.

![Definicja roli w przypisaniu roli](./media/shared/rbac-role-definition.png)

Platforma Azure zawiera kilka [wbudowanych ról](built-in-roles.md) , których można użyć. Na przykład rola [współautora maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) umożliwia użytkownikowi tworzenie maszyn wirtualnych i zarządzanie nimi. Jeśli wbudowane role nie są zgodne z konkretnymi potrzebami organizacji, możesz utworzyć własne [role niestandardowe platformy Azure](custom-roles.md).

Ten film wideo zawiera krótkie omówienie wbudowanych ról i ról niestandardowych.

>[!VIDEO https://www.youtube.com/embed/I1mefHptRgo]

Platforma Azure zawiera operacje na danych, które umożliwiają udzielenie dostępu do danych w ramach obiektu. Jeśli na przykład użytkownik ma uprawnienie do odczytu na koncie magazynu, może odczytywać obiekty blob lub komunikaty na tym koncie magazynu.

Aby uzyskać więcej informacji, zobacz informacje na temat [definicji ról platformy Azure](role-definitions.md).

### <a name="scope"></a>Zakres

*Zakres* to zestaw zasobów, w ramach którego jest przydzielany dostęp. Podczas przypisywania roli możesz dodatkowo ograniczyć dozwolone czynności, określając zakres. Jest to przydatne, jeśli chcesz, aby ktoś został [współautorem witryny sieci Web](built-in-roles.md#website-contributor), ale tylko dla jednej grupy zasobów.

Na platformie Azure można określić zakres na czterech poziomach: [grupy zarządzania](../governance/management-groups/overview.md), subskrypcji, [grupy zasobów](../azure-resource-manager/management/overview.md#resource-groups)lub zasobu. Zakresy mają strukturę opartą na relacji nadrzędny-podrzędny. Role można przypisywać na dowolnym z tych poziomów zakresu.

![Zakres w przypisaniu roli](./media/shared/rbac-scope.png)

Aby uzyskać więcej informacji na temat zakresu, zobacz [Opis zakresu](scope-overview.md).

### <a name="role-assignments"></a>Przypisania ról

*Przypisywanie roli* to proces dołączania definicji roli do użytkownika, grupy, jednostki usługi lub tożsamości zarządzanej w wybranym zakresie w celu udzielenia dostępu. Udzielenie dostępu polega na utworzeniu przypisania roli, a odwołanie dostępu — na usunięciu przypisania roli.

Na poniższym diagramie przedstawiono przykład przypisania roli. W tym przykładzie do grupy Marketing przypisano rolę [współautora](built-in-roles.md#contributor) w grupie zasobów pharma-sales. To oznacza, że użytkownicy należący do grupy Marketing mogą tworzyć dowolne zasoby platformy Azure w grupie zasobów pharma-sales i zarządzać nimi. Użytkownicy należący do grupy Marketing nie mają dostępu do zasobów spoza grupy zasobów pharma-sales, o ile nie przypisano do nich innej roli.

![Przypisanie roli w celu kontroli dostępu](./media/overview/rbac-overview.png)

Przypisania ról można tworzyć, korzystając z witryny Azure Portal, interfejsu wiersza polecenia platformy Azure, programu Azure PowerShell, zestawów Azure SDK lub interfejsów API REST.

Aby uzyskać więcej informacji, zobacz [kroki umożliwiające dodanie przypisania roli](role-assignments-steps.md).

## <a name="multiple-role-assignments"></a>Przypisania wielu ról

Co więc się dzieje w przypadku wielu nakładających się przypisań ról? Azure RBAC jest modelem dodatku, więc Twoje czynne uprawnienia są sumą przypisań ról. Rozważmy poniższy przykład, w którym użytkownikowi przypisano rolę Współautor w zakresie subskrypcji i rolę Czytelnik w grupie zasobów. Suma uprawnień współautora i uprawnień czytelnika jest efektywnie rolą współautor dla grupy zasobów. Dlatego w tym przypadku przypisanie roli Czytelnik nie ma żadnego znaczenia.

![Przypisania wielu ról](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>Przypisania odmowy

Wcześniej kontrola RBAC systemu Azure była modelem tylko dozwolonym bez Odmów, ale teraz usługa Azure RBAC obsługuje przydziały Odmów w ograniczonym stopniu. Podobnie jak przypisanie roli *przypisanie odmowy* dołącza zestaw akcji odmowy do użytkownika, grupy, jednostki usługi lub tożsamości zarządzanej w określonym zakresie w celu odmowy dostępu. Przypisanie roli definiuje zestaw akcji, które są *dozwolone*, podczas gdy przypisanie odmowy definiuje zestaw akcji, które są *niedozwolone*. Innymi słowy przypisanie odmowy uniemożliwia użytkownikom wykonywanie określonych akcji, nawet jeśli przypisanie roli daje im taki dostęp. Przypisanie odmowy ma pierwszeństwo przed przypisaniem roli.

Aby uzyskać więcej informacji, zobacz [Omówienie przypisań Odmów na platformie Azure](deny-assignments.md).

## <a name="how-azure-rbac-determines-if-a-user-has-access-to-a-resource"></a>Jak usługa Azure RBAC decyduje o tym, czy użytkownik ma dostęp do zasobu

Poniżej przedstawiono ogólne kroki używane przez usługę Azure RBAC do określenia, czy masz dostęp do zasobu na płaszczyźnie zarządzania. Warto je znać, jeśli próbujesz rozwiązać problem z dostępem.

1. Użytkownik (lub jednostka usługi) uzyskuje token dla usługi Azure Resource Manager.

    Ten token zawiera informację o członkostwie użytkownika w grupach (w tym przechodnim członkostwie w grupach).

1. Użytkownik tworzy wywołanie interfejsu API REST do usługi Azure Resource Manager, dołączając token.

1. Usługa Azure Resource Manager pobiera wszystkie przypisania roli lub odmowy, które dotyczą zasobu, wobec którego podejmowana jest akcja.

1. Usługa Azure Resource Manager zawęża przypisania roli, które dotyczą tego użytkownika lub jego grupy, i określa, jakie role posiada użytkownik względem tego zasobu.

1. Usługa Azure Resource Manager określa, czy akcja w wywołaniu interfejsu API należy do ról, jakie użytkownik posiada względem tego zasobu.

1. Jeśli użytkownik nie ma roli z akcją w żądanym zakresie, dostęp nie zostanie udzielony. W przeciwnym razie usługa Azure Resource Manager sprawdza, czy ma w tej sytuacji zastosowanie przypisanie odmowy.

1. Jeśli tak, dostęp jest blokowany. Jeśli nie, dostęp jest udzielany.

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](role-assignments-portal.md)
- [Omówienie różnych ról](rbac-and-directory-admin-roles.md)
- [Struktura wdrażania chmury: zarządzanie dostępem do zasobów na platformie Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
