---
title: Usługa Azure Defender dla serwerów — zalety i funkcje
description: Dowiedz się więcej o zaletach i funkcjach usługi Azure Defender dla serwerów.
author: memildin
ms.author: memildin
ms.date: 9/23/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b7457e258cf8a9936e9a63ab9eec9edc0b54d3eb
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797738"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Wprowadzenie do usługi Azure Defender dla serwerów

Usługa Azure Defender dla serwerów dodaje wykrywanie zagrożeń i zaawansowane zabezpieczenia dla maszyn z systemami Windows i Linux.

W przypadku systemu Windows usługa Azure Defender integruje się z usługami platformy Azure w celu monitorowania i ochrony maszyn z systemem Windows. Security Center przedstawia alerty i sugestie dotyczące korygowania ze wszystkich tych usług w łatwym do użycia formacie.

W przypadku systemu Linux usługa Azure Defender zbiera rekordy inspekcji z maszyn z systemem Linux przy użyciu **inspekcji**, co jest jednym z najpopularniejszych struktur inspekcji systemu Linux. Inspekcja w jądrze linii głównej. 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Jakie są korzyści z używania usługi Azure Defender dla serwerów?

Możliwości wykrywania zagrożeń i ochrony zapewniane w usłudze Azure Defender dla serwerów obejmują:

- **Zintegrowanej licencji dla programu Microsoft Defender for Endpoint (tylko system Windows)** — usługa Azure Defender dla serwerów zawiera usługę  [Microsoft Defender dla punktu końcowego](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Razem zapewniają kompleksowe możliwości wykrywania i reagowania punktów końcowych (EDR). [Dowiedz się więcej](security-center-wdatp.md).

    Gdy usługa Defender dla punktów końcowych wykryje zagrożenie, wyzwala alert. Alert jest wyświetlany w Security Center. W Security Center można również przestawić do usługi Defender dla konsoli punktu końcowego i przeprowadzić szczegółowe badanie, aby odkryć zakres ataku. Dowiedz się więcej o usłudze Microsoft Defender dla punktu końcowego.

    > [!IMPORTANT]
    > Czujnik usługi **Microsoft Defender dla punktów końcowych** jest automatycznie włączany na serwerach z systemem Windows, które używają Security Center.

- **Skanowanie w celu oceny luk w zabezpieczeniach dla maszyn wirtualnych** — skaner luk w zabezpieczeniach zawarty w Azure Security Center jest obsługiwany przez Qualys. 

    Skaner Qualys jest jednym z wiodących narzędzi do identyfikacji luk w zabezpieczeniach w czasie rzeczywistym w usłudze Azure Virtual Machines. Nie potrzebujesz licencji Qualys, a nawet konta Qualys — wszystko, co jest obsługiwane bezproblemowo w Security Center. [Dowiedz się więcej](deploy-vulnerability-assessment-vm.md).

- **W przypadku maszyn wirtualnych z dostępem "just-in-Time (JIT)" (VM)** dla osób niezagrażających zagrożeniom aktywne komputery z otwartymi portami zarządzania, takimi jak RDP lub SSH. Wszystkie maszyny wirtualne są potencjalnymi obiektami docelowymi ataku. Po pomyślnym naruszeniu maszyny wirtualnej jest ona używana jako punkt wejścia do ataku dalszych zasobów w środowisku.

    Po włączeniu usługi Azure Defender dla serwerów można użyć dostępu just in Time do maszyny wirtualnej w celu zablokowania ruchu przychodzącego do maszyn wirtualnych, co zmniejsza narażenie na ataki, zapewniając łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby. [Dowiedz się więcej](just-in-time-explained.md).

- **Monitorowanie integralności plików (FIM)** — monitorowanie integralności plików (FIM), znane także jako monitorowanie zmian, bada pliki i rejestry systemu operacyjnego, oprogramowania aplikacji i innych użytkowników w celu wprowadzenia zmian, które mogą wskazywać na atak. Metoda porównania służy do określenia, czy bieżący stan pliku różni się od ostatniego skanowania pliku. Możesz użyć tego porównania, aby określić, czy w plikach wprowadzono prawidłowe lub podejrzane modyfikacje.

    Po włączeniu usługi Azure Defender dla serwerów można sprawdzić integralność plików systemu Windows, rejestrów systemu Windows i plików z systemem Linux przy użyciu programu FIM. [Dowiedz się więcej](security-center-file-integrity-monitoring.md).

- Adaptacyjne kontrolki **aplikacji (AAC)** — funkcje adaptacyjnego sterowania aplikacjami to inteligentne i zautomatyzowane rozwiązanie służące do definiowania listy dozwolonych, znanych bezpiecznych aplikacji dla maszyn.

    Po włączeniu i skonfigurowaniu adaptacyjnych kontrolek aplikacji otrzymasz alerty zabezpieczeń w przypadku uruchamiania aplikacji innych niż te, które zostały zdefiniowane jako bezpieczne. [Dowiedz się więcej](security-center-adaptive-application.md).

- **Adaptacyjne ograniczanie sieci (Anh)** — stosowanie sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) do filtrowania ruchu do i z zasobów, usprawnia zabezpieczenia sieci stan. Nadal jednak mogą istnieć sytuacje, w których rzeczywisty ruch przepływający przez sieciowej grupy zabezpieczeń jest podzbiorem zdefiniowanych reguł sieciowej grupy zabezpieczeń. W takich przypadkach dalsze ulepszanie stan zabezpieczeń można osiągnąć przez zaostrzonie reguł sieciowej grupy zabezpieczeń na podstawie rzeczywistych wzorców ruchu.

    Adaptacyjne zwiększanie przepustowości sieci zapewnia rekomendacje w celu dalszej ochrony reguł sieciowej grupy zabezpieczeń. Używa algorytmu uczenia maszynowego, który ma czynniki w rzeczywistym ruchu, znanej zaufanej konfiguracji, analizie zagrożeń i innych wskaźnikach naruszenia, a następnie udostępnia zalecenia zezwalające na ruch tylko z konkretnych krotek IP/portów. [Dowiedz się więcej](security-center-adaptive-network-hardening.md).

- **Ograniczanie funkcjonalności hosta platformy Docker** — Azure Security Center identyfikuje niezarządzane kontenery hostowane na maszynach wirtualnych z systemem Linux IaaS lub innych maszynach systemu Linux z uruchomionymi kontenerami Docker Security Center stale ocenia konfiguracje tych kontenerów. Następnie porównuje je ze wzorcem usługi Docker dla usługi Internet Security (CIS). Security Center obejmuje cały zestaw reguł testu wydajnościowego usługi CIS Docker i generuje alert, jeśli kontenery nie spełniają żadnej z tych kontrolek. [Dowiedz się więcej](harden-docker-hosts.md).

- **Wykrywanie ataków bez plików (tylko system Windows)** — ataki bez plików wprowadzają złośliwe ładunki do pamięci, aby uniknąć wykrywania technik skanowania opartych na dyskach. Ładunek osoby atakującej będzie nadal utrzymywać się w pamięci procesów objętych naruszeniem i wykonuje szeroką gamę złośliwych działań.

  W przypadku wykrywania ataków bez plików, zautomatyzowane metody śledczej pamięci identyfikują narzędzia, techniki i zachowania ataku bez plików. To rozwiązanie okresowo skanuje maszynę w czasie wykonywania i wyodrębnia informacje bezpośrednio z pamięci procesów. Szczegółowe informacje obejmują identyfikację: 

  - Dobrze znane zestawy narzędzi i oprogramowanie do wyszukiwania kryptograficznego 

  - Wykryto kod powłoki, który jest niewielkim fragmentem kodu zwykle używanym jako ładunek w trakcie wykorzystywania luki w zabezpieczeniach oprogramowania.

  - Wstrzykiwano złośliwy plik wykonywalny w pamięci procesu

  Wykrywanie ataków bez plików generuje szczegółowe alerty zabezpieczeń zawierające opisy z dodatkowymi metadanymi procesów, takimi jak aktywność sieciowa. Przyspiesza to klasyfikacja alertu, korelację i czas odpowiedzi podrzędnej. Takie podejście uzupełnia rozwiązania EDR oparte na zdarzeniach i zapewnia zwiększony zakres wykrywania.

  Aby uzyskać szczegółowe informacje o alertach dotyczących wykrywania ataków bez plików, zobacz [tabelę referencyjną alertów](alerts-reference.md#alerts-windows).

- **Alerty poddane inspekcji systemu Linux i integracja z agentem log Analytics (tylko system Linux)** — poddawany Audyt obejmuje podsystem poziomu jądra, który jest odpowiedzialny za wywołania systemu monitorowania. Filtruje je według określonego zestawu reguł i zapisuje komunikaty dla nich w gnieździe. Security Center integruje funkcje z pakietu objętego inspekcją w ramach agenta Log Analytics. Ta Integracja umożliwia zbieranie zdarzeń inspekcji we wszystkich obsługiwanych dystrybucjach systemu Linux bez wymagań wstępnych.

    rekordy poddane inspekcji są zbierane, wzbogacane i agregowane do zdarzeń przy użyciu agenta Log Analytics agenta dla systemu Linux. Security Center ciągle dodaje nową analizę, która używa sygnałów systemu Linux do wykrywania złośliwych zachowań na maszynach w chmurze i lokalnych systemach Linux. Podobnie jak w przypadku funkcji systemu Windows, te analizy obejmują między podejrzanymi procesami, podejrzanych prób logowania, ładowanie modułu jądra i inne działania. Te działania mogą wskazywać, że komputer jest atakowany lub został naruszony.  

    Listę alertów systemu Linux można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-linux).


## <a name="simulating-alerts"></a>Symulowanie alertów

Alerty można symulować przez pobranie jednego z następujących elementy PlayBook:

- Dla systemu Windows: [Azure Security Center element PlayBook: alerty zabezpieczeń](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- Dla systemu Linux: [Azure Security Center element PlayBook: wykrywanie systemu Linux](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf).




## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o usłudze Azure Defender dla serwerów. 

W przypadku pokrewnego materiału zapoznaj się z następującymi artykułami: 

- Czy alert jest generowany przez Security Center, czy odbierany przez Security Center z innego produktu zabezpieczeń, można go wyeksportować. Aby wyeksportować alerty do usługi Azure wskaźnikowej, wszelkich SIEM innych firm lub dowolnego innego narzędzia zewnętrznego, postępuj zgodnie z instrukcjami w temacie [Eksportowanie alertów do Siem](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Włączanie usługi Azure Defender](security-center-pricing.md#enable-azure-defender)