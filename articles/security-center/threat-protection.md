---
title: Ochrona przed zagrożeniami w usłudze Azure Security Center
description: W tym temacie opisano zasoby chronione za pomocą funkcji ochrony przed zagrożeniami Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: e2f72cc0ea6851caaf5d0db2f17f8e16473d420e
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667553"
---
# <a name="threat-protection-in-azure-security-center"></a>Ochrona przed zagrożeniami w usłudze Azure Security Center

Gdy Security Center wykryje zagrożenie w dowolnym obszarze środowiska, generuje alert. Te alerty opisują szczegóły zasobów, których to dotyczy, sugerowane kroki zaradcze, a w niektórych przypadkach opcja wyzwalająca aplikację logiki w odpowiedzi.

Ochrona przed zagrożeniami w Azure Security Center zapewnia kompleksową ochronę środowiska:

* **Ochrona przed zagrożeniami dla zasobów obliczeniowych platformy Azure**: maszyny z systemem Windows, komputery z systemem Linux, Azure App Service i kontenery platformy Azure

* **Ochrona przed zagrożeniami dla zasobów danych platformy Azure**: SQL Database i Azure Synapse Analytics (dawniej SQL Data Warehouse), Azure Storage i Azure Cosmos DB

* **Ochrona przed zagrożeniami dla warstw usług platformy Azure**: warstwa sieci platformy Azure, warstwa zarządzania azure (Azure Resource Manager) (wersja zapoznawcza) i Azure Key Vault (wersja zapoznawcza)

Czy alert jest generowany przez Security Center, czy odbierany przez Security Center z innego produktu zabezpieczeń, można go wyeksportować. Aby wyeksportować alerty do usługi Azure wskaźnikowej, wszelkich SIEM innych firm lub dowolnego innego narzędzia zewnętrznego, postępuj zgodnie z instrukcjami w temacie [Eksportowanie alertów do Siem](continuous-export.md). 

> [!NOTE]
> Alerty z różnych źródeł mogą mieć różne ilości czasu. Na przykład alerty wymagające analizy ruchu sieciowego mogą trwać dłużej niż alerty związane z podejrzanymi procesami uruchomionymi na maszynach wirtualnych.

> [!TIP]
> Aby włączyć funkcje ochrony przed zagrożeniami Security Center, należy zastosować warstwę cenową standardowa do subskrypcji zawierającej odpowiednie obciążenia.
>
> Ochronę przed zagrożeniami dla **kont usługi Azure Storage** można włączyć na poziomie subskrypcji lub na poziomie zasobu.
> Ochronę przed zagrożeniami dla **Azure SQL Database serwerów SQL** można włączyć na poziomie subskrypcji lub na poziomie zasobu.
> Ochronę przed zagrożeniami dla **Azure Database for MariaDB/MySQL/PostgreSQL** można włączyć tylko na poziomie zasobów.



## <a name="threat-protection-for-windows-machines"></a>Ochrona przed zagrożeniami dla maszyn z systemem Windows <a name="windows-machines"></a>

Azure Security Center integruje się z usługami platformy Azure w celu monitorowania i ochrony maszyn z systemem Windows. Security Center przedstawia alerty i sugestie dotyczące korygowania ze wszystkich tych usług w łatwym do użycia formacie.

* **Microsoft Defender Advanced Threat Protection (ATP)** <a name="windows-atp"></a> -Security Center rozszerza platformę ochrony obciążeń w chmurze przez integrację z usługą Microsoft Defender Advanced Threat Protection (ATP). Razem zapewniają kompleksowe możliwości wykrywania i reagowania punktów końcowych (EDR).

    > [!IMPORTANT]
    > Czujnik ATP programu Microsoft Defender jest automatycznie włączany na serwerach z systemem Windows, które używają Security Center.

    Gdy usługa Microsoft Defender ATP wykryje zagrożenie, wyzwala alert. Alert jest wyświetlany na pulpicie nawigacyjnym Security Center. Z poziomu pulpitu nawigacyjnego można przestawiać do konsoli usługi Microsoft Defender ATP i przeprowadzić szczegółowe badanie w celu odzyskania zakresu ataku. Aby uzyskać więcej informacji o usłudze Microsoft Defender ATP, zobacz Dołączanie [serwerów do usługi Microsoft Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* Wykrywanie ataków bezplikowych **Fileless attack detection** <a name="windows-fileless"></a> — Ataki bez plików wsuwają złośliwe ładunki do pamięci, aby uniknąć wykrywania przez techniki skanowania oparte na dyskach. Ładunek osoby atakującej będzie nadal utrzymywać się w pamięci procesów objętych naruszeniem i wykonuje szeroką gamę złośliwych działań.

    W przypadku wykrywania ataków bez plików, zautomatyzowane metody śledczej pamięci identyfikują narzędzia, techniki i zachowania ataku bez plików. To rozwiązanie okresowo skanuje maszynę w czasie wykonywania i wyodrębnia informacje bezpośrednio z pamięci procesów. Szczegółowe informacje dotyczące systemu Linux obejmują: 

    - Dobrze znane zestawy narzędzi i oprogramowanie do wyszukiwania kryptograficznego 
    - Wykryto kod powłoki, który jest niewielkim fragmentem kodu zwykle używanym jako ładunek w trakcie wykorzystywania luki w zabezpieczeniach oprogramowania.
    - Wstrzykiwano złośliwy plik wykonywalny w pamięci procesu

    Wykrywanie ataków bez plików generuje szczegółowe alerty zabezpieczeń zawierające opisy z dodatkowymi metadanymi procesów, takimi jak aktywność sieciowa. Przyspiesza to klasyfikacja alertu, korelację i czas odpowiedzi podrzędnej. Takie podejście uzupełnia rozwiązania EDR oparte na zdarzeniach i zapewnia zwiększony zakres wykrywania.

    Aby uzyskać szczegółowe informacje o alertach dotyczących wykrywania ataków bez plików, zobacz [tabelę referencyjną alertów](alerts-reference.md#alerts-windows).

> [!TIP]
> Alerty systemu Windows można symulować przez pobranie [Azure Security Center element PlayBook: alerty zabezpieczeń](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).






## <a name="threat-protection-for-linux-machines"></a>Ochrona przed zagrożeniami dla komputerów z systemem Linux <a name="linux-machines"></a>

Security Center zbiera rekordy inspekcji z maszyn z systemem Linux przy użyciu **inspekcji**, jednej z najpopularniejszych struktur inspekcji systemu Linux. Inspekcja w jądrze linii głównej. 

* **Alerty poddane inspekcji systemu Linux i integracja** <a name="linux-auditd"></a> z agentem log Analytics — System podlegający inspekcji składa się z podsystemu poziomu jądra, który jest odpowiedzialny za wywołania systemu monitorowania. Filtruje je według określonego zestawu reguł i zapisuje komunikaty dla nich w gnieździe. Security Center integruje funkcje z pakietu objętego inspekcją w ramach agenta Log Analytics. Ta Integracja umożliwia zbieranie zdarzeń inspekcji we wszystkich obsługiwanych dystrybucjach systemu Linux bez wymagań wstępnych.

    rekordy poddane inspekcji są zbierane, wzbogacane i agregowane do zdarzeń przy użyciu agenta Log Analytics agenta dla systemu Linux. Security Center ciągle dodaje nową analizę, która używa sygnałów systemu Linux do wykrywania złośliwych zachowań na maszynach w chmurze i lokalnych systemach Linux. Podobnie jak w przypadku funkcji systemu Windows, te analizy obejmują między podejrzanymi procesami, podejrzanych prób logowania, ładowanie modułu jądra i inne działania. Te działania mogą wskazywać, że komputer jest atakowany lub został naruszony.  

    Listę alertów systemu Linux można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-linux).

> [!TIP]
> Alerty systemu Linux można symulować przez pobranie [Azure Security Center element PlayBook: wykrywania systemu Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## <a name="threat-protection-for-azure-app-service"></a>Ochrona przed zagrożeniami dla Azure App Service <a name="app-services"></a>

> [!NOTE]
> Ta usługa nie jest obecnie dostępna w regionach platformy Azure dla instytucji rządowych i suwerennych.

Security Center używa skali chmury, aby identyfikować ataki ukierunkowane na aplikacje działające przez App Service. Osoby atakujące mogą wykrywać słabe i luki w zabezpieczeniach aplikacji sieci Web. Przed skierowaniem do określonych środowisk żądania do aplikacji uruchomionych na platformie Azure przechodzą przez kilka bram, gdzie są kontrolowane i rejestrowane. Te dane są następnie używane do identyfikowania luk w zabezpieczeniach i ataków oraz do uczenia się nowych wzorców, które będą używane później.

Korzystając z widoczności platformy Azure jako dostawcy chmury, Security Center analizuje App Service dzienników wewnętrznych w celu zidentyfikowania metodologii ataków na wiele obiektów docelowych. Na przykład metodologia obejmuje szerokie skanowanie i rozproszone ataki. Ten typ ataku zwykle pochodzi z małego podzbioru adresów IP i pokazuje wzorce przeszukiwania do podobnych punktów końcowych na wielu hostach. Ataki są wyszukiwaniem zagrożonej strony lub wtyczki i nie mogą być zidentyfikowane z punktu widzenia jednego hosta.

Jeśli używasz planu App Service opartego na systemie Windows, Security Center również ma dostęp do podstawowych piaskownic i maszyn wirtualnych. Wraz z danymi dzienników wymienionymi powyżej infrastruktura może powiedzieć historię, od nowego ataku w przypadku komputerów klienckich. W związku z tym nawet jeśli Security Center zostanie wdrożona po wykorzystaniu aplikacji sieci Web, może być możliwe wykrycie trwających ataków.

Listę alertów Azure App Service można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-azureappserv).

Aby uzyskać więcej informacji na temat planów App Service, zobacz [plany App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).





## <a name="threat-protection-for-containers"></a>Ochrona przed zagrożeniami dla kontenerów <a name="azure-containers"></a>

### <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna|
|Wpisaną|Warstwa Standardowa|
|Wymagane role i uprawnienia:|**Administrator zabezpieczeń** może odrzucić alerty.<br>**Czytelnik zabezpieczeń** może przeglądać wyniki.|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Nie](./media/icons/no-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]








## <a name="threat-protection-for-sql-database-and-azure-synapse-analytics-formerly-sql-data-warehouse"></a>Ochrona przed zagrożeniami dla SQL Database i usługi Azure Synapse Analytics (dawniej SQL Data Warehouse) <a name="data-sql"></a>

Zaawansowana ochrona przed zagrożeniami dla Azure SQL Database wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Zobaczysz alerty w przypadku podejrzanych działań bazy danych, potencjalnych luk w zabezpieczeniach lub ataków wstrzykiwania kodu SQL oraz nietypowego dostępu do bazy danych i wzorców zapytań.

Zaawansowana ochrona przed zagrożeniami dla Azure SQL Database i SQL jest częścią ujednoliconego pakietu [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) w celu uzyskania zaawansowanych możliwości zabezpieczeń SQL, obejmujących Azure SQL Database, wystąpienia zarządzane usługi Azure SQL, bazy danych usługi Azure Synapse Analytics (dawniej SQL Data Warehouse) i serwery SQL na platformie Azure Virtual Machines.

Aby uzyskać więcej informacji, zobacz:

* [Jak włączyć zaawansowaną ochronę przed zagrożeniami dla Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Jak włączyć zaawansowaną ochronę przed zagrożeniami dla serwerów SQL na platformie Azure Virtual Machines](security-center-iaas-advanced-data.md)
* [Lista alertów dotyczących ochrony przed zagrożeniami dla SQL Database i usługi Azure Synapse Analytics (dawniej SQL Data Warehouse)](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Ochrona przed zagrożeniami dla usługi Azure Storage <a name="azure-storage"></a>

### <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|[BLOB Storage](https://azure.microsoft.com/services/storage/blobs/) (ogólna dostępność)<br>[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (wersja zapoznawcza)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (wersja zapoznawcza)|
|Wpisaną|Warstwa Standardowa|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) US Gov<br>![Nie](./media/icons/no-icon.png) Chiny gov, inne gov|
|||


### <a name="whats-protected"></a>Co jest chronione?

Ochrona przed zagrożeniami dla usługi Azure Storage wykrywa potencjalnie szkodliwe działanie na kontach usługi Azure Storage. Dane można chronić, niezależnie od tego, czy są przechowywane jako kontenery obiektów blob, udziały plików czy jeziora danych.

Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń *bez* konieczności posiadania eksperta zabezpieczeń oraz zarządzania systemami monitorowania zabezpieczeń.

### <a name="what-kind-of-alerts-does-threat-protection-for-azure-storage-provide"></a>Jakiego rodzaju alerty zapewnia ochrona przed zagrożeniami dla usługi Azure Storage?

Alerty zabezpieczeń są wyzwalane w przypadku:

- **Podejrzane działanie** — na przykład dostęp do konta magazynu powiodło się z adresu IP, który jest znany jako aktywny węzeł zakończenia tor
- **Nietypowe zachowanie** — na przykład zmiany w wzorcu dostępu do konta magazynu
- **Przekazane potencjalne złośliwe oprogramowanie** — analiza reputacji skrótu wskazuje, że przekazany plik zawiera złośliwe oprogramowanie

Alerty obejmują szczegółowe informacje o zdarzeniu, które je wywołały, a także zalecenia dotyczące sposobu badania i korygowania zagrożeń.

### <a name="what-is-hash-reputation-analysis-for-malware"></a>Co to jest analiza reputacji w przypadku złośliwego oprogramowania?

Aby określić, czy przekazany plik jest podejrzany, ochrona przed zagrożeniami dla usługi Azure Storage używa analizy reputacji skrótu obsługiwanej przez usługę [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Narzędzia ochrony przed zagrożeniami nie skanują przekazanych plików, a zamiast nich sprawdzają dzienniki magazynu i porównują skróty nowo przekazanych plików z tymi znanymi wirusami, koniami trojańskimi, programami szpiegującymi i oprogramowaniem wymuszającego okup. 

W przypadku podejrzenia, że plik zawiera złośliwe oprogramowanie, Security Center wyświetla alert i opcjonalnie może wysłać wiadomość e-mail do właściciela magazynu w celu zatwierdzenia, aby usunąć podejrzany plik. Aby skonfigurować to automatyczne usuwanie plików, których analiza reputacji jest określona, zawiera złośliwe oprogramowanie, wdróż [automatyzację przepływu pracy, aby wyzwolić alerty zawierające "potencjalne złośliwe oprogramowanie przekazane do konta magazynu"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).



### <a name="next-steps"></a>Następne kroki 

Aby uzyskać szczegółowe informacje o cenach, w tym bezpłatną 30-dniową wersję próbną, zobacz [stronę z cennikiem Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Aby uzyskać więcej informacji, zobacz:

* [Jak włączyć zaawansowaną ochronę przed zagrożeniami dla usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Lista alertów dotyczących ochrony przed zagrożeniami dla usługi Azure Storage](alerts-reference.md#alerts-azurestorage)
* [Możliwości analizy zagrożeń firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)

> [!TIP]
> Alerty magazynu można symulować, postępując zgodnie z instrukcjami zawartymi w [tym wpisie w blogu](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).







## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Ochrona przed zagrożeniami dla Azure Cosmos DB (wersja zapoznawcza) <a name="cosmos-db"></a>

Alerty Azure Cosmos DB są generowane przez nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont Azure Cosmos DB lub ich wykorzystania.

Aby uzyskać więcej informacji, zobacz:

* [Zaawansowana ochrona przed zagrożeniami dla Azure Cosmos DB (wersja zapoznawcza)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Lista alertów dotyczących ochrony przed zagrożeniami dla Azure Cosmos DB (wersja zapoznawcza)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Ochrona przed zagrożeniami dla warstwy sieci platformy Azure <a name="network-layer"></a>

Security Center analiza warstwy sieciowej opiera się na przykładowych [danych IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), które są nagłówkami pakietów zebranymi przez routery podstawowe platformy Azure. W oparciu o te strumieniowe źródło danych Security Center korzysta z modeli uczenia maszynowego do identyfikowania i flagowania złośliwych działań w zakresie ruchu. Security Center używa również bazy danych Microsoft Threat Intelligence do wzbogacania adresów IP.

Niektóre konfiguracje sieci mogą ograniczać Security Center generowania alertów dotyczących podejrzanych działań w sieci. Aby Security Center wygenerować alerty sieciowe, upewnij się, że:

- Maszyna wirtualna ma publiczny adres IP (lub znajduje się w usłudze równoważenia obciążenia z publicznym adresem IP).

- Ruch wychodzący z sieci maszyny wirtualnej nie jest blokowany przez rozwiązanie identyfikatorów zewnętrznych.

- Twoja maszyna wirtualna ma przypisany ten sam adres IP przez całą godzinę, w której wystąpiła podejrzana komunikacja. Dotyczy to również maszyn wirtualnych utworzonych w ramach usługi zarządzanej (na przykład AKS, datakosteks).

Aby uzyskać listę alertów warstwy sieci platformy Azure, zobacz [tabelę referencyjną alertów](alerts-reference.md#alerts-azurenetlayer).




## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Ochrona przed zagrożeniami dla warstwy zarządzania Azure (Azure Resource Manager) (wersja zapoznawcza)<a name ="management-layer"></a>

Warstwa ochrony Security Center oparta na Azure Resource Manager jest obecnie dostępna w wersji zapoznawczej.

Security Center oferuje dodatkową warstwę ochrony przy użyciu zdarzeń Azure Resource Manager, które są traktowane jako płaszczyzny kontroli dla platformy Azure. Analizując Azure Resource Manager rekordy, Security Center wykrywa nietypowe lub potencjalnie szkodliwe operacje w środowisku subskrypcji platformy Azure.

Aby uzyskać listę alertów Azure Resource Manager (wersja zapoznawcza), zobacz [tabelę referencyjną alertów](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Niektóre z powyższych analiz są obsługiwane przez Microsoft Cloud App Security. Aby skorzystać z tych analiz, należy aktywować licencję Cloud App Securityową. Jeśli masz licencję na Cloud App Security, te alerty są domyślnie włączone. Aby wyłączyć alerty:
>
> 1. W menu Security Center wybierz pozycję **cennik & ustawienia**.
> 1. Wybierz subskrypcję, którą chcesz zmienić.
> 1. Wybierz pozycję **wykrywanie zagrożeń**.
> 1. Wyczyść pole wyboru **zezwalaj Microsoft Cloud App Security na dostęp do danych**i wybierz pozycję **Zapisz**.

>[!NOTE]
>Security Center przechowuje dane klienta związane z zabezpieczeniami w tym samym miejscu geograficznym, co jego zasób. Jeśli firma Microsoft jeszcze nie wdrożona Security Center w lokalizacji geograficznej zasobu, przechowuje dane w Stany Zjednoczone. Gdy Cloud App Security jest włączona, te informacje są przechowywane zgodnie z zasadami lokalizacji geograficznej Cloud App Security. Aby uzyskać więcej informacji, zobacz [Magazyn danych dla usług nieregionalnych](https://azuredatacentermap.azurewebsites.net/).

1. Ustaw obszar roboczy, w którym jest instalowany Agent. Upewnij się, że obszar roboczy znajduje się w tej samej subskrypcji, której używasz w Security Center i że masz uprawnienia do odczytu/zapisu w obszarze roboczym.

1. Ustaw warstwę cenową standardowa i wybierz pozycję **Zapisz**.






## <a name="threat-protection-for-azure-key-vault-preview"></a>Ochrona przed zagrożeniami dla Azure Key Vault (wersja zapoznawcza)<a name="azure-keyvault"></a>

> [!NOTE]
> Ta usługa nie jest obecnie dostępna w regionach platformy Azure dla instytucji rządowych i suwerennych.

Azure Key Vault to usługa w chmurze, która chroni klucze szyfrowania i wpisy tajne, takie jak certyfikaty, ciągi połączeń i hasła. 

Azure Security Center obejmuje platformę Azure — natywną, zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault, zapewniając dodatkową warstwę analizy zabezpieczeń. Security Center wykrywa nietypowe i potencjalnie szkodliwe próby dostępu do kont Key Vault lub korzystania z nich. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń bez konieczności postanowień eksperta w zakresie bezpieczeństwa, bez konieczności zarządzania systemami monitorowania zabezpieczeń innych firm.  

W przypadku wystąpienia nietypowych działań Security Center wyświetla alerty i opcjonalnie wysyła je za pośrednictwem poczty e-mail do administratorów subskrypcji. Te alerty obejmują szczegóły podejrzanego działania i zalecenia dotyczące sposobu badania i korygowania zagrożeń. 

Listę alertów Azure Key Vault można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-azurekv).





## <a name="threat-protection-for-other-microsoft-services"></a>Ochrona przed zagrożeniami dla innych usług firmy Microsoft <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Ochrona przed zagrożeniami dla usługi Azure WAF <a name="azure-waf"></a>

Usługa Azure Application Gateway oferuje zaporę aplikacji internetowej (WAF), która zapewnia scentralizowaną ochronę aplikacji internetowych przed typowymi programami wykorzystującymi luki i lukami w zabezpieczeniach.

Aplikacje sieci Web są coraz bardziej przeznaczone dla złośliwych ataków wykorzystujących często znane luki w zabezpieczeniach. Application Gateway WAF opiera się na podstawowym zestawie reguł 3,0 lub 2.2.9 w projekcie zabezpieczeń aplikacji sieci Web. WAF jest automatycznie aktualizowana w celu ochrony przed nowymi lukami w zabezpieczeniach. 

Jeśli masz licencję na usługę Azure WAF, alerty WAF są przesyłane strumieniowo do Security Center bez konieczności dodatkowej konfiguracji. Aby uzyskać więcej informacji na temat alertów wygenerowanych przez WAF, zobacz [zasady i reguły reguł KSR aplikacji sieci Web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Ochrona przed zagrożeniami dla Azure DDoS Protection <a name="azure-ddos"></a>

Ataki rozproszonego typu "odmowa usługi" (DDoS) są znane, aby można je było łatwo wykonywać. Stają się one doskonałym problemem z zabezpieczeniami, szczególnie w przypadku przeniesienia aplikacji do chmury. 

Atak DDoS próbuje wymusić wyczerpanie zasobów aplikacji, co sprawia, że aplikacja jest niedostępna dla uprawnionych użytkowników. Ataki DDoS mogą wskazywać każdy punkt końcowy, który można uzyskać za pomocą Internetu.

Aby chronić przed atakami DDoS, Kup licencję na Azure DDoS Protection i upewnij się, że korzystasz z najlepszych rozwiązań dotyczących projektowania aplikacji. DDoS Protection oferuje różne warstwy usług. Aby uzyskać więcej informacji, zobacz [Azure DDoS Protection przegląd](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Listę alertów Azure DDoS Protection można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat alertów zabezpieczeń z tych funkcji ochrony przed zagrożeniami, zobacz następujące artykuły:

* [Tabela referencyjna dla wszystkich alertów Azure Security Center](alerts-reference.md)
* [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](security-center-alerts-overview.md)
* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Eksportowanie alertów zabezpieczeń i zaleceń (wersja zapoznawcza)](continuous-export.md)