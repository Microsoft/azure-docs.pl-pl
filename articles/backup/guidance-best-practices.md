---
title: Wskazówki i najlepsze rozwiązania
description: Poznaj najlepsze rozwiązania i wskazówki dotyczące tworzenia kopii zapasowych obciążeń w chmurze i lokalnych w chmurze
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 7b65556d8dd9b5b12e8da25055f6e39732c83afd
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258765"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>Tworzenie kopii zapasowych obciążeń w chmurze i lokalnych w chmurze

## <a name="introduction"></a>Wprowadzenie

Azure Backup Kompleksowa ochrona zasobów danych na platformie Azure za pomocą prostego, bezpiecznego i ekonomicznego rozwiązania, które wymaga zastosowania infrastruktury zerowej. Jest to Azure's wbudowane rozwiązanie do ochrony danych dla szerokiego zakresu obciążeń. Ułatwia ona ochronę obciążeń o znaczeniu krytycznym działającym w chmurze i gwarantuje, że kopie zapasowe są zawsze dostępne i zarządzane na dużą skalę w całej kopii zapasowej.

### <a name="intended-audience"></a>Docelowi odbiorcy

Głównymi docelowymi odbiorcami tego artykułu są jej Administratorzy aplikacji oraz realizatorów dużych i średnich organizacji, którzy chcą poznać możliwości wbudowanej technologii ochrony danych platformy Azure, Azure Backup i sposobu wydajnego wdrażania rozwiązań, które lepiej chronią Twoje wdrożenia. W tym artykule założono, że znasz podstawowe technologie platformy Azure, koncepcje ochrony danych i masz doświadczenie w pracy z rozwiązaniem tworzenia kopii zapasowej. Wskazówki omówione w tym artykule ułatwiają projektowanie rozwiązania do tworzenia kopii zapasowych na platformie Azure przy użyciu ustalonych wzorców i unikanie znanych pułapek.

### <a name="how-this-article-is-organized"></a>Jak jest zorganizowany ten artykuł

Chociaż możesz łatwo rozpocząć ochronę infrastruktury i aplikacji na platformie Azure, po upewnieniu się, że bazowe zasoby platformy Azure są prawidłowo skonfigurowane i są używane optymalnie, możesz skrócić czas do wartości. Ten artykuł zawiera krótkie omówienie zagadnień dotyczących projektowania oraz wskazówki dotyczące optymalnego konfigurowania wdrożenia Azure Backup. Analizuje podstawowe składniki (na przykład magazyn Recovery Services, zasady tworzenia kopii zapasowych) i koncepcje (na przykład ładu) oraz sposób ich działania i możliwości z linkami do szczegółowej dokumentacji produktu.

## <a name="architecture"></a>Architektura

![Architektura usługi Azure Backup](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>Obciążenia

Azure Backup umożliwia ochronę danych w różnych obciążeniach (lokalnie i w chmurze). Jest to bezpieczny i niezawodny wbudowany mechanizm ochrony danych na platformie Azure. Może bezproblemowo skalować ochronę wielu obciążeń bez konieczności zarządzania przez Ciebie. Istnieje wiele kanałów automatyzacji, które umożliwiają włączenie tej funkcji (za pośrednictwem programu PowerShell, interfejsu wiersza polecenia, szablonów Azure Resource Manager i interfejsów API REST).

* **Skalowalny, trwały i bezpieczny magazyn —** Azure Backup używa niezawodnego magazynu obiektów blob z wbudowanymi funkcjami zabezpieczeń i wysokiej dostępności. Dla danych kopii zapasowej można wybrać magazyny LRS, GRS lub RA-GRS.  

* **Natywna integracja obciążeń —** Azure Backup zapewnia natywną integrację z obciążeniami platformy Azure (maszynami wirtualnymi, SAP HANA, SQL na maszynach wirtualnych platformy Azure i nawet Azure Files) bez konieczności zarządzania automatyzacją lub infrastrukturą w celu wdrażania agentów, pisania nowych skryptów lub udostępniania magazynu.

### <a name="data-plane"></a>Płaszczyzna danych

* **Automatyczne zarządzanie magazynem** — Azure Backup automatyzuje aprowizacji i zarządzanie kontami magazynu dla danych kopii zapasowej, aby zapewnić ich skalowanie w miarę wzrostu ilości danych kopii zapasowej.

* **Złośliwa ochrona usuwania —** Chroń przed przypadkową i złośliwą próbą usuwania kopii zapasowych za pomocą nietrwałego usuwania kopii zapasowych. Usunięte dane kopii zapasowej są przechowywane przez 14 dni bezpłatnie i umożliwiają ich odzyskanie z tego stanu.

* **Bezpieczne szyfrowane kopie zapasowe —** Azure Backup gwarantuje, że dane kopii zapasowej są przechowywane w bezpieczny sposób, wykorzystując wbudowane funkcje zabezpieczeń platformy Azure, takie jak RBAC i szyfrowanie platformy Azure.

* **Zarządzanie cyklem życia danych kopii zapasowych —** Azure Backup automatycznie czyści starsze dane kopii zapasowej, aby zachować zgodność z zasadami przechowywania. Możesz również warstwy danych z magazynu operacyjnego do magazynu magazynów.

### <a name="management-plane"></a>Płaszczyzna zarządzania

* **Kontrola dostępu** — magazyny (Recovery Services i magazyny kopii zapasowych) zapewniają możliwości zarządzania i są dostępne za pośrednictwem Azure Portal, centrum kopii zapasowych, pulpitów nawigacyjnych magazynu, zestawu SDK, interfejsu wiersza polecenia, a nawet interfejsów API REST. Jest to również granica RBAC na platformie Azure, zapewniając możliwość ograniczenia dostępu do kopii zapasowych tylko dla autoryzowanych administratorów kopii zapasowych.

* **Zarządzanie zasadami** — zasady Azure Backup w ramach każdego magazynu definiują, kiedy kopie zapasowe mają być wyzwalane oraz jak długo muszą być przechowywane. Można także zarządzać tymi zasadami i stosować je w wielu elementach.

* **Monitorowanie i raportowanie** — Azure Backup integruje się z log Analytics i zapewnia możliwość wyświetlania raportów za pośrednictwem skoroszytów.

* **Zarządzanie migawkami** — Azure Backup wykonuje migawki niektórych obciążeń natywnych platformy Azure (maszyn wirtualnych i Azure Files), zarządza tymi migawkami i umożliwia szybkie przywracanie z nich. Ta opcja znacząco skraca czas na odzyskanie danych do oryginalnego magazynu.

## <a name="vault-considerations"></a>Zagadnienia dotyczące magazynu

Azure Backup używa magazynów (Recovery Services i magazynów kopii zapasowych) do organizowania kopii zapasowych i zarządzania nimi. Używa również magazynów do przechowywania danych kopii zapasowej. Efektywny projekt magazynu ułatwia organizacjom stworzenie struktury do organizowania zasobów kopii zapasowych i zarządzania nimi na platformie Azure w celu zapewnienia obsługi priorytetów firmy. Podczas tworzenia magazynu należy wziąć pod uwagę następujące wytyczne:  

### <a name="align-to-subscription-design-strategy"></a>Dopasuj do strategii projektowania subskrypcji

Ponieważ magazyn jest objęty zakresem subskrypcji, Dopasuj projekt magazynu, aby spełniał strategię projektu subskrypcji, taką jak *strategia kategorii aplikacji* , w której subskrypcje są rozdzielone na podstawie określonych aplikacji lub usług lub w wierszach aplikacji Archetypes. Więcej informacji znajduje się w tym [artykule](/azure/cloud-adoption-framework/decision-guides/subscriptions/).

### <a name="single-or-multiple-vault"></a>Jeden lub wiele magazynów

Za pomocą jednego magazynu lub wielu magazynów można organizować kopie zapasowe i zarządzać nimi. Należy uwzględnić następujące wytyczne:

* Jeśli Twoje obciążenia są zarządzane przez pojedynczą subskrypcję i pojedynczy zasób, można użyć jednego magazynu do monitorowania i zarządzania zapasami kopii.

* Jeśli Twoje obciążenia są rozłożone między subskrypcjami, możesz utworzyć wiele magazynów, co najmniej jedną subskrypcję.
  * Centrum kopii zapasowych umożliwia korzystanie z jednego okienka Glass do zarządzania wszystkimi zadaniami związanymi z tworzeniem kopii zapasowych. [Więcej informacji można znaleźć tutaj]().
  * Widoki można dostosować za pomocą szablonów skoroszytów. Eksplorator kopii zapasowych jest jednym z takich szablonów dla maszyn wirtualnych platformy Azure. [Więcej informacji można znaleźć tutaj](monitor-azure-backup-with-backup-explorer.md).
  * Jeśli potrzebujesz spójnych zasad w różnych magazynach, możesz użyć usługi Azure Policy do propagowania zasad tworzenia kopii zapasowych w wielu magazynach. Można napisać niestandardową [definicję Azure Policy](../governance/policy/concepts/definition-structure.md) , która używa efektu ["deployifnotexists"](../governance/policy/concepts/effects.md#deployifnotexists) do propagowania zasad tworzenia kopii zapasowych w wielu magazynach. Tę definicję Azure Policy można także [przypisać](../governance/policy/assign-policy-portal.md) do określonego zakresu (subskrypcja lub RG), tak aby wdrażał zasób "zasady kopii zapasowych" do wszystkich magazynów Recovery Services w zakresie przypisania Azure Policy. Ustawienia zasad tworzenia kopii zapasowych (takie jak częstotliwość tworzenia kopii zapasowych, przechowywanie i tak dalej) powinny zostać określone przez użytkownika jako parametry w przypisaniu Azure Policy.

* W miarę rozwoju Twojej organizacji można przenieść obciążenia między subskrypcjami z następujących powodów: Wyrównaj według zasad tworzenia kopii zapasowej, Konsoliduj magazyny, a także Obniż poziom nadmiarowości, aby zaoszczędzić koszt (przechodzenie od GRS do LRS).  Azure Backup obsługuje przeniesienie magazynu Recovery Services między subskrypcjami platformy Azure lub inną grupą zasobów w ramach tej samej subskrypcji. [Więcej informacji można znaleźć tutaj](backup-azure-move-recovery-services-vault.md).

### <a name="review-default-settings"></a>Przejrzyj ustawienia domyślne

Przed skonfigurowaniem kopii zapasowych w magazynie Sprawdź ustawienia domyślne dla typu replikacji magazynu i ustawień zabezpieczeń.

* *Typ replikacji magazynu* jest domyślnie ustawiony na Geograficznie nadmiarowy (GRS). Po skonfigurowaniu kopii zapasowej opcja Modyfikuj jest wyłączona. Wykonaj [następujące](backup-create-rs-vault.md#set-storage-redundancy) kroki, aby przejrzeć i zmodyfikować ustawienia.

* Funkcja *usuwania nietrwałego* jest włączana domyślnie dla nowo utworzonych magazynów w celu ochrony danych kopii zapasowej przed przypadkowym lub złośliwym usunięciem. Wykonaj [następujące](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) kroki, aby przejrzeć i zmodyfikować ustawienia.

* *Przywracanie między regionami* umożliwia przywracanie maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest sparowanym regionem platformy Azure. Ta opcja umożliwia przechodzenie do szczegółów w celu spełnienia wymagań dotyczących inspekcji lub zgodności oraz przywrócenie maszyny wirtualnej lub jej dysku w przypadku awarii w regionie podstawowym. CRR to funkcja opcjonalna dla dowolnego magazynu GRS. [Więcej informacji można znaleźć tutaj](backup-create-rs-vault.md#set-cross-region-restore).

* Przed zakończeniem projektowania magazynu Przejrzyj [macierze obsługi magazynu](backup-support-matrix.md#vault-support) , aby poznać czynniki, które mogą mieć wpływ na lub ograniczyć wybór projektu.

## <a name="backup-policy-considerations"></a>Zagadnienia dotyczące zasad tworzenia kopii zapasowych

Zasada Azure Backup ma dwa składniki: *Schedule* (Kiedy należy wykonać kopię zapasową) i *przechowywanie* (jak długo ma być przechowywana kopia zapasowa). Zasady można definiować na podstawie typu danych, których kopia zapasowa jest tworzona, wymagania RTO/RPO, potrzeby operacyjne lub prawne oraz typ obciążenia (na przykład maszyna wirtualna, baza danych, pliki). [Więcej informacji można znaleźć tutaj](backup-architecture.md#backup-policy-essentials).

Podczas tworzenia zasad tworzenia kopii zapasowych należy wziąć pod uwagę następujące wytyczne:

### <a name="schedule-considerations"></a>Zagadnienia dotyczące harmonogramu

* Należy rozważyć grupowanie maszyn wirtualnych, które wymagają tego samego harmonogramu czasu rozpoczęcia, częstotliwości i ustawień przechowywania w ramach jednej zasady.

* Upewnij się, że zaplanowana godzina rozpoczęcia tworzenia kopii zapasowej jest w czasie aplikacji produkcyjnej poza szczytem.

* Aby dystrybuować ruch kopii zapasowych, należy rozważyć tworzenie kopii zapasowych różnych maszyn wirtualnych w różnych porach dnia i upewnić się, że czasy nie nakładają się na siebie.

### <a name="retention-considerations"></a>Zagadnienia dotyczące przechowywania

* Krótkoterminowe przechowywanie może wynosić "minuty" lub "dzienne". Przechowywanie "tygodniowych", "miesięcznych" lub "rocznych" punktów kopii zapasowych jest określane jako długoterminowe przechowywanie.

* Długoterminowe przechowywanie:
  * Planowane (wymagania dotyczące zgodności) — Jeśli wiesz już, że dane są wymagane przez lata od bieżącego czasu, użyj długoterminowego przechowywania.
  * Nieplanowane (wymaganie na żądanie) — Jeśli nie znasz z góry, użyj opcji na żądanie z określonymi ustawieniami przechowywania niestandardowego (te niestandardowe ustawienia przechowywania nie mają wpływu na ustawienia zasad).

* Tworzenie kopii zapasowej na żądanie z niestandardowym przechowywaniem — Jeśli chcesz utworzyć kopię zapasową zaplanowaną za pomocą zasad tworzenia kopii zapasowych, możesz użyć kopii zapasowej na żądanie. Może to być przydatne w przypadku tworzenia kopii zapasowych, które nie pasują do zaplanowanej kopii zapasowej, ani do tworzenia szczegółowej kopii zapasowej (na przykład wiele kopii zapasowych maszyn wirtualnych IaaS dziennie, ponieważ zaplanowana kopia zapasowa zezwala na wykonywanie tylko jednej Należy pamiętać, że zasady przechowywania zdefiniowane w zaplanowanych zasadach nie mają zastosowania do kopii zapasowych na żądanie.

### <a name="optimize-backup-policy"></a>Optymalizowanie zasad tworzenia kopii zapasowych

* W miarę zmiany wymagań firmy może być konieczne przeprowadzenie lub skrócenie okresu przechowywania. Gdy to zrobisz, możesz oczekiwać, że:  
  * Jeśli przechowywanie zostanie rozszerzone, istniejące punkty odzyskiwania są oznaczane i przechowywane zgodnie z nowymi zasadami.
  * W przypadku skrócenia okresu przechowywania punkty odzyskiwania są oznaczane do oczyszczenia w następnym zadaniu oczyszczania, a następnie usunięte.
  * Najnowsze reguły przechowywania są stosowane dla wszystkich punktów przechowywania (z wyjątkiem punktów przechowywania na żądanie). W przypadku przekroczenia okresu przechowywania (na przykład do 100 dni), gdy wykonywana jest kopia zapasowa, a następnie zmniejszenie ilości przechowywania (na przykład z 100 dni do siedmiu dni), wszystkie dane kopii zapasowej będą przechowywane zgodnie z ostatnim określonym okresem przechowywania (czyli 7 dni).

* Azure Backup zapewnia elastyczność *zatrzymywania ochrony kopii zapasowych i zarządzania nimi*:
  * *Zatrzymaj ochronę i Zachowaj dane kopii zapasowej*. W przypadku wycofywania lub likwidowania źródła danych (maszyny wirtualnej, aplikacji), ale należy zachować dane do celów inspekcji lub zgodności, można użyć tej opcji, aby zatrzymać wszystkie przyszłe zadania tworzenia kopii zapasowej chroniące źródło danych i zachować kopie zapasowe punktów odzyskiwania. Następnie można przywrócić lub wznowić ochronę maszyny wirtualnej.
  * *Zatrzymaj ochronę i Usuń dane kopii zapasowej*. Ta opcja spowoduje zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej z ochrony maszyny wirtualnej i usunięcie wszystkich punktów odzyskiwania. Nie będzie można przywrócić maszyny wirtualnej ani użyć opcji Wznów tworzenie kopii zapasowej.

  * W przypadku wznowienia ochrony (źródła danych, które zostało zatrzymane z zachowaniem zachowania danych), zostaną zastosowane reguły przechowywania. Wszystkie wygasłe punkty odzyskiwania zostaną usunięte (w zaplanowanym czasie).

* Przed ukończeniem projektu zasad należy pamiętać o następujących czynnikach, które mogą wpływać na wybór projektu.
  * Zasady tworzenia kopii zapasowych są objęte zakresem magazynu.
  * Istnieje limit liczby elementów na zasady (na przykład maszyny wirtualne 100). Aby skalować, można utworzyć zduplikowane zasady z takimi samymi lub różnymi harmonogramami.
  * Nie można selektywnie usunąć określonych punktów odzyskiwania.
  * Nie można całkowicie wyłączyć zaplanowanej kopii zapasowej i zachować źródła danych w stanie chronionym. Co najmniej częste kopie zapasowe, które można skonfigurować za pomocą zasad, mają mieć jedną cotygodniową zaplanowaną kopię zapasową. Alternatywą jest zatrzymanie ochrony z zachowaniem danych i włączenie ochrony za każdym razem, gdy chcesz utworzyć kopię zapasową, wykonać kopię zapasową na żądanie, a następnie wyłączyć ochronę, ale zachować dane kopii zapasowej. [Więcej informacji można znaleźć tutaj](backup-azure-manage-vms.md#stop-protecting-a-vm).

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Aby pomóc w ochronie danych kopii zapasowych i spełnieniu potrzeb związanych z bezpieczeństwem firmy, Azure Backup zapewnia poufność, integralność i dostępność w odniesieniu do świadomych ataków i nadużycia cennych danych i systemów. Należy wziąć pod uwagę następujące wytyczne dotyczące zabezpieczeń rozwiązania Azure Backup:

### <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

* Kontrola dostępu oparta na rolach na platformie Azure (RBAC) umożliwia precyzyjne zarządzanie dostępem, segregowanie obowiązków w zespole i udzielanie dostępu tylko użytkownikom potrzebnym do wykonywania swoich zadań. [Więcej informacji można znaleźć tutaj](backup-rbac-rs-vault.md).

* Azure Backup udostępnia trzy wbudowane role do kontrolowania operacji zarządzania kopiami zapasowymi: Współautorzy, operatorzy i czytelnicy kopii zapasowych. [Więcej informacji można znaleźć tutaj](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions).

* Azure Backup ma kilka kontrolek zabezpieczeń wbudowanych w usługę, aby zapobiec wykryciu luk w zabezpieczeniach, wykrywać je i reagować na nie (Dowiedz się więcej)

* Konta magazynu używane przez magazyny Recovery Services są izolowane i nie są dostępne dla użytkowników w żadnych złośliwych celach. Dostęp jest dozwolony tylko za pomocą Azure Backup operacji zarządzania, takich jak przywracanie.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>Szyfrowanie danych podczas przesyłania i przechowywania

Szyfrowanie chroni dane i pomaga sprostać wymaganiom dotyczącym zabezpieczeń i zgodności w organizacji.

* W ramach platformy Azure dane przesyłane między usługą Azure Storage i magazynem są chronione za pośrednictwem protokołu HTTPS. Te dane pozostają w sieci platformy Azure.

* Dane kopii zapasowej są automatycznie szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Alternatywnie możesz użyć własnych kluczy, nazywanych również [kluczami zarządzanymi przez klienta](encryption-at-rest-with-cmk.md).

* Azure Backup obsługuje tworzenie kopii zapasowych i przywracanie maszyn wirtualnych platformy Azure, w których dyski systemu operacyjnego/danych są szyfrowane za pomocą Azure Disk Encryption (ADE). [Więcej informacji można znaleźć tutaj](backup-azure-vms-encryption.md).

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>Ochrona danych kopii zapasowej przed przypadkowym usunięciem

Azure Backup zapewnia funkcje zabezpieczeń chroniące dane kopii zapasowej nawet po usunięciu. W przypadku usuwania nietrwałego, jeśli użytkownik usunie kopię zapasową (maszyny wirtualnej, SQL Server bazę danych, udział plików platformy Azure, baza danych SAP HANA), dane kopii zapasowej są przechowywane przez 14 dodatkowych dni, co pozwala na odzyskanie tego elementu kopii zapasowej bez utraty danych. Dodatkowe 14 dni przechowywania danych kopii zapasowej w stanie "usuwanie nietrwałe" nie wiążą się z pozostałymi kosztami. [Więcej informacji można znaleźć tutaj](backup-azure-security-feature-cloud.md).

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitorowanie i alerty podejrzanych działań

Azure Backup udostępnia wbudowane funkcje monitorowania i alertów umożliwiające wyświetlanie i Konfigurowanie akcji dla zdarzeń związanych z Azure Backup. [Więcej informacji można znaleźć tutaj](security-overview.md#monitoring-and-alerts-of-suspicious-activity).

### <a name="security-features-to-help-protect-hybrid-backups"></a>Funkcje zabezpieczeń chroniące hybrydowe kopie zapasowe

Usługa Azure Backup używa agenta Microsoft Azure Recovery Services (MARS) do tworzenia kopii zapasowych i przywracania plików, folderów oraz stanu woluminu lub systemu z komputera lokalnego na platformę Azure. Usługa MARS udostępnia teraz funkcje zabezpieczeń: hasło do szyfrowania przed przekazaniem i odszyfrowaniem po pobraniu z Azure Backup usunięte dane kopii zapasowej są przechowywane przez dodatkowe 14 dni od daty usunięcia, a operacja krytyczna (np. Zmiana hasła może być wykonywana tylko przez użytkowników, którzy mają prawidłowe poświadczenia platformy Azure. [Więcej informacji można znaleźć tutaj](backup-azure-security-feature.md).

## <a name="network-considerations"></a>Kwestie dotyczące sieci

Azure Backup wymaga przenoszenia danych z obciążenia do magazynu Recovery Services. Azure Backup zapewnia kilka funkcji ochrony danych kopii zapasowej przed przypadkowym ujawnieniem (na przykład ataku typu man-in-the-Middle w sieci). Należy uwzględnić następujące wytyczne:

### <a name="internet-connectivity"></a>Łączność z Internetem

* *Kopia zapasowa maszyny wirtualnej platformy Azure* — cała wymagana komunikacja i transfer danych między magazynem a usługą Azure Backup odbywa się w sieci platformy Azure bez konieczności uzyskiwania dostępu do sieci wirtualnej. Dlatego tworzenie kopii zapasowych maszyn wirtualnych platformy Azure umieszczonych w sieciach zabezpieczonych nie wymaga zezwolenia na dostęp do żadnych adresów IP ani nazw FQDN.

* *SAP HANA baz danych na maszynach wirtualnych platformy Azure, SQL Server bazach danych na maszynie wirtualnej platformy Azure* — wymaga łączności z usługą Azure Backup, Azure Storage i Azure Active Directory. Można to osiągnąć za pomocą prywatnych punktów końcowych lub zezwalając na dostęp do wymaganych publicznych adresów IP lub nazw FQDN. Nieumożliwienie właściwej łączności z wymaganymi usługami platformy Azure może prowadzić do niepowodzenia operacji takich jak odnajdywanie bazy danych, konfigurowanie kopii zapasowej, wykonywanie kopii zapasowych i przywracanie danych. Aby uzyskać pełne wskazówki dotyczące sieci przy użyciu tagów sieciowej grupy zabezpieczeń, zapory platformy Azure i serwera proxy HTTP, zapoznaj się z tymi artykułami [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) i [SAP HANA](./backup-azure-sap-hana-database.md#establish-network-connectivity) .

* *Hybrydowy* — Agent MARS (Microsoft Azure Recovery Services) wymaga dostępu do sieci dla wszystkich krytycznych operacji — instalacji, konfiguracji, tworzenia kopii zapasowej i przywracania. Agent MARS może nawiązać połączenie z usługą Azure Backup za pośrednictwem usługi [Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) za pomocą publicznej komunikacji równorzędnej (dostępne dla starych obwodów) i komunikacji równorzędnej firmy Microsoft przy użyciu [prywatnych punktów końcowych](install-mars-agent.md#private-endpoints) lub za pośrednictwem [serwera proxy/zapory z odpowiednimi kontrolami dostępu](install-mars-agent.md#verify-internet-access).

### <a name="private-endpoints-for-azure-backup"></a>Prywatne punkty końcowe dla Azure Backup

[Prywatny punkt końcowy](../private-link/private-endpoint-overview.md) platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Azure Backup pozwala na bezpieczne wykonywanie kopii zapasowych i przywracanie danych z magazynów Recovery Services przy użyciu prywatnych punktów końcowych.

* Po włączeniu prywatnych punktów końcowych dla magazynu są one używane tylko na potrzeby tworzenia kopii zapasowych i przywracania danych SQL i SAP HANA obciążeń na maszynach wirtualnych platformy Azure i w ramach kopii zapasowych agenta MARS.  Magazyn można również używać do tworzenia kopii zapasowych innych obciążeń (nie wymagają jednak prywatnych punktów końcowych). Oprócz wykonywania kopii zapasowych obciążeń SQL i SAP HANA oraz tworzenia kopii zapasowych przy użyciu agenta MARS prywatne punkty końcowe są również używane do odzyskiwania plików w przypadku tworzenia kopii zapasowych maszyny wirtualnej platformy Azure. [Więcej informacji można znaleźć tutaj](private-endpoints.md#recommended-and-supported-scenarios).

* Azure Active Directory nie obsługuje obecnie prywatnych punktów końcowych. W związku z tym adresy IP i nazwy FQDN wymagane dla Azure Active Directory muszą mieć dozwolony dostęp wychodzący z zabezpieczonej sieci podczas wykonywania kopii zapasowej baz danych na maszynach wirtualnych platformy Azure i kopii zapasowej przy użyciu agenta MARS. Możesz również użyć tagów sieciowej grupy zabezpieczeń i tagów zapory platformy Azure, aby umożliwić dostęp do usługi Azure AD, zgodnie z wymaganiami. Więcej informacji o [wymaganiach wstępnych znajdziesz tutaj](./private-endpoints.md#before-you-start).

## <a name="governance-considerations"></a>Zagadnienia związane z nadzorem

Zarządzanie na platformie Azure jest implementowane głównie przy użyciu [Azure Policy](../governance/policy/overview.md) i [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md). [Azure Policy](../governance/policy/overview.md) umożliwia tworzenie i przypisywanie definicji zasad oraz zarządzanie nimi w celu wymuszania reguł dla zasobów. Ta funkcja zapewnia zgodność tych zasobów ze standardami firmowymi. [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) umożliwia śledzenie użycia i wydatków w chmurze dla zasobów platformy Azure i innych dostawców chmury. Ponadto następujące narzędzia, takie jak [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) i [Azure Advisor](../advisor/advisor-overview.md)  odgrywają ważną rolę w procesie zarządzania kosztami.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure Backup obsługiwać dwa kluczowe scenariusze za pośrednictwem wbudowanego Azure Policy

* Upewnij się, że nowo utworzone maszyny o krytycznym znaczeniu dla firmy są tworzone automatycznie przy użyciu odpowiednich ustawień przechowywania. Azure Backup udostępnia wbudowane zasady (przy użyciu Azure Policy), które można przypisać do wszystkich maszyn wirtualnych platformy Azure w określonej lokalizacji w ramach subskrypcji lub grupy zasobów. Gdy te zasady są przypisane do danego zakresu, wszystkie nowe maszyny wirtualne utworzone w tym zakresie są automatycznie konfigurowane na potrzeby tworzenia kopii zapasowych w istniejącym magazynie w tej samej lokalizacji i subskrypcji. Użytkownik może określić magazyn i zasady przechowywania, do których mają być skojarzone maszyny wirtualne z kopią zapasową. [Więcej informacji można znaleźć tutaj](backup-azure-auto-enable-backup.md).

* Upewnij się, że nowo utworzone magazyny mają włączoną diagnostykę do obsługi raportów. Często dodanie ustawień diagnostycznych ręcznie dla magazynu może być skomplikowanym zadaniem. Ponadto każdy utworzony nowy magazyn musi mieć włączone ustawienia diagnostyczne, aby można było wyświetlać raporty dla tego magazynu. Aby uprościć tworzenie ustawień diagnostycznych na dużą skalę (z Log Analytics jako miejsce docelowe), Azure Backup udostępnia wbudowaną Azure Policy. Ta zasada dodaje ustawienie diagnostyki LA do wszystkich magazynów w każdej subskrypcji lub grupie zasobów. Poniższe sekcje zawierają instrukcje dotyczące korzystania z tych zasad. [Więcej informacji można znaleźć tutaj](azure-policy-configure-diagnostics.md).

### <a name="azure-backup-cost-considerations"></a>Azure Backup zagadnienia dotyczące kosztów

Możliwości usługi Azure Backup oferują elastyczność w skutecznym zarządzaniu kosztami i nadal spełniają wymagania biznesowe BCDR (ciągłość działania i odzyskiwanie po awarii). Należy uwzględnić następujące wytyczne:

* Za pomocą kalkulatora cen można oceniać i optymalizować koszt, dostosowując różne dźwignie. [Dowiedz się więcej tutaj](azure-backup-pricing.md)

* Eksplorator kopii zapasowych: Użyj Eksploratora kopii zapasowej lub raportów kopii zapasowych, aby zrozumieć i zoptymalizować wzrost magazynu kopii zapasowych, zatrzymywać kopie zapasowe dla niekrytycznych obciążeń lub usuniętych maszyn wirtualnych. Możesz uzyskać Zagregowany widok całej wartości z perspektywy tworzenia kopii zapasowych. Obejmuje to nie tylko informacje o elementach kopii zapasowych, ale również zasoby, które nie są skonfigurowane do tworzenia kopii zapasowych. Gwarantuje to, że ochrona najważniejszych danych nie zostanie nigdy pominięta, a kopie zapasowe są zoptymalizowane pod kątem niekrytycznych obciążeń lub usuniętych obciążeń.

* Optymalizowanie zasad tworzenia kopii zapasowych
  * Optymalizowanie ustawień harmonogramu i przechowywania w oparciu o obciążenie Archetypes (na przykład krytyczne dla działalności, niekrytyczne)
  * Optymalizuj ustawienia przechowywania dla natychmiastowego przywracania
  * Wybierz odpowiedni typ kopii zapasowej, aby spełnić wymagania, jednocześnie pobierając obsługiwane typy kopii zapasowych (pełne, przyrostowe, Rejestruj, różnicowe) przez obciążenie w Azure Backup.

* Selektywne dyski kopii zapasowej: wykluczanie dysku (funkcja w wersji zapoznawczej) zapewnia wydajne i ekonomiczne rozwiązanie umożliwiające wybiórcze wykonywanie kopii zapasowych kluczowych danych. Na przykład utwórz kopię zapasową tylko jednego dysku, gdy nie chcesz tworzyć kopii zapasowej reszty dysków dołączonych do maszyny wirtualnej. Jest to przydatne również w przypadku wielu rozwiązań do tworzenia kopii zapasowych. Na przykład podczas tworzenia kopii zapasowej baz danych lub danych za pomocą rozwiązania do tworzenia kopii zapasowych obciążenia (SQL Server Database w kopii zapasowej maszyny wirtualnej platformy Azure) i chcesz użyć kopii zapasowej na poziomie maszyny wirtualnej platformy Azure dla wybranych dysków.

* Azure Backup wykonuje migawki maszyn wirtualnych platformy Azure i przechowuje je razem z dyskami w celu zwiększenia tworzenia punktów odzyskiwania i przyspieszenia operacji przywracania. Jest to nazywane natychmiastowym przywracaniem. Domyślnie migawki przywracania natychmiastowego są przechowywane przez dwa dni. Ta funkcja pozwala wykonać operację przywracania z tych migawek, wycinając czasy przywracania. Skraca czas wymagany do przekształcania i kopiowania danych z magazynu. W związku z tym zobaczysz koszty magazynu odpowiadające migawkom pobranym w tym okresie. [Więcej informacji można znaleźć tutaj](backup-instant-restore-capability.md#configure-snapshot-retention).

* Typ replikacji magazynu Azure Backup magazyn domyślnie jest ustawiany jako Geograficznie nadmiarowy (GRS). Tej opcji nie można zmienić po włączeniu ochrony elementów. Magazyn Geograficznie nadmiarowy (GRS) zapewnia wyższy poziom trwałości danych niż magazyn lokalnie nadmiarowy (LRS), dzięki czemu umożliwia korzystanie z funkcji przywracania między regionami i zwiększania kosztów. Zapoznaj się z kompromisem między niższymi kosztami i wyższą trwałością danych i zdecyduj, co jest najlepsze w danym scenariuszu. [Dowiedz się więcej tutaj](backup-create-rs-vault.md#set-storage-redundancy)

* W przypadku ochrony obciążeń uruchomionych wewnątrz maszyny wirtualnej i samej maszyny wirtualnej Sprawdź, czy jest wymagana taka Podwójna ochrona.

## <a name="monitoring-and-alerting-considerations"></a>Zagadnienia dotyczące monitorowania i alertów

Jako użytkownik lub administrator kopii zapasowej powinien być w stanie monitorować wszystkie rozwiązania do tworzenia kopii zapasowych i otrzymywać powiadomienia o ważnych scenariuszach. Ta sekcja zawiera szczegółowe informacje na temat możliwości monitorowania i powiadamiania zapewnianych przez usługę Azure Backup.

### <a name="monitoring"></a>Monitorowanie

* Azure Backup udostępnia **wbudowane monitorowanie zadań** dla operacji, takich jak konfigurowanie kopii zapasowej, tworzenie kopii zapasowej, przywracanie, Usuwanie kopii zapasowej itd. Jest to zakres do magazynu i idealny do monitorowania pojedynczego magazynu. [Więcej informacji można znaleźć tutaj](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

* Jeśli konieczne jest monitorowanie działań operacyjnych w odpowiedniej skali, program **Backup Explorer** zapewnia Zagregowany widok całej kopii zapasowej, co umożliwia szczegółowe analizowanie i rozwiązywanie problemów. Jest to wbudowany Azure Monitor skoroszyt, który udostępnia jedną, centralną lokalizację, która ułatwia monitorowanie działań operacyjnych w całej kopii zapasowej na platformie Azure, łączenie dzierżawców, lokalizacji, subskrypcji, grup zasobów i magazynów. [Więcej informacji można znaleźć tutaj](monitor-azure-backup-with-backup-explorer.md).
  * Służy do identyfikowania zasobów, które nie są skonfigurowane na potrzeby tworzenia kopii zapasowych, i zapewnienia, że nie zostaną kiedykolwiek pominięte ochronę najważniejszych danych w ramach rosnącej nieruchomości.
  * Pulpit nawigacyjny zawiera działania operacyjne z ostatnich siedmiu dni (maksimum). Jeśli chcesz zachować te dane, możesz je wyeksportować jako plik programu Excel i je zachować.
  * Jeśli jesteś użytkownikiem usługi Azure Lighthouse, możesz wyświetlić informacje dla wielu dzierżawców, co pozwala na monitorowanie mniejszej granicy.

* Jeśli zachodzi potrzeba zatrzymywania i wyświetlania działań operacyjnych na potrzeby długoterminowych, użyj **raportów**. Typowym wymaganiem dla administratorów kopii zapasowych jest uzyskanie wglądu w kopie zapasowe w oparciu o dane, które obejmują dłuższy czas. W przypadku takich rozwiązań należy uwzględnić następujące przypadki:
  * Przydzielanie i prognozowanie zużytego magazynu w chmurze.
  * Inspekcja kopii zapasowych i przywracania.
  * Identyfikowanie kluczowych trendów na różnych poziomach szczegółowości.

* W dodatku
  * Możesz wysyłać dane (na przykład zadania, zasady itd.) do obszaru roboczego **log Analytics** . Spowoduje to włączenie funkcji dzienników Azure Monitor, aby umożliwić korelację danych z innymi danymi monitorowania zebranymi przez Azure Monitor, Konsoliduj wpisy dziennika z wielu subskrypcji platformy Azure i dzierżawców w jedną lokalizację do analizy razem, użyj zapytań dzienników do przeprowadzenia złożonej analizy i uzyskania szczegółowych informacji na temat wpisów dziennika. [Więcej informacji można znaleźć tutaj](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace).
  * Dane można wysyłać do centrum zdarzeń w celu wysyłania wpisów poza platformą Azure, na przykład do SIEM innych firm (informacji o zabezpieczeniach i zarządzania zdarzeniami) lub innych rozwiązań usługi log Analytics. [Więcej informacji można znaleźć tutaj](../azure-monitor/platform/activity-log.md#send-to-azure-event-hubs).
  * Możesz wysłać dane do konta usługi Azure Storage, jeśli dane dziennika mają być przechowywane dłużej niż 90 dni na potrzeby inspekcji, statycznej analizy lub tworzenia kopii zapasowej. Jeśli musisz tylko zachować zdarzenia przez 90 dni lub mniej, nie musisz konfigurować archiwów na koncie magazynu, ponieważ zdarzenia dziennika aktywności są przechowywane na platformie Azure przez 90 dni. [Dowiedz się więcej](../azure-monitor/platform/activity-log.md#send-to--azure-storage).

### <a name="alerting"></a>Generowanie alertów

* Alerty są przede wszystkim sposobem na uzyskanie odpowiednich działań. W sekcji alerty kopii zapasowej znajdują się alerty wygenerowane przez usługę Azure Backup.

* Azure Backup zapewnia wbudowany mechanizm powiadamiania o **alertach** za pośrednictwem poczty e-mail w przypadku błędów, ostrzeżeń i operacji krytycznych. Po wygenerowaniu alertu można określić poszczególne adresy e-mail lub listy dystrybucyjne. Możesz również wybrać, czy otrzymywać powiadomienia o każdym indywidualnym alercie, czy grupować je w co godzinę, a następnie otrzymywać powiadomienia.
  * Te alerty są definiowane przez usługę i zapewniają obsługę ograniczonych scenariuszy — błędów tworzenia kopii zapasowej/przywracania, zatrzymywania ochrony przy zachowaniu danych/zatrzymywania ochrony przy użyciu usuwania danych itd. [Więcej informacji można znaleźć tutaj](backup-azure-monitoring-built-in-monitor.md#alert-scenarios).
  * W przypadku wykonania operacji niszczącej, takiej jak zatrzymanie ochrony z usuwaniem danych, zostanie zgłoszony alert, a wiadomość e-mail zostanie wysłana do właścicieli subskrypcji, administratorów i współadministratorów, nawet jeśli **nie** skonfigurowano powiadomień dla magazynu Recovery Services.
  * Niektóre obciążenia mogą generować wysoką częstotliwość niepowodzeń (na przykład SQL Server co 15 minut). Aby zapobiec przeciążeniu alertów zgłoszonych dla każdego wystąpienia błędu, alerty są konsolidowane. [Więcej informacji można znaleźć tutaj](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts).
  * Wbudowane alerty nie mogą być dostosowane i są ograniczone do wiadomości e-mail zdefiniowanych w Azure Portal.

* Jeśli musisz **utworzyć niestandardowe alerty** (na przykład alerty zadań zakończonych powodzeniem), Użyj log Analytics. W Azure Monitor można tworzyć własne alerty w obszarze roboczym Log Analytics. Obciążenia hybrydowe (DPM/serwera usługi MAB) mogą również wysyłać dane do LA i używać LA do zapewniania typowych alertów w ramach obciążeń obsługiwanych przez Azure Backup.

* Możesz również otrzymywać powiadomienia za poorednictwem wbudowanych **dzienników aktywności** magazynu Recovery Services. Obsługiwane są jednak ograniczone scenariusze i nie są odpowiednie dla operacji, takich jak zaplanowane kopie zapasowe, które są lepiej dostosowane do dzienników zasobów niż w przypadku dzienników aktywności. Aby dowiedzieć się więcej o tych ograniczeniach i sposobach używania Log Analytics obszaru roboczego do monitorowania i generowania alertów dla wszystkich obciążeń chronionych przez Azure Backup, zapoznaj się z tym [artykułem](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale).

## <a name="next-steps"></a>Następne kroki

Zalecamy zapoznanie się z następującymi artykułami jako punktami początkowymi dotyczącymi używania Azure Backup:

* [Przegląd Azure Backup](backup-overview.md)
* [Często zadawane pytania](backup-azure-backup-faq.md)
