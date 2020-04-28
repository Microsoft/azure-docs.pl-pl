---
title: Informacje na temat kontroli dostępu opartej na rolach — Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Informacje na temat kontroli dostępu opartej na rolach i zarządzania uprawnieniami w usłudze Azure Digital bliźniaczych reprezentacji.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: feda4b3a7f21b581fb4f08aec013f87c0fabb7e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76044940"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Kontrola dostępu oparta na rolach w usłudze Azure Digital bliźniaczych reprezentacji

Usługa Azure Digital bliźniaczych reprezentacji umożliwia precyzyjne sterowanie dostępem do określonych danych, zasobów i akcji w grafie przestrzennym. Odbywa się to za pośrednictwem szczegółowego zarządzania rolami i uprawnieniami o nazwie [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC). RBAC składa się z _ról_ i _przypisań ról_. Role identyfikują poziom uprawnień. Przypisania ról kojarzą rolę z użytkownikiem lub urządzeniem.

Przy użyciu RBAC można udzielić uprawnienia do:

- Użytkownik.
- Urządzenie.
- Nazwa główna usługi.
- Funkcja zdefiniowana przez użytkownika.
- Wszyscy użytkownicy, którzy należą do domeny.
- Dzierżawa.

Stopień dostępu może być również dostosowany.

RBAC jest unikatowy w tym, że uprawnienia są dziedziczone jako wykres przestrzenny.

## <a name="what-can-i-do-with-rbac"></a>Co można zrobić za pomocą kontroli dostępu opartej na rolach?

Deweloper może używać RBAC, aby:

- Przyznaj użytkownikowi możliwość zarządzania urządzeniami w całym budynku lub tylko dla określonego pokoju lub podłogi.
- Przyznaj administratorowi globalny dostęp do wszystkich węzłów wykresu przestrzennego dla całego wykresu lub tylko dla sekcji grafu.
- Przyznaj specjalistom pomocy technicznej dostęp do odczytu do grafu, z wyjątkiem kluczy dostępu.
- Przyznaj wszystkim członkom domeny dostęp do odczytu do wszystkich obiektów grafu.

## <a name="rbac-best-practices"></a>Najlepsze rozwiązania RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Role

### <a name="role-definitions"></a>Definicje ról

Definicja roli jest kolekcją uprawnień i innych atrybutów, które stanowią rolę. Definicja roli zawiera listę dozwolonych operacji, takich jak *Tworzenie*, *odczytywanie*, *Aktualizowanie*i *usuwanie* dowolnego obiektu z tą rolą. Określa również, do których typów obiektów stosowane są uprawnienia.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Aby pobrać pełne definicje dla poprzednich ról, wykonaj zapytanie do interfejsu API System/role.
> Dowiedz się więcej, odczytując [Tworzenie przypisań ról i zarządzanie nimi](./security-create-manage-role-assignments.md#retrieve-all-roles).

### <a name="object-identifier-types"></a>Typy identyfikatorów obiektów

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Dowiedz się, jak przyznać uprawnienia do nazwy głównej usługi, odczytując [Tworzenie przypisań ról i zarządzanie nimi](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal).

W poniższych artykułach dokumentacji referencyjnej opisano:

- Jak [wykonać zapytanie lub identyfikator obiektu dla użytkownika](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Jak [uzyskać identyfikator obiektu dla jednostki usługi](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- Jak [pobrać identyfikator obiektu dla dzierżawy usługi Azure AD](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Przypisania ról

Przypisanie roli bliźniaczych reprezentacji Digital Azure kojarzy obiekt, taki jak użytkownik lub dzierżawa usługi Azure AD, z rolą i spacją. Uprawnienia są przyznawane wszystkim obiektom należącym do tego miejsca. Przestrzeń zawiera cały wykres przestrzenny poniżej.

Na przykład użytkownik otrzymuje przypisanie roli z rolą `DeviceInstaller` dla głównego węzła wykresu przestrzennego, który reprezentuje Kompilowanie. Użytkownik może następnie odczytywać i aktualizować urządzenia dla tego węzła oraz wszystkie inne miejsca podrzędne w budynku.

Aby udzielić uprawnień do adresata, Utwórz przypisanie roli. Aby odwołać uprawnienia, usuń przypisanie roli.

>[!IMPORTANT]
> Dowiedz się więcej o przypisaniach ról, odczytując informacje o [tworzeniu przypisań ról i zarządzaniu nimi](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat tworzenia przypisań ról bliźniaczych reprezentacji cyfrowych platformy Azure i zarządzania nimi, zobacz [Tworzenie przypisań ról i zarządzanie nimi](./security-create-manage-role-assignments.md).

- Przeczytaj więcej [na temat RBAC na platformie Azure](https://docs.microsoft.com/azure/role-based-access-control/).
