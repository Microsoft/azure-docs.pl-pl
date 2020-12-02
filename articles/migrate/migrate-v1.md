---
title: Pracuj z poprzednią wersją Azure Migrate
description: Opisuje sposób pracy z poprzednią wersją Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c87290b36ec77e834a0d0fcd99e9a8bdb05675b5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494702"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Pracuj z poprzednią wersją Azure Migrate

Ten artykuł zawiera informacje dotyczące pracy z poprzednią wersją programu Azure Migrate. 


Istnieją dwie wersje usługi Azure Migrate:

- **Bieżąca wersja**: Użyj tej wersji do tworzenia projektów Azure Migrate, odnajdywania maszyn lokalnych i organizowania ocen i migracji. [Dowiedz się więcej](whats-new.md) na temat Nowości w tej wersji.
- **Poprzednia wersja**: Jeśli używasz starszej wersji Azure Migrate (obsługiwana jest tylko Ocena lokalnych maszyn wirtualnych programu VMware), należy teraz używać bieżącej wersji. Poprzednie projekty wersji są nazywane projektami klasycznymi w tym artykule. Jeśli nadal musisz używać Azure Migrate projektów utworzonych w poprzedniej wersji, to to, co możesz zrobić, i nie możesz tego zrobić:
    - Nie można już tworzyć projektów migracji.
    - Zalecamy, aby nie wykonywać nowych odnajdywania.
    - Nadal możesz uzyskiwać dostęp do istniejących projektów.
    - Nadal można uruchamiać oceny.
    

## <a name="upgrade-between-versions"></a>Uaktualnianie między wersjami

Nie można uaktualnić projektów lub składników w poprzedniej wersji do nowej wersji. Należy [utworzyć nowy projekt Azure Migrate](create-manage-projects.md)i dodać do niego [Narzędzia do oceny i migracji](./create-manage-projects.md) . Skorzystaj z samouczków, aby zrozumieć, jak korzystać z dostępnych narzędzi do oceny i migracji. Jeśli masz obszar roboczy Log Analytics dołączony do projektu klasycznego, możesz dołączyć go do projektu bieżącej wersji po usunięciu projektu klasycznego.

## <a name="find-projects-from-previous-version"></a>Znajdź projekty z poprzedniej wersji

Znajdź projekty z poprzedniej wersji w następujący sposób:

1. W Azure Portal > **wszystkie usługi**, Wyszukaj i wybierz **Azure Migrate**. 
2. Na pulpicie nawigacyjnym Azure Migrate istnieje powiadomienie i link umożliwiający dostęp do starych projektów Azure Migrate.
3. Kliknij link, aby otworzyć projekty klasyczne.

## <a name="delete-projects-from-previous-version"></a>Usuń projekty z poprzedniej wersji

Znajdź i Usuń projekty z poprzedniej wersji w następujący sposób:

1. W Azure Portal > **wszystkie usługi**, Wyszukaj i wybierz **Azure Migrate**. 
2. Na pulpicie nawigacyjnym Azure Migrate istnieje powiadomienie i link umożliwiający dostęp do starych projektów Azure Migrate.
3. Kliknij link, aby otworzyć projekty klasyczne.
4. Wybierz projekt, który chcesz usunąć i usunąć. 


## <a name="create-an-assessment"></a>Tworzenie oceny

Po odnalezieniu maszyn wirtualnych w portalu należy je pogrupować i utworzyć oceny.

- Natychmiast po odnalezieniu maszyn wirtualnych w portalu można od razu utworzyć jako oceny lokalne.
- W przypadku ocen opartych na wydajności zalecamy oczekiwanie co najmniej dnia przed utworzeniem oceny opartej na wydajności, aby uzyskać niezawodne zalecenia dotyczące rozmiaru.

Utwórz ocenę w następujący sposób:

1. Na stronie **Omówienie** projektu kliknij pozycję **+Utwórz ocenę**.
2. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.
3. Utwórz grupę i określ jej nazwę.
4. Wybierz maszyny wirtualne, które chcesz dodać do grupy.
5. Kliknij pozycję **Utwórz ocenę**, aby utworzyć grupę i ocenę.
6. Po utworzeniu oceny możesz wyświetlić ją w obszarze **Omówienie** > **Pulpit nawigacyjny**.
7. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.

Jeśli chcesz zaktualizować istniejącą ocenę przy użyciu najnowszych danych wydajności, możesz użyć polecenia **Oblicz ponownie** w ramach oceny, aby ją zaktualizować.

## <a name="review-an-assessment"></a>Przegląd oceny 

Ocena ma trzy etapy:

- Ocena zaczyna się od analizy gotowości, aby ustalić, czy maszyny są zgodne na platformie Azure.
- Ustalanie wielkości szacowania.
- Szacowany koszt miesięczny.

Komputer jest przenoszony tylko do późniejszego etapu, jeśli przekaże poprzednią. Na przykład jeśli komputer nie może sprawdzić przydatności, jest on oznaczony jako nieodpowiedni dla platformy Azure, a jego wielkość i koszt nie są wykonywane.


### <a name="review-azure-readiness"></a>Przegląd gotowości na platformę Azure

Widok gotowości platformy Azure w ocenie przedstawia stan gotowości każdej maszyny wirtualnej.

**Gotowości** | **Stan** | **Szczegóły**
--- | --- | ---
Gotowa na platformę Azure | Brak problemów ze zgodnością. Maszynę można migrować na platformę Azure i uruchamiać ją na platformie Azure z pełną pomocą techniczną platformy Azure. | W przypadku maszyn wirtualnych, które są gotowe do migracji, usługa Azure Migrate wyświetla zalecany rozmiar maszyny wirtualnej na platformie Azure.
Warunkowo gotowa na platformę Azure | Komputer może przeprowadzić rozruch na platformie Azure, ale może nie mieć pełnej pomocy technicznej platformy Azure. Na przykład maszyna ze starszą wersją systemu Windows Server, która nie jest obsługiwana na platformie Azure. | Azure Migrate objaśnia problemy z gotowością i zawiera czynności zaradcze.
Niegotowa na platformę Azure |  Maszyna wirtualna nie zostanie przeprowadzony na platformie Azure. Na przykład jeśli maszyna wirtualna ma dysk o pojemności ponad 4 TB, nie może być hostowana na platformie Azure. | Azure Migrate objaśnia problemy z gotowością i zawiera czynności zaradcze.
Nieznana gotowość | Azure Migrate nie może zidentyfikować gotowości platformy Azure, zwykle ponieważ dane nie są dostępne.


#### <a name="azure-vm-properties"></a>Właściwości maszyny wirtualnej platformy Azure
Gotowość uwzględnia wiele właściwości maszyn wirtualnych, aby określić, czy maszyna wirtualna może działać na platformie Azure.


**Właściwość** | **Szczegóły** | **Gotowości**
--- | --- | ---
**Typ rozruchu** | Obsługiwane przez system BIOS. Interfejs UEFI nie jest obsługiwany. | Warunkowo gotowe, jeśli typ rozruchu to UEFI.
**Rdzenie** | Komputery Core <= Maksymalna liczba rdzeni (128) obsługiwana przez maszynę wirtualną platformy Azure.<br/><br/> Jeśli historia wydajności jest dostępna, Azure Migrate traktuje wykorzystane rdzenie.<br/>Jeśli czynnik komfortu jest określony w ustawieniach oceny, Liczba użytych rdzeni jest mnożona przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii wydajności, Azure Migrate używa przyznanych rdzeni, bez zastosowania współczynnika komfortu. | Gotowe, jeśli jest mniejsze lub równe limitom.
**Pamięć** | Rozmiar pamięci maszyny <= Maksymalna ilość pamięci (3892 GB na platformie Azure M Series Standard_M128m &nbsp; <sup>2</sup>) dla maszyny wirtualnej platformy Azure. [Dowiedz się więcej](../virtual-machines/sizes.md).<br/><br/> Jeśli historia wydajności jest dostępna, Azure Migrate uważa wykorzystanie pamięci.<br/><br/>W przypadku określenia współczynnika komfortu wykorzystanie pamięci jest mnożone przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii, przydzieloną pamięć jest używana, bez zastosowania współczynnika komfortu.<br/><br/> | Gotowe, jeśli w ramach limitów.
**Dysk magazynu** | Przydzielony rozmiar dysku musi mieć wartość 4 TB (4096 GB) lub mniejszą.<br/><br/> Liczba dysków dołączonych do maszyny musi być 65 lub mniejsza, łącznie z dyskiem systemu operacyjnego. | Gotowe, jeśli w ramach limitów.
**Sieć** | Maszyna musi mieć dołączoną 32 lub mniej kart sieciowych. | Gotowe, jeśli w ramach limitów.

#### <a name="guest-operating-system"></a>System operacyjny gościa

Wraz z właściwościami maszyny wirtualnej Azure Migrate również sprawdzać system operacyjny gościa lokalnej maszyny wirtualnej w celu ustalenia, czy maszyna wirtualna może działać na platformie Azure.

- Azure Migrate uznaje system operacyjny określony w vCenter Server.
- Ponieważ odnajdywanie wykonywane przez Azure Migrate jest oparte na urządzeniu, nie ma możliwości sprawdzenia, czy system operacyjny działający na maszynie wirtualnej jest taki sam jak określony w vCenter Server.

Używana jest następująca logika.

**System operacyjny** | **Szczegóły** | **Gotowości**
--- | --- | ---
Windows Server 2016 i wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2012 R2 i wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2012 i wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2008 R2 i wszystkie SPs | Platforma Azure oferuje pełną pomoc techniczną.| Gotowa na platformę Azure
Windows Server 2008 (32-bitowe i 64-bit) | Platforma Azure oferuje pełną pomoc techniczną. | Gotowa na platformę Azure
Windows Server 2003, 2003 R2 | Poza wsparciem i potrzebna jest [niestandardowa umowa dotycząca pomocy technicznej](/troubleshoot/azure/virtual-machines/server-software-support) w celu uzyskania pomocy technicznej na platformie Azure. | Warunkowo gotowa na platformę Azure Rozważ uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.
Windows 2000, 98, 95, NT, 3,1, MS-DOS | Poza wsparciem. Komputer może przeprowadzić rozruch na platformie Azure, ale nie ma pomocy technicznej systemu operacyjnego na platformie Azure. | Warunkowo gotowa na platformę Azure zaleca się uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.
Klient z systemem Windows 7, 8 i 10 | Platforma Azure zapewnia pomoc techniczną tylko w przypadku [subskrypcji programu Visual Studio.](../virtual-machines/windows/client-images.md) | Warunkowo gotowa na platformę Azure
Windows 10 Pro Desktop | Platforma Azure zapewnia pomoc techniczną z [wielodostępnymi prawami hostingu.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Warunkowo gotowa na platformę Azure
Windows Vista, XP Professional | Poza wsparciem. Komputer może przeprowadzić rozruch na platformie Azure, ale nie ma pomocy technicznej systemu operacyjnego na platformie Azure. | Warunkowo gotowa na platformę Azure zaleca się uaktualnienie systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.
Linux | System Azure poświadcza te [systemy operacyjne Linux](../virtual-machines/linux/endorsed-distros.md). Inne systemy operacyjne Linux mogą zostać uruchomione na platformie Azure, ale zalecamy uaktualnienie systemu operacyjnego do wersji zatwierdzonej przed przeprowadzeniem migracji na platformę Azure. | Gotowe na platformę Azure, jeśli wersja jest zatwierdzona.<br/><br/>Warunkowo gotowe, jeśli wersja nie jest zatwierdzona.
Inne systemy operacyjne<br/><br/> Na przykład Oracle Solaris, Apple macOS itp., FreeBSD itp. | Na platformie Azure nie są zatwierdzona te systemy operacyjne. Komputer może przeprowadzić rozruch na platformie Azure, ale nie ma pomocy technicznej systemu operacyjnego na platformie Azure. | Warunkowo gotowa na platformę Azure zaleca się zainstalowanie obsługiwanego systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.  
System operacyjny określony jako **inny** w vCenter Server | W takim przypadku Azure Migrate nie może zidentyfikować systemu operacyjnego. | Nieznane gotowość. Upewnij się, że system operacyjny działający na maszynie wirtualnej jest obsługiwany na platformie Azure.
32-bitowe systemy operacyjne | Komputer może przeprowadzić rozruch na platformie Azure, ale platforma Azure może nie zapewniać pełnej pomocy technicznej. | Warunkowo gotowa na platformę Azure Rozważ uaktualnienie systemu operacyjnego maszyny z 32-bitowego systemu operacyjnego do 64-bitowego systemu operacyjnego przed przeprowadzeniem migracji na platformę Azure.


### <a name="review-sizing"></a>Przegląd rozmiarów

 Zalecenie dotyczące rozmiaru Azure Migrate zależy od kryterium ustalania rozmiaru określonego we właściwościach oceny.

- Jeśli ustalanie rozmiaru jest oparte na wydajności, zalecenie dotyczące rozmiaru traktuje historię wydajności maszyn wirtualnych (procesora i pamięci) oraz dysków (IOPS i przepływność).
- Jeśli kryterium ustalania rozmiaru jest "jako lokalne", zalecenie dotyczące rozmiaru na platformie Azure jest zależne od rozmiaru maszyny wirtualnej w środowisku lokalnym. Rozmiar dysku jest oparty na typie magazynu określonym we właściwościach oceny (domyślnie są to dyski w warstwie Premium). Azure Migrate nie uwzględnia danych wydajności dla maszyny wirtualnej i dysków.

### <a name="review-cost-estimates"></a>Przegląd szacowanych kosztów

Szacunkowe koszty przedstawiają łączny koszt obliczeń i magazynu na potrzeby uruchamiania maszyn wirtualnych na platformie Azure wraz ze szczegółami dla każdej maszyny.

- Oszacowania kosztów są obliczane przy użyciu zalecenia dotyczącego rozmiaru maszyny wirtualnej i jej dysków oraz właściwości oceny.
- Szacowany miesięczny koszt zasobów obliczeniowych i magazynowych jest agregowany dla wszystkich maszyn wirtualnych w grupie.
- Oszacowanie kosztów polega na uruchomieniu lokalnej maszyny wirtualnej jako maszyn wirtualnych usługi Azure Infrastructure as (IaaS). Azure Migrate nie uwzględnia kosztów platformy jako usługi (PaaS) lub oprogramowania jako usługi (SaaS).

### <a name="review-confidence-rating-performance-based-assessment"></a>Przegląd klasyfikacji zaufania (Ocena oparta na wydajności)

Każda Ocena oparta na wydajności jest skojarzona z oceną zaufania.

- Klasyfikacja zaufania mieści się w zakresie od jednej gwiazdki do pięciu gwiazdek (jedna gwiazdka to najniższy i pięć gwiazdek).
- Ocena zaufania jest przypisana do oceny na podstawie dostępności punktów danych potrzebnych do obliczenia oceny.
- Ocena zaufania do oceny pomaga oszacować niezawodność zaleceń dotyczących rozmiaru określanych przez usługę Azure Migrate.
- Ocena zaufania jest niedostępna dla "AS-IS" w przypadku ocen lokalnych.

W przypadku ustalania wielkości na podstawie wydajności Azure Migrate wymaga następujących czynności:
- Dane użycia procesora CPU.
- Dane pamięci maszyny wirtualnej.
- Dla każdego dysku podłączonego do maszyny wirtualnej potrzebuje danych o liczbie operacji we/wy na dysku.
- Dla każdej karty sieciowej podłączonej do maszyny wirtualnej Azure Migrate potrzebuje danych wejściowych/wyjściowych sieci.
- Jeśli którykolwiek z powyższych elementów nie jest dostępny, zalecenia dotyczące rozmiaru (i w ten sposób klasyfikacje zaufania) mogą być niezawodne.


W zależności od wartości procentowej dostępnych punktów danych w tabeli podsumowano możliwe klasyfikacje zaufania.

**Dostępność punktów danych** | **Ocena zaufania**
--- | ---
0%–20% | 1 gwiazdka
21%–40% | 2 gwiazdki
41%–60% | 3 gwiazdki
61%–80% | 4 gwiazdki
81%–100% | 5 gwiazdek


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Problemy z oceną wpływające na klasyfikacje zaufania

Ocena może nie mieć wszystkich dostępnych punktów danych z kilku powodów:

- Twoje środowisko nie było profilem na czas trwania oceny. Na przykład jeśli tworzysz ocenę z czasem trwania wydajności ustawionym na jeden dzień, musisz poczekać co najmniej dzień po rozpoczęciu odnajdywania lub wszystkie punkty danych do zebrania.
- Niektóre maszyny wirtualne zostały wyłączone w okresie, dla którego została obliczona Ocena. Jeśli dowolna maszyna wirtualna została wyłączona przez część czasu trwania, Azure Migrate nie może zbierać danych wydajności dla tego okresu.
- Niektóre maszyny wirtualne zostały utworzone w okresie obliczeń oceny. Jeśli na przykład utworzysz ocenę przy użyciu historii wydajności w ostatnim miesiącu, ale w danym tygodniu utworzysz wiele maszyn wirtualnych, historia wydajności nowych maszyn wirtualnych nie będzie dla całego czasu trwania.

> [!NOTE]
> Jeśli Ocena zaufania dowolnej oceny jest poniżej pięciu gwiazdek, zaczekaj co najmniej jeden dzień, aby profilować środowisko, a następnie ponownie Oblicz ocenę. Jeśli nie można określić, że rozmiar na podstawie wydajności nie może być niezawodny. Jeśli nie chcesz ponownie obliczać, zalecamy przełączenie do lokalnego rozmiaru, zmieniając właściwości oceny.



## <a name="create-groups-using-dependency-visualization"></a>Tworzenie grup przy użyciu wizualizacji zależności

Oprócz ręcznego tworzenia grup można tworzyć grupy za pomocą wizualizacji zależności.
- Ta metoda jest używana zwykle do oceny grup o wyższym poziomie zaufania przez wzajemne sprawdzanie zależności między maszynami, przed uruchomieniem oceny.
- Wizualizacja zależności może pomóc efektywnie zaplanować migrację na platformę Azure. Pomaga upewnić się, że nic nie pozostało w tle, a w przypadku migrowania do platformy Azure nie wystąpił nieoczekiwany problem.
- Można odkryć wszystkie systemy zależne, które muszą zostać zmigrowane razem i określić, czy uruchomiony system nadal obsługuje użytkowników, czy też jest kandydatem do likwidacji zamiast migracji.
- Azure Migrate używa rozwiązania Service Map w Azure Monitor, aby włączyć wizualizację zależności.

> [!NOTE]
> Wizualizacja zależności nie jest dostępna w Azure Government.

Aby skonfigurować wizualizację zależności, należy skojarzyć obszar roboczy Log Analytics z projektem Azure Migrate, zainstalować agentów na maszynach, dla których chcesz wizualizować zależności, a następnie utworzyć grupy przy użyciu informacji o zależnościach. 



### <a name="associate-a-log-analytics-workspace"></a>Kojarzenie obszaru roboczego Log Analytics

Aby użyć wizualizacji zależności, należy skojarzyć obszar roboczy Log Analytics z projektem migracji. Można utworzyć lub dołączyć obszar roboczy tylko w ramach tej samej subskrypcji, w której jest tworzony projekt migracji.

1. Aby dołączyć Log Analytics obszar roboczy do projektu, w obszarze **omówienie**> **Essentials** kliknij pozycję **wymaga konfiguracji**.
2. Można utworzyć nowy obszar roboczy lub dołączyć istniejący:
  - Aby utworzyć nowy obszar roboczy, określ nazwę. Obszar roboczy jest tworzony w regionie w tej samej lokalizacji [geograficznej platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) co projekt migracji.
  - Po dołączeniu istniejącego obszaru roboczego możesz wybrać wszystkie dostępne obszary robocze w tej samej subskrypcji co projekt migracji. Na liście są wyświetlane tylko te obszary robocze, które zostały utworzone w [obsługiwanym Service map regionie](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions). Aby dołączyć obszar roboczy, upewnij się, że masz dostęp do obszaru roboczego "czytelnik".

> [!NOTE]
> Nie można zmienić obszaru roboczego skojarzonego z projektem migracji.

### <a name="download-and-install-vm-agents"></a>Pobieranie i instalowanie agentów maszyn wirtualnych

Po skonfigurowaniu obszaru roboczego należy pobrać i zainstalować agentów na poszczególnych maszynach lokalnych, które chcesz oszacować. Ponadto, jeśli masz maszyny bez łączności z Internetem, musisz pobrać i zainstalować [bramę log Analytics](../azure-monitor/platform/gateway.md) na nich.

1. W obszarze **Przegląd** kliknij pozycję **Zarządzaj**  >  **maszynami**, a następnie wybierz wymagany komputer.
2. W kolumnie **zależności** kliknij pozycję **Zainstaluj agentów**.
3. Na stronie **zależności** Pobierz i zainstaluj Microsoft Monitoring Agent (MMA) i agenta zależności dla każdej maszyny wirtualnej, którą chcesz ocenić.
4. Skopiuj identyfikator i klucz obszaru roboczego. Są one potrzebne po zainstalowaniu MMA na maszynie lokalnej.

> [!NOTE]
> Aby zautomatyzować instalację agentów, można użyć narzędzia do wdrażania, takiego jak Configuration Manager lub narzędzia partnerskiego, takiego jak [Intigua](https://www.intigua.com/intigua-for-azure-migration), które zapewnia rozwiązanie do wdrażania agentów dla Azure Migrate.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Instalowanie agenta MMA na komputerze z systemem Windows

Aby zainstalować agenta na komputerze z systemem Windows:

1. Kliknij dwukrotnie pobranego agenta.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**. Na stronie **postanowienia licencyjne** kliknij przycisk **zgadzam** się, aby zaakceptować licencję.
3. W **folderze docelowym** Zachowaj lub zmodyfikuj domyślny Folder instalacji > **dalej**.
4. W obszarze **Opcje instalacji agenta** wybierz pozycję **Azure log Analytics**  >  **dalej**.
5. Kliknij przycisk **Dodaj** , aby dodać nowy obszar roboczy log Analytics. Wklej w obszarze Identyfikator i klucz obszaru roboczego skopiowane z portalu. Kliknij przycisk **Dalej**.

Agenta można zainstalować z poziomu wiersza polecenia lub przy użyciu metody zautomatyzowanej, takiej jak Configuration Manager. [Dowiedz się więcej](../azure-monitor/platform/log-analytics-agent.md#installation-options) na temat korzystania z tych metod w celu zainstalowania agenta MMA.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Instalowanie agenta MMA na komputerze z systemem Linux

Aby zainstalować agenta na komputerze z systemem Linux:

1. Przenieś odpowiedni zbiór (x86 lub x64) na komputer z systemem Linux przy użyciu protokołu SCP/SFTP.
2. Zainstaluj pakiet przy użyciu argumentu--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Dowiedz się więcej](../azure-monitor/platform/agents-overview.md#supported-operating-systems) o obsłudze systemu operacyjnego Linux przez program MMA.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Zainstaluj agenta MMA na komputerze monitorowanym przez Operations Manager

W przypadku maszyn monitorowanych przez program System Center Operations Manager w wersji 2012 R2 lub nowszej nie ma potrzeby instalowania agenta MMA. Service Map integruje się z Operations Manager MMA, aby zebrać niezbędne dane zależności. [Dowiedz się więcej](../azure-monitor/insights/service-map-scom.md#prerequisites). Agent zależności musi być zainstalowany.

### <a name="install-the-dependency-agent"></a>Instalowanie agenta zależności

1. Aby zainstalować agenta zależności na komputerze z systemem Windows, kliknij dwukrotnie plik Instalatora i postępuj zgodnie z instrukcjami kreatora.
2. Aby zainstalować agenta zależności na komputerze z systemem Linux, należy zainstalować program jako katalog główny przy użyciu następującego polecenia:

    ```sh InstallDependencyAgent-Linux64.bin```

- Dowiedz się więcej o [obsłudze agentów zależności](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) dla systemów operacyjnych Windows i Linux.
- [Dowiedz się więcej](../azure-monitor/insights/vminsights-enable-hybrid.md#dependency-agent) na temat sposobu instalowania agenta zależności za pomocą skryptów.

>[!NOTE]
> Artykuł Azure Monitor dla maszyn wirtualnych, do którego odwołuje się opis wymagań wstępnych systemu oraz metod wdrażania agenta zależności, dotyczą także rozwiązania Service Map.

### <a name="create-a-group-with-dependency-mapping"></a>Tworzenie grupy z mapowaniem zależności

1. Po zainstalowaniu agentów przejdź do portalu, a następnie kliknij pozycję **Zarządzaj**  >  **komputerami**.
2. Wyszukaj maszynę, na której zainstalowano agentów.
3. Kolumna **zależności** dla maszyny powinna teraz być wyświetlana jako **zależności widoku**. Kliknij kolumnę, aby wyświetlić zależności maszyny.
4. Mapa zależności dla maszyny zawiera następujące szczegóły:
    - Ruch przychodzący (klientów) i ruch wychodzący (serwery) TCP do/z komputera
        - Maszyny zależne, na których nie zainstalowano agenta MMA i zależności, są pogrupowane według numerów portów.
        - Maszyny zależne, na których zainstalowano program MMA i Agent zależności, są wyświetlane jako oddzielne pola.
    - Procesy działające na maszynie, można rozwinąć każde pole komputera, aby wyświetlić procesy
    - Wyświetlane są właściwości komputera, w tym nazwa FQDN, system operacyjny, adres MAC. Możesz kliknąć poszczególne maszyny, aby wyświetlić szczegóły.

4. Zależności dla różnych czasów trwania można wyświetlić, klikając czas trwania w etykiecie zakresu czasu. Domyślnie zakresem jest godzina. Możesz zmodyfikować zakres czasu lub określić datę początkową i końcową oraz czas trwania.

   > [!NOTE]
   >    Obsługiwany jest zakres czasu do godziny. Użyj dzienników Azure Monitor do [wykonywania zapytań o dane zależności](./how-to-create-group-machine-dependencies.md) przez dłuższy czas.

5. Po zidentyfikowaniu maszyn zależnych, które mają być pogrupowane, użyj kombinacji klawiszy CTRL i kliknięcia, aby wybrać wiele maszyn na mapie, a następnie kliknij pozycję **Grupuj maszyny**.
6. Określ nazwę grupy. Sprawdź, czy maszyny zależne są odnajdywane przez Azure Migrate.

    > [!NOTE]
    > Jeśli komputer zależny nie zostanie odnaleziony przez Azure Migrate, nie można dodać go do grupy. Aby dodać te maszyny do grupy, należy ponownie uruchomić proces odnajdywania z odpowiednim zakresem w vCenter Server i upewnić się, że komputer został odnaleziony przez Azure Migrate.  

7. Jeśli chcesz utworzyć ocenę dla tej grupy, zaznacz pole wyboru, aby utworzyć nową ocenę dla grupy.
8. Kliknij przycisk **OK** , aby zapisać grupę.

Po utworzeniu grupy zaleca się zainstalowanie agentów na wszystkich maszynach grupy i udoskonalanie grupy poprzez wizualizację zależności całej grupy.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Wykonywanie zapytań względem danych zależności z dzienników Azure Monitor

Dane zależności przechwycone przez Service Map są dostępne do wykonywania zapytań w obszarze roboczym Log Analytics skojarzonym z projektem Azure Migrate. [Dowiedz się więcej](../azure-monitor/insights/service-map.md#log-analytics-records) o tabelach danych Service map do wykonywania zapytań w dziennikach Azure monitor. 

Aby uruchomić zapytania Kusto:

1. Po zainstalowaniu agentów przejdź do portalu, a następnie kliknij pozycję **Przegląd**.
2. W obszarze **Przegląd** przejdź do sekcji **podstawowe** projektu i kliknij nazwę obszaru roboczego podaną obok **obszaru roboczego pakietu OMS**.
3. Na stronie obszar roboczy log Analytics kliknij pozycję **General**  >  **dzienniki** ogólne.
4. Napisz zapytanie, aby zebrać dane zależności przy użyciu dzienników Azure Monitor. Znajdź przykładowe zapytania w następnej sekcji.
5. Uruchom zapytanie, klikając polecenie Uruchom. 

[Dowiedz się więcej](../azure-monitor/log-query/log-analytics-tutorial.md) na temat pisania zapytań Kusto. 

### <a name="sample-azure-monitor-logs-queries"></a>Przykładowe zapytania dzienników Azure Monitor

Poniżej znajdują się przykładowe zapytania, których można użyć w celu wyodrębnienia danych zależności. Zapytania można modyfikować w celu wyodrębnienia preferowanych punktów danych. Wyczerpująca lista pól w rekordach danych zależności jest dostępna [tutaj](../azure-monitor/insights/service-map.md#log-analytics-records). Więcej przykładowych zapytań znajdziesz [tutaj](../azure-monitor/insights/service-map.md#sample-log-searches).

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Podsumuj połączenia przychodzące na zestawie maszyn

Rekordy w tabeli dotyczące metryk połączeń VMConnection nie reprezentują poszczególnych fizycznych połączeń sieciowych. Wiele połączeń sieci fizycznych jest zgrupowanych w połączenie logiczne. [Dowiedz się więcej](../azure-monitor/insights/service-map.md#connections) o tym, jak dane połączenia sieci fizycznej są agregowane w jednym rekordzie logicznym w VMConnection. 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Podsumowywanie ilości danych wysłanych i odebranych w połączeniach przychodzących między zestawem maszyn

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```


## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o](migrate-services-overview.md) najnowszej wersji programu Azure Migrate.