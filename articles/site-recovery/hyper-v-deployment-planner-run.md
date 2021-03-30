---
title: Uruchom Planista wdrażania funkcji Hyper-V w Azure Site Recovery
description: W tym artykule opisano sposób uruchamiania Planista wdrażania usługi Azure Site Recovery na potrzeby odzyskiwania po awarii funkcji Hyper-V na platformie Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mayg
ms.openlocfilehash: ba1979c940d4a92b3d1a7a52a4f356b2896ece55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "74082617"
---
# <a name="run-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Uruchamianie Planisty wdrażania usługi Azure Site Recovery na potrzeby odzyskiwania po awarii funkcji Hyper-V na platformie Azure

Narzędzie wiersza polecenia planisty wdrożenia Site Recovery (ASRDeploymentPlanner.exe) można uruchomić w dowolnym z czterech następujących trybów: 
-   Pobieranie listy maszyn wirtualnych
-   [Profil](#profile-hyper-v-vms)
-   Generowanie raportu
-   [Uzyskiwanie informacji o przepływności](#get-throughput)

Najpierw uruchom narzędzie, aby uzyskać listę maszyn wirtualnych z jednego lub wielu hostów funkcji Hyper-V. Następnie uruchom narzędzie w trybie profilowania, aby zebrać informacje na temat współczynnika zmian danych i liczby operacji we/wy na sekundę maszyny wirtualnej. Następnie uruchom narzędzie w celu wygenerowania raportu, aby uzyskać wymagania dotyczące przepustowości i magazynu.

## <a name="get-the-vm-list-for-profiling-hyper-v-vms"></a>Pobieranie listy maszyn wirtualnych na potrzeby profilowania maszyn wirtualnych funkcji Hyper-V
Najpierw musisz utworzyć listę maszyn wirtualnych, które chcesz profilować. Użyj trybu GetVMList narzędzia planisty wdrażania, aby za pomocą jednego polecenia wygenerować listę maszyn wirtualnych istniejących na wielu hostach funkcji Hyper-V. Po wygenerowaniu pełnej listy możesz usunąć z pliku wyjściowego maszyny wirtualne, które nie powinny być profilowane. Następnie użyj pliku wyjściowego dla wszystkich innych operacji — profilowania, generowania raportów i uzyskiwania informacji o przepływności.

Listę maszyn wirtualnych możesz wygenerować, wskazując w narzędziu klaster funkcji Hyper-V lub autonomiczny host funkcji Hyper-V bądź kombinację obu tych elementów.

### <a name="command-line-parameters"></a>Parametry wiersza polecenia
Poniższa tabela zawiera listę obowiązkowych i opcjonalnych parametrów narzędzia uruchamianego w trybie GetVMList. 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```

| Nazwa parametru | Opis |
|---|---|
| -Operation | GetVMList |
| -User | Nazwa użytkownika używana do nawiązywania połączenia z hostem lub klastrem funkcji Hyper-V. Użytkownik musi mieć dostęp administracyjny.|
| -ServerListFile | Plik z listą serwerów zawierającą maszyny wirtualne do profilowania. Można użyć bezwzględnej lub względnej ścieżki pliku. Ten plik powinien zawierać w każdym wierszu jeden z następujących elementów:<ul><li>Nazwa hosta funkcji Hyper-V lub jego adres IP</li><li>Nazwa klastra funkcji Hyper-V lub jego adres IP</li></ul><br>**Przykład:** plik ServerList.txt zawiera informacje o następujących serwerach:<ul><li>Host_1</li><li>10.8.59.27</li><li>Klaster_1</li><li>Host_2</li>|
| -Directory|(Opcjonalnie) Ścieżka UNC (Universal Naming Convention) lub ścieżka katalogu lokalnego, w której mają być przechowywane dane wygenerowane podczas tej operacji. Jeśli nie podano nazwy, jako katalog domyślny zostanie użyty katalog o nazwie „ProfiledData” w bieżącej ścieżce.|
|-OutputFile| Obowiązkowe Plik z listą maszyn wirtualnych pobranych z serwerów funkcji Hyper-V jest zapisywany. Jeśli nazwa nie zostanie podana, szczegółowe informacje zostaną zapisane w pliku VMList.txt.  Użyj tego pliku do uruchomienia profilowania po usunięciu z niego maszyn wirtualnych, które nie muszą być profilowane.|
|-Password|(Opcjonalnie) Hasło wymagane do nawiązania połączenia z hostem funkcji Hyper-V. Jeśli nie zostanie ono określone jako parametr, po uruchomieniu polecenia zostanie wyświetlony związany z tym monit.|

### <a name="getvmlist-discovery"></a>Odnajdywanie GetVMList

- **Klaster funkcji Hyper-V**: jeśli w pliku zawierającym listę serwerów zostanie podana nazwa klastra funkcji Hyper-V, narzędzie znajdzie wszystkie węzły klastra funkcji Hyper-V i pobierze informacje o maszynach wirtualnych istniejących na poszczególnych hostach funkcji Hyper-V.
**Host funkcji Hyper-V**: jeśli podano nazwę hosta funkcji Hyper-V, narzędzie najpierw sprawdza, czy należy on do klastra. Jeśli tak, narzędzie pobiera węzły, które należą do klastra. Następnie pobierane są informacje o maszynach wirtualnych z poszczególnych hostów funkcji Hyper-V. 

Można również wyświetlić w pliku listę przyjaznych nazw lub adresów IP maszyn wirtualnych, które będą profilowane ręcznie.

Otwórz plik wyjściowy w Notatniku, a następnie skopiuj nazwy wszystkich maszyn wirtualnych do profilowania do innego pliku (na przykład ProfileVMList.txt). W każdym wierszu powinna znajdować się jedna nazwa maszyny wirtualnej. Ten plik jest używany jako dane wejściowe dla parametru VMListFile narzędzia dla wszystkich innych operacji: profilowania, generowania raportów i uzyskiwania informacji o przepływności.

### <a name="examples"></a>Przykłady

#### <a name="store-the-list-of-vms-in-a-file"></a>Przechowywanie listy maszyn wirtualnych w pliku
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

#### <a name="store-the-list-of-vms-at-the-default-location--directory-path"></a>Przechowywanie listy maszyn wirtualnych w lokalizacji domyślnej (ścieżka -Directory)
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>Profilowanie maszyn wirtualnych funkcji Hyper-V
W trybie profilowania narzędzie planisty wdrożenia łączy się z poszczególnymi hostami funkcji Hyper-V w celu zebrania danych wydajności maszyn wirtualnych. 

Profilowanie nie wpływa na wydajność produkcyjnych maszyn wirtualnych, ponieważ nie nawiązujesz z nimi bezpośredniego połączenia. Wszystkie dane dotyczące wydajności są zbierane z hosta funkcji Hyper-V.

Narzędzie wysyła co 15 sekund zapytanie do hosta funkcji Hyper-V w celu zapewnienia dokładności profilowania. Co minutę zapisuje również średnie wartości z danych licznika wydajności.

Narzędzie bezproblemowo obsługuje migrację maszyn wirtualnych z jednego węzła do innego węzła w klastrze i migrację magazynu w obrębie hosta.

### <a name="getting-the-vm-list-to-profile"></a>Uzyskiwanie listy maszyn wirtualnych do profilowania
Aby utworzyć listę maszyn wirtualnych do profilowania, zapoznaj się z operacją GetVMList.

Po utworzeniu listy maszyn wirtualnych do profilowania można uruchomić narzędzie w trybie profilowania. 

### <a name="command-line-parameters"></a>Parametry wiersza polecenia
Poniższa tabela zawiera listę obowiązkowych i opcjonalnych parametrów narzędzia uruchamianego w trybie profilowania. Narzędzie to jest typowe dla scenariuszy przechodzenia z programu VMware na platformę Azure i z funkcji Hyper-V na platformę Azure. Następujące parametry są odpowiednie dla funkcji Hyper-V. 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Nazwa parametru | Opis |
|---|---|
| -Operation | StartProfiling |
| -User | Nazwa użytkownika używana do nawiązywania połączenia z hostem lub klastrem funkcji Hyper-V. Użytkownik musi mieć dostęp administracyjny.|
| -VMListFile | Plik z listą maszyn wirtualnych, które mają być profilowane. Można użyć bezwzględnej lub względnej ścieżki pliku. W przypadku funkcji Hyper-V jest to plik wyjściowy operacji GetVMList. Jeśli jest on przygotowywany ręcznie, powinien zawierać w każdym wierszu jedną nazwę serwera lub adres IP, po którym występuje nazwa maszyny wirtualnej. Oba elementy powinny być rozdzielone znakiem \. Nazwa maszyny wirtualnej określona w pliku powinna być taka sama jak nazwa maszyny wirtualnej na hoście funkcji Hyper-V.<br><br>**Przykład:** plik VMList.txt zawiera informacje o następujących maszynach wirtualnych:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|Czas trwania profilowania w minutach. Wartość minimalna to 30 minut.|
|-NoOfHoursToProfile|Czas trwania profilowania w godzinach.|
|-NoOfDaysToProfile |Czas trwania profilowania w dniach. Zaleca się przeprowadzanie profilowania przez więcej niż 7 dni. Umożliwi to zaobserwowanie wzorca obciążenia w środowisku względem danego okresu i użycie go do udostępnienia dokładnego zalecenia.|
|-Virtualization|Typ wirtualizacji (VMware lub Hyper-V).|
|-Directory|(Opcjonalnie) Ścieżka UNC lub ścieżka do katalogu lokalnego, w której są przechowywane dane profilowania wygenerowane podczas profilowania. Jeśli nie podano nazwy, jako katalog domyślny zostanie użyty katalog o nazwie „ProfiledData” w bieżącej ścieżce.|
|-Password|(Opcjonalnie) Hasło wymagane do nawiązania połączenia z hostem funkcji Hyper-V. Jeśli nie zostanie ono określone jako parametr, po uruchomieniu polecenia zostanie wyświetlony związany z tym monit.|
|-StorageAccountName|(Opcjonalnie) Nazwa konta magazynu używana do wyszukiwania osiągalnej przepływności na potrzeby replikacji danych ze środowiska lokalnego na platformę Azure. Narzędzie przekazuje dane testowe na to konto magazynu w celu obliczenia przepływności. Musi to być konto magazynu ogólnego przeznaczenia typu v1 (GPv1).|
|-StorageAccountKey|(Opcjonalnie) Klucz używany do uzyskiwania dostępu do konta magazynu. Przejdź do obszaru Azure Portal > **konta magazynu**  >  *Ustawienia nazwy konta* magazynu  >    >    >  **Klucz1** (lub podstawowy klucz dostępu dla klasycznego konta magazynu).|
|-Environment|(Opcjonalnie) Docelowe środowisko na potrzeby konta usługi Azure Storage. Ten parametr może przyjmować jedną z trzech wartości — AzureCloud, AzureUSGovernment lub AzureChinaCloud. Wartość domyślna to AzureCloud. Użyj parametru, jeśli regionem docelowym jest platforma Azure US USA lub Azure Chiny 21Vianet.|

Zalecamy, aby maszyny wirtualne były profilowane przez więcej niż 7 dni. Jeśli wzorzec zmian zmienia się w ciągu miesiąca, zaleca się przeprowadzenie profilowania w tygodniu, w którym występuje maksymalna wartość współczynnika zmian. W celu uzyskania lepszych rekomendacji zaleca się wykonywanie profilowania przez 31 dni. 

Podczas profilowania jest uruchomiony proces ASRDeploymentPlanner.exe. Czas profilowania w narzędziu jest określany w dniach. Aby przeprowadzić szybki test narzędzia lub weryfikację koncepcji, można wykonać profilowanie przez kilka godzin lub minut. Minimalny dozwolony czas profilowania to 30 minut. 

Podczas profilowania można przekazać nazwę i klucz konta magazynu w celu znalezienia osiągalnej przepływności usługi Azure Site Recovery podczas replikacji z serwera funkcji Hyper-V na platformę Azure. Jeśli nazwa i klucz konta magazynu nie zostaną przekazane podczas profilowania, narzędzie nie obliczy osiągalnej przepływności.

Możesz uruchomić wiele wystąpień narzędzia dla różnych zestawów maszyn wirtualnych. Upewnij się, że nazwy maszyn wirtualnych nie powtarzają się w żadnym zestawie profilowania. Na przykład załóżmy, że miało miejsce profilowanie 10 maszyn wirtualnych (maszyny wirtualne od VM1 do VM10). Po upływie kilku dni ma mieć miejsce profilowanie kolejnych 5 maszyn wirtualnych (maszyny wirtualne od VM11 do VM15). Narzędzie można uruchomić z poziomu innej konsoli wiersza polecenia dla drugiego zestawu maszyn wirtualnych (maszyny wirtualne od VM11 do VM15). 

Sprawdź, czy drugi zestaw maszyn wirtualnych nie zawiera żadnych nazw maszyn wirtualnych z pierwszego wystąpienia profilowania, lub czy używany jest inny katalog wyjściowy dla drugiego przebiegu. Jeśli dwa wystąpienia narzędzia są używane na potrzeby profilowania tych samych maszyn wirtualnych i korzystają z tego samego katalogu wyjściowego, zostanie wygenerowany niepoprawny raport. 

Domyślnie narzędzie jest skonfigurowane na potrzeby profilowania i generowania raportów nawet dla 1000 maszyn wirtualnych. Limit można zmienić, zmieniając wartość klucza MaxVMsSupported w pliku ASRDeploymentPlanner.exe.config.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Aby przeprowadzić profilowanie na przykład 1500 maszyn wirtualnych przy użyciu ustawień domyślnych, utwórz dwa pliki VMList.txt. Jeden z nich zawiera 1000 maszyn wirtualnych, a drugi 500 maszyn wirtualnych. Uruchom dwa wystąpienia narzędzia planisty wdrażania usługi Azure Site Recovery: jedno z plikiem VMList1.txt, a drugie z plikiem VMList2.txt. Do przechowywania danych profilowanych maszyn wirtualnych z obu plików VMList możesz używać tej samej ścieżki katalogu. 

W zależności od konfiguracji sprzętu (a szczególnie ilości pamięci RAM) serwera, z poziomu którego uruchamiane jest narzędzie do generowania raportu, operacja może zakończyć się niepowodzeniem z powodu zbyt małej ilości pamięci. Jeśli masz dobry sprzęt, możesz odpowiednio zwiększyć wartość właściwości MaxVMsSupported.  

Konfiguracja maszyn wirtualnych jest przechwytywana raz na początku operacji profilowania i zapisywana w pliku o nazwie VMDetailList.xml. Te informacje są używane podczas generowania raportu. Żadne zmiany konfiguracji maszyny wirtualnej (np. zwiększenie liczby rdzeni, dysków lub kart sieciowych) wprowadzone od początku do końca okresu profilowania nie są przechwytywane. Jeśli konfiguracja profilowanej maszyny wirtualnej zmieniła się w trakcie profilowania, poniżej przedstawiono obejście pozwalające uzyskać najnowsze szczegóły maszyny wirtualnej podczas generowania raportu:

* Utworzenie kopii zapasowej pliku VMdetailList.xml i usunięcie pliku z jego bieżącej lokalizacji.
* Przekazanie argumentów -User i -Password podczas generowania raportu.

Polecenie profilowania powoduje wygenerowanie kilku plików w katalogu profilowania. Nie usuwaj żadnych plików, ponieważ wpływa to na proces generowania raportów.

### <a name="examples"></a>Przykłady

#### <a name="profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Profilowanie maszyn wirtualnych przez 30 dni i znajdowanie informacji o przepływności między środowiskiem lokalnym i platformą Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="profile-vms-for-15-days"></a>Profilowanie maszyn wirtualnych przez 15 dni
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\vCenter1_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Profilowanie maszyn wirtualnych przez 60 minut w celu szybkiego przetestowania narzędzia
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="profile-vms-for-2-hours-for-a-proof-of-concept"></a>Profilowanie maszyn wirtualnych przez 2 godziny w celu weryfikacji koncepcji
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

### <a name="considerations-for-profiling"></a>Uwagi dotyczące profilowania

Jeśli serwer, na którym uruchomione jest narzędzie, został ponownie uruchomiony lub uległ awarii, albo jeśli zamknięto narzędzie za pomocą klawiszy Ctrl+C, profilowane dane zostaną zachowane. Może jednak brakować profilowanych danych z ostatnich 15 minut. W takich przypadkach uruchom narzędzie jeszcze raz w trybie profilowania po ponownym uruchomieniu serwera.

Jeśli przekazano nazwę i klucz konta magazynu, narzędzie mierzy przepływność na ostatnim etapie profilowania. Jeśli narzędzie zostanie zamknięte przed ukończeniem profilowania, przepływność nie zostanie obliczona. Aby znaleźć przepływność przed wygenerowaniem raportu, można uruchomić operację GetThroughput w konsoli wiersza polecenia. W przeciwnym razie wygenerowany raport nie będzie zawierać informacji o przepływności.

Azure Site Recovery nie obsługuje maszyn wirtualnych z dyskami iSCSI i pass-through. Narzędzie nie może wykryć i profilować dysków iSCSI i przekazujące, które są dołączone do maszyn wirtualnych.

## <a name="generate-a-report"></a>Generowanie raportu
Narzędzie generuje plik programu Microsoft Excel z włączoną obsługą makr (plik XLSM) jako dane wyjściowe raportu. Plik ten zawiera podsumowanie wszystkich zaleceń dotyczących wdrożenia. Raport nosi nazwę DeploymentPlannerReport_ *unikatowy_identyfikator_numeryczny*.xlsm i jest umieszczany w wybranym katalogu.

Po zakończeniu profilowania możesz uruchomić narzędzie w trybie generowania raportu. 

### <a name="command-line-parameters"></a>Parametry wiersza polecenia
Poniższa tabela zawiera listę obowiązkowych i opcjonalnych parametrów narzędzia uruchamianego w trybie generowania raportu. Narzędzie to jest typowe w przypadku przechodzenia z programu VMware na platformę Azure i z funkcji Hyper-V na platformę Azure. Następujące parametry są odpowiednie dla funkcji Hyper-V.
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```

| Nazwa parametru | Opis |
|---|---|
| -Operation | GenerateReport |
|-VMListFile | Plik zawierający listę profilowanych maszyn wirtualnych, dla których będzie generowany raport. Można użyć bezwzględnej lub względnej ścieżki pliku. W przypadku funkcji Hyper-V jest to plik wyjściowy operacji GetVMList. Jeśli jest on przygotowywany ręcznie, powinien zawierać w każdym wierszu jedną nazwę serwera lub adres IP, po którym występuje nazwa maszyny wirtualnej. Oba elementy powinny być rozdzielone znakiem \. Nazwa maszyny wirtualnej określona w pliku powinna być taka sama jak nazwa maszyny wirtualnej na hoście funkcji Hyper-V.<br><br>**Przykład:** plik VMList.txt zawiera informacje o następujących maszynach wirtualnych:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|Typ wirtualizacji (VMware lub Hyper-V).|
|-Directory|(Opcjonalnie) Ścieżka UNC lub ścieżka katalogu lokalnego, w której są przechowywane profilowane dane (pliki wygenerowane podczas profilowania). Te dane są wymagane do wygenerowania raportu. Jeśli nie podano nazwy, jako katalog domyślny zostanie użyty katalog o nazwie „ProfiledData” w bieżącej ścieżce.|
| -User | (Opcjonalnie) Nazwa użytkownika używana do nawiązywania połączenia z hostem lub klastrem funkcji Hyper-V. Użytkownik musi mieć dostęp administracyjny. Nazwa użytkownika i hasło są używane do pobierania najnowszych informacji o konfiguracji maszyn wirtualnych (takich jak liczba dysków, liczba rdzeni i liczba kart sieciowych) do użycia w raporcie. Jeśli nie podasz wartości, zostaną użyte informacje o konfiguracji zebrane podczas profilowania.|
|-Password|(Opcjonalnie) Hasło wymagane do nawiązania połączenia z hostem funkcji Hyper-V. Jeśli nie zostanie ono określone jako parametr, po uruchomieniu polecenia zostanie wyświetlony związany z tym monit.|
| -DesiredRPO | (Opcjonalnie) Żądany cel punktu odzyskiwania (RPO, recovery point objective) w minutach. Wartość domyślna to 15 minut.|
| -Bandwidth | (Opcjonalnie) Przepustowość w megabitach na sekundę. Użyj tego parametru do obliczenia celu punktu odzyskiwania, który można osiągnąć dla określonej przepustowości. |
| -StartDate | (Opcjonalnie) Data i godzina rozpoczęcia w formacie DD-MM-RRRR:GG:MM (format 24-godzinny). Oprócz parametru StartDate należy także określić parametr EndDate. W przypadku określenia wartości parametru StartDate wygenerowany raport będzie dotyczyć profilowanych danych zebranych od daty StartDate do daty EndDate. |
| -EndDate | (Opcjonalnie) Data i godzina zakończenia w formacie DD-MM-RRRR:GG:MM (format 24-godzinny). Oprócz parametru EndDate należy także określić parametr StartDate. W przypadku określenia wartości parametru EndDate wygenerowany raport będzie dotyczyć profilowanych danych zebranych od daty StartDate do daty EndDate. |
| -GrowthFactor | (Opcjonalnie) Współczynnik wzrostu wyrażony jako wartość procentowa. Wartość domyślna to 30 procent. |
| -UseManagedDisks | (Opcjonalnie) UseManagedDisks — Yes/No. Wartość domyślna to Yes. Liczba maszyn wirtualnych, które można umieścić w ramach pojedynczego konta magazynu, jest obliczana z uwzględnieniem tego, czy na potrzeby pracy w trybie failover/testu pracy w trybie failover jest używany dysk zarządzany, czy niezarządzany. |
|-SubscriptionId |(Opcjonalnie) Identyfikator GUID subskrypcji. Użyj tego parametru, aby wygenerować raport szacowania kosztów z najnowszymi cenami opartymi na Twojej subskrypcji, ofercie skojarzonej z Twoją subskrypcją i określonym docelowym regionie świadczenia usługi Azure w konkretnej walucie.|
|-TargetRegion|(Opcjonalnie) Region świadczenia usługi Azure, w którym ma zostać przeprowadzona replikacja. Ze względu na to, że koszty korzystania z platformy Azure są różne w różnych regionach, użycie tego parametru jest przydatne w przypadku generowania raportu dla konkretnego docelowego regionu świadczenia usługi Azure. Domyślnym regionem jest WestUS2 lub ostatnio używany region docelowy. Więcej informacji zawiera lista [obsługiwanych regionów docelowych](hyper-v-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Opcjonalnie) Oferta skojarzona z subskrypcją. Wartością domyślną jest MS-AZR-0003P (płatność zgodnie z rzeczywistym użyciem).|
|-Currency|(Opcjonalnie) Waluta, w jakiej pokazywany jest koszt w wygenerowanym raporcie. Domyślna waluta to dolar amerykański ($) lub ostatnio użyta waluta. Więcej informacji zawiera lista [obsługiwanych walut](hyper-v-deployment-planner-cost-estimation.md#supported-currencies).|

Domyślnie narzędzie jest skonfigurowane na potrzeby profilowania i generowania raportów nawet dla 1000 maszyn wirtualnych. Limit można zmienić, zmieniając wartość klucza MaxVMsSupported w pliku ASRDeploymentPlanner.exe.config.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```

### <a name="examples"></a>Przykłady
#### <a name="generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Generowanie raportu przy użyciu wartości domyślnych, gdy profilowane dane znajdują się na dysku lokalnym
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Generowanie raportu, gdy profilowane dane znajdują się na serwerze zdalnym
Użytkownik musi mieć uprawnienia odczytu/zapisu w katalogu zdalnym.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "\\PS1-W2K12R2\Hyper-V_ProfiledData" -VMListFile "\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-with-a-specific-bandwidth-that-you-will-provision-for-the-replication"></a>Generowanie raportu z określoną przepustowością, która zostanie zaprowizowana na potrzeby replikacji
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -Bandwidth 100
```

#### <a name="generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Generowanie raportu przy użyciu współczynnika wzrostu o wartości 5% zamiast domyślnego współczynnika o wartości 30% 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -GrowthFactor 5
```

#### <a name="generate-a-report-with-a-subset-of-profiled-data"></a>Generowanie raportu przy użyciu podzestawu profilowanych danych
Załóżmy, że masz profilowane dane z 30 dni i chcesz wygenerować raport tylko z 20 dni.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="generate-a-report-for-a-5-minute-rpo"></a>Generowanie raportu w przypadku 5-minutowego celu punktu odzyskiwania
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -DesiredRPO 5
```

#### <a name="generate-a-report-for-the-south-india-azure-region-with-indian-rupee-and-a-specific-offer-id"></a>Generowanie raportu dla regionu świadczenia usługi Azure Indie Południowe w rupiach indyjskich i z konkretnym identyfikatorem oferty
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```


### <a name="percentile-value-used-for-the-calculation"></a>Wartość percentyla używana do obliczenia
Gdy narzędzie generuje raport, jako wartość domyślna przyjmowany jest 95. percentyl dla liczby operacji we/wy zapisu i odczytu, liczby operacji we/wy zapisu oraz współczynnika zmian danych. Te wartości są zbierane podczas profilowania wszystkich maszyn wirtualnych. Dzięki tej metryce masz pewność, że ewentualna szczytowa wartość 100, która może wystąpić na maszynach wirtualnych z powodu tymczasowych zdarzeń, nie będzie używana do określania wymagań dotyczących docelowego konta magazynu ani przepustowości źródłowej. Zdarzenie tymczasowe to na przykład uruchamiane raz dziennie zadanie tworzenia kopii zapasowej, działanie polegające na okresowym indeksowaniu bazy danych lub generowaniu raportu analitycznego albo inne podobne, krótkotrwałe zdarzenia występujące w danym momencie czasowym.

Korzystanie z wartości 95. percentyla oddaje rzeczywistą charakterystykę obciążenia i zapewnia najlepszą wydajność, gdy te obciążenia działają na platformie Azure. Nie przewidujemy konieczności zmiany tej wartości numerycznej. Jeśli nie zmieniasz wartości (na przykład na 90. percentyl), możesz zaktualizować plik konfiguracji ASRDeploymentPlanner.exe.config w folderze domyślnym, a następnie zapisać go w celu wygenerowania nowego raportu dotyczącego istniejących profilowanych danych.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

### <a name="considerations-for-growth-factor"></a>Uwagi dotyczące współczynnika wzrostu
Uwzględnianie wzrostu w charakterystyce obciążenia jest niezwykle ważne przy zakładaniu potencjalnego wzrostu użycia wraz z upływem czasu. W przypadku zmiany charakterystyki po włączeniu ochrony nie ma możliwości przełączenia do innego konta magazynu w celu zapewnienia ochrony bez wyłączenia i ponownego włączenia ochrony.

Załóżmy na przykład, że dzisiaj maszyna wirtualna mieści się na standardowym koncie replikacji magazynu. W ciągu następnych trzech miesięcy prawdopodobnie wystąpią następujące zmiany:

1. Wzrośnie liczba użytkowników aplikacji uruchamianej na maszynie wirtualnej.
2. Zwiększony współczynnik zmiany danych na maszynie wirtualnej będzie wymagać przeniesienia jej do magazynu w warstwie Premium, aby replikacja w usłudze Azure Site Recovery przebiegała wystarczająco szybko.
3. W konsekwencji trzeba będzie wyłączyć i ponownie włączyć ochronę konta magazynu w warstwie Premium.

Zdecydowanie zalecamy zaplanowanie wzrostu podczas planowania wdrożenia. Mimo że wartość domyślna to 30%, to Ty najlepiej znasz wzorzec użycia swoich aplikacji i projekcje wzrostu. Możesz odpowiednio zmienić tę wartość podczas generowania raportu. Ponadto możesz wygenerować wiele raportów dla różnych współczynników wzrostu i tych samych profilowanych danych. Możesz również określić, które zalecenia dotyczące docelowego magazynu i przepustowości źródłowej będą dla Ciebie optymalne. 

Wygenerowany raport programu Microsoft Excel zawiera następujące informacje:

* [Podsumowanie środowiska lokalnego](hyper-v-deployment-planner-analyze-report.md#on-premises-summary)
* [Zalecenia](hyper-v-deployment-planner-analyze-report.md#recommendations)
* [Rozmieszczenie maszyny wirtualnej](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
* [Zgodne maszyny wirtualne](hyper-v-deployment-planner-analyze-report.md#compatible-vms)
* [Niezgodne maszyny wirtualne](hyper-v-deployment-planner-analyze-report.md#incompatible-vms)
* [Wymagania dotyczące magazynu lokalnego](hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement)
* [Dzielenie replikacji początkowej na partie](hyper-v-deployment-planner-analyze-report.md#initial-replication-batching)
* [Szacowanie kosztów](hyper-v-deployment-planner-cost-estimation.md)

![Raport planisty wdrożenia](media/hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>Uzyskiwanie informacji o przepływności
Aby oszacować przepływność osiągalną dla usługi Azure Site Recovery podczas replikacji między środowiskiem lokalnym i platformą Azure, uruchom narzędzie w trybie uzyskiwania informacji o przepływności (GetThroughput). Narzędzie oblicza przepływność z serwera, na którym uruchomiono to narzędzie. Najlepiej, jeśli ten serwer jest serwerem funkcji Hyper-V, której maszyny wirtualne mają być chronione. 

### <a name="command-line-parameters"></a>Parametry wiersza polecenia 
Otwórz konsolę wiersza polecenia, a następnie przejdź do folderu narzędzia do planowania wdrożenia usługi Azure Site Recovery. Uruchom program ASRDeploymentPlanner.exe z poniższymi parametrami.
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```

 Nazwa parametru | Opis |
|---|---|
| -Operation | GetThroughput |
|-Virtualization|Typ wirtualizacji (VMware lub Hyper-V).|
|-Directory|(Opcjonalnie) Ścieżka UNC lub ścieżka katalogu lokalnego, w której są przechowywane profilowane dane (pliki wygenerowane podczas profilowania). Te dane są wymagane do wygenerowania raportu. Jeśli nie podano nazwy, jako katalog domyślny zostanie użyty katalog o nazwie „ProfiledData” w bieżącej ścieżce.|
| -StorageAccountName | Nazwa konta magazynu używana w celu znalezienia użytej przepustowości na potrzeby replikacji danych ze środowiska lokalnego na platformę Azure. Narzędzie przekazuje dane testowe na to konto magazynu w celu określenia użytej przepustowości. Musi to być konto magazynu ogólnego przeznaczenia typu v1 (GPv1).|
| -StorageAccountKey | Klucz konta magazynu używany do uzyskiwania dostępu do konta magazynu. Przejdź do obszaru Azure Portal > **konta magazynu**  >  *Ustawienia nazw kont* magazynu  >    >    >  **Klucz1**.|
| -VMListFile | Plik zawierający listę maszyn wirtualnych, które mają być profilowane, na potrzeby obliczenia użytej przepustowości. Można użyć bezwzględnej lub względnej ścieżki pliku. W przypadku funkcji Hyper-V jest to plik wyjściowy operacji GetVMList. Jeśli jest on przygotowywany ręcznie, powinien zawierać w każdym wierszu jedną nazwę serwera lub adres IP, po którym występuje nazwa maszyny wirtualnej. Oba elementy powinny być rozdzielone znakiem \. Nazwa maszyny wirtualnej określona w pliku powinna być taka sama jak nazwa maszyny wirtualnej na hoście funkcji Hyper-V.<br><br>**Przykład:** plik VMList.txt zawiera informacje o następujących maszynach wirtualnych:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Environment|(Opcjonalnie) Docelowe środowisko na potrzeby konta usługi Azure Storage. Ten parametr może przyjmować jedną z trzech wartości — AzureCloud, AzureUSGovernment lub AzureChinaCloud. Wartość domyślna to AzureCloud. Użyj parametru, jeśli docelowy region świadczenia usługi Azure to Azure USA lub Azure Chiny 21Vianet.|

### <a name="example"></a>Przykład
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

### <a name="throughput-considerations"></a>Zagadnienia dotyczące przepływności

Narzędzie tworzy kilka plików asrvhdfile *liczba*.vhd (gdzie *liczba* to liczba plików) o rozmiarze 64 MB w określonym katalogu. Narzędzie przekazuje te pliki na konto magazynu w celu znalezienia informacji o przepływności. Po zmierzeniu przepływności narzędzie usuwa wszystkie pliki z konta magazynu i serwera lokalnego. Jeśli działanie narzędzia zostanie z jakiegokolwiek powodu zakończone podczas obliczania przepływności, nie usunie ono plików z konta magazynu ani z serwera lokalnego. Konieczne będzie ręczne usunięcie tych plików.

Przepływność jest mierzona w określonym momencie. Oznacza ona maksymalną przepływność osiągalną dla usługi Azure Site Recovery podczas replikacji, jeśli wszystkie pozostałe czynniki pozostają bez zmian. Jeśli na przykład aplikacja zacznie używać większej przepustowości w tej samej sieci, rzeczywista przepływność będzie się zmieniać podczas replikacji. Wynik pomiaru przepływności jest inny, jeśli operacja GetThroughput została uruchomiona przy wysokim współczynniku zmian danych chronionych maszyn wirtualnych. 

Aby sprawdzić osiągalne poziomy przepływności w różnym czasie, zaleca się uruchomienie narzędzia w różnych momentach podczas profilowania. Raport narzędzia zawiera ostatnio zmierzoną przepływność.

> [!NOTE]
> Uruchom narzędzie na serwerze z magazynem i procesorem CPU o takiej samej charakterystyce, jak serwer funkcji Hyper-V.

W przypadku replikacji ustaw zalecaną przepustowość tak, aby spełniała wymagania celu punktu odzyskiwania przez 100 procent czasu. Jeśli nie widzisz wzrostu osiągniętej przepływności zgłoszonej przez narzędzie po ustawieniu odpowiedniej przepustowości, sprawdź następujące kwestie:

1. Sprawdź, czy w sieci występuje problem związany ze standardem QoS (Quality of Service) ograniczający przepływność usługi Azure Site Recovery.
2. Ustal, czy magazyn usługi Azure Site Recovery znajduje się w najbliższym obsługiwanym fizycznym regionie świadczenia usługi Microsoft Azure, aby zminimalizować opóźnienie sieci.
3. Sprawdź charakterystyki magazynu lokalnego, aby ustalić, czy można ulepszyć sprzęt (np. zastąpić dysk twardy dyskiem SSD).

    
## <a name="next-steps"></a>Następne kroki
* [Analizowanie wygenerowanego raportu](hyper-v-deployment-planner-analyze-report.md)
