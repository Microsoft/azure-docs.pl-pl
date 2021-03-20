---
title: Uaktualnij wersję Service Fabric platformy Azure w klastrze
description: Uaktualnij Service Fabric kod i/lub konfigurację, w której działa klaster Service Fabric, w tym Ustawianie trybu aktualizacji klastra, uaktualnianie certyfikatów, Dodawanie portów aplikacji, wykonywanie poprawek systemu operacyjnego i tak dalej. Czego można oczekiwać w przypadku wykonywania uaktualnień?
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 01fe916f0ee78c8481ac6b17b8f7409b47c852ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90564291"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Uaktualnianie wersji klastra usługi Service Fabric

W przypadku każdego nowoczesnego systemu projektowanie pod kątem wydajności jest kluczem do osiągnięcia długotrwałego sukcesu produktu. Klaster Service Fabric platformy Azure to zasób, którego jesteś członkiem, ale jest częścią zarządzaną przez firmę Microsoft. W tym artykule opisano sposób uaktualniania wersji Service Fabric uruchomionej w klastrze platformy Azure.

Można ustawić, aby klaster odbierał automatyczne uaktualnienia sieci szkieletowej, gdy zostaną wydane przez firmę Microsoft lub można wybrać obsługiwaną wersję sieci szkieletowej, na której ma być włączony klaster.

W tym celu należy ustawić konfigurację klastra "upgrademode" w portalu lub przy użyciu Menedżer zasobów w momencie tworzenia lub w późniejszym klastrze na żywo. 

> [!NOTE]
> Upewnij się, że w klastrze działa zawsze obsługiwana wersja sieci szkieletowej. Po opublikowaniu wydania nowej wersji usługi Service Fabric Poprzednia wersja zostanie oznaczona jako przeznaczona do końca wsparcia po upływie co najmniej 60 dni od tej daty. Nowe wersje są ogłaszane [na blogu zespołu usługi Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric). Nowe wydanie jest dostępne do wyboru. 
> 
> 

14 dni przed wygaśnięciem wydania klastra zostanie wygenerowane zdarzenie kondycji, które powoduje przełączenie klastra w stan kondycji ostrzegawczej. Ten klaster pozostaje w stanie ostrzegawczym do momentu uaktualnienia do obsługiwanej wersji sieci szkieletowej.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Ustaw tryb uaktualniania w Azure Portal
Podczas tworzenia klastra można ustawić automatyczny lub ręczny klaster.

![Zrzut ekranu przedstawia okienko Utwórz klaster Service Fabric z wybraną opcją 2 Konfiguracja klastra, a okienko Konfiguracja klastra jest otwarte.][Create_Manualmode]

Klaster można ustawić na automatyczny lub ręczny w przypadku klastra na żywo przy użyciu środowiska zarządzania. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Uaktualnianie do nowej wersji w klastrze, który jest ustawiony na tryb ręczny za pośrednictwem portalu.
Aby uaktualnić do nowej wersji, wystarczy wybrać dostępną wersję z listy rozwijanej i zapisać. Uaktualnianie sieci szkieletowej zostanie rozpoczęte automatycznie. Zasady kondycji klastra (połączenie kondycji węzła i kondycji wszystkich aplikacji działających w klastrze) są przestrzegane podczas uaktualniania.

Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie jest wycofywane. Przewiń w dół ten dokument, aby dowiedzieć się więcej na temat ustawiania tych niestandardowych zasad kondycji. 

Po rozwiązaniu problemów, które spowodowały wycofanie, należy ponownie zainicjować uaktualnienie, wykonując te same kroki jak wcześniej.

![Zrzut ekranu przedstawia okno klastry Service Fabric z otwartym okienkiem uaktualniania sieci szkieletowej i z wyróżnionymi opcjami uaktualniania, w tym automatycznym i ręcznym.][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Ustawianie trybu uaktualniania przy użyciu szablonu Menedżer zasobów
Dodaj konfigurację "upgrademode" do definicji zasobu Microsoft. servicefabric/klastrów i ustaw wartość "clusterCodeVersion" na jedną z obsługiwanych wersji sieci szkieletowej, jak pokazano poniżej, a następnie wdróż szablon. Prawidłowe wartości dla "upgrademode" są "ręczne" lub "Automatyczne"

![Zrzut ekranu przedstawia szablon, który jest w postaci zwykłego tekstu wcięcie w celu odzwierciedlenia struktury, a clusterCodeVersion i upgrademode są wyróżnione.][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Uaktualnianie do nowej wersji w klastrze, który jest ustawiony na tryb ręczny za pośrednictwem szablonu Menedżer zasobów.
Jeśli klaster działa w trybie ręcznym, aby przeprowadzić uaktualnienie do nowej wersji, Zmień wartość "clusterCodeVersion" na obsługiwaną wersję i Wdróż ją. Wdrożenie szablonu, rozpoczęcie uaktualniania sieci szkieletowej zostaje automatycznie wyłączone. Zasady kondycji klastra (połączenie kondycji węzła i kondycji wszystkich aplikacji działających w klastrze) są przestrzegane podczas uaktualniania.

Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie jest wycofywane.  

Po rozwiązaniu problemów, które spowodowały wycofanie, należy ponownie zainicjować uaktualnienie, wykonując te same kroki jak wcześniej.

## <a name="set-custom-health-polices-for-upgrades"></a>Ustawianie niestandardowych zasad kondycji dla uaktualnień
Możesz określić niestandardowe zasady kondycji dla uaktualniania sieci szkieletowej. Jeśli ustawiono klaster na potrzeby automatycznych uaktualnień sieci szkieletowej, te zasady zostaną zastosowane do [fazy 1 uaktualnienia automatycznej sieci szkieletowej](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades).
Jeśli ustawiono klaster do ręcznego uaktualniania sieci szkieletowej, te zasady zostaną zastosowane przy każdym wybraniu nowej wersji wyzwalającej system w celu uruchomienia uaktualnienia sieci szkieletowej w klastrze. Jeśli nie zastąpisz zasad, zostaną użyte wartości domyślne.

Możesz określić niestandardowe zasady dotyczące kondycji lub przejrzeć bieżące ustawienia w bloku "Sieć szkieletowa", wybierając zaawansowane ustawienia uaktualnienia. Zapoznaj się z poniższym obrazem. 

![Zarządzanie niestandardowymi zasadami kondycji][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Wyświetl wszystkie dostępne wersje dla wszystkich środowisk dla danej subskrypcji
Uruchom następujące polecenie, a następnie powinny pojawić się dane wyjściowe podobne do tego.

"supportExpiryUtc" informuje o tym, kiedy dana wersja wygaśnie lub wygasła. Najnowsza wersja nie ma poprawnej daty — ma wartość "9999-12-31T23:59:59.9999999", co oznacza, że data wygaśnięcia nie jest jeszcze ustawiona.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

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
