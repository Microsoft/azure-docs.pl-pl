---
title: Informacje o Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, w jaki sposób DevTest Labs może ułatwić tworzenie i monitorowanie maszyn wirtualnych platformy Azure oraz zarządzanie nimi
ms.topic: article
ms.date: 06/20/2020
ms.openlocfilehash: b7e6493e5fb1c99dfa7962ed464dca3ebc6b0c7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480205"
---
# <a name="about-azure-devtest-labs"></a>Informacje o usłudze Azure DevTest Labs
Azure DevTest Labs umożliwia deweloperom w zespołach wydajne samodzielne zarządzanie maszynami wirtualnymi i zasobami PaaS bez czekania na zatwierdzenia.

DevTest Labs tworzy laboratoria składające się z wstępnie skonfigurowanych baz lub Azure Resource Manager szablonów. Mają one wszystkie niezbędne narzędzia i oprogramowanie, których można użyć do tworzenia środowisk. Środowiska można tworzyć w ciągu kilku minut, w przeciwieństwie do godzin lub dni.

Za pomocą DevTest Labs można testować najnowsze wersje aplikacji, wykonując następujące zadania:

- Szybkie Inicjowanie obsługi środowisk systemów Windows i Linux za pomocą szablonów i artefaktów wielokrotnego użytku.
- Łatwo zintegruj swój potok wdrażania za pomocą usługi DevTest Labs, aby aprowizować środowiska na żądanie.
- Skalowanie w górę testów obciążenia dzięki aprowizacji wielu agentów testowych i tworzeniu wstępnie zainicjowanych środowisk do szkolenia i demonstracji.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Możliwości
DevTest Labs udostępnia następujące możliwości dla deweloperów pracujących z maszynami wirtualnymi:

- Szybko Twórz maszyny wirtualne, wykonując mniej niż pięć prostych kroków.
- Wybierz z nadzorowanej listy baz maszyn wirtualnych skonfigurowanych, zatwierdzonych i autoryzowanych przez lidera zespołu lub centralnie.
- Utwórz maszyny wirtualne na podstawie wstępnie utworzonych obrazów niestandardowych, które mają już zainstalowane oprogramowanie i narzędzia. 
- Twórz maszyny wirtualne na podstawie formuł, które zasadniczo są obrazami niestandardowymi połączonymi z najnowszymi kompilacjami oprogramowania zainstalowanego podczas tworzenia maszyn wirtualnych. 
- Zainstaluj artefakty, które są rozszerzeniami wdrożonymi na maszynach wirtualnych po zainicjowaniu obsługi administracyjnej.
- Ustawianie automatycznego zamykania i automatycznego uruchamiania harmonogramów na maszynach wirtualnych.
- Przejmij wstępnie utworzoną maszynę wirtualną bez przechodzenia przez proces tworzenia.

DevTest Labs udostępnia następujące możliwości dla deweloperów pracujących ze środowiskami PaaS:

- Użyj Menedżer zasobów, aby szybko utworzyć środowiska PaaS, wykonując mniej niż trzy proste kroki.
- Wybierz z nadzorowanej listy szablonów Menedżer zasobów, które są skonfigurowane i autoryzowane przez lidera zespołu lub centralnie.
- Uruchom pustą grupę zasobów (piaskownicę) przy użyciu szablonu Menedżer zasobów, aby poznać platformę Azure w kontekście laboratorium.

DevTest Labs umożliwiają również centralne dział IT do kontrolowania odpadów, optymalizowanie kosztów zasobów i pozostawania w obrębie budżetów przez wykonywanie następujących zadań:  

- Ustawianie automatycznego zamykania i automatycznego uruchamiania harmonogramów na maszynach wirtualnych.
- Ustawianie zasad dla liczby maszyn wirtualnych, które użytkownicy mogą tworzyć.
- Ustawianie zasad dotyczących rozmiarów maszyn wirtualnych i obrazów galerii wybieranych przez użytkowników.
- Śledzenie kosztów i ustawianie elementów docelowych w laboratoriach.
- Otrzymywanie powiadomień o wysokich kosztach przewidywanych dla laboratoriów, dzięki czemu możesz podjąć niezbędne działania.

DevTest Labs oferuje następujące korzyści związane z tworzeniem i konfigurowaniem środowisk w chmurze oraz zarządzaniem nimi.

## <a name="cost-control-and-governance"></a>Kontrola kosztów i zarządzanie
DevTest Labs ułatwiają kontrolę kosztów, umożliwiając wykonywanie następujących zadań:

- [Ustaw zasady dla laboratoriów](devtest-lab-set-lab-policy.md), takie jak liczba maszyn wirtualnych na użytkownika lub na laboratorium. 
- Utwórz [zasady, aby automatycznie zamykać](devtest-lab-set-lab-policy.md) i uruchamiać maszyny wirtualne.
- Śledź koszty maszyn wirtualnych i PaaS zasobów w laboratoriach w celu utrzymania ich w ramach [budżetu](devtest-lab-configure-cost-management.md).
- Pozostań w kontekście swoich laboratoriów, aby nie zwiększać zasobów poza nimi.

## <a name="quickly-get-to-ready-to-test"></a>Szybkie przechodzenie do testowania
DevTest Labs pozwala tworzyć wstępnie udostępnione środowiska z wszystko, czego zespół potrzebuje do tworzenia i testowania aplikacji. Po prostu Zarezerwuj [środowiska](devtest-lab-add-claimable-vm.md) , w których ostatnia dobra kompilacja aplikacji jest zainstalowana i zacznie działać. Można też używać kontenerów do szybszego tworzenia środowiska w ramach produkcji oszczędnej.

## <a name="create-once-use-everywhere"></a>Utwórz raz, używaj wszędzie
Przechwytywanie i udostępnianie szablonów i [artefaktów](add-artifact-repository.md) [środowiska](devtest-lab-create-environment-from-arm.md) PaaS w ramach zespołu lub organizacji — wszystko to w kontroli źródła — w celu łatwego tworzenia środowisk deweloperskich i testowych.

## <a name="worry-free-self-service"></a>Nieodpłatne samoobsługowe
DevTest Labs umożliwiają deweloperom i testerom szybkie i łatwe [Tworzenie IaaS maszyn wirtualnych](devtest-lab-add-vm.md) i [zasobów PaaS](devtest-lab-create-environment-from-arm.md) przy użyciu zestawu wstępnie skonfigurowanych zasobów.

## <a name="use-iaas-and-paas-resources"></a>Korzystanie z zasobów IaaS i PaaS 
Deweloperzy mogą również korzystać z zasobów PaaS, takich jak klastry platformy Azure Service Fabric, funkcja Web Apps Azure App Service i farmy programu SharePoint, przy użyciu szablonów Menedżer zasobów. Aby rozpocząć pracę z PaaS w laboratoriach, użyj szablonów z [repozytorium środowiska publicznego](devtest-lab-configure-use-public-environments.md) lub [Połącz laboratorium z własnym repozytorium git](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). Możesz również śledzić koszty dotyczące tych zasobów, aby zachować dostęp do budżetu.

## <a name="integrate-with-your-existing-toolchain"></a>Integruj z istniejącym łańcucha narzędzi
Aby udostępnić środowiska deweloperskie/testowe bezpośrednio z preferowanego [Narzędzia do ciągłej integracji (ci)](devtest-lab-integrate-ci-cd.md), zintegrowanego środowiska programistycznego (IDE) lub zautomatyzowanego potoku wersji, Użyj wbudowanych dodatków lub interfejsu API. Można również użyć kompleksowego narzędzia wiersza polecenia.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- Aby dowiedzieć się więcej na temat DevTest Labs, zobacz [pojęcia dotyczące DevTest Labs](devtest-lab-concepts.md).
- Aby zapoznać się z instrukcjami krok po kroku, zobacz [Samouczek: Konfigurowanie laboratorium przy użyciu Azure DevTest Labs](tutorial-create-custom-lab.md).