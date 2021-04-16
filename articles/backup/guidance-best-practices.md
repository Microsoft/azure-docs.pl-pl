---
title: Wskazówki i najlepsze rozwiązania
description: Poznaj najlepsze rozwiązania i wskazówki dotyczące kopii zapasowej obciążenia w chmurze i obciążenia lokalnego w chmurze
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 14476533cf896434182e1d63f89c6a1279b36362
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519067"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>Tworzenie kopii zapasowych obciążeń w chmurze i obciążeń lokalnych w chmurze

## <a name="introduction"></a>Wprowadzenie

Azure Backup zapewnia kompleksową ochronę zasobów danych na platformie Azure za pomocą prostego, bezpiecznego i ekonomicznego rozwiązania, które nie wymaga infrastruktury zerowej. Jest to wbudowane rozwiązanie do ochrony danych na platformie Azure dla szerokiego zakresu obciążeń. Pomaga chronić obciążenia o kluczowym znaczeniu działające w chmurze i zapewnia, że kopie zapasowe są zawsze dostępne i zarządzane na dużą skalę w całej kopii zapasowej.

### <a name="intended-audience"></a>Docelowi odbiorcy

Głównymi odbiorcami docelowymi tego artykułu są administratorzy IT i aplikacji oraz osoby wdrażane w dużych i średnich organizacjach, które chcą poznać możliwości wbudowanej technologii ochrony danych platformy Azure, usługi Azure Backup oraz sposób efektywnego implementowania rozwiązań, które lepiej chronią wdrożenia. W tym artykule założono, że znasz podstawowe technologie platformy Azure, pojęcia związane z ochroną danych i masz doświadczenie w pracy z rozwiązaniem do tworzenia kopii zapasowych. Wskazówki w tym artykule mogą ułatwić projektowanie rozwiązania do tworzenia kopii zapasowych na platformie Azure przy użyciu ustalonych wzorców i uniknąć znanych pułapek.

### <a name="how-this-article-is-organized"></a>Jak zorganizowano ten artykuł

Chociaż ochrona infrastruktury i aplikacji na platformie Azure jest łatwa, to gdy upewnisz się, że bazowe zasoby platformy Azure są prawidłowo skonfigurowane i używane optymalnie, możesz przyspieszyć czas do wartości. Ten artykuł zawiera krótkie omówienie kwestii dotyczących projektowania i wskazówek dotyczących optymalnego konfigurowania Azure Backup wdrożenia. Analizuje podstawowe składniki (na przykład magazyn usługi Recovery Services, zasady kopii zapasowych) i pojęcia (na przykład ład) oraz sposób myślenia o nich i ich możliwościach za pomocą linków do szczegółowej dokumentacji produktu.

## <a name="architecture"></a>Architektura

![Architektura usługi Azure Backup](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>Pakiety robocze

Azure Backup umożliwia ochronę danych dla różnych obciążeń (lokalnych i w chmurze). Jest to bezpieczny i niezawodny wbudowany mechanizm ochrony danych na platformie Azure. Umożliwia bezproblemowe skalowanie ochrony w wielu obciążeniach bez konieczności zarządzania. Istnieje również wiele kanałów automatyzacji umożliwiających włączenie tej funkcji (za pośrednictwem programu PowerShell, interfejsu wiersza polecenia, Azure Resource Manager szablonów i interfejsów API REST).

* **Skalowalny, trwały i bezpieczny magazyn —** Azure Backup niezawodnego magazynu obiektów blob z wbudowanymi funkcjami zabezpieczeń i wysokiej dostępności. Możesz wybrać magazynY LRS, GRS lub RA-GRS dla danych kopii zapasowej.  

* **Integracja obciążeń natywnych —** usługa Azure Backup zapewnia natywną integrację z obciążeniami platformy Azure (maszynami SAP HANA, bazą danych SQL na platformie Azure, a nawet z maszynami Azure Files), bez konieczności zarządzania automatyzacją lub infrastrukturą w celu wdrażania agentów, pisania nowych skryptów lub aprowizowanie magazynu.

### <a name="data-plane"></a>Płaszczyzna danych

* **Zautomatyzowane zarządzanie magazynem** — Azure Backup aprowizowanie kont magazynu i zarządzanie nimi dla danych kopii zapasowej w celu zapewnienia ich skalowania w miarę rozwoju danych kopii zapasowej.

* **Ochrona przed złośliwym usuwaniem —** Chroń przed przypadkowymi i złośliwymi próbami usunięcia kopii zapasowych za pośrednictwem usuwania nie softu kopii zapasowych. Usunięte dane kopii zapasowej są przechowywane bezpłatnie przez 14 dni i umożliwiają ich odzyskanie po tym stanie.

* **Bezpieczne zaszyfrowane kopie zapasowe.** Azure Backup, że dane kopii zapasowej są przechowywane w bezpieczny sposób, korzystając z wbudowanych funkcji zabezpieczeń platformy Azure, takich jak RBAC platformy Azure i szyfrowanie.

* **Zarządzanie cyklem życia danych kopii zapasowej —** Azure Backup automatycznie czyści starsze dane kopii zapasowej w celu zachowania zgodności z zasadami przechowywania. Dane można również warstwieć z magazynu operacyjnego do magazynu magazynu.

### <a name="management-plane"></a>Płaszczyzna zarządzania

* **Kontrola dostępu** — magazyny (usługi Recovery Services i magazyny kopii zapasowych) zapewniają możliwości zarządzania i są dostępne za pośrednictwem usług Azure Portal, Centrum kopii zapasowych, pulpitów nawigacyjnych magazynu, zestawu SDK, interfejsu wiersza polecenia, a nawet interfejsów API REST. Jest to również granica RBAC platformy Azure, która umożliwia ograniczenie dostępu do kopii zapasowych tylko do autoryzowanych administratorów kopii zapasowych.

* **Zarządzanie zasadami** — Azure Backup w każdym magazynie określają, kiedy mają być wyzwalane kopie zapasowe i jak długo mają być przechowywane. Możesz również zarządzać tymi zasadami i stosować je do wielu elementów.

* **Monitorowanie i raportowanie** — Azure Backup integruje się z usługą Log Analytics i zapewnia możliwość wyświetlania raportów za pośrednictwem skoroszytów.

* **Zarządzanie migawkami** — Azure Backup migawki dla niektórych obciążeń natywnych platformy Azure (maszyn wirtualnych i Azure Files), zarządza tymi migawkami i umożliwia ich szybkie przywracanie. Ta opcja znacząco skraca czas odzyskiwania danych do oryginalnego magazynu.

## <a name="vault-considerations"></a>Zagadnienia dotyczące magazynu

Azure Backup używa magazynów (magazynów usług Recovery Services i backup) do organizowania kopii zapasowych i zarządzania nimi. Magazyny są również używane do przechowywania danych kopii zapasowej. Skuteczny projekt magazynu ułatwia organizacjom ustanowienie struktury do organizowania zasobów kopii zapasowych na platformie Azure i zarządzania nimi w celu wspierania priorytetów firmy. Podczas tworzenia magazynu należy wziąć pod uwagę następujące wskazówki:  

### <a name="align-to-subscription-design-strategy"></a>Dopasowanie do strategii projektowania subskrypcji

Ponieważ zakres magazynu jest ograniczony do subskrypcji, dopasuj projekt magazynu do  strategii projektowania subskrypcji, takiej jak strategia kategorii aplikacji, w której subskrypcje są oddzielone na podstawie określonych aplikacji lub usług lub wzdłuż linii archetypów aplikacji. Więcej informacji znajduje się w tym [artykule](/azure/cloud-adoption-framework/decision-guides/subscriptions/).

### <a name="single-or-multiple-vault"></a>Jeden lub wiele magazynów

Do organizowania kopii zapasowych i zarządzania nimi można użyć jednego magazynu lub wielu magazynów. Należy uwzględnić następujące wytyczne:

* Jeśli wszystkie obciążenia są zarządzane przez pojedynczą subskrypcję i pojedynczy zasób, możesz użyć jednego magazynu do monitorowania zasobów kopii zapasowych i zarządzania nimi.

* Jeśli obciążenia są rozłożone na subskrypcje, możesz utworzyć wiele magazynów, co najmniej jedną subskrypcję.
  * Centrum kopii zapasowych umożliwia zarządzanie wszystkimi zadaniami związanymi z usługą Backup w jednym okienku. [Więcej informacji można znaleźć tutaj]().
  * Widoki można dostosowywać za pomocą szablonów skoroszytów. Eksplorator usługi Backup jest jednym z takich szablonów dla maszyn wirtualnych platformy Azure. [Więcej informacji można znaleźć tutaj](monitor-azure-backup-with-backup-explorer.md).
  * Jeśli potrzebujesz spójnych zasad w wielu magazynach, możesz użyć zasad platformy Azure do propagowania zasad kopii zapasowych w wielu magazynach. Możesz napisać niestandardową [definicję Azure Policy,](../governance/policy/concepts/definition-structure.md) która używa efektu ["deployifnotexists"](../governance/policy/concepts/effects.md#deployifnotexists) do propagowania zasad kopii zapasowych w wielu magazynach. Tę definicję usługi Azure Policy można również przypisać do określonego zakresu (subskrypcji lub grupy zasobów), aby wdrożyć zasób "zasad tworzenia kopii zapasowych" we wszystkich magazynach usługi Recovery Services w zakresie przypisania Azure Policy odzyskiwania. [](../governance/policy/assign-policy-portal.md) Ustawienia zasad tworzenia kopii zapasowych (takie jak częstotliwość tworzenia kopii zapasowych, przechowywanie itp.) powinny być określane przez użytkownika jako parametry w Azure Policy kopii zapasowej.

* W miarę wzrostu zasięgu organizacji warto przenieść obciążenia między subskrypcjami z następujących powodów: dostosowanie do zasad tworzenia kopii zapasowych, konsolidacja magazynów, zmniejszenie nadmiarowości w celu obniżenia kosztów (przejście z magazynu GRS do magazynu LRS).  Azure Backup obsługuje przenoszenie magazynu usługi Recovery Services między subskrypcjami platformy Azure lub do innej grupy zasobów w ramach tej samej subskrypcji. [Więcej informacji można znaleźć tutaj](backup-azure-move-recovery-services-vault.md).

### <a name="review-default-settings"></a>Przeglądanie ustawień domyślnych

Przejrzyj ustawienia domyślne dla ustawień Typ replikacji magazynu i Ustawienia zabezpieczeń, aby spełnić wymagania przed skonfigurowaniem kopii zapasowych w magazynie.

* *Domyślny typ replikacji* magazynu to Geograficznie nadmiarowy (GRS). Po skonfigurowaniu kopii zapasowej opcja modyfikowania jest wyłączona. Wykonaj [następujące kroki,](backup-create-rs-vault.md#set-storage-redundancy) aby przejrzeć i zmodyfikować ustawienia.

* *Usuwanie nieumyślne* domyślnie ma wartość Włączone w nowo utworzonych magazynach, aby chronić dane kopii zapasowej przed przypadkowym lub złośliwym usunięciem. Wykonaj [następujące kroki,](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) aby przejrzeć i zmodyfikować ustawienia.

* *Przywracanie między regionami* umożliwia przywracanie maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest sparowany z platformą Azure. Ta opcja umożliwia przeprowadzenie awarii w celu spełnienia wymagań dotyczących inspekcji lub zgodności oraz przywrócenie maszyny wirtualnej lub jej dysku w przypadku awarii w regionie podstawowym. Magazyn CRR jest funkcją do wyboru dla dowolnego magazynu GRS. [Więcej informacji można znaleźć tutaj](backup-create-rs-vault.md#set-cross-region-restore).

* Przed zakończeniem projektu magazynu [](backup-support-matrix.md#vault-support) zapoznaj się z macierzami obsługi magazynu, aby zrozumieć czynniki, które mogą mieć wpływ na wybór projektu lub go ograniczyć.

## <a name="backup-policy-considerations"></a>Zagadnienia dotyczące zasad kopii zapasowych

Azure Backup policy ma dwa składniki: *Harmonogram* (kiedy należy wykonać kopię zapasową) i *Przechowywanie* (jak długo ma być zachowywana kopia zapasowa). Zasady można zdefiniować na podstawie typu danych, których kopię zapasową chcesz utworzyć, wymagań celu RTO/celu celu czasowego, wymagań dotyczących zgodności operacyjnej lub prawnej oraz typu obciążenia (na przykład maszyny wirtualnej, bazy danych, plików). [Więcej informacji można znaleźć tutaj](backup-architecture.md#backup-policy-essentials).

Podczas tworzenia zasad kopii zapasowych należy wziąć pod uwagę następujące wskazówki:

### <a name="schedule-considerations"></a>Zagadnienia dotyczące harmonogramu

* Rozważ zgrupowanie maszyn wirtualnych, które wymagają tego samego czasu rozpoczęcia harmonogramu, częstotliwości i ustawień przechowywania w ramach jednej zasady.

* Upewnij się, że zaplanowany czas rozpoczęcia tworzenia kopii zapasowej jest poza szczytowym czasem aplikacji produkcyjnej.

* Aby dystrybuować ruch kopii zapasowych, rozważ tworzenie kopii zapasowych różnych maszyn wirtualnych o różnych godzinach dnia i upewnij się, że te czasy nie nakładają się na siebie.

### <a name="retention-considerations"></a>Zagadnienia dotyczące przechowywania

* Przechowywanie krótkoterminowe może być "minuty" lub "codziennie". Przechowywanie punktów kopii zapasowej "co tydzień", "co miesiąc" lub "co rok" jest określane jako przechowywanie długoterminowe.

* Długoterminowe przechowywanie:
  * Planowane (wymagania dotyczące zgodności) — jeśli z wyprzedzeniem wiadomo, że dane są wymagane przez lata od bieżącego czasu, należy użyć przechowywania długoterminowego.
  * Nieplanowane (wymaganie na żądanie) — jeśli nie wiesz wcześniej, możesz użyć opcji na żądanie z określonymi niestandardowymi ustawieniami przechowywania (te niestandardowe ustawienia przechowywania nie mają wpływu na ustawienia zasad).

* Kopia zapasowa na żądanie z niestandardowym przechowywaniem — jeśli chcesz utworzyć kopię zapasową, która nie została zaplanowana za pomocą zasad tworzenia kopii zapasowych, możesz użyć kopii zapasowej na żądanie. Może to być przydatne do tworzenia kopii zapasowych, które nie pasują do zaplanowanej kopii zapasowej, lub do wykonywania szczegółowych kopii zapasowych (na przykład wielu kopii zapasowych maszyn wirtualnych IaaS dziennie, ponieważ zaplanowana kopia zapasowa zezwala tylko na jedną kopię zapasową dziennie). Należy pamiętać, że zasady przechowywania zdefiniowane w zaplanowanych zasadach nie mają zastosowania do kopii zapasowych na żądanie.

### <a name="optimize-backup-policy"></a>Optymalizowanie zasad kopii zapasowych

* W przypadku zmiany wymagań biznesowych może być konieczne wydłużenie lub skrócenie czasu przechowywania. Gdy to zrobisz, możesz oczekiwać następujących czynności:  
  * Jeśli okres przechowywania zostanie rozszerzony, istniejące punkty odzyskiwania zostaną oznaczone i zachowane zgodnie z nowymi zasadami.
  * Jeśli okres przechowywania zostanie skrócony, punkty odzyskiwania zostaną oznaczone do oczyszczenia w następnym zadaniu oczyszczania, a następnie usunięte.
  * Najnowsze reguły przechowywania mają zastosowanie do wszystkich punktów przechowywania (z wyjątkiem punktów przechowywania na żądanie). Jeśli więc okres przechowywania zostanie wydłużony (na przykład do 100 dni), to podczas wykonywania kopii zapasowej, po którym następuje zmniejszenie okresu przechowywania (na przykład z 100 dni do siedmiu dni), wszystkie dane kopii zapasowej zostaną zachowane zgodnie z ostatnim określonym okresem przechowywania (czyli 7 dni).

* Azure Backup zapewnia elastyczność zatrzymania ochrony kopii zapasowych i zarządzania *nimi:*
  * *Zatrzymaj ochronę i zachowaj dane kopii zapasowej.* Jeśli wycofywano lub zlikwidowano źródło danych (maszynę wirtualną, aplikację), ale chcesz zachować dane na potrzeby inspekcji lub zapewnienia zgodności, możesz użyć tej opcji, aby zatrzymać ochronę źródła danych i punktów odzyskiwania, których kopia zapasowa została w przyszłości zatrzymana. Następnie możesz przywrócić lub wznowić ochronę maszyny wirtualnej.
  * *Zatrzymaj ochronę i usuń dane kopii zapasowej.* Ta opcja spowoduje zatrzymanie ochrony maszyny wirtualnej przez wszystkie przyszłe zadania tworzenia kopii zapasowej i usunięcie wszystkich punktów odzyskiwania. Nie będzie można przywrócić maszyny wirtualnej ani użyć opcji Wznów tworzenie kopii zapasowej.

  * Jeśli wznowisz ochronę (źródła danych, które zostało zatrzymane z zachowaniem danych), będą stosowane reguły przechowywania. Wszystkie wygasłe punkty odzyskiwania zostaną usunięte (w zaplanowanym czasie).

* Przed ukończeniem projektowania zasad należy pamiętać o następujących czynnikach, które mogą mieć wpływ na wybór projektu.
  * Zakres zasad kopii zapasowych jest ograniczony do magazynu.
  * Istnieje limit liczby elementów na zasady (na przykład 100 maszyn wirtualnych). Aby skalować, można utworzyć zduplikowane zasady z tym samym lub różnymi harmonogramami.
  * Nie można selektywnie usunąć określonych punktów odzyskiwania.
  * Nie można całkowicie wyłączyć zaplanowanej kopii zapasowej i zachować źródła danych w stanie chronionym. Najmniejszą częstotliwością tworzenia kopii zapasowych, które można skonfigurować przy użyciu zasad, jest cotygodniowe tworzenie zaplanowanych kopii zapasowych. Alternatywą jest zatrzymanie ochrony z zachowaniem danych i włączenie ochrony za każdym razem, gdy chcesz utworzyć kopię zapasową, wykonać kopię zapasową na żądanie, a następnie wyłączyć ochronę, ale zachować dane kopii zapasowej. [Więcej informacji można znaleźć tutaj](backup-azure-manage-vms.md#stop-protecting-a-vm).

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Aby ułatwić ochronę danych kopii zapasowej i spełniać potrzeby firmy dotyczące zabezpieczeń, program Azure Backup zapewnia poufność, integralność i dostępność przed celowymi atakami i nadużyciami cennych danych i systemów. Należy wziąć pod uwagę następujące wytyczne dotyczące zabezpieczeń Azure Backup rozwiązania:

### <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

* Kontrola dostępu oparta na rolach (RBAC) platformy Azure umożliwia precyzyjne zarządzanie dostępem, podział obowiązków w ramach zespołu i udzielanie użytkownikom tylko dostępu niezbędnego do wykonywania ich zadań. [Więcej informacji można znaleźć tutaj](backup-rbac-rs-vault.md).

* Azure Backup udostępnia trzy wbudowane role do kontrolowania operacji zarządzania kopiami zapasowymi: Współautorzy kopii zapasowych, operatorzy i czytelnicy. [Więcej informacji można znaleźć tutaj](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions).

* Azure Backup ma kilka kontrolek zabezpieczeń wbudowanych w usługę, aby zapobiegać lukom w zabezpieczeniach, wykrywać je i reagować na nie (dowiedz się więcej)

* Konta magazynu używane przez magazyny usługi Recovery Services są izolowane i użytkownicy nie mogą uzyskiwać do nich dostępu w złośliwych celach. Dostęp jest dozwolony tylko za pośrednictwem Azure Backup zarządzania, takich jak przywracanie.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>Szyfrowanie danych podczas przesyłania i przechowywania

Szyfrowanie chroni dane i pomaga spełnić wymagania organizacji dotyczące zabezpieczeń i zgodności.

* Na platformie Azure dane w trakcie przesyłania między usługą Azure Storage i magazynem są chronione przez protokół HTTPS. Te dane pozostają w sieci platformy Azure.

* Dane kopii zapasowej są automatycznie szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Alternatywnie możesz użyć własnych kluczy, nazywanych również [kluczami zarządzanymi przez klienta.](encryption-at-rest-with-cmk.md)

* Azure Backup obsługuje tworzenie kopii zapasowych i przywracanie maszyn wirtualnych platformy Azure, które mają dyski systemu operacyjnego/danych zaszyfrowane za pomocą Azure Disk Encryption (ADE). [Więcej informacji można znaleźć tutaj](backup-azure-vms-encryption.md).

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>Ochrona danych kopii zapasowej przed niezamierzonym usunięciem

Azure Backup udostępnia funkcje zabezpieczeń, które pomagają chronić dane kopii zapasowej nawet po usunięciu. Jeśli użytkownik usunie kopię zapasową (maszyny wirtualnej, bazy danych programu SQL Server, udziału plików platformy Azure, bazy danych SAP HANA), dane kopii zapasowej zostaną zachowane przez 14 dodatkowych dni, co umożliwia odzyskiwanie tego elementu kopii zapasowej bez utraty danych. Dodatkowe 14-dniowe przechowywanie danych kopii zapasowej w stanie "usuwania nie soft" nie wiąże się z żadnymi kosztami. [Więcej informacji można znaleźć tutaj](backup-azure-security-feature-cloud.md).

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitorowanie i alerty dotyczące podejrzanych działań

Azure Backup wbudowane funkcje monitorowania i alertów do wyświetlania i konfigurowania akcji dla zdarzeń związanych z Azure Backup. [Więcej informacji można znaleźć tutaj](security-overview.md#monitoring-and-alerts-of-suspicious-activity).

### <a name="security-features-to-help-protect-hybrid-backups"></a>Funkcje zabezpieczeń, które ułatwiają ochronę hybrydowych kopii zapasowych

Azure Backup używa agenta usługi Microsoft Azure Recovery Services (MARS) do kopii zapasowej i przywracania plików, folderów oraz stanu woluminu lub systemu z komputera lokalnego na platformę Azure. Usługę MARS udostępnia teraz funkcje zabezpieczeń: hasło do szyfrowania przed przekazaniem i odszyfrowywaniem po pobraniu z usługi Azure Backup, usunięte dane kopii zapasowej są przechowywane przez dodatkowe 14 dni od daty usunięcia oraz operację krytyczną (np. zmiana hasła) mogą być wykonywane tylko przez użytkowników, którzy mają prawidłowe poświadczenia platformy Azure. [Więcej informacji można znaleźć tutaj](backup-azure-security-feature.md).

## <a name="network-considerations"></a>Kwestie dotyczące sieci

Azure Backup wymaga zmiany danych z obciążenia do magazynu usługi Recovery Services. Azure Backup udostępnia kilka możliwości ochrony danych kopii zapasowej przed nieumyślne narażenie na ataki typu man-in-the-middle w sieci. Należy uwzględnić następujące wytyczne:

### <a name="internet-connectivity"></a>Łączność z Internetem

* *Kopia zapasowa maszyny* wirtualnej platformy Azure — cała wymagana komunikacja i transfer danych między magazynem a usługą Azure Backup odbywa się w sieci platformy Azure bez konieczności uzyskiwania dostępu do sieci wirtualnej. Dlatego tworzenie kopii zapasowych maszyn wirtualnych platformy Azure umieszczonych wewnątrz zabezpieczonych sieci nie wymaga zezwalania na dostęp do żadnych ip lub FQDN.

* *SAP HANA baz danych na maszynie* wirtualnej platformy Azure SQL Server na maszynie wirtualnej platformy Azure — wymaga łączności z usługą Azure Backup, usługą Azure Storage i Azure Active Directory. Można to osiągnąć, używając prywatnych punktów końcowych lub zezwalając na dostęp do wymaganych publicznych adresów IP lub FQDN. Nie zezwalanie na odpowiednią łączność z wymaganymi usługami platformy Azure może prowadzić do błędów w operacjach, takich jak odnajdywanie bazy danych, konfigurowanie kopii zapasowych, wykonywanie kopii zapasowych i przywracanie danych. Aby uzyskać pełne wskazówki dotyczące sieci podczas korzystania z tagów sieciowej SAP HANA, zapory platformy Azure i serwera proxy HTTP, zapoznaj się z tymi artykułami [w](backup-sql-server-database-azure-vms.md#establish-network-connectivity) [SAP HANA](./backup-azure-sap-hana-database.md#establish-network-connectivity) SQL.

* *Hybrydowe* — agent MARS (Microsoft Azure Recovery Services) wymaga dostępu do sieci dla wszystkich krytycznych operacji — instalowania, konfigurowania, tworzenia kopii zapasowych i przywracania. Agent MARS może nawiązać połączenie z usługą Azure Backup za pośrednictwem usługi [Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) przy użyciu publicznej komunikacji [](install-mars-agent.md#private-endpoints) równorzędnej (dostępnej dla starych obwodów) i komunikacji równorzędnej firmy Microsoft, przy użyciu prywatnych punktów końcowych lub za pośrednictwem serwera [proxy/zapory](install-mars-agent.md#verify-internet-access)z odpowiednią kontrolą dostępu.

### <a name="private-endpoints-for-azure-backup"></a>Prywatne punkty końcowe dla Azure Backup

Prywatny [punkt końcowy platformy](../private-link/private-endpoint-overview.md) Azure to interfejs sieciowy, który łączy Cię prywatnie i bezpiecznie z usługą Azure Private Link. Azure Backup umożliwia bezpieczne tworzenie kopii zapasowych i przywracanie danych z magazynów usługi Recovery Services przy użyciu prywatnych punktów końcowych.

* Po włączeniu prywatnych punktów końcowych dla magazynu są one używane tylko do tworzenia kopii zapasowych i przywracania obciążeń SQL i SAP HANA na maszynie wirtualnej platformy Azure i kopii zapasowych agenta MARS.  Możesz również użyć magazynu do tworzenia kopii zapasowych innych obciążeń (nie będą one jednak wymagały prywatnych punktów końcowych). Oprócz tworzenia kopii zapasowych obciążeń SQL i SAP HANA kopii zapasowej przy użyciu agenta MARS, prywatne punkty końcowe są również używane do odzyskiwania plików w przypadku tworzenia kopii zapasowej maszyny wirtualnej platformy Azure. [Więcej informacji można znaleźć tutaj](private-endpoints.md#recommended-and-supported-scenarios).

* Azure Active Directory nie obsługuje obecnie prywatnych punktów końcowych. W związku z tym w przypadku wykonywania kopii zapasowych baz danych na maszynach wirtualnych platformy Azure i tworzenia kopii zapasowych przy użyciu agenta MARS konieczne będzie zezwolić na dostęp wychodzący z zabezpieczonej sieci i sieci FQNS wymagany przez usługę Azure Active Directory. Możesz również użyć tagów sieciowej grupy Azure Firewall tagów sieciowych, aby w odpowiednich przypadkach zezwalać na dostęp do usługi Azure AD. Dowiedz się więcej o [wymaganiach wstępnych tutaj.](./private-endpoints.md#before-you-start)

## <a name="governance-considerations"></a>Zagadnienia związane z nadzorem

Ład na platformie Azure jest wdrażany głównie [za Azure Policy](../governance/policy/overview.md) i [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md). [Azure Policy](../governance/policy/overview.md) umożliwia tworzenie i przypisywanie definicji zasad oraz zarządzanie nimi w celu wymuszania reguł dla zasobów. Ta funkcja zapewnia zgodność tych zasobów ze standardami firmy. [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) umożliwia śledzenie użycia chmury i wydatków na zasoby platformy Azure i innych dostawców usług w chmurze. Ponadto następujące narzędzia, takie jak [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) platformy Azure [Azure Advisor,](../advisor/advisor-overview.md)  odgrywają ważną rolę w procesie zarządzania kosztami.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure Backup obsługują dwa kluczowe scenariusze za pośrednictwem wbudowanych Azure Policy

* Upewnij się, że nowo utworzone maszyny o krytycznym znaczeniu dla firmy są automatycznie tworzone z właściwymi ustawieniami przechowywania. Azure Backup udostępnia wbudowane zasady (przy użyciu usługi Azure Policy), które można przypisać do wszystkich maszyn wirtualnych platformy Azure w określonej lokalizacji w ramach subskrypcji lub grupy zasobów. Po przypisaniu tych zasad do danego zakresu wszystkie nowe maszyny wirtualne utworzone w tym zakresie są automatycznie konfigurowane do tworzenia kopii zapasowych w istniejącym magazynie w tej samej lokalizacji i subskrypcji. Użytkownik może określić magazyn i zasady przechowywania, z którymi powinny być skojarzone maszyny wirtualne, których kopię zapasową należy skojarzyć. [Więcej informacji można znaleźć tutaj](backup-azure-auto-enable-backup.md).

* Upewnij się, że nowo utworzone magazyny mają włączoną diagnostykę do obsługi raportów. Często ręczne dodanie ustawienia diagnostycznego dla każdego magazynu może być uciążliwym zadaniem. Ponadto każdy nowo utworzony magazyn musi mieć włączone ustawienia diagnostyki, aby można było wyświetlać raporty dotyczące tego magazynu. Aby uprościć tworzenie ustawień diagnostycznych na dużą skalę (z usługą Log Analytics jako miejscem docelowym), usługa Azure Backup udostępnia wbudowane Azure Policy. Te zasady dodają ustawienie diagnostyczne la do wszystkich magazynów w każdej subskrypcji lub grupie zasobów. Poniższe sekcje zawierają instrukcje dotyczące korzystania z tych zasad. [Więcej informacji można znaleźć tutaj](azure-policy-configure-diagnostics.md).

### <a name="azure-backup-cost-considerations"></a>Azure Backup zagadnienia dotyczące kosztów

Możliwości Azure Backup zapewniają elastyczność efektywnego zarządzania kosztami i nadal spełniają wymagania biznesowe BCDR (ciągłość działalności biznesowej i odzyskiwanie po awarii). Należy uwzględnić następujące wytyczne:

* Skorzystaj z kalkulatora cen, aby oszacować i zoptymalizować koszty, dostosowując różne dźwignie. [Dowiedz się więcej tutaj](azure-backup-pricing.md)

* Eksplorator usługi Backup: użyj Eksplorator usługi Backup lub Raporty usługi Backup, aby zrozumieć i zoptymalizować wzrost magazynu kopii zapasowych, zatrzymując tworzenie kopii zapasowych dla obciążeń niekrytyczych lub usuniętych maszyn wirtualnych. Możesz uzyskać zagregowany widok całej majątku z perspektywy kopii zapasowej. Obejmuje to nie tylko informacje o elementach kopii zapasowej, ale również zasoby, które nie są skonfigurowane do tworzenia kopii zapasowej. Dzięki temu nigdy nie przeoczysz ochrony danych o krytycznym znaczeniu w rosnącym majątku, a kopie zapasowe są zoptymalizowane pod kątem obciążeń niekrytyczych lub usuniętych obciążeń.

* Optymalizowanie zasad kopii zapasowych
  * Optymalizowanie ustawień harmonogramu i przechowywania na podstawie archetypów obciążeń (na przykład krytycznych, niekrytyczych)
  * Optymalizowanie ustawień przechowywania pod kątem natychmiastowego przywracania
  * Wybierz odpowiedni typ kopii zapasowej, aby spełnić wymagania, przy jednoczesnym uwzględnieniu obsługiwanych typów kopii zapasowych (pełnych, przyrostowych, dzienników i różnicowych) przez obciążenie Azure Backup kopii zapasowych.

* Selektywne tworzenie kopii zapasowych dysków: wykluczanie dysku (funkcja w wersji zapoznawczej) zapewnia wydajny i opłacalny wybór selektywnego tworzenia kopii zapasowych krytycznych danych. Na przykład jeśli nie chcesz kopii zapasowej pozostałych dysków dołączonych do maszyny wirtualnej, możesz na przykład wrócić do kopii zapasowej tylko jednego dysku. Jest to również przydatne w przypadku wielu rozwiązań do tworzenia kopii zapasowych. Na przykład w przypadku tworzenia kopii zapasowych baz danych lub danych za pomocą rozwiązania do tworzenia kopii zapasowych obciążeń (bazy danych SQL Server w kopii zapasowej maszyny wirtualnej platformy Azure) i chcesz użyć kopii zapasowej na poziomie maszyny wirtualnej platformy Azure dla wybranych dysków.

* Azure Backup migawki maszyn wirtualnych platformy Azure i przechowuje je wraz z dyskami w celu przyspieszenia tworzenia punktów odzyskiwania i przyspieszenia operacji przywracania. Jest to określane jako natychmiastowe przywracanie. Domyślnie migawki natychmiastowego przywracania są przechowywane przez dwa dni. Ta funkcja umożliwia operację przywracania z tych migawek przez ograniczenie czasu przywracania. Skraca to czas potrzebny do przekształcenia i skopiowania danych z magazynu. W związku z tym zobaczysz koszty magazynowania, które odpowiadają migawce wykonanej w tym okresie. [Więcej informacji można znaleźć tutaj](backup-instant-restore-capability.md#configure-snapshot-retention).

* Azure Backup magazynu magazynu jest domyślnie ustawiona wartość Geograficznie nadmiarowa (GRS). Tej opcji nie można zmienić po ochronie elementów. Magazyn geograficznie nadmiarowy (GRS) zapewnia wyższy poziom trwałości danych niż magazyn lokalnie nadmiarowy (LRS), co pozwala na korzystanie z przywracania między regionami i zapewnia większe koszty. Zapoznaj się z różnicami między niższymi kosztami i wyższą trwałością danych i zdecyduj, co jest najlepsze dla Twojego scenariusza. [Dowiedz się więcej tutaj](backup-create-rs-vault.md#set-storage-redundancy)

* Jeśli chronisz zarówno obciążenie uruchomione wewnątrz maszyny wirtualnej, jak i samą maszynę wirtualną, sprawdź, czy ta podwójna ochrona jest potrzebna.

## <a name="monitoring-and-alerting-considerations"></a>Zagadnienia dotyczące monitorowania i alertów

Jako użytkownik lub administrator kopii zapasowej możesz monitorować wszystkie rozwiązania do tworzenia kopii zapasowych i być powiadamiane o ważnych scenariuszach. Ta sekcja zawiera szczegółowe informacje o możliwościach monitorowania i powiadamiania zapewnianych przez Azure Backup usługi.

### <a name="monitoring"></a>Monitorowanie

* Azure Backup wbudowane **monitorowanie zadań dla** operacji, takich jak konfigurowanie kopii zapasowej, tworzenia kopii zapasowej, przywracania, usuwania kopii zapasowej i tak dalej. Jest on ograniczony do magazynu i idealnie nadaje się do monitorowania pojedynczego magazynu. [Więcej informacji można znaleźć tutaj](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

* Jeśli musisz monitorować działania operacyjne na dużą skalę, program **Eksplorator usługi Backup** zagregowany widok całej kopii zapasowej, umożliwiając szczegółową analizę przechodzenia do szczegółów i rozwiązywanie problemów. Jest to wbudowany skoroszyt usługi Azure Monitor, który zapewnia jedną centralną lokalizację, która pomaga monitorować działania operacyjne w całej zasobie kopii zapasowej na platformie Azure, obejmujące dzierżawy, lokalizacje, subskrypcje, grupy zasobów i magazyny. [Więcej informacji można znaleźć tutaj](monitor-azure-backup-with-backup-explorer.md).
  * Umożliwia ona identyfikowanie zasobów, które nie są skonfigurowane do tworzenia kopii zapasowych, i zapewnianie, że nigdy nie przeocniesz ochrony danych o krytycznym znaczeniu w rosnącym majątku.
  * Pulpit nawigacyjny zawiera działania operacyjne z ostatnich siedmiu dni (maksymalnie). Jeśli musisz zachować te dane, możesz wyeksportować je jako plik programu Excel i zachować.
  * Jeśli jesteś użytkownikiem Azure Lighthouse, możesz wyświetlać informacje w wielu dzierżawach, włączając monitorowanie bez granic.

* Jeśli konieczne jest długoterminowe zachowanie i wyświetlenie działań operacyjnych, użyj programu **Raporty.** Częstym wymaganiem dla administratorów kopii zapasowych jest uzyskiwanie szczegółowych informacji o kopiach zapasowych na podstawie danych, które obejmują dłuższy czas. Przypadki użycia takiego rozwiązania obejmują:
  * Przydzielanie i prognozowanie używanego magazynu w chmurze.
  * Inspekcja kopii zapasowych i przywracania.
  * Identyfikowanie kluczowych trendów na różnych poziomach szczegółowości.

* W dodatku
  * Dane (na przykład zadania, zasady itp.) można wysyłać do obszaru **roboczego usługi Log Analytics.** Umożliwi to korzystanie z funkcji dzienników usługi Azure Monitor w celu umożliwienia korelacji danych z innymi danymi monitorowania zebranymi przez usługę Azure Monitor, skonsolidowania wpisów dzienników z wielu subskrypcji i dzierżaw platformy Azure w jednej lokalizacji w celu przeprowadzenia wspólnej analizy, używania zapytań dzienników do wykonywania złożonych analiz i uzyskania szczegółowych informacji na temat wpisów dziennika. [Więcej informacji można znaleźć tutaj](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace).
  * Dane można wysyłać do centrum zdarzeń w celu wysyłania wpisów poza platformę Azure, na przykład do rozwiązania SIEM innej firmy (zarządzanie informacjami i zdarzeniami zabezpieczeń) lub innego rozwiązania do analizy dzienników. [Więcej informacji można znaleźć tutaj](../azure-monitor/essentials/activity-log.md#send-to-azure-event-hubs).
  * Możesz wysłać dane do konta usługi Azure Storage, jeśli chcesz przechowywać dane dziennika dłużej niż 90 dni na czas inspekcji, analizy statycznej lub tworzenia kopii zapasowej. Jeśli musisz przechowywać zdarzenia tylko przez 90 dni lub mniej, nie musisz rejestrować archiwów na koncie magazynu, ponieważ zdarzenia dziennika aktywności są przechowywane na platformie Azure przez 90 dni. [Dowiedz się więcej](../azure-monitor/essentials/activity-log.md#send-to--azure-storage).

### <a name="alerting"></a>Generowanie alertów

* Alerty to przede wszystkim sposób na uzyskiwanie powiadomień w celu podjęcia odpowiednich działań. Sekcja Alerty kopii zapasowej zawiera alerty wygenerowane przez Azure Backup usługi.

* Azure Backup wbudowany mechanizm **powiadamiania** o alertach za pośrednictwem poczty e-mail w przypadku niepowodzeń, ostrzeżeń i operacji krytycznych. Można określić poszczególne adresy e-mail lub listy dystrybucyjne, które mają być powiadamiane po wygenerowaniu alertu. Możesz również wybrać, czy chcesz otrzymać powiadomienie dla każdego alertu, czy grupować je w godzinowy skrót, a następnie otrzymać powiadomienie.
  * Te alerty są definiowane przez usługę i zapewniają obsługę ograniczonych scenariuszy — niepowodzenia tworzenia/przywracania kopii zapasowych, zatrzymywanie ochrony przy zachowaniu danych/zatrzymywanie ochrony przy usuwaniu danych i tak dalej. [Więcej informacji można znaleźć tutaj](backup-azure-monitoring-built-in-monitor.md#alert-scenarios).
  * W przypadku wykonania destrukcyjnej operacji, takiej jak zatrzymanie ochrony za pomocą usuwania danych, jest wywoływany alert,  a do właścicieli subskrypcji, administratorów i współadministracyjnym jest wysyłana wiadomość e-mail, nawet jeśli nie skonfigurowano powiadomień dla magazynu usługi Recovery Services.
  * Niektóre obciążenia mogą generować wysoką częstotliwość awarii (na przykład SQL Server co 15 minut). Aby zapobiec przeciążeniom alertów wywoływanych dla każdego wystąpienia błędu, alerty są konsolidowane. [Więcej informacji można znaleźć tutaj](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts).
  * Wbudowanych alertów nie można dostosowywać i są one ograniczone do wiadomości e-mail zdefiniowanych w Azure Portal.

* Jeśli musisz utworzyć **alerty niestandardowe** (na przykład alerty o pomyślnych zadaniach), użyj usługi Log Analytics. W Azure Monitor można tworzyć własne alerty w obszarze roboczym usługi Log Analytics. Obciążenia hybrydowe (DPM/MABS) mogą również wysyłać dane do usługi LA i używać usługi LA do zapewnienia typowych alertów dla obciążeń obsługiwanych przez Azure Backup.

* Powiadomienia można również otrzymywać za pośrednictwem wbudowanych dzienników aktywności magazynu **usługi** Recovery Services. Jednak obsługuje ona ograniczone scenariusze i nie jest odpowiednia dla operacji, takich jak zaplanowane tworzenie kopii zapasowej, która lepiej pasuje do dzienników zasobów niż do dzienników aktywności. Aby dowiedzieć się więcej na temat tych ograniczeń i sposobu używania obszaru roboczego usługi Log Analytics do monitorowania i alertów na dużą skalę dla wszystkich obciążeń chronionych przez usługę Azure Backup, zapoznaj się z tym [artykułem.](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale)

## <a name="next-steps"></a>Następne kroki

Zalecamy przeczytanie następujących artykułów jako punktów wyjścia do korzystania z Azure Backup:

* [Azure Backup omówienie](backup-overview.md)
* [Często zadawane pytania](backup-azure-backup-faq.yml)
