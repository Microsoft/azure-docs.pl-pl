---
title: Omówienie zasad platformy Azure
description: Azure Policy to usługa platformy Azure, która umożliwia tworzenie i przypisywanie definicji zasad oraz zarządzanie nimi w środowisku platformy Azure.
ms.date: 10/05/2020
ms.topic: overview
ms.openlocfilehash: 8a32e32afb544588bb033cc64ede5ecbe6e2bac2
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097392"
---
# <a name="what-is-azure-policy"></a>Co to jest Azure Policy?

Usługa Azure Policy pomaga wymuszać standardy organizacyjne i oceniać zgodność na dużą skalę. Pulpit nawigacyjny zgodności udostępnia Zagregowany widok do oceny ogólnego stanu środowiska, z możliwością przechodzenia do szczegółów dla poszczególnych zasobów i stopnia szczegółowości poszczególnych zasad. Pomaga również zapewnić zgodność zasobów dzięki korygowaniu zbiorczemu dla istniejących zasobów i automatycznym korygowaniu nowych zasobów.

Typowe przypadki użycia Azure Policy obejmują wdrażanie ładu pod kątem spójności zasobów, zgodności z przepisami, bezpieczeństwa, kosztów i zarządzania. Definicje zasad dla tych typowych przypadków użycia są już dostępne w środowisku platformy Azure jako wbudowane, aby pomóc Ci rozpocząć pracę.

Wszystkie dane i obiekty Azure Policy są szyfrowane w stanie spoczynku. Aby uzyskać więcej informacji, zobacz [Azure Data Encryption w spoczynku](../../security/fundamentals/encryption-atrest.md).

## <a name="overview"></a>Omówienie

Azure Policy oblicza zasoby na platformie Azure, porównując właściwości tych zasobów z regułami biznesowymi. Te reguły biznesowe, opisane w [formacie JSON](./concepts/definition-structure.md), są nazywane [definicjami zasad](#policy-definition). Aby uprościć zarządzanie, można zgrupować kilka reguł firmowych w celu utworzenia [inicjatywy](#initiative-definition) dotyczącej zasad (nazywanych czasem _policySet_). Po utworzeniu reguł firmy definicja zasad lub inicjatywa jest [przypisywana](#assignments) do dowolnego zakresu zasobów obsługiwanych przez platformę Azure, takich jak [grupy zarządzania](../management-groups/overview.md), subskrypcje, [grupy zasobów](../../azure-resource-manager/management/overview.md#resource-groups)lub poszczególne zasoby. Przypisanie dotyczy wszystkich zasobów w [zakresie Menedżer zasobów](../../azure-resource-manager/management/overview.md#understand-scope) tego przydziału. W razie potrzeby można wykluczyć podzakresy. Aby uzyskać więcej informacji, zobacz [zakres w Azure Policy](./concepts/scope.md).

Azure Policy używa [formatu JSON](./concepts/definition-structure.md) do tworzenia logiki używanej przez ewaluacyjną do określenia, czy zasób jest zgodny, czy nie. Definicje obejmują metadane i regułę zasad. Zdefiniowana reguła może używać funkcji, parametrów, operatorów logicznych, warunków i [aliasów](./concepts/definition-structure.md#aliases) właściwości, aby dokładnie dopasować żądany scenariusz. Reguła zasad określa, które zasoby z zakresu przydziału zostaną obliczone.

### <a name="understand-evaluation-outcomes"></a>Poznaj wyniki oceny

Zasoby są oceniane w określonym czasie podczas cyklu życia zasobów, cyklu życia przypisania zasad i dla regularnej ciągłej oceny zgodności. Poniżej przedstawiono czasy lub zdarzenia, które powodują obliczenie zasobu:

- Zasób jest tworzony, aktualizowany lub usuwany w zakresie z przypisaniem zasad.
- Zasady lub inicjatywa są nowo przypisane do zakresu.
- Zasady lub inicjatywa już przypisane do zakresu zostały zaktualizowane.
- W trakcie standardowego cyklu oceny zgodności, który występuje co 24 godziny.

Aby uzyskać szczegółowe informacje o tym, kiedy i jak odbywa się Ocena zasad, zobacz [wyzwalacze oceny](./how-to/get-compliance-data.md#evaluation-triggers).

### <a name="control-the-response-to-an-evaluation"></a>Sterowanie odpowiedzią na ocenę

Reguły biznesowe do obsługi niezgodnych zasobów różnią się między organizacjami. Przykłady sposobu, w jaki organizacja chce odpowiedzieć na platformę do zasobów niezwiązanych z skargą:

- Odmowa zmiany zasobu
- Rejestruj zmianę w zasobie
- Zmień zasób przed zmianą
- Zmień zasób po zmianie
- Wdróż powiązane zasoby zgodne

Azure Policy każda z tych odpowiedzi jest możliwa w wyniku zastosowania [efektów](./concepts/effects.md). Efekty są ustawiane w części **reguły zasad** w [definicji zasad](./concepts/definition-structure.md).

### <a name="remediate-non-compliant-resources"></a>Korygowanie niezgodnych zasobów

Chociaż te efekty mają wpływ głównie na zasób, gdy zasób jest tworzony lub aktualizowany, Azure Policy obsługuje również obsługę istniejących niezgodnych zasobów bez konieczności zmiany tego zasobu. Aby uzyskać więcej informacji na temat tworzenia zgodności istniejących zasobów, zobacz [korygowaniem Resources](./how-to/remediate-resources.md).

### <a name="video-overview"></a>Omówienie wideo

Poniższe omówienie usługi Azure Policy dotyczy kompilacji 2018. Aby pobrać slajdy lub klip wideo, odwiedź stronę [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) (Zarządzanie środowiskiem platformy Azure przy użyciu usługi Azure Policy) w witrynie Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Wprowadzenie

### <a name="azure-policy-and-azure-rbac"></a>Azure Policy i kontrola RBAC na platformie Azure

Istnieje kilka najważniejszych różnic między Azure Policy i kontroli dostępu opartej na rolach (RBAC) na platformie Azure. Azure Policy szacuje stan, sprawdzając właściwości zasobów, które są reprezentowane w Menedżer zasobów i właściwościach niektórych dostawców zasobów. Azure Policy nie ogranicza akcji (nazywanych również _operacjami_). Azure Policy zapewnia, że stan zasobów jest zgodny z regułami biznesowymi bez obaw dla użytkowników, którzy wprowadzili zmianę lub którzy mają uprawnienia do wprowadzania zmian.

Usługi Azure RBAC koncentrują się na zarządzaniu [akcjami](../../role-based-access-control/resource-provider-operations.md) użytkownika w różnych zakresach. Jeśli wymagana jest kontrola nad akcją, funkcja RBAC platformy Azure jest poprawnym narzędziem do użycia. Nawet jeśli osoba ma dostęp do wykonywania akcji, jeśli wynik jest niezgodnym zasobem, Azure Policy nadal blokuje Tworzenie lub aktualizowanie.

Kombinacja RBAC i Azure Policy platformy Azure zapewnia pełną kontrolę zakresu na platformie Azure.

### <a name="azure-rbac-permissions-in-azure-policy"></a>Uprawnienia usługi Azure RBAC w Azure Policy

Usługa Azure Policy ma kilka uprawnień, znanych jako operacje, w ramach dwóch dostawców zasobów:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft. PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Wiele wbudowanych ról udziela uprawnień zasobom usługi Azure Policy. Rola **współautor zasad zasobów** obejmuje większość operacji Azure Policy. **Właściciel** ma pełne uprawnienia. Zarówno **współautor** , jak i **czytelnik** mają dostęp do wszystkich operacji _odczytu_ Azure Policy. **Współautor** może wyzwolić korygowanie zasobów, ale nie może _tworzyć_ definicji ani przypisań. **Administrator dostępu użytkowników** jest wymagany do udzielenia tożsamości zarządzanej na **deployIfNotExists** lub **zmodyfikowania** przypisań wymaganych uprawnień.

Jeśli żadna z wbudowanych ról nie ma wymaganych uprawnień, należy utworzyć [rolę niestandardową](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Zarządzana tożsamość **deployIfNotExists** lub **Modyfikowanie** przypisania zasad wymaga wystarczających uprawnień do tworzenia lub aktualizowania zasobów docelowe. Aby uzyskać więcej informacji, zobacz [Konfigurowanie definicji zasad na potrzeby korygowania](./how-to/remediate-resources.md#configure-policy-definition).

### <a name="resources-covered-by-azure-policy"></a>Zasoby objęte Azure Policy

Azure Policy szacuje wszystkie zasoby na platformie Azure i w zasobach z włączonym łukiem. W przypadku niektórych dostawców zasobów, takich jak [Konfiguracja gościa](./concepts/guest-configuration.md), [usługa Azure Kubernetes](../../aks/intro-kubernetes.md)i [Azure Key Vault](../../key-vault/general/overview.md), istnieje dokładniejsza integracja z zarządzaniem ustawieniami i obiektami. Aby dowiedzieć się więcej, zobacz [tryby dostawcy zasobów](./concepts/definition-structure.md).

### <a name="recommendations-for-managing-policies"></a>Zalecenia dotyczące zarządzania zasadami

Poniżej przedstawiono kilka wskazówek i porad, które warto uwzględnić:

- Rozpocznij od efektu inspekcji zamiast efektu odrzucenia, aby śledzić wpływ definicji zasad na zasoby w Twoim środowisku. Jeśli masz już skrypty umożliwiające automatyczne skalowanie swoich aplikacji, skonfigurowanie efektu odrzucenia może negatywnie wpłynąć na zdefiniowane wcześniej zadania automatyzacji.

- Podczas tworzenia definicji i przypisań należy zwrócić uwagę na hierarchie organizacyjne. Firma Microsoft zaleca tworzenie definicji wyższym poziomie, takim jak poziom grupy zarządzania lub subskrypcji. Następnie utwórz przypisanie na następnym poziomie podrzędnym. Jeśli utworzysz definicję na poziomie grupy zarządzania, można ograniczyć zakres przypisania do subskrypcji lub grupy zasobów w tej grupie zarządzania.

- Firma Microsoft zaleca tworzenie i przypisywanie definicji inicjatyw, nawet w przypadku pojedynczych definicji zasad.
  Na przykład możesz mieć definicję zasad _policyDefA_ utworzoną w ramach definicji inicjatywy _initiativeDefC_. Jeśli zdecydujesz się na utworzenie w późniejszym czasie kolejnej definicji zasad _policyDefB_ o celach podobnych do _policyDefA_, możesz dodać ją do definicji _initiativeDefC_ i śledzić je razem.

- Po utworzeniu przypisania inicjatywy definicje zasad dodane do inicjatywy również stają się częścią przypisań tej inicjatywy.

- Podczas oceny przypisania inicjatywy oceniane są również wszystkie zasady w ramach tej inicjatywy.
  Jeśli konieczne jest indywidualne ocenianie zasad, lepszym rozwiązaniem jest nieuwzględnianie ich w inicjatywie.

## <a name="azure-policy-objects"></a>Azure Policy obiektów

### <a name="policy-definition"></a>Definicja zasad

Proces tworzenia i implementowania zasad w usłudze Azure Policy rozpoczyna się od utworzenia definicji zasad. Każda definicja zasad zawiera warunki, w jakich zasady są wymuszane. Zawiera także zdefiniowany efekt, który występuje w przypadku spełnienia warunków.

Usługa Azure Policy oferuje kilka wbudowanych zasad, które są domyślnie dostępne. Na przykład:

- **Dozwolone jednostki SKU konta magazynu** (odmowa): określa, czy wdrożone konto magazynu znajduje się w zestawie rozmiarów jednostki SKU. Jej efektem jest odrzucanie wszystkich kont magazynu, które nie są zgodne z zestawem zdefiniowanych rozmiarów SKU.
- **Dozwolony typ zasobu** (Odmów): określa typy zasobów, które można wdrożyć. Jej efektem jest odrzucanie wszystkich zasobów, które nie należą do tej zdefiniowanej listy.
- **Dozwolone lokalizacje** (Odmów): ogranicza dostępne lokalizacje dla nowych zasobów. Jej efekt jest używany do wymuszania wymagań dotyczących zgodności obszarów geograficznych.
- **Dozwolone jednostki SKU maszyny wirtualnej** (Odmów): określa zestaw jednostek SKU maszyn wirtualnych, które można wdrożyć.
- **Dodaj tag do zasobów** (Modyfikuj): stosuje wymagany tag i jego wartość domyślną, jeśli nie jest określony przez żądanie wdrożenia.
- **Niedozwolone typy zasobów** (Odmów): uniemożliwiają wdrożenie listy typów zasobów.

Aby móc zaimplementować te definicje zasad (wbudowane i niestandardowe), musisz je przypisać. Dowolną z tych zasad można przypisać za pośrednictwem witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Ocena zasad odbywa się przy użyciu kilku różnych akcji, takich jak przypisanie zasad lub aktualizacje zasad. Aby uzyskać pełną listę, zobacz [Wyzwalacze oceny zasad](./how-to/get-compliance-data.md#evaluation-triggers).

Aby dowiedzieć się więcej o strukturach definicji zasad, zapoznaj się z tematem [Policy Definition Structure](./concepts/definition-structure.md) (Struktura definicji zasad).

Parametry zasad ułatwiają zarządzanie zasadami przez redukowanie liczby definicji zasad, które należy utworzyć. Podczas tworzenia definicji zasad można zdefiniować parametry, które czynią je bardziej ogólnymi. Następnie można użyć tej definicji zasad ponownie w różnych scenariuszach. Polega to na przekazaniu innych wartości podczas przypisywania definicji zasad. Można na przykład określić jeden zestaw lokalizacji dla subskrypcji.

Parametry są definiowane podczas tworzenia definicji zasad. Jeśli parametr jest zdefiniowany, otrzymuje nazwę i opcjonalnie wartość. Na przykład można zdefiniować parametr dla zasad o nazwie _location_. Następnie podczas przypisywania zasad można przydzielić mu różne wartości, takie jak _EastUS_ i _WestUS_.

Dodatkowe informacje na temat parametrów zasad zamieszczono w artykule [Struktura definicji — parametry](./concepts/definition-structure.md#parameters).

### <a name="initiative-definition"></a>Definicja inicjatywy

Definicja inicjatywy to kolekcja definicji zasad dostosowanych w celu osiągnięcia jednego ogólnego celu. Definicje inicjatyw upraszczają przypisywanie definicji zasad i zarządzanie nimi. Upraszczają działania przez grupowanie zestawu zasad w ramach pojedynczego elementu. Można na przykład utworzyć inicjatywę o nazwie **Włączanie monitorowania w Azure Security Center**, której celem jest monitorowanie wszystkich dostępnych zaleceń dotyczących zabezpieczeń w Azure Security Center.

> [!NOTE]
> Zestaw SDK, taki jak interfejs wiersza polecenia platformy Azure i Azure PowerShell, używają właściwości i parametrów o nazwie **PolicySet** , aby odwołać się do inicjatyw.

W ramach tej inicjatywy mogą występować definicje zasad, takie jak:

- **Monitorowanie nieszyfrowanej bazy danych SQL w Security Center ** — do monitorowania niezaszyfrowanych baz danych i serwerów SQL.
- **Monitorowanie luk w zabezpieczeniach systemu operacyjnego w usłudze Security Center** — do monitorowania serwerów, które nie spełniają wymagań skonfigurowanego punktu odniesienia.
- **Monitorowanie brakującej ochrony punktów końcowych w Security Center** — do monitorowania serwerów bez zainstalowanego agenta chroniącego punkty końcowe.

Podobnie jak parametry zasad, parametry inicjatywy upraszczają zarządzanie inicjatywą przez ograniczenie nadmiarowości. Parametry inicjatywy to parametry używane przez definicje zasad w ramach tej inicjatywy.

Na przykład masz definicję inicjatywy **initiativeC** oraz definicje zasad **policyA** i **policyB**. Każda z nich oczekuje innego typu parametru:

| Zasady | Nazwa parametru |Typ parametru  |Uwaga |
|---|---|---|---|
| policyA | allowedLocations | array  |Ten parametr oczekuje listy ciągów dla wartości, ponieważ typ parametru został zdefiniowany jako tablica |
| policyB | allowedSingleLocation |ciąg |Ten parametr oczekuje jednego słowa dla wartości, ponieważ typ parametru został zdefiniowany jako ciąg |

W tym scenariuszu podczas definiowania parametrów inicjatywy **initiativeC** dostępne są trzy opcje:

- Użycie parametrów definicji zasad w ramach tej inicjatywy: w tym przykładzie _allowedLocations_ i _allowedSingleLocation_ stają się parametrami inicjatywy dla **initiativeC**.
- Przekaż wartości parametrom definicji zasad w ramach tej definicji inicjatywy. W tym przykładzie można podać listę lokalizacji parametru **Policy**- **allowedLocations** i **policyB**- **allowedSingleLocation**. Wartości można przekazać również podczas przypisywania tej inicjatywy.
- Podaj listę opcji _value_, które mogą być używane podczas przypisywania tej inicjatywy. Podczas przypisywania tej inicjatywy odziedziczone parametry z definicji zasad w ramach tej inicjatywy mogą zawierać jedynie wartości z tej dostarczonej listy.

W przypadku tworzenia opcji wartości w definicji inicjatywy nie można wprowadzić innej wartości w trakcie przypisywania inicjatywy, ponieważ nie jest ona częścią listy.

Aby dowiedzieć się więcej o strukturach definicji inicjatywy, przejrzyj [strukturę definicji inicjatywy](./concepts/initiative-definition-structure.md).

### <a name="assignments"></a>Przypisania

Przypisanie jest definicją lub inicjatywą zasad, która została przypisana w określonym zakresie. Ten zakres może się wahać od [grupy zarządzania](../management-groups/overview.md) do pojedynczego zasobu. _Termin ten odnosi się do_ wszystkich zasobów, grup zasobów, subskrypcji lub grup zarządzania, do których jest przypisana definicja. Przypisania są dziedziczone przez wszystkie zasoby podrzędne. Ten projekt oznacza, że definicja zastosowana do grupy zasobów jest również stosowana do zasobów w tej grupie zasobów. Można jednak wykluczyć Podzakres z przypisania.

Na przykład w zakresie subskrypcji można przypisać definicję, która uniemożliwia tworzenie zasobów sieciowych. Można wyłączyć grupę zasobów w ramach subskrypcji, która jest przeznaczona dla infrastruktury sieciowej. Następnie dostęp do tej grupy zasobów sieciowych można przyznać użytkownikom, którym powierzono tworzenie zasobów sieciowych.

W innym przykładzie możesz chcieć przypisać do definicji listy dozwolonych typów zasobów na poziomie grupy zarządzania. Następnie można przypisać bardziej ograniczające zasady (co pozwala na więcej typów zasobów) w podrzędnej grupie zarządzania lub nawet bezpośrednio w subskrypcjach. Jednak ten przykład nie działa, ponieważ Azure Policy jest jawnym systemem odmowy. Zamiast tego należy wykluczyć podrzędną grupę zarządzania lub subskrypcję z przypisań na poziomie grupy zarządzania. Następnie należy przypisać bardziej ograniczającą definicję w podrzędnej grupie zarządzania lub na poziomie subskrypcji. Jeśli jakiekolwiek przypisanie powoduje odmowa zasobu, jedynym sposobem na umożliwienie zasobu jest zmodyfikowanie przypisania odmowy.

Aby uzyskać więcej informacji na temat ustawiania przypisań za pomocą portalu, zobacz [Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów w środowisku platformy Azure](./assign-policy-portal.md). Dostępne są również instrukcje dotyczące korzystania z programu [PowerShell](./assign-policy-powershell.md) i [interfejsu wiersza polecenia platformy Azure](./assign-policy-azurecli.md). Aby uzyskać informacje na temat struktury przypisania, zobacz [Struktura przydziałów](./concepts/assignment-structure.md).

## <a name="maximum-count-of-azure-policy-objects"></a>Maksymalna liczba obiektów Azure Policy

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już podstawowe informacje na temat usługi Azure Policy i kluczowych pojęć, oto zalecane kolejne kroki:

- [Przejrzyj strukturę definicji zasad](./concepts/definition-structure.md).
- [Przypisywanie definicji zasad przy użyciu portalu](./assign-policy-portal.md).
