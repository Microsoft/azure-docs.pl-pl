---
title: Omówienie
description: Opis wdrażania zasobów na platformie Azure, kontrolowania dostępu do tych zasobów oraz zarządzania nimi za pomocą usługi Azure Resource Manager.
ms.topic: overview
ms.date: 04/21/2020
ms.openlocfilehash: 253fc2f296fa764a6c22fa1331221df60ca21bb5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81870488"
---
# <a name="what-is-azure-resource-manager"></a>Co to jest Azure Resource Manager?

Usługa Azure Resource Manager to usługa wdrażania i zarządzania dla platformy Azure. Zapewnia ona warstwę zarządzania, która umożliwia tworzenie, aktualizowanie i usuwanie zasobów na koncie platformy Azure. Za pomocą funkcji zarządzania, takich jak kontrola dostępu, blokady i Tagi, można zabezpieczyć i zorganizować zasoby po wdrożeniu.

Aby dowiedzieć się więcej o szablonach Azure Resource Manager, zobacz [Template Deployment Omówienie](../templates/overview.md).

## <a name="consistent-management-layer"></a>Spójna warstwa zarządzania

Gdy użytkownik wysyła żądanie z dowolnego narzędzia, interfejsów API lub zestawów SDK platformy Azure, Menedżer zasobów odbiera żądanie. Uwierzytelnia i autoryzuje żądanie. Menedżer zasobów wysyła żądanie do usługi platformy Azure, która przyjmuje żądaną akcję. Ponieważ wszystkie żądania są obsługiwane przez ten sam interfejs API, użytkownik widzi spójne wyniki i możliwości w różnych narzędziach.

Na poniższej ilustracji przedstawiono rolę Azure Resource Manager odgrywaną w obsłudze żądań platformy Azure.

![Model żądań usługi Resource Manager](./media/overview/consistent-management-layer.png)

Wszystkie funkcje, które są dostępne w portalu są również dostępne za pośrednictwem programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsów API REST oraz zestawów SDK klienta. Funkcje udostępnione najpierw za pośrednictwem interfejsów API zostaną wprowadzone w witrynie Portal w ciągu 180 dni od początkowego wydania.

## <a name="terminology"></a>Terminologia

Jeśli dopiero zaczynasz korzystać z usługi Azure Resource Manager, oto kilka terminów, których możesz nie znać.

* **Zasób** — dostępny za pośrednictwem platformy Azure element, którym można zarządzać. Przykładami zasobów są maszyny wirtualne, konta magazynu, aplikacje internetowe, bazy danych i sieci wirtualne. Grupy zasobów, subskrypcje, grupy zarządzania i Tagi są również przykładami zasobów.
* **Grupa zasobów** — kontener, który zawiera powiązane zasoby rozwiązania dla platformy Azure. Grupa zasobów zawiera zasoby, którymi chcesz zarządzać jako grupą. Należy zdecydować, które zasoby należą do grupy zasobów, w oparciu o to, co jest najrozsądniejsze dla Twojej organizacji. Zobacz [Grupy zasobów](#resource-groups).
* **Dostawca zasobów** — usługa dostarczająca zasoby platformy Azure. Na przykład typowym dostawcą zasobów jest Microsoft.Compute, który dostarcza zasób maszyny wirtualnej. Innym typowym dostawcą zasobów jest Microsoft.Storage. Zobacz [dostawcy zasobów i ich typy](resource-providers-and-types.md).
* **Szablon Menedżer zasobów** — plik JavaScript Object Notation (JSON), który definiuje co najmniej jeden zasób do wdrożenia w grupie zasobów, subskrypcji, grupie zarządzania lub dzierżawie. Szablon może służyć do spójnego i wielokrotnego wdrażania zasobów. Zobacz [Template Deployment przegląd](../templates/overview.md).
* **Składnia deklaratywna** — składnia pozwalająca określić, co zamierzasz utworzyć, bez konieczności pisania w tym celu sekwencji poleceń programistycznych. Przykładem składni deklaratywnej jest szablon usługi Resource Manager. W tym pliku definiuje się właściwości infrastruktury do wdrożenia na platformie Azure.  Zobacz [Template Deployment przegląd](../templates/overview.md).

## <a name="the-benefits-of-using-resource-manager"></a>Zalety korzystania z usługi Resource Manager

Za pomocą Menedżer zasobów można:

* Zarządzaj infrastrukturą przy użyciu szablonów deklaratywnych zamiast skryptów.

* Wdrażaj i Monitoruj wszystkie zasoby dla rozwiązania jako grupę, a także zarządzaj nimi, a nie Przetwarzaj tych zasobów pojedynczo.

* Wdróż ponownie rozwiązanie w całym cyklu rozwoju i Zachowaj pewność, że zasoby są wdrażane w spójnym stanie.

* Zdefiniuj zależności między zasobami, aby zostały wdrożone w odpowiedniej kolejności.

* Zastosuj kontrolę dostępu do wszystkich usług, ponieważ Access Control oparte na rolach (RBAC) są natywnie zintegrowane z platformą zarządzania.

* Zastosuj Tagi do zasobów, aby logicznie organizować wszystkie zasoby w subskrypcji.

* Wyjaśnij rozliczenia w organizacji, wyświetlając koszty dla grupy zasobów, które współużytkują ten sam tag.

## <a name="understand-scope"></a>Objaśnienie zakresu

Platforma Azure udostępnia cztery poziomy zakresu: [grupy zarządzania](../../governance/management-groups/overview.md), subskrypcje, [grupy zasobów](#resource-groups)i zasoby. Na poniższej ilustracji przedstawiono takie przykładowe warstwy.

![Poziomy zarządzania](./media/overview/scope-levels.png)

Ustawienia zarządzania są stosowane na dowolnych z tych poziomów zakresu. Zasięg zastosowania ustawienia jest określany na podstawie wybranego poziomu. Niższe poziomy dziedziczą ustawienia z wyższych poziomów. Na przykład w przypadku zastosowania [zasad](../../governance/policy/overview.md) do subskrypcji zasady są stosowane do wszystkich grup zasobów i zasobów w ramach subskrypcji. Po zastosowaniu zasad w grupie zasobów te zasady są stosowane do grupy zasobów i wszystkich jej zasobów. Jednak inna grupa zasobów nie ma tego przypisania zasad.

Szablony można wdrażać dla dzierżawców, grup zarządzania, subskrypcji lub grup zasobów.

## <a name="resource-groups"></a>Grupy zasobów

Definiując grupę zasobów, należy wziąć pod uwagę pewne ważne czynniki:

* Wszystkie zasoby w grupie powinny mieć ten sam cykl życia. Są one wdrażane, aktualizowane i usuwane razem. Jeśli jakiś zasób, na przykład serwer bazy danych, ma mieć inny cykl wdrażania, powinien zostać umieszczony w innej grupie zasobów.

* Każdy zasób może znajdować się tylko w jednej grupie zasobów.

* Niektóre zasoby mogą znajdować się poza grupą zasobów. Te zasoby są wdrażane w [subskrypcji](../templates/deploy-to-subscription.md), [grupie zarządzania](../templates/deploy-to-management-group.md)lub [dzierżawie](../templates/deploy-to-tenant.md). W tych zakresach są obsługiwane tylko określone typy zasobów.

* Zasoby w grupie można dodawać i usuwać w dowolnym momencie.

* Zasoby można przenosić między poszczególnymi grupami. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](move-resource-group-and-subscription.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

* Grupa zasobów może zawierać zasoby, które znajdują się w różnych regionach.

* Grupa zasobów może służyć do określania zakresu kontroli dostępu na potrzeby działań administracyjnych.

* Zasób może wchodzić w interakcję z zasobami znajdującymi się w innych grupach zasobów. Ta interakcja jest typowa, gdy dwa zasoby są ze sobą powiązane, ale nie mają tego samego cyklu życia (na przykład aplikacje internetowe łączące się z bazą danych).

Podczas tworzenia grupy zasobów, należy podać lokalizację dla danej grupy zasobów. Być może zastanawiasz się, „Dlaczego grupa zasobów wymaga określenia lokalizacji? Ponadto dlaczego lokalizacja grupy zasobów jest w ogóle istotna, skoro zasoby mogą znajdować się w innej lokalizacji niż grupa zasobów?” Grupa zasobów przechowuje metadane dotyczące zasobów. W przypadku określania lokalizacji grupy zasobów należy określić miejsce przechowywania metadanych. Dla zachowania zgodności może być konieczne upewnienie się, że dane są przechowywane w odpowiednim regionie.

Jeśli region grupy zasobów jest tymczasowo niedostępny, nie można zaktualizować zasobów w grupie zasobów, ponieważ metadane są niedostępne. Zasoby w innych regionach będą nadal działać zgodnie z oczekiwaniami, ale nie można ich zaktualizować. Aby uzyskać więcej informacji na temat tworzenia niezawodnych aplikacji, zobacz [projektowanie niezawodnych aplikacji platformy Azure](/azure/architecture/checklist/resiliency-per-service).

## <a name="resiliency-of-azure-resource-manager"></a>Odporność Azure Resource Manager

Usługa Azure Resource Manager została zaprojektowana pod kątem odporności i ciągłej dostępności. Operacje płaszczyzny Menedżer zasobów i kontroli (żądania wysyłane do management.azure.com) w interfejsie API REST są następujące:

* Rozproszone w różnych regionach. Niektóre usługi są regionalne.

* Dystrybuowane między Strefy dostępności (jak również regiony) w lokalizacjach, które mają wiele Strefy dostępności.

* Nie jest zależne od pojedynczego logicznego centrum danych.

* Nigdy nie są wykonywane w ramach działań konserwacyjnych.

Ta odporność ma zastosowanie do usług, które odbierają żądania przez Menedżer zasobów. Na przykład Key Vault korzyści wynikające z tej odporności.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o przenoszeniu zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](move-resource-group-and-subscription.md).

* Aby dowiedzieć się więcej o znakowaniu zasobów, zobacz [Używanie tagów do organizowania zasobów platformy Azure](tag-resources.md).

* Aby dowiedzieć się więcej o blokowaniu zasobów, zobacz [blokowanie zasobów, aby zapobiec nieoczekiwanym zmianom](lock-resources.md).
