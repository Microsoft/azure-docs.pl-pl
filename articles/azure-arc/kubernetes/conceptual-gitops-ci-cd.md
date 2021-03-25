---
title: Przepływ pracy ciągłej integracji/ciągłego wdrażania za pomocą GitOps — usługa Azure Arc Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: tcare
ms.author: tcare
description: Ten artykuł zawiera omówienie pojęć dotyczących przepływu pracy ciągłej integracji/ciągłego wdrażania przy użyciu GitOps
keywords: GitOps, Kubernetes, K8s, Azure, Helm, ARC, AKS, Azure Kubernetes Service, Containers, CI, CD, Azure DevOps
ms.openlocfilehash: 47633ed5bec1a07c878983d0e93e03149d8967ba
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025870"
---
# <a name="cicd-workflow-using-gitops---azure-arc-enabled-kubernetes"></a>Przepływ pracy ciągłej integracji/ciągłego wdrażania za pomocą GitOps — usługa Azure Arc Kubernetes

Nowoczesne wdrożenia Kubernetes z wieloma aplikacjami, klastrami i środowiskami. Dzięki programowi GitOps można łatwiej zarządzać tymi złożonymi konfiguracjami, śledząc żądany stan środowisk Kubernetes w sposób deklaratywny z usługą git. Korzystając ze wspólnych narzędzi git do śledzenia stanu klastra, można zwiększyć odpowiedzialność, ułatwić badanie błędów i włączyć automatyzację w celu zarządzania środowiskami.

To omówienie koncepcji wyjaśnia GitOps jako rzeczywistość w całym cyklu życia zmiany aplikacji przy użyciu usługi Azure ARC, Azure Repos i Azure Pipelines. [Przejdź do przykładu](#example-workflow) pojedynczej aplikacji zmiana na GitOps środowiska Kubernetes.

## <a name="architecture"></a>Architektura

Rozważ użycie aplikacji wdrożonej w co najmniej jednym środowisku Kubernetes.

![Architektura CI/CD GitOps](./media/gitops-arch.png)

### <a name="application-repo"></a>Repozytorium aplikacji
Repozytorium aplikacji zawiera kod aplikacji, do której deweloperzy pracują w trakcie ich wewnętrznej pętli. Szablony wdrażania aplikacji na żywo w tym repozytorium w postaci generycznej, takiej jak Helm lub Kustomize. Wartości specyficzne dla środowiska nie są przechowywane. Zmiany w tym repozytorium wywołują potok żądania ściągnięcia lub CI, który uruchamia proces wdrożenia.
### <a name="container-registry"></a>Container Registry
Rejestr kontenerów zawiera wszystkie obrazy pierwszej i innych firm używane w środowiskach Kubernetes. Oznacz obrazy aplikacji pierwszej firmy przy użyciu tagów czytelnych dla ludzi i zatwierdzenia git użytego do skompilowania obrazu. Buforuj obrazy innych firm pod kątem bezpieczeństwa, szybkości i odporności. Ustaw plan do przeprowadzenia testów i integracji aktualizacji zabezpieczeń. Aby uzyskać więcej informacji, zobacz artykuł [ACRing and utrzymują publiczną zawartość](../../container-registry/tasks-consume-public-content.md) podręcznika.
### <a name="pr-pipeline"></a>Potok żądania ściągnięcia
Żądań ściągnięcia do repozytorium aplikacji są zależne od pomyślnego uruchomienia potoku żądania ściągnięcia. Ten potok uruchamia podstawowe bramy jakości, takie jak zaznaczanie błędów i testy jednostkowe w kodzie aplikacji. Potok testuje aplikacje i szablony lints wieloetapowe dockerfile i Helm używane do wdrożenia w środowisku Kubernetesu. Obrazy platformy Docker powinny być kompilowane i testowane, ale nie wypychane. Przechowuj czas trwania potoku stosunkowo krótko, aby umożliwić szybką iterację.
### <a name="ci-pipeline"></a>Potok elementu konfiguracji
Potok elementu CI aplikacji uruchamia wszystkie kroki potoku żądania ściągnięcia i rozszerza testy i wdrożenia. Potok może być uruchamiany dla każdego zatwierdzenia lub w regularnych erze z grupą zatwierdzeń. Na tym etapie wykonaj testy aplikacji, które są zbyt długie dla potoku żądania ściągnięcia. Wypychanie obrazów platformy Docker do Container Registry po skompilowaniu przygotowania do wdrożenia. Zastąpiony szablon może być linted z zestawem wartości testowych. Obrazy używane w czasie wykonywania usługi powinny mieć linted, skompilowany i przetestowany w tym momencie. W kompilacji elementu konfiguracji, artefakty są publikowane dla kroku CD, aby użyć go do przygotowania do wdrożenia.
### <a name="flux"></a>Flux
Strumień to usługa, która działa w każdym klastrze i jest odpowiedzialna za utrzymanie żądanego stanu. Usługa często sonduje repozytorium GitOps pod kątem zmian w jego klastrze i stosuje je.
### <a name="cd-pipeline"></a>Potok CD
Potok CD jest automatycznie wyzwalany przez pomyślne kompilacje CI. Używa poprzednio opublikowanych szablonów, zastępuje wartości środowiskowe i otwiera żądanie ściągnięcia do repozytorium GitOps, aby zażądać zmiany żądanego stanu co najmniej jednego klastra Kubernetes. Administratorzy klastrów przeglądają stan żądania ściągnięcia i zatwierdzają Scalanie do repozytorium GitOps. Następnie potok czeka na zakończenie żądania ściągnięcia, co umożliwia strumienie pobranie zmiany stanu.
### <a name="gitops-repo"></a>Repozytorium GitOps
Repozytorium GitOps reprezentuje bieżący żądany stan wszystkich środowisk w klastrach. Każda zmiana tego repozytorium jest wybierana przez usługę strumień w każdym klastrze i wdrożona. Żądań ściągnięcia są tworzone z uwzględnieniem zmian w żądanym stanie, zrecenzowanych i scalonych. Te żądań ściągnięcia zawierają zmiany w szablonach wdrożenia i powstających manifestach Kubernetes. Renderowane manifesty niskiego poziomu umożliwiają dokładniejszą inspekcję zmian zwykle niewidocznych na poziomie szablonu.
### <a name="kubernetes-clusters"></a>Klastry Kubernetes
Co najmniej jeden klaster Kubernetes z obsługą usługi Azure Arc obsługuje różne środowiska wymagane przez aplikację. Na przykład pojedynczy klaster może obsłużyć zarówno środowisko deweloperskie, jak i usługi pytania i odpowiedzi za pomocą różnych przestrzeni nazw. Drugi klaster może zapewnić łatwiejsze rozdzielenie środowisk i bardziej precyzyjną kontrolę.
## <a name="example-workflow"></a>Przykładowy przepływ pracy
Jako deweloper aplikacji Alicja:
* Zapisuje kod aplikacji.
* Określa sposób uruchamiania aplikacji w kontenerze platformy Docker.
* Definiuje szablony służące do uruchamiania kontenera i usług zależnych w klastrze Kubernetes.

Chociaż Robert wie, że aplikacja wymaga możliwości uruchamiania w wielu środowiskach, nie wie o konkretnych ustawieniach dla każdego środowiska.

Załóżmy, że Alicja chce wprowadzić zmianę aplikacji, która zmienia obraz platformy Docker używany w szablonie wdrożenia aplikacji.

1. Alicja zmienia szablon wdrożenia, wypchnięcie go do zdalnej gałęzi w repozytorium aplikacji i otwiera żądanie ściągnięcia do przeglądu.
2. Alicja prosi swojego zespołu o sprawdzenie zmiany.
    * Potok żądania ściągnięcia wykonuje weryfikację.
    * Po pomyślnym uruchomieniu potoku zespół zostanie wylogowany, a zmiana zostanie scalona.
3. Potok CI weryfikuje zmianę i pomyślne zakończenie operacji.
    * Zmiana jest bezpieczna do wdrożenia w klastrze, a artefakty są zapisywane w przebiegu potoku CI.
4. Modyfikowanie i wyzwalanie potoku z dysku CD przez Alicja.
    * Potok CD wybiera artefakty przechowywane przez przebiegu potoku CI programu Alicja.
    * Potok CD zastępuje szablony z wartościami specyficznymi dla środowiska i etapami wszelkich zmian w istniejącym stanie klastra w repozytorium GitOps.
    * Potok CD tworzy żądanie ściągnięcia dla repozytorium GitOps z żądanymi zmianami stanu klastra.
5. Zespół Alicja przegląda i zatwierdza swoje żądania ściągnięcia.
    * Zmiana zostanie scalona z gałęzią docelową odpowiadającą środowisku.
6. W ciągu kilku minut strumień zauważy zmianę w repozytorium GitOps i pobiera zmiany Alicja.
    * Ze względu na zmianę obrazu platformy Docker aplikacja wymaga aktualizacji.
    * Strumień stosuje zmiany do klastra.
7. Alicja testuje punkt końcowy aplikacji, aby upewnić się, że wdrożenie zostało pomyślnie zakończone.
   > [!NOTE]
   > W przypadku większej liczby środowisk przeznaczonych do wdrożenia potok CD iteruje przez utworzenie żądania ściągnięcia dla następnego środowiska i powtórzenie kroków 4-7. Proces wiele wymaga dodatkowego zatwierdzenia dla wdrożeń riskier lub środowisk, takich jak zmiana związana z zabezpieczeniami lub środowisko produkcyjne.
8.  Po pomyślnym wdrożeniu wszystkich środowisk zostanie ukończony potok.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat tworzenia połączeń między klastrem a repozytorium git jako [zasobu konfiguracji z włączonym usługą Azure Arc Kubernetes](./conceptual-configurations.md)