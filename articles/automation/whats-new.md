---
title: Co nowego w programie Azure Automation
description: Ważne aktualizacje Azure Automation aktualizowane każdego miesiąca.
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 04/09/2021
ms.custom: references_regions
ms.openlocfilehash: f8b4d6965a8a1f046fd2459ce9fe5cce8ea45443
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531070"
---
# <a name="whats-new-in-azure-automation"></a>Co nowego w programie Azure Automation?

Azure Automation są na bieżąco ulepszane. Aby być na bieżąco z najnowszymi zmianami, ten artykuł zawiera informacje na temat:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów

Ta strona jest aktualizowana co miesiąc, więc regularnie ją odwiedzaj.

## <a name="march-2021"></a>Marzec 2021 r.

### <a name="new-azure-automation-built-in-policies"></a>Nowe Azure Automation wbudowanych zasad

**Wpisz:** Nowa funkcja

Azure Automation dodano 5 nowych wbudowanych zasad:

- Konta usługi Automation powinny wyłączać dostęp do sieci publicznej.
- Azure Automation powinny używać kluczy zarządzanych przez klienta do szyfrowania danych w spoczynku
- Konfigurowanie Azure Automation w celu wyłączenia dostępu do sieci publicznej
- Konfigurowanie połączeń z prywatnym punktem końcowym na Azure Automation kont
- Połączenia z prywatnym punktem końcowym na kontach usługi Automation powinny być włączone.

Aby uzyskać [więcej informacji,](./policy-reference.md) zobacz artykuł z informacjami o zasadach.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-south-india"></a>Obsługa automatyzacji i zadeklarowanej State Configuration ga w Indiach Południowo-Wschodnich

**Wpisz:** Nowa funkcja

Korzystanie z funkcji automatyzacji procesów i konfiguracji stanu w Indiach Południowej. Przeczytaj [ogłoszenie,](https://azure.microsoft.com/updates/azure-automation-in-south-india-region/) aby uzyskać więcej informacji.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uk-west"></a>Obsługa automatyzacji i State Configuration zadeklarowana ga w Zachodnie Zjednoczone Królestwo

**Wpisz:** Nowa funkcja

Korzystaj z funkcji automatyzacji procesów i konfiguracji stanu w Zachodnie Zjednoczone Królestwo. Aby uzyskać więcej informacji, przeczytaj [anons](https://azure.microsoft.com/updates/azure-automation-in-uk-west-region/).

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uae-central"></a>Obsługa automatyzacji i State Configuration zadeklarowana ga w Środkowych Zjednoczone Emiratach Zjednoczonych

**Wpisz:** Nowa funkcja

Korzystanie z funkcji automatyzacji procesów i konfiguracji stanu w środkowych Stanach Zjednoczonych. Przeczytaj [ogłoszenie,](https://azure.microsoft.com/updates/azure-automation-in-uae-central-region/) aby uzyskać więcej informacji.

### <a name="support-for-automation-and-state-configuration-available-in-australia-central-2--norway-west-and-france-south"></a>Obsługa automatyzacji i State Configuration australia środkowa 2, Kanada Zachodnia i Francja Południowa

**Wpisz:** Nowa funkcja

Aby uzyskać więcej informacji na [stronie Rezydencja danych,](https://azure.microsoft.com/global-infrastructure/data-residency/) wybierz obszar geograficzny dla każdego regionu.

### <a name="new-scripts-added-for-installing-hybrid-worker-on-windows-and-linux"></a>Dodano nowe skrypty do instalowania hybrydowego procesu roboczego w systemach Windows i Linux

**Wpisz:** Nowa funkcja

Do repozytorium github usługi Azure Automation dodano dwa nowe [skrypty](https://github.com/azureautomation) dotyczące jednego z kluczowych scenariuszy usługi Azure Automation dotyczących konfigurowania hybrydowego procesu roboczego runbook na maszynie z systemem Windows lub Linux. Skrypt tworzy nową maszynę wirtualną lub używa istniejącej, w razie potrzeby tworzy obszar roboczy usługi Log Analytics, instaluje agenta usługi Log Analytics dla systemu Windows lub agenta usługi Log Analytics dla systemu Linux i rejestruje maszynę w obszarze roboczym usługi Log Analytics. Skrypt systemu Windows nosi nazwę **Create Automation Windows HybridWorker (Utwórz** automatyzację w systemie Windows HybridWorker), a skrypt systemu Linux to Create Automation Linux **HybridWorker (Utwórz automatyzację w systemie Linux HybridWorker).**

### <a name="invoke-runbook-through-an-azure-resource-manager-template-webhook"></a>Wywoływanie element runbook za pomocą Azure Resource Manager webhook szablonu

**Wpisz:** Nowa funkcja

Aby uzyskać więcej informacji, zobacz Używanie [element webhook z szablonu usługi ARM.](./automation-webhooks.md#use-a-webhook-from-an-arm-template)

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>Usługa Azure Update Management obsługuje teraz centos 8.x, Red Hat Enterprise Linux Server 8.x i SUSE Linux Enterprise Server 15

**Wpisz:** Nowa funkcja

Aby uzyskać [więcej informacji, zobacz pełną listę](./update-management/overview.md#supported-operating-systems) obsługiwanych systemów operacyjnych Linux.

### <a name="in-region-data-residency-support-for-brazil-south-and-south-east-asia"></a>Obsługa rezydencji danych w regionie dla Brazylii Południowej i Południowej Azja Wschodnia 

**Wpisz:** Nowa funkcja

We wszystkich regionach, z wyjątkiem Brazylii Południowej i Azji Południowo-Wschodniej, Azure Automation są przechowywane w innym regionie (sparowany region platformy Azure) w celu zapewnienia ciągłości działania i odzyskiwania po awarii (BCDR). W przypadku tylko regionów Brazylia i Azja Południowo-Wschodnia dane przechowywane Azure Automation w tym samym regionie, aby spełnić wymagania dotyczące rezydencji danych dla tych regionów. Zobacz [Replikacja geograficzna w Azure Automation,](./automation-managing-data.md#geo-replication-in-azure-automation) aby uzyskać więcej szczegółów.

## <a name="february-2021"></a>Luty 2021 r.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Obsługa usługi Automation i State Configuration ga w Japonii Zachodniej

**Wpisz:** Nowa funkcja

Konto usługi Automation i State Configuration dostępności w regionie Japonia Zachodnia. Aby uzyskać więcej informacji, przeczytaj [anons](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/).

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Wprowadzono niestandardową zgodność Azure Policy w celu wymuszenia wykonywania runbook w hybrydowym procesu roboczego

**Wpisz :** Nowa funkcja

Nowej reguły zgodności usługi Azure Policy można użyć, aby umożliwić uruchamianie zadań, webhook i harmonogramów zadań tylko w grupach hybrydowych procesów roboczych.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Update Management dostępności w regionach Wschodnie usa, Francja Środkowa i Europa Północna

**Wpisz:** Nowa funkcja

Funkcja Update Management Automation jest dostępna w regionach Wschodnie usa, Francja Środkowa i Europa Północna. Zobacz [Obsługiwane mapowanie regionów,](how-to/region-mappings.md) aby uzyskać informacje o aktualizacjach dokumentacji odzwierciedlających tę zmianę.

## <a name="january-2021"></a>Styczeń 2021 r.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>Obsługa automatyzacji i State Configuration z zadeklarowaną chętną chętną do pomocy technicznej w Szwajcarii Zachodniej

**Wpisz:** Nowa funkcja

Konto usługi Automation i State Configuration dostępności w regionie Szwajcaria Zachodnia. Aby uzyskać więcej informacji, przeczytaj [anons](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/).

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>Dodano skrypt języka Python 3 do importowania modułu z wieloma zależnościami

**Wpisz:** Nowa funkcja

Skrypt jest dostępny do pobrania z [repozytorium GitHub.](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py) 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Obsługa roli hybrydowego procesu roboczego runbook dla systemu Centos 8.x/RHEL 8.x/SLES 15

**Typu.** Nowa funkcja

Funkcja hybrydowego procesu roboczego elementu Runbook obsługuje dystrybucje centOS 8.x, REHL 8.x i SLES 15 tylko w przypadku automatyzacji procesów w hybrydowych procesach roboczych elementu Runbook. Zobacz [Obsługiwane systemy operacyjne,](automation-linux-hrw-install.md#supported-linux-operating-systems) aby uzyskać informacje o aktualizacjach dokumentacji w celu odzwierciedlenia tych zmian.

### <a name="update-management-and-change-tracking-availability-in-australia-east-east-asia-west-us-and-central-us-regions"></a>Update Management i Change Tracking w regionach Australia Wschodnia, Azja Wschodnia, Zachodnie stany USA i Środkowe stany USA

**Wpisz:** Nowa funkcja

Konta usługi Automation, Śledzenie zmian i spis i Update Management są dostępne w regionach Australia Wschodnia, Azja Wschodnia, Zachodnie stany USA i Środkowe stany USA. 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>Wprowadzono publiczną podglądu podręczników Runbook języka Python 3 w chmurze dla instytucji rządowych USA

**Wpisz:** Nowa funkcja Azure Automation wprowadzono obsługę publicznej wersji zapoznawczej chmury języka Python 3 i hybrydowego wykonywania elementu Runbook w regionach chmury dla instytucji rządowych USA. Aby uzyskać więcej informacji, zobacz [anons](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/).

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Azure Automation Runbook zostały przeniesione z Centrum skryptów TechNet do usługi GitHub

**Wpisz:** Planowanie zmian

Centrum skryptów TechNet jest wycofywowane, a wszystkie podręczniki Runbook hostowane w galerii runbook zostały przeniesione do naszej organizacji [usługi Automation w witrynie GitHub.](https://github.com/azureautomation) Aby uzyskać więcej informacji, [przeczytaj Azure Automation Runbook przenoszących się do usługi GitHub.](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)

## <a name="december-2020"></a>Grudzień 2020 r.

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure Automation i Update Management Private Link (ga ga ga)

**Wpisz:** Nowa funkcja

Azure Automation i Update Management w chmurze globalnej i dla instytucji rządowych na platformie Azure jest ogłoszona jako "ga ga ga". Azure Automation włączono obsługę Private Link zabezpieczania wykonywania runbook w roli hybrydowego procesu roboczego przy użyciu programu Update Management w celu stosowania poprawek maszyn, wywołania runbook za pośrednictwem tegohook i korzystania z usługi State Configuration w celu zapewnienia skarg maszyn. Aby uzyskać więcej informacji, przeczytaj [Azure Automation Private Link pomocy technicznej](https://azure.microsoft.com/updates/azure-automation-private-link)

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Azure Automation sklasyfikowane jako certyfikowane na poziomie Grade-C w zakresie ułatwień dostępu

**Wpisz:** Nowa funkcja

Funkcje ułatwień dostępu produktów firmy Microsoft pomagają agencjom spełnić globalne wymagania dotyczące ułatwień dostępu. Na stronie [z ogłoszeniem w](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) blogu wyszukaj **Azure Automation,** aby przeczytać raport o zgodności ułatwień dostępu dla usługi Automation.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>Obsługa automatyzacji i State Configuration gałązce w Północnej Zjednoczone Emiraty Północnej

**Wpisz:** Nowa funkcja

Konto usługi Automation i State Configuration dostępności w regionie Północne Zjednoczone Emiraty Północne. Aby uzyskać więcej informacji, przeczytaj [anons](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Obsługa automatyzacji i State Configuration ga-ga w Niemczech Zachodnio-środkowe

**Wpisz:** Nowa funkcja

Konto usługi Automation i State Configuration dostępności w regionie Niemcy Zachodnie. Aby uzyskać więcej informacji, przeczytaj [anons](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/).

### <a name="dsc-support-for-oracle-6-and-7"></a>Obsługa dsc dla oracle 6 i 7

**Wpisz:** Nowa funkcja

Zarządzanie maszynami Oracle Linux 6 i 7 przy użyciu usługi Automation State Configuration. Zobacz [Obsługiwane dystrybucje systemu Linux,](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) aby uzyskać informacje o aktualizacjach dokumentacji, aby odzwierciedlić te zmiany.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Publiczna wersja zapoznawcza dla podręczników Runbook języka Python3 w u usługi Automation

**Wpisz:** Nowa funkcja

Azure Automation teraz obsługuje wykonywanie hybrydowych i chmurowych runbook języka Python 3 w publicznej wersji zapoznawczej we wszystkich regionach w chmurze globalnej platformy Azure. Zobacz [anons](( https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) aby uzyskać więcej szczegółów.

## <a name="november-2020"></a>Listopad 2020 r.

### <a name="dsc-support-for-ubuntu-1804"></a>Obsługa DSC dla systemu Ubuntu 18.04

**Wpisz:** Nowa funkcja

Zobacz [Obsługiwane dystrybucje systemu Linux,](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) aby uzyskać aktualizacje dokumentacji odzwierciedlające te zmiany.

## <a name="october-2020"></a>Październik 2020 r.

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>Obsługa automatyzacji i State Configuration ga w Korei Północnej

**Wpisz:** Nowa funkcja

Konto usługi Automation i State Configuration dostępności w Korei Północnej. Aby uzyskać więcej informacji, przeczytaj [anons](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Obsługa automatyzacji i State Configuration ga w Brazylia Południowo-Wschodnia

**Wpisz:** Nowa funkcja

Konto usługi Automation i State Configuration dostępności w Brazylia Południowo-Wschodnia. Aby uzyskać więcej informacji, przeczytaj [anons](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/).

### <a name="update-management-availability-in-south-central-us"></a>Update Management dostępności w południowo-środkowych usa

**Wpisz:** Nowa funkcja

Azure Automation regionów zaktualizowano w celu obsługi Update Management w regionie Południowo-środkowe usa. Zobacz [Obsługiwane mapowanie regionów,](how-to/region-mappings.md#supported-mappings) aby uzyskać informacje o aktualizacjach dokumentacji w celu odzwierciedlenia tej zmiany.

## <a name="september-2020"></a>Wrzesień 2020

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>Start/Stop VMs during off-hours Runbook zaktualizowano w celu używania modułów Azure Az

**Wpisz:** Nowa funkcja

Zaktualizowano uruchamianie/zatrzymywanie modułów Runbook maszyny wirtualnej w celu używania modułów Az Azure Resource Manager modułów. Zobacz [Start/Stop VMs during off-hours](automation-solution-vm-management.md) omówienie aktualizacji dokumentacji, aby odzwierciedlić te zmiany.

## <a name="august-2020"></a>Sierpień 2020 r.

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>Opublikowano rozszerzenie DSC w celu obsługi Azure Arc

**Wpisz:** Nowa funkcja

Użyj Azure Automation State Configuration, aby centralnie przechowywać konfiguracje i utrzymywać żądany stan hybrydowych połączonych maszyn włączonych za pośrednictwem rozszerzenia maszyny wirtualnej DSC z obsługą Azure Arc z obsługą serwerów. Aby uzyskać więcej informacji, przeczytaj Omówienie rozszerzeń maszyn wirtualnych [serwerów z obsługą usługi Arc.](../azure-arc/servers/manage-vm-extensions.md)

### <a name="july-2020"></a>Lipiec 2020 r.

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Wprowadzono publiczną zapoznawczą obsługę Private Link usługi Automation

**Wpisz:** Nowa funkcja

Użyj Azure Private Link, aby bezpiecznie połączyć sieci wirtualne z Azure Automation przy użyciu prywatnych punktów końcowych. Aby uzyskać więcej informacji, przeczytaj [anons](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/).

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Obsługa hybrydowego procesu roboczego runbook dla systemu Windows Server 2008 R2

**Wpisz:** Nowa funkcja

Hybrydowy proces roboczy runbook usługi Automation obsługuje system operacyjny Windows Server 2008 R2. Zobacz [Obsługiwane systemy operacyjne,](automation-windows-hrw-install.md#supported-windows-operating-system) aby uzyskać informacje o aktualizacjach dokumentacji w celu odzwierciedlenia tych zmian.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Update Management obsługę systemu Windows Server 2008 R2

**Wpisz:** Nowa funkcja

Update Management obsługuje ocenę i stosowanie poprawek systemu operacyjnego Windows Server 2008 R2. Zobacz [Obsługiwane systemy operacyjne,](update-management/overview.md#clients) aby uzyskać informacje o aktualizacjach dokumentacji, aby odzwierciedlić te zmiany.

### <a name="automation-diagnostic-logs-schema-update"></a>Aktualizacja schematu dzienników diagnostycznych usługi Automation

**Wpisz:** Nowa funkcja

Zmieniono schemat Azure Automation danych dziennika w usłudze Log Analytics. Aby dowiedzieć się więcej, zobacz Forward Azure Automation job data to Azure Monitor logs (Przekazywanie danych zadania [Azure Monitor do dzienników).](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object)

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Lighthouse obsługuje usługę Automation Update Management

**Wpisz:** Nowa funkcja

Azure Lighthouse umożliwia delegowane zarządzanie zasobami za Update Management dla dostawców usług i klientów. Przeczytaj więcej [tutaj](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/).

## <a name="june-2020"></a>Czerwiec 2020 r.

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Automatyzacja i Update Management dostępności w US Gov Arizona regionu

**Wpisz:** Nowa funkcja

Konta i Update Management usługi Automation są dostępne w US Gov Arizona. Aby uzyskać więcej informacji, zobacz [anons](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/).

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Zaktualizowano skrypt dołączania hybrydowego procesu roboczego runbook w celu używania modułów Az

**Wpisz:** Nowa funkcja

Zaktualizowano New-OnPremiseHybridWorker Runbook w celu obsługi modułów Az. Aby uzyskać więcej informacji, zobacz pakiet w [Galeria programu PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7).

### <a name="update-management-availability-in-china-east-2"></a>Update Management dostępności w Chiny Wschodnie 2

**Wpisz:** Nowa funkcja

Azure Automation regionów zaktualizowano w celu Update Management funkcji Chiny Wschodnie 2 regionu. Zobacz [Obsługiwane mapowanie regionów,](how-to/region-mappings.md#supported-mappings) aby uzyskać informacje o aktualizacjach dokumentacji w celu odzwierciedlenia tej zmiany.

## <a name="may-2020"></a>Maj 2020 r.

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>Zaktualizowano rekordy DNS usługi Automation z adresów URL specyficznych dla regionu do adresów URL specyficznych dla konta usługi Automation

**Wpisz:** Nowa funkcja

Azure Automation dns zostały zaktualizowane w celu obsługi linków prywatnych. Aby uzyskać więcej informacji, przeczytaj [anons](https://azure.microsoft.com/updates/azure-automation-updateddns-records/).

### <a name="added-capability-to-keep-automation-runbooks-and-dsc-scripts-encrypted-by-default"></a>Dodano możliwość domyślnego szyfrowania skryptów DSC i runbook usługi Automation

**Wpisz:** Nowa funkcja

Oprócz zwiększenia bezpieczeństwa zasobów, w celu zwiększenia bezpieczeństwa zasobów są również szyfrowane Azure Automation Runbook i skrypty DSC.

## <a name="april-2020"></a>Kwiecień 2020 r.

### <a name="retirement-of-the-automation-watcher-task"></a>Wycofanie zadania usługi Automation Watcher

**Wpisz:** Planowanie zmian

Azure Logic Apps jest teraz zalecanym i obsługiwanym sposobem monitorowania zdarzeń, planowania zadań cyklicznych i wyzwalania akcji. Nie będzie żadnych dalszych inwestycji w funkcje zadań watcher. Aby dowiedzieć się więcej, zobacz [Planowanie i uruchamianie cyklicznych zadań automatycznych za pomocą Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

## <a name="march-2020"></a>Marzec 2020 r.

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Obsługa izolacji obliczeniowej Impact Level 5 (IL5) w komercyjnej chmurze platformy Azure i chmurze dla instytucji rządowych

**Typu:**

Azure Automation hybrydowy proces roboczy runbook może być używany w Azure Government do obsługi obciążeń na poziomie Impact Level 5. Aby dowiedzieć się więcej, zobacz naszą [dokumentację](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5).

## <a name="february-2020"></a>Luty 2020 r.

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Wprowadzono obsługę tagów usługi dla sieci wirtualnej platformy Azure

**Wpisz:** Nowa funkcja

Obsługa automatyzacji tagów usługi zezwala lub nie zezwala na ruch w usłudze Automation dla podzbioru scenariuszy. Aby dowiedzieć się więcej, zobacz [dokumentację](automation-hybrid-runbook-worker.md#service-tags).

### <a name="enable-tls-12-support-for-azure-automation-service"></a>Włączanie obsługi TLS 1.2 dla Azure Automation usługi

**Wpisz:** Planowanie zmian

Azure Automation w pełni obsługuje TLS 1.2 i wszystkie wywołania klienta (za pośrednictwem webhook, węzłów DSC i hybrydowego procesu roboczego). Wersje TLS 1.1 i TLS 1.0 są nadal obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami ze starszymi klientami do momentu standaryzacji i pełnej migracji klientów do wersji TLS 1.2.

## <a name="january-2020"></a>Styczeń 2020 r.

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Wprowadzono publiczną podgląd kluczy zarządzanych przez klienta dla Azure Automation

**Wpisz:** Nowa funkcja

Klienci mogą zarządzać szyfrowaniem zasobów Azure Automation i zabezpieczać je przy użyciu własnych kluczy zarządzanych. Aby uzyskać więcej informacji, zobacz [Korzystanie z kluczy zarządzanych przez klienta.](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Wycofanie interfejsów API REST usługi Azure Service Management (ASM) dla Azure Automation

**Wpisz:** Wycofać

Interfejsy API REST usługi Azure Service Management (ASM) dla usługi Azure Automation zostaną wycofane i nie będą już obsługiwane po 30 stycznia 2020 r. Aby dowiedzieć się więcej, zobacz [anons](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/).

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz współtwórz dokumentację Azure Automation, zobacz [Przewodnik współautora dokumentacji.](/contribute/)
