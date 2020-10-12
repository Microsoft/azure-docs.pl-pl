---
title: Najlepsze rozwiązania w zakresie zabezpieczeń dotyczące obciążeń IaaS na platformie Azure | Microsoft Docs
description: " Migracja obciążeń do usługi Azure IaaS pozwala na ocenę naszych projektów "
services: security
documentationcenter: na
author: terrylanfear
manager: rkarlin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 03258bf204491afce4635828b3a33a06886aca2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87448411"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Najlepsze rozwiązania dotyczące zabezpieczeń dla obciążeń IaaS na platformie Azure
W tym artykule opisano najlepsze rozwiązania w zakresie zabezpieczeń dotyczące maszyn wirtualnych i systemów operacyjnych.

Najlepsze rozwiązania są oparte na konsensusie opinii i współpracują z bieżącymi funkcjami platformy Azure i zestawami funkcji. Ponieważ Opinie i technologie mogą ulec zmianie z upływem czasu, ten artykuł zostanie zaktualizowany w celu odzwierciedlenia tych zmian.

W większości scenariuszy infrastruktury jako usługi (IaaS) [maszyny wirtualne platformy Azure](/azure/virtual-machines/) są głównym obciążeniem dla organizacji korzystających z chmury obliczeniowej. Ten fakt jest oczywisty w [scenariuszach hybrydowych](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) , w których organizacje chcą wolno migrować obciążenia do chmury. W takich scenariuszach postępuj zgodnie z [ogólnymi zagadnieniami dotyczącymi zabezpieczeń IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)i stosuj najlepsze rozwiązania w zakresie zabezpieczeń do wszystkich maszyn wirtualnych.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Ochrona maszyn wirtualnych przy użyciu uwierzytelniania i kontroli dostępu
Pierwszym krokiem w ochronie maszyn wirtualnych jest upewnienie się, że tylko autoryzowani użytkownicy mogą konfigurować nowe maszyny wirtualne i uzyskiwać dostęp do maszyn wirtualnych.

> [!NOTE]
> Aby zwiększyć bezpieczeństwo maszyn wirtualnych z systemem Linux na platformie Azure, możesz zintegrować się z uwierzytelnianiem za pomocą usługi Azure AD. Korzystając z [uwierzytelniania usługi Azure AD dla maszyn wirtualnych z systemem Linux](/azure/virtual-machines/linux/login-using-aad), można centralnie kontrolować i wymuszać zasady zezwalające na dostęp do maszyn wirtualnych lub odmawiające tego dostępu.
>
>

**Najlepsze rozwiązanie**: kontrolowanie dostępu do maszyny wirtualnej.   
**Szczegóły**: Użyj [zasad platformy Azure](/azure/azure-policy/azure-policy-introduction) , aby określić konwencje dla zasobów w organizacji i utworzyć dostosowane zasady. Zastosuj te zasady do zasobów, takich jak [grupy zasobów](/azure/azure-resource-manager/resource-group-overview). Maszyny wirtualne należące do grupy zasobów dziedziczą swoje zasady.

Jeśli Twoja organizacja ma wiele subskrypcji, konieczny może być sposób na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. [Grupy zarządzania platformy Azure](/azure/azure-resource-manager/management-groups-overview) zapewniają poziom zakresu powyżej subskrypcji. Można organizować subskrypcje w grupy zarządzania (kontenery) i stosować warunki ładu do tych grup. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki zastosowane do grupy. Grupy zarządzania umożliwiają zarządzanie klasy korporacyjnej na dużą skalę niezależnie od typu subskrypcji.

**Najlepsze rozwiązanie**: redukcja zmienności instalacji i wdrożenia maszyn wirtualnych.   
**Szczegóły**: użyj szablonów [Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates) , aby wzmocnić wybór wdrożenia i ułatwić jego zrozumienie i Spis maszyn wirtualnych w środowisku.

**Najlepsze rozwiązanie**: bezpieczny dostęp uprzywilejowany.   
**Szczegóły**: aby umożliwić użytkownikom dostęp do maszyn wirtualnych i konfigurować je, użyj [podejścia najniższych uprawnień](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) oraz wbudowanych ról platformy Azure:

- [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): może zarządzać maszynami wirtualnymi, ale nie z siecią wirtualną ani kontem magazynu, z którym są połączone.
- [Współautor klasycznej maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): może zarządzać maszynami wirtualnymi utworzonymi przy użyciu klasycznego modelu wdrażania, ale nie z siecią wirtualną ani kontem magazynu, z którym są połączone maszyny wirtualne.
- [Administrator zabezpieczeń](../../role-based-access-control/built-in-roles.md#security-admin): tylko w Security Center: mogą wyświetlać zasady zabezpieczeń, wyświetlać Stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlać alerty i zalecenia, odrzucać alerty i zalecenia.
- [Użytkownik DevTest Labs](../../role-based-access-control/built-in-roles.md#devtest-labs-user): może wyświetlać wszystko i łączyć, uruchamiać, ponownie uruchamiać i zamykać maszyny wirtualne.

Administratorzy subskrypcji i współadministratorzy mogą zmienić to ustawienie, tworząc administratorów wszystkich maszyn wirtualnych w ramach subskrypcji. Upewnij się, że ufasz wszystkim administratorom subskrypcji i współadministratorom logowanie się na dowolnych komputerach.

> [!NOTE]
> Zalecamy konsolidowanie maszyn wirtualnych z tym samym cyklem życia w tej samej grupie zasobów. Korzystając z grup zasobów, można wdrażać, monitorować i zestawiać koszty rozliczeń dla zasobów.
>
>

Organizacje kontrolujące dostęp do maszyn wirtualnych i Instalatora ulepszają ogólne zabezpieczenia maszyn wirtualnych.

## <a name="use-multiple-vms-for-better-availability"></a>Korzystanie z wielu maszyn wirtualnych w celu zapewnienia lepszej dostępności
Jeśli maszyna wirtualna uruchamia krytyczne aplikacje, które muszą mieć wysoką dostępność, zdecydowanie zalecamy użycie wielu maszyn wirtualnych. Aby zapewnić lepszą dostępność, użyj [zestawu dostępności](../../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) lub [stref](../../availability-zones/az-overview.md)dostępności.

Zestaw dostępności jest grupą logiczną, której można użyć na platformie Azure, aby upewnić się, że zasoby maszyny wirtualnej, które znajdują się w niej, są od siebie odizolowane, gdy zostaną wdrożone w centrum danych platformy Azure. System Azure zapewnia, że maszyny wirtualne, które są umieszczane w zestawie dostępności, są uruchamiane na wielu serwerach fizycznych, w stojakach obliczeniowych, jednostkach magazynowych i przełącznikach sieciowych. Jeśli wystąpi awaria sprzętu lub oprogramowania platformy Azure, ma to zastosowanie tylko do podzbioru maszyn wirtualnych, a ogólna aplikacja będzie nadal dostępna dla klientów. Zestawy dostępności są istotną funkcją do tworzenia niezawodnych rozwiązań w chmurze.

## <a name="protect-against-malware"></a>Ochrona przed złośliwym oprogramowaniem
Należy zainstalować ochronę przed złośliwym oprogramowaniem, aby ułatwić identyfikowanie i usuwanie wirusów, programów szpiegujących i innego złośliwego oprogramowania. Można zainstalować [oprogramowanie Microsoft chroniące przed złośliwym kodem](antimalware.md) lub rozwiązanie Endpoint Protection partnera firmy Microsoft ([Trend Micro](https://help.deepsecurity.trendmicro.com/Welcome.html), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security)i [System Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft oferuje takie funkcje jak ochrona w czasie rzeczywistym, zaplanowane skanowanie, korygowanie złośliwego oprogramowania, aktualizacje sygnatur, aktualizacje aparatu, raportowanie przykładów i zbieranie zdarzeń wykluczania. W przypadku środowisk, które są hostowane niezależnie od środowiska produkcyjnego, można użyć rozszerzenia chroniącego przed złośliwym kodem, aby chronić maszyny wirtualne i usługi w chmurze.

Możesz zintegrować rozwiązania firmy Microsoft chroniące przed złośliwym oprogramowaniem i partnerzy [Azure Security Center](../../security-center/index.yml) , aby ułatwić wdrażanie i wbudowane wykrywanie (alerty i zdarzenia).

**Najlepsze**rozwiązania: Zainstaluj rozwiązanie chroniące przed złośliwym kodem, aby chronić przed złośliwym oprogramowaniem.   
**Szczegóły**: [Zainstaluj rozwiązanie partnerskie firmy Microsoft lub oprogramowanie chroniące przed złośliwym kodem](../../security-center/security-center-install-endpoint-protection.md)

**Najlepsze**rozwiązania: Zintegruj rozwiązanie chroniące przed złośliwym kodem za pomocą Security Center, aby monitorować stan ochrony.   
**Szczegóły**: [Zarządzanie problemami z programem Endpoint protection przy użyciu Security Center](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Zarządzanie aktualizacjami maszyny wirtualnej
Maszyny wirtualne platformy Azure, takie jak wszystkie lokalne maszyny wirtualne, są przeznaczone do zarządzania przez użytkownika. Platforma Azure nie wypycha do nich aktualizacji systemu Windows. Musisz zarządzać aktualizacjami maszyny wirtualnej.

**Najlepsze rozwiązanie**: Zachowaj aktualność maszyn wirtualnych.   
**Szczegóły**: użyj rozwiązania [Update Management](../../automation/update-management/update-mgmt-overview.md) w Azure Automation do zarządzania aktualizacjami systemu operacyjnego na komputerach z systemami Windows i Linux, które są wdrożone na platformie Azure, w środowiskach lokalnych lub w innych dostawcach chmury. Umożliwia ono szybką ocenę stanu dostępnych aktualizacji na wszystkich komputerach agentów oraz zarządzanie procesem instalacji wymaganych aktualizacji serwerów.

W celu przeprowadzania ocen i wdrożeń aktualizacji na komputerach zarządzanych przez rozwiązanie Update Management są używane następujące konfiguracje:

- Program Microsoft Monitoring Agent (MMA) dla systemu Windows lub Linux
- Platforma PowerShell Desired State Configuration (DSC) dla systemu Linux
- Hybrydowy proces roboczy elementu runbook usługi Automation
- Usługa Microsoft Update lub Windows Server Update Services (WSUS) dla komputerów z systemem Windows

Jeśli używasz Windows Update, pozostaw ustawienie automatyczne Windows Update włączone.

**Najlepsze rozwiązanie**: Upewnij się, że wdrożone obrazy zawierają najnowsze aktualizacje systemu Windows.   
**Szczegóły**: należy sprawdzić i zainstalować wszystkie aktualizacje systemu Windows jako pierwszy krok każdego wdrożenia. Ta miara jest szczególnie ważna w przypadku wdrażania obrazów pochodzących z własnej lub własnej biblioteki. Mimo że obrazy z portalu Azure Marketplace są automatycznie aktualizowane domyślnie, może istnieć czas opóźnienia (do kilku tygodni) po wersji publicznej.

**Najlepsze rozwiązanie**: okresowo ponownie Wdrażaj maszyny wirtualne w celu wymuszenia nowej wersji systemu operacyjnego.   
**Szczegóły**: Zdefiniuj maszynę wirtualną za pomocą [szablonu Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) , aby można było ją łatwo wdrożyć ponownie. Korzystanie z szablonu zapewnia bezpieczną i zabezpieczoną maszynę wirtualną, gdy jej potrzebujesz.

**Najlepsze rozwiązanie**: Szybkie stosowanie aktualizacji zabezpieczeń do maszyn wirtualnych.   
**Szczegóły**: Włącz Azure Security Center (warstwa Bezpłatna lub standardowa), aby [zidentyfikować brakujące aktualizacje zabezpieczeń i zastosować je](../../security-center/security-center-apply-system-updates.md).

**Najlepsze rozwiązanie**: Zainstaluj najnowsze aktualizacje zabezpieczeń.   
**Szczegóły**: niektóre z pierwszych obciążeń, które klienci przechodzą na platformę Azure, to laboratoria i systemy zewnętrzne. Jeśli aplikacje lub usługi dla maszyn wirtualnych platformy Azure, które wymagają dostępu do Internetu, są czujność na temat stosowania poprawek. Poprawka poza system operacyjny. Niepoprawione luki w zabezpieczeniach aplikacji partnerskich mogą również prowadzić do problemów, które można uniknąć w przypadku dobrego zarządzania poprawkami.

**Najlepsze**rozwiązanie: Wdróż i przetestuj rozwiązanie do tworzenia kopii zapasowych.   
**Szczegóły**: kopia zapasowa musi być obsługiwana w taki sam sposób, jak w przypadku każdej innej operacji. Jest to prawdziwe w przypadku systemów, które są częścią środowiska produkcyjnego, rozszerzając do chmury.

Systemy testowe i deweloperskie muszą stosować się do strategii tworzenia kopii zapasowych, które udostępniają możliwości przywracania, które są podobne do tego, do jakich użytkowników korzystali, w zależności od ich środowiska lokalnego. Obciążenia produkcyjne przeniesione na platformę Azure powinny być zintegrowane z istniejącymi rozwiązaniami do tworzenia kopii zapasowych, jeśli to możliwe Można też użyć [Azure Backup](../../backup/backup-azure-vms-first-look-arm.md) , aby pomóc w rozwiązywaniu wymagań dotyczących kopii zapasowych.

Organizacje, które nie wymuszają zasad aktualizacji oprogramowania, są bardziej ujawniane zagrożeniom wykorzystującym znane, wcześniej wyprawione luki w zabezpieczeniach. Aby zapewnić zgodność z przepisami branżowymi, firmy muszą udowodnić, że są sumienni i używają odpowiednich kontroli zabezpieczeń, aby pomóc w zapewnieniu bezpieczeństwa obciążeń znajdujących się w chmurze.

Oprogramowanie — najlepsze rozwiązania z zakresu aktualizacji dla tradycyjnych centrów danych i platformy Azure IaaS mają wiele podobieństw. Zalecamy oszacowanie bieżących zasad aktualizacji oprogramowania w celu uwzględnienia maszyn wirtualnych znajdujących się na platformie Azure.

## <a name="manage-your-vm-security-posture"></a>Zarządzanie stanem zabezpieczeń maszyny wirtualnej
Dotyczące środowiskach. Ochrona maszyn wirtualnych wymaga możliwości monitorowania, która może szybko wykrywać zagrożenia, zapobiegać nieautoryzowanemu dostępowi do zasobów, wyzwalać alerty i zmniejszać liczbę fałszywie dodatnich.

Aby monitorować stan zabezpieczeń [maszyn wirtualnych](../../security-center/security-center-linux-virtual-machine.md)z [systemami Windows](../../security-center/security-center-virtual-machine.md) i Linux, użyj [Azure Security Center](../../security-center/security-center-intro.md). W Security Center Zabezpiecz swoje maszyny wirtualne, wykorzystując następujące możliwości:

- Zastosuj ustawienia zabezpieczeń systemu operacyjnego przy użyciu zalecanych reguł konfiguracji.
- Zidentyfikuj i Pobierz zabezpieczenia systemu oraz krytyczne aktualizacje, które mogą być niedostępne.
- Wdróż zalecenia dotyczące ochrony przed złośliwym oprogramowaniem punktu końcowego.
- Sprawdź poprawność szyfrowania dysku.
- Oceń i Koryguj luki w zabezpieczeniach.
- Wykrywaj zagrożenia.

Security Center może aktywnie monitorować zagrożenia, a potencjalne zagrożenia są ujawniane w alertach zabezpieczeń. Skorelowane zagrożenia są agregowane w jednym widoku zwanym zdarzeniem zabezpieczeń.

Security Center przechowuje dane w [dziennikach Azure monitor](/azure/log-analytics/log-analytics-overview). Dzienniki Azure Monitor udostępniają język zapytań i aparat analityczny, który zapewnia wgląd w działanie aplikacji i zasobów. Dane są również zbierane z [Azure monitor](../../batch/monitoring-overview.md), rozwiązań do zarządzania i agentów zainstalowanych na maszynach wirtualnych w chmurze lub lokalnych. Ta wspólna funkcjonalność pomaga utworzyć pełny obraz środowiska.

Organizacje, które nie wymuszają mocnych zabezpieczeń dla swoich maszyn wirtualnych, pozostają nieświadome potencjalnych prób spowodowanych przez nieautoryzowanych użytkowników do obejścia kontroli zabezpieczeń.

## <a name="monitor-vm-performance"></a>Monitorowanie wydajności maszyny wirtualnej
Użycie zasobów może być problemem, gdy procesy maszyny wirtualnej zużywają więcej zasobów niż powinny. Problemy z wydajnością maszyny wirtualnej mogą prowadzić do przerw w działaniu usługi, co narusza zasadę zabezpieczeń. Jest to szczególnie ważne w przypadku maszyn wirtualnych, które obsługują usługi IIS lub inne serwery sieci Web, ponieważ duże użycie procesora lub pamięci może wskazywać na atak typu "odmowa usługi" (DoS). Jest to konieczne, aby monitorować dostęp do maszyny wirtualnej nie tylko w sposób nieaktywny w trakcie wystąpienia problemu, ale również aktywnie z wydajnością bazową mierzoną podczas normalnego działania.

Zalecamy używanie [Azure monitor](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) , aby uzyskać wgląd w kondycję zasobu. Funkcje Azure Monitor:

- [Pliki dziennika diagnostyki zasobów](../../azure-monitor/platform/platform-logs-overview.md): monitoruje zasoby maszyn wirtualnych i identyfikuje potencjalne problemy, które mogą naruszyć wydajność i dostępność.
- [Rozszerzenie Diagnostyka Azure](/azure/azure-monitor/platform/diagnostics-extension-overview): oferuje funkcje monitorowania i diagnostyki na maszynach wirtualnych z systemem Windows. Te możliwości można włączyć, dołączając rozszerzenie jako część [szablonu Azure Resource Manager](/azure/virtual-machines/windows/extensions-diagnostics-template).

Organizacje, które nie monitorują wydajności maszyn wirtualnych, nie mogą określić, czy pewne zmiany wzorców wydajności są normalne, czy nietypowe. Maszyna wirtualna, która zużywa więcej zasobów niż normalna, może wskazywać na atak z zasobów zewnętrznych lub zagrożony proces uruchomiony na maszynie wirtualnej.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Szyfruj pliki wirtualnego dysku twardego
Zalecamy zaszyfrowanie wirtualnych dysków twardych (VHD), aby chronić wolumin rozruchowy i woluminy danych przechowywane w magazynie oraz klucze szyfrowania i wpisy tajne.

[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) ułatwia szyfrowanie dysków maszyn wirtualnych z systemami Windows i Linux IaaS. Azure Disk Encryption używa standardowej funkcji [funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) systemu Windows i funkcji [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) w systemie Linux, aby zapewnić szyfrowanie woluminów dla systemu operacyjnego i dysków danych. Rozwiązanie jest zintegrowane z [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) , które ułatwiają kontrolowanie kluczy szyfrowania dysków i wpisów tajnych w ramach subskrypcji magazynu kluczy oraz zarządzanie nimi. Rozwiązanie gwarantuje również, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w usłudze Azure Storage.

Poniżej przedstawiono najlepsze rozwiązania dotyczące korzystania z Azure Disk Encryption:

**Najlepsze rozwiązanie**: Włącz szyfrowanie na maszynach wirtualnych.   
**Szczegóły**: Azure Disk Encryption generuje i zapisuje klucze szyfrowania do magazynu kluczy. Zarządzanie kluczami szyfrowania w magazynie kluczy wymaga uwierzytelniania usługi Azure AD. Utwórz w tym celu aplikację usługi Azure AD. Do celów uwierzytelniania można użyć uwierzytelniania opartego na kluczu tajnym klienta lub [uwierzytelniania usługi Azure AD opartego na certyfikatach klienta](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

**Najlepsze rozwiązanie**: Użyj klucza szyfrowania klucza (KEK) w celu uzyskania dodatkowej warstwy zabezpieczeń dla kluczy szyfrowania. Dodaj KEK do magazynu kluczy.   
**Szczegóły**: Użyj polecenia cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) , aby utworzyć klucz szyfrowania klucza w magazynie kluczy. Możesz również zaimportować KEK z lokalnego sprzętowego modułu zabezpieczeń (HSM) do zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [dokumentację Key Vault](../../key-vault/keys/hsm-protected-keys.md). W przypadku określenia klucza szyfrowania klucza Azure Disk Encryption używa tego klucza do zawijania wpisów tajnych szyfrowania przed zapisem w Key Vault. Przechowywanie kopii tego klucza w ramach lokalnego modułu HSM zarządzania kluczami zapewnia dodatkową ochronę przed przypadkowym usunięciem kluczy.

**Najlepsze rozwiązanie**: wykonaj [migawkę](../../virtual-machines/windows/snapshot-copy-managed-disk.md) i/lub kopię zapasową przed zaszyfrowaniem dysków. Kopie zapasowe zapewniają opcję odzyskiwania, jeśli wystąpi nieoczekiwany błąd podczas szyfrowania.   
**Szczegóły**: maszyny wirtualne z dyskami zarządzanymi wymagają utworzenia kopii zapasowej przed wystąpieniem szyfrowania. Po wykonaniu kopii zapasowej można użyć polecenia cmdlet **Set-AzVMDiskEncryptionExtension** w celu zaszyfrowania dysków zarządzanych przez określenie parametru *-skipVmBackup* . Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania szyfrowanych maszyn wirtualnych, zobacz artykuł [Azure Backup](../../backup/backup-azure-vms-encryption.md) .

**Najlepsze rozwiązanie**: aby upewnić się, że wpisy tajne szyfrowania nie przekraczają granic regionalnych, Azure Disk Encryption potrzebuje magazynu kluczy i maszyn wirtualnych znajdujących się w tym samym regionie.   
**Szczegóły**: Utwórz i Użyj magazynu kluczy znajdującego się w tym samym regionie co maszyna wirtualna, która ma być szyfrowana.

Po zastosowaniu Azure Disk Encryption można spełnić następujące wymagania biznesowe:

- Maszyny wirtualne IaaS są zabezpieczone przez standardową branżową technologię szyfrowania, aby sprostać wymaganiom bezpieczeństwa i zgodności w organizacji.
- Maszyny wirtualne IaaS są uruchamiane w obszarze klucze i zasady kontrolowane przez klienta. Możesz także przeprowadzić inspekcję ich użycia w magazynie kluczy.

## <a name="restrict-direct-internet-connectivity"></a>Ograniczanie bezpośredniej łączności z Internetem
Monitorowanie i ograniczanie łączności z Internetem bezpośrednio z maszyn wirtualnych. Osoby atakujące stale skanują zakresy adresów IP w chmurze publicznej dla otwartych portów zarządzania i podejmują próby ataków typu "łatwe", takie jak typowe hasła i znane luki w zabezpieczeniach. W poniższej tabeli przedstawiono najlepsze rozwiązania ułatwiające ochronę przed atakami:

**Najlepsze rozwiązanie**: zapobiegaj nieumyślnemu narażeniu na Routing i zabezpieczenia sieci.   
**Szczegóły**: Użyj RBAC, aby upewnić się, że tylko Centralna Grupa sieciowa ma uprawnienia do zasobów sieciowych.

**Najlepsze rozwiązanie**: Identyfikowanie i korygowanie narażonych maszyn wirtualnych, które zezwalają na dostęp z "dowolnego" źródłowego adresu IP.   
**Szczegóły**: Użyj Azure Security Center. Security Center zaleca się ograniczyć dostęp za pośrednictwem punktów końcowych dostępnych z Internetu, jeśli dowolna z grup zabezpieczeń sieci ma co najmniej jedną regułę ruchu przychodzącego, która zezwala na dostęp z "dowolnego" źródłowego adresu IP. Security Center będzie zalecać edytowanie tych reguł ruchu przychodzącego w celu [ograniczenia dostępu](../../security-center/security-center-network-recommendations.md) do źródłowych adresów IP, które w rzeczywistości potrzebują dostępu.

**Najlepsze rozwiązanie**: ograniczanie portów zarządzania (RDP, SSH).   
**Szczegóły**: [dostęp do maszyny wirtualnej just-in-Time (JIT)](../../security-center/security-center-just-in-time.md) może służyć do blokowania ruchu przychodzącego do maszyn wirtualnych platformy Azure, co pozwala ograniczyć narażenie na ataki, zapewniając łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby. Po włączeniu JIT Security Center blokuje ruch przychodzący do maszyn wirtualnych platformy Azure przez utworzenie reguły sieciowej grupy zabezpieczeń. Wybierasz porty na maszynie wirtualnej, do której zostanie zablokowany ruch przychodzący. Te porty są kontrolowane przez rozwiązanie JIT.

## <a name="next-steps"></a>Następne kroki
Zobacz [najlepsze rozwiązania i wzorce dotyczące zabezpieczeń platformy Azure](best-practices-and-patterns.md) , aby uzyskać więcej najlepszych rozwiązań w zakresie zabezpieczeń, które są używane podczas projektowania i wdrażania rozwiązań w chmurze oraz zarządzania nimi przy użyciu platformy Azure.

Dostępne są następujące zasoby umożliwiające dostarczenie bardziej ogólnych informacji na temat zabezpieczeń platformy Azure i powiązanych usług firmy Microsoft:
* [Blog zespołu ds. zabezpieczeń platformy Azure](https://blogs.msdn.microsoft.com/azuresecurity/) — na bieżąco z najnowszymi informacjami na temat zabezpieczeń platformy Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — w przypadku których luki w zabezpieczeniach firmy Microsoft, w tym problemy z platformą Azure, mogą być zgłaszane lub wysyłane pocztą e-mail secure@microsoft.com
