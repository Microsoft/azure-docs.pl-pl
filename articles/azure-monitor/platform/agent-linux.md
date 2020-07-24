---
title: Łączenie komputerów z systemem Linux z Azure Monitor | Microsoft Docs
description: W tym artykule opisano sposób łączenia komputerów z systemem Linux hostowanych w innych chmurach lub lokalnych w celu Azure Monitor z agentem Log Analytics dla systemu Linux.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: accd7c3ad82853c1f2af0b632326b2798f85b36b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073658"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Łączenie komputerów z systemem Linux z Azure Monitor

W celu monitorowania maszyn wirtualnych lub komputerów fizycznych w lokalnym centrum danych lub w innym środowisku chmury przy użyciu Azure Monitor należy wdrożyć agenta Log Analytics i skonfigurować go do raportowania w obszarze roboczym Log Analytics. Agent obsługuje również rolę hybrydowego procesu roboczego elementu Runbook dla Azure Automation.

Log Analytics agenta dla systemu Linux można zainstalować przy użyciu jednej z poniższych metod. Szczegółowe informacje o używaniu każdej metody są podane w dalszej części artykułu.

* [Ręcznie Pobierz i zainstaluj](#install-the-agent-manually) agenta. Jest to wymagane, gdy komputer z systemem Linux nie ma dostępu do Internetu i będzie komunikować się z Azure Monitor lub Azure Automation za pomocą [bramy log Analytics](gateway.md). 
* [Zainstaluj agenta dla systemu Linux, używając skryptu otoki](#install-the-agent-using-wrapper-script) hostowanego w witrynie GitHub. Jest to zalecana metoda instalacji i uaktualnienia agenta, gdy komputer ma łączność z Internetem bezpośrednio lub za pośrednictwem serwera proxy.

Aby poznać obsługiwaną konfigurację, przejrzyj tematy dotyczące [obsługiwanych systemów operacyjnych Linux](log-analytics-agent.md#supported-linux-operating-systems) oraz [konfiguracji zapory sieciowej](log-analytics-agent.md#network-requirements).

>[!NOTE]
>Agenta usługi Log Analytics dla systemu Linux nie można skonfigurować w taki sposób, aby przesyłał raporty do więcej niż jednego obszaru roboczego usługi Log Analytics. Można ją skonfigurować tylko do raportowania zarówno do System Center Operations Manager grupy zarządzania, jak i do Log Analytics obszaru roboczego, lub pojedynczo.

## <a name="agent-install-package"></a>Pakiet instalacji agenta

Agent Log Analytics dla systemu Linux składa się z wielu pakietów. Plik Release zawiera następujące pakiety, które są dostępne przez uruchomienie pakietu Shell z `--extract` parametrem:

**Pakiet** | **Wersja** | **Opis**
----------- | ----------- | --------------
omsagent | 1.12.15 | Agent Log Analytics dla systemu Linux
omsconfig | 1.1.1 | Agent konfiguracji agenta Log Analytics
OMI | 1.6.3 | Open Management Infrastructure (OMI) — uproszczony serwer modelu wspólnych informacji. *Należy pamiętać, że OMI wymaga dostępu głównego do uruchomienia zadania firmy CRONUS niezbędnego do funkcjonowania usługi*
SCX | 1.6.3 | OMI dostawców CIM dla metryk wydajności systemu operacyjnego
Apache-cimprov | 1.0.1 | Dostawca monitorowania wydajności serwera Apache HTTP Server dla OMI. Instalowane tylko w przypadku wykrycia serwera Apache HTTP.
MySQL — cimprov | 1.0.1 | Dostawca monitorowania wydajności serwera MySQL dla OMI. Zainstalowane tylko w przypadku wykrycia serwera MySQL/MariaDB.
Docker-cimprov | 1.0.0 | Dostawca platformy Docker dla usługi OMI. Instalowane tylko w przypadku wykrycia platformy Docker.

### <a name="agent-installation-details"></a>Szczegóły instalacji agenta

Po zainstalowaniu agenta Log Analytics dla pakietów systemu Linux stosowane są następujące dodatkowe zmiany konfiguracji całego systemu. Te artefakty są usuwane po odinstalowaniu pakietu omsagent.

* Utworzono użytkownika bez uprawnień o nazwie: `omsagent` . Demon zostanie uruchomiony w ramach tego poświadczenia. 
* Plik *dołączany* sudo jest tworzony w `/etc/sudoers.d/omsagent` . `omsagent`Pozwala to na ponowne uruchomienie dziennika systemowego i demonów omsagent. Jeśli sudo dyrektywy *include* nie są obsługiwane w zainstalowanej wersji programu sudo, te wpisy będą zapisywane w `/etc/sudoers` .
* Konfiguracja dziennika systemowego zostanie zmodyfikowana w celu przekierowania podzbioru zdarzeń do agenta. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zbierania danych dziennika](data-sources-syslog.md)systemowego.

Na monitorowanym komputerze z systemem Linux Agent jest wyświetlany jako `omsagent` . `omsconfig`jest agentem konfiguracji agenta Log Analytics dla systemu Linux, który szuka nowej konfiguracji po stronie portalu co 5 minut. Nowa i zaktualizowana konfiguracja jest stosowana do plików konfiguracji agenta znajdujących się w lokalizacji `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="obtain-workspace-id-and-key"></a>Uzyskiwanie identyfikatora i klucza obszaru roboczego

Przed zainstalowaniem agenta usługi Log Analytics dla systemu Linux potrzebne są identyfikator i klucz obszaru roboczego usługi Log Analytics. Te informacje są wymagane podczas instalacji agenta w celu poprawnego skonfigurowania i upewnienia się, że może on pomyślnie komunikować się z Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. W lewym górnym rogu Azure Portal wybierz pozycję **wszystkie usługi**. W polu wyszukiwania wprowadź **log Analytics**. Podczas wpisywania lista jest filtrowana na podstawie danych wejściowych. Wybierz **log Analytics obszary robocze**.

2. Na liście obszarów roboczych Log Analytics wybierz utworzony wcześniej obszar roboczy. (Może być nazwany IT **DefaultLAWorkspace**).

3. Wybierz pozycję **Ustawienia zaawansowane**:

    ![Menu Ustawienia zaawansowane dla Log Analytics w Azure Portal](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Wybierz **Połączone źródła**, a następnie **Serwery Linux**.

5. Wartość z prawej strony **identyfikatora obszaru roboczego** i **klucza podstawowego**. Skopiuj i wklej obie wartości do ulubionego edytora.

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

## <a name="supported-linux-hardening"></a>Obsługiwane Ograniczanie poziomu systemu Linux
Agent pakietu OMS ma ograniczoną obsługę dostosowywania dla systemu Linux. 

Obecnie obsługiwane są następujące elementy: 
- Trybu

Następujące elementy są planowane, ale nie są jeszcze obsługiwane:
- CIS — SELINUX

Inne metody ograniczania i dostosowywania nie są obsługiwane ani planowane dla agenta pakietu OMS.  


## <a name="upgrade-from-a-previous-release"></a>Uaktualnianie z poprzedniej wersji

Uaktualnianie z poprzedniej wersji, począwszy od wersji 1.0.0-47, jest obsługiwane w każdej wersji. Wykonaj instalację z `--upgrade` parametrem, aby uaktualnić wszystkie składniki agenta do najnowszej wersji.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z tematem [Zarządzanie agentem log Analytics dla systemów Windows i Linux oraz ich obsługiwanie](agent-manage.md) , aby dowiedzieć się, jak ponownie skonfigurować, uaktualnić lub usunąć agenta z maszyny wirtualnej.

- Sprawdź [Rozwiązywanie problemów z agentem systemu Linux,](agent-linux-troubleshoot.md) Jeśli wystąpią problemy podczas instalowania agenta lub zarządzania nim.
