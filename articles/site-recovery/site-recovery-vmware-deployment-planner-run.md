---
title: Uruchom Planista wdrażania na potrzeby odzyskiwania po awarii programu VMware z Azure Site Recovery
description: W tym artykule opisano sposób uruchamiania Planista wdrażania usługi Azure Site Recovery na potrzeby odzyskiwania po awarii oprogramowania VMware na platformę Azure.
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: ramamill
ms.openlocfilehash: 05d260de726c62c130a58938c2a2c9fa2440a96d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004725"
---
# <a name="run-the-deployment-planner-for-vmware-disaster-recovery"></a>Uruchamianie Planista wdrażania na potrzeby odzyskiwania po awarii oprogramowania VMware
Ten artykuł to podręcznik użytkownika planisty wdrożenia usługi Azure Site Recovery dla wdrożeń produkcyjnych oprogramowania VMware na platformie Azure.


## <a name="modes-of-running-deployment-planner"></a>Tryby uruchamiania planisty wdrożenia
Narzędzie wiersza polecenia (ASRDeploymentPlanner.exe) możesz uruchomić w jednym z trzech następujących trybów:

1.  [Profilowanie](#profile-vmware-vms)
2.  [Generowanie raportu](#generate-report)
3.  [Uzyskiwanie informacji o przepływności](#get-throughput)

Najpierw uruchom narzędzie w trybie profilowania, aby zebrać informacje na temat współczynnika zmian danych i operacji we/wy na sekundę maszyny wirtualnej. Następnie uruchom narzędzie w celu wygenerowania raportu, aby uzyskać informacje dotyczące przepustowości sieci, wymagań w zakresie magazynowania oraz kosztu odzyskiwania po awarii.

## <a name="profile-vmware-vms"></a>Profilowanie maszyn wirtualnych VMware
W trybie profilowania narzędzie planisty wdrożenia łączy się z serwerem vCenter/hostem vSphere ESXi w celu zebrania danych wydajności maszyny wirtualnej.

* Profilowanie nie wpływa na wydajność produkcyjnych maszyn wirtualnych, ponieważ nie nawiązujesz z nimi bezpośredniego połączenia. Wszystkie dane wydajności są zbierane z serwera vCenter/hosta vSphere ESXi.
* Aby upewnić się, że profilowanie ma niewielki wpływ na wydajność serwera, co 15 minut narzędzie przesyła zapytanie na serwer vCenter/do hosta vSphere ESXi. Interwał zapytań nie ma wpływu na dokładność profilowania, ponieważ narzędzie zapisuje dane liczników wydajności z każdej minuty.

### <a name="create-a-list-of-vms-to-profile"></a>Tworzenie listy maszyn wirtualnych do profilowania
Najpierw musisz utworzyć listę maszyn wirtualnych, które chcesz profilować. Wszystkie nazwy maszyn wirtualnych na serwerze vCenter/hoście vSphere ESXi można uzyskać za pomocą poniższych poleceń programu VMware vSphere PowerCLI, wykonując kroki poniższej procedury. Alternatywnie można wyświetlić w pliku listę przyjaznych nazw lub adresów IP maszyn wirtualnych, które chcesz profilować ręcznie.

1. Zaloguj się do maszyny wirtualnej z zainstalowanym programem VMware vSphere PowerCLI.
2. Otwórz konsolę programu VMware vSphere PowerCLI.
3. Upewnij się, że zasady wykonywania skryptu zostały włączone. Jeśli zostały wyłączone, uruchom konsolę programu VMware vSphere PowerCLI w trybie administratora, a następnie włącz zasady, uruchamiając następujące polecenie:

    ```powershell
    Set-ExecutionPolicy –ExecutionPolicy AllSigned
    ```

4. Opcjonalnie można uruchomić następujące polecenie, jeśli Connect-VIServer nie jest rozpoznawana jako nazwa polecenia cmdlet.

    ```powershell
    Add-PSSnapin VMware.VimAutomation.Core
    ```

5. Aby pobrać wszystkie nazwy maszyn wirtualnych na serwerze vCenter/hoście vSphere ESXi i zapisać listę w pliku TXT, uruchom dwa poniższe polecenia.
Zamień wartości &lsaquo;server name&rsaquo;, &lsaquo;user name&rsaquo;, &lsaquo;password&rsaquo; i &lsaquo;outputfile.txt&rsaquo; na własne wartości.

    ```powershell
    Connect-VIServer -Server <server name> -User <user name> -Password <password>

    Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>
    ```

6. Otwórz plik wyjściowy w Notatniku, a następnie skopiuj nazwy wszystkich maszyn wirtualnych do profilowania do innego pliku (np. ProfileVMList.txt) tak, aby w każdym wierszu znajdowała się jedna nazwa maszyny wirtualnej. Ten plik jest używany jako źródło danych wejściowych dla parametru *-VMListFile* narzędzia wiersza polecenia.

    ![Lista nazw maszyn wirtualnej w planiście wdrożenia
](media/site-recovery-vmware-deployment-planner-run/profile-vm-list-v2a.png)

### <a name="start-profiling"></a>Rozpoczynanie profilowania
Po utworzeniu listy maszyn wirtualnych do profilowania można uruchomić narzędzie w trybie profilowania. Poniżej przedstawiono listę obowiązkowych i opcjonalnych parametrów narzędzia w trybie profilowania.

```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Nazwa parametru | Opis |
|---|---|
| -Operation | StartProfiling |
| -Server | W pełni kwalifikowana nazwa domeny lub adres IP serwera vCenter/hosta vSphere ESXi, którego maszyny wirtualne mają być profilowane.|
| -User | Nazwa użytkownika łączącego się z serwerem vCenter/hostem vSphere ESXi. Użytkownik musi mieć minimalnie uprawnienia tylko do odczytu.|
| -VMListFile | Plik zawierający listę maszyn wirtualnych do profilowania. Można użyć bezwzględnej lub względnej ścieżki pliku. Ten plik powinien zawierać jedną nazwę/jeden adres IP maszyny wirtualnej w każdym wierszu. Nazwa maszyny wirtualnej określona w pliku powinna być taka sama jak nazwa maszyny wirtualnej na serwerze vCenter/hoście vSphere ESXi.<br>Na przykład plik VMList.txt zawiera informacje o następujących maszynach wirtualnych:<ul><li>maszyna_wirtualna_A</li><li>10.150.29.110</li><li>maszyna_wirtualna_B</li><ul> |
|-NoOfMinutesToProfile|Czas trwania profilowania w minutach. Wartość minimalna to 30 minut.|
|-NoOfHoursToProfile|Czas trwania profilowania w godzinach.|
| -NoOfDaysToProfile | Czas trwania profilowania w dniach. Zalecamy przeprowadzanie profilowania przez więcej niż 7 dni, aby umożliwić zaobserwowanie wzorca obciążenia w środowisku względem danego okresu i użycie go do udostępnienia dokładnego zalecenia. |
|-Virtualization|Określ typ wirtualizacji (VMware lub Hyper-V).|
| -Directory | (Opcjonalnie) Ścieżka UNC (Universal Naming Convention) lub ścieżka katalogu lokalnego, w której są przechowywane dane profilowania wygenerowane podczas profilowania. Jeśli nazwa katalogu nie zostanie podana, jako katalog domyślny zostanie użyty katalog o nazwie „ProfiledData” w bieżącej ścieżce. |
| -Password | (Opcjonalnie) Hasło do użycia podczas nawiązywania połączenia z serwerem vCenter/hostem vSphere ESXi. Jeśli nie określisz go teraz, monit o podanie hasła zostanie wyświetlony podczas wykonywania polecenia.|
|-Port|(Opcjonalnie) Numer portu do połączenia z hostem vCenter/ESXi. Port domyślny to 443.|
|-Protocol| (Opcjonalnie) Wybrany protokół — „http” lub „https” — na potrzeby łączenia z programem vCenter. Protokół domyślny to https.|
| -StorageAccountName | (Opcjonalnie) Nazwa konta magazynu używana do wyszukiwania osiągalnej przepływności na potrzeby replikacji danych ze środowiska lokalnego na platformę Azure. Narzędzie przekazuje dane testowe na to konto magazynu w celu obliczenia przepływności. Musi to być konto magazynu ogólnego przeznaczenia typu v1 (GPv1). |
| -StorageAccountKey | (Opcjonalnie) Klucz konta magazynu używany do uzyskiwania dostępu do konta magazynu. W witrynie Azure Portal przejdź do pozycji Konta magazynu > <*Nazwa_konta_magazynu*> > Ustawienia > Klucze dostępu > Klucz1. |
| -Environment | (Opcjonalnie) Jest to docelowe środowisko konta usługi Azure Storage. Ten parametr może przyjmować jedną z trzech wartości — AzureCloud, AzureUSGovernment i AzureChinaCloud. Wartością domyślną jest AzureCloud. Użyj parametru, jeśli docelowy region świadczenia usługi Azure to Azure USA lub Azure Chiny 21Vianet. |


Zalecamy, aby maszyny wirtualne były profilowane przez więcej niż 7 dni. Jeśli wzorzec zmian zmienia się w ciągu miesiąca, zaleca się przeprowadzenie profilowania w tygodniu, w którym występuje maksymalna wartość współczynnika zmian. W celu uzyskania lepszych rekomendacji zaleca się wykonywanie profilowania przez 31 dni. Podczas profilowania jest uruchomiony proces ASRDeploymentPlanner.exe. Czas profilowania w narzędziu jest określany w dniach. Aby przeprowadzić szybki test narzędzia lub weryfikację koncepcji, można wykonać profilowanie przez kilka godzin lub minut. Minimalny dozwolony czas profilowania to 30 minut.

Podczas profilowania można przekazać nazwę i klucz konta magazynu w celu znalezienia osiągalnej przepływności usługi Site Recovery podczas replikacji z serwera konfiguracji lub serwera przetwarzania na platformę Azure. Jeśli nazwa i klucz konta magazynu nie zostaną przekazane podczas profilowania, narzędzie nie obliczy osiągalnej przepływności.

Możesz uruchomić wiele wystąpień narzędzia dla różnych zestawów maszyn wirtualnych. Upewnij się, że nazwy maszyn wirtualnych nie powtarzają się w żadnym zestawie profilowania. Jeśli na przykład profilowano dziesięć maszyn wirtualnych (MW1–MW10) i po kilku dniach chcesz przeprowadzić profilowanie kolejnych pięciu maszyn wirtualnych (MW11–MW15), możesz uruchomić narzędzie z poziomu innej konsoli wiersza polecenia dla drugiego zestawu maszyn wirtualnych (MW11–MW15). Upewnij się, że drugi zestaw maszyn wirtualnych nie zawiera żadnych nazw maszyn wirtualnych z pierwszego wystąpienia profilowania, lub użyj innego katalogu wyjściowego dla drugiego przebiegu. Jeśli dwa wystąpienia narzędzia są używane na potrzeby profilowania tych samych maszyn wirtualnych i korzystają z tego samego katalogu wyjściowego, zostanie wygenerowany niepoprawny raport.

Domyślnie narzędzie jest skonfigurowane do profilowania i generowania raportu do 1000 maszyn wirtualnych. Limit można zmienić, zmieniając wartość klucza MaxVMsSupported w pliku *ASRDeploymentPlanner.exe.config*.
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Aby przeprowadzić profilowanie, powiedzmy, 1500 maszyn wirtualnych przy ustawieniach domyślnych, utwórz dwa pliki VMList.txt. Jeden zawierający listę 1000 maszyn wirtualnych i drugi zawierający listę 500 maszyn wirtualnych. Uruchom dwa wystąpienia Planista wdrażania usługi Azure Site Recovery, jeden z VMList1.txt i inne z VMList2.txt. Do przechowywania danych profilowanych maszyn wirtualnych z obu plików VMList możesz używać tej samej ścieżki katalogu.

Zauważyliśmy, że w zależności od konfiguracji sprzętu, a szczególnie rozmiaru pamięci RAM serwera, z którego jest uruchamiane narzędzie do generowania raportu, operacja może zakończyć się niepowodzeniem z powodu zbyt małej ilości pamięci. Jeśli masz dobry sprzęt, możesz zwiększyć wartość klucza MaxVMsSupported.  

Jeśli masz wiele serwerów vCenter, musisz uruchomić jedno wystąpienie narzędzia ASRDeploymentPlanner dla każdego serwera vCenter w celu profilowania.

Konfiguracja maszyn wirtualnych jest przechwytywana raz na początku operacji profilowania i zapisywana w pliku o nazwie VMDetailList.xml. Te informacje są używane podczas generowania raportu. Żadne zmiany konfiguracji maszyny wirtualnej (np. zwiększenie liczby rdzeni, dysków lub kart sieciowych) wprowadzone od początku do końca okresu profilowania nie są przechwytywane. Jeśli konfiguracja profilowanej maszyny wirtualnej zmieniła się w trakcie profilowania w publicznej wersji zapoznawczej, poniżej przedstawiono obejście pozwalające uzyskać najnowsze szczegóły maszyny wirtualnej podczas generowania raportu:

* Utworzenie kopii zapasowej pliku VMdetailList.xml i usunięcie pliku z jego bieżącej lokalizacji.
* Przekazanie argumentów -User i -Password podczas generowania raportu.

Polecenie profilowania powoduje wygenerowanie kilku plików w katalogu profilowania. Nie usuwaj żadnych plików, ponieważ wpływa to na proces generowania raportów.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Przykład 1: profilowanie maszyn wirtualnych przez 30 dni i znajdowanie przepływności między środowiskiem lokalnym i platformą Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Przykład 2: profilowanie maszyn wirtualnych przez 15 dni

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Przykład 3: profilowanie maszyn wirtualnych przez 60 minut w celu szybkiego przetestowania narzędzia
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60  -User vCenterUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>Przykład 4: profilowanie maszyn wirtualnych przez 2 godziny w celu weryfikacji koncepcji
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfHoursToProfile 2 -User vCenterUser1
```

>[!NOTE]
>
>* Jeśli serwer z działającym narzędziem został ponownie uruchomiony lub uległ awarii albo jeśli zamknięto narzędzie za pomocą klawiszy Ctrl+C, profilowane dane zostaną zachowane. Może jednak brakować profilowanych danych z ostatnich 15 minut. W takiej sytuacji uruchom narzędzie jeszcze raz w trybie profilowania po ponownym uruchomieniu serwera.
>* Jeśli przekazano nazwę i klucz konta magazynu, narzędzie mierzy przepływność na ostatnim etapie profilowania. Jeśli narzędzie zostanie zamknięte przed ukończeniem profilowania, przepływność nie zostanie obliczona. Aby znaleźć przepływność przed wygenerowaniem raportu, można uruchomić operację GetThroughput w konsoli wiersza polecenia. W przeciwnym razie wygenerowany raport nie będzie zawierać informacji o przepływności.


## <a name="generate-report"></a>Generowanie raportu
Narzędzie generuje plik programu Microsoft Excel z włączoną obsługą makr (plik XLSM) jako dane wyjściowe raportu zawierające podsumowanie wszystkich zaleceń dotyczące wdrożenia. Raport ma nazwę `DeploymentPlannerReport_<unique numeric identifier>.xlsm` i znajduje się w określonym katalogu.

>[!NOTE]
>Generowanie raportu wymaga komputera z systemem Windows lub Windows Server z programem Excel 2013 lub nowszym. Symbol dziesiętny na tym komputerze powinien zostać skonfigurowany jako "." w celu utworzenia oszacowania kosztów. Jeśli masz Instalatora "," jako symbol dziesiętny, przejdź do pozycji "Zmień datę, godzinę lub format liczbowy" w panelu sterowania i przejdź do pozycji "Ustawienia dodatkowe", aby zmienić symbol dziesiętny na ".".

Po zakończeniu profilowania możesz uruchomić narzędzie w trybie generowania raportu. Poniższa tabela zawiera listę obowiązkowych i opcjonalnych parametrów narzędzia uruchamianego w trybie generowania raportu.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Nazwa parametru | Opis |
|-|-|
| -Operation | GenerateReport |
| -Server |  W pełni kwalifikowana nazwa domeny lub adres IP serwera vCenter/vSphere (nazwa lub adres IP muszą być takie same jak użyte podczas profilowania) z profilowanymi maszynami wirtualnymi, dla których ma zostać wygenerowany raport. Pamiętaj, że jeśli podczas profilowania użyto serwera vCenter, nie można użyć serwera vSphere podczas generowania raportu i odwrotnie.|
| -VMListFile | Plik zawierający listę profilowanych maszyn wirtualnych, dla których będzie generowany raport. Można użyć bezwzględnej lub względnej ścieżki pliku. Ten plik powinien zawierać jedną nazwę lub jeden adres IP maszyny wirtualnej w każdym wierszu. Nazwy maszyn wirtualnych określone w pliku powinny być takie same jak nazwy maszyn wirtualnych na serwerze vCenter/hoście vSphere ESXi i zgodne z nazwami użytymi podczas profilowania.|
|-Virtualization|Określ typ wirtualizacji (VMware lub Hyper-V).|
| -Directory | (Opcjonalnie) Ścieżka UNC lub ścieżka katalogu lokalnego, w której są przechowywane profilowane dane (pliki wygenerowane podczas profilowania). Te dane są wymagane do wygenerowania raportu. Jeśli nie określisz nazwy, zostanie użyty katalog „ProfiledData”. |
| -GoalToCompleteIR | (Opcjonalnie) Czas w godzinach, w którym musi zostać ukończona replikacja początkowa profilowanych maszyn wirtualnych. Wygenerowany raport zawiera liczbę maszyn wirtualnych, dla których można ukończyć replikację początkową w określonym czasie. Wartość domyślna to 72 godziny. |
| -User | (Opcjonalnie) Nazwa użytkownika do użycia podczas nawiązywania połączenia z serwerem vCenter/vSphere. Nazwa jest używana do pobierania najnowszych informacji o konfiguracji maszyn wirtualnych, takich jak liczba dysków, liczba rdzeni czy liczba kart sieciowych, do zastosowania w raporcie. Jeśli nie podasz nazwy, zostaną użyte informacje o konfiguracji zebrane na początku profilowania. |
| -Password | (Opcjonalnie) Hasło do użycia podczas nawiązywania połączenia z serwerem vCenter/hostem vSphere ESXi. Jeśli nie określisz hasła jako parametru, później zostanie wyświetlony monit o podanie hasła podczas wykonywania polecenia. |
|-Port|(Opcjonalnie) Numer portu do połączenia z hostem vCenter/ESXi. Port domyślny to 443.|
|-Protocol|(Opcjonalnie) Wybrany protokół — „http” lub „https” — na potrzeby łączenia z programem vCenter. Protokół domyślny to https.|
| -DesiredRPO | (Opcjonalnie) Żądany cel punktu odzyskiwania w minutach. Wartość domyślna to 15 minut.|
| -Bandwidth | Przepustowość w Mb/s. Parametr służący do obliczania celu punktu odzyskiwania, który można osiągnąć dla określonej przepustowości. |
| -StartDate | (Opcjonalnie) Data i godzina rozpoczęcia w formacie DD-MM-RRRR:GG:MM (format 24-godzinny). *StartDate* musi być określony wraz z parametrem *EndDate*. W przypadku określenia wartości parametru StartDate wygenerowany raport będzie dotyczyć profilowanych danych zebranych od daty StartDate do daty EndDate. |
| -EndDate | (Opcjonalnie) Data i godzina zakończenia w 24-godzinnym formacie DD-MM-RRRR:GG:MM. Należy określić parametr *EndDate* wraz z *StartDate*. W przypadku określenia wartości parametru EndDate wygenerowany raport będzie dotyczyć profilowanych danych zebranych od daty StartDate do daty EndDate. |
| -GrowthFactor | (Opcjonalnie) Współczynnik wzrostu wyrażony jako wartość procentowa. Wartość domyślna to 30 procent. |
| -UseManagedDisks | (Opcjonalnie) UseManagedDisks — Yes/No. Wartość domyślna to Yes. Liczba maszyn wirtualnych, które można umieścić w ramach pojedynczego konta magazynu, jest obliczana z uwzględnieniem tego, czy na potrzeby pracy w trybie failover/testu pracy w trybie failover jest używany dysk zarządzany, czy niezarządzany. |
|-SubscriptionId |(Opcjonalnie) Identyfikator GUID subskrypcji. Należy pamiętać, że ten parametr jest wymagany, gdy konieczne jest wygenerowanie raportu szacowania kosztów z najnowszymi cenami w oparciu o subskrypcję, ofertę skojarzoną z subskrypcją i dla określonego docelowego regionu świadczenia usługi Azure w **podanej walucie**.|
|-TargetRegion|(Opcjonalnie) Region świadczenia usługi Azure, w którym ma zostać przeprowadzona replikacja. Użycie tego parametru jest przydatne w przypadku generowania raportu dla konkretnego docelowego regionu świadczenia usługi Azure, ponieważ koszty korzystania z platformy Azure są różne w różnych regionach.<br>Domyślnym regionem jest WestUS2 lub ostatnio używany region docelowy.<br>Więcej informacji zawiera lista [obsługiwanych regionów docelowych](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Opcjonalnie) Oferta skojarzona z daną subskrypcją. Wartością domyślną jest MS-AZR-0003P (Płatność zgodnie z rzeczywistym użyciem).|
|-Currency|(Opcjonalnie) Waluta, w jakiej pokazywany jest koszt w wygenerowanym raporcie. Domyślna waluta to dolar amerykański ($) lub ostatnio użyta waluta.<br>Więcej informacji zawiera lista [obsługiwanych walut](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).|

Domyślnie narzędzie jest skonfigurowane do profilowania i generowania raportu do 1000 maszyn wirtualnych. Limit można zmienić, zmieniając wartość klucza MaxVMsSupported w pliku *ASRDeploymentPlanner.exe.config*.
```xml
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Przykład 1: generowanie raportu przy użyciu wartości domyślnych, gdy profilowane dane znajdują się na dysku lokalnym
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Przykład 2: generowanie raportu, gdy profilowane dane znajdują się na serwerze zdalnym
Użytkownik musi mieć uprawnienia odczytu/zapisu w katalogu zdalnym.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Przykład 3: generowanie raportu przy użyciu określonej przepustowości i celu ukończenia replikacji początkowej w określonym czasie
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Przykład 4: generowanie raportu przy użyciu współczynnika wzrostu 5 procent zamiast domyślnego współczynnika 30 procent
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Przykład 5: generowanie raportu przy użyciu podzestawu profilowanych danych
Załóżmy, że masz profilowane dane z 30 dni i chcesz wygenerować raport tylko z 20 dni.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>Przykład 6: generowanie raportu w przypadku 5-minutowego celu punktu odzyskiwania
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>Przykład 7: generowanie raportu dla regionu świadczenia usługi Azure Indie Południowe w rupiach indyjskich i z konkretnym identyfikatorem oferty

Należy pamiętać, że identyfikator subskrypcji jest wymagany do generowania raportu kosztów w określonej walucie.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware  -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>Wartość percentyla używana do obliczenia
**Jaka domyślna wartość percentyla metryk wydajności zebranych podczas profilowania jest używana przez narzędzie podczas generowania raportu?**

Domyślnie narzędzie używa wartości 95. percentyla operacji we/wy odczytu i zapisu na sekundę, operacji we/wy zapisu na sekundę i współczynnika zmian danych zebranych podczas profilowania wszystkich maszyn wirtualnych. Dzięki tej metryce masz pewność, że ewentualna szczytowa wartość 100. percentyla, która może wystąpić na maszynach wirtualnych z powodu tymczasowych zdarzeń, nie będzie używana do określania wymagań dotyczących docelowego konta magazynu ani przepustowości źródłowej. Zdarzenie tymczasowe to na przykład uruchamiane raz dziennie zadanie tworzenia kopii zapasowej, działanie polegające na okresowym indeksowaniu bazy danych lub generowaniu raportu analitycznego albo inne podobne, krótkotrwałe zdarzenia występujące w danym momencie czasowym.

Korzystanie z wartości 95. percentyla oddaje rzeczywistą charakterystykę obciążenia i zapewnia najlepszą wydajność, gdy te obciążenia działają na platformie Azure. Nie przewidujemy konieczności zmiany tej wartości numerycznej. Jeśli nie zmieniasz wartości (np. na 90. percentyl), możesz zaktualizować plik konfiguracji *ASRDeploymentPlanner.exe.config* w folderze domyślnym i zapisać go w celu wygenerowania nowego raportu dotyczącego istniejących profilowanych danych.
```xml
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>Zagadnienia związane ze współczynnikiem wzrostu
**Dlaczego podczas planowania wdrożenia warto wziąć pod uwagę współczynnik wzrostu?**

Uwzględnianie wzrostu w charakterystyce obciążenia jest niezwykle ważne przy zakładaniu potencjalnego wzrostu użycia wraz z upływem czasu. W przypadku zmiany charakterystyki po włączeniu ochrony nie ma możliwości przełączenia do innego konta magazynu w celu zapewnienia ochrony bez wyłączenia i ponownego włączenia ochrony.

Załóżmy na przykład, że dzisiaj maszyna wirtualna mieści się na standardowym koncie replikacji magazynu. W ciągu następnych trzech miesięcy prawdopodobnie wystąpią następujące zmiany:

* Wzrośnie liczba użytkowników aplikacji uruchamianej na maszynie wirtualnej.
* Zwiększony wynikowy współczynnik zmiany danych na maszynie wirtualnej będzie wymagać przeniesienia jej do magazynu w warstwie Premium, aby replikacja w usłudze Site Recovery przebiegała wystarczająco szybko.
* W konsekwencji trzeba będzie wyłączyć i ponownie włączyć ochronę konta magazynu w warstwie Premium.

Zdecydowanie zalecamy zaplanowanie wzrostu podczas planowania wdrożenia i jeśli wartość domyślna to 30 procent. Ty najlepiej znasz wzorzec użycia swoich aplikacji i projekcje wzrostu, możesz więc odpowiednio zmienić tę wartość podczas generowania raportu. Ponadto możesz wygenerować wiele raportów dla różnych współczynników wzrostu i tych samych profilowanych danych oraz określić, które zalecenia dotyczące docelowego konta magazynu i przepustowości źródłowej będą dla Ciebie optymalne.

Wygenerowany raport programu Microsoft Excel zawiera następujące informacje:

* [Podsumowanie lokalne](site-recovery-vmware-deployment-planner-analyze-report.md#on-premises-summary)
* [Zalecenia](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)
* [< maszyny wirtualnej — >rozmieszczenia magazynu](site-recovery-vmware-deployment-planner-analyze-report.md#vm-storage-placement)
* [Zgodne maszyny wirtualne](site-recovery-vmware-deployment-planner-analyze-report.md#compatible-vms)
* [Niezgodne maszyny wirtualne](site-recovery-vmware-deployment-planner-analyze-report.md#incompatible-vms)
* [Szacowanie kosztów](site-recovery-vmware-deployment-planner-cost-estimation.md)

![Planista wdrożenia](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

## <a name="get-throughput"></a>Uzyskiwanie informacji o przepływności

Aby oszacować przepływność osiągalną dla usługi Site Recovery podczas replikacji między środowiskiem lokalnym i platformą Azure, uruchom narzędzie w trybie uzyskiwania informacji o przepływności (GetThroughput). Narzędzie oblicza przepływność z serwera, na którym uruchomiono to narzędzie. W idealnej sytuacji ten serwer jest oparty na przewodniku dotyczącym zmian rozmiaru serwera konfiguracji. Jeśli wdrożono już lokalnie składniki infrastruktury usługi Site Recovery, uruchom narzędzie na serwerze konfiguracji.

Otwórz konsolę wiersza polecenia i przejdź do folderu narzędzia do planowania wdrożenia usługi Site Recovery. Uruchom program ASRDeploymentPlanner.exe z poniższymi parametrami.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Nazwa parametru | Opis |
|-|-|
| -Operation | GetThroughput |
|-Virtualization|Określ typ wirtualizacji (VMware lub Hyper-V).|
| -Directory | (Opcjonalnie) Ścieżka UNC lub ścieżka katalogu lokalnego, w której są przechowywane profilowane dane (pliki wygenerowane podczas profilowania). Te dane są wymagane do wygenerowania raportu. Jeśli nie określono nazwy katalogu, jest używany katalog „ProfiledData”. |
| -StorageAccountName | Nazwa konta magazynu używana w celu znalezienia użytej przepustowości na potrzeby replikacji danych ze środowiska lokalnego na platformę Azure. Narzędzie przekazuje dane testowe na to konto magazynu w celu określenia użytej przepustowości. Musi to być konto magazynu ogólnego przeznaczenia typu v1 (GPv1).|
| -StorageAccountKey | Klucz konta magazynu używany do uzyskiwania dostępu do konta magazynu. W witrynie Azure Portal przejdź do pozycji Konta magazynu > <*Nazwa_konta_magazynu*> > Ustawienia > Klucze dostępu > Klucz1 (lub podstawowy klucz dostępu w przypadku klasycznego konta magazynu). |
| -VMListFile | Plik zawierający listę maszyn wirtualnych, które mają być profilowane, na potrzeby obliczenia użytej przepustowości. Można użyć bezwzględnej lub względnej ścieżki pliku. Ten plik powinien zawierać jedną nazwę/jeden adres IP maszyny wirtualnej w każdym wierszu. Nazwy maszyn wirtualnych określone w pliku powinny być takie same jak nazw maszyn wirtualnych na serwerze vCenter/hoście vSphere ESXi.<br>Na przykład plik VMList.txt zawiera informacje o następujących maszynach wirtualnych:<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Environment | (Opcjonalnie) Jest to docelowe środowisko konta usługi Azure Storage. Ten parametr może przyjmować jedną z trzech wartości — AzureCloud, AzureUSGovernment i AzureChinaCloud. Wartością domyślną jest AzureCloud. Użyj parametru, jeśli docelowy region świadczenia usługi Azure to Azure USA lub Azure Chiny 21Vianet. |

Narzędzie tworzy kilka plików asrvhdfile<#>.vhd (gdzie znak „#” to liczba plików) o rozmiarze 64 MB w określonym katalogu. Narzędzie przekazuje te pliki na konto magazynu w celu znalezienia informacji o przepływności. Po zmierzeniu przepływności narzędzie usuwa wszystkie pliki z konta magazynu i serwera lokalnego. Jeśli działanie narzędzia zostanie z jakiegokolwiek powodu przerwane podczas obliczania przepływności, nie usunie ono plików z magazynu ani z serwera lokalnego. Trzeba będzie usunąć je ręcznie.

Przepływność jest mierzona w określonym momencie i jest maksymalną przepływnością osiągalną dla usługi Site Recovery podczas replikacji, jeśli wszystkie pozostałe czynniki pozostają bez zmian. Jeśli na przykład aplikacja zacznie używać większej przepustowości w tej samej sieci, rzeczywista przepływność będzie się zmieniać podczas replikacji. Jeśli używasz polecenia GetThroughput na serwerze konfiguracji, narzędzie nie uwzględnia chronionych maszyn wirtualnych ani replikacji w toku. Wynik pomiaru przepływności jest inny, jeśli operacja GetThroughput została uruchomiona przy wysokim współczynniku zmian danych chronionych maszyn wirtualnych. Zalecamy uruchomienie narzędzia w różnych momentach podczas profilowania, aby sprawdzić osiągalne poziomy przepływności w różnym czasie. Raport narzędzia zawiera ostatnio zmierzoną przepływność.

### <a name="example"></a>Przykład
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -Virtualization VMware -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> Uruchom narzędzie na serwerze z magazynem i procesorem CPU o takiej samej charakterystyce jak serwer konfiguracji.
>
> W przypadku replikacji ustaw zalecaną przepustowość tak, aby spełniała wymagania celu punktu odzyskiwania przez 100 procent czasu. Jeśli nie widzisz wzrostu osiągniętej przepływności zgłoszonej przez narzędzie po ustawieniu odpowiedniej przepustowości, sprawdź następujące kwestie:
>
>  1. Ustal, czy w sieci jest używana technologia QoS (Quality of Service) ograniczająca przepływność usługi Site Recovery.
>
>  2. Ustal, czy magazyn usługi Site Recovery znajduje się w najbliższym obsługiwanym fizycznym regionie platformy Microsoft Azure, aby zminimalizować opóźnienie sieci.
>
>  3. Sprawdź charakterystyki magazynu lokalnego, aby ustalić, czy można ulepszyć sprzęt (np. zastąpić dysk twardy dyskiem SSD).
>
>  4. Zmień ustawienia usługi Site Recovery na serwerze przetwarzania, aby [zwiększyć przepustowość sieci na potrzeby replikacji](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

## <a name="next-steps"></a>Następne kroki
* [Analizuj wygenerowany raport](site-recovery-vmware-deployment-planner-analyze-report.md).
