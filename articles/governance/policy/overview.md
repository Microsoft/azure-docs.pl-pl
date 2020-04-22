---
title: Omówienie zasad platformy Azure
description: Azure Policy to usługa platformy Azure, która umożliwia tworzenie i przypisywanie definicji zasad oraz zarządzanie nimi w środowisku platformy Azure.
ms.date: 04/21/2020
ms.topic: overview
ms.openlocfilehash: 68005a9a07e7f081a646c75566a0be4046f078ad
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770580"
---
# <a name="what-is-azure-policy"></a>Co to jest Azure Policy?

Usługa Azure Policy pomaga w egzekwowaniu standardów organizacyjnych i ocenie zgodności na dużą skalę. Za pośrednictwem pulpitu nawigacyjnego zgodności zapewnia zagregowany widok do oceny ogólnego stanu środowiska, z możliwością przechodzenia do szczegółów do szczegółowości dla zasobów, na zasady. Pomaga również zapewnić zgodność zasobów poprzez zbiorcze korygowanie istniejących zasobów i automatyczne korygowanie nowych zasobów.

Typowe przypadki użycia dla usługi Azure Policy obejmują implementowanie nadzoru w celu zapewnienia spójności zasobów, zgodności z przepisami, zabezpieczeń, kosztów i zarządzania. Definicje zasad dla tych typowych przypadków użycia są już dostępne w środowisku platformy Azure jako wbudowane, aby ułatwić rozpoczęcie pracy.

## <a name="overview"></a>Omówienie

Usługa Azure Policy ocenia zasoby na platformie Azure, porównując właściwości tych zasobów z regułami biznesowymi. Te reguły biznesowe, opisane w [formacie JSON,](./concepts/definition-structure.md)są znane jako [definicje zasad.](#policy-definition) Aby uprościć zarządzanie, można zgrupować kilka reguł biznesowych w celu utworzenia [inicjatywy politycznej](#initiative-definition) (czasami nazywanej _policySet_). Po utworzeniu reguł biznesowych definicja lub inicjatywa zasad jest [przypisywana](#assignments) do dowolnego zakresu zasobów, które obsługuje platforma Azure, takich jak grupy [zarządzania,](../management-groups/overview.md)subskrypcje, [grupy zasobów](../../azure-resource-manager/management/overview.md#resource-groups)lub zasoby indywidualne. Przypisanie ma zastosowanie do wszystkich zasobów w [zakresie](../../azure-resource-manager/management/overview.md#understand-scope) tego przypisania.
W razie potrzeby można wykluczyć podkwzakoń.

Usługa Azure Policy używa [formatu JSON](./concepts/definition-structure.md) do utworzenia logiki używanej przez ocenę do określenia, czy zasób jest zgodny, czy nie. Definicje obejmują metadane i regułę zasad. Zdefiniowana reguła może używać funkcji, parametrów, operatorów logicznych, warunków i [aliasów](./concepts/definition-structure.md#aliases) właściwości, aby dokładnie dopasować odpowiedni scenariusz. Reguła zasad określa, które zasoby w zakresie przydziału są oceniane.

### <a name="understand-evaluation-outcomes"></a>Zrozumienie wyników oceny

Zasoby są oceniane w określonych godzinach w cyklu życia zasobu, cyklu życia przypisania zasad i dla regularnej bieżącej oceny zgodności. Poniżej przedstawiono godziny lub zdarzenia, które powodują, że zasób ma zostać oceniony:

- Zasób jest tworzony, aktualizowany lub usuwany w zakresie z przypisaniem zasad.
- Zasady lub inicjatywy jest nowo przypisany do zakresu.
- Zasady lub inicjatywy już przypisane do zakresu jest aktualizowana.
- Podczas standardowego cyklu oceny zgodności, który występuje raz na 24 godziny.

Aby uzyskać szczegółowe informacje o tym, kiedy i jak odbywa się ocena zasad, zobacz [Wyzwalacze ewaluacji](./how-to/get-compliance-data.md#evaluation-triggers).

### <a name="control-the-response-to-an-evaluation"></a>Kontrola reakcji na ocenę

Reguły biznesowe dotyczące obsługi niezgodnych zasobów różnią się znacznie w zależności od organizacji. Przykłady tego, jak organizacja chce, aby platforma odpowiadała na zasób niebędący skargą:

- Odmów zmiany zasobu
- Rejestrowanie zmiany w zasobie
- Zmienianie zasobu przed zmianą
- Zmienianie zasobu po zmianie
- Wdrażanie powiązanych zgodnych zasobów

Usługa Azure Policy umożliwia każdej z tych odpowiedzi biznesowych za pomocą [efektów.](./concepts/effects.md) Efekty są ustawiane w części **reguły zasad** [definicji zasad](./concepts/definition-structure.md).

### <a name="remediate-non-compliant-resources"></a>Korygowanie niezgodnych zasobów

Podczas gdy te efekty wpływają przede wszystkim na zasób, gdy zasób jest tworzony lub aktualizowany, usługa Azure Policy obsługuje również radzenie sobie z istniejącymi zasobami niezgodnymi bez konieczności zmiany tego zasobu. Aby uzyskać więcej informacji na temat zgodności istniejących zasobów, zobacz [korygowanie zasobów](./how-to/remediate-resources.md).

### <a name="video-overview"></a>Omówienie wideo

Poniższe omówienie usługi Azure Policy dotyczy kompilacji 2018. Aby pobrać slajdy lub klip wideo, odwiedź stronę [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) (Zarządzanie środowiskiem platformy Azure przy użyciu usługi Azure Policy) w witrynie Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Wprowadzenie

### <a name="azure-policy-and-rbac"></a>Zasady platformy Azure i rbac

Istnieje kilka kluczowych różnic między zasadami platformy Azure a kontrolą dostępu opartą na rolach (RBAC). Usługa Azure Policy ocenia stan, badając właściwości zasobów, które są reprezentowane w Menedżerze zasobów i właściwości niektórych dostawców zasobów. Usługa Azure Policy nie ogranicza akcji (nazywanych również _operacjami)._ Usługa Azure Policy zapewnia, że stan zasobów jest zgodny z regułami biznesowymi bez obawy, kto dokonał zmiany lub kto ma uprawnienia do wprowadzania zmian.

RBAC koncentruje się na zarządzaniu [działaniami](../../role-based-access-control/resource-provider-operations.md) użytkowników w różnych zakresach. Jeśli wymagana jest kontrola akcji, rbac jest właściwym narzędziem do użycia. Nawet jeśli osoba ma dostęp do wykonania akcji, jeśli wynikiem jest niezgodny zasób, usługa Azure Policy nadal blokuje tworzenie lub aktualizowanie.

Połączenie rbac i usługi Azure Policy zapewniają pełną kontrolę zakresu na platformie Azure.

### <a name="rbac-permissions-in-azure-policy"></a>Uprawnienia RBAC w usłudze Azure Policy

Usługa Azure Policy ma kilka uprawnień, znanych jako operacje, w ramach dwóch dostawców zasobów:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Wiele wbudowanych ról udziela uprawnień zasobom usługi Azure Policy. Rola **współautora zasad zasobów** obejmuje większość operacji usługi Azure Policy. **Właściciel** ma pełne uprawnienia. Współautor **Contributor** i **Program Reader** mają dostęp do wszystkich operacji _odczytu_ usługi Azure Policy. **Współautor** może wyzwolić korygowanie zasobów, ale nie może _tworzyć_ definicji ani przypisań.

Jeśli żadna z wbudowanych ról nie ma wymaganych uprawnień, należy utworzyć [rolę niestandardową](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Tożsamość zarządzana **deployIfNotExists** przypisanie zasad wymaga wystarczających uprawnień do tworzenia lub aktualizowania zasobów zawartych w szablonie. Aby uzyskać więcej informacji, zobacz [Konfigurowanie definicji zasad w celu korygowania](./how-to/remediate-resources.md#configure-policy-definition).

### <a name="resources-covered-by-azure-policy"></a>Zasoby objęte zasadami platformy Azure

Usługa Azure Policy ocenia wszystkie zasoby na platformie Azure. W przypadku niektórych dostawców zasobów, takich jak [Konfiguracja gościa,](./concepts/guest-configuration.md) [Usługa Azure Kubernetes](../../aks/intro-kubernetes.md)i [Usługa Azure Key Vault,](../../key-vault/key-vault-overview.md)istnieje głębsza integracja w zakresie zarządzania ustawieniami i obiektami. Aby dowiedzieć się więcej, zobacz [Tryby dostawcy zasobów](./concepts/definition-structure.md).

### <a name="recommendations-for-managing-policies"></a>Zalecenia dotyczące zarządzania zasadami

Poniżej przedstawiono kilka wskazówek i porad, które warto uwzględnić:

- Rozpocznij od efektu inspekcji zamiast efektu odrzucenia, aby śledzić wpływ definicji zasad na zasoby w Twoim środowisku. Jeśli masz już skrypty umożliwiające automatyczne skalowanie swoich aplikacji, skonfigurowanie efektu odrzucenia może negatywnie wpłynąć na zdefiniowane wcześniej zadania automatyzacji.

- Podczas tworzenia definicji i przypisań należy zwrócić uwagę na hierarchie organizacyjne. Firma Microsoft zaleca tworzenie definicji wyższym poziomie, takim jak poziom grupy zarządzania lub subskrypcji. Następnie utwórz przypisanie na następnym poziomie podrzędnym. Jeśli utworzysz definicję na poziomie grupy zarządzania, można ograniczyć zakres przypisania do subskrypcji lub grupy zasobów w tej grupie zarządzania.

- Firma Microsoft zaleca tworzenie i przypisywanie definicji inicjatyw, nawet w przypadku pojedynczych definicji zasad.
  Na przykład możesz mieć definicję zasad _policyDefA_ utworzoną w ramach definicji inicjatywy _initiativeDefC_. Jeśli zdecydujesz się na utworzenie w późniejszym czasie kolejnej definicji zasad _policyDefB_ o celach podobnych do _policyDefA_, możesz dodać ją do definicji _initiativeDefC_ i śledzić je razem.

- Po utworzeniu przypisania inicjatywy definicje zasad dodane do inicjatywy również stają się częścią przypisań tej inicjatywy.

- Podczas oceny przypisania inicjatywy oceniane są również wszystkie zasady w ramach tej inicjatywy.
  Jeśli konieczne jest indywidualne ocenianie zasad, lepszym rozwiązaniem jest nieuwzględnianie ich w inicjatywie.

## <a name="azure-policy-objects"></a>Obiekty zasad platformy Azure

### <a name="policy-definition"></a>Definicja zasad

Proces tworzenia i implementowania zasad w usłudze Azure Policy rozpoczyna się od utworzenia definicji zasad. Każda definicja zasad zawiera warunki, w jakich zasady są wymuszane. Zawiera także zdefiniowany efekt, który występuje w przypadku spełnienia warunków.

Usługa Azure Policy oferuje kilka wbudowanych zasad, które są domyślnie dostępne. Przykład:

- **Dozwolone jednostki SKU konta magazynu** (odmów) określa, czy konto magazynu jest wdrażane w zestawie rozmiarów jednostek SKU. Jej efektem jest odrzucanie wszystkich kont magazynu, które nie są zgodne z zestawem zdefiniowanych rozmiarów SKU.
- **Dozwolony typ zasobu** (odmowa): definiuje typy zasobów, które można wdrożyć. Jej efektem jest odrzucanie wszystkich zasobów, które nie należą do tej zdefiniowanej listy.
- **Dozwolone lokalizacje** (odmowa): Ogranicza dostępne lokalizacje dla nowych zasobów. Jej efekt jest używany do wymuszania wymagań dotyczących zgodności obszarów geograficznych.
- **Dozwolone jednostki SKU maszyny wirtualnej** (odmowa): Określa zestaw jednostek SKU maszyny wirtualnej, które można wdrożyć.
- **Dodaj znacznik do zasobów** (Modyfikuj): Stosuje wymagany tag i jego wartość domyślną, jeśli nie jest określony przez żądanie wdrożenia.
- **Dołącz tag i jego wartość domyślną** (Dołącz): Wymusza wymagany znacznik i jego wartość do zasobu.
- **Niedozwolone typy zasobów** (Odmów): Zapobiega wdrażaniu listy typów zasobów.

Aby móc zaimplementować te definicje zasad (wbudowane i niestandardowe), musisz je przypisać. Dowolną z tych zasad można przypisać za pośrednictwem witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Ocena zasad odbywa się przy użyciu kilku różnych akcji, takich jak przypisanie zasad lub aktualizacje zasad. Aby uzyskać pełną listę, zobacz [Wyzwalacze oceny zasad](./how-to/get-compliance-data.md#evaluation-triggers).

Aby dowiedzieć się więcej o strukturach definicji zasad, zapoznaj się z tematem [Policy Definition Structure](./concepts/definition-structure.md) (Struktura definicji zasad).

Parametry zasad ułatwiają zarządzanie zasadami przez redukowanie liczby definicji zasad, które należy utworzyć. Podczas tworzenia definicji zasad można zdefiniować parametry, które czynią je bardziej ogólnymi. Następnie można użyć tej definicji zasad ponownie w różnych scenariuszach. Polega to na przekazaniu innych wartości podczas przypisywania definicji zasad. Można na przykład określić jeden zestaw lokalizacji dla subskrypcji.

Parametry są definiowane podczas tworzenia definicji zasad. Jeśli parametr jest zdefiniowany, otrzymuje nazwę i opcjonalnie wartość. Na przykład można zdefiniować parametr dla zasad o nazwie _location_. Następnie podczas przypisywania zasad można przydzielić mu różne wartości, takie jak _EastUS_ i _WestUS_.

Dodatkowe informacje na temat parametrów zasad zamieszczono w artykule [Struktura definicji — parametry](./concepts/definition-structure.md#parameters).

### <a name="initiative-definition"></a>Definicja inicjatywy

Definicja inicjatywy to kolekcja definicji zasad dostosowanych w celu osiągnięcia jednego ogólnego celu. Definicje inicjatyw upraszczają przypisywanie definicji zasad i zarządzanie nimi. Upraszczają działania przez grupowanie zestawu zasad w ramach pojedynczego elementu. Można na przykład utworzyć inicjatywę o nazwie **Włączanie monitorowania w Azure Security Center**, której celem jest monitorowanie wszystkich dostępnych zaleceń dotyczących zabezpieczeń w Azure Security Center.

> [!NOTE]
> Zestaw SDK, taki jak narzędzia interfejsu wiersza polecenia platformy Azure i program Azure PowerShell, używa właściwości i parametrów o nazwie **PolicySet** w celu odwoływania się do inicjatyw.

W ramach tej inicjatywy mogą występować definicje zasad, takie jak:

- **Monitorowanie nieszyfrowanej bazy danych SQL w Security Center ** — do monitorowania niezaszyfrowanych baz danych i serwerów SQL.
- **Monitorowanie luk w zabezpieczeniach systemu operacyjnego w usłudze Security Center** — do monitorowania serwerów, które nie spełniają wymagań skonfigurowanego punktu odniesienia.
- **Monitorowanie brakującej ochrony punktów końcowych w Security Center** — do monitorowania serwerów bez zainstalowanego agenta chroniącego punkty końcowe.

Podobnie jak parametry zasad, parametry inicjatywy upraszczają zarządzanie inicjatywą przez ograniczenie nadmiarowości. Parametry inicjatywy to parametry używane przez definicje zasad w ramach tej inicjatywy.

Na przykład masz definicję inicjatywy **initiativeC** oraz definicje zasad **policyA** i **policyB**. Każda z nich oczekuje innego typu parametru:

| Zasady | Nazwa parametru |Typ parametru  |Uwaga |
|---|---|---|---|
| policyA | allowedLocations | tablica  |Ten parametr oczekuje listy ciągów dla wartości, ponieważ typ parametru został zdefiniowany jako tablica |
| policyB | allowedSingleLocation |ciąg |Ten parametr oczekuje jednego słowa dla wartości, ponieważ typ parametru został zdefiniowany jako ciąg |

W tym scenariuszu podczas definiowania parametrów inicjatywy **initiativeC** dostępne są trzy opcje:

- Użycie parametrów definicji zasad w ramach tej inicjatywy: w tym przykładzie _allowedLocations_ i _allowedSingleLocation_ stają się parametrami inicjatywy dla **initiativeC**.
- Przekaż wartości parametrom definicji zasad w ramach tej definicji inicjatywy. W tym przykładzie można podać listę lokalizacji do **policyA**parametr - **allowedLocations** i **policyB**'s parametr - **allowedSingleLocation**. Wartości można przekazać również podczas przypisywania tej inicjatywy.
- Podaj listę opcji _value_, które mogą być używane podczas przypisywania tej inicjatywy. Podczas przypisywania tej inicjatywy odziedziczone parametry z definicji zasad w ramach tej inicjatywy mogą zawierać jedynie wartości z tej dostarczonej listy.

W przypadku tworzenia opcji wartości w definicji inicjatywy nie można wprowadzić innej wartości w trakcie przypisywania inicjatywy, ponieważ nie jest ona częścią listy.

### <a name="assignments"></a>Przypisania

Przypisanie jest definicją zasad lub inicjatywą, która została przypisana do odbywania się w określonym zakresie. Ten zakres może wahać się od [grupy zarządzania](../management-groups/overview.md) do pojedynczego zasobu. Termin _zakres_ odnosi się do wszystkich zasobów, grup zasobów, subskrypcji lub grup zarządzania, do których jest przypisana definicja. Przydziały są dziedziczone przez wszystkie zasoby podrzędne. Ten projekt oznacza, że definicja zastosowana do grupy zasobów jest również stosowana do zasobów w tej grupie zasobów. Można jednak wykluczyć podzakres z przypisania.

Na przykład w zakresie subskrypcji można przypisać definicję, która uniemożliwia tworzenie zasobów sieciowych. Można wyłączyć grupę zasobów w ramach subskrypcji, która jest przeznaczona dla infrastruktury sieciowej. Następnie dostęp do tej grupy zasobów sieciowych można przyznać użytkownikom, którym powierzono tworzenie zasobów sieciowych.

W innym przykładzie można przypisać definicję listy zezwalania na typ zasobu na poziomie grupy zarządzania. Następnie można przypisać bardziej dopuszczalne zasady (zezwalające na więcej typów zasobów) w podrzędnej grupie zarządzania lub nawet bezpośrednio w subskrypcjach. Jednak w tym przykładzie nie będzie działać, ponieważ zasady platformy Azure jest jawnym systemem odmowy. Zamiast tego należy wykluczyć podrzędną grupę zarządzania lub subskrypcję z przypisania na poziomie grupy zarządzania. Następnie przypisz bardziej dopuszczalną definicję na podrzędnej grupie zarządzania lub poziomie subskrypcji. Jeśli jakiekolwiek przypisanie powoduje odmowę zasobu, jedynym sposobem na zezwolenie zasobu jest zmodyfikowanie przypisania odmowy.

Aby uzyskać więcej informacji na temat ustawiania przydziałów za pośrednictwem portalu, zobacz [Tworzenie przypisania zasad w celu identyfikowania niezgodnych zasobów w środowisku platformy Azure](assign-policy-portal.md). Dostępne są również instrukcje dotyczące korzystania z programu [PowerShell](assign-policy-powershell.md) i [interfejsu wiersza polecenia platformy Azure](assign-policy-azurecli.md).

## <a name="maximum-count-of-azure-policy-objects"></a>Maksymalna liczba obiektów usługi Azure Policy

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już podstawowe informacje na temat usługi Azure Policy i kluczowych pojęć, oto zalecane kolejne kroki:

- [Przejrzyj strukturę definicji zasad](./concepts/definition-structure.md).
- [Przypisywanie definicji zasad za pomocą portalu](./assign-policy-portal.md).
