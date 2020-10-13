---
title: Źródłowa kontrola i programowanie — LUIS
description: Jak zachować aplikację Language Understanding (LUIS) pod kontrolą źródła. Jak zastosować aktualizacje do aplikacji LUIS podczas pracy w gałęzi deweloperskiej.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 25f2c4f4698785326f80c24d3749e7585e85d5bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91309510"
---
# <a name="devops-practices-for-luis"></a>DevOps praktyk dla LUIS

Inżynierowie oprogramowania, którzy opracowują aplikację Language Understanding (LUIS), mogą stosować praktyki DevOps dotyczące [kontroli źródła](luis-concept-devops-sourcecontrol.md), [zautomatyzowanych kompilacji](luis-concept-devops-automation.md), [testów](luis-concept-devops-testing.md)i [zarządzania wersjami](luis-concept-devops-automation.md#release-management) , postępując zgodnie z tymi wskazówkami.

## <a name="source-control-and-branch-strategies-for-luis"></a>Strategie kontroli źródła i stosowania gałęzi dla usługi LUIS

Jednym z kluczowych czynników, od których zależy powodzenie DevOps, jest [Kontrola źródła](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops). System kontroli źródła umożliwia deweloperom współpracę w kodzie i śledzenie zmian. Użycie gałęzi umożliwia deweloperom przełączanie się między różnymi wersjami bazy kodu i pracę niezależnie od innych członków zespołu. Gdy deweloperzy zgłaszają [żądanie ściągnięcia](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) w celu zaproponowania aktualizacji z jednej gałęzi do innej lub gdy zmiany są scalane, może to być wyzwalacz dla [zautomatyzowanych kompilacji](luis-concept-devops-automation.md) , aby kompilować i ciągle testować kod.

Korzystając z pojęć i wskazówek opisanych w tym dokumencie, można opracowywać aplikację LUIS podczas śledzenia zmian w systemie kontroli źródła i stosować następujące najlepsze rozwiązania dotyczące inżynierii oprogramowania:

- **Kontrola źródła**
  - Kod źródłowy aplikacji LUIS jest w formacie do odczytu przez użytkownika.
  - Model można skompilować ze źródła w sposób powtarzalny.
  - Kod źródłowy może być zarządzany przez repozytorium kodu źródłowego.
  - Poświadczenia i wpisy tajne, takie jak tworzenie i klucze subskrypcji, nigdy nie są przechowywane w kodzie źródłowym.

- **Rozgałęzianie i scalanie**
  - Deweloperzy mogą korzystać z niezależnych gałęzi.
  - Deweloperzy mogą jednocześnie korzystać z wielu gałęzi.
  - Istnieje możliwość zintegrowania zmian w aplikacji LUIS z jednej gałęzi do innej za pośrednictwem zmiany bazy lub scalenia.
  - Deweloperzy mogą scalić żądanie ściągnięcia z gałęzią nadrzędną.

- **Przechowywanie wersji**
  - Każdy składnik w dużej aplikacji powinien mieć niezależną wersję, dzięki czemu deweloperzy mogą wykrywać istotne zmiany lub aktualizacje bezpośrednio, sprawdzając numer wersji.

- **Przeglądy kodu**
  - Zmiany w żądaniach ściągnięcia są prezentowane jako czytelny dla człowieka kod źródłowy, który można przejrzeć przed zaakceptowaniem żądania ściągnięcia.

## <a name="source-control"></a>Kontrola źródła

Aby zarządzać [definicją schematu aplikacji](https://docs.microsoft.com/azure/cognitive-services/luis/app-schema-definition) Luis w systemie zarządzania kodem źródłowym, użyj [formatu LUDown ( `.lu` )](https://docs.microsoft.com/azure/bot-service/file-format/bot-builder-lu-file-format?view=azure-bot-service-4.0)  aplikacji. `.lu` Format jest preferowany do `.json` formatowania, ponieważ jest czytelny, co ułatwia dokonywanie i przeglądanie zmian w żądań ściągnięcia.

### <a name="save-a-luis-app-using-the-ludown-format"></a>Zapisywanie aplikacji LUIS przy użyciu formatu LUDown

Aby zapisać aplikację LUIS w `.lu` formacie i umieścić ją pod kontrolą źródła:

- : [Wyeksportuj wersję aplikacji](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#other-actions) `.lu` z [portalu Luis](https://www.luis.ai/) i Dodaj ją do swojego repozytorium kontroli źródła

- LUB: Użyj edytora tekstów, aby utworzyć `.lu` plik dla aplikacji Luis i dodać go do repozytorium kontroli źródła

> [!TIP]
> Jeśli pracujesz z eksportem JSON aplikacji LUIS, możesz [ją skonwertować do LUDown](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert) przy użyciu [interfejsu wiersza polecenia BOTBUILDER-Tools Luis](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS). Użyj `--sort` opcji, aby upewnić się, że intencje i wyrażenia długości są sortowane alfabetycznie.  
> Należy pamiętać, że **. Funkcja eksportowania jednostki LU** wbudowana w Portal Luis już sortuje dane wyjściowe.

### <a name="build-the-luis-app-from-source"></a>Kompilowanie aplikacji LUIS ze źródła

W przypadku aplikacji LUIS do *kompilowania ze źródła* środków w celu [utworzenia nowej wersji aplikacji Luis przez zaimportowanie `.lu` źródła](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version) w celu [nauczenia](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) i[opublikowania](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app)wersji. Można to zrobić w portalu LUIS lub w wierszu polecenia:

- Użyj portalu LUIS, aby [zaimportować `.lu` wersję](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version) aplikacji z kontroli źródła, a następnie [nauczenie](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) i [opublikowanie](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app) aplikacji.

- Użyj [interfejsu wiersza polecenia bot Framework dla Luis](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) w wierszu polecenia lub w przepływie pracy ciągłej integracji/ciągłego [importowania](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport) `.lu` wersji aplikacji z kontroli źródła do aplikacji Luis, a następnie [nauczenie](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun) i [opublikowanie](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) aplikacji.

### <a name="files-to-maintain-under-source-control"></a>Pliki do obtrzymywania pod kontrolą źródła

Następujące typy plików dla aplikacji LUIS powinny być utrzymywane pod kontrolą źródła:

- `.lu` plik dla aplikacji LUIS

- [Pliki definicji testów jednostkowych](luis-concept-devops-testing.md#writing-tests) (wyrażenia długości i oczekiwane wyniki)

- [Pliki testów wsadowych](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test#batch-file-format) (wyrażenia długości i oczekiwane wyniki) używane do testowania wydajności

### <a name="credentialsand-keys-are-not-checked-in"></a>Poświadczenia i klucze nie są zaewidencjonowane

Nie uwzględniaj kluczy subskrypcji ani podobnych poufnych wartości w plikach, które można zaewidencjonować do repozytorium, gdzie mogą być widoczne dla nieautoryzowanych osób. Klucze i inne wartości, których nie należy zaewidencjonować, obejmują:

- LUIS tworzenie i prognozowanie
- LUIS tworzenie i punkty końcowe przewidywania
- Klucze subskrypcji platformy Azure
- Tokeny dostępu, takie jak token dla jednostki usługi platformy Azure używany do uwierzytelniania w [usłudze](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest) Automation

#### <a name="strategies-for-securely-managing-secrets"></a>Strategie bezpiecznego zarządzania kluczami tajnymi

Strategie bezpiecznego zarządzania kluczami tajnymi obejmują:

- Jeśli korzystasz z kontroli wersji Git, możesz przechowywać wpisy tajne środowiska uruchomieniowego w pliku lokalnym i uniemożliwić zaewidencjonować plik przez dodanie wzorca, aby dopasować nazwę pliku do pliku [. gitignore](https://git-scm.com/docs/gitignore)
- W przepływie pracy usługi Automation można bezpiecznie przechowywać wpisy tajne w konfiguracji parametrów oferowanych przez tę technologię automatyzacji. Na przykład jeśli korzystasz z [akcji GitHub](https://github.com/features/actions), możesz bezpiecznie przechowywać wpisy tajne w wpisach [tajnych usługi GitHub](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).

## <a name="branching-and-merging"></a>Rozgałęzianie i scalanie

Rozproszone systemy kontroli wersji, takie jak Git, zapewniają elastyczność w zakresie publikowania, udostępniania, przeglądania i iterowania zmian w kodzie za pomocą gałęzi programistycznych udostępnianych innym osobom. Zastosuj [strategię rozgałęziania git](https://docs.microsoft.com/azure/devops/repos/git/git-branching-guidance) , która jest odpowiednia dla Twojego zespołu.

W zależności od przyjętej strategii rozgałęziania każda z nich jest taka, że członkowie zespołu mogą korzystać z rozwiązania w ramach *gałęzi funkcji* niezależnie od pracy, która przechodzi w innych gałęziach.

Aby obsługiwać niezależną pracę w gałęziach z projektem LUIS:

- **Gałąź główna ma własną aplikację LUIS.** Ta aplikacja reprezentuje bieżący stan rozwiązania dla projektu, a jego bieżąca aktywna wersja powinna zawsze być mapowana na `.lu` Źródło, które znajduje się w gałęzi głównej. Wszystkie aktualizacje `.lu` źródła dla tej aplikacji powinny zostać sprawdzone i przetestowane, aby można było wdrożyć tę aplikację w środowiskach kompilacji, takich jak produkcja w dowolnym momencie. Gdy aktualizacje programu `.lu` są scalone z gałęzią funkcji, należy utworzyć nową wersję w aplikacji Luis i obsłużyć [numer wersji](#versioning).

- **Każda gałąź funkcji musi używać własnego wystąpienia aplikacji Luis**. Deweloperzy pracują z tą aplikacją w gałęzi funkcji bez ryzyka dla deweloperów, którzy pracują w innych gałęziach. Ta aplikacja "dev Branch" jest kopią roboczą, która powinna zostać usunięta po usunięciu gałęzi funkcji.

![Gałąź funkcji usługi git](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>Deweloperzy mogą współpracować z niezależnymi gałęziami

Deweloperzy mogą korzystać z aktualizacji aplikacji LUIS niezależnie od innych gałęzi:

1. Tworzenie gałęzi funkcji z głównej gałęzi (w zależności od strategii gałęzi, zazwyczaj głównego lub programowania).

1. [Utwórz nową aplikację Luis w portalu Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-start-new-app) ("*dev Branch App*"), aby zapewnić obsługę pracy w gałęzi funkcji.

   * Jeśli `.lu` Źródło rozwiązania już istnieje w gałęzi, ponieważ zostało zapisane po zakończeniu pracy w innej gałęzi w projekcie, Utwórz aplikację dev Branch Luis, importując `.lu` plik.

   * Jeśli zaczynasz pracę nad nowym projektem, nie będziesz jeszcze mieć `.lu` źródła dla głównej aplikacji Luis w repozytorium. Ten plik zostanie utworzony `.lu` przez wyeksportowanie aplikacji gałęzi deweloperskiej z portalu po zakończeniu pracy gałęzi funkcji i przesłaniu jej jako części żądania ściągnięcia.

1. Aby zaimplementować wymagane zmiany, Pracuj w aktywnej wersji aplikacji do tworzenia gałęzi deweloperskich. Zalecamy, aby można było używać tylko jednej wersji aplikacji dev Branch dla całej pracy gałęzi funkcji. Jeśli tworzysz więcej niż jedną wersję w aplikacji dla gałęzi deweloperskiej, należy zachować ostrożność, aby śledzić, która wersja zawiera zmiany, które chcesz zaewidencjonować po podniesieniu żądania ściągnięcia.

1. Przetestuj aktualizacje — zobacz [test for Luis DevOps](luis-concept-devops-testing.md) , aby uzyskać szczegółowe informacje na temat testowania aplikacji gałęzi deweloperskiej.

1. Wyeksportuj aktywną wersję aplikacji gałęzi deweloperskiej `.lu` z [listy wersje](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions).

1. Zaewidencjonuj aktualizacje i zaproś równorzędne przeglądy swoich aktualizacji. W przypadku korzystania z usługi GitHub zostanie zgłoszone [żądanie ściągnięcia](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests).

1. Po zatwierdzeniu zmian Scal aktualizacje z gałęzią główną. W tym momencie utworzysz nową [wersję](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions) *głównej* aplikacji Luis przy użyciu zaktualizowanego elementu `.lu` głównego. Zobacz temat [przechowywanie wersji](#versioning) , aby poznać zagadnienia dotyczące ustawiania nazwy wersji.

1. Po usunięciu gałęzi funkcji warto usunąć aplikację dev Branch LUIS utworzoną dla działania oddziału funkcji.

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>Deweloperzy mogą jednocześnie korzystać z wielu gałęzi

W przypadku zastosowania wzorca opisanego powyżej w programie [deweloperzy mogą współpracować z niezależnymi gałęziami](#developers-can-work-from-independent-branches), w każdej gałęzi funkcji będzie używana unikatowa aplikacja Luis. Pojedynczy programista może pracować na wielu gałęziach współbieżnie, o ile przełączają się do odpowiedniej aplikacji dev Branch LUIS dla gałęzi, w której obecnie pracujemy.

Zalecamy użycie tej samej nazwy dla gałęzi funkcji i dla aplikacji dev Branch LUIS, która została utworzona dla działania oddziału funkcji, aby zmniejszyć ryzyko przypadkowej pracy w niewłaściwej aplikacji.

Jak wspomniano powyżej, firma Microsoft zaleca, aby dla uproszczenia działała jedna wersja w każdej aplikacji gałęzi deweloperskiej. Jeśli używasz wielu wersji, weź pod uwagę aktywację odpowiedniej wersji podczas przełączania między aplikacjami tworzenia gałęzi deweloperskich.

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>Wielu deweloperów może współdziałać w tej samej gałęzi współbieżnie

Można obsługiwać wielu deweloperów pracujących nad tą samą gałęzią funkcji w tym samym czasie:

- Deweloperzy ewidencjonują tę samą gałąź funkcji i przechodzą i ściągają zmiany, które zostały przesłane przez siebie i innych deweloperów, gdy pracują tak, jak zwykle.

- W przypadku zastosowania wzorca opisanego powyżej w programie [deweloperzy mogą współpracować z niezależnymi gałęziami](#developers-can-work-from-independent-branches), ta gałąź będzie używać unikatowej aplikacji Luis do obsługi programowania. Ta aplikacja LUIS "dev Branch" zostanie utworzona przez pierwszego członka zespołu projektowego, który zaczyna działać w gałęzi funkcji.

- [Dodaj członków zespołu jako współautorów](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate) do aplikacji dev Branch Luis.

- Po zakończeniu pracy gałęzi funkcji należy wyeksportować aktywną wersję aplikacji LUIS dla gałęzi dev `.lu` z [listy wersje](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions), zapisać zaktualizowany `.lu` plik w repozytorium i zaewidencjonować zmiany.

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>Dołączanie zmian z jednej gałęzi do innej za pomocą zmiany bazy lub scalenia

Niektórzy deweloperzy pracujący w innym rozgałęzieniu mogą zaktualizować `.lu` Źródło i scalić je z gałęzią główną po utworzeniu gałęzi funkcji. Przed wprowadzeniem zmian w gałęzi funkcji warto wprowadzić zmiany do wersji roboczej. Można to zrobić przez zmianę [bazy lub scalenie z wzorcem](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) w taki sam sposób jak każdy inny zasób kodu. Ponieważ aplikacja LUIS w formacie LUDown jest czytelna, obsługuje scalanie za pomocą standardowych narzędzi do scalania.

Postępuj zgodnie z tymi wskazówkami, jeśli tworzysz aplikację LUIS w gałęzi funkcji:

- Przed zmianą bazy lub scalania upewnij się, że lokalna kopia `.lu` źródła aplikacji zawiera wszystkie najnowsze zmiany, które zostały zastosowane przy użyciu portalu Luis, przez ponowne wyeksportowanie aplikacji z portalu. Dzięki temu można upewnić się, że wszelkie zmiany wprowadzone w portalu i jeszcze nie wyeksportowane nie zostaną utracone.

- Podczas scalania należy używać standardowych narzędzi do rozwiązywania konfliktów scalania.

- Nie zapomnij po zakończeniu zmiany bazy lub scalania, aby ponownie zaimportować aplikację z powrotem do portalu, dzięki czemu Pracujesz ze zaktualizowaną aplikacją, gdy będziesz kontynuować stosowanie własnych zmian.

### <a name="merge-prs"></a>Scal żądań ściągnięcia

Po zatwierdzeniu żądania ściągnięcia można scalić zmiany w gałęzi głównej. Żadne specjalne zagadnienia nie dotyczą źródła LUDown aplikacji LUIS: jest ono czytelne i obsługuje scalanie przy użyciu standardowych narzędzi do scalania. Wszelkie konflikty scalania mogą zostać rozwiązane w taki sam sposób jak w przypadku innych plików źródłowych.

Po scaleniu żądania ściągnięcia zaleca się oczyszczenie:

- Usuwanie gałęzi w repozytorium

- Usuń aplikację LUIS "dev Branch" utworzoną dla działania oddziału funkcji.

W taki sam sposób jak w przypadku zasobów kodu aplikacji należy napisać testy jednostkowe, aby towarzyszyły aktualizacje aplikacji LUIS. Aby przetestować, należy zastosować przepływy pracy ciągłej integracji:

- Aktualizacje w żądaniach ściągnięcia przed scaleniem żądania ściągnięcia
- Aplikacja Master Branch LUIS po zatwierdzeniu żądania ściągnięcia, a zmiany zostały scalone z wzorcem.

Aby uzyskać więcej informacji na temat testowania dla LUIS DevOps, zobacz [testowanie dla DevOps dla Luis](luis-concept-devops-testing.md). Aby uzyskać więcej informacji na temat implementowania przepływów pracy, zobacz [przepływy pracy automatyzacji dla Luis DevOps](luis-concept-devops-automation.md).

## <a name="code-reviews"></a>Przeglądy kodu

Aplikacja LUIS w formacie LUDown jest czytelna dla człowieka, która obsługuje komunikację z zmianami w ramach żądania ściągnięcia, które jest odpowiednie do przeglądu. Pliki testów jednostkowych są również zapisywane w formacie LUDown, a także łatwo można je przeglądać w ramach żądania ściągnięcia.

## <a name="versioning"></a>Przechowywanie wersji

Aplikacja składa się z wielu składników, które mogą obejmować takie elementy, jak bot działające w [Azure bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0), [QNA Maker](https://www.qnamaker.ai/), [Azure Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)i innych. Aby osiągnąć cel luźno sprzężonych aplikacji, należy użyć [kontroli wersji](https://docs.microsoft.com/azure/devops/learn/git/what-is-version-control) , tak aby każdy składnik aplikacji miał niezależną wersję, dzięki czemu deweloperzy mogą wykrywać istotne zmiany lub aktualizacje bezpośrednio, sprawdzając numer wersji. Łatwiej jest w wersji aplikacji LUIS niezależnie od innych składników, Jeśli przechowujesz ją we własnym repozytorium.

Aplikacja LUIS dla gałęzi głównej powinna mieć zastosowany schemat przechowywania wersji. Po scaleniu aktualizacji `.lu` aplikacji Luis z serwerem głównym należy zaimportować to zaktualizowane źródło do nowej wersji aplikacji Luis dla gałęzi głównej.

Zaleca się używanie schematu wersji liczbowej dla głównej wersji aplikacji LUIS, na przykład:

`major.minor[.build[.revision]]`

Każda aktualizacja numeru wersji jest zwiększana o ostatnią cyfrę.

Wersja główna/pomocnicza może służyć do wskazywania zakresu zmian funkcji aplikacji LUIS:

* Wersja główna: znacząca zmiana, taka jak obsługa nowego [zamiaru](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent) lub [jednostki](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types)
* Wersja pomocnicza: niewielka zmiana zgodna z poprzednimi wersjami, na przykład po znaczącym nowym szkoleniu
* Kompilacja: nie została zmieniona żadna funkcjonalność, tylko inna kompilacja.

Po ustaleniu numeru wersji dla najnowszej wersji głównej aplikacji LUIS należy skompilować i przetestować nową wersję aplikacji oraz opublikować ją w punkcie końcowym, w którym może być używana w różnych środowiskach kompilacji, takich jak gwarancja jakości lub produkcja. Zdecydowanie zaleca się Automatyzowanie wszystkich tych kroków w przepływie pracy ciągłej integracji (CI).

Zobacz:
- [Przepływy pracy automatyzacji](luis-concept-devops-automation.md) zawierają szczegółowe informacje na temat sposobu implementacji przepływu pracy Ci w celu przetestowania i zwolnienia aplikacji Luis.
- [Release Management](luis-concept-devops-automation.md#release-management) , aby uzyskać informacje na temat wdrażania aplikacji Luis.

### <a name="versioning-the-feature-branch-luis-app"></a>Przechowywanie wersji aplikacji LUIS dla gałęzi funkcji

Podczas pracy z aplikacją LUIS "dev Branch" utworzoną w celu obsługi pracy w gałęzi funkcji, będziesz eksportować aplikację po zakończeniu pracy i będzie zawierać zaktualizowane w ramach żądania ściągnięcia `'lu` . Gałąź w repozytorium i aplikacja LUIS "dev Branch" należy usunąć po scaleniu żądania ściągnięcia z serwerem głównym. Ponieważ ta aplikacja istnieje wyłącznie do obsługi pracy w gałęzi funkcji, nie ma konkretnego schematu przechowywania wersji, który należy zastosować w ramach tej aplikacji.

Gdy zmiany w żądaniu ściągnięcia zostaną scalone z wzorcem, to w przypadku, gdy ma zostać zastosowana wersja, w związku z czym wszystkie aktualizacje w wersji głównej są niezależne.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [testowaniu dla Luis DevOps](luis-concept-devops-testing.md)
* Dowiedz się, jak [zaimplementować DevOps dla Luis przy użyciu usługi GitHub](luis-how-to-devops-with-github.md)
