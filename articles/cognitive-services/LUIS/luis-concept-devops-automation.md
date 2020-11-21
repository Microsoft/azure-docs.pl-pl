---
title: Ciągłe przepływy pracy dla aplikacji LUIS
description: Jak zaimplementować przepływy pracy CI/CD dla DevOps dla Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.openlocfilehash: 370dade1b74634649c9de44864a0fd9f5cac988f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025980"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>Przepływy pracy ciągłej integracji i ciągłego dostarczania dla LUIS DevOps

Inżynierowie oprogramowania, którzy opracowują aplikację Language Understanding (LUIS), mogą stosować rozwiązania DevOps dotyczące [kontroli źródła](luis-concept-devops-sourcecontrol.md), [zautomatyzowanych kompilacji](luis-concept-devops-automation.md), [testów](luis-concept-devops-testing.md)i [zarządzania zleceniami](luis-concept-devops-automation.md#release-management). W tym artykule opisano koncepcje dotyczące implementowania zautomatyzowanych kompilacji dla LUIS.

## <a name="build-automation-workflows-for-luis"></a>Przepływy pracy automatyzacji kompilacji dla LUIS

![Przepływy pracy CI](./media/luis-concept-devops-automation/luis-automation.png)

W systemie zarządzania kodem źródłowym (SCM) skonfiguruj zautomatyzowane potoki kompilacji, aby uruchomić je w następujących zdarzeniach:

1. **Przepływ pracy** żądania ściągnięcia jest wyzwalany, gdy zostanie zgłoszony [wniosek ściągania](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR). Ten przepływ pracy sprawdza poprawność zawartości żądania ściągnięcia *przed* scaleniem aktualizacji z gałęzią główną.
1. **Przepływ pracy** ciągłej integracji/ciągłego uruchamiania wyzwalany podczas wypychania aktualizacji do gałęzi głównej, na przykład podczas scalania zmian z żądania ściągnięcia. Ten przepływ pracy zapewnia jakość wszystkich aktualizacji gałęzi głównej.

**Przepływ pracy** ciągłej integracji/ciągłego łączenia dwóch uzupełniających procesów programistycznych:

* [Ciągła integracja](/azure/devops/learn/what-is-continuous-integration) (ci) to proces inżynieryjny często zatwierdzania kodu w repozytorium udostępnionym i wykonywania zautomatyzowanej kompilacji. Sparowane [z podejściem](luis-concept-devops-testing.md) zautomatyzowanym, Integracja ciągła pozwala nam sprawdzić, czy dla każdej aktualizacji źródło LUDown jest nadal ważne i można je zaimportować do aplikacji Luis, ale również przekazywać grupę testów, które weryfikują przeszkolonej aplikacji, aby rozpoznawać intencje i jednostki wymagane dla danego rozwiązania.

* [Ciągłe dostarczanie](/azure/devops/learn/what-is-continuous-delivery) pozwala jeszcze bardziej szczegółowo wdrożyć aplikację w środowisku, w którym można wykonywać bardziej szczegółowe testy. Dysk CD umożliwia nam wczesne zapoznanie się z przewidzianymi problemami, które wynikają z naszych zmian, jak najszybciej, a także informacje o lukach w pokryciu testów.

Celem ciągłej integracji i ciągłego dostarczania jest upewnienie się, że "Master jest zawsze w drodze". W przypadku aplikacji LUIS oznacza to, że jeśli będziemy musieli, pobrać dowolną wersję z głównej gałęzi aplikacji LUIS i wydać ją w środowisku produkcyjnym.

### <a name="tools-for-building-automation-workflows-for-luis"></a>Narzędzia do tworzenia przepływów pracy automatyzacji dla LUIS

Istnieją różne technologie automatyzacji kompilacji dostępne do tworzenia przepływów pracy automatyzacji kompilacji. Wszystkie z nich wymagają, aby można było wykonać kroki skryptu przy użyciu interfejsu wiersza polecenia (CLI) lub wywołań REST, aby mogły być wykonywane na serwerze kompilacji.

Użyj następujących narzędzi do tworzenia przepływów pracy automatyzacji dla LUIS:

* [Interfejs wiersza polecenia bot Framework Tools Luis](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) do pracy z aplikacjami i wersjami Luis, uczeniem się, testowaniem i publikowaniem w ramach usługi Luis.

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/?view=azure-cli-latest) do wykonywania zapytań dotyczących subskrypcji platformy Azure, pobierania Luis tworzenia i przewidywania kluczy oraz do tworzenia jednostki [usługi](/cli/azure/ad/sp?view=azure-cli-latest) platformy Azure używanej do uwierzytelniania automatyzacji.

* [NLU. Narzędzie DevOps](https://github.com/microsoft/NLU.DevOps) do [testowania aplikacji Luis](luis-concept-devops-testing.md) i analizowania wyników testów.

### <a name="the-pr-workflow"></a>Przepływ pracy żądania ściągnięcia

Jak wspomniano, należy skonfigurować ten przepływ pracy, aby był uruchamiany, gdy deweloper zgłasza żądanie ściągnięcia, aby zaproponować zmiany do scalenia z gałęzi funkcji do gałęzi głównej. Celem jest zweryfikowanie jakości zmian w żądania ściągnięcia, zanim zostaną one scalone z gałęzią główną.

Ten przepływ pracy powinien:

* Utwórz tymczasową aplikację LUIS, importując `.lu` Źródło w ramach żądania ściągnięcia.
* Nauczenie i opublikowanie wersji aplikacji LUIS.
* Uruchom wszystkie [testy jednostkowe](luis-concept-devops-testing.md) względem tego.
* Przekaż przepływ pracy, jeśli wszystkie testy zakończą się niepowodzeniem w przeciwnym razie.
* Wyczyść i Usuń tymczasową aplikację.

Jeśli Menedżer sterowania usługami jest obsługiwany przez menedżera SCM, skonfiguruj reguły ochrony oddziału, aby umożliwić pomyślne zakończenie tego przepływu pracy.

### <a name="the-master-branch-cicd-workflow"></a>Przepływ pracy ciągłej integracji/ciągłego rozgałęzienia głównego

Skonfiguruj ten przepływ pracy do uruchomienia po scaleniu aktualizacji żądania ściągnięcia z gałęzią główną. Celem jest zapewnienie, że pasek jakości dla gałęzi głównej jest wysoki, testując aktualizacje. Jeśli aktualizacje są zgodne z paskiem jakości, ten przepływ pracy wdraża nową wersję aplikacji LUIS w środowisku, w którym można wykonywać bardziej szczegółowe testy.

Ten przepływ pracy powinien:

* Utwórz nową wersję w podstawowej aplikacji LUIS (aplikacja utrzymywana dla gałęzi głównej) przy użyciu zaktualizowanego kodu źródłowego.

* Nauczenie i opublikowanie wersji aplikacji LUIS.

  > [!NOTE]
  > Zgodnie z opisem w temacie [Uruchamianie testów w zautomatyzowanym przepływie pracy kompilacji](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow) należy opublikować testowaną wersję aplikacji Luis, tak aby narzędzia takie jak NLU. DevOps może uzyskać do niego dostęp. LUIS obsługuje tylko dwa nazwane gniazda publikacji, *przygotowanie* i *produkcję* dla aplikacji Luis, ale można również [opublikować wersję bezpośrednio](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) i [wykonać zapytanie według wersji](./luis-migration-api-v3.md#changes-by-slot-name-and-version-name). Używaj bezpośredniej publikacji wersji w przepływach pracy usługi Automation, aby uniknąć ograniczenia do korzystania z nazwanych miejsc publikacji.

* Uruchom wszystkie [testy jednostkowe](luis-concept-devops-testing.md).

* Opcjonalnie Uruchom [testy wsadowe](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing) , aby zmierzyć jakość i dokładność wersji aplikacji Luis i porównać ją z niektórymi liniami bazowymi.

* Jeśli testy zakończą się pomyślnie:
  * Oznacz źródło w repozytorium.
  * Uruchom zadanie ciągłego dostarczania (CD), aby wdrożyć wersję aplikacji LUIS w środowiskach w celu przeprowadzenia dalszych testów.

### <a name="continuous-delivery-cd"></a>Ciągłe dostarczanie (CD)

Zadanie dysku CD w przepływie pracy ciągłej integracji/ciągłego przebiega warunkowo po pomyślnym wykonaniu kompilacji i zautomatyzowanych testów jednostkowych. Jego zadaniem jest automatyczne wdrażanie aplikacji LUIS w środowisku, w którym można wykonywać więcej testów.

Nie ma żadnych zalecanych rozwiązań dotyczących najlepszego wdrożenia aplikacji LUIS i należy zaimplementować proces odpowiedni dla projektu. Repozytorium [szablonów Luis DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) implementuje proste rozwiązanie dla tego programu, które ma na celu [opublikowanie nowej wersji aplikacji Luis](./luis-how-to-publish-app.md) w miejscu publikowania *produkcyjnego* . Jest to bardzo precyzyjne w przypadku prostej konfiguracji. Jeśli jednak potrzebujesz obsługi wielu różnych środowisk produkcyjnych w tym samym czasie, takich jak *programowanie*, *przemieszczanie* i *przeprowadzających*, limit dwóch nazwanych miejsc publikowania dla aplikacji będzie niewystarczający.

Inne opcje wdrażania wersji aplikacji obejmują:

* Pozostaw wersję aplikacji opublikowaną w punkcie końcowym wersji bezpośredniej i zaimplementuj proces konfigurowania podrzędnych środowisk produkcyjnych z punktem końcowym wersji bezpośredniej zgodnie z wymaganiami.
* Przechowuj różne aplikacje LUIS dla każdego środowiska produkcyjnego i pisz kroki automatyzacji, aby zaimportować do `.lu` nowej wersji aplikacji Luis dla docelowego środowiska produkcyjnego, aby przeprowadzić uczenie i opublikować ją.
* Wyeksportuj przetestowaną wersję aplikacji LUIS do [kontenera Docker Luis](./luis-container-howto.md?tabs=v3) i Wdróż kontener Luis do usługi Azure [Container Instances](../../container-instances/index.yml).

## <a name="release-management"></a>Release Management

Ogólnie rzecz biorąc zalecamy przeprowadzenie ciągłego dostarczania tylko w środowiskach nieprodukcyjnych, takich jak programowanie i przemieszczanie. Większość zespołów wymaga ręcznego przeglądu i procesu zatwierdzania wdrożenia w środowisku produkcyjnym. W przypadku wdrożenia produkcyjnego warto upewnić się, że występuje, gdy kluczowe osoby w zespole programistycznym są dostępne do obsługi lub w okresie niskiego ruchu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [zaimplementować DevOps dla Luis przy użyciu usługi GitHub](luis-how-to-devops-with-github.md)
* Dowiedz się, jak napisać [przepływ pracy akcji usługi GitHub za pomocą NLU. DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md)