---
title: Instalowanie agenta usługi Log Analytics na komputerach z systemem Linux
description: W tym artykule opisano sposób łączenia komputerów z systemem Linux hostowanych w innych chmurach lub lokalnych z usługą Azure Monitor pomocą agenta usługi Log Analytics dla systemu Linux.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 23597804a34a9bc409db179010569024aa472016
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725930"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Instalowanie agenta usługi Log Analytics na komputerach z systemem Linux
Ten artykuł zawiera szczegółowe informacje na temat instalowania agenta usługi Log Analytics na komputerach z systemem Linux przy użyciu następujących metod:

* [Zainstaluj agenta dla systemu Linux przy użyciu skryptu otoki hostowanej](#install-the-agent-using-wrapper-script) w usłudze GitHub. Jest to zalecana metoda instalowania i uaktualniania agenta, gdy komputer ma łączność z Internetem, bezpośrednio lub za pośrednictwem serwera proxy.
* [Ręcznie pobierz i zainstaluj](#install-the-agent-manually) agenta. Jest to wymagane, gdy komputer z systemem Linux nie ma dostępu do Internetu i będzie komunikować się z usługą Azure Monitor lub Azure Automation za pośrednictwem [bramy usługi Log Analytics.](./gateway.md) 

>[!IMPORTANT]
> Metody instalacji opisane w tym artykule są zwykle używane dla maszyn wirtualnych lokalnie lub w innych chmurach. Zobacz [Opcje instalacji,](./log-analytics-agent.md#installation-options) aby uzyskać bardziej wydajne opcje, których można użyć dla maszyn wirtualnych platformy Azure.



## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Aby [uzyskać listę dystrybucji systemu](agents-overview.md#supported-operating-systems) Linux obsługiwanych przez agenta usługi Log Analytics, zobacz Overview of Azure Monitor agents (Omówienie agentów usługi Log Analytics).

>[!NOTE]
>Program OpenSSL 1.1.0 jest obsługiwany tylko na platformach x86_x64 (wersja 64-bitowa), a program OpenSSL w wersji wcześniejszej niż 1.x nie jest obsługiwany na żadnej platformie.

>[!NOTE]
>Uruchamianie agenta systemu Linux usługi Log Analytics w kontenerach nie jest obsługiwane. Jeśli musisz monitorować kontenery, skorzystaj z rozwiązania [do](../containers/containers.md) monitorowania kontenerów dla hostów platformy Docker lub [usługi Container Insights](../containers/container-insights-overview.md) dla platformy Kubernetes.

Począwszy od wersji wydanych po sierpniu 2018 r., wprowadzamy następujące zmiany w naszym modelu pomocy technicznej:  

* Obsługiwane są tylko wersje serwera, a nie klient.  
* Skoncentruj się na dowolnej dystrybucji zatwierdzonej dla systemu [Linux na platformie Azure.](../../virtual-machines/linux/endorsed-distros.md) Należy pamiętać, że może wystąpić pewne opóźnienie między nową dystrybucją/wersją a wersją obsługiwaną przez platformę Azure dla systemu Linux i obsługiwaną przez agenta usługi Log Analytics dla systemu Linux.
* Wszystkie wersje pomocnicze są obsługiwane dla każdej z wymienionych wersji głównych.
* Wersje, które przeszły datę zakończenia wsparcia technicznego producenta, nie są obsługiwane.
* Obsługują tylko obrazy maszyn wirtualnych; Kontenery, nawet te pochodzące z oficjalnych obrazów wydawców dystrybucji, nie są obsługiwane.
* Nowe wersje AMI nie są obsługiwane.  
* Obsługiwane są tylko wersje z domyślnie uruchomionym programem OpenSSL 1.x.

>[!NOTE]
>Jeśli używasz dystrybucji lub wersji, która nie jest obecnie obsługiwana i nie jest dostosowana do naszego modelu pomocy technicznej, zalecamy użycie tego rozdęcie, potwierdzając, że pomoc techniczna firmy Microsoft nie będzie zapewniać pomocy dotyczącej wersji rozdrobnienego agenta.

### <a name="python-requirement"></a>Wymaganie dotyczące języka Python

Począwszy od wersji 1.13.27 agenta systemu Linux będzie obsługiwać zarówno język Python 2, jak i 3. Zawsze zalecamy używanie najnowszego agenta. 

Jeśli używasz starszej wersji agenta, maszyna wirtualna musi domyślnie używać języka Python 2. Jeśli maszyna wirtualna używa dystrybucji, która domyślnie nie zawiera języka Python 2, należy ją zainstalować. Następujące przykładowe polecenia zainstalują język Python 2 w różnych dystrybucji.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - Suse: `zypper install -y python2`

Plik wykonywalny python2 musi mieć alias python *.* Poniżej przedstawiono jedną z metod, których można użyć do ustawienia tego aliasu:

1. Uruchom następujące polecenie, aby usunąć wszystkie istniejące aliasy.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Uruchom następujące polecenie, aby utworzyć alias.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>Obsługiwane wzmacnianie zabezpieczeń systemu Linux
Agent OMS ma ograniczoną obsługę dostosowywania i wzmacniania zabezpieczeń dla systemu Linux.

Obecnie obsługiwane są następujące elementy: 
- Fips

Następujące funkcje nie są obsługiwane:
- Cis
- Selinux

Planuje się obsługę wzmacniania zabezpieczeń CIS i SELINUX dla [agenta monitorowania platformy Azure.](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) Dodatkowe metody wzmacniania zabezpieczeń i dostosowywania nie są obsługiwane ani planowane dla agenta OMS.  

## <a name="agent-prerequisites"></a>Wymagania wstępne agenta

W poniższej tabeli przedstawiono pakiety wymagane dla obsługiwanych dystrybucji [systemu Linux,](#supported-operating-systems) w których zostanie zainstalowany agent.

|Wymagany pakiet |Opis |Minimalna wersja |
|-----------------|------------|----------------|
|Glibc |    Biblioteka GNU C | 2.5-12 
|Openssl    | Biblioteki OpenSSL | 1.0.x lub 1.1.x |
|Narzędzie Curl | Klient internetowy programu cURL | 7.15.5 |
|Python | | 2.7 lub 3.6+
|Python-ctypes | | 
|PAM | Podłączane moduły uwierzytelniania (PAM) | | 

>[!NOTE]
>Do zbierania komunikatów syslog jest wymagany program rsyslog lub syslog-ng. Domyślny demon syslog w wersji 5 systemów Red Hat Enterprise Linux, CentOS i Oracle Linux (sysklog) nie jest obsługiwany w przypadku zbierania zdarzeń syslog. Aby zbierać dane syslog z tej wersji tych dystrybucji, należy zainstalować demona rsyslog i skonfigurować go w taki sposób, aby zastąpił go.

## <a name="network-requirements"></a>Wymagania dotyczące sieci
Zobacz [Omówienie agenta usługi Log Analytics,](./log-analytics-agent.md#network-requirements) aby uzyskać informacje o wymaganiach dotyczących sieci dla agenta systemu Linux.

## <a name="agent-install-package"></a>Pakiet instalacji agenta

Agent usługi Log Analytics dla systemu Linux składa się z wielu pakietów. Plik wydania zawiera następujące pakiety, które są dostępne po uruchomieniu pakietu powłoki z `--extract` parametrem :

**Pakiet** | **Wersja** | **Opis**
----------- | ----------- | --------------
omsagent | 1.13.9 | Agent usługi Log Analytics dla systemu Linux
omsconfig | 1.1.1 | Agent konfiguracji agenta usługi Log Analytics
Omi | 1.6.4 | Open Management Infrastructure (OMI) — lekki serwer CIM. *Należy pamiętać, że usługa OMI wymaga dostępu głównego do uruchomienia zadania cron niezbędnego do działania usługi*
Scx | 1.6.4 | Dostawcy OMI CIM dla metryk wydajności systemu operacyjnego
apache-cimprov | 1.0.1 | Dostawca monitorowania wydajności serwera HTTP Apache dla OMI. Instalowany tylko w przypadku wykrycia serwera HTTP Apache.
mysql-cimprov | 1.0.1 | Dostawca monitorowania wydajności serwera MySQL dla OMI. Instalowany tylko w przypadku wykrycia serwera MySQL/MariaDB.
docker-cimprov | 1.0.0 | Dostawca platformy Docker dla usługi OMI. Instalowany tylko w przypadku wykrycia platformy Docker.

### <a name="agent-installation-details"></a>Szczegóły instalacji agenta

Po zainstalowaniu agenta usługi Log Analytics dla pakietów systemu Linux są stosowane następujące dodatkowe zmiany konfiguracji dla całego systemu. Te artefakty są usuwane po odinstalowaniu pakietu omsagent.

* Zostanie utworzony użytkownik bez uprawnień o nazwie : `omsagent` . Demon jest uruchamiany pod tym poświadczeniem. 
* Plik dołączania  programu sudoers jest tworzony w pliku `/etc/sudoers.d/omsagent` . Pozwala to `omsagent` na ponowne uruchomienie demonów syslog i omsagent. Jeśli dyrektywy sudo *include* nie są obsługiwane w zainstalowanej wersji programu sudo, te wpisy zostaną zapisane w pliku `/etc/sudoers` .
* Konfiguracja usługi Syslog jest modyfikowana w celu przekazywania podzestawu zdarzeń do agenta. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zbierania danych usługi Syslog.](data-sources-syslog.md)

Na monitorowanym komputerze z systemem Linux agent jest wymieniony jako `omsagent` . `omsconfig` to agent usługi Log Analytics dla agenta konfiguracji systemu Linux, który szuka nowej konfiguracji po stronie portalu co 5 minut. Nowa i zaktualizowana konfiguracja jest stosowana do plików konfiguracji agenta znajdujących się w folderze `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="install-the-agent-using-wrapper-script"></a>Instalowanie agenta przy użyciu skryptu otoki

Poniższe kroki pozwalają skonfigurować konfigurację agenta usługi Log Analytics na platformie Azure i w chmurze usługi Azure Government przy użyciu skryptu otoki dla komputerów z systemem Linux, które mogą komunikować się bezpośrednio lub za pośrednictwem serwera proxy w celu pobrania agenta hostowanej w usłudze GitHub i zainstalowania agenta.  

Jeśli komputer z systemem Linux musi komunikować się z usługą Log Analytics za pośrednictwem serwera proxy, tę konfigurację można określić w wierszu polecenia, uwzględniając polecenie `-p [protocol://][user:password@]proxyhost[:port]` . Właściwość *protocol* akceptuje wartość lub , a właściwość proxyhost akceptuje w pełni kwalifikowaną nazwę domeny lub `http` adres IP serwera `https` proxy.  

Na przykład: `https://proxy01.contoso.com:30443`

Jeśli w obu przypadkach wymagane jest uwierzytelnianie, należy określić nazwę użytkownika i hasło. Na przykład: `https://user01:password@proxy01.contoso.com:30443`

1. Aby skonfigurować komputer z systemem Linux w celu nawiązania połączenia z obszarem roboczym usługi Log Analytics, uruchom następujące polecenie, podając identyfikator obszaru roboczego i klucz podstawowy. To polecenie pobiera agenta, weryfikuje jego sumę kontrolną i instaluje go.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Następujące polecenie zawiera parametr `-p` serwera proxy i przykładowej składni, gdy uwierzytelnianie jest wymagane przez serwer proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Aby skonfigurować komputer z systemem Linux do nawiązywania połączenia z obszarem roboczym usługi Log Analytics Azure Government w chmurze, uruchom następujące polecenie, podając skopiowany wcześniej identyfikator obszaru roboczego i klucz podstawowy. To polecenie pobiera agenta, weryfikuje jego sumę kontrolną i instaluje go. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Następujące polecenie zawiera parametr `-p` serwera proxy i przykładowej składni, gdy uwierzytelnianie jest wymagane przez serwer proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Uruchom ponownie agenta, uruchamiając następujące polecenie: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## <a name="install-the-agent-manually"></a>Ręczna instalacja agenta

Agent usługi Log Analytics dla systemu Linux jest dostarczany w samodzielnym wyodrębniającym i instalowalnym pakiecie skryptów powłoki. Ten pakiet zawiera pakiety Debian i RPM dla każdego ze składników agenta i może być instalowany bezpośrednio lub wyodrębniony w celu pobrania poszczególnych pakietów. Jeden pakiet jest dostarczany dla architektury x64 i jeden dla architektur x86. 

> [!NOTE]
> W przypadku maszyn wirtualnych platformy Azure zalecamy zainstalowanie na nich agenta przy użyciu rozszerzenia maszyny wirtualnej usługi [Azure Log Analytics dla](../../virtual-machines/extensions/oms-linux.md) systemu Linux. 

1. [Pobierz](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) i przenieś odpowiedni pakiet (x64 lub x86) na maszynę wirtualną z systemem Linux lub komputer fizyczny przy użyciu narzędzia scp/sftp.

2. Zainstaluj pakiet przy użyciu `--install` argumentu . Aby do dołączać do obszaru roboczego usługi Log Analytics podczas instalacji, podaj `-w <WorkspaceID>` `-s <workspaceKey>` skopiowane wcześniej parametry i .

    >[!NOTE]
    >Należy użyć argumentu , jeśli są zainstalowane jakiekolwiek pakiety zależne, takie jak `--upgrade` omi, scx, omsconfig lub ich starsze wersje, podobnie jak w przypadku, gdy agent programu system Center Operations Manager dla systemu Linux jest już zainstalowany. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Aby skonfigurować agenta systemu Linux do instalowania obszaru roboczego usługi Log Analytics i nawiązywania z tym obszarem za pośrednictwem bramy usługi Log Analytics, uruchom następujące polecenie, podając parametry serwera proxy, identyfikatora obszaru roboczego i klucza obszaru roboczego. Tę konfigurację można określić w wierszu polecenia, włączając polecenie `-p [protocol://][user:password@]proxyhost[:port]` . Właściwość *proxyhost* akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP serwera bramy usługi Log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Jeśli wymagane jest uwierzytelnianie, należy określić nazwę użytkownika i hasło. Na przykład: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Aby skonfigurować komputer z systemem Linux do nawiązywania połączenia z obszarem roboczym usługi Log Analytics w Azure Government cloud, uruchom następujące polecenie, podając skopiowany wcześniej identyfikator obszaru roboczego i klucz podstawowy.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Jeśli chcesz zainstalować pakiety agentów i skonfigurować je do późniejszego zgłaszania do określonego obszaru roboczego usługi Log Analytics, uruchom następujące polecenie:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Jeśli chcesz wyodrębnić pakiety agentów z pakietu bez instalowania agenta, uruchom następujące polecenie:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>Uaktualnianie z poprzedniej wersji

Uaktualnianie z poprzedniej wersji, począwszy od wersji 1.0.0–47, jest obsługiwane w każdej wersji. Wykonaj instalację za pomocą parametru , aby uaktualnić wszystkie `--upgrade` składniki agenta do najnowszej wersji.

## <a name="cache-information"></a>Informacje o pamięci podręcznej
Dane z agenta usługi Log Analytics dla systemu Linux są buforowane na maszynie lokalnej w lokalizacji *%STATE_DIR_WS%/out_oms_common*.buffer* przed ich wysłaniem do Azure Monitor. Niestandardowe dane dziennika są buforowane w *%STATE_DIR_WS%/out_oms_blob*.buffer*. Ścieżka może być inna w przypadku niektórych [rozwiązań i typów danych.](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=)

Agent próbuje przekazywać je co 20 sekund. Jeśli to się nie powiedzie, będzie oczekiwać wykładniczo wydłużać czas, aż zakończy się powodzeniem: 30 sekund przed drugą próbą, 60 sekund przed trzecią, 120 sekund... i tak dalej, maksymalnie 16 minut między ponownych prób, dopóki nie zostanie pomyślnie nawiąże połączenie ponownie. Agent ponowi próbę maksymalnie 6 razy dla danego fragmentu danych przed odrzuceniem i przejściem do następnego. Będzie to kontynuowane do momentu pomyślnego przekazania agenta. Oznacza to, że dane mogą być buforowane do około 30 minut przed odrzuceniem.

Domyślny rozmiar pamięci podręcznej to 10 MB, ale można go zmodyfikować w pliku [omsagent.conf.](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf)


## <a name="next-steps"></a>Następne kroki

- Zapoznaj [się z tematem Zarządzanie](agent-manage.md) agentem usługi Log Analytics dla systemów Windows i Linux oraz ich obsługa, aby dowiedzieć się, jak ponownie skonfigurować, uaktualnić lub usunąć agenta z maszyny wirtualnej.

- Zapoznaj [się z tematem Rozwiązywanie problemów z agentem systemu Linux,](agent-linux-troubleshoot.md) jeśli wystąpią problemy podczas jego instalowania lub zarządzania.
