---
title: Omówienie agenta Log Analytics
description: Ten temat pomaga zrozumieć, jak zbierać dane i monitorować komputery hostowane na platformie Azure, lokalnie lub w innym środowisku chmury przy użyciu Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 409a119804354b85e3af380d33a4801549ef8133
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325291"
---
# <a name="log-analytics-agent-overview"></a>Omówienie agenta Log Analytics
Agent usługi Azure Log Analytics został opracowany z myślą o rozbudowanym zarządzaniu między maszynami wirtualnymi w każdej chmurze, maszynach lokalnych i tych monitorowanych przez [System Center Operations Manager](/system-center/scom/). Agenci systemów Windows i Linux wysyłają zebrane dane z różnych źródeł do obszaru roboczego Log Analytics w Azure Monitor, a także do wszystkich unikatowych dzienników lub metryk zgodnie z definicją w rozwiązaniu monitorowania. Agent Log Analytics obsługuje także szczegółowe informacje i inne usługi w Azure Monitor, takie jak [Azure monitor dla maszyn wirtualnych](../insights/vminsights-enable-overview.md), [Azure Security Center](../../security-center/index.yml)i [Azure Automation](../../automation/automation-intro.md).

Ten artykuł zawiera szczegółowe omówienie wymagań dotyczących agenta, systemu i sieci oraz różnych metod wdrażania.

> [!NOTE]
> Może również zostać wyświetlony Agent Log Analytics nazywany Microsoft Monitoring Agent (MMA) lub agentem usługi OMS Linux.

> [!NOTE]
> Diagnostyka Azure rozszerzenie jest jednym z agentów dostępnych do zbierania danych monitorowania z systemu operacyjnego gościa zasobów obliczeniowych. Zapoznaj się z [omówieniem agentów Azure monitor](agents-overview.md) , aby uzyskać opis różnych agentów i wskazówki dotyczące wybierania odpowiednich agentów w celu spełnienia określonych wymagań.

## <a name="comparison-to-azure-diagnostics-extension"></a>Porównanie z rozszerzeniem usługi Diagnostyka Azure
[Rozszerzenia usługi Azure Diagnostics](diagnostics-extension-overview.md) w Azure monitor można także użyć do zbierania danych monitorowania z systemu operacyjnego gościa maszyn wirtualnych platformy Azure. W zależności od potrzeb można użyć lub obu tych opcji. Szczegółowe porównanie agentów Azure Monitor można znaleźć w temacie [Omówienie agentów Azure monitor](agents-overview.md) . 

Kluczowe różnice, które należy wziąć pod uwagę:

- Rozszerzenia Diagnostyka Azure można używać tylko z maszynami wirtualnymi platformy Azure. Agent Log Analytics może być używany z maszynami wirtualnymi platformy Azure, innymi chmurami i lokalnymi.
- Diagnostyka Azure rozszerzenie wysyła dane do usługi Azure Storage, [Azure monitor metryk](data-platform-metrics.md) (tylko system Windows) i Event Hubs. Agent Log Analytics zbiera dane do [dzienników Azure monitor](data-platform-logs.md).
- Agent Log Analytics jest wymagany w przypadku [rozwiązań](../monitor-reference.md#insights-and-core-solutions), [Azure monitor dla maszyn wirtualnych](../insights/vminsights-overview.md)i innych usług, takich jak [Azure Security Center](../../security-center/index.yml).

## <a name="costs"></a>Koszty
Nie ma kosztu dla agenta Log Analytics, ale opłaty za dane pozyskiwane mogą być naliczane. Aby uzyskać szczegółowe informacje na temat cen zebranych w obszarze roboczym Log Analytics, zobacz [temat Zarządzanie użyciem i kosztami za pomocą dzienników Azure monitor](manage-cost-storage.md) .

## <a name="data-collected"></a>Zbierane dane
Poniższa tabela zawiera listę typów danych, które można skonfigurować dla obszaru roboczego Log Analytics do zebrania ze wszystkich połączonych agentów. Zobacz, [co jest monitorowane przez Azure monitor?](../monitor-reference.md) , aby zapoznać się z listą szczegółowych informacji, rozwiązań i innych rozwiązań, które używają agenta log Analytics do zbierania innych rodzajów danych.

| Źródło danych | Opis |
| --- | --- |
| [Dzienniki zdarzeń systemu Windows](data-sources-windows-events.md) | Informacje wysyłane do systemu rejestrowania zdarzeń systemu Windows. |
| [Syslog](data-sources-syslog.md)                     | Informacje wysyłane do systemu rejestrowania zdarzeń w systemie Linux. |
| [Wydajność](data-sources-performance-counters.md)  | Wartości liczbowe mierzące wydajność różnych aspektów systemu operacyjnego i obciążeń. |
| [Dzienniki usług IIS](data-sources-iis-logs.md)                 | Informacje o użyciu witryn sieci Web usług IIS działających w systemie operacyjnym gościa. |
| [Niestandardowe dzienniki](data-sources-custom-logs.md)           | Zdarzenia z plików tekstowych na komputerach z systemem Windows i Linux. |

## <a name="data-destinations"></a>Miejsca docelowe danych
Agent Log Analytics wysyła dane do Log Analytics obszaru roboczego w Azure Monitor. Agent systemu Windows może być wieloadresowy w celu wysyłania danych do wielu obszarów roboczych i System Center Operations Manager grup zarządzania. Agent systemu Linux może wysyłać tylko do jednego miejsca docelowego.

## <a name="other-services"></a>Inne usługi
Agent dla systemu Linux i Windows nie tylko do łączenia się z Azure Monitor, obsługuje także Azure Automation do hostowania roli hybrydowego procesu roboczego elementu Runbook i innych usług, takich jak [Change Tracking](../../automation/change-tracking.md), [Update Management](../../automation/automation-update-management.md)i [Azure Security Center](../../security-center/security-center-intro.md). Aby uzyskać więcej informacji na temat roli hybrydowego procesu roboczego elementu Runbook, zobacz [Azure Automation hybrydowego procesu roboczego elementu Runbook](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Instalacja i konfiguracja

Korzystając z Log Analytics agentów do zbierania danych, należy zrozumieć następujące kwestie w celu zaplanowania wdrożenia agenta:

* Aby zbierać dane z agentów systemu Windows, można [skonfigurować każdego agenta w celu raportowania do co najmniej jednego obszaru roboczego](agent-windows.md), nawet gdy jest on raportowany do System Center Operations Manager grupy zarządzania. Agent systemu Windows może zgłosić do czterech obszarów roboczych.
* Agent systemu Linux nie obsługuje wiele multihostingu i może być raportowany tylko do jednego obszaru roboczego.
* Agent systemu Windows obsługuje [Standard FIPS 140](/windows/security/threat-protection/fips-140-validation), a agent Linux nie obsługuje go.  

Jeśli używasz System Center Operations Manager 2012 R2 lub nowszego:

* Każda Operations Manager grupy zarządzania może być [połączona tylko z jednym obszarem roboczym](om-agents.md).
* Komputery z systemem Linux, które są zgłaszane do grupy zarządzania, muszą być skonfigurowane do bezpośredniego raportowania do obszaru roboczego Log Analytics. Jeśli komputery z systemem Linux są już zgłaszane bezpośrednio do obszaru roboczego i chcesz monitorować je za pomocą Operations Manager, wykonaj następujące kroki, aby [zgłosić Operations Manager do grupy zarządzania](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Program Log Analytics Agent systemu Windows można zainstalować na komputerze z systemem Windows i mieć do niego raport Operations Manager zintegrowany z obszarem roboczym i innym obszarze roboczym.


Istnieje wiele metod instalacji agenta Log Analytics i podłączenia maszyny do Azure Monitor w zależności od wymagań. W poniższej tabeli przedstawiono każdą metodę, aby określić, która działa najlepiej w organizacji.

|Element źródłowy | Metoda | Opis|
|-------|-------------|-------------|
|Maszyna wirtualna platformy Azure| [Ręcznie z Azure Portal](../learn/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json) | Określ maszyny wirtualne do wdrożenia z obszaru roboczego Log Analytics. |
| | Log Analytics rozszerzenie maszyny wirtualnej dla [systemu Windows](../../virtual-machines/extensions/oms-windows.md) lub [Linux](../../virtual-machines/extensions/oms-linux.md) przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonu Azure Resource Manager | Rozszerzenie instaluje agenta Log Analytics na maszynach wirtualnych platformy Azure i rejestruje je w istniejącym Azure Monitor obszarze roboczym. |
| | [Usługa Azure Monitor dla maszyn wirtualnych](../insights/vminsights-enable-overview.md) | Włączenie monitorowania za pomocą Azure Monitor dla maszyn wirtualnych powoduje zainstalowanie rozszerzenia Log Analytics i agenta. |
| | [Azure Security Center automatycznej aprowizacji](../../security-center/security-center-enable-data-collection.md) | Azure Security Center może udostępnić agentowi Log Analytics na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i nowych, które są tworzone w przypadku włączenia ich do monitorowania luk w zabezpieczeniach i zagrożeń. W przypadku włączenia tej nowej lub istniejącej maszyny wirtualnej bez zainstalowanego agenta zostanie zainicjowana obsługa administracyjna. |
| Hybrydowy komputer z systemem Windows| [Instalacja ręczna](agent-windows.md) | Zainstaluj program Microsoft Monitoring Agent z poziomu wiersza polecenia. |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatyzowanie instalacji przy użyciu Azure Automation DSC. |
| | [Menedżer zasobów szablon z Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Jeśli wdrożono Microsoft Azure Stack w centrum danych, użyj szablonu Azure Resource Manager.| 
| Hybrydowy komputer z systemem Linux| [Instalacja ręczna](../learn/quick-collect-linux-computer.md)|Zainstaluj agenta dla systemu Linux wywołującego skrypt otoki hostowany w witrynie GitHub. | 
| System Center Operations Manager|[Integracja Operations Manager z usługą Log Analytics](./om-agents.md) | Skonfiguruj integrację między dziennikami Operations Manager i Azure Monitor, aby przekazywać zebrane dane z komputerów z systemem Windows do grupy zarządzania.|  


## <a name="supported-windows-operating-systems"></a>Obsługiwane systemy operacyjne Windows

Następujące wersje systemu operacyjnego Windows są oficjalnie obsługiwane dla agenta systemu Windows:

* Windows Server 2019
* Windows Server 2016, wersja 1709 i 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 z dodatkiem SP2 (x64), 2008 R2
* Windows 10 Enterprise (w tym wiele sesji) i Pro
* Windows 8 Enterprise i Pro 
* Windows 7 z dodatkiem SP1

>[!NOTE]
>Agent Log Analytics dla systemu Windows został zaprojektowany z myślą o obsłudze scenariuszy monitorowania serwera, dlatego można uruchomić klienta systemu Windows w celu obsługi obciążeń skonfigurowanych i zoptymalizowanych pod kątem systemu operacyjnego serwera. Agent obsługuje klienta systemu Windows, jednak nasze rozwiązania do monitorowania nie koncentrują się na scenariuszach monitorowania klientów, chyba że określono inaczej.

## <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Ta sekcja zawiera szczegółowe informacje o obsługiwanych dystrybucjach systemu Linux.

Począwszy od wersji wydanej po 2018 sierpnia, wprowadzamy następujące zmiany w naszym modelu pomocy technicznej:  

* Obsługiwane są tylko wersje serwera, a nie klient.  
* Zanotuj pomoc techniczną na dowolnym z [potwierdzonych dystrybucje systemu Azure](../../virtual-machines/linux/endorsed-distros.md)w systemie Linux. Należy pamiętać, że może wystąpić pewne opóźnienie między nowym dystrybucji/wersją zatwierdzona w systemie Azure Linux i jest ona obsługiwana dla agenta Log Analytics Linux.
* Wszystkie wersje pomocnicze są obsługiwane dla każdej wymienionej wersji głównej.
* Wersje, które przekazały datę zakończenia obsługi przez producenta, nie są obsługiwane.  
* Nowe wersje AMI nie są obsługiwane.  
* Obsługiwane są tylko wersje, które domyślnie korzystają z protokołu SSL 1. x.

>[!NOTE]
>Jeśli używasz dystrybucji lub wersji, która nie jest obecnie obsługiwana i nie jest zgodna z naszym modelem pomocy technicznej, zalecamy przerozwidlenie tego repozytorium, co oznacza, że pomoc techniczna firmy Microsoft nie będzie świadczyć pomocy z wersjami agentów z rozwidleniami.


### <a name="python-2-requirement"></a>Wymagania dotyczące języka Python 2
 Agent Log Analytics wymaga języka Python 2. Jeśli maszyna wirtualna używa dystrybucji, która domyślnie nie zawiera języka Python 2, należy ją zainstalować. Następujące przykładowe polecenia zainstalują środowisko Python 2 na różnych dystrybucje.

 - Red Hat, CentOS, Oracle:`yum install -y python2`
 - Ubuntu, Debian:`apt-get install -y python2`
 - SZŁO`zypper install -y python2`

Plik wykonywalny python2 musi mieć alias "Python" przy użyciu następującego polecenia:

```
alternatives --set python `which python2`
```

### <a name="supported-distros"></a>Obsługiwane dystrybucje

Następujące wersje systemu operacyjnego Linux są oficjalnie obsługiwane dla agentów z systemem Linux:

* Amazon Linux 2017,09 (x64)
* CentOS Linux 6 (x64) i 7 (x64)  
* Oracle Linux 6 i 7 (x64) 
* Red Hat Enterprise Linux Server 6 (x64), 7 (x64) i 8 (x64)
* Debian GNU/Linux 8 i 9 (x64)
* Ubuntu 14,04 LTS (x86/x64), 16,04 LTS (x64) i 18,04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) i 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 jest obsługiwana tylko na platformach x86_x64 (64-bitowe) i OpenSSL wcześniejszych niż 1. x nie jest obsługiwana na żadnej platformie.
>

### <a name="agent-prerequisites"></a>Wymagania wstępne agenta

W poniższej tabeli przedstawiono pakiety wymagane dla obsługiwanych dystrybucje systemu Linux, na których zostanie zainstalowany agent.

|Wymagany pakiet |Opis |Minimalna wersja |
|-----------------|------------|----------------|
|Glibc |    Biblioteka GNU C | 2.5-12 
|Openssl    | Biblioteki OpenSSL | 1.0. x lub 1.1. x |
|Narzędzie Curl | zazwinięcie klienta sieci Web | 7.15.5 |
|Python | | 2.6 + lub 3.3 +
|Python — ctypes | | 
|PAM | Podłączane moduły uwierzytelniania (PAM) | | 

>[!NOTE]
>Do zbierania komunikatów dziennika systemowego wymagane są rsyslog lub Dziennik systemu. Domyślny demon dziennika systemowego w wersji 5 Red Hat Enterprise Linux, CentOS i Oracle Linux wersja (sysklog) nie jest obsługiwany w przypadku zbierania zdarzeń dziennika systemowego. Aby zebrać dane dziennika systemu z tej wersji dystrybucji, demona rsyslog powinna zostać zainstalowana i skonfigurowana do zastępowania sysklog.

## <a name="tls-12-protocol"></a>Protokół TLS 1,2

Aby zapewnić bezpieczeństwo danych przesyłanych do dzienników Azure Monitor, zdecydowanie zachęcamy do skonfigurowania agenta do korzystania z co najmniej Transport Layer Security (TLS) 1,2. Starsze wersje protokołu TLS/SSL (SSL) są zagrożone i chociaż nadal działają tak, aby umożliwić zgodność z poprzednimi wersjami, nie są **zalecane**.  Aby uzyskać dodatkowe informacje, zapoznaj się z [bezpiecznym przesyłaniem danych przy użyciu protokołu TLS 1,2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Wymagania dotyczące obsługi podpisywania kodu SHA-2 w przypadku systemu Windows
Agent systemu Windows rozpocznie korzystanie wyłącznie z podpisywania SHA-2 w dniu 17 sierpnia 2020. Ta zmiana wpłynie na klientów korzystających z agenta Log Analytics w ramach starszej wersji systemu operacyjnego w ramach dowolnej usługi platformy Azure (Azure Monitor, Azure Automation, Update Management platformy Azure, Change Tracking platformy Azure, Azure Security Center, platformy Azure, usługi Windows Defender ATP). Zmiana nie wymaga żadnej akcji klienta, chyba że Agent jest uruchomiony w starszej wersji systemu operacyjnego (Windows 7, Windows Server 2008 R2 i Windows Server 2008). Klienci korzystający ze starszej wersji systemu operacyjnego są zobowiązani do podjęcia następujących działań na swoich maszynach przed 17 sierpnia 2020 lub ich agenci przestają wysyłać dane do swoich Log Analyticsych obszarów roboczych:

1. Zainstaluj najnowszy dodatek Service Pack dla systemu operacyjnego. Wymagane wersje dodatku Service Pack to:
    - Windows 7 z dodatkiem SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Zainstaluj w systemie operacyjnym Windows podpis SHA-2, zgodnie z opisem w artykule [2019 wymagania obsługi podpisywania kodu SHA-2 dla systemu Windows i programu WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Zaktualizuj do najnowszej wersji agenta systemu Windows (wersja 10.20.18029).
4. Zalecane, aby skonfigurować agenta do [korzystania z protokołu TLS 1,2](agent-windows.md#configure-agent-to-use-tls-12). 


## <a name="network-requirements"></a>Wymagania dotyczące sieci
Agent dla systemów Linux i Windows komunikuje się z usługą Azure Monitor za pośrednictwem portu TCP 443, a jeśli komputer nawiązuje połączenie za pośrednictwem zapory lub serwera proxy w celu komunikacji za pośrednictwem Internetu, przejrzyj poniższe wymagania, aby poznać wymaganą konfigurację sieci. Jeśli zasady zabezpieczeń IT nie zezwalają komputerom w sieci na łączenie się z Internetem, można skonfigurować [bramę log Analytics](gateway.md) , a następnie skonfigurować agenta do nawiązywania połączeń za pomocą bramy do Azure monitor dzienników. Agent może następnie odbierać informacje o konfiguracji i wysyłać zebrane dane w zależności od tego, jakie reguły zbierania danych i rozwiązania monitorowania, które zostały włączone w obszarze roboczym.

![Diagram komunikacji agenta Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

W poniższej tabeli wymieniono informacje o konfiguracji serwera proxy i zapory, które są wymagane dla agentów systemu Linux i Windows w celu komunikowania się z dziennikami Azure Monitor.

### <a name="firewall-requirements"></a>Wymagania dotyczące zapory

|Zasób agenta|Porty |Kierunek |Obejście inspekcji HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |port 443 |Wychodzący|Tak |  
|*.oms.opinsights.azure.com |port 443 |Wychodzący|Tak |  
|*.blob.core.windows.net |port 443 |Wychodzący|Tak |
|*.azure-automation.net |port 443 |Wychodzący|Tak |

Informacje dotyczące zapory wymagane do Azure Government można znaleźć w temacie [Azure Government Management](../../azure-government/compare-azure-government-global-azure.md#azure-monitor-logs). 

Jeśli planujesz używać Azure Automation hybrydowego procesu roboczego elementu Runbook do nawiązywania połączenia z usługą Automation i zarejestrowania się z nią w celu używania elementów Runbook lub rozwiązań do zarządzania w danym środowisku, musi on mieć dostęp do numeru portu i adresów URL opisanych w temacie [Konfigurowanie sieci dla hybrydowego procesu roboczego elementu Runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

### <a name="proxy-configuration"></a>Konfiguracja serwera proxy

Agent systemów Windows i Linux obsługuje komunikację za pośrednictwem serwera proxy lub bramy Log Analytics, aby Azure Monitor przy użyciu protokołu HTTPS.  Obsługiwane są zarówno uwierzytelnianie anonimowe, jak i podstawowe (nazwa użytkownika i hasło).  W przypadku agenta systemu Windows połączonego bezpośrednio z usługą konfiguracja serwera proxy jest określana podczas instalacji lub [po wdrożeniu](agent-manage.md#update-proxy-settings) z panelu sterowania lub za pomocą programu PowerShell.  

W przypadku agenta systemu Linux serwer proxy jest określony podczas instalacji lub [po instalacji](agent-manage.md#update-proxy-settings) , modyfikując plik konfiguracji proxy. conf.  Wartość konfiguracji serwera proxy agenta systemu Linux ma następującą składnię:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Jeśli serwer proxy nie wymaga uwierzytelniania, Agent systemu Linux nadal wymaga podania pseudo użytkownika/hasła. Może to być dowolna nazwa użytkownika lub hasło.

|Właściwość| Opis |
|--------|-------------|
|Protokół | https |
|użytkownik | Opcjonalna nazwa użytkownika dla uwierzytelniania serwera proxy |
|hasło | Opcjonalne hasło do uwierzytelniania serwera proxy |
|proxyhost | Adres lub nazwa FQDN serwera proxy/bramy Log Analytics |
|port | Opcjonalny numer portu dla bramy serwera proxy/Log Analytics |

Na przykład: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Jeśli używasz znaków specjalnych, takich jak " \@ " w haśle, pojawi się komunikat o błędzie połączenia z serwerem proxy, ponieważ wartość jest analizowana nieprawidłowo.  Aby obejść ten problem, zakoduj hasło w adresie URL przy użyciu narzędzia, takiego jak [UrlDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Następne kroki

* Przejrzyj [źródła danych](agent-data-sources.md) , aby zrozumieć źródła danych dostępne do zbierania danych z systemu Windows lub Linux. 
* Informacje na temat [zapytań dzienników](../log-query/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań. 
* Dowiedz się więcej o [rozwiązaniach do monitorowania](../insights/solutions.md) , które dodają funkcje do Azure monitor, a także Zbieraj dane w obszarze roboczym log Analytics.

