---
title: Włącz szczegółowe informacje o maszynie wirtualnej za pomocą Azure Policy
description: Opisuje sposób włączania usługi VM Insights dla wielu maszyn wirtualnych platformy Azure lub zestawów skalowania maszyn wirtualnych przy użyciu Azure Policy.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 51baf009543208fbbfe091238d0215a24761641d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031960"
---
# <a name="enable-vm-insights-by-using-azure-policy"></a>Włącz szczegółowe informacje o maszynie wirtualnej za pomocą Azure Policy
W tym artykule wyjaśniono, jak włączyć usługę VM Insights dla maszyn wirtualnych platformy Azure lub hybrydowej maszyny wirtualnej połączonej z usługą Azure ARC (wersja zapoznawcza) przy użyciu Azure Policy. Azure Policy umożliwia przypisanie definicji zasad instalujących wymaganych agentów na potrzeby usługi VM Insights w środowisku platformy Azure i automatyczne włączenie monitorowania maszyn wirtualnych w miarę tworzenia każdej maszyny wirtualnej. Szczegółowe informacje o maszynie wirtualnej udostępniają funkcję, która umożliwia odnajdywanie i korygowanie niezgodnych maszyn wirtualnych w środowisku. Użyj tej funkcji zamiast bezpośrednio pracować z Azure Policy.

Jeśli nie znasz Azure Policy, zapoznaj się z krótkim wprowadzeniem do [wdrażania Azure monitor na dużą skalę przy użyciu Azure Policy](../deploy-scale.md).

> [!NOTE]
> Aby używać Azure Policy z zestawami skalowania maszyn wirtualnych platformy Azure lub korzystać z Azure Policy bezpośrednio do włączania maszyn wirtualnych platformy Azure, zobacz [wdrażanie Azure monitor na dużą skalę przy użyciu Azure Policy](../deploy-scale.md#vm-insights).

## <a name="prerequisites"></a>Wymagania wstępne
- [Utwórz i skonfiguruj obszar roboczy log Analytics](./vminsights-configure-workspace.md).
- Zobacz [obsługiwane systemy operacyjne](./vminsights-enable-overview.md#supported-operating-systems) , aby upewnić się, że jest obsługiwany system operacyjny maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych. 


## <a name="vm-insights-initiative"></a>Inicjatywa usługi VM Insights
Usługa VM Insights udostępnia wbudowane definicje zasad umożliwiające zainstalowanie agenta Log Analytics i agenta zależności na maszynach wirtualnych platformy Azure. Inicjatywa **włączenia usługi VM Insights** obejmuje każdą z tych definicji zasad. Przypisz tę inicjatywę do grupy zarządzania, subskrypcji lub grupy zasobów, aby automatycznie zainstalować agentów na wszystkich maszynach wirtualnych systemu Windows lub Linux w tym zakresie.

## <a name="open-policy-coverage-feature"></a>Otwórz funkcję pokrycia zasad
Aby uzyskać dostęp do **zakresu zasad usługi VM Insights**, przejdź do obszaru **maszyny wirtualne** w menu **Azure monitor** w Azure Portal. Wybierz **inne opcje** dołączania, a następnie **Włącz** opcję **Włącz używanie zasad**.

[![Karta wprowadzenie Azure Monitor z maszyn wirtualnych](./media/vminsights-enable-policy/get-started-page.png)](./media/vminsights-enable-policy/get-started-page.png#lightbox)

## <a name="create-new-assignment"></a>Utwórz nowe przypisanie
Jeśli nie masz jeszcze przypisania, Utwórz nowe, klikając pozycję **Przypisz zasady**.

[![Utwórz przypisanie](media/vminsights-enable-policy/create-assignment.png)](media/vminsights-enable-policy/create-assignment.png#lightbox)

Jest to ta sama strona, która umożliwia przypisanie inicjatywy w Azure Policy z wyjątkiem tego, że jest on stałe z wybranym zakresem i definicją inicjatywy **enable VM Insights** . Opcjonalnie można zmienić **nazwę przypisania** i dodać **Opis**. Wybierz pozycję **wykluczenia** , jeśli chcesz określić wykluczenie dla zakresu. Na przykład zakres może być grupą zarządzania i można określić subskrypcję w tej grupie zarządzania, która ma zostać wykluczona z przypisania.

[![Przypisz inicjatywę](media/vminsights-enable-policy/assign-initiative.png)](media/vminsights-enable-policy/assign-initiative.png#lightbox)

Na stronie **Parametry** wybierz **obszar roboczy log Analytics** , który ma być używany przez wszystkie maszyny wirtualne w przypisaniu. Aby określić różne obszary robocze dla różnych maszyn wirtualnych, należy utworzyć wiele przypisań, z których każdy ma własny zakres. 

   > [!NOTE]
   > Jeśli obszar roboczy przekracza zakres przypisania, udziel *log Analytics uprawnienia współautora* do identyfikatora podmiotu zabezpieczeń przypisania zasad. Jeśli tego nie zrobisz, może zostać wyświetlony błąd wdrażania, jak `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`

[![Obszary](media/vminsights-enable-policy/assignment-workspace.png)](media/vminsights-enable-policy/assignment-workspace.png#lightbox)

Kliknij przycisk **Przegląd + Utwórz** , aby przejrzeć szczegóły przypisania przed kliknięciem pozycji **Utwórz** , aby je utworzyć. Nie twórz w tym momencie zadania korygowania, ponieważ najprawdopodobniej potrzebujesz wielu zadań korygowania do włączania istniejących maszyn wirtualnych. Zobacz [Koryguj wyniki zgodności](#remediate-compliance-results) poniżej.

## <a name="review-compliance"></a>Przegląd zgodności
Po utworzeniu przypisania można przejrzeć i zarządzać pokryciem w ramach inicjatywy **enable VM Insights** w grupach zarządzania i subskrypcjach. Zostanie wyświetlona liczba maszyn wirtualnych znajdujących się w każdej z grup lub subskrypcji zarządzania oraz ich stan zgodności.

[![Strona zasad zarządzania usługą VM Insights](media/vminsights-enable-policy/manage-policy-page-01.png)](media/vminsights-enable-policy/manage-policy-page-01.png#lightbox)


Poniższa tabela zawiera opis informacji w tym widoku.

| Funkcja | Opis | 
|----------|-------------| 
| **Zakres** | Grupa zarządzania i subskrypcje, do których masz lub odziedziczył dostęp, z możliwością przechodzenia do szczegółów w hierarchii grupy zarządzania.|
| **Role** | Twoja rola w zakresie, która może być czytelnikem, właścicielem lub współautorem. Ta wartość będzie pusta, jeśli masz dostęp do subskrypcji, ale nie do grupy zarządzania, do której należy. Ta rola określa, jakie dane mogą być widoczne i akcje, które można wykonywać w kontekście przypisywania zasad lub inicjatyw (właściciel), edytowania ich lub wyświetlania zgodności. |
| **Łączna liczba maszyn wirtualnych** | Łączna liczba maszyn wirtualnych w tym zakresie niezależnie od ich stanu. W przypadku grupy zarządzania jest to suma wszystkich maszyn wirtualnych zagnieżdżonych w ramach subskrypcji lub podrzędnych grup zarządzania. |
| **Pokrycie przypisania** | Procent maszyn wirtualnych objętych inicjatywą. |
| **Stan przypisania** | **Sukces** — wszystkie maszyny wirtualne w zakresie mają wdrożonych log Analytics i agentów zależności.<br>**Ostrzeżenie** — subskrypcja nie znajduje się w grupie zarządzania.<br>**Nierozpoczęte** — dodano nowe przypisanie.<br>**Blokada** — nie masz wystarczających uprawnień do grupy zarządzania.<br>**Puste** — żadne maszyny wirtualne nie istnieją lub nie są przypisane żadne zasady. |
| **Zgodne maszyny wirtualne** | Liczba zgodnych maszyn wirtualnych, czyli liczba maszyn wirtualnych z zainstalowanym agentem Log Analytics agentem i zależnością. To pole będzie puste, jeśli nie ma żadnych przypisań, w zakresie nie ma żadnych maszyn wirtualnych ani nie ma odpowiednich uprawnień. |
| **Zgodność** | Ogólny numer zgodności to suma różnych zasobów, które są zgodne, podzielone przez sumę wszystkich różnych zasobów. |
| **Stan zgodności** | **Zgodne** — wszystkie maszyny wirtualne na maszynach wirtualnych zakresu mają wdrożonych log Analytics i agentów zależności lub wszystkie nowe maszyny wirtualne w zakresie, które podlegają przypisaniu, nie zostały jeszcze ocenione.<br>**Niezgodne** — istnieją maszyny wirtualne, które zostały ocenione, ale nie są włączone i mogą wymagać skorygowania.<br>**Nierozpoczęte** — dodano nowe przypisanie.<br>**Blokada** — nie masz wystarczających uprawnień do grupy zarządzania.<br>**Puste** — nie są przypisane żadne zasady.  |


Po przypisaniu inicjatywy zakres wybrany w przypisaniu może być zakresem na liście lub podzbiorem. Na przykład mogło zostać utworzone przypisanie dla subskrypcji (zakres zasad), a nie grupy zarządzania (zakres pokrycia). W takim przypadku wartość **pokrycie przypisania** wskazuje maszyny wirtualne w zakresie inicjatywy podzielone przez maszyny wirtualne w zakresie pokrycia. W innym przypadku niektóre maszyny wirtualne, grupy zasobów lub subskrypcje z zakresu zasad mogły zostać wykluczone. Jeśli wartość jest pusta, oznacza to, że zasady lub inicjatywy nie istnieją lub nie masz odpowiedniego uprawnienia. Informacje są dostępne w obszarze **stan przypisania**.


## <a name="remediate-compliance-results"></a>Koryguj wyniki zgodności
Inicjatywa zostanie zastosowana do maszyn wirtualnych w miarę ich tworzenia lub modyfikacji, ale nie zostanie zastosowana do istniejących maszyn wirtualnych. Jeśli przypisanie nie pokazuje zgodności z 100%, Utwórz zadania korygowania do oceny i włączenia istniejących maszyn wirtualnych, wybierz pozycję **Wyświetl zgodność** , wybierając wielokropek (...).

[![Wyświetl zgodność](media/vminsights-enable-policy/view-compliance.png)](media/vminsights-enable-policy/view-compliance.png#lightbox)

Na stronie **zgodność** znajduje się lista przypisań pasujących do określonego filtru oraz tego, czy są one zgodne. Kliknij przypisanie, aby wyświetlić jego szczegóły.

[![Zgodność zasad dla maszyn wirtualnych platformy Azure](./media/vminsights-enable-policy/policy-view-compliance.png)](./media/vminsights-enable-policy/policy-view-compliance.png#lightbox)

Na stronie **zgodność z inicjatywą** znajduje się lista definicji zasad w ramach inicjatywy oraz tego, czy każda z nich jest zgodna.

[![Szczegóły zgodności](media/vminsights-enable-policy/compliance-details.png)](media/vminsights-enable-policy/compliance-details.png#lightbox)

Kliknij definicję zasad, aby wyświetlić jego szczegóły. Scenariusze, które są wyświetlane w definicjach zasad jako niezgodne, obejmują następujące elementy:

* Agent Log Analytics lub Agent zależności nie został wdrożony. Utwórz zadanie korygowania, aby rozwiązać problem.
* Obraz maszyny wirtualnej (OS) nie został zidentyfikowany w definicji zasad. Kryteria zasad wdrażania obejmują tylko maszyny wirtualne wdrożone z dobrze znanych obrazów maszyn wirtualnych platformy Azure. Zapoznaj się z dokumentacją, aby sprawdzić, czy system operacyjny maszyny wirtualnej jest obsługiwany.
* Maszyny wirtualne nie są rejestrowane w określonym Log Analytics obszarze roboczym. Niektóre maszyny wirtualne w zakresie inicjatywy są połączone z obszarem roboczym Log Analytics innym niż ten, który jest określony w przypisaniu zasad.

[![Szczegóły zgodności zasad](media/vminsights-enable-policy/policy-compliance-details.png)](media/vminsights-enable-policy/policy-compliance-details.png#lightbox)

Aby utworzyć zadanie korygowania w celu ograniczenia problemów ze zgodnością, kliknij pozycję **Utwórz zadanie korygowania**. 

[![Nowe zadanie korygowania](media/vminsights-enable-policy/new-remediation-task.png)](media/vminsights-enable-policy/new-remediation-task.png#lightbox)

Kliknij przycisk **Koryguj** , aby utworzyć zadanie korygowania, a następnie **skoryguj** je, aby je uruchomić. Najprawdopodobniej trzeba będzie utworzyć wiele zadań korygowania, po jednym dla każdej definicji zasad. Nie można utworzyć zadania korygowania dla inicjatywy.

[![Zrzut ekranu przedstawia okienko korygowanie zasad dla monitora | Virtual Machines.](media/vminsights-enable-policy/remediation.png)](media/vminsights-enable-policy/remediation.png#lightbox)


Po ukończeniu zadań korygowania maszyny wirtualne powinny być zgodne z agentami zainstalowanymi i włączonymi do wglądu w dane maszyn wirtualnych. 

## <a name="next-steps"></a>Następne kroki

Po włączeniu monitorowania dla maszyn wirtualnych te informacje są dostępne do analizy za pomocą usługi VM Insights. 

- Aby wyświetlić odnalezione zależności aplikacji, zobacz [Wyświetlanie mapy usługi VM Insights](vminsights-maps.md). 
- Aby identyfikować wąskie gardła i ogólne wykorzystanie z wydajnością maszyny wirtualnej, zobacz [Wyświetlanie wydajności maszyny wirtualnej platformy Azure](vminsights-performance.md).
