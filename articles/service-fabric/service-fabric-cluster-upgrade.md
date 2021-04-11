---
title: Uaktualnianie klastrów usługi Azure Service Fabric
description: Informacje na temat opcji aktualizowania klastra usługi Azure Service Fabric
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 636d4cb11f7cc6780d560d3d0043a89c69840a4f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731120"
---
# <a name="upgrading-and-updating-azure-service-fabric-clusters"></a>Uaktualnianie i aktualizowanie klastrów Service Fabric platformy Azure

Klaster usługi Azure Service Fabric to posiadany zasób, ale jest on częściowo zarządzany przez firmę Microsoft. W tym artykule opisano opcje dotyczące czasu i sposobu aktualizowania klastra usługi Azure Service Fabric.

## <a name="automatic-versus-manual-upgrades"></a>Automatyczne uaktualnianie ręczne

Należy upewnić się, że w klastrze Service Fabric zawsze działa [obsługiwana wersja środowiska uruchomieniowego](service-fabric-versions.md). Za każdym razem, gdy firma Microsoft ogłasza wydanie nowej wersji Service Fabric, poprzednią wersję jest oznaczona do *końca wsparcia* po upływie co najmniej 60 dni od tej daty. Nowe wersje są ogłaszane na [blogu zespołu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

Czternaście dni przed wygaśnięciem wydania klastra zostanie wygenerowane zdarzenie kondycji, które powoduje wyświetlenie kondycji klastra w stanie *ostrzegawczym* . Ten klaster pozostaje w stanie ostrzegawczym do momentu uaktualnienia do obsługiwanej wersji środowiska uruchomieniowego.

Można ustawić, aby klaster odbierał automatyczne uaktualnienia Service Fabric, gdy zostaną wydane przez firmę Microsoft, lub można ręcznie wybrać opcję z listy aktualnie obsługiwanych wersji. Te opcje są dostępne w sekcji **uaktualnienia sieci szkieletowej** zasobu klastra Service Fabric.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Wybierz opcję uaktualnienia automatyczne lub ręczne w sekcji &quot;uaktualnienia sieci szkieletowej&quot; zasobu klastra w Azure Portal.":::

Możesz również ustawić tryb uaktualniania klastra i wybrać wersję środowiska uruchomieniowego [przy użyciu szablonu Menedżer zasobów](service-fabric-cluster-upgrade-version-azure.md#resource-manager-template).

Aktualizacje automatyczne są zalecanym trybem uaktualniania, ponieważ ta opcja zapewnia, że klaster jest w stanie obsługiwanym i korzysta z najnowszych poprawek i funkcji, a jednocześnie pozwala na zaplanowanie aktualizacji w sposób, który jest najtańszy do obciążeń przy użyciu strategii [wdrażania Wave](#wave-deployment-for-automatic-upgrades) .

## <a name="wave-deployment-for-automatic-upgrades"></a>Wdrożenie usługi Wave na potrzeby automatycznych uaktualnień

Dzięki wdrożeniu w usłudze Wave można zminimalizować zakłócenia uaktualniania do klastra, wybierając poziom dojrzałości dla uaktualnienia, w zależności od obciążenia. Na przykład można skonfigurować   ->    ->  potok wdrożenia fazy *produkcyjnej* dla różnych klastrów Service Fabric w celu przetestowania zgodności uaktualnienia środowiska uruchomieniowego przed zastosowaniem go do obciążeń produkcyjnych.

Aby zrezygnować z wdrożenia Wave, Określ jedną z następujących wartości Wave dla klastra (w szablonie wdrożenia):

* **Fala 0**: klastry są aktualizowane zaraz po wydaniu nowej kompilacji Service Fabric. Przeznaczone dla klastrów testowych/deweloperskich.
* **Fala 1**: klastry są aktualizowane jeden tydzień (siedem dni) po wydaniu nowej kompilacji. Przeznaczone dla klastrów przedprodukcyjnych/produkcyjnych.
* **Fala 2**: klastry są aktualizowane przez dwa tygodnie (14 dni) po wydaniu nowej kompilacji. Przeznaczone dla klastrów produkcyjnych.

Możesz zarejestrować się w celu otrzymywania powiadomień e-mail z linkami, aby uzyskać pomoc w przypadku niepowodzenia uaktualniania klastra. Aby rozpocząć, zobacz [wdrożenie Wave na potrzeby automatycznych uaktualnień](service-fabric-cluster-upgrade-version-azure.md#wave-deployment-for-automatic-upgrades) .

## <a name="phases-of-automatic-upgrade"></a>Fazy automatycznego uaktualniania

Firma Microsoft obsługuje kod i konfigurację środowiska uruchomieniowego Service Fabric, które działa w klastrze platformy Azure. Przeprowadzamy automatyczne monitorowanie uaktualnień do oprogramowania zgodnie z wymaganiami. Mogą to być kod, konfiguracja lub oba te uaktualnienia. Aby zminimalizować wpływ tych uaktualnień na aplikacje, są one wykonywane w następujących fazach:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Faza 1: uaktualnienie jest wykonywane przy użyciu wszystkich zasad dotyczących kondycji klastra

W tej fazie uaktualnienia są kontynuowane w jednej domenie uaktualnienia, a aplikacje, które działały w klastrze, nadal działają bez żadnych przestojów. Zasady kondycji klastra (w przypadku kondycji węzła i kondycji aplikacji) są przestrzegane podczas uaktualniania.

Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie zostanie wycofane i zostanie wysłana wiadomość e-mail do właściciela subskrypcji. Wiadomość e-mail zawiera następujące informacje:

* Powiadomienie o wycofaniu uaktualnienia klastra.
* Sugerowane akcje naprawcze (jeśli istnieją).
* Liczba dni (*n*) do momentu wykonania fazy 2.

Podjęto próbę wykonania tego samego uaktualnienia na wypadek niepowodzenia uaktualnienia z przyczyn związanych z infrastrukturą. Po *n* dniach od daty wysłania wiadomości e-mail będziemy mogli przejść do fazy 2.

Jeśli zasady kondycji klastra są spełnione, uaktualnienie jest uznawane za pomyślne i oznaczone jako ukończone. Ta sytuacja może wystąpić podczas wstępnego uaktualniania lub dowolnego przebiegu uaktualniania w tej fazie. Nie ma potwierdzenia pomyślnego uruchomienia w wiadomości e-mail, aby uniknąć wysyłania nadmiernych wiadomości e-mail. Otrzymanie wiadomości e-mail wskazuje wyjątek dla normalnych operacji. Oczekujemy, że większość uaktualnień klastra zakończy się pomyślnie bez wpływu na dostępność aplikacji.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Faza 2: uaktualnienie jest przeprowadzane tylko przy użyciu domyślnych zasad dotyczących kondycji

Zasady dotyczące kondycji w tej fazie są ustawiane w taki sposób, że liczba aplikacji, które były w dobrej kondycji na początku uaktualnienia, pozostaje taka sama podczas procesu uaktualniania. Podobnie jak w przypadku fazy 1, uaktualnienia fazy 2 są kontynuowane w jednej domenie uaktualnienia, a aplikacje, które były uruchomione w klastrze, nadal działają bez żadnych przestojów. Zasady kondycji klastra (połączenie kondycji węzła i kondycji wszystkich aplikacji działających w klastrze) są przestrzegane podczas uaktualniania.

Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie jest wycofywane. Następnie do właściciela subskrypcji jest wysyłana wiadomość e-mail. Wiadomość e-mail zawiera następujące informacje:

* Powiadomienie o wycofaniu uaktualnienia klastra.
* Sugerowane akcje naprawcze (jeśli istnieją).
* Liczba dni (n) do momentu wykonania fazy 3.

Podjęto próbę wykonania tego samego uaktualnienia na wypadek niepowodzenia uaktualnienia z przyczyn związanych z infrastrukturą. Wiadomość e-mail z przypomnieniem jest wysyłana kilka dni przed n dni. Po n dniach od daty wysłania wiadomości e-mail przejdziemy do fazy 3. Wiadomości e-mail, które wysyłamy w fazie 2, muszą być podejmowane poważnie i należy podjąć działania naprawcze.

Jeśli zasady kondycji klastra są spełnione, uaktualnienie jest uznawane za pomyślne i oznaczone jako ukończone. Taka sytuacja może wystąpić podczas wstępnego uaktualniania lub dowolnego uruchomienia procesu uaktualniania w tej fazie. Nie ma potwierdzenia pomyślnego uruchomienia wiadomości e-mail.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Faza 3: uaktualnienie jest przeprowadzane przy użyciu agresywnych zasad dotyczących kondycji

Te zasady kondycji w tej fazie są ukierunkowane na zakończenie uaktualniania, a nie na kondycję aplikacji. Niektóre uaktualnienia klastra kończą się w tej fazie. Jeśli klaster przechodzi do tej fazy, istnieje dobry szansa, że aplikacja stanie się w złej kondycji i/lub utraci dostępność.

Podobnie jak w przypadku innych dwóch faz, uaktualnienia fazy 3 przechodzą w jedną domenę uaktualnienia jednocześnie.

Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie jest wycofywane. Podjęto próbę wykonania tego samego uaktualnienia na wypadek niepowodzenia uaktualnienia z przyczyn związanych z infrastrukturą. Następnie klaster jest przypięty, dzięki czemu nie będzie już otrzymywać pomocy technicznej i/lub uaktualnień.

Wiadomość e-mail zawierająca te informacje jest wysyłana do właściciela subskrypcji wraz z działaniami naprawczymi. Nie oczekuje się, że żaden klaster nie przejdzie do stanu, w którym faza 3 nie powiodła się.

Jeśli zasady kondycji klastra są spełnione, uaktualnienie jest uznawane za pomyślne i oznaczone jako ukończone. Taka sytuacja może wystąpić podczas wstępnego uaktualniania lub dowolnego uruchomienia procesu uaktualniania w tej fazie. Nie ma potwierdzenia pomyślnego uruchomienia wiadomości e-mail.

## <a name="custom-policies-for-manual-upgrades"></a>Zasady niestandardowe dla uaktualnień ręcznych

Można określić zasady niestandardowe do ręcznego uaktualniania klastra. Te zasady są stosowane przy każdym wybraniu nowej wersji środowiska uruchomieniowego, która wyzwala system w celu uruchomienia uaktualnienia klastra. Jeśli nie zastąpisz zasad, zostaną użyte wartości domyślne. Aby uzyskać więcej informacji, zobacz [Ustawianie zasad niestandardowych dla uaktualnień ręcznych](service-fabric-cluster-upgrade-version-azure.md#custom-policies-for-manual-upgrades).

## <a name="other-cluster-updates"></a>Inne aktualizacje klastra

Poza uaktualnianiem środowiska uruchomieniowego istnieje kilka innych działań, które mogą być konieczne w celu zapewnienia aktualności klastra, w tym następujących elementów:

### <a name="managing-certificates"></a>Zarządzanie certyfikatami

Service Fabric używa [certyfikatów serwera X. 509](service-fabric-cluster-security.md) , które należy określić podczas tworzenia klastra w celu zabezpieczenia komunikacji między węzłami klastra i uwierzytelniania klientów. Można dodawać, aktualizować i usuwać certyfikaty klastra i klienta w [Azure Portal](https://portal.azure.com) lub przy użyciu interfejsu wiersza polecenia platformy Azure.  Aby dowiedzieć się więcej, przeczytaj artykuł [Dodawanie lub usuwanie certyfikatów](service-fabric-cluster-security-update-certs-azure.md)

### <a name="opening-application-ports"></a>Otwieranie portów aplikacji

Porty aplikacji można zmienić, zmieniając właściwości zasobów Load Balancer skojarzonych z typem węzła. Możesz użyć Azure Portal lub użyć interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, przeczytaj artykuł [otwieranie portów aplikacji dla klastra](create-load-balancer-rule.md).

### <a name="defining-node-properties"></a>Definiowanie właściwości węzła

Czasami warto upewnić się, że pewne obciążenia są uruchamiane tylko na niektórych typach węzłów w klastrze. Na przykład niektóre obciążenia mogą wymagać procesora GPU lub dysków SSD, a inne mogą nie być. Dla każdego z typów węzłów w klastrze można dodać niestandardowe właściwości węzła do węzłów klastra. Ograniczenia umieszczania to instrukcje dołączone do poszczególnych usług, które wybierają jedną lub więcej właściwości węzła. Ograniczenia umieszczania definiują, gdzie mają być uruchamiane usługi.

Aby uzyskać szczegółowe informacje na temat używania ograniczeń umieszczania, właściwości węzła i sposobu ich definiowania, Odczytaj [właściwości węzła i ograniczenia umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

### <a name="adding-capacity-metrics"></a>Dodawanie metryk pojemności

Dla każdego typu węzła można dodać niestandardowe metryki pojemności, które mają być używane w aplikacjach do raportowania obciążenia. Aby uzyskać szczegółowe informacje na temat używania metryk pojemności do raportowania obciążenia, zapoznaj się z klastrem Service Fabric Menedżer zasobów dokumenty dotyczące [opisywania klastra](service-fabric-cluster-resource-manager-cluster-description.md) i [metryk i obciążenia](service-fabric-cluster-resource-manager-metrics.md).

### <a name="customizing-settings-for-your-cluster"></a>Dostosowywanie ustawień klastra

Wiele różnych ustawień konfiguracji można dostosować w klastrze, na przykład na poziomie niezawodności właściwości klastra i węzła. Aby uzyskać więcej informacji, Przeczytaj [Service Fabric ustawienia sieci szkieletowej klastra](service-fabric-cluster-fabric-settings.md).

### <a name="upgrading-os-images-for-cluster-nodes"></a>Uaktualnianie obrazów systemu operacyjnego dla węzłów klastra

Najlepszym rozwiązaniem jest włączenie automatycznych uaktualnień obrazów systemu operacyjnego dla węzłów klastra Service Fabric. Aby to zrobić, istnieje kilka wymagań dotyczących klastra i czynności, które należy wykonać. Inną opcją jest użycie aplikacji aranżacji patch (POA, Service Fabric aplikacji, która automatyzuje stosowanie poprawek systemu operacyjnego w klastrze Service Fabric bez przestoju. Aby dowiedzieć się więcej na temat tych opcji, zobacz [poprawki systemu operacyjnego Windows w klastrze Service Fabric](service-fabric-patch-orchestration-application.md).

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie uaktualnieniami Service Fabric](service-fabric-cluster-upgrade-version-azure.md)
* Dostosuj [Ustawienia klastra Service Fabric](service-fabric-cluster-fabric-settings.md)
* [Skalowanie klastra do i wychodzącego](service-fabric-cluster-scale-in-out.md)
* Informacje o [uaktualnieniach aplikacji](service-fabric-application-upgrade.md)
