---
title: Projektowanie przepływów pracy typu „zasady platformy Azure jako kod”
description: Dowiedz się, jak projektować przepływy pracy, aby wdrożyć definicje Azure Policy jako kod i automatycznie sprawdzać poprawność zasobów.
ms.date: 10/20/2020
ms.topic: conceptual
ms.openlocfilehash: 74d2097e4db4442e6e65f30541864fb554f7379d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359684"
---
# <a name="design-azure-policy-as-code-workflows"></a>Projektowanie przepływów pracy typu „zasady platformy Azure jako kod”

W miarę postępów związanych z zarządzaniem chmurą należy zmienić ręczną procedurę zarządzania wszystkimi definicjami zasad w Azure Portal lub za pośrednictwem różnych zestawów SDK, aby łatwiej zarządzać i powtarzać się w skali przedsiębiorstwa. Dwa z dominujących metod zarządzania systemami na dużą skalę w chmurze to:

- Infrastruktura jako kod: praktyczne traktowanie zawartości definiującej Twoje środowiska, wszystko z szablonów Azure Resource Manager (szablony ARM) do Azure Policy definicji do planów platformy Azure, jako kod źródłowy.
- DevOps: związek osób, procesów i produktów, aby umożliwić ciągłe dostarczanie wartości naszym użytkownikom końcowym.

Azure Policy, ponieważ kod jest kombinacją tych pomysłów. Zasadniczo należy zachować definicje zasad w kontroli źródła oraz za każdym razem, gdy zmiana została wprowadzona, przetestować i zweryfikować tę zmianę. Jednak nie powinno to być stopień zaangażowania zasad z infrastrukturą jako kodem lub DevOps.

Krok walidacji powinien również być składnikiem innych przepływów pracy ciągłej integracji i ciągłego wdrażania. Przykłady obejmują wdrażanie środowiska aplikacji lub infrastruktury wirtualnej. Dzięki wykorzystaniu Azure Policy sprawdzać poprawność wczesnego składnika procesu kompilacji i wdrożenia, zespoły aplikacji i operacji wykrywają, czy zmiany nie są zgodne, długo przed upływem zbyt późno i podejmują próbę wdrożenia w środowisku produkcyjnym.

## <a name="definitions-and-foundational-information"></a>Definicje i informacje podstawowe

Przed zapoznaj się ze szczegółami Azure Policy jako przepływ pracy kodu, przejrzyj następujące definicje i przykłady:

- [Definicja zasad](./definition-structure.md)
- [Definicja inicjatywy](./initiative-definition-structure.md)

Nazwy plików są wyrównane do części definicji zasad lub inicjatywy:
- `policy(set).json` -Cała definicja
- `policy(set).parameters.json` - `properties.parameters` Część definicji
- `policy.rules.json` - `properties.policyRule` Część definicji
- `policyset.definitions.json` - `properties.policyDefinitions` Część definicji

Przykłady tych formatów plików są dostępne w [repozytorium Azure Policy GitHub](https://github.com/Azure/azure-policy/):

- Definicja zasad: [Dodawanie znacznika do zasobów](https://github.com/Azure/azure-policy/tree/master/samples/Tags/add-tag)
- Definicja inicjatywy: [Tagi rozliczeń](https://github.com/Azure/azure-policy/tree/master/samples/PolicyInitiatives/multiple-billing-tags)

## <a name="workflow-overview"></a>Omówienie przepływu pracy

Zalecany ogólny przepływ pracy Azure Policy jako kod wygląda podobnie do tego diagramu:

:::image type="complex" source="../media/policy-as-code/policy-as-code-workflow.png" alt-text="Diagram przedstawiający Azure Policy jako pola przepływu pracy kodu z tworzenia do przetestowania w celu wdrożenia." border="false":::
   Diagram przedstawiający Azure Policy jako pola przepływu pracy w kodzie. Tworzenie obejmuje tworzenie definicji zasad i inicjatyw. Test obejmuje przypisanie z wyłączonym trybem wymuszania. Po sprawdzeniu stanu zgodności następuje Nadanie uprawnień M S i korygowaniem zasobów.  Wdrożenie obejmuje aktualizację przypisania z włączonym trybem wymuszania.
:::image-end:::

### <a name="create-and-update-policy-definitions"></a>Tworzenie i aktualizowanie definicji zasad

Definicje zasad są tworzone przy użyciu formatu JSON i przechowywane w kontroli źródła. Każda zasada ma swój własny zestaw plików, taki jak parametry, reguły i parametry środowiska, które powinny być przechowywane w tym samym folderze. Następująca struktura jest zalecanym sposobem utrzymywania definicji zasad w kontroli źródła.

```text
.
|
|- policies/  ________________________ # Root folder for policy resources
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- assign.<name1>.json _________ # Assignment 1 for this policy definition
|     |- assign.<name2>.json _________ # Assignment 2 for this policy definition
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- assign.<name1>.json _________ # Assignment 1 for this policy definition
|     |- assign.<name2>.json _________ # Assignment 2 for this policy definition
|
```

Po dodaniu nowych zasad lub zaktualizowaniu istniejącego, przepływ pracy powinien automatycznie aktualizować definicję zasad na platformie Azure. Przetestowanie nowej lub zaktualizowanej definicji zasad znajduje się w późniejszym kroku.

Zapoznaj się również z tematem [eksportowanie Azure Policy zasobów](../how-to/export-resources.md) , aby uzyskać istniejące definicje i przypisania do środowiska zarządzania kodem źródłowym w serwisie [GitHub](https://www.github.com).

### <a name="create-and-update-initiative-definitions"></a>Tworzenie i aktualizowanie definicji inicjatywy

Podobnie inicjatywy mają własny plik JSON i powiązane pliki, które powinny być przechowywane w tym samym folderze. Definicja inicjatywy wymaga, aby definicja zasad już istnieje, dlatego nie można jej utworzyć ani zaktualizować, dopóki źródło zasad nie zostanie zaktualizowane w kontroli źródła, a następnie zaktualizowane na platformie Azure. Następująca struktura jest zalecanym sposobem utrzymywania definicji inicjatyw w kontroli źródła:

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- assign.<name1>.json _________ # Assignment 1 for this policy initiative
|     |- assign.<name2>.json _________ # Assignment 2 for this policy initiative
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- assign.<name1>.json _________ # Assignment 1 for this policy initiative
|     |- assign.<name2>.json _________ # Assignment 2 for this policy initiative
|
```

Podobnie jak w przypadku definicji zasad, podczas dodawania lub aktualizowania istniejącej inicjatywy przepływ pracy powinien automatycznie aktualizować definicję inicjatywy na platformie Azure. Przetestowanie nowej lub zaktualizowanej definicji inicjatywy znajduje się w późniejszym kroku.

### <a name="test-and-validate-the-updated-definition"></a>Testowanie i weryfikowanie zaktualizowanej definicji

Gdy usługa Automation przestała nowo utworzone lub zaktualizowane definicje zasad lub inicjatyw i wykona aktualizację obiektu na platformie Azure, czas na przetestowanie wprowadzonych zmian zostanie przeprowadzony. Zasady lub inicjatywy, które są częścią, powinny następnie być przypisane do zasobów w środowisku najdalej z produkcji. To środowisko jest zwykle _deweloperskie_.

Przypisanie powinno używać [wymuszania](./assignment-structure.md#enforcement-mode) _wyłączone_ , tak aby tworzenie i aktualizowanie zasobów nie było blokowane, ale istniejące zasoby nadal są poddawane inspekcji pod kątem zgodności ze zaktualizowaną definicją zasad. Nawet w przypadku wymuszania, zaleca się, aby zakres przypisania był grupą zasobów lub subskrypcją, która jest przeznaczona do sprawdzania poprawności zasad.

> [!NOTE]
> Tryb wymuszania jest przydatny, ale nie zastępuje dokładnego testowania definicji zasad w różnych warunkach. Definicja zasad powinna być testowana przy użyciu `PUT` `PATCH` wywołań interfejsu API REST, zgodnych i niezgodnych zasobów oraz przypadków brzegowych, takich jak brak właściwości w zasobie.

Po wdrożeniu przypisania Użyj zestawu SDK Azure Policy, [akcji skanowania zgodności Azure Policy](https://github.com/marketplace/actions/azure-policy-compliance-scan)lub zadania w witrynie GitHub dotyczącej [Azure Pipelines zabezpieczeń i zgodności](/azure/devops/pipelines/tasks/deploy/azure-policy) , aby [uzyskać dane zgodności](../how-to/get-compliance-data.md) dla nowego przypisania. Środowisko używane do testowania zasad i przypisań powinno mieć zarówno zgodne, jak i niezgodne zasoby.
Podobnie jak w przypadku dobrego testu jednostkowego dla kodu, należy przetestować, czy zasoby są zgodne z oczekiwaniami, a także nie mają fałszywych wartości fałszywie dodatnich lub fałszywych. W przypadku testowania i weryfikowania tylko tego, czego oczekujesz, może wystąpić nieoczekiwany i niezidentyfikowany wpływ zasad. Aby uzyskać więcej informacji, zobacz [ocenę wpływu nowej definicji Azure Policy](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Włączanie zadań korygowania

Jeśli walidacja przypisania spełnia oczekiwania, następnym krokiem jest zweryfikowanie korekty.
Zasady korzystające z usługi [deployIfNotExists](./effects.md#deployifnotexists) lub [Modify](./effects.md#modify) mogą być uwzględniane w zadaniu korygowania i poprawiać zasoby z niezgodnego stanu.

Pierwszym krokiem korygowaniem zasobów jest przyznanie przypisania zasad zdefiniowanego w definicji zasad. To przypisanie roli umożliwia zarządzanie tożsamością przez przypisanie zasad wystarczających uprawnień, aby wprowadzić wymagane zmiany w celu uzyskania zgodności zasobów.

Gdy przypisanie zasad ma odpowiednie prawa, użyj zestawu SDK zasad, aby wyzwolić zadanie korygowania względem zestawu zasobów, które są znane jako niezgodne. Przed kontynuowaniem należy wykonać trzy testy w odniesieniu do tych skorygowanych zadań:

- Sprawdź, czy zadanie korygowania zostało ukończone pomyślnie
- Uruchom ocenę zasad, aby zobaczyć, że wyniki zgodności z zasadami są aktualizowane zgodnie z oczekiwaniami
- Uruchom test jednostkowy środowiska w odniesieniu do zasobów bezpośrednio, aby sprawdzić, czy zmieniono ich właściwości

Testowanie zarówno zaktualizowanych wyników oceny zasad, jak i środowiska bezpośrednio zapewniają potwierdzenie, że zadania korygowania uległy zmianie, czego oczekuje się i że definicja zasad wykazała zmianę zgodności zgodnie z oczekiwaniami.

### <a name="update-to-enforced-assignments"></a>Aktualizuj, aby wymuszać przypisania

Po zakończeniu wszystkich bram walidacji zaktualizuj przypisanie, aby używać _włączonego_ elementu **wymuszania** . Zaleca się, aby ta zmiana była początkowo w tym samym środowisku, daleko od środowiska produkcyjnego. Po sprawdzeniu poprawności tego środowiska zgodnie z oczekiwaniami należy zmienić zakres, aby uwzględnić następne środowisko itd., dopóki zasady nie zostaną wdrożone w zasobach produkcyjnych.

## <a name="process-integrated-evaluations"></a>Przetwarzaj zintegrowane oceny

Ogólny przepływ pracy dla Azure Policy jako kod służy do opracowywania i wdrażania zasad oraz inicjatyw w środowisku na dużą skalę. Jednak Ocena zasad powinna być częścią procesu wdrażania dla każdego przepływu pracy, który wdraża lub tworzy zasoby na platformie Azure, takie jak wdrażanie aplikacji lub uruchamianie szablonów ARM w celu utworzenia infrastruktury.

W takich przypadkach po wdrożeniu aplikacji lub infrastruktury w ramach subskrypcji testowej lub grupy zasobów należy przeprowadzić ocenę dla tego zakresu, sprawdzając sprawdzanie poprawności wszystkich istniejących zasad i inicjatyw. Chociaż mogą być skonfigurowane jako **wymuszenia** _wyłączone_ w takim środowisku, warto szybko wiedzieć, czy wdrożenie aplikacji lub infrastruktury ma na celu wczesne naruszenie definicji zasad. W związku z tym Ocena zasad powinna być krokiem w tych przepływach pracy i wdrożeniami zakończonymi niezgodnością.

## <a name="review"></a>Przegląd

W tym artykule omówiono ogólny przepływ pracy dla Azure Policy jako kod, a także to, że Ocena zasad powinna być częścią innych przepływów pracy wdrożenia. Tego przepływu pracy można użyć w dowolnym środowisku, które obsługuje wykonywanie kroków skryptowych i automatyzację na podstawie wyzwalaczy. Aby zapoznać się z samouczkiem dotyczącym korzystania z tego przepływu pracy w usłudze GitHub, zobacz [Samouczek: implementowanie Azure Policy jako kodu w usłudze GitHub](../tutorials/policy-as-code-github.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [strukturze definicji zasad](./definition-structure.md).
- Dowiedz się więcej na temat [struktury przypisywania zasad](./assignment-structure.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
