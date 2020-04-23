---
title: Omówienie
description: Opis wdrażania zasobów na platformie Azure, kontrolowania dostępu do tych zasobów oraz zarządzania nimi za pomocą usługi Azure Resource Manager.
ms.topic: overview
ms.date: 04/21/2020
ms.openlocfilehash: 253fc2f296fa764a6c22fa1331221df60ca21bb5
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870488"
---
# <a name="what-is-azure-resource-manager"></a>Co to jest Usługa Azure Resource Manager?

Usługa Azure Resource Manager to usługa wdrażania i zarządzania dla platformy Azure. Zawiera warstwę zarządzania, która umożliwia tworzenie, aktualizowanie i usuwanie zasobów na koncie platformy Azure. Za pomocą funkcji zarządzania, takich jak kontrola dostępu, blokady i tagi, można zabezpieczyć i zorganizować zasoby po wdrożeniu.

Aby dowiedzieć się więcej o szablonach usługi Azure Resource Manager, zobacz [Omówienie wdrażania szablonów](../templates/overview.md).

## <a name="consistent-management-layer"></a>Spójna warstwa zarządzania

Gdy użytkownik wysyła żądanie z dowolnego narzędzia platformy Azure, interfejsy API lub zestawów SDK, Menedżer zasobów odbiera żądanie. Uwierzytelnia i autoryzuje żądanie. Menedżer zasobów wysyła żądanie do usługi Azure, która wykonuje żądaną akcję. Ponieważ wszystkie żądania są obsługiwane przez ten sam interfejs API, użytkownik widzi spójne wyniki i możliwości w różnych narzędziach.

Na poniższej ilustracji przedstawiono rolę, jaką usługa Azure Resource Manager odgrywa w obsłudze żądań platformy Azure.

![Model żądań usługi Resource Manager](./media/overview/consistent-management-layer.png)

Wszystkie funkcje, które są dostępne w portalu są również dostępne za pośrednictwem programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsów API REST oraz zestawów SDK klienta. Funkcje udostępnione najpierw za pośrednictwem interfejsów API zostaną wprowadzone w witrynie Portal w ciągu 180 dni od początkowego wydania.

## <a name="terminology"></a>Terminologia

Jeśli dopiero zaczynasz korzystać z usługi Azure Resource Manager, oto kilka terminów, których możesz nie znać.

* **Zasób** — dostępny za pośrednictwem platformy Azure element, którym można zarządzać. Przykładami zasobów są maszyny wirtualne, konta magazynu, aplikacje internetowe, bazy danych i sieci wirtualne. Grupy zasobów, subskrypcje, grupy zarządzania i tagi są również przykładami zasobów.
* **Grupa zasobów** — kontener, który zawiera powiązane zasoby rozwiązania dla platformy Azure. Grupa zasobów zawiera zasoby, którymi chcesz zarządzać jako grupą. Należy zdecydować, które zasoby należą do grupy zasobów, w oparciu o to, co jest najrozsądniejsze dla Twojej organizacji. Zobacz [Grupy zasobów](#resource-groups).
* **Dostawca zasobów** — usługa dostarczająca zasoby platformy Azure. Na przykład typowym dostawcą zasobów jest Microsoft.Compute, który dostarcza zasób maszyny wirtualnej. Innym typowym dostawcą zasobów jest Microsoft.Storage. Zobacz [Dostawców zasobów i typy](resource-providers-and-types.md).
* **Szablon Menedżera zasobów** — plik JSON (JavaScript Object Notation), który definiuje jeden lub więcej zasobów do wdrożenia w grupie zasobów, subskrypcji, grupie zarządzania lub dzierżawie. Szablon może służyć do spójnego i wielokrotnego wdrażania zasobów. Zobacz [omówienie wdrażania szablonu](../templates/overview.md).
* **Składnia deklaratywna** — składnia pozwalająca określić, co zamierzasz utworzyć, bez konieczności pisania w tym celu sekwencji poleceń programistycznych. Przykładem składni deklaratywnej jest szablon usługi Resource Manager. W tym pliku definiuje się właściwości infrastruktury do wdrożenia na platformie Azure.  Zobacz [omówienie wdrażania szablonu](../templates/overview.md).

## <a name="the-benefits-of-using-resource-manager"></a>Zalety korzystania z usługi Resource Manager

Za pomocą Menedżera zasobów można:

* Zarządzanie infrastrukturą za pomocą szablonów deklaratywnych, a nie skryptów.

* Wdrażaj, zarządzaj i monitoruj wszystkie zasoby rozwiązania jako grupę, zamiast obsługiwać te zasoby indywidualnie.

* Ponownie wdrożyć rozwiązanie w całym cyklu życia rozwoju i mieć pewność, że zasoby są wdrażane w spójnym stanie.

* Zdefiniuj zależności między zasobami, aby były wdrażane w odpowiedniej kolejności.

* Zastosuj kontrolę dostępu do wszystkich usług, ponieważ kontrola dostępu oparta na rolach (RBAC) jest natywnie zintegrowana z platformą zarządzania.

* Zastosuj tagi do zasobów, aby logicznie zorganizować wszystkie zasoby w ramach subskrypcji.

* Wyjaśnij rozliczenia organizacji, wyświetlając koszty dla grupy zasobów udostępniających ten sam tag.

## <a name="understand-scope"></a>Objaśnienie zakresu

Platforma Azure udostępnia cztery poziomy zakresu: [grupy zarządzania,](../../governance/management-groups/overview.md)subskrypcje, [grupy zasobów](#resource-groups)i zasoby. Na poniższej ilustracji przedstawiono takie przykładowe warstwy.

![Poziomy zarządzania](./media/overview/scope-levels.png)

Ustawienia zarządzania są stosowane na dowolnych z tych poziomów zakresu. Zasięg zastosowania ustawienia jest określany na podstawie wybranego poziomu. Niższe poziomy dziedziczą ustawienia z wyższych poziomów. Na przykład po zastosowaniu [zasad](../../governance/policy/overview.md) do subskrypcji, zasady są stosowane do wszystkich grup zasobów i zasobów w ramach subskrypcji. Po zastosowaniu zasad do grupy zasobów ta zasada jest stosowana w grupie zasobów i wszystkich jej zasobach. Jednak inna grupa zasobów nie ma tego przypisania zasad.

Szablony można wdrażać dla dzierżaw, grup zarządzania, subskrypcji lub grup zasobów.

## <a name="resource-groups"></a>Grupy zasobów

Definiując grupę zasobów, należy wziąć pod uwagę pewne ważne czynniki:

* Wszystkie zasoby w grupie powinny mieć ten sam cykl życia. Są one wdrażane, aktualizowane i usuwane razem. Jeśli jakiś zasób, na przykład serwer bazy danych, ma mieć inny cykl wdrażania, powinien zostać umieszczony w innej grupie zasobów.

* Każdy zasób może znajdować się tylko w jednej grupie zasobów.

* Niektóre zasoby mogą istnieć poza grupą zasobów. Te zasoby są wdrażane w [ramach subskrypcji,](../templates/deploy-to-subscription.md) [grupy zarządzania](../templates/deploy-to-management-group.md)lub [dzierżawy.](../templates/deploy-to-tenant.md) Tylko określone typy zasobów są obsługiwane w tych zakresach.

* Zasoby w grupie można dodawać i usuwać w dowolnym momencie.

* Zasoby można przenosić między poszczególnymi grupami. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](move-resource-group-and-subscription.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

* Grupa zasobów może zawierać zasoby, które znajdują się w różnych regionach.

* Grupa zasobów może służyć do określania zakresu kontroli dostępu na potrzeby działań administracyjnych.

* Zasób może wchodzić w interakcję z zasobami znajdującymi się w innych grupach zasobów. Ta interakcja jest typowa, gdy dwa zasoby są ze sobą powiązane, ale nie mają tego samego cyklu życia (na przykład aplikacje internetowe łączące się z bazą danych).

Podczas tworzenia grupy zasobów, należy podać lokalizację dla danej grupy zasobów. Być może zastanawiasz się, „Dlaczego grupa zasobów wymaga określenia lokalizacji? Ponadto dlaczego lokalizacja grupy zasobów jest w ogóle istotna, skoro zasoby mogą znajdować się w innej lokalizacji niż grupa zasobów?” Grupa zasobów przechowuje metadane dotyczące zasobów. Po określeniu lokalizacji dla grupy zasobów, określasz, gdzie te metadane są przechowywane. Dla zachowania zgodności może być konieczne upewnienie się, że dane są przechowywane w odpowiednim regionie.

Jeśli region grupy zasobów jest tymczasowo niedostępny, nie można zaktualizować zasobów w grupie zasobów, ponieważ metadane są niedostępne. Zasoby w innych regionach będą nadal działać zgodnie z oczekiwaniami, ale nie można ich zaktualizować. Aby uzyskać więcej informacji na temat tworzenia niezawodnych aplikacji, zobacz [Projektowanie niezawodnych aplikacji platformy Azure.](/azure/architecture/checklist/resiliency-per-service)

## <a name="resiliency-of-azure-resource-manager"></a>Odporność usługi Azure Resource Manager

Usługa Azure Resource Manager została zaprojektowana z myślą o odporności i ciągłej dostępności. Operacje Menedżera zasobów i płaszczyzny sterowania (żądania wysyłane do management.azure.com) w interfejsie API REST są następujące:

* Rozmieszczony w różnych regionach. Niektóre usługi mają regionalny.

* Rozmieszczony w strefach dostępności (a także regionach) w lokalizacjach, w których znajduje się wiele stref dostępności.

* Nie zależy od jednego logicznego centrum danych.

* Nigdy nie zdjęty do czynności konserwacyjnych.

Ta odporność ma zastosowanie do usług, które odbierają żądania za pośrednictwem Menedżera zasobów. Na przykład Usługa Key Vault korzysta z tej odporności.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o przenoszeniu zasobów, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](move-resource-group-and-subscription.md).

* Aby dowiedzieć się więcej o oznaczaniu zasobów, zobacz [Organizowanie zasobów platformy Azure za pomocą tagów.](tag-resources.md)

* Aby dowiedzieć się więcej o blokowaniu zasobów, zobacz [Blokowanie zasobów, aby zapobiec nieoczekiwanym zmianom](lock-resources.md).
