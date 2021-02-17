---
title: Wdróż Azure Monitor na dużą skalę przy użyciu Azure Policy
description: Wdrażaj Azure Monitor funkcje na dużą skalę przy użyciu Azure Policy.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/08/2020
ms.openlocfilehash: f06ed85e362f15e36e030cd11639d9d17348e938
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573617"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>Wdróż Azure Monitor na dużą skalę przy użyciu Azure Policy
Niektóre funkcje Azure Monitor są konfigurowane raz lub ograniczoną liczbę razy, inne muszą być powtórzone dla każdego zasobu, który ma być monitorowany. W tym artykule opisano metody używania Azure Policy do implementowania Azure Monitor na dużą skalę w celu zapewnienia spójnego i dokładnego monitorowania dla wszystkich zasobów platformy Azure.

Na przykład należy utworzyć ustawienie diagnostyczne dla wszystkich istniejących zasobów platformy Azure i dla każdego nowo utworzonego zasobu. Należy także zainstalować agenta i skonfigurować go za każdym razem, gdy tworzysz maszynę wirtualną. Aby wykonać te działania, można użyć metod, takich jak PowerShell lub CLI, ponieważ te metody są dostępne dla wszystkich funkcji Azure Monitor. Korzystając z Azure Policy, można mieć wdrożoną logikę, która automatycznie przeprowadza odpowiednią konfigurację za każdym razem, gdy tworzysz lub modyfikujesz zasób.


## <a name="azure-policy"></a>Azure Policy
Ta sekcja zawiera krótkie wprowadzenie do [Azure Policy](../governance/policy/overview.md) , które umożliwiają ocenę i wymuszanie standardów organizacyjnych w całej subskrypcji platformy Azure lub grupie zarządzania z minimalnym nakładem pracy. Szczegółowe informacje znajdują się w [dokumentacji Azure Policy](../governance/policy/overview.md) .

Za pomocą Azure Policy można określić wymagania dotyczące konfiguracji dla wszystkich utworzonych zasobów i zidentyfikować zasoby, które są niezgodne, blokować tworzenie zasobów lub dodać wymaganą konfigurację. Działa przez przechwycenie wywołań w celu utworzenia nowego zasobu lub zmodyfikowania istniejącego zasobu. Może odpowiedzieć na takie skutki, jak odrzucanie żądania, jeśli nie jest zgodne z właściwościami oczekiwanymi w definicji zasad, oflagowaniu ich pod kątem braku zgodności lub wdrożenie powiązanego zasobu. Możesz skorygować istniejące zasoby za pomocą **deployIfNotExists** lub **zmodyfikować** definicję zasad.

Azure Policy składa się z obiektów w poniższej tabeli. Zobacz [Azure Policy obiektów](../governance/policy/overview.md#azure-policy-objects) , aby uzyskać bardziej szczegółowy opis każdego z nich.

| Element | Opis |
|:---|:---|
| Definicja zasad | Opisuje warunki zgodności zasobów i efekt, który należy wykonać w przypadku spełnienia warunku. Mogą to być wszystkie zasoby określonego typu lub tylko zasoby, które pasują do określonych właściwości. Efektem może być po prostu Oflagowanie zasobu pod kątem zgodności lub wdrożenie powiązanego zasobu. Definicje zasad są zapisywane przy użyciu notacji JSON zgodnie z opisem w [Azure Policy strukturze definicji](../governance/policy/concepts/definition-structure.md). Efekty zostały opisane w artykule [objaśnienie Azure Policy efektów](../governance/policy/concepts/effects.md).
| Inicjatywa zasad | Grupa definicji zasad, które powinny być stosowane razem. Na przykład można mieć jedną definicję zasad do wysyłania dzienników zasobów do obszaru roboczego Log Analytics i innego do wysyłania dzienników zasobów do centrów zdarzeń. Utwórz inicjatywę obejmującą zarówno definicje zasad, jak i Zastosuj inicjatywę do zasobów, a nie poszczególnych definicji zasad. Inicjatywy są zapisywane przy użyciu formatu JSON zgodnie z opisem w [Azure Policy strukturze inicjatywy](../governance/policy/concepts/initiative-definition-structure.md). |
| Przypisanie | Definicja zasad lub inicjatywa nie zacznie obowiązywać, dopóki nie zostanie przypisana do zakresu. Na przykład Przypisz zasady do grupy zasobów, aby zastosować je do wszystkich zasobów utworzonych w tym zasobie, lub zastosować je do subskrypcji, aby zastosować je do wszystkich zasobów w tej subskrypcji.  Aby uzyskać więcej informacji, zobacz [Azure Policy struktury przypisywania](../governance/policy/concepts/assignment-structure.md). |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>Wbudowane definicje zasad dla Azure Monitor
Azure Policy zawiera kilka wstępnie skompilowanych definicji związanych z Azure Monitor. Te definicje zasad można przypisać do istniejącej subskrypcji lub użyć ich jako podstawy do utworzenia własnych definicji niestandardowych. Aby zapoznać się z pełną listą wbudowanych polityka w kategorii **monitorowanie** , zobacz [Azure Policy wbudowane definicje zasad dla Azure monitor](.//policy-reference.md).

Aby wyświetlić wbudowane definicje zasad związane z monitorowaniem, wykonaj następujące czynności:

1. Przejdź do **Azure Policy** w Azure Portal.
2. Wybierz pozycję **definicje**.
3. W **polu Typ** wybierz pozycję *wbudowane* i dla **kategorii** wybierz pozycję *monitorowanie*.

  ![Zrzut ekranu strony definicje Azure Policy w Azure Portal przedstawiający listę definicji zasad dla kategorii monitorowania i typu wbudowanego.](media/deploy-scale/builtin-policies.png)


## <a name="diagnostic-settings"></a>Ustawienia diagnostyczne
[Ustawienia diagnostyczne](essentials/diagnostic-settings.md) umożliwiają zbieranie dzienników zasobów i metryk z zasobów platformy Azure do wielu lokalizacji, zwykle do obszaru roboczego log Analytics, który umożliwia analizowanie danych za pomocą [zapytań dziennika](logs/log-query-overview.md) i [alertów dziennika](alerts/alerts-log.md). Użyj zasad, aby automatycznie utworzyć ustawienia diagnostyczne za każdym razem, gdy tworzysz zasób.

Każdy typ zasobu platformy Azure ma unikatowy zestaw kategorii, które muszą być wymienione w ustawieniu diagnostyki. Z tego względu każdy typ zasobu wymaga oddzielnej definicji zasad. Niektóre typy zasobów mają wbudowane definicje zasad, które można przypisać bez modyfikacji. W przypadku innych typów zasobów należy utworzyć niestandardową definicję.

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Wbudowane definicje zasad dla Azure Monitor
Istnieją dwie wbudowane definicje zasad dla każdego typu zasobu, jeden do wysłania do Log Analytics obszaru roboczego i innego do centrum zdarzeń. Jeśli potrzebujesz tylko jednej lokalizacji, przypisz te zasady dla tego typu zasobu. W razie potrzeby należy przypisać obie definicje zasad dla zasobu.

Na przykład na poniższej ilustracji przedstawiono wbudowane definicje zasad ustawień diagnostycznych dla Data Lake Analytics.

  ![Zrzut ekranu ze strony definicji Azure Policy, w którym są wyświetlane dwie wbudowane definicje zasad ustawień diagnostycznych dla Data Lake Analytics.](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>Niestandardowe definicje zasad
W przypadku typów zasobów, które nie mają wbudowanych zasad, należy utworzyć niestandardową definicję zasad. Można to zrobić ręcznie w Azure Portal przez skopiowanie istniejących wbudowanych zasad, a następnie zmodyfikowanie dla typu zasobu. Tworzenie zasad programowo przy użyciu skryptu w Galeria programu PowerShell jest bardziej wydajne.

Skrypt [Create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) tworzy pliki zasad dla określonego typu zasobu, który można zainstalować przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Aby utworzyć niestandardową definicję zasad dla ustawień diagnostycznych, należy wykonać czynności opisane w poniższej procedurze.


1. Upewnij się, że zainstalowano [Azure PowerShell](/powershell/azure/install-az-ps) .
2. Zainstaluj skrypt za pomocą następującego polecenia:
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. Uruchom skrypt przy użyciu parametrów, aby określić, gdzie mają być wysyłane dzienniki. Zostanie wyświetlony monit o określenie typu subskrypcji i zasobu. Na przykład, aby utworzyć definicję zasad, która wysyła do Log Analytics obszaru roboczego i centrum zdarzeń, użyj następującego polecenia.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. Alternatywnie możesz określić subskrypcję i typ zasobu w poleceniu. Aby na przykład utworzyć definicję zasad, która wysyła do Log Analytics obszaru roboczego i centrum zdarzeń dla baz danych Azure SQL Server, użyj następującego polecenia.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. Skrypt tworzy oddzielne foldery dla każdej definicji zasad, z których każda zawiera trzy pliki o nazwie azurepolicy.json, azurepolicy.rules.json, azurepolicy.parameters.json. Jeśli chcesz ręcznie utworzyć zasady w Azure Portal, możesz skopiować i wkleić zawartość azurepolicy.js, ponieważ zawiera ona całą definicję zasad. Aby utworzyć definicję zasad z poziomu wiersza polecenia, należy użyć dwóch innych plików z programem PowerShell lub CLI.

    W poniższych przykładach pokazano, jak zainstalować definicję zasad z programu PowerShell i interfejsu wiersza polecenia. Każda z nich zawiera metadane, aby określić kategorię **monitorowania** w celu grupowania nowej definicji zasad z wbudowanymi definicjami zasad.

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>Podjęł
Zamiast tworzyć przypisanie dla każdej definicji zasad, typową strategią jest utworzenie inicjatywy zawierającej definicje zasad w celu utworzenia ustawień diagnostycznych dla każdej usługi platformy Azure. Utwórz przypisanie między inicjatywą a grupą zarządzania, subskrypcją lub grupą zasobów w zależności od sposobu zarządzania środowiskiem. Ta strategia oferuje następujące korzyści:

- Utwórz jedno przypisanie dla inicjatywy zamiast wielu przypisań dla każdego typu zasobu. Użyj tej samej inicjatywy dla wielu grup monitorowania, subskrypcji lub grup zasobów.
- Zmodyfikuj inicjatywę, gdy musisz dodać nowy typ zasobu lub miejsce docelowe. Na przykład wstępne wymagania mogą być wysyłane tylko do Log Analytics obszaru roboczego, ale później trzeba dodać centrum zdarzeń. Zmodyfikuj inicjatywę zamiast tworzenia nowych przypisań.

Szczegółowe informacje na temat tworzenia inicjatywy można znaleźć w temacie [Tworzenie i przypisywanie definicji inicjatywy](../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition) . Należy wziąć pod uwagę następujące zalecenia:

- Ustaw **kategorię** na **monitorowanie** , aby zgrupować ją z powiązanymi wbudowanymi i niestandardowymi definicjami zasad.
- Zamiast określać szczegóły dotyczące obszaru roboczego Log Analytics i centrum zdarzeń dla definicji zasad zawartych w ramach inicjatywy, użyj typowego parametru inicjatywy. Pozwala to łatwo określić wspólną wartość dla wszystkich definicji zasad i w razie potrzeby zmienić tę wartość.

![Definicja inicjatywy](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>Przypisanie 
Przypisz inicjatywę do grupy zarządzania, subskrypcji lub grupy zasobów platformy Azure w zależności od zakresu zasobów do monitorowania. [Grupa zarządzania](../governance/management-groups/overview.md) jest szczególnie przydatna w przypadku zasad określania zakresu, zwłaszcza jeśli organizacja ma wiele subskrypcji.

![Zrzut ekranu przedstawiający ustawienia karty podstawowe w sekcji Przypisywanie inicjatywy ustawień diagnostycznych do Log Analytics obszaru roboczego w Azure Portal.](media/deploy-scale/initiative-assignment.png)

Korzystając z parametrów inicjatywy, można określić obszar roboczy lub inne szczegóły raz dla wszystkich definicji zasad w ramach inicjatywy. 

![Parametry inicjatywy](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>Korekty
Inicjatywa będzie miała zastosowanie do każdej maszyny wirtualnej, która została utworzona. [Zadanie korygowania](../governance/policy/how-to/remediate-resources.md) wdraża definicje zasad z inicjatywy do istniejących zasobów, dzięki czemu można utworzyć ustawienia diagnostyczne dla wszystkich już utworzonych zasobów. Podczas tworzenia przypisania przy użyciu Azure Portal istnieje możliwość utworzenia zadania korygowania w tym samym czasie. Zobacz [korygowanie niezgodnych zasobów Azure Policy](../governance/policy/how-to/remediate-resources.md) , aby uzyskać szczegółowe informacje dotyczące korygowania.

![Korygowanie inicjatywy](media/deploy-scale/initiative-remediation.png)


## <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych
[Azure monitor dla maszyn wirtualnych](vm/vminsights-overview.md) to podstawowe narzędzie w Azure monitor do monitorowania maszyn wirtualnych. Włączenie Azure Monitor dla maszyn wirtualnych powoduje zainstalowanie zarówno agenta Log Analytics, jak i agenta zależności. Zamiast wykonywać te zadania ręcznie, należy użyć Azure Policy, aby mieć pewność, że każda maszyna wirtualna została skonfigurowana podczas jej tworzenia.

> [!NOTE]
> Azure Monitor dla maszyn wirtualnych obejmuje funkcję o nazwie **pokrycie zasad Azure monitor dla maszyn wirtualnych** , która umożliwia odnajdywanie i korygowanie niezgodnych maszyn wirtualnych w środowisku. Tej funkcji można użyć zamiast pracy bezpośrednio z Azure Policyami dla maszyn wirtualnych platformy Azure oraz hybrydowych maszyn wirtualnych połączonych z usługą Azure Arc. W przypadku zestawów skalowania maszyn wirtualnych platformy Azure należy utworzyć przypisanie przy użyciu Azure Policy.
 

Azure Monitor dla maszyn wirtualnych obejmuje następujące wbudowane inicjatywy, które instalują obu agentów w celu włączenia pełnego monitorowania. 

|Nazwa |Opis |
|:---|:---|
|Włącz Azure Monitor dla maszyn wirtualnych | Instaluje agenta Log Analytics i agenta zależności na maszynach wirtualnych platformy Azure oraz hybrydowych maszyn wirtualnych połączonych z usługą Azure Arc. |
|Włącz Azure Monitor dla zestawów skalowania maszyn wirtualnych | Instaluje agenta Log Analytics i agenta zależności w zestawie skalowania maszyn wirtualnych platformy Azure. |


### <a name="virtual-machines"></a>Maszyny wirtualne
Zamiast tworzyć przydziały dla tych inicjatyw przy użyciu interfejsu Azure Policy, Azure Monitor dla maszyn wirtualnych zawiera funkcję, która umożliwia sprawdzenie liczby maszyn wirtualnych w każdym zakresie w celu ustalenia, czy inicjatywa została zastosowana. Następnie można skonfigurować obszar roboczy i utworzyć wszystkie wymagane przypisania przy użyciu tego interfejsu.

Aby uzyskać szczegółowe informacje o tym procesie, zobacz [włączanie Azure monitor dla maszyn wirtualnych przy użyciu Azure Policy](./vm/vminsights-enable-policy.md).

![Zasady Azure Monitor dla maszyn wirtualnych](media/deploy-scale/vminsights-policy.png)

### <a name="virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych
Aby użyć Azure Policy do włączenia monitorowania dla zestawów skalowania maszyn wirtualnych, przypisz **Azure monitor dla inicjatywy dla zestawów skalowania maszyn wirtualnych** do grupy zarządzania platformy Azure, subskrypcji lub grupy zasobów w zależności od zakresu zasobów do monitorowania. [Grupa zarządzania](../governance/management-groups/overview.md) jest szczególnie przydatna w przypadku zasad określania zakresu, zwłaszcza jeśli organizacja ma wiele subskrypcji.

![Zrzut ekranu strony Przypisywanie inicjatywy w Azure Portal. Definicja inicjatywy została ustawiona tak, aby włączyć Azure Monitor dla zestawów skalowania maszyn wirtualnych.](media/deploy-scale/virtual-machine-scale-set-assign-initiative.png)

Wybierz obszar roboczy, do którego będą wysyłane dane. W tym obszarze roboczym musi być zainstalowane rozwiązanie *VMInsights* , zgodnie z opisem w artykule [Konfigurowanie log Analytics obszaru roboczego dla Azure monitor dla maszyn wirtualnych](vm/vminsights-configure-workspace.md).

![Wybór obszaru roboczego](media/deploy-scale/virtual-machine-scale-set-workspace.png)

Utwórz zadanie korygowania, jeśli masz istniejący zestaw skalowania maszyn wirtualnych, które mają być przypisane do tych zasad.

![Zadanie korygowania](media/deploy-scale/virtual-machine-scale-set-remediation.png)

### <a name="log-analytics-agent"></a>Agent usługi Log Analytics
Możesz mieć scenariusze, w których chcesz zainstalować agenta Log Analytics, ale nie agenta zależności. Nie ma żadnej wbudowanej inicjatywy tylko dla agenta, ale możesz utworzyć własne na podstawie wbudowanych definicji zasad zapewnianych przez Azure Monitor dla maszyn wirtualnych.

> [!NOTE]
> Nie ma żadnego powodu, aby samodzielnie wdrożyć agenta zależności, ponieważ wymaga on Log Analytics Agent do dostarczenia danych do Azure Monitor.


|Nazwa |Opis |
|-----|------------|
|Inspekcja wdrożenia agenta Log Analytics — lista obrazów maszyn wirtualnych (OS) nie została wystawiona |Zgłasza maszyny wirtualne jako niezgodne, jeśli na liście nie ma zdefiniowanego obrazu maszyny wirtualnej (OS), a agent nie jest zainstalowany. |
|Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Linux |Wdróż Log Analytics agenta dla maszyn wirtualnych z systemem Linux, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |
|Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Windows |Wdróż agenta Log Analytics dla maszyn wirtualnych z systemem Windows, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |
| [Wersja zapoznawcza]: Agent Log Analytics powinien być zainstalowany na komputerach z systemem Linux Azure Arc |Zgłasza hybrydowe maszyny wirtualne z systemem Linux jako niezgodne z maszynami wirtualnymi w systemie, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |
| [Wersja zapoznawcza]: Agent Log Analytics powinien być zainstalowany na komputerach z systemem Windows Azure Arc |Zgłasza hybrydowe maszyny wirtualne z systemem Windows jako niezgodne z maszynami wirtualnymi, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |
| [Wersja zapoznawcza]: Wdróż agenta Log Analytics na komputerach z systemem Linux Azure Arc |Wdróż Log Analytics agenta dla hybrydowych maszyn Azure z systemem Linux, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |
| [Wersja zapoznawcza]: Wdróż agenta Log Analytics w usłudze Microsoft Azure Arc Machines |Wdróż Log Analytics agenta dla hybrydowych maszyn platformy Azure z systemem Windows, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |
|Inspekcja wdrożenia agenta zależności w zestawach skalowania maszyn wirtualnych — obraz maszyny wirtualnej (system operacyjny) nie został wystawiony |Raporty zestawu skalowania maszyn wirtualnych są wyświetlane jako niezgodne, jeśli na liście nie ma zdefiniowanego obrazu maszyny wirtualnej (OS), a agent nie jest zainstalowany. |
|Inspekcja wdrożenia agenta Log Analytics w zestawach skalowania maszyn wirtualnych — obraz maszyny wirtualnej (OS) nie został wystawiony |Raporty zestawu skalowania maszyn wirtualnych są wyświetlane jako niezgodne, jeśli na liście nie ma zdefiniowanego obrazu maszyny wirtualnej (OS), a agent nie jest zainstalowany. |
|Wdrażanie agenta Log Analytics dla zestawów skalowania maszyn wirtualnych z systemem Linux |Wdróż Log Analytics agenta dla maszyn wirtualnych z systemem Linux, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |
|Wdrażanie agenta Log Analytics dla zestawów skalowania maszyn wirtualnych z systemem Windows |Wdróż agenta Log Analytics dla zestawów skalowania maszyn wirtualnych z systemem Windows, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |


## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej na temat [Azure Policy](../governance/policy/overview.md).
- Przeczytaj więcej na temat [ustawień diagnostycznych](essentials/diagnostic-settings.md).