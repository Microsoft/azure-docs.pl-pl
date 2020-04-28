---
title: Projektowanie przepływów pracy typu zasady jako kod
description: Dowiedz się, jak projektować przepływy pracy, aby wdrożyć definicje Azure Policy jako kod i automatycznie sprawdzać poprawność zasobów.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: fd77fdd4011c3e1e83f8dfa9f30045bb72881c25
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187736"
---
# <a name="design-policy-as-code-workflows"></a>Projektowanie przepływów pracy typu zasady jako kod

W miarę postępów związanych z zarządzaniem chmurą należy zmienić ręczną procedurę zarządzania wszystkimi definicjami zasad w Azure Portal lub za pośrednictwem różnych zestawów SDK, aby łatwiej zarządzać i powtarzać się w skali przedsiębiorstwa. Dwa z dominujących metod zarządzania systemami na dużą skalę w chmurze to:

- Infrastruktura jako kod: praktyczne traktowanie zawartości definiującej Twoje środowiska, wszystko to z szablonów Menedżer zasobów, aby Azure Policy definicje do planów platformy Azure, jako kod źródłowy.
- DevOps: związek osób, procesów i produktów, aby umożliwić ciągłe dostarczanie wartości naszym użytkownikom końcowym.

Zasady jako kod to kombinacja tych pomysłów. Zasadniczo należy zachować definicje zasad w kontroli źródła i za każdym razem, gdy zmiana została wprowadzona, przetestować i zweryfikować tę zmianę. Jednak nie powinno to być stopień zaangażowania zasad z infrastrukturą jako kodem lub DevOps.

Krok walidacji powinien również być składnikiem innych przepływów pracy ciągłej integracji i ciągłego wdrażania. Przykłady obejmują wdrażanie środowiska aplikacji lub infrastruktury wirtualnej. Dzięki wykorzystaniu Azure Policy sprawdzać poprawność wczesnego składnika procesu kompilacji i wdrożenia, zespoły aplikacji i operacji wykrywają, czy ich zmiany nie są reklamacją, długo przed upływem zbyt późno i podejmują próbę wdrożenia w środowisku produkcyjnym.

## <a name="workflow-overview"></a>Omówienie przepływu pracy

Zalecany ogólny przepływ pracy zasad jako kod wygląda jak na tym diagramie:

:::image type="content" source="../media/policy-as-code/policy-as-code-workflow.png" alt-text="Omówienie zasad jako przepływu pracy w kodzie" border="false":::

### <a name="create-and-update-policy-definitions"></a>Tworzenie i aktualizowanie definicji zasad

Definicje zasad są tworzone przy użyciu formatu JSON i przechowywane w kontroli źródła. Każda zasada ma swój własny zestaw plików, taki jak parametry, reguły i parametry środowiska, które powinny być przechowywane w tym samym folderze. Następująca struktura jest zalecanym sposobem utrzymywania definicji zasad w kontroli źródła.

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Po dodaniu nowych zasad lub zaktualizowaniu istniejącego, przepływ pracy powinien automatycznie aktualizować definicję zasad na platformie Azure. Przetestowanie nowej lub zaktualizowanej definicji zasad znajduje się w późniejszym kroku.

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
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Podobnie jak w przypadku definicji zasad, podczas dodawania lub aktualizowania istniejącej inicjatywy przepływ pracy powinien automatycznie aktualizować definicję inicjatywy na platformie Azure. Przetestowanie nowej lub zaktualizowanej definicji inicjatywy znajduje się w późniejszym kroku.

### <a name="test-and-validate-the-updated-definition"></a>Testowanie i weryfikowanie zaktualizowanej definicji

Gdy usługa Automation przestała nowo utworzone lub zaktualizowane definicje zasad lub inicjatyw i wykona aktualizację obiektu na platformie Azure, czas na przetestowanie wprowadzonych zmian zostanie przeprowadzony. Zasady lub inicjatywy, które są częścią, powinny następnie być przypisane do zasobów w środowisku najdalej z produkcji. To środowisko jest zwykle _deweloperskie_.

Przypisanie powinno używać [wymuszania](./assignment-structure.md#enforcement-mode) _wyłączone_ , tak aby tworzenie i aktualizowanie zasobów nie było blokowane, ale istniejące zasoby nadal są poddawane inspekcji pod kątem zgodności ze zaktualizowaną definicją zasad. Nawet w przypadku wymuszania, zaleca się, aby zakres przypisania był grupą zasobów lub subskrypcją, która jest używana do sprawdzania poprawności zasad.

> [!NOTE]
> Tryb wymuszania jest przydatny, ale nie zastępuje dokładnego testowania definicji zasad w różnych warunkach. Definicja zasad powinna być testowana przy użyciu `PUT` wywołań `PATCH` interfejsu API REST, zgodnych i niezgodnych zasobów oraz przypadków brzegowych, takich jak brak właściwości w zasobie.

Po wdrożeniu przypisania Użyj zestawu SDK zasad, aby [uzyskać dane zgodności](../how-to/get-compliance-data.md) dla nowego przypisania. Środowisko używane do testowania zasad i przypisań powinno mieć zarówno zgodne, jak i niezgodne zasoby. Podobnie jak w przypadku dobrego testu jednostkowego dla kodu, należy przetestować, czy zasoby są zgodne z oczekiwaniami, a także nie mają fałszywych wartości fałszywie dodatnich lub fałszywych. W przypadku testowania i weryfikowania tylko tego, czego oczekujesz, może wystąpić nieoczekiwany i niezidentyfikowany wpływ zasad. Aby uzyskać więcej informacji, zobacz [ocenę wpływu nowej definicji Azure Policy](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Włączanie zadań korygowania

Jeśli walidacja przypisania spełnia oczekiwania, następnym krokiem jest zweryfikowanie korekty.
Zasady korzystające z usługi [deployIfNotExists](./effects.md#deployifnotexists) lub [Modify](./effects.md#modify) mogą być uwzględniane w zadaniu korygowania i poprawiać zasoby z niezgodnego stanu.

Pierwszym krokiem do wykonania tej czynności jest przyznanie przypisania zasad zdefiniowanego w definicji zasad. To przypisanie roli umożliwia zarządzanie tożsamością przez przypisanie zasad wystarczających uprawnień, aby wprowadzić wymagane zmiany w celu uzyskania zgodności zasobów.

Gdy przypisanie zasad ma odpowiednie prawa, użyj zestawu SDK zasad, aby wyzwolić zadanie korygowania względem zestawu zasobów, które są znane jako niezgodne. Przed kontynuowaniem należy wykonać trzy testy w odniesieniu do tych skorygowanych zadań:

- Sprawdź, czy zadanie korygowania zostało ukończone pomyślnie
- Uruchom ocenę zasad, aby zobaczyć, że wyniki zgodności z zasadami są aktualizowane zgodnie z oczekiwaniami
- Uruchom test jednostkowy środowiska w odniesieniu do zasobów bezpośrednio, aby sprawdzić, czy zmieniono ich właściwości

Testowanie zarówno zaktualizowanych wyników oceny zasad, jak i środowiska bezpośrednio zapewniają potwierdzenie, że zadania korygowania uległy zmianie, czego oczekuje się i że definicja zasad wykazała zmianę zgodności zgodnie z oczekiwaniami.

### <a name="update-to-enforced-assignments"></a>Aktualizuj, aby wymuszać przypisania

Po zakończeniu wszystkich bram walidacji zaktualizuj przypisanie, aby używać _włączonego_elementu **wymuszania** . Ta zmiana powinna zostać początkowo wprowadzona w tym samym środowisku poza środowiskiem produkcyjnym. Po sprawdzeniu poprawności tego środowiska zgodnie z oczekiwaniami należy zmienić zakres, aby uwzględnić następne środowisko i tak dalej, dopóki zasady nie zostaną wdrożone w zasobach produkcyjnych.

## <a name="process-integrated-evaluations"></a>Przetwarzaj zintegrowane oceny

Ogólny przepływ pracy dla zasad jako kod służy do opracowywania i wdrażania zasad oraz inicjatyw w środowisku na dużą skalę. Jednak Ocena zasad powinna być częścią procesu wdrażania dla każdego przepływu pracy, który wdraża lub tworzy zasoby na platformie Azure, takie jak wdrażanie aplikacji lub uruchamianie szablonów Menedżer zasobów w celu utworzenia infrastruktury.

W takich przypadkach po wdrożeniu aplikacji lub infrastruktury w ramach subskrypcji testowej lub grupy zasobów należy przeprowadzić ocenę dla tego zakresu, sprawdzając sprawdzanie poprawności wszystkich istniejących zasad i inicjatyw. Chociaż mogą być skonfigurowane jako **wymuszenia** _wyłączone_ w takim środowisku, warto szybko wiedzieć, czy wdrożenie aplikacji lub infrastruktury ma na celu wczesne naruszenie definicji zasad. W związku z tym Ocena zasad powinna być krokiem w tych przepływach pracy i wdrożeniami zakończonymi niezgodnością.

## <a name="review"></a>Przegląd

W tym artykule opisano ogólny przepływ pracy dla zasad jako kod, a także miejsce oceny zasad, które powinny być częścią innych przepływów pracy wdrożenia. Tego przepływu pracy można użyć w dowolnym środowisku, które obsługuje wykonywanie kroków skryptowych i automatyzację na podstawie wyzwalaczy.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [strukturze definicji zasad](./definition-structure.md).
- Dowiedz się więcej na temat [struktury przypisywania zasad](./assignment-structure.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).