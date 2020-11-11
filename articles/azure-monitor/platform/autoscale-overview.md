---
title: Skalowanie automatyczne w Microsoft Azure
description: Skalowanie automatyczne w Microsoft Azure
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: bd7c1582cdb4b2b1b72d3f969ad08879d208785f
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505841"
---
# <a name="overview-of-autoscale-in-microsoft-azure"></a>Omówienie automatycznego skalowania w Microsoft Azure
W tym artykule opisano, co Microsoft Azure automatyczne skalowanie, jego zalety oraz jak rozpocząć korzystanie z niego.  

Automatyczne skalowanie Azure Monitor ma zastosowanie tylko do [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/), [API Management usług](../../api-management/api-management-key-concepts.md)i [klastrów Eksplorator danych platformy Azure](/azure/data-explorer/).

> [!NOTE]
> Platforma Azure ma dwie metody skalowania automatycznego. Starsza wersja automatycznego skalowania ma zastosowanie do Virtual Machines (zbiory dostępności). Ta funkcja ma ograniczoną obsługę i zalecamy Migrowanie do zestawów skalowania maszyn wirtualnych w celu szybszego i bardziej niezawodnego wsparcia automatycznego skalowania. W tym artykule znajduje się link dotyczący sposobu korzystania ze starszej technologii.  
>

## <a name="what-is-autoscale"></a>Co to jest automatyczne skalowanie?
Funkcja automatycznego skalowania umożliwia korzystanie z odpowiedniej ilości zasobów, aby obsłużyć obciążenie aplikacji. Pozwala to na dodawanie zasobów w celu zwiększenia obciążenia, a także oszczędność pieniędzy dzięki usunięciu zasobów znajdujących się w stanie bezczynności. Należy określić minimalną i maksymalną liczbę wystąpień do uruchomienia oraz automatyczne dodawanie lub usuwanie maszyn wirtualnych na podstawie zestawu reguł. Posiadanie minimum gwarantuje, że aplikacja zawsze działa nawet w przypadku braku obciążenia. Maksymalne ograniczenie łącznego kosztu jest możliwe. Automatyczne skalowanie między tymi dwoma skrajnymi elementami przy użyciu tworzonych reguł.

 ![Wyjaśniono automatyczne skalowanie. Dodawanie i usuwanie maszyn wirtualnych](./media/autoscale-overview/AutoscaleConcept.png)

Gdy warunki reguły są spełnione, wyzwalane są co najmniej jedno działanie skalowania automatycznego. Możesz dodawać i usuwać maszyny wirtualne lub wykonywać inne akcje. Ten proces przedstawiono na poniższym diagramie koncepcyjnym.  

 ![Diagram przepływu automatycznego skalowania](./media/autoscale-overview/Autoscale_Overview_v4.png)

Poniższe wyjaśnienie dotyczy elementów poprzedniego diagramu.   

## <a name="resource-metrics"></a>Metryki zasobu
Zasoby emitują metryki, te metryki są później przetwarzane przez zasady. Metryki są realizowane za pomocą różnych metod.
Zestawy skalowania maszyn wirtualnych wykorzystują dane telemetryczne z agentów diagnostyki platformy Azure, a usługi telemetryczne dla aplikacji sieci Web i usług w chmurze pochodzą bezpośrednio z infrastruktury platformy Azure. Niektóre często używane Statystyki obejmują użycie procesora CPU, użycie pamięci, liczby wątków, Długość kolejki i użycie dysku. Aby uzyskać listę danych telemetrycznych, których można użyć, zobacz [często używane metryki skalowania automatycznego](autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Metryki niestandardowe
Możesz również wykorzystać własne metryki niestandardowe, które mogą emitować aplikacje. Jeśli skonfigurowano aplikacje do wysyłania metryk do Application Insights można wykorzystać te metryki, aby podejmować decyzje dotyczące tego, czy skalowanie ma być skalowane, czy nie.

## <a name="time"></a>Godzina
Reguły oparte na harmonogramie są oparte na formacie UTC. W przypadku konfigurowania reguł należy odpowiednio ustawić strefę czasową.  

## <a name="rules"></a>Reguły
Na diagramie jest wyświetlana tylko jedna reguła automatycznego skalowania, ale można jej wiele. Możesz tworzyć złożone nakładające się reguły w zależności od potrzeb.  Typy reguł obejmują  

* **Na podstawie metryk** — na przykład wykonaj tę akcję, gdy użycie procesora CPU przekracza 50%.
* **Oparty na czasie** — na przykład Wyzwól element webhook każdy 8:00 w sobotę w danej strefie czasowej.

Reguły oparte na metrykach mierzą obciążenie aplikacji i dodają lub usuwają maszyny wirtualne na podstawie tego obciążenia. Reguły oparte na harmonogramie umożliwiają skalowanie, gdy widoczne są wzorce czasowe w obciążeniu i które mają być skalowane przed możliwym wzrostem lub zmniejszeniem obciążenia.  

## <a name="actions-and-automation"></a>Akcje i Automatyzacja
Reguły mogą wyzwalać jeden lub więcej typów akcji.

* **Skalowanie** maszyn wirtualnych w poziomie lub na zewnątrz
* Wyślij wiadomość **e-mail** do administratorów subskrypcji, współadministratorów i/lub dodatkowego adresu e-mail określonego przez użytkownika
* **Automatyzowanie za pośrednictwem elementów webhook** — wywoływanie elementów webhook, które mogą wyzwolić wiele złożonych akcji wewnątrz lub na zewnątrz platformy Azure. Na platformie Azure można uruchomić Azure Automation element Runbook, funkcję platformy Azure lub aplikację logiki platformy Azure. Przykładowy adres URL innej firmy poza platformą Azure obejmuje usługi, takie jak zapasy czasu i Twilio.

## <a name="autoscale-settings"></a>Ustawienia skalowania automatycznego
Skalowanie automatyczne wykorzystuje następującą terminologię i strukturę.

- **Ustawienie automatycznego skalowania** jest odczytywane przez aparat skalowania automatycznego w celu określenia, czy skalować w górę czy w dół. Zawiera co najmniej jeden profil, informacje o zasobie docelowym i ustawienia powiadomień.

  - **Profil skalowania automatycznego** jest kombinacją:

    - **ustawienie wydajności** , które wskazuje wartości minimalne, maksymalne i domyślne dla liczby wystąpień.
    - **zestaw reguł** , z których każdy obejmuje wyzwalacz (czas lub metrykę) oraz akcję skalowania (w górę lub w dół).
    - **cykl** wskazujący, że funkcja automatycznego skalowania powinna umieścić ten profil w efekcie.

      Można korzystać z wielu profilów, co pozwala na korzystanie z różnych nakładających się wymagań. Można mieć różne profile skalowania automatycznego dla różnych godzin dnia lub dni tygodnia, na przykład.

  - **Ustawienie powiadomienia** określa, jakie powiadomienia powinny wystąpić w przypadku wystąpienia zdarzenia automatycznego skalowania na podstawie spełnienia kryteriów jednego z profilów ustawień skalowania automatycznego. Funkcja automatycznego skalowania może powiadomić jeden lub więcej adresów e-mail lub nawiązywać wywołania do jednego lub większej liczby elementów webhook.


![Ustawienia, profil i struktura reguły automatycznego skalowania na platformie Azure](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

Pełna lista konfigurowalnych pól i opisów jest dostępna w [interfejsie API REST skalowania automatycznego](/rest/api/monitor/autoscalesettings).

Aby zapoznać się z przykładami kodu, zobacz

* [Zaawansowana konfiguracja automatycznego skalowania przy użyciu Menedżer zasobów szablonów dla VM Scale Sets](autoscale-virtual-machine-scale-sets.md)  
* [Interfejs API REST automatycznego skalowania](/rest/api/monitor/autoscalesettings)

## <a name="horizontal-vs-vertical-scaling"></a>Skalowanie w poziomie i w pionie
Skalowanie automatyczne jest skalowane w poziomie, co zwiększa się ("out") lub zmniejsza ("in") w liczbie wystąpień maszyn wirtualnych.  Poziome jest bardziej elastyczne w sytuacji chmury, ponieważ umożliwia uruchamianie potencjalnie tysięcy maszyn wirtualnych do obsługi obciążenia.

Natomiast skalowanie w pionie jest inne. Zachowuje tę samą liczbę maszyn wirtualnych, ale sprawia, że maszyny wirtualne są bardziej wydajne ("w górę") lub mniej ("w dół"). Moc jest mierzona w pamięci, szybkości procesora, ilości miejsca na dysku itp.  Skalowanie w pionie ma więcej ograniczeń. Jest ona zależna od dostępności większego sprzętu, który szybko trafi górny limit i może różnić się w zależności od regionu. Skalowanie w pionie wymaga również, aby maszyna wirtualna została zatrzymana i ponownie uruchomiona.

## <a name="methods-of-access"></a>Metody dostępu
Można skonfigurować Skalowanie automatyczne za pomocą

* [Witryna Azure Portal](autoscale-get-started.md)
* [Program PowerShell](../samples/powershell-samples.md#create-and-manage-autoscale-settings)
* [Międzyplatformowy interfejs wiersza polecenia (CLI)](../samples/cli-samples.md#autoscale)
* [Interfejs API REST usługi Azure Monitor](/rest/api/monitor/autoscalesettings)

## <a name="supported-services-for-autoscale"></a>Obsługiwane usługi skalowania automatycznego
| Usługa | Dokumentacja & schematu |
| --- | --- |
| Web Apps |[Skalowanie Web Apps](autoscale-get-started.md) |
| Cloud Services |[Automatyczne skalowanie usługi w chmurze](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtual Machines: klasyczny |[Skalowanie klasycznych zestawów dostępności maszyn wirtualnych](/archive/blogs/kaevans/autoscaling-azurevirtual-machines) |
| Virtual Machines: zestawy skalowania systemu Windows |[Skalowanie zestawów skalowania maszyn wirtualnych w systemie Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Virtual Machines: zestawy skalowania systemu Linux |[Skalowanie zestawów skalowania maszyn wirtualnych w systemie Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Virtual Machines: przykład systemu Windows |[Zaawansowana konfiguracja automatycznego skalowania przy użyciu Menedżer zasobów szablonów dla VM Scale Sets](autoscale-virtual-machine-scale-sets.md) |
| Azure App Service |[Skalowanie aplikacji w górę w usłudze Azure App Service](../../app-service/manage-scale-up.md)|
| Usługa API Management|[Automatyczne skalowanie wystąpienia usługi Azure API Management](../../api-management/api-management-howto-autoscale.md)
| Klastry usługi Azure Eksplorator danych|[Zarządzaj skalowaniem klastrów Eksplorator danych platformy Azure w celu uwzględnienia zmiany popytu](/azure/data-explorer/manage-cluster-horizontal-scaling)|
| Logic Apps |[Dodawanie pojemności usługi Integration Service Environment (ISE)](../../logic-apps/ise-manage-integration-service-environment.md#add-ise-capacity)|
| Spring Cloud |[Konfigurowanie autoskalowania dla aplikacji mikrousług](../../spring-cloud/spring-cloud-tutorial-setup-autoscale.md)|
| Service Bus |[Automatycznie Aktualizuj jednostki obsługi komunikatów Azure Service Bus przestrzeni nazw](../../service-bus-messaging/automate-update-messaging-units.md)|

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat automatycznego skalowania, użyj wymienionych wcześniej przewodników skalowania automatycznego lub zapoznaj się z następującymi zasobami:

* [Azure Monitor metryki automatycznego skalowania](autoscale-common-metrics.md)
* [Najlepsze rozwiązania dotyczące skalowania automatycznego w usłudze Azure Monitor](autoscale-best-practices.md)
* [Używanie akcji skalowania automatycznego do wysyłania powiadomień o alertach dotyczących wiadomości e-mail i elementów webhook](autoscale-webhook-email.md)
* [Interfejs API REST automatycznego skalowania](/rest/api/monitor/autoscalesettings)
* [Rozwiązywanie problemów Virtual Machine Scale Sets automatyczne skalowanie](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
