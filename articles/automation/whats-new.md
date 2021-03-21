---
title: Co nowego w Azure Automation
description: Ważne aktualizacje Azure Automation aktualizowane co miesiąc.
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: 899249c98c3ce0fdf061b1e689182f71c120aa13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729402"
---
# <a name="whats-new-in-azure-automation"></a>Co nowego w Azure Automation?

Azure Automation w bieżąco otrzymuje ulepszenia. Aby zachować aktualność w zakresie najnowszych zmian, ten artykuł zawiera informacje na temat:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów

Ta strona jest aktualizowana co miesiąc, dlatego należy ją regularnie odwiedzać.

## <a name="february-2021"></a>Luty 2021 r.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Obsługa konfiguracji automatyzacji i stanu zadeklarowana w Japonii zachodniej

**Typ:** Nowa funkcja

Dostępność konfiguracji konta i stanu usługi Automation w regionie Japonia Zachodnia. Aby uzyskać więcej informacji, przeczytaj temat [ogłoszenie](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/).

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Wprowadzono niestandardowe zgodność Azure Policy, aby wymusić wykonanie elementu Runbook w hybrydowym procesie roboczym

**Typ:** Nowa funkcja

Możesz użyć nowej reguły zgodności Azure Policy, aby umożliwić tworzenie zadań, elementów webhook i harmonogramów zadań do uruchomienia tylko w grupach hybrydowych procesów roboczych.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Update Management dostępność w regionach Wschodnie stany USA, Francja Środkowa i Europa Północna

**Typ:** Nowa funkcja

Funkcja Automatyzacja Update Management jest dostępna w regionach Wschodnie stany USA, Francja Środkowa i Europa Północna. Zobacz sekcję [obsługiwane mapowanie regionów](how-to/region-mappings.md) w celu uzyskania aktualizacji dokumentacji odzwierciedlającej tę zmianę.

## <a name="january-2021"></a>Styczeń 2021 r.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>Obsługa konfiguracji automatyzacji i stanu zadeklarowana w Szwajcaria Zachodnia

**Typ:** Nowa funkcja

Dostępność konfiguracji konta i stanu usługi Automation w regionie Szwajcaria Zachodnia. Aby uzyskać więcej informacji, zapoznaj się z [ogłoszeniem](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/).

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>Dodano skrypt języka Python 3 służący do importowania modułu z wieloma zależnościami

**Typ:** Nowa funkcja

Skrypt jest dostępny do pobrania z naszego [repozytorium GitHub](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py). 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Obsługa roli hybrydowego procesu roboczego elementu Runbook dla CentOS 8. x/RHEL 8. x/SLES 15

**Wprowadź.** Nowa funkcja

Funkcja hybrydowego procesu roboczego elementu Runbook obsługuje dystrybucje CentOS 8. x, REHL 8. x i SLES 15 wyłącznie w przypadku automatyzacji procesów na hybrydowych procesach roboczych elementów Runbook.  Zapoznaj się z [obsługiwanymi systemami operacyjnymi](automation-linux-hrw-install.md#supported-linux-operating-systems) dotyczącymi aktualizacji dokumentacji, aby odzwierciedlić te zmiany.

### <a name="update-management--change-tracking-availability-in-australia-east-east-asia-west-us--central-us-regions"></a>Update Management & Change Tracking dostępność w Australii Wschodniej, Azja Wschodnia zachodnie stany USA & środkowe regiony USA

**Typ:** Nowa funkcja

Konto usługi Automation, Change Tracking i spis i Update Management są dostępne w regionach Australia Wschodnia, Azja Wschodnia zachodnie stany USA & środkowe stany USA. 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>Wprowadzono publiczną wersję zapoznawczą elementów Runbook języka Python 3 w chmurze dla instytucji rządowych USA

**Typ:** Nowa funkcja Azure Automation wprowadza publiczną obsługę podglądu w chmurze języka Python 3 i wykonywanie hybrydowego elementu Runbook w regionach w chmurze dla instytucji rządowych USA.  Aby uzyskać więcej informacji, zobacz [ogłoszenie](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/).

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Azure Automation elementów Runbook przenoszonych z centrum skryptów TechNet do usługi GitHub

**Typ:** Planowanie zmiany

Centrum skryptów TechNet jest wycofywane i wszystkie elementy Runbook hostowane w galerii elementów Runbook zostały przeniesione do naszej [organizacji usługi GitHub](https://github.com/azureautomation). Aby uzyskać więcej informacji, Przeczytaj [Azure Automation elementów Runbook przenoszonych do usługi GitHub](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="december-2020"></a>Grudzień 2020 r.

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure Automation i Update Management prywatnego łącza

**Typ:** Nowa funkcja

Azure Automation i Update Management obsługi ogłoszone jako GA dla chmur globalnych i instytucji rządowych platformy Azure. Azure Automation włączyć obsługę linków prywatnych, aby zabezpieczyć wykonywanie elementu Runbook w roli hybrydowego procesu roboczego, przy użyciu Update Management do poprawiania maszyn, wywoływania elementu Runbook za pośrednictwem elementu webhook i korzystania z usługi konfiguracji stanu, aby zachować skargę maszyn. Aby uzyskać więcej informacji, Przeczytaj [Azure Automation pomocy technicznej dotyczącej linków prywatnych](https://azure.microsoft.com/updates/azure-automation-private-link)

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Azure Automation sklasyfikowane jako "certyfikat klasy C" w zakresie dostępności

**Typ:** Nowa funkcja

Funkcje ułatwień dostępu produktów firmy Microsoft pomagają agencjom w zakresie ogólnych wymagań dotyczących ułatwień dostępu. Na stronie [anons blogu](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) Wyszukaj **Azure Automation** , aby odczytać raport zgodności z dostępnością dla usługi Automation.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>Obsługa automatyzacji i konfiguracji stanu GA w regionie Zjednoczone Emiraty Arabskie

**Typ:** Nowa funkcja

Dostępność konfiguracji konta i stanu usługi Automation w regionie północno-Zjednoczone Emiraty Arabskie. Aby uzyskać więcej informacji, przeczytaj temat [ogłoszenie](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Obsługa konfiguracji automatyzacji i stanu w Niemcy Środkowo-Zachodnie

**Typ:** Nowa funkcja

Dostępność konfiguracji konta i stanu usługi Automation w regionie Niemcy Zachodnie. Aby uzyskać więcej informacji, przeczytaj temat [ogłoszenie](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/).

### <a name="dsc-support-for-oracle-6-and-7"></a>Obsługa DSC dla systemów Oracle 6 i 7

**Typ:** Nowa funkcja

Zarządzaj Oracle Linux 6 i 7 maszyn z konfiguracją stanu automatyzacji. Aby odzwierciedlić te zmiany, zobacz temat [obsługiwane dystrybucje systemu Linux](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) dla aktualizacji dokumentacji.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Publiczna wersja zapoznawcza elementów Runbook python3 w usłudze Automation

**Typ:** Nowa funkcja

Azure Automation teraz obsługuje chmurę języka Python 3 & wykonywanie hybrydowego elementu Runbook w publicznej wersji zapoznawczej we wszystkich regionach w chmurze globalnej platformy Azure. Zobacz [anons] ( https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) Aby uzyskać więcej informacji.

## <a name="november-2020"></a>Listopad 2020 r.

### <a name="dsc-support-for-ubuntu-1804"></a>Obsługa DSC dla Ubuntu 18,04

**Typ:** Nowa funkcja

Zobacz temat [obsługiwane dystrybucje systemu Linux](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) dla aktualizacji dokumentacji odzwierciedlających te zmiany.

## <a name="october-2020"></a>Październik 2020 r.

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>Obsługa konfiguracji automatyzacji i stanu w Szwajcaria Północna

**Typ:** Nowa funkcja

Dostępność konfiguracji konta i stanu usługi Automation w Szwajcaria Północna. Aby uzyskać więcej informacji, przeczytaj temat [ogłoszenie](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Obsługa automatyzacji i konfiguracji stanu GA w regionie Brazylia Południowo-Wschodnia

**Typ:** Nowa funkcja

Dostępność konfiguracji konta i stanu usługi Automation w Brazylii Południowo-Wschodnia. Aby uzyskać więcej informacji, przeczytaj temat [ogłoszenie](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/).

### <a name="update-management-availability-in-south-central-us"></a>Dostępność Update Management w Południowo-środkowe stany USA

**Typ:** Nowa funkcja

Azure Automation mapowanie regionu zostało zaktualizowane w celu obsługi funkcji Update Management w regionie Południowo-środkowe stany USA. Aby odzwierciedlić tę zmianę, zobacz [obsługiwane mapowanie regionów](how-to/region-mappings.md#supported-mappings) dla aktualizacji dokumentacji.

## <a name="september-2020"></a>Wrzesień 2020

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>Start/Stop VMs during off-hours elementów Runbook zaktualizowanych do korzystania z platformy Azure AZ modules

**Typ:** Nowa funkcja

Uruchamianie/zatrzymywanie elementów Runbook maszyny wirtualnej zostało zaktualizowane w celu używania AZ module zamiast modułów Azure Resource Manager. Aby odzwierciedlić te zmiany, zobacz [Start/Stop VMS during off-hours](automation-solution-vm-management.md) Omówienie aktualizacji dokumentacji.

## <a name="august-2020"></a>Sierpień 2020 r.

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>Opublikowano rozszerzenie DSC w celu obsługi usługi Azure Arc

**Typ:** Nowa funkcja

Użyj konfiguracji stanu Azure Automation, aby centralnie przechowywać konfiguracje i zachować żądany stan maszyn podłączonych hybrydowo z włączonym rozszerzeniem maszyny wirtualnej DSC usługi Azure Arc. Aby uzyskać więcej informacji, zobacz [Omówienie rozszerzeń maszyn wirtualnych z obsługą Arc](../azure-arc/servers/manage-vm-extensions.md).

### <a name="july-2020"></a>Lipiec 2020 r.

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Wprowadzono publiczną wersję zapoznawczą obsługi linków prywatnych w usłudze Automation

**Typ:** Nowa funkcja

Użyj prywatnego linku platformy Azure, aby bezpiecznie połączyć sieci wirtualne w celu Azure Automation przy użyciu prywatnych punktów końcowych. Aby uzyskać więcej informacji, zapoznaj się z [ogłoszeniem](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/).

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Obsługa hybrydowego procesu roboczego elementu Runbook dla systemu Windows Server 2008 R2

**Typ:** Nowa funkcja

Hybrydowy proces roboczy elementu Runbook usługi Automation obsługuje system operacyjny Windows Server 2008 R2. Zapoznaj się z [obsługiwanymi systemami operacyjnymi](automation-windows-hrw-install.md#supported-windows-operating-system) dotyczącymi aktualizacji dokumentacji, aby odzwierciedlić te zmiany.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Obsługa Update Management dla systemu Windows Server 2008 R2

**Typ:** Nowa funkcja

Update Management obsługuje ocenianie i stosowanie poprawek systemu operacyjnego Windows Server 2008 R2. Zapoznaj się z [obsługiwanymi systemami operacyjnymi](update-management/overview.md#clients) dotyczącymi aktualizacji dokumentacji, aby odzwierciedlić te zmiany.

### <a name="automation-diagnostic-logs-schema-update"></a>Aktualizacja schematu dzienników diagnostycznych automatyzacji

**Typ:** Nowa funkcja

Zmieniono schemat Azure Automation danych dziennika w usłudze Log Analytics. Aby dowiedzieć się więcej, zobacz [przekazywanie Azure Automation danych zadania do dzienników Azure monitor](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object).

### <a name="azure-lighthouse-supports-automation-update-management"></a>Usługa Azure Lighthouse obsługuje automatyzację Update Management

**Typ:** Nowa funkcja

Usługa Azure Lighthouse umożliwia zarządzanie zasobami delegowanymi za pomocą Update Management dla dostawców usług i klientów. Przeczytaj więcej [tutaj](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/).

## <a name="june-2020"></a>Czerwiec 2020 r.

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Automatyzacja i Update Management dostępność w regionie US Gov Arizona

**Typ:** Nowa funkcja

Konto usługi Automation i Update Management są dostępne w programie US Gov Arizona. Aby uzyskać więcej informacji, zobacz temat [ogłoszenie](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/).

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Skrypt dołączania hybrydowego procesu roboczego elementu Runbook został zaktualizowany do korzystania z AZ modules

**Typ:** Nowa funkcja

New-OnPremiseHybridWorker elementu Runbook został zaktualizowany do obsługi AZ modules. Aby uzyskać więcej informacji, zobacz pakiet w [Galeria programu PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7).

### <a name="update-management-availability-in-china-east-2"></a>Dostępność Update Management w Chiny Wschodnie 2

**Typ:** Nowa funkcja

Azure Automation mapowanie regionu zostało zaktualizowane w celu obsługi funkcji Update Management w regionie Chiny Wschodnie 2. Aby odzwierciedlić tę zmianę, zobacz [obsługiwane mapowanie regionów](how-to/region-mappings.md#supported-mappings) dla aktualizacji dokumentacji.

## <a name="may-2020"></a>Maj 2020 r.

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>Zaktualizowano rekordy DNS usługi Automation z określonych regionów względem adresów URL specyficznych dla konta usługi Automation

**Typ:** Nowa funkcja

Azure Automation rekordy DNS zostały zaktualizowane w celu obsługi linków prywatnych. Aby uzyskać więcej informacji, zapoznaj się z [ogłoszeniem](https://azure.microsoft.com/updates/azure-automation-updateddns-records/).

### <a name="added-capability-to-keep-automation-runbooks--dsc-scripts-encrypted-by-default"></a>Dodano możliwość zachowania elementów Runbook usługi Automation & skryptów DSC szyfrowanych domyślnie

**Typ:** Nowa funkcja

Oprócz poprawy bezpieczeństwa zasobów elementy Runbook & skrypty DSC są również szyfrowane w celu zwiększenia Azure Automation zabezpieczeń.

## <a name="april-2020"></a>Kwiecień 2020 r.

### <a name="retirement-of-the-automation-watcher-task"></a>Wycofanie zadania obserwatora usługi Automation

**Typ:** Planowanie zmiany

Azure Logic Apps jest teraz zalecanym i obsługiwanym sposobem monitorowania zdarzeń, planowania cyklicznych zadań i wyzwalania akcji. Nie będzie żadnych dalszych inwestycji w funkcje zadania obserwatora. Aby dowiedzieć się więcej, zobacz [Planowanie i uruchamianie cyklicznych zadań zautomatyzowanych z Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

## <a name="march-2020"></a>Marzec 2020 r.

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Obsługa izolacji obliczeniowej na poziomie 5 (IL5) w chmurze komercyjnej i centralnej platformy Azure

**Wprowadź**

Azure Automation hybrydowego procesu roboczego elementu Runbook można użyć w Azure Government do obsługi obciążeń poziomu 5. Aby dowiedzieć się więcej, zapoznaj się z naszą [dokumentacją](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5).

## <a name="february-2020"></a>Luty 2020 r.

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Wprowadzono obsługę tagów usługi sieci wirtualnej platformy Azure

**Typ:** Nowa funkcja

Obsługa automatyzacji tagów usługi zezwala na ruch dla usługi Automation lub go odmawiać dla podzestawu scenariuszy. Aby dowiedzieć się więcej, zapoznaj się z [dokumentacją](automation-hybrid-runbook-worker.md#service-tags).

### <a name="enable-tls-12-support-for-azure-automation-service"></a>Włącz obsługę protokołu TLS 1,2 dla usługi Azure Automation

**Typ:** Planowanie zmiany

Azure Automation w pełni obsługuje protokół TLS 1,2 i wszystkie wywołania klienta (za poorednictwem elementów webhook, węzłów DSC i hybrydowego procesu roboczego). Protokoły TLS 1,1 i TLS 1,0 są nadal obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami ze starszymi klientami, dopóki klienci nie będą w pełni migrować do protokołu TLS 1,2.

## <a name="january-2020"></a>Styczeń 2020 r.

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Wprowadzono publiczną wersję zapoznawczą kluczy zarządzanych przez klienta dla Azure Automation

**Typ:** Nowa funkcja

Klienci mogą zarządzać szyfrowaniem zasobów Azure Automation i zabezpieczać je przy użyciu własnych kluczy zarządzanych. Aby uzyskać więcej informacji, zobacz [Korzystanie z kluczy zarządzanych przez klienta](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account).

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Wycofanie interfejsów API REST usługi Azure Service Management (ASM) dla Azure Automation

**Typ:** Zwolnić

Interfejsy API REST usługi Azure Service Management (ASM) dla Azure Automation zostaną wycofane i nie będą już obsługiwane po 30 stycznia 2020. Aby dowiedzieć się więcej, zobacz [ogłoszenie](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/).

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz współtworzyć dokumentację Azure Automation, zobacz [Przewodnik współautora dokumentów](/contribute/).
