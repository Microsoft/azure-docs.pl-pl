---
title: Tworzenie środowiska klastra Service Fabric w programie Azure DevTest Labs
description: Dowiedz się, jak utworzyć środowisko z niezależnym klastrem Service Fabric i uruchomić i zatrzymać klaster przy użyciu harmonogramów.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 530cf3b20820e34913612419d0ffa731a70f6a58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85484013"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Tworzenie środowiska z niezależnym klastrem Service Fabric w programie Azure DevTest Labs
Ten artykuł zawiera informacje dotyczące sposobu tworzenia środowiska z niezależnym klastrem Service Fabric w programie Azure DevTest Labs. 

## <a name="overview"></a>Omówienie
DevTest Labs mogą tworzyć własne środowiska testowe zdefiniowane przez szablony zarządzania zasobami platformy Azure. Te środowiska zawierają zarówno zasoby IaaS, jak maszyny wirtualne, jak i zasoby PaaS, takie jak Service Fabric. DevTest Labs umożliwia zarządzanie maszynami wirtualnymi w środowisku przez dostarczanie poleceń do sterowania maszynami wirtualnymi. Te polecenia umożliwiają uruchamianie lub zatrzymywanie maszyny wirtualnej zgodnie z harmonogramem. Podobnie DevTest Labs mogą także ułatwić zarządzanie klastrami Service Fabric w środowisku. Klaster Service Fabric można uruchomić lub zatrzymać w środowisku ręcznie lub za pomocą harmonogramu.

## <a name="create-a-service-fabric-cluster"></a>Tworzenie klastra usługi Service Fabric
Klastry Service Fabric są tworzone przy użyciu środowisk w DevTest Labs. Każde środowisko jest definiowane przez szablon Azure Resource Manager w repozytorium git. [Publiczne repozytorium git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) dla DevTest Labs zawiera szablon Menedżer zasobów, aby utworzyć klaster Service Fabric w folderze [servicefabric-Cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) . 

1. Najpierw utwórz laboratorium w Azure DevTest Labs przy użyciu instrukcji przedstawionych w następującym artykule: [Tworzenie laboratorium](devtest-lab-create-lab.md). Należy zauważyć, że opcja **środowiska publiczne** jest domyślnie **włączona** . 
2. Upewnij się, że dostawca Service Fabric został zarejestrowany dla Twojej subskrypcji, wykonując następujące czynności:
    1. Wybierz pozycję **subskrypcje** w menu nawigacji po lewej stronie i wybierz swoją **subskrypcję**
    2. Na stronie **subskrypcja** wybierz pozycję **dostawcy zasobów** w sekcji **Ustawienia** w menu po lewej stronie. 
    3. Jeśli **Microsoft. ServiecFabric** nie jest zarejestrowany, wybierz pozycję **zarejestruj**. 
3. Na stronie **laboratorium DevTest** dla laboratorium wybierz pozycję **+ Dodaj** na pasku narzędzi. 
    
    ![Przycisk Dodaj na pasku narzędzi](./media/create-environment-service-fabric-cluster/add-button.png)
3. Na stronie **Wybierz bazę** wybierz z listy pozycję **klaster laboratorium Service Fabric** . 

    ![Wybierz z listy klaster Service Fabric Lab](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Na stronie **Konfigurowanie ustawień** wykonaj następujące czynności: 
    1. Określ **nazwę** **środowiska**klastra. Jest to nazwa grupy zasobów na platformie Azure, w której ma zostać utworzony klaster Service Fabric. 
    2. Wybierz **system operacyjny** dla maszyn wirtualnych klastra. Wartość domyślna to: **Windows**.
    3. Określ nazwę **administratora** klastra. 
    4. Określ **hasło** dla administratora. 
    5. Dla **certyfikatu**wprowadź informacje o certyfikacie jako ciąg szyfrowany algorytmem Base64. Aby utworzyć certyfikat, wykonaj następujące czynności:
        1. Pobierz plik **Create-ClusterCertificate.ps1** z [repozytorium git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Alternatywnie Sklonuj repozytorium na komputerze. 
        2. Uruchom program **PowerShell**. 
        3. Uruchom plik **ps1** za pomocą polecenia `.\Create-ClusterCertificate.ps1` . Zostanie wyświetlony plik tekstowy otwarty w programie Notepad z informacjami potrzebnymi do wypełnienia pól związanych z certyfikatem na tej stronie. . 
    6. Wprowadź **hasło dla certyfikatu**.
    7. Określ **odcisk palca** certyfikatu.
    8. Na stronie **Konfigurowanie ustawień** wybierz pozycję **Dodaj** . 

        ![Konfigurowanie ustawień klastra](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Po utworzeniu klastra zostanie wyświetlona Grupa zasobów o nazwie środowiska podanej w poprzednim kroku. Po rozwinięciu zostanie wyświetlony klaster Service Fabric. Jeśli stan grupy zasobów jest zablokowany podczas **tworzenia**, wybierz pozycję **Odśwież** na pasku narzędzi. Środowisko **klastra Service Fabric** tworzy klaster 5-węzłowy 1-NodeType w systemie Linux lub Windows.

    W poniższym przykładzie **mysfabricclusterrg** jest nazwą grupy zasobów, która została utworzona dla klastra Service Fabric. Należy pamiętać, że środowiska laboratoryjne są samodzielne w grupie zasobów, w której zostały utworzone. Oznacza to, że szablon definiujący środowisko, w którym można uzyskać dostęp tylko do zasobów w nowo utworzonej grupie zasobów lub [sieci wirtualnych skonfigurowanych do użycia w laboratorium](devtest-lab-configure-vnet.md). Ten przykład powyżej tworzy wszystkie wymagane zasoby w tej samej grupie zasobów.

    ![Utworzono klaster](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Uruchamianie lub zatrzymywanie klastra
Klaster można uruchomić lub zatrzymać ze strony laboratorium DevTest albo ze strony klastra Service Fabric dostarczonej przez DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Na stronie laboratorium DevTest
Możesz uruchomić lub zatrzymać klaster na stronie laboratorium DevTest dla laboratorium. 

1. Wybierz **trzy kropki (...)** dla klastra Service Fabric, jak pokazano na poniższej ilustracji: 

    ![Uruchamianie i zatrzymywanie poleceń klastra](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Do **uruchamiania** i **zatrzymywania** klastra są widoczne dwa polecenia w menu kontekstowym. Polecenie Uruchom uruchamia wszystkie węzły w klastrze. Polecenie Stop zatrzymuje wszystkie węzły w klastrze. Gdy klaster zostanie zatrzymany, klaster Service Fabric pozostaje w stanie gotowości, ale nie są dostępne żadne węzły, dopóki polecenie Uruchom nie zostanie ponownie wystawione w klastrze w środowisku laboratoryjnym.

    Istnieje kilka kwestii, które należy wziąć pod uwagę podczas korzystania z klastra Service Fabric w środowisku testowym. Może upłynąć trochę czasu, zanim klaster Service Fabric będzie w pełni liofilizowany po ponownym uruchomieniu węzłów. Aby zachować dane z zamykania do uruchamiania, dane muszą zostać zapisane na dysku zarządzanym dołączonym do maszyny wirtualnej. Istnieją konsekwencje dotyczące wydajności podczas korzystania z dołączonego dysku zarządzanego, więc jest to zalecane tylko w środowiskach testowych. Jeśli dysk używany do przechowywania danych nie jest objęty kopią zapasową, dane zostaną utracone po wydaniu polecenia zatrzymania w klastrze.

### <a name="from-the-service-fabric-cluster-page"></a>Na stronie klastra Service Fabric 
Istnieje inny sposób uruchamiania lub zatrzymywania klastra. 

1. Wybierz klaster Service Fabric w widoku drzewa na stronie laboratorium DevTest Lab. 

    ![Wybierz klaster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Na stronie **klaster Service Fabric** klastra zobaczysz polecenia na pasku narzędzi, aby uruchomić lub zatrzymać klaster. 

    ![Uruchamianie lub zatrzymywanie poleceń na stronie klastra Service Fabric](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Konfigurowanie harmonogramu automatycznego uruchamiania i automatycznego zamykania
Klastry Service Fabric można także uruchamiać lub zatrzymywać zgodnie z harmonogramem. To środowisko jest podobne do środowiska w przypadku maszyn wirtualnych w środowisku laboratoryjnym. Aby zaoszczędzić pieniądze, domyślnie każdy klaster utworzony w laboratorium jest automatycznie zamykany w czasie zdefiniowanym przez [zasady zamykania](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)laboratorium. Można przesłonić, określając, czy klaster powinien być wyłączony, czy przez określenie czasu, w którym klaster jest wyłączony. 

![Istniejące harmonogramy automatycznego uruchamiania i automatycznego zamykania](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Zacznij korzystać z harmonogramu Autostart
Aby zrezygnować z harmonogramu uruchamiania, wykonaj następujące czynności:

1. Wybierz pozycję **Uruchom ponownie** w menu po lewej stronie
2. Wybierz **On** opcję włączone **, aby zezwolić na planowanie automatycznego uruchamiania tego klastra usługi Service Fabric**. Ta strona jest włączona tylko wtedy, gdy właściciel laboratorium zezwoli użytkownikom na Autouruchamianie ich maszyn wirtualnych lub klastrów Service Fabric.
3. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Strona Autotekstu](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Skonfiguruj ustawienia automatycznego zamykania 
Aby zmienić ustawienia dotyczące zamykania, wykonaj następujące czynności:

1. W menu po lewej stronie wybierz pozycję **automatyczne zamykanie** . 
2. **Na tej**stronie możesz zrezygnować z automatycznego zamykania, wybierając pozycję **wyłączone** . 
3. Jeśli **wybrano opcję** **włączone**, wykonaj następujące kroki:
    1. Określ **godzinę** zamknięcia.
    2. Określ **strefę** czasową. 
    3. Określ, czy DevTest Labs mają wysyłać **powiadomienia** przed automatycznym zamknięciem. 
    4. W przypadku wybrania opcji **tak** w polu powiadomienia Określ **adres URL elementu webhook** i/lub **adres e-mail** do wysyłania powiadomień. 
    5. Wybierz pozycję **Zapisz** na pasku narzędzi.

        ![Automatycznie Zamknij stronę](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Aby wyświetlić węzły w klastrze Service Fabric
Strona klastra Service Fabric, która była widoczna w pozostałej części kroków, jest specyficzna dla strony DevTest Labs. Nie pokazuje on węzłów w klastrze. Aby wyświetlić więcej informacji na temat klastra, wykonaj następujące kroki:

1. Na stronie **laboratorium DevTest** dla laboratorium wybierz **grupę zasobów** w widoku drzewa w sekcji **moje maszyny wirtualne** .

    ![Wybieranie grupy zasobów](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Na stronie **Grupa zasobów** są wyświetlane wszystkie zasoby w grupie zasobów na liście. Wybierz z listy **klaster Service Fabric** . 

    ![Wybierz klaster na liście](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Zostanie wyświetlona strona **Service Fabric klastra** dla klastra. Jest to strona dostarczana przez Service Fabric. Zobaczysz wszystkie informacje o klastrach, takie jak węzły, typy węzłów itd.

    ![Strona główna klastra Service Fabric](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Następne kroki
Szczegółowe informacje o środowiskach można znaleźć w następujących artykułach: 

- [Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Konfigurowanie i używanie środowisk publicznych w programie Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
