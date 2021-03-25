---
title: Instalowanie agenta usługi Log Analytics na komputerach z systemem Linux
description: W tym artykule opisano sposób łączenia komputerów z systemem Linux hostowanych w innych chmurach lub lokalnych w celu Azure Monitor z agentem Log Analytics dla systemu Linux.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 5171cefdb82b958ae8148ff63f1daef5f67916c6
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044961"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Instalowanie agenta usługi Log Analytics na komputerach z systemem Linux
Ten artykuł zawiera szczegółowe informacje dotyczące instalowania agenta Log Analytics na komputerach z systemem Linux przy użyciu następujących metod:

* [Zainstaluj agenta dla systemu Linux, używając skryptu otoki](#install-the-agent-using-wrapper-script) hostowanego w witrynie GitHub. Jest to zalecana metoda instalacji i uaktualnienia agenta, gdy komputer ma łączność z Internetem bezpośrednio lub za pośrednictwem serwera proxy.
* [Ręcznie Pobierz i zainstaluj](#install-the-agent-manually) agenta. Jest to wymagane, gdy komputer z systemem Linux nie ma dostępu do Internetu i będzie komunikować się z Azure Monitor lub Azure Automation za pomocą [bramy log Analytics](./gateway.md). 

>[!IMPORTANT]
> Metody instalacji opisane w tym artykule są zwykle używane w przypadku maszyn wirtualnych lokalnie lub w innych chmurach. Zobacz [Opcje instalacji](./log-analytics-agent.md#installation-options) , aby uzyskać więcej wydajnych opcji, których można użyć w przypadku maszyn wirtualnych platformy Azure.



## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Listę dystrybucji systemu Linux obsługiwanych przez agenta Log Analytics można znaleźć w temacie [Omówienie agentów Azure monitor](agents-overview.md#supported-operating-systems) .

>[!NOTE]
>OpenSSL 1.1.0 jest obsługiwana tylko na platformach x86_x64 (64-bitowe) i OpenSSL wcześniejszych niż 1. x nie jest obsługiwana na żadnej platformie.

>[!NOTE]
>Uruchamianie agenta Log Analytics Linux w kontenerach nie jest obsługiwane. Jeśli potrzebujesz monitorować kontenery, Skorzystaj z rozwiązania do [monitorowania kontenerów](../containers/containers.md) dla hostów platformy Docker lub szczegółowych informacji o [kontenerach](../containers/container-insights-overview.md) dla Kubernetes.

Począwszy od wersji wydanej po 2018 sierpnia, wprowadzamy następujące zmiany w naszym modelu pomocy technicznej:  

* Obsługiwane są tylko wersje serwera, a nie klient.  
* Zanotuj pomoc techniczną na dowolnym z [potwierdzonych dystrybucje systemu Azure](../../virtual-machines/linux/endorsed-distros.md)w systemie Linux. Należy pamiętać, że może wystąpić pewne opóźnienie między nowym dystrybucji/wersją zatwierdzona w systemie Azure Linux i jest ona obsługiwana dla agenta Log Analytics Linux.
* Wszystkie wersje pomocnicze są obsługiwane dla każdej wymienionej wersji głównej.
* Wersje, które przekazały datę zakończenia obsługi przez producenta, nie są obsługiwane.
* Obsługują tylko obrazy maszyn wirtualnych; kontenery, nawet te pochodzące z obrazów oficjalnych wydawców dystrybucji, nie są obsługiwane.
* Nowe wersje AMI nie są obsługiwane.  
* Obsługiwane są tylko wersje, które domyślnie OpenSSL 1. x.

>[!NOTE]
>Jeśli używasz dystrybucji lub wersji, która nie jest obecnie obsługiwana i nie jest zgodna z naszym modelem pomocy technicznej, zalecamy przerozwidlenie tego repozytorium, co oznacza, że pomoc techniczna firmy Microsoft nie będzie świadczyć pomocy z wersjami agentów z rozwidleniami.

### <a name="python-requirement"></a>Wymagania dotyczące języka Python

Począwszy od wersji 1.13.27 agenta, Agent systemu Linux będzie obsługiwał zarówno Język Python 2, jak i 3. Zawsze zalecamy korzystanie z najnowszego agenta. 

Jeśli używasz starszej wersji agenta, maszyna wirtualna musi domyślnie korzystać z języka Python 2. Jeśli maszyna wirtualna używa dystrybucji, która domyślnie nie zawiera języka Python 2, należy ją zainstalować. Następujące przykładowe polecenia zainstalują środowisko Python 2 na różnych dystrybucje.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SZŁO `zypper install -y python2`

Plik wykonywalny python2 musi mieć alias do języka *Python*. Oto jedna z metod, których można użyć do ustawienia tego aliasu:

1. Uruchom następujące polecenie, aby usunąć istniejące aliasy.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Uruchom następujące polecenie, aby utworzyć alias.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>Obsługiwane Ograniczanie poziomu systemu Linux
Agent pakietu OMS ma ograniczoną obsługę dostosowywania dla systemu Linux. 

Obecnie obsługiwane są następujące elementy: 
- Trybu

Są one rozważane, ale nie są jeszcze obsługiwane:
- SIC
- SELINUX

Inne metody ograniczania i dostosowywania nie są obsługiwane ani planowane dla agenta pakietu OMS.  

## <a name="agent-prerequisites"></a>Wymagania wstępne agenta

W poniższej tabeli przedstawiono pakiety wymagane dla [obsługiwanych dystrybucje systemu Linux](#supported-operating-systems) , na których zostanie zainstalowany agent.

|Wymagany pakiet |Opis |Minimalna wersja |
|-----------------|------------|----------------|
|Glibc |    Biblioteka GNU C | 2.5-12 
|Openssl    | Biblioteki OpenSSL | 1.0. x lub 1.1. x |
|Narzędzie Curl | zazwinięcie klienta sieci Web | 7.15.5 |
|Python | | 2,7 lub 3.6 +
|Python — ctypes | | 
|PAM | Podłączane moduły uwierzytelniania (PAM) | | 

>[!NOTE]
>Do zbierania komunikatów dziennika systemowego wymagane są rsyslog lub Dziennik systemu. Domyślny demon dziennika systemowego w wersji 5 Red Hat Enterprise Linux, CentOS i Oracle Linux wersja (sysklog) nie jest obsługiwany w przypadku zbierania zdarzeń dziennika systemowego. Aby zebrać dane dziennika systemu z tej wersji dystrybucji, demona rsyslog powinna zostać zainstalowana i skonfigurowana do zastępowania sysklog.

## <a name="network-requirements"></a>Wymagania dotyczące sieci
Zapoznaj się z [omówieniem log Analytics agenta](./log-analytics-agent.md#network-requirements) , aby poznać wymagania dotyczące sieci dla agenta systemu Linux.

## <a name="agent-install-package"></a>Pakiet instalacji agenta

Agent Log Analytics dla systemu Linux składa się z wielu pakietów. Plik Release zawiera następujące pakiety, które są dostępne przez uruchomienie pakietu Shell z `--extract` parametrem:

**Pakiet** | **Wersja** | **Opis**
----------- | ----------- | --------------
omsagent | 1.13.9 | Agent Log Analytics dla systemu Linux
omsconfig | 1.1.1 | Agent konfiguracji agenta Log Analytics
OMI | 1.6.4 | Open Management Infrastructure (OMI) — uproszczony serwer modelu wspólnych informacji. *Należy pamiętać, że OMI wymaga dostępu głównego do uruchomienia zadania firmy CRONUS niezbędnego do funkcjonowania usługi*
SCX | 1.6.4 | OMI dostawców CIM dla metryk wydajności systemu operacyjnego
Apache-cimprov | 1.0.1 | Dostawca monitorowania wydajności serwera Apache HTTP Server dla OMI. Instalowane tylko w przypadku wykrycia serwera Apache HTTP.
MySQL — cimprov | 1.0.1 | Dostawca monitorowania wydajności serwera MySQL dla OMI. Zainstalowane tylko w przypadku wykrycia serwera MySQL/MariaDB.
Docker-cimprov | 1.0.0 | Dostawca platformy Docker dla usługi OMI. Instalowane tylko w przypadku wykrycia platformy Docker.

### <a name="agent-installation-details"></a>Szczegóły instalacji agenta

Po zainstalowaniu agenta Log Analytics dla pakietów systemu Linux stosowane są następujące dodatkowe zmiany konfiguracji całego systemu. Te artefakty są usuwane po odinstalowaniu pakietu omsagent.

* Utworzono użytkownika bez uprawnień o nazwie: `omsagent` . Demon zostanie uruchomiony w ramach tego poświadczenia. 
* Plik *dołączany* sudo jest tworzony w `/etc/sudoers.d/omsagent` . `omsagent`Pozwala to na ponowne uruchomienie dziennika systemowego i demonów omsagent. Jeśli sudo dyrektywy *include* nie są obsługiwane w zainstalowanej wersji programu sudo, te wpisy będą zapisywane w `/etc/sudoers` .
* Konfiguracja dziennika systemowego zostanie zmodyfikowana w celu przekierowania podzbioru zdarzeń do agenta. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zbierania danych dziennika](data-sources-syslog.md)systemowego.

Na monitorowanym komputerze z systemem Linux Agent jest wyświetlany jako `omsagent` . `omsconfig` jest agentem konfiguracji agenta Log Analytics dla systemu Linux, który szuka nowej konfiguracji po stronie portalu co 5 minut. Nowa i zaktualizowana konfiguracja jest stosowana do plików konfiguracji agenta znajdujących się w lokalizacji `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="install-the-agent-using-wrapper-script"></a>Instalowanie agenta przy użyciu skryptu otoki

Poniższe kroki umożliwiają skonfigurowanie instalatora agenta dla Log Analytics na platformie Azure i w chmurze Azure Government przy użyciu skryptu otoki dla komputerów z systemem Linux, które mogą komunikować się bezpośrednio lub za pośrednictwem serwera proxy w celu pobrania agenta hostowanego w witrynie GitHub i zainstalowania agenta.  

Jeśli komputer z systemem Linux musi komunikować się za pomocą serwera proxy w celu Log Analytics, tę konfigurację można określić w wierszu polecenia, włączając w to polecenie `-p [protocol://][user:password@]proxyhost[:port]` . Właściwość *protokołu* akceptuje `http` lub `https` , a właściwość *ProxyHost* akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP serwera proxy. 

Na przykład: `https://proxy01.contoso.com:30443`

Jeśli w obu przypadkach wymagane jest uwierzytelnianie, należy określić nazwę użytkownika i hasło. Na przykład: `https://user01:password@proxy01.contoso.com:30443`

1. Aby skonfigurować komputer z systemem Linux do łączenia się z obszarem roboczym Log Analytics, uruchom następujące polecenie, podając identyfikator obszaru roboczego i klucz podstawowy. To polecenie pobiera agenta, weryfikuje jego sumę kontrolną i instaluje go.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Następujące polecenie zawiera `-p` parametr proxy i składnię przykładową, gdy uwierzytelnianie jest wymagane przez serwer proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Aby skonfigurować komputer z systemem Linux do łączenia się z obszarem roboczym Log Analytics w Azure Government chmurze, uruchom następujące polecenie, podając wcześniej skopiowany identyfikator obszaru roboczego i klucz podstawowy. To polecenie pobiera agenta, weryfikuje jego sumę kontrolną i instaluje go. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Następujące polecenie zawiera `-p` parametr proxy i składnię przykładową, gdy uwierzytelnianie jest wymagane przez serwer proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Uruchom ponownie agenta, uruchamiając następujące polecenie: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## <a name="install-the-agent-manually"></a>Ręczna instalacja agenta

Agent Log Analytics dla systemu Linux jest dostępny w ramach samodzielnego wyodrębniania i instalowalnego pakietu skryptu powłoki. Ten pakiet zawiera pakiety Debian i RPM dla każdego składnika agenta i może być instalowany bezpośrednio lub wyodrębniony w celu pobrania poszczególnych pakietów. Jeden pakiet jest dostarczany dla architektury x64 i jednego dla architektur x86. 

> [!NOTE]
> W przypadku maszyn wirtualnych platformy Azure zalecamy zainstalowanie na nich agenta przy użyciu [rozszerzenia maszyny wirtualnej log Analytics Azure dla systemu](../../virtual-machines/extensions/oms-linux.md) Linux. 

1. [Pobierz](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) i przenieś odpowiedni zbiór (x64 lub x86) do maszyny wirtualnej z systemem Linux lub komputera fizycznego przy użyciu protokołu SCP/SFTP.

2. Zainstaluj pakiet przy użyciu `--install` argumentu. Aby dołączyć do obszaru roboczego Log Analytics podczas instalacji, podaj `-w <WorkspaceID>` `-s <workspaceKey>` wcześniej skopiowane parametry i.

    >[!NOTE]
    >Musisz użyć `--upgrade` argumentu, jeśli są zainstalowane jakiekolwiek zależne pakiety, takie jak OMI, SCX, omsconfig lub ich starsze wersje, tak jak w przypadku, gdy program System Center Operations Manager Agent dla systemu Linux jest już zainstalowany. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Aby skonfigurować agenta systemu Linux do instalacji i nawiązywania połączenia z obszarem roboczym Log Analytics za pomocą bramy Log Analytics, uruchom następujące polecenie, podając parametry proxy, identyfikator obszaru roboczego i klucz obszaru roboczego. Tę konfigurację można określić w wierszu polecenia, dołączając `-p [protocol://][user:password@]proxyhost[:port]` . Właściwość *ProxyHost* akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP serwera bramy log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Jeśli wymagane jest uwierzytelnianie, należy określić nazwę użytkownika i hasło. Na przykład: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Aby skonfigurować komputer z systemem Linux do łączenia się z obszarem roboczym Log Analytics w Azure Government chmurze, uruchom następujące polecenie, podając wcześniej skopiowany identyfikator obszaru roboczego i klucz podstawowy.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Jeśli chcesz zainstalować pakiety agentów i skonfigurować je do raportowania do określonego obszaru roboczego Log Analytics w późniejszym czasie, uruchom następujące polecenie:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Jeśli chcesz wyodrębnić pakiety agentów z pakietu bez instalowania agenta, uruchom następujące polecenie:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>Uaktualnianie z poprzedniej wersji

Uaktualnianie z poprzedniej wersji, począwszy od wersji 1.0.0-47, jest obsługiwane w każdej wersji. Wykonaj instalację z `--upgrade` parametrem, aby uaktualnić wszystkie składniki agenta do najnowszej wersji.

## <a name="cache-information"></a>Informacje o pamięci podręcznej
Dane z agenta Log Analytics dla systemu Linux są przechowywane w pamięci podręcznej na komputerze lokalnym w lokalizacji *% STATE_DIR_WS%/out_oms_common*. Buffer * przed wysłaniem do Azure monitor. Niestandardowe dane dziennika są buforowane w *% STATE_DIR_WS%/out_oms_blob*. Buffer *. Ścieżka może być różna w przypadku niektórych [rozwiązań i typów danych](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=).

Agent próbuje przekazać co 20 sekund. Jeśli to się nie powiedzie, będzie oczekiwać wykładniczego wydłużenia czasu do momentu pomyślnego: 30 sekund przed kolejną próbą, 60 sekund przed trzecim, 120 sekund... i tak dalej, aż do maksymalnie 16 minut między ponownymi próbami ponownego nawiązania połączenia. Agent ponowi próbę do 6 razy dla danego fragmentu danych przed odrzuceniem i przejściem do kolejnego. Ten proces jest kontynuowany do momentu pomyślnego przekazania agenta. Oznacza to, że dane mogą być buforowane do około 30 minut, zanim zostaną odrzucone.

Domyślny rozmiar pamięci podręcznej wynosi 10 MB, ale można go zmodyfikować w [pliku omsagent. conf](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf).


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z tematem [Zarządzanie agentem log Analytics dla systemów Windows i Linux oraz ich obsługiwanie](agent-manage.md) , aby dowiedzieć się, jak ponownie skonfigurować, uaktualnić lub usunąć agenta z maszyny wirtualnej.

- Sprawdź [Rozwiązywanie problemów z agentem systemu Linux,](agent-linux-troubleshoot.md) Jeśli wystąpią problemy podczas instalowania agenta lub zarządzania nim.
