---
title: Popularne scenariusze używania Azure DevTest Labs
description: W tym artykule przedstawiono podstawowe scenariusze używania Azure DevTest Labs i dwóch ścieżek ogólnych do rozpoczęcia korzystania z usługi w organizacji.
ms.topic: article
ms.date: 06/20/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1adb9b009a250942bab7e9b0e3b4b3e52840341c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481599"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Popularne scenariusze używania Azure DevTest Labs
W zależności od potrzeb przedsiębiorstwa DevTest Labs można skonfigurować tak, aby spełniały różne wymagania.  W tym artykule omówiono popularne scenariusze. Każdy scenariusz dotyczy korzyści płynących z używania DevTest Labs i zasobów do wdrożenia tych scenariuszy.  

- Komputery stacjonarne dla deweloperów
- Środowiska testowe
- Sesje szkoleniowe, praktyczne i imprezy rozpoczynają
- Badania w trybie piaskownicy
- Laboratorium na potrzeby zajęć

## <a name="developer-desktops"></a>Komputery stacjonarne dla deweloperów
Deweloperzy często mają różne wymagania dotyczące maszyn deweloperskich w różnych projektach. Dzięki DevTest Labs deweloperzy mogą mieć dostęp do maszyn wirtualnych na żądanie, które są skonfigurowane pod kątem zgodności z najczęściej spotykanymi scenariuszami. DevTest Labs zapewnia następujące korzyści:

- Organizacje mogą udostępniać typowe komputery deweloperskie zapewniające spójność między zespołami.
- Deweloperzy mogą szybko inicjować obsługę swoich maszyn programistycznych na żądanie lub [zażądać istniejącej wstępnie skonfigurowanej maszyny](devtest-lab-add-claimable-vm.md).
- Deweloperzy mogą udostępniać zasoby w sposób samoobsługi bez konieczności stosowania uprawnień na poziomie subskrypcji.
- Administratorzy i mogą [wstępnie definiować topologię sieci](devtest-lab-configure-vnet.md) , a deweloperzy mogą bezpośrednio korzystać z niej w prosty i intuicyjny sposób bez konieczności dostępu specjalnego.
- Deweloperzy mogą łatwo [dostosować](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) swoje komputery deweloperskie zgodnie z potrzebami.
- Administratorzy mogą kontrolować koszty, upewniając się, że:
    - Deweloperzy [nie mogą uzyskać więcej maszyn wirtualnych](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) niż potrzeba do rozwoju
    - [Maszyny wirtualne są zamykane](devtest-lab-set-lab-policy.md#set-auto-shutdown) , gdy nie są używane
    - [Zezwalanie tylko na podzbiór rozmiarów wystąpień maszyn wirtualnych](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) dla określonych laboratoriów
    - [Zarządzanie obiektami docelowymi kosztów i powiadomieniami](devtest-lab-configure-cost-management.md) dla każdego laboratorium.

Aby uzyskać więcej informacji, zobacz [Korzystanie z Azure DevTest Labs dla deweloperów](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Środowiska testowe
Tworzenie środowisk testowych i zarządzanie nimi w całym przedsiębiorstwie może wymagać znacznego nakładu pracy. Za pomocą DevTest Labs środowiska testowe mogą być łatwo tworzone, aktualizowane lub duplikowane. Dzięki temu zespoły mogą uzyskać dostęp do w pełni skonfigurowanego środowiska, gdy jest to konieczne. W tym scenariuszu DevTest Labs zapewnia następujące korzyści:

- Organizacje mogą zapewnić wspólne środowiska testowe zapewniające spójność między zespołami.
- Testerzy mogą testować najnowszą wersję swojej aplikacji przez szybkie Inicjowanie obsługi środowisk systemów Windows i Linux za pomocą szablonów wielokrotnego użytku.
- Administratorzy mogą połączyć laboratorium z usługą Azure DevOps, aby włączyć scenariusze DevOps
- Właściciele laboratorium mogą kontrolować koszty, upewniając się, że:
    - [Maszyny wirtualne w środowiskach są zamykane](devtest-lab-set-lab-policy.md#set-auto-shutdown) , gdy nie są używane
    - [Zezwalanie tylko na podzbiór rozmiarów wystąpień maszyn wirtualnych dla](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) określonych laboratoriów
    - [Zarządzanie obiektami docelowymi kosztów i powiadomieniami](devtest-lab-configure-cost-management.md) dla każdego laboratorium.

Aby uzyskać więcej informacji, zobacz [używanie Azure DevTest Labs dla środowisk testowych i PaaS](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Badania w trybie piaskownicy
Deweloperzy często badają różne technologie i Projektowanie infrastruktury. Domyślnie wszystkie środowiska utworzone za pomocą DevTest Labs są tworzone w ich własnej grupie zasobów. Użytkownik DevTest Labs uzyskuje dostęp tylko do odczytu do tych zasobów. Jednak w przypadku deweloperów, którzy potrzebują większej kontroli, można zaktualizować ustawienia dla całej sieci, aby przyznać [uprawnienia współautora](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) do źródłowego użytkownika DevTest Labs dla każdego tworzonego środowiska.  Za pomocą DevTest Labs deweloperzy mogą automatycznie udzielić uprawnień współautora do środowisk tworzonych w laboratorium.  W tym scenariuszu deweloperzy mogą dodawać i/lub zmieniać zasoby platformy Azure, które są potrzebne w środowisku deweloperskim lub testowym. Strona [Koszt według zasobów](devtest-lab-configure-cost-management.md#view-cost-by-resource) umożliwia właścicielom laboratorium śledzenie kosztów każdego środowiska używanego do badania.

Aby uzyskać więcej informacji, zobacz [ustawianie praw dostępu do grupy zasobów środowiska](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Szkolenia, praktyczne i imprezy rozpoczynają 
Laboratorium w Azure DevTest Labs działa jako doskonały kontener dla działań przejściowych, takich jak warsztaty, praktyczne laboratoria, szkolenia lub imprezy rozpoczynają.  Usługa umożliwia tworzenie laboratorium, w którym można dostarczać szablony niestandardowe, które mogą być używane przez każdego z nich do tworzenia identycznych i izolowanych środowisk na potrzeby szkoleń. W tym scenariuszu DevTest Labs zapewnia następujące korzyści:

- [Zasady](devtest-lab-set-lab-policy.md) zapewniają, że program szkolony pobiera tylko liczbę zasobów, takich jak maszyny wirtualne, których potrzebują.
- Wstępnie skonfigurowane i utworzone maszyny są [zgłaszane](devtest-lab-add-claimable-vm.md) z jedną akcją od powyższego poziomu.
- Laboratoria są współdzielony z programem stażyści, uzyskując dostęp do [adresu URL dla laboratorium](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [Daty wygaśnięcia](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) na maszynach wirtualnych zapewniają, że maszyny są usuwane, gdy nie są już potrzebne.
- Można łatwo [usunąć laboratorium](devtest-lab-delete-lab-vm.md#delete-a-lab) i wszystkie [powiązane zasoby](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) , gdy szkolenie jest przenoszone.

Aby uzyskać więcej informacji, zobacz [używanie Azure DevTest Labs do szkoleń](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Weryfikacja koncepcji a wdrożenie skalowane
Po podjęciu decyzji o eksplorowaniu DevTest Labs istnieją dwie ogólne ścieżki do przodu: Weryfikacja koncepcji a wdrożenie skalowalne.  

**Wdrożenie skalowane** obejmuje tygodnie/miesiące recenzji i planowania z zamiarem wdrożenia laboratoriów DevTest w całym przedsiębiorstwie, które ma setki lub tysiące deweloperów.

**Weryfikacja wdrożenia koncepcji** koncentruje się na skoncentrowanym wysiłku z jednego zespołu do ustanowienia wartości organizacyjnej. Chociaż może to być skłonność do pomyślnego wdrożenia skalowanego, podejście jest częściej częściej niż opcja weryfikacji koncepcji. W związku z tym zaleca się, aby zacząć od pierwszego zespołu, powtarzać te same podejście co dwa do trzech dodatkowych zespołów, a następnie zaplanować wdrożenie skalowalne w oparciu o zdobytą wiedzę. W celu pomyślnego potwierdzenia koncepcji zalecamy wybranie jednego lub dwóch zespołów i zidentyfikowanie ich scenariuszy (środowiska deweloperskie vs test Environment), udokumentowanie ich bieżących przypadków użycia i wdrożenie DevTest Labs.

## <a name="next-steps"></a>Następne kroki
Przeczytaj następujące artykuły:

- [DevTest Labs — pojęcia](devtest-lab-concepts.md)
- [DevTest Labs — często zadawane pytania](devtest-lab-faq.md)

