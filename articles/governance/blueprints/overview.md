---
title: Omówienie usługi Azure Blueprints
description: Dowiedz się, w jaki sposób usługa Azure Plans umożliwia tworzenie, Definiowanie i wdrażanie artefaktów w środowisku platformy Azure.
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: f4ba77f5fcb376bf600d94997b0d6ba569f04f82
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919346"
---
# <a name="what-is-azure-blueprints"></a>Czym jest usługa Azure Blueprints?

Usługę Azure Blueprints można porównać do planu, który pozwala inżynierowi lub architektowi naszkicować parametry projektu. Usługa ta umożliwia architektom chmury i centralnym grupom technologii informatycznych zdefiniowanie powtarzalnego zestawu zasobów platformy Azure, który implementuje standardy, wzorce i wymagania organizacji oraz jest z nimi zgodny. Plany platformy Azure umożliwiają zespołom programistycznym szybkie tworzenie i wdrażanie nowych środowisk z zaufaniem, które są tworzone w ramach zgodności organizacji z zestawem wbudowanych składników, takich jak sieć, w celu przyspieszenia tworzenia i dostarczania.

Usługa Blueprints umożliwia deklaratywne organizowanie i wdrażanie różnych szablonów zasobów i innych artefaktów, takich jak:

- Przypisania ról
- Przypisania zasad
- Szablony Azure Resource Manager (szablony ARM)
- Grupy zasobów

Usługa Azure Blueprints jest wspierana przez globalnie dystrybuowaną [usługę Azure Cosmos DB](../../cosmos-db/introduction.md). Obiekty strategii są replikowane w wielu regionach świadczenia usługi Azure. Ta replikacja zapewnia małe opóźnienia, wysoką dostępność i spójny dostęp do obiektów planu, niezależnie od tego, w jakim regionie plany platformy Azure wdrażają Twoje zasoby.

## <a name="how-its-different-from-arm-templates"></a>Różnice między szablonami ARM

Usługa została zaprojektowana w celu ułatwienia _konfiguracji środowiska_. Ta konfiguracja często składa się z zestawu grup zasobów, zasad, przypisań ról i wdrożeń szablonów ARM. Plan to pakiet, który umożliwia łączenie każdego z tych typów _artefaktów_ , oraz pozwala na redagowanie i Tworzenie wersji tego pakietu, w tym za pomocą potoku ciągłej integracji i ciągłego dostarczania (Ci/CD). Każdy pakiet jest ostatecznie przypisywany do subskrypcji w ramach jednej operacji, którą można poddawać inspekcji i śledzić.

Niemal wszystkie elementy, które chcesz dołączyć do wdrożenia w planach platformy Azure, można zrealizować przy użyciu szablonu ARM. Jednak szablon ARM jest dokumentem, który nie istnieje natywnie na platformie Azure — każdy jest przechowywany lokalnie lub w kontroli źródła. Szablon jest używany na potrzeby wdrożenia jednego lub większej liczby zasobów platformy Azure, ale po wdrożeniu tych zasobów nie ma aktywnego połączenia z wykorzystanym szablonem ani relacji z nim.

Dzięki planom platformy Azure relacja między definicją strategii (co _należy_ wdrożyć) a przypisaniem planu (co _zostało_ wdrożone) jest zachowywana. To połączenie obsługuje ulepszone śledzenie i inspekcję wdrożeń. Plany platformy Azure mogą również uaktualnić kilka subskrypcji jednocześnie, które podlegają temu samemu planowi.

Nie trzeba wybierać między szablonem ARM a planem. Każdy plan może składać się z co najmniej zero _artefaktów_ szablonu ARM. Ta pomoc techniczna oznacza, że poprzednie wysiłki w zakresie opracowywania i konserwowania biblioteki szablonów ARM są wielokrotnego użytku w planach platformy Azure.

## <a name="how-its-different-from-azure-policy"></a>Czym różni się od usługi Azure Policy

Strategia to pakiet lub kontener umożliwiający tworzenie specyficznych dla kontekstu zestawów standardów, wzorców i wymagań dotyczących implementacji usług Azure Cloud Services, zabezpieczeń i projektów, które mogą zostać ponownie użyte w celu zachowania spójności i zgodności.

[Zasady](../policy/overview.md) to system z domyślnym zezwalaniem i wyraźnym zabranianiem, który jest skoncentrowany na właściwościach wdrażanych i istniejących już zasobów. Obsługuje on zarządzanie chmurą przez weryfikowanie, czy zasoby w ramach subskrypcji są zgodne z wymaganiami i standardami.

Uwzględnienie zasad w strategii umożliwia utworzenie odpowiedniego wzorca lub projektu podczas przypisywania strategii. Dołączenie zasad gwarantuje, że w środowisku mogą być wprowadzane tylko zatwierdzone lub oczekiwane zmiany, co umożliwia ochronę stałej zgodności z intencją strategii.

Zasady mogą być dołączane jako jeden z wielu _artefaktów_ w definicji strategii. Strategie obsługują również używanie parametrów z zasadami i inicjatywami.

## <a name="blueprint-definition"></a>Definicja strategii

Plan składa się z _artefaktów_. Plany platformy Azure obsługują obecnie następujące zasoby jako artefakty:

|Zasób  | Opcje hierarchii| Opis  |
|---------|---------|---------|
|Grupy zasobów | Subskrypcja | Umożliwia utworzenie nowej grupy zasobów do użytku przez inne artefakty w ramach strategii.  Te symbole zastępcze grup zasobów umożliwiają organizowanie zasobów dokładnie w taki sposób, aby były one uporządkowane i udostępniają ogranicznik zakresu dla dołączonych zasad i artefaktów przypisywania ról oraz szablonów ARM. |
|Szablon ARM | Subskrypcja, grupa zasobów | Szablony, w tym zagnieżdżone i połączone szablony, służą do tworzenia złożonych środowisk. Przykładowe środowiska: farma programu SharePoint, konfiguracja stanu usługi Azure Automation lub obszar roboczy usługi Log Analytics. |
|Przypisanie zasad | Subskrypcja, grupa zasobów | Umożliwia przypisanie zasad lub inicjatywy do subskrypcji, do której przypisano strategię. Zasady lub inicjatywa muszą znajdować się w zakresie lokalizacji definicji strategii. Jeśli zasady lub inicjatywa mają parametry, są one przypisywane podczas tworzenia strategii bądź podczas jej przypisywania. |
|Przypisanie roli | Subskrypcja, grupa zasobów | Dodawanie istniejącego użytkownika lub grupy do wbudowanej roli w celu zagwarantowania, że odpowiednie osoby zawsze będą mieć odpowiedni dostęp do zasobów. Przypisania ról mogą być definiowane dla całej subskrypcji lub mogą być zagnieżdżone w konkretnej grupie zasobów uwzględnionej w strategii. |

### <a name="blueprint-definition-locations"></a>Lokalizacje definicji strategii

Podczas tworzenia definicji strategii należy zdefiniować miejsce, w którym strategia zostanie zapisana. Strategie można zapisywać tylko w [grupie zarządzania](../management-groups/overview.md) lub subskrypcji, do której użytkownik ma dostęp jako **Współautor**. Jeśli lokalizacja znajduje się w grupie zarządzania, strategię można przypisać do dowolnej subskrypcji podrzędnej tej grupy zarządzania.

### <a name="blueprint-parameters"></a>Parametry strategii

Plany mogą przekazywać parametry do szablonu Policy/Initiative lub ARM. Podczas dodawania dowolnego _artefaktu_ do strategii autor decyduje o udostępnieniu zdefiniowanej wartości dla każdego przypisania strategii lub zezwoleniu, aby każde przypisanie strategii udostępniało wartość w czasie przypisywania. Dzięki tej elastyczności można zdefiniować wstępnie ustaloną wartość dla wszystkich zastosowań strategii lub umożliwić podjęcie decyzji w czasie przypisywania.

> [!NOTE]
> Strategia może mieć własne parametry, ale obecnie można je tworzyć tylko w przypadku, kiedy strategia jest generowana w interfejsie API REST, a nie za pomocą portalu.

Aby uzyskać więcej informacji, zobacz [parametry strategii](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Publikowanie strategii

Kiedy strategia jest tworzona po raz pierwszy, przyjmuje się, że jest w trybie **wersji roboczej**. Kiedy jest gotowa do przypisania, należy ją **opublikować**. Publikowanie wymaga zdefiniowania ciągu **wersji** (liter, cyfr i łączników o maksymalnej długości 20 znaków) wraz z opcjonalnymi **uwagami dotyczącymi zmian**. Dzięki zastosowaniu **wersji** przyszłe zmiany wprowadzone w strategii mogą być traktowane jako osobne wersje z możliwością ich przypisania. Obsługa wersji oznacza również, że różne **wersje** tej samej strategii można przypisać do jednej subskrypcji. Po wprowadzeniu dodatkowych zmian do planu **opublikowana** 
 **wersja** nadal istnieje, tak jak w przypadku **nieopublikowanych zmian**. Po zakończeniu wprowadzania zmian zaktualizowana strategia jest **publikowana** jako nowa unikatowa **wersja**, którą teraz również można przypisać.

## <a name="blueprint-assignment"></a>Przypisywanie strategii

Każdą **opublikowaną** **wersję** planu można przypisać (z maksymalną długością 90 znaków) do istniejącej grupy zarządzania lub subskrypcji. W portalu domyślną **wersją** strategii jest ta, która została **opublikowana** jako ostatnia. Jeśli istnieją parametry artefaktów lub parametry planu, parametry są definiowane podczas procesu przypisywania.

> [!NOTE]
> Przypisanie definicji planu do grupy zarządzania oznacza, że obiekt przypisania istnieje w grupie zarządzania. Wdrożenie artefaktów nadal odwołuje się do subskrypcji. Aby można było wykonać przypisanie grupy zarządzania, należy użyć [interfejsu API Rest tworzenia lub aktualizacji](/rest/api/blueprints/assignments/createorupdate) , a treść żądania musi zawierać wartość określającą `properties.scope` , aby zdefiniować subskrypcję docelową.

## <a name="permissions-in-azure-blueprints"></a>Uprawnienia w usłudze Azure Blueprints

Aby korzystać z planów, należy przyznać uprawnienia za pośrednictwem [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md). Aby odczytać lub wyświetlić plan w Azure Portal, Twoje konto musi mieć dostęp do odczytu do zakresu, w którym znajduje się definicja planu.

Aby użytkownik mógł tworzyć strategie, jego konto musi mieć następujące uprawnienia:

- `Microsoft.Blueprint/blueprints/write` — tworzenie definicji strategii
- `Microsoft.Blueprint/blueprints/artifacts/write` — tworzenie artefaktów w definicji strategii
- `Microsoft.Blueprint/blueprints/versions/write` — publikowanie strategii

Aby użytkownik mógł usuwać strategie, jego konto musi mieć następujące uprawnienia:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> Uprawnienia definicji strategii muszą zostać nadane lub odziedziczone w grupie zarządzania lub w zakresie subskrypcji, w których zostały zapisane.

Aby użytkownik mógł przypisywać strategie lub anulować ich przypisanie, jego konto musi mieć następujące uprawnienia:

- `Microsoft.Blueprint/blueprintAssignments/write` — przypisywanie strategii
- `Microsoft.Blueprint/blueprintAssignments/delete` — anulowanie przypisania strategii

> [!NOTE]
> Przypisania strategii są tworzone w ramach subskrypcji, dlatego uprawnienia przypisywania strategii lub anulowania takiego przypisania muszą zostać przyznane w zakresie subskrypcji bądź muszą być dziedziczone w zakresie subskrypcji.

Dostępne są następujące wbudowane role:

|Rola na platformie Azure | Opis |
|-|-|
|[Właściciel](../../role-based-access-control/built-in-roles.md#owner) | Oprócz innych uprawnień program zawiera wszystkie Azure Blueprint powiązane uprawnienia. |
|[Współautor](../../role-based-access-control/built-in-roles.md#contributor) | Oprócz innych uprawnień program może tworzyć i usuwać definicje planów, ale nie ma uprawnień do przypisywania strategii. |
|[Współautor planu](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | Może zarządzać definicjami planu, ale nie należy ich przypisywać. |
|[Operator planu](../../role-based-access-control/built-in-roles.md#blueprint-operator) | Może przypisywać istniejące opublikowane plany, ale nie może tworzyć nowych definicji planu. Przypisanie strategii działa tylko wtedy, gdy przypisanie zostało wykonane przy użyciu tożsamości zarządzanej przypisanej przez użytkownika. |

Jeśli te wbudowane role nie spełniają wymagań dotyczących zabezpieczeń, należy rozważyć utworzenie [roli niestandardowej](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> W przypadku korzystania z tożsamości zarządzanej przypisanej do systemu jednostka usługi dla planów platformy Azure wymaga roli **właściciela** w przypisanej subskrypcji, aby można było włączyć wdrożenie. W przypadku korzystania z portalu ta rola jest automatycznie przyznawana i odwoływana dla wdrożenia. W przypadku korzystania z interfejsu API REST ta rola musi zostać ręcznie przyznana, ale jest automatycznie odwoływana po zakończeniu wdrożenia. Jeśli jest używana tożsamość zarządzana przypisana przez użytkownika, tylko użytkownik tworzący przypisanie planu potrzebuje `Microsoft.Blueprint/blueprintAssignments/write` uprawnienia, które jest zawarte w rolach role **właściciela** i **operatora** planu.

## <a name="naming-limits"></a>Limity nazewnictwa

Dla niektórych pól istnieją następujące ograniczenia:

|Obiekt|Pole|Dozwolone znaki|Maksymalnie z Długość|
|-|-|-|-|
|Potrzeby|Nazwa|litery, cyfry, łączniki i kropki|48|
|Potrzeby|Wersja|litery, cyfry, łączniki i kropki|20|
|Przypisywanie strategii|Nazwa|litery, cyfry, łączniki i kropki|90|
|Artefakt planu|Nazwa|litery, cyfry, łączniki i kropki|48|

## <a name="video-overview"></a>Omówienie wideo

Poniższy omówienie usługi Azure Blueprints pochodzi z witryny Azure Fridays. Aby pobrać wideo, odwiedź stronę [Azure Fridays - An overview of Azure Blueprints (Azure Fridays — omówienie usługi Azure Blueprints)](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) w witrynie Channel 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Następne kroki

- [Utwórz plan — Portal](./create-blueprint-portal.md).
- [Utwórz plan — PowerShell](./create-blueprint-powershell.md).
- [Tworzenie interfejsu API REST](./create-blueprint-rest-api.md).