---
title: Uaktualnianie klastra usługi Azure Service Fabric
description: Dowiedz się więcej o uaktualnianiu wersji lub konfiguracji klastra Service Fabric platformy Azure — ustawianiu trybu aktualizacji klastra, uaktualnianiu certyfikatów, dodawaniu portów aplikacji, wykonywaniu poprawek systemu operacyjnego oraz o tym, czego można oczekiwać podczas przeprowadzania uaktualnień.
ms.topic: conceptual
ms.date: 11/12/2018
ms.custom: sfrev
ms.openlocfilehash: d92ac90e0e41d534231bafbe991a05764dbee07d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82789559"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Uaktualnianie i aktualizowanie klastra usługi Service Fabric na platformie Azure

W przypadku każdego nowoczesnego systemu projektowanie pod kątem wydajności jest kluczem do osiągnięcia długotrwałego sukcesu produktu. Klaster Service Fabric platformy Azure to zasób, którego jesteś członkiem, ale jest częścią zarządzaną przez firmę Microsoft. W tym artykule opisano, co jest zarządzane automatycznie i jakie elementy można skonfigurować samodzielnie.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Kontrolowanie wersji sieci szkieletowej działającej w klastrze

Upewnij się, że w klastrze jest zawsze uruchomiona [obsługiwana wersja sieci szkieletowej](service-fabric-versions.md). Za każdym razem, gdy firma Microsoft ogłasza wydanie nowej wersji Service Fabric, poprzednią wersję jest oznaczona do końca wsparcia po upływie co najmniej 60 dni od tej daty. Nowe wersje są ogłaszane na [blogu zespołu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

14 dni przed wygaśnięciem wydania klastra zostanie wygenerowane zdarzenie kondycji, które powoduje przełączenie klastra w stan kondycji ostrzegawczej. Ten klaster pozostaje w stanie ostrzegawczym do momentu uaktualnienia do obsługiwanej wersji sieci szkieletowej.

Można ustawić, aby klaster odbierał automatyczne uaktualnienia sieci szkieletowej, gdy zostaną wydane przez firmę Microsoft lub można wybrać obsługiwaną wersję sieci szkieletowej, na której ma być włączony klaster.  Aby dowiedzieć się więcej, przeczytaj artykuł [Uaktualnij wersję Service Fabric klastra](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Zachowanie podczas uaktualniania sieci szkieletowej podczas automatycznych uaktualnień

Firma Microsoft utrzymuje kod sieci szkieletowej i konfigurację, która działa w klastrze platformy Azure. Przeprowadzamy automatyczne monitorowanie uaktualnień do oprogramowania zgodnie z wymaganiami. Mogą to być kod, konfiguracja lub oba te uaktualnienia. Aby upewnić się, że aplikacja nie ma wpływu ani nie ma minimalnego wpływu z powodu tych uaktualnień, uaktualnienia są wykonywane w następujących fazach:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Faza 1: uaktualnienie jest wykonywane przy użyciu wszystkich zasad dotyczących kondycji klastra

W tej fazie uaktualnienia są kontynuowane w jednej domenie uaktualnienia, a aplikacje, które działały w klastrze, nadal działają bez żadnych przestojów. Zasady kondycji klastra (w przypadku kondycji węzła i kondycji aplikacji) są przestrzegane podczas uaktualniania.

Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie zostanie wycofane i zostanie wysłana wiadomość e-mail do właściciela subskrypcji. Wiadomość e-mail zawiera następujące informacje:

* Powiadomienie o wycofaniu uaktualnienia klastra.
* Sugerowane akcje naprawcze (jeśli istnieją).
* Liczba dni (*n*) do momentu wykonania fazy 2.

Podjęto próbę wykonania tego samego uaktualnienia na wypadek niepowodzenia uaktualnienia z przyczyn związanych z infrastrukturą. Po *n* dniach od daty wysłania wiadomości e-mail przejdziemy do fazy 2.

Jeśli zasady kondycji klastra są spełnione, uaktualnienie jest uznawane za pomyślne i oznaczone jako ukończone. Taka sytuacja może wystąpić podczas wstępnego uaktualniania lub dowolnego uruchomienia procesu uaktualniania w tej fazie. Nie ma potwierdzenia pomyślnego uruchomienia wiadomości e-mail. Ma to na celu uniknięcie wysyłania zbyt wielu wiadomości e-mail; odbiór wiadomości e-mail powinien być traktowany jako wyjątek normalny. Oczekujemy, że większość uaktualnień klastra zakończy się pomyślnie bez wpływu na dostępność aplikacji.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Faza 2: uaktualnienie jest przeprowadzane tylko przy użyciu domyślnych zasad dotyczących kondycji

Zasady dotyczące kondycji w tej fazie są ustawiane w taki sposób, że liczba aplikacji, które były w dobrej kondycji na początku uaktualnienia, pozostaje taka sama dla czasu trwania procesu uaktualniania. Podobnie jak w przypadku fazy 1, uaktualnienia fazy 2 są kontynuowane w jednej domenie uaktualnienia, a aplikacje, które były uruchomione w klastrze, nadal działają bez żadnych przestojów. Zasady kondycji klastra (połączenie kondycji węzła i kondycji wszystkich aplikacji działających w klastrze) są zgodne z czasem trwania uaktualnienia.

Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie jest wycofywane. Następnie do właściciela subskrypcji jest wysyłana wiadomość e-mail. Wiadomość e-mail zawiera następujące informacje:

* Powiadomienie o wycofaniu uaktualnienia klastra.
* Sugerowane akcje naprawcze (jeśli istnieją).
* Liczba dni (n) do momentu wykonania fazy 3.

Podjęto próbę wykonania tego samego uaktualnienia na wypadek niepowodzenia uaktualnienia z przyczyn związanych z infrastrukturą. Wiadomość e-mail z przypomnieniem jest wysyłana kilka dni przed n dni. Po n dniach od daty wysłania wiadomości e-mail przejdziemy do fazy 3. Wiadomości e-mail, które wysyłamy w fazie 2, muszą być podejmowane poważnie i należy podjąć działania naprawcze.

Jeśli zasady kondycji klastra są spełnione, uaktualnienie jest uznawane za pomyślne i oznaczone jako ukończone. Taka sytuacja może wystąpić podczas wstępnego uaktualniania lub dowolnego uruchomienia procesu uaktualniania w tej fazie. Nie ma potwierdzenia pomyślnego uruchomienia wiadomości e-mail.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Faza 3: uaktualnienie jest przeprowadzane przy użyciu agresywnych zasad dotyczących kondycji

Te zasady kondycji w tej fazie są ukierunkowane na zakończenie uaktualniania, a nie na kondycję aplikacji. Niektóre uaktualnienia klastra kończą się w tej fazie. Jeśli klaster przechodzi do tej fazy, istnieje dobry szansa, że aplikacja stanie się zła i/lub utraci dostęp.

Podobnie jak w przypadku innych dwóch faz, uaktualnienia fazy 3 przechodzą w jedną domenę uaktualnienia jednocześnie.

Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie jest wycofywane. Podjęto próbę wykonania tego samego uaktualnienia na wypadek niepowodzenia uaktualnienia z przyczyn związanych z infrastrukturą. Następnie klaster jest przypięty, dzięki czemu nie będzie już otrzymywać pomocy technicznej i/lub uaktualnień.

Wiadomość e-mail zawierająca te informacje jest wysyłana do właściciela subskrypcji wraz z działaniami naprawczymi. Nie oczekuje się, że żaden klaster nie przejdzie do stanu, w którym faza 3 nie powiodła się.

Jeśli zasady kondycji klastra są spełnione, uaktualnienie jest uznawane za pomyślne i oznaczone jako ukończone. Taka sytuacja może wystąpić podczas wstępnego uaktualniania lub dowolnego uruchomienia procesu uaktualniania w tej fazie. Nie ma potwierdzenia pomyślnego uruchomienia wiadomości e-mail.

## <a name="manage-certificates"></a>Zarządzanie certyfikatami

Service Fabric używa [certyfikatów serwera X. 509](service-fabric-cluster-security.md) , które należy określić podczas tworzenia klastra w celu zabezpieczenia komunikacji między węzłami klastra i uwierzytelniania klientów. Można dodawać, aktualizować i usuwać certyfikaty klastra i klienta w [Azure Portal](https://portal.azure.com) lub przy użyciu interfejsu wiersza polecenia platformy Azure.  Aby dowiedzieć się więcej, przeczytaj artykuł [Dodawanie lub usuwanie certyfikatów](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Otwórz porty aplikacji

Porty aplikacji można zmienić, zmieniając właściwości zasobów Load Balancer skojarzonych z typem węzła. Możesz użyć Azure Portal lub użyć interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, przeczytaj artykuł [otwieranie portów aplikacji dla klastra](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Definiowanie właściwości węzła

Czasami warto upewnić się, że pewne obciążenia są uruchamiane tylko na niektórych typach węzłów w klastrze. Na przykład niektóre obciążenia mogą wymagać procesora GPU lub dysków SSD, a inne mogą nie być. Dla każdego z typów węzłów w klastrze można dodać niestandardowe właściwości węzła do węzłów klastra. Ograniczenia umieszczania to instrukcje dołączone do poszczególnych usług, które wybierają jedną lub więcej właściwości węzła. Ograniczenia umieszczania definiują, gdzie mają być uruchamiane usługi.

Aby uzyskać szczegółowe informacje na temat używania ograniczeń umieszczania, właściwości węzła i sposobu ich definiowania, Odczytaj [właściwości węzła i ograniczenia umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Dodawanie metryk pojemności

Dla każdego typu węzła można dodać niestandardowe metryki pojemności, które mają być używane w aplikacjach do raportowania obciążenia. Aby uzyskać szczegółowe informacje na temat używania metryk pojemności do raportowania obciążenia, zapoznaj się z klastrem Service Fabric Menedżer zasobów dokumenty dotyczące [opisywania klastra](service-fabric-cluster-resource-manager-cluster-description.md) i [metryk i obciążenia](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Ustawianie zasad kondycji dla automatycznych uaktualnień

Możesz określić niestandardowe zasady kondycji dla uaktualnienia sieci szkieletowej. Jeśli ustawiono klaster na potrzeby automatycznych uaktualnień sieci szkieletowej, te zasady zostaną zastosowane do fazy 1 uaktualnienia automatycznej sieci szkieletowej.
Jeśli ustawiono klaster do ręcznego uaktualniania sieci szkieletowej, te zasady zostaną zastosowane przy każdym wybraniu nowej wersji wyzwalającej system w celu uruchomienia uaktualnienia sieci szkieletowej w klastrze. Jeśli nie zastąpisz zasad, zostaną użyte wartości domyślne.

Możesz określić niestandardowe zasady dotyczące kondycji lub przejrzeć bieżące ustawienia w bloku "Sieć szkieletowa", wybierając zaawansowane ustawienia uaktualnienia. Zapoznaj się z poniższym obrazem.

![Zarządzanie niestandardowymi zasadami kondycji][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Dostosowywanie ustawień sieci szkieletowej dla klastra

Wiele różnych ustawień konfiguracji można dostosować w klastrze, na przykład na poziomie niezawodności właściwości klastra i węzła. Aby uzyskać więcej informacji, Przeczytaj [Service Fabric ustawienia sieci szkieletowej klastra](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Poprawianie systemu operacyjnego w węzłach klastra

Aplikacja aranżacji patch (POA) jest aplikacją Service Fabric, która automatyzuje stosowanie poprawek systemu operacyjnego w klastrze Service Fabric bez przestojów. [Aplikacja aranżacji poprawek dla systemu Windows](service-fabric-patch-orchestration-application.md) może zostać wdrożona w klastrze w celu zainstalowania poprawek w zorganizowany sposób, dzięki czemu usługi są dostępne przez cały czas.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak dostosować niektóre [Ustawienia sieci szkieletowej klastra usługi Service Fabric](service-fabric-cluster-fabric-settings.md)
* Dowiedz się [, jak skalować klaster w i na zewnątrz](service-fabric-cluster-scale-in-out.md)
* Informacje o [uaktualnieniach aplikacji](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
