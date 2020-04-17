---
title: Najważniejsze wskazówki dotyczące zabezpieczeń dla obciążeń usługi IaaS na platformie Azure | Dokumenty firmy Microsoft
description: " Migracja obciążeń do usługi Azure IaaS daje możliwości ponownej oceny naszych projektów "
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
ms.openlocfilehash: 49a40d78b4ba3bc1e90bb341cca90bece0b998a8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450019"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Najlepsze rozwiązania dotyczące zabezpieczeń dla obciążeń IaaS na platformie Azure
W tym artykule opisano najważniejsze wskazówki dotyczące zabezpieczeń dla maszyn wirtualnych i systemów operacyjnych.

Najlepsze rozwiązania są oparte na konsensusie opinii i działają z bieżącymi możliwościami platformy Azure i zestawami funkcji. Ponieważ opinie i technologie mogą się zmieniać w czasie, ten artykuł zostanie zaktualizowany w celu odzwierciedlenia tych zmian.

W większości scenariuszy infrastruktury jako usługi (IaaS) [maszyny wirtualne platformy Azure (VM)](/azure/virtual-machines/) są głównym obciążeniem dla organizacji korzystających z przetwarzania w chmurze. Ten fakt jest widoczny w [scenariuszach hybrydowych,](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) w których organizacje chcą powoli migrować obciążenia do chmury. W takich scenariuszach postępuj zgodnie z [ogólnymi zagadnieniami zabezpieczeń dla usługi IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)i zastosuj najlepsze rozwiązania dotyczące zabezpieczeń do wszystkich maszyn wirtualnych.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Ochrona maszyn wirtualnych przy użyciu uwierzytelniania i kontroli dostępu
Pierwszym krokiem w ochronie maszyn wirtualnych jest zapewnienie, że tylko autoryzowani użytkownicy mogą konfigurowania nowych maszyn wirtualnych i uzyskiwać dostęp do maszyn wirtualnych.

> [!NOTE]
> Aby zwiększyć bezpieczeństwo maszyn wirtualnych z systemem Linux na platformie Azure, można zintegrować z uwierzytelnianiem usługi Azure AD. Korzystając z [uwierzytelniania usługi Azure AD dla maszyn wirtualnych z systemem Linux,](/azure/virtual-machines/linux/login-using-aad)można centralnie kontrolować i wymuszać zasady, które zezwalają lub odmawiają dostępu do maszyn wirtualnych.
>
>

**Najlepsze rozwiązanie**: Kontrola dostępu do maszyn wirtualnych.   
**Szczegóły:** Użyj [zasad platformy Azure,](/azure/azure-policy/azure-policy-introduction) aby ustanowić konwencje dla zasobów w organizacji i utworzyć dostosowane zasady. Zastosuj te zasady do zasobów, takich jak [grupy zasobów](/azure/azure-resource-manager/resource-group-overview). Maszyny wirtualne należące do grupy zasobów dziedziczą jej zasady.

Jeśli Twoja organizacja ma wiele subskrypcji, konieczny może być sposób na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. [Grupy zarządzania platformy Azure](/azure/azure-resource-manager/management-groups-overview) zapewniają poziom zakresu powyżej subskrypcji. Organizujesz subskrypcje w grupy zarządzania (kontenery) i stosujesz warunki nadzoru do tych grup. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki zastosowane do grupy. Grupy zarządzania umożliwiają zarządzanie klasy korporacyjnej na dużą skalę niezależnie od typu subskrypcji.

**Najlepsze rozwiązanie:** Zmniejsz zmienność konfiguracji i wdrażania maszyn wirtualnych.   
**Szczegóły:** Użyj szablonów [usługi Azure Resource Manager,](/azure/azure-resource-manager/resource-group-authoring-templates) aby wzmocnić wybory wdrażania i ułatwić zrozumienie i spis maszyn wirtualnych w twoim środowisku.

**Najlepsze rozwiązanie**: Bezpieczny uprzywilejowany dostęp.   
**Szczegóły:** Użyj [podejścia z najmniejszymi uprawnieniami](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) i wbudowanych ról platformy Azure, aby umożliwić użytkownikom dostęp do maszyn wirtualnych i konfigurowanie ich:

- [Współautor maszyny wirtualnej:](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)Może zarządzać maszynami wirtualnymi, ale nie z kontem sieci wirtualnej lub magazynu, z którym są połączone.
- [Klasyczny współautor maszyny wirtualnej:](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor)Może zarządzać maszynami wirtualnymi utworzonymi przy użyciu klasycznego modelu wdrażania, ale nie z kontem sieci wirtualnej lub magazynu, z którym są połączone maszyny wirtualne.
- [Administrator zabezpieczeń:](../../role-based-access-control/built-in-roles.md#security-admin)Tylko w Centrum zabezpieczeń: może wyświetlać zasady zabezpieczeń, wyświetlać stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlać alerty i zalecenia, odrzucać alerty i zalecenia.
- [DevTest Labs Użytkownik:](../../role-based-access-control/built-in-roles.md#devtest-labs-user)Można wyświetlić wszystko i połączyć, uruchomić, uruchom ponownie i zamknąć maszyny wirtualne.

Administratorzy subskrypcji i współadministratorzy mogą zmienić to ustawienie, czyniąc ich administratorami wszystkich maszyn wirtualnych w ramach subskrypcji. Upewnij się, że ufasz wszystkim administratorom subskrypcji i coadmins zalogować się do dowolnego komputera.

> [!NOTE]
> Zaleca się konsolidację maszyn wirtualnych z tym samym cyklem życia w tej samej grupie zasobów. Korzystając z grup zasobów, można wdrażać, monitorować i zwijać koszty rozliczeń zasobów.
>
>

Organizacje, które kontrolują dostęp do maszyn wirtualnych i ich konfiguracja poprawić swoje ogólne zabezpieczenia maszyn wirtualnych.

## <a name="use-multiple-vms-for-better-availability"></a>Korzystaj z wielu maszyn wirtualnych, aby uzyskać lepszą dostępność
Jeśli maszyna wirtualna uruchamia krytyczne aplikacje, które muszą mieć wysoką dostępność, zdecydowanie zaleca się użycie wielu maszyn wirtualnych. Aby uzyskać lepszą dostępność, użyj [zestawu dostępności](../../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) lub [stref](../../availability-zones/az-overview.md)dostępności .

Zestaw dostępności to logiczne grupowanie, którego można użyć na platformie Azure, aby upewnić się, że zasoby maszyny Wirtualnej, które umieszczasz w nim, są odizolowane od siebie, gdy są wdrażane w centrum danych platformy Azure. Platforma Azure zapewnia, że maszyny wirtualne umieszczane w zestawie dostępności są uruchamiane na wielu serwerach fizycznych, stojakach obliczeniowych, jednostkach magazynu i przełącznikach sieciowych. Jeśli wystąpi awaria sprzętu lub oprogramowania platformy Azure, dotyczy to tylko podzbioru maszyn wirtualnych, a ogólna aplikacja będzie nadal dostępna dla klientów. Zestawy dostępności są niezbędną funkcją, gdy chcesz tworzyć niezawodne rozwiązania w chmurze.

## <a name="protect-against-malware"></a>Ochrona przed złośliwym oprogramowaniem
Należy zainstalować ochronę przed złośliwym oprogramowaniem, aby zidentyfikować i usunąć wirusy, programy szpiegujące i inne złośliwe oprogramowanie. Można zainstalować program [Microsoft Antimalware](antimalware.md) lub rozwiązanie do ochrony punktów końcowych partnera firmy Microsoft ([Trend Micro](https://help.deepsecurity.trendmicro.com/Welcome.html), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security)i System [Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

Ochrona przed złośliwym oprogramowaniem firmy Microsoft zawiera funkcje, takie jak ochrona w czasie rzeczywistym, zaplanowane skanowanie, korygowanie złośliwego oprogramowania, aktualizacje podpisów, aktualizacje aparatu, raportowanie próbek i zbieranie zdarzeń wykluczenia. W środowiskach, które są hostowane oddzielnie od środowiska produkcyjnego, można użyć rozszerzenia ochrony przed złośliwym oprogramowaniem, aby chronić maszyny wirtualne i usługi w chmurze.

Rozwiązania microsoft antimalware i partnerów z [usługą Azure Security Center](../../security-center/index.yml) ułatwiają wdrażanie i wbudowane wykrywania (alerty i zdarzenia).

**Najlepsze rozwiązanie:** Zainstaluj rozwiązanie chroniące przed złośliwym oprogramowaniem, aby chronić przed złośliwym oprogramowaniem.   
**Szczegóły**: [Instalowanie rozwiązania partnerskiego firmy Microsoft lub oprogramowania antywirusowego Microsoft Antimalware](../../security-center/security-center-install-endpoint-protection.md)

**Najlepsze rozwiązanie:** Zintegruj rozwiązanie chroniące przed złośliwym oprogramowaniem z centrum zabezpieczeń, aby monitorować stan ochrony.   
**Szczegóły:** [Zarządzanie problemami z ochroną punktów końcowych za pomocą centrum zabezpieczeń](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Zarządzanie aktualizacjami maszyn wirtualnych
Maszyny wirtualne platformy Azure, podobnie jak wszystkie lokalne maszyny wirtualne, mają być zarządzane przez użytkownika. Platforma Azure nie wypycha do nich aktualizacji systemu Windows. Musisz zarządzać aktualizacjami maszyn wirtualnych.

**Najlepsze rozwiązanie:** Dbaj o aktualność maszyn wirtualnych.   
**Szczegóły:** Użyj rozwiązania [do zarządzania aktualizacjami](../../automation/automation-update-management.md) w usłudze Azure Automation do zarządzania aktualizacjami systemu operacyjnego dla komputerów z systemem Windows i Linux, które są wdrażane na platformie Azure, w środowiskach lokalnych lub w innych dostawcach chmury. Umożliwia ono szybką ocenę stanu dostępnych aktualizacji na wszystkich komputerach agentów oraz zarządzanie procesem instalacji wymaganych aktualizacji serwerów.

W celu przeprowadzania ocen i wdrożeń aktualizacji na komputerach zarządzanych przez rozwiązanie Update Management są używane następujące konfiguracje:

- Program Microsoft Monitoring Agent (MMA) dla systemu Windows lub Linux
- Platforma PowerShell Desired State Configuration (DSC) dla systemu Linux
- Hybrydowy proces roboczy elementu runbook usługi Automation
- Usługa Microsoft Update lub Windows Server Update Services (WSUS) dla komputerów z systemem Windows

Jeśli korzystasz z usługi Windows Update, pozostaw włączone ustawienie automatycznej usługi Windows Update.

**Najlepsze rozwiązanie:** Upewnij się, że podczas wdrażania utworzone obrazy zawierają najnowszą rundę aktualizacji systemu Windows.   
**Szczegóły:** Sprawdź i zainstaluj wszystkie aktualizacje systemu Windows jako pierwszy krok każdego wdrożenia. Ta miara jest szczególnie ważne, aby zastosować podczas wdrażania obrazów, które pochodzą z ciebie lub własnej biblioteki. Mimo że obrazy z portalu Azure Marketplace są domyślnie aktualizowane automatycznie, po wydaniu publicznym może wystąpić opóźnienie (do kilku tygodni).

**Najlepsze rozwiązanie:** Okresowo ponownie rozmieszczaj maszyny wirtualne, aby wymusić nową wersję systemu operacyjnego.   
**Szczegóły:** Zdefiniuj maszynę wirtualną za pomocą [szablonu usługi Azure Resource Manager,](../../azure-resource-manager/templates/template-syntax.md) dzięki czemu można ją łatwo wdrożyć ponownie. Za pomocą szablonu daje poprawione i bezpieczne maszyny Wirtualnej, gdy jest to potrzebne.

**Najlepsze rozwiązanie:** Szybkie stosowanie aktualizacji zabezpieczeń do maszyn wirtualnych.   
**Szczegóły:** Włącz usługę Azure Security Center (warstwa bezpłatna lub warstwa standardowa), aby [zidentyfikować brakujące aktualizacje zabezpieczeń i zastosować je](../../security-center/security-center-apply-system-updates.md).

**Najważniejsze rozwiązanie:** Zainstaluj najnowsze aktualizacje zabezpieczeń.   
**Szczegóły:** Niektóre z pierwszych obciążeń, które klienci przenoszą na platformę Azure, to laboratoria i systemy zewnętrzne. Jeśli maszyny wirtualne platformy Azure host aplikacji lub usług, które muszą być dostępne dla Internetu, należy zachować czujność na temat poprawek. Łata poza systemem operacyjnym. Niezałatane luki w zabezpieczeniach aplikacji partnerskich mogą również prowadzić do problemów, których można uniknąć, jeśli istnieje dobre zarządzanie poprawkami.

**Najlepsze rozwiązanie:** Wdrażanie i testowanie rozwiązania do tworzenia kopii zapasowych.   
**Szczegóły:** Kopia zapasowa musi być obsługiwana w taki sam sposób, jak każda inna operacja. Dotyczy to systemów, które są częścią środowiska produkcyjnego rozszerzającego się na chmurę.

Systemy testowe i dewelopera muszą być zgodne ze strategiami tworzenia kopii zapasowych, które zapewniają możliwości przywracania, które są podobne do tego, do którego użytkownicy przyzwyczaili się, na podstawie ich doświadczenia ze środowiskami lokalnymi. Obciążenia produkcyjne przeniesione na platformę Azure powinny integrować się z istniejącymi rozwiązaniami do tworzenia kopii zapasowych, jeśli to możliwe. Możesz też użyć [usługi Azure Backup,](../../backup/backup-azure-vms-first-look-arm.md) aby spełnić wymagania dotyczące tworzenia kopii zapasowych.

Organizacje, które nie wymuszają zasad aktualizacji oprogramowania, są bardziej narażone na zagrożenia wykorzystujące znane, wcześniej naprawione luki w zabezpieczeniach. Aby zapewnić zgodność z przepisami branżowymi, firmy muszą udowodnić, że są staranne i stosują odpowiednie środki kontroli bezpieczeństwa, aby zapewnić bezpieczeństwo swoich obciążeń znajdujących się w chmurze.

Najlepsze rozwiązania dotyczące aktualizacji oprogramowania dla tradycyjnego centrum danych i usługi Azure IaaS mają wiele podobieństw. Zaleca się, aby ocenić bieżące zasady aktualizacji oprogramowania w celu uwzględnienia maszyn wirtualnych znajdujących się na platformie Azure.

## <a name="manage-your-vm-security-posture"></a>Zarządzanie postawą bezpieczeństwa maszyn wirtualnych
Cyberzagrożenia ewoluują. Ochrona maszyn wirtualnych wymaga możliwości monitorowania, które mogą szybko wykrywać zagrożenia, zapobiegać nieautoryzowanemu dostępowi do zasobów, wyzwalać alerty i zmniejszać liczbę fałszywych alarmów.

Aby monitorować poziom zabezpieczeń maszyn wirtualnych z [systemem Windows](../../security-center/security-center-virtual-machine.md) i [Linux,](../../security-center/security-center-linux-virtual-machine.md)należy użyć [usługi Azure Security Center](../../security-center/security-center-intro.md). W umyku zabezpieczeń w uchodz maszyny wirtualne, korzystając z następujących możliwości:

- Zastosuj ustawienia zabezpieczeń systemu operacyjnego z zalecanymi regułami konfiguracji.
- Identyfikowanie i pobieranie zabezpieczeń systemu i aktualizacji krytycznych, których może brakować.
- Wdrażanie zaleceń dotyczących ochrony przed złośliwym oprogramowaniem w punktach końcowych.
- Sprawdzanie poprawności szyfrowania dysku.
- Ocena i usuwanie luk w zabezpieczeniach.
- Wykrywanie zagrożeń.

Usługa Security Center może aktywnie monitorować zagrożenia, a potencjalne zagrożenia są widoczne w alertach zabezpieczeń. Skorelowane zagrożenia są agregowane w jednym widoku nazywanym incydentem zabezpieczeń.

Usługa Security Center przechowuje dane w [dziennikach usługi Azure Monitor](/azure/log-analytics/log-analytics-overview). Dzienniki usługi Azure Monitor udostępnia język zapytań i aparat analizy, który zapewnia wgląd w działanie aplikacji i zasobów. Dane są również zbierane z [usługi Azure Monitor,](../../batch/monitoring-overview.md)rozwiązań do zarządzania i agentów zainstalowanych na maszynach wirtualnych w chmurze lub lokalnie. Ta wspólna funkcjonalność pomaga utworzyć pełny obraz środowiska.

Organizacje, które nie wymuszają silnych zabezpieczeń dla swoich maszyn wirtualnych, pozostają nieświadome potencjalnych prób obejścia przez nieautoryzowanych użytkowników kontroli zabezpieczeń.

## <a name="monitor-vm-performance"></a>Monitorowanie wydajności maszyny wirtualnej
Nadużywanie zasobów może być problemem, gdy procesy maszyn wirtualnych zużywają więcej zasobów niż powinny. Problemy z wydajnością maszyny Wirtualnej może prowadzić do zakłóceń w usłudze, co narusza zasadę zabezpieczeń dostępności. Jest to szczególnie ważne w przypadku maszyn wirtualnych obsługujących usługi IIS lub innych serwerów sieci web, ponieważ wysokie użycie procesora CPU lub pamięci może wskazywać na atak typu "odmowa usługi" (DoS). Konieczne jest monitorowanie dostępu do maszyny Wirtualnej nie tylko reaktywnie podczas wystąpienia problemu, ale także proaktywnie w stosunku do wydajności linii bazowej mierzonej podczas normalnej pracy.

Zaleca się, aby użyć [usługi Azure Monitor,](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) aby uzyskać wgląd w kondycję zasobu. Funkcje usługi Azure Monitor:

- [Pliki dziennika diagnostycznego zasobów:](../../azure-monitor/platform/platform-logs-overview.md)monitoruje zasoby maszyny Wirtualnej i identyfikuje potencjalne problemy, które mogą naruszyć wydajność i dostępność.
- [Rozszerzenie diagnostyki platformy Azure:](/azure/azure-monitor/platform/diagnostics-extension-overview)zapewnia funkcje monitorowania i diagnostyki na maszynach wirtualnych z systemem Windows. Można włączyć te możliwości, dołączając rozszerzenie jako część [szablonu usługi Azure Resource Manager](/azure/virtual-machines/windows/extensions-diagnostics-template).

Organizacje, które nie monitorują wydajności maszyny Wirtualnej, nie mogą określić, czy niektóre zmiany wzorców wydajności są normalne czy nieprawidłowe. Maszyna wirtualna, która zużywa więcej zasobów niż zwykle może wskazywać na atak z zewnętrznego zasobu lub proces złamany problem uruchomiony na maszynie Wirtualnej.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Szyfrowanie plików wirtualnego dysku twardego
Zaleca się szyfrowanie wirtualnych dysków twardych (VHD), aby chronić wolumin rozruchowy i woluminy danych w magazynie, a także klucze szyfrowania i wpisy tajne.

[Usługa Azure Disk Encryption](../azure-security-disk-encryption-overview.md) ułatwia szyfrowanie dysków maszyn wirtualnych systemu Windows i Linux IaaS. Usługa Azure Disk Encryption korzysta z standardowej funkcji [funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) systemu Windows i funkcji [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) systemu Linux w celu zapewnienia szyfrowania woluminów dla systemu operacyjnego i dysków z danymi. Rozwiązanie jest zintegrowane z [usługą Azure Key Vault,](https://azure.microsoft.com/documentation/services/key-vault/) aby ułatwić kontrolowanie kluczy szyfrowania dysków i wpisów tajnych w subskrypcji magazynu kluczy i zarządzanie nimi. Rozwiązanie zapewnia również, że wszystkie dane na dyskach maszyny wirtualnej są szyfrowane w spoczynku w usłudze Azure Storage.

Poniżej przedstawiono najlepsze rozwiązania dotyczące korzystania z szyfrowania dysków platformy Azure:

**Najlepsze rozwiązanie:** Włącz szyfrowanie na maszynach wirtualnych.   
**Szczegóły:** Szyfrowanie dysków platformy Azure generuje i zapisuje klucze szyfrowania w magazynie kluczy. Zarządzanie kluczami szyfrowania w magazynie kluczy wymaga uwierzytelniania usługi Azure AD. W tym celu utwórz aplikację usługi Azure AD. Do celów uwierzytelniania można użyć uwierzytelniania opartego na kluczu tajnym klienta lub [uwierzytelniania usługi Azure AD opartego na certyfikatach klienta.](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)

**Najlepsze rozwiązanie:** Użyj klucza szyfrowania klucza (KEK) dla dodatkowej warstwy zabezpieczeń kluczy szyfrowania. Dodaj KEK do magazynu kluczy.   
**Szczegóły:** Użyj polecenia cmdlet [Add-AzKeyVaultKey,](/powershell/module/az.keyvault/add-azkeyvaultkey) aby utworzyć klucz szyfrowania klucza w magazynie kluczy. Można również zaimportować KEK z lokalnego sprzętowego modułu zabezpieczeń (HSM) do zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [dokumentację magazynu kluczy](../../key-vault/keys/hsm-protected-keys.md). Po określeniu klucza szyfrowania klucza szyfrowanie platformy Azure Disk Encryption używa tego klucza do zawijania klucza tajne szyfrowania przed zapisaniem do usługi Key Vault. Przechowywanie kopii escrow tego klucza w lokalnym zarządzania kluczami HSM oferuje dodatkową ochronę przed przypadkowym usunięciem kluczy.

**Najlepsze rozwiązanie:** Zrób [migawkę](../../virtual-machines/windows/snapshot-copy-managed-disk.md) i/lub utwórz kopię zapasową, zanim dyski zostaną zaszyfrowane. Kopie zapasowe zapewniają opcję odzyskiwania, jeśli podczas szyfrowania występuje nieoczekiwana awaria.   
**Szczegóły:** Maszyny wirtualne z dyskami zarządzanymi wymagają kopii zapasowej przed wystąpieniem szyfrowania. Po wykonaniu kopii zapasowej można użyć polecenia cmdlet **Set-AzVMDiskEncryptionExtension** do szyfrowania dysków zarządzanych, określając parametr *-skipVmBackup.* Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania zaszyfrowanych maszyn wirtualnych, zobacz artykuł [usługi Azure Backup.](../../backup/backup-azure-vms-encryption.md)

**Najlepsze rozwiązanie:** Aby upewnić się, że wpisy tajne szyfrowania nie przekraczają granic regionalnych, szyfrowanie dysków platformy Azure wymaga magazynu kluczy i maszyn wirtualnych, które mają znajdować się w tym samym regionie.   
**Szczegóły:** Tworzenie i używanie magazynu kluczy, który znajduje się w tym samym regionie co maszyna wirtualna do zaszyfrowania.

Po zastosowaniu szyfrowania dysków platformy Azure można spełnić następujące potrzeby biznesowe:

- Maszyny wirtualne IaaS są zabezpieczone w spoczynku za pomocą standardowej w branży technologii szyfrowania, aby spełnić wymagania dotyczące zabezpieczeń i zgodności organizacji.
- Maszyny wirtualne IaaS rozpoczynają się w ramach kluczy i zasad kontrolowanych przez klienta i można przeprowadzać inspekcję ich użycia w magazynie kluczy.

## <a name="restrict-direct-internet-connectivity"></a>Ograniczanie bezpośredniej łączności z Internetem
Monitoruj i ograniczaj bezpośrednią łączność z Internetem maszyny Wirtualnej. Osoby atakujące stale skanują zakresy adresów IP w chmurze publicznej w poszukiwaniu otwartych portów zarządzania i próbują "łatwych" ataków, takich jak typowe hasła i znane niezałatane luki w zabezpieczeniach. W poniższej tabeli wymieniono najlepsze rozwiązania ułatwiające ochronę przed tymi atakami:

**Najlepsze rozwiązanie:** Zapobiegaj przypadkowemu narażeniu na routing sieci i bezpieczeństwo.   
**Szczegóły:** Użyj RBAC, aby upewnić się, że tylko centralna grupa sieci ma uprawnienia do zasobów sieciowych.

**Najlepsze rozwiązanie:** Identyfikowanie i korygowanie narażonych maszyn wirtualnych, które umożliwiają dostęp z "dowolnego" źródłowego adresu IP.   
**Szczegóły:** Użyj Usługi Azure Security Center. Usługa Security Center zaleca ograniczenie dostępu za pośrednictwem punktów końcowych z dostępem do Internetu, jeśli którakolwiek z sieciowych grup zabezpieczeń ma jedną lub więcej reguł przychodzących, które zezwalają na dostęp z "dowolnego" źródłowego adresu IP. Usługa Security Center zaleca edycję tych reguł ruchu przychodzącego w celu [ograniczenia dostępu](../../security-center/security-center-network-recommendations.md) do źródłowych adresów IP, które faktycznie potrzebują dostępu.

**Najlepsze rozwiązanie:** Ogranicz porty zarządzania (RDP, SSH).   
**Szczegóły:** [Dostęp do maszyn wirtualnych just-in-time (JIT)](../../security-center/security-center-just-in-time.md) może służyć do blokowania ruchu przychodzącego do maszyn wirtualnych platformy Azure, zmniejszając narażenie na ataki, zapewniając jednocześnie łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby. Gdy JIT jest włączona, Usługa Security Center blokuje ruch przychodzący do maszyn wirtualnych platformy Azure, tworząc regułę sieciowej grupy zabezpieczeń. Wybierz porty na maszynie Wirtualnej, do których ruch przychodzący zostanie zablokowany. Porty te są kontrolowane przez rozwiązanie JIT.

## <a name="next-steps"></a>Następne kroki
Zobacz [najlepsze rozwiązania i wzorce zabezpieczeń platformy Azure,](best-practices-and-patterns.md) aby uzyskać więcej najlepszych rozwiązań w zakresie zabezpieczeń, których należy używać podczas projektowania, wdrażania i zarządzania rozwiązaniami w chmurze przy użyciu platformy Azure.

Dostępne są następujące zasoby, aby zapewnić bardziej ogólne informacje na temat zabezpieczeń platformy Azure i powiązanych usług firmy Microsoft:
* [Blog zespołu zabezpieczeń platformy Azure](https://blogs.msdn.microsoft.com/azuresecurity/) — aby uzyskać aktualne informacje na temat najnowszych informacji w usłudze Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — gdzie można zgłaszać luki w zabezpieczeniach firmy Microsoft, w tym problemy z platformą Azure, lubsecure@microsoft.com
