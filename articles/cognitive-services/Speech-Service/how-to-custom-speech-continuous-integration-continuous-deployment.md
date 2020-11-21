---
title: Ciągłej integracji/ciągłego dostarczania Custom Speech-Speech Service
titleSuffix: Azure Cognitive Services
description: Stosuj DevOps Custom Speech z przepływami pracy ciągłej integracji i ciągłego wdrażania. Zaimplementuj istniejące rozwiązanie DevOps dla własnego projektu.
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: f82ea154d5949f4d229ac76e7a7ce2a89d15ac13
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025671"
---
# <a name="cicd-for-custom-speech"></a>Ciągła integracja/ciągłe wdrażanie dla usługi Custom Speech

Zaimplementuj zautomatyzowane szkolenia, testowanie i zarządzanie wydaniami, aby umożliwić ciągłe ulepszanie modeli Custom Speech podczas stosowania aktualizacji do szkoleń i testowania danych. Dzięki efektywnej implementacji przepływów pracy ciągłej integracji/ciągłego dostępu można upewnić się, że punkt końcowy najlepszego Custom Speech modelu jest zawsze dostępny.

[Ciągła integracja](/azure/devops/learn/what-is-continuous-integration) (ci) to proces inżynieryjny często zatwierdzania aktualizacji w repozytorium udostępnionym i wykonywania zautomatyzowanej kompilacji. Przepływy pracy CI dla Custom Speech przeszkolią nowy model ze źródeł danych i przeprowadź automatyczne testowanie na nowym modelu, aby upewnić się, że działa lepiej niż poprzedni model.

[Ciągłe dostarczanie](/azure/devops/learn/what-is-continuous-delivery) (CD) pobiera modele z procesu ci i tworzy punkt końcowy dla każdego ulepszonego modelu Custom Speech. Dzięki temu punkty końcowe są łatwo dostępne do zintegrowania z rozwiązaniami.

Możliwe są niestandardowe rozwiązania ciągłej integracji/ciągłego wdrażania, ale dla niezawodnego, wstępnie skompilowanego rozwiązania Użyj [repozytorium szablonów DevOps mowy](https://github.com/Azure-Samples/Speech-Service-DevOps-Template), które wykonuje przepływy pracy ciągłej integracji/ciągłego wdrażania za pomocą akcji usługi GitHub.

## <a name="cicd-workflows-for-custom-speech"></a>Przepływy pracy CI/CD dla Custom Speech

Celem tych przepływów pracy jest upewnienie się, że każdy model Custom Speech ma lepszą dokładność rozpoznawania niż poprzednia kompilacja. Jeśli aktualizacje danych testowych i/lub szkoleniowych poprawiją dokładność, te przepływy pracy tworzą nowy punkt końcowy Custom Speech.

Serwery git, takie jak GitHub i Azure DevOps, mogą uruchamiać zautomatyzowane przepływy pracy w przypadku wystąpienia określonych zdarzeń git, takie jak scalenia lub żądania ściągnięcia. Na przykład przepływ pracy CI może być wyzwalany, gdy aktualizacje do testowania danych są wypychane do gałęzi *głównej* . Różne serwery git będą mieć różne narzędzia, ale umożliwią wykonywanie skryptów poleceń interfejsu wiersza polecenia (CLI) na serwerze kompilacji.

W ten sposób przepływy pracy powinny zawierać nazwy i przechowywać dane, testy, pliki testowe, modele i punkty końcowe, aby można je było śledzić z powrotem do zatwierdzenia lub wersji, z których pochodzą. Pomocne jest również nazwy tych zasobów, dzięki czemu można łatwo zobaczyć, które zostały utworzone po uaktualnieniu danych testowych i danych szkoleniowych.

### <a name="ci-workflow-for-testing-data-updates"></a>Przepływ pracy CI na potrzeby testowania aktualizacji danych

Głównym celem przepływów pracy ciągłej integracji/ciągłego wdrażania jest utworzenie nowego modelu przy użyciu danych szkoleniowych i przetestowanie tego modelu przy użyciu danych testowych w celu ustalenia, czy [Współczynnik błędów wyrazów](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (raportowanie) został ulepszony w porównaniu z poprzednim najlepszym modelem ("model testu porównawczego"). Jeśli nowy model działa lepiej, będzie on nowym modelem testu porównawczego, na którym porównywane są przyszłe modele.

Przepływ pracy CI do testowania aktualizacji danych powinien przetestować bieżący model porównawczy przy użyciu zaktualizowanych danych testowych, aby obliczyć zweryfikowane raportowanie błędów systemu Windows. Gwarantuje to, że gdy raportowanie błędów nowego modelu jest porównywane z raportowaniem błędów testów porównawczych, oba modele zostały przetestowane w oparciu o te same dane testowe i są porównywane, podobnie jak na przykład.

Ten przepływ pracy powinien wyzwalać aktualizacje do testowania danych i:

- Przetestuj model testu porównawczego pod kątem zaktualizowanych danych testowych.
- Przechowaj dane wyjściowe testu, które zawierają raportowanie błędów dla modelu porównawczego przy użyciu zaktualizowanych danych.
- Wynikowe raportowanie błędów z tych testów stanie się nowym testowym raportowaniem błędów, które powinny być nabierane przez przyszłe modele.
- Przepływ pracy CD nie jest wykonywany w przypadku aktualizacji do testowania danych.

### <a name="ci-workflow-for-training-data-updates"></a>Przepływ pracy CI na potrzeby aktualizacji danych szkoleniowych

Aktualizacje danych szkoleniowych oznaczają aktualizacje modelu niestandardowego.

Ten przepływ pracy powinien wyzwalać aktualizacje danych szkoleniowych i:

- Uczenie nowego modelu przy użyciu zaktualizowanych danych szkoleniowych.
- Przetestuj nowy model względem danych testowych.
- Przechowuj dane wyjściowe testów, które zawierają raportowanie błędów systemu Windows.
- Porównaj raportowanie błędów systemu Windows z nowego modelu z raportowaniem błędów z modelu porównawczego.
- Jeśli Raportowanie błędów systemu Windows nie poprawi się, Zatrzymaj przepływ pracy.
- Jeśli Raportowanie błędów systemu Windows poprawi, należy wykonać przepływ pracy na dysku CD, aby utworzyć punkt końcowy Custom Speech.

### <a name="cd-workflow"></a>Przepływ pracy CD

Gdy aktualizacja danych szkoleniowych poprawi rozpoznawanie modelu, przepływ pracy CD powinien zostać automatycznie wykonany, aby utworzyć nowy punkt końcowy dla tego modelu i udostępnić ten punkt końcowy, aby można go było używać w rozwiązaniu.

#### <a name="release-management"></a>Release Management

Większość zespołów wymaga ręcznego przeglądu i procesu zatwierdzania wdrożenia w środowisku produkcyjnym. W przypadku wdrożenia produkcyjnego warto upewnić się, że występuje, gdy kluczowe osoby w zespole programistycznym są dostępne do obsługi lub w okresie niskiego ruchu.

### <a name="tools-for-custom-speech-workflows"></a>Narzędzia dla Custom Speech przepływów pracy

Użyj następujących narzędzi dla przepływów pracy usługi CI/CD Automation dla Custom Speech:

- [Interfejs wiersza polecenia platformy Azure](/cli/azure/?view=azure-cli-latest) do tworzenia uwierzytelniania jednostki usługi platformy Azure, wykonywania zapytań dotyczących subskrypcji platformy Azure i zapisywania wyników testów w obiekcie blob platformy Azure.
- [Interfejs wiersza polecenia usługi Azure Speech](spx-overview.md) do współpracy z usługą mowy z poziomu wiersza poleceń lub zautomatyzowanego przepływu pracy.

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>DevOps rozwiązanie do Custom Speech przy użyciu akcji GitHub

Aby uzyskać już zaimplementowane rozwiązanie DevOps dla Custom Speech, przejdź do [repozytorium szablonów DevOps mowy](https://github.com/Azure-Samples/Speech-Service-DevOps-Template). Utwórz kopię szablonu i Rozpocznij tworzenie modeli niestandardowych za pomocą niezawodnego systemu DevOps, który obejmuje testowanie, uczenie i przechowywanie wersji przy użyciu akcji usługi GitHub. Repozytorium zawiera przykładowe testy i dane szkoleniowe, które ułatwiają instalację i objaśniają przepływ pracy. Po początkowej konfiguracji Zastąp przykładowe dane danymi projektu.

[Repozytorium szablonów DevOps mowy](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) zawiera infrastrukturę i szczegółowe wskazówki dotyczące:

- Skopiuj repozytorium szablonów do swojego konta usługi GitHub, a następnie utwórz zasoby platformy Azure i jednostkę [usług](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) dla przepływów pracy Ci/CD akcji usługi GitHub.
- Zapoznaj się z tematem "[Pętla wewnętrzna dev](https://mitchdenny.com/the-inner-loop/)". Aktualizowanie szkoleń i testowania danych z gałęzi funkcji, testowanie zmian przy użyciu tymczasowego modelu programowania i zgłaszanie żądania ściągnięcia w celu zaproponowania i przejrzenia zmian.
- Gdy dane szkoleniowe są aktualizowane w ramach żądania ściągnięcia do *głównego*, nauczenie modeli z przepływem pracy Ci akcji usługi GitHub.
- Wykonaj zautomatyzowane testowanie dokładności, aby określić [Współczynnik błędów wyrazów](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) dla modelu. Przechowaj wyniki testów w obiekcie blob platformy Azure.
- Wykonaj przepływ pracy na dysku CD, aby utworzyć punkt końcowy, gdy raportowanie błędów systemu Windows poprawi.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat DevOps z funkcją mowy:

- Użyj [repozytorium szablonów DevOps mowy](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) , aby zaimplementować DevOps dla Custom Speech z akcjami usługi GitHub.