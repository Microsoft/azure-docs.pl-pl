---
title: Rozwiązywanie problemów z agentem usługi Azure Log Analytics Linux | Microsoft Docs
description: Opisz objawy, przyczyny i rozwiązywanie typowych problemów dotyczących programu Log Analytics Agent dla systemu Linux w Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 6c262e75bfc3fe9c7fb5ce3e00b42b0ff6cbe3bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952953"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Jak rozwiązywać problemy z agentem usługi Log Analytics dla systemu Linux 

Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów dotyczących błędów, które mogą wystąpić w przypadku Log Analytics agenta dla systemu Linux w programie Azure Monitor i sugeruje rozwiązania, które można rozwiązać.

Jeśli żadna z tych kroków nie zadziałała, dostępne są również następujące kanały pomocy technicznej:

* Klienci korzystający z usług Premier Premium mogą otworzyć żądanie pomocy technicznej w wersji [Premium.](https://premier.microsoft.com/)
* Klienci z umowami pomocy technicznej systemu Azure mogą otworzyć żądanie pomocy technicznej [w Azure Portal](https://azure.microsoft.com/support/options/).
* Diagnozuj problemy z OMI, korzystając z [przewodnika rozwiązywania problemów z OMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Zaplikowanie problemu z usługą [GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Odwiedź stronę Log Analytics opinię, aby przejrzeć przesłane pomysły i usterki [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) albo plik nowy. 

## <a name="log-analytics-troubleshooting-tool"></a>Narzędzie do rozwiązywania problemów Log Analytics

Narzędzie do rozwiązywania problemów z agentem Log Analytics w systemie Linux jest narzędziem ułatwiającym znalezienie i zdiagnozowanie problemów z agentem Log Analytics. Jest on automatycznie dołączany do agenta podczas instalacji. Uruchomienie narzędzia powinno być pierwszym krokiem w diagnozowaniu problemu.

### <a name="how-to-use"></a>Jak używać
Narzędzie do rozwiązywania problemów można uruchomić, wklejając następujące polecenie do okna terminalu na komputerze z agentem Log Analytics: `sudo /opt/microsoft/omsagent/bin/troubleshooter`

### <a name="manual-installation"></a>Instalacja ręczna
Narzędzie do rozwiązywania problemów jest automatycznie dołączane podczas instalacji agenta Log Analytics. Jeśli jednak instalacja nie powiedzie się w jakikolwiek sposób, można ją także zainstalować ręcznie, wykonując poniższe kroki.

1. Skopiuj pakiet narzędzia do rozwiązywania problemów na swoją maszynę: `wget https://raw.github.com/microsoft/OMS-Agent-for-Linux/master/source/code/troubleshooter/omsagent_tst.tar.gz`
2. Rozpakuj pakiet: `tar -xzvf omsagent_tst.tar.gz`
3. Uruchom instalację ręczną: `sudo ./install_tst`

### <a name="scenarios-covered"></a>Omówione scenariusze
Poniżej znajduje się lista scenariuszy sprawdzanych przez narzędzie do rozwiązywania problemów:

1. Agent jest w złej kondycji, puls nie działa prawidłowo
2. Nie uruchomiono agenta, nie można nawiązać połączenia z usługami log Analytics
3. Dziennik systemowy agenta nie działa
4. Agent ma duże użycie procesora CPU/pamięci
5. Agent z problemami z instalacją
6. Dzienniki niestandardowe agenta nie działają
7. Zbierz dzienniki agentów

Aby uzyskać więcej informacji, zapoznaj się z naszą [dokumentacją usługi GitHub](https://github.com/microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting-Tool.md).

 >[!NOTE]
 >Uruchom narzędzie zbierające dzienniki w przypadku wystąpienia problemu. Zarejestrowanie dzienników na początku znacznie ułatwi zespołowi pomocy technicznej Rozwiązywanie problemów.

## <a name="purge-and-re-install-the-linux-agent"></a>Przeczyszczanie i Re-Install agenta systemu Linux

Zaobserwowano, że czysta ponowna instalacja agenta naprawi większość problemów. W rzeczywistości może to być pierwsza sugestia z działu pomocy technicznej, aby uzyskać agenta do stanu uncurropted z naszego zespołu pomocy technicznej. Uruchomienie narzędzia do rozwiązywania problemów, zbieranie dzienników i próba czystej ponownej instalacji ułatwi szybkie rozwiązywanie problemów.

1. Pobierz skrypt przeczyszczania:
- `$ wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/purge_omsagent.sh`
2. Uruchom skrypt przeczyszczania (z uprawnieniami sudo):
- `$ sudo sh purge_omsagent.sh`

## <a name="important-log-locations-and-log-collector-tool"></a>Ważne lokalizacje dzienników i narzędzie do modułu zbierającego dzienniki

 Plik | Ścieżka
 ---- | -----
 Plik dziennika Log Analytics Agent dla systemu Linux | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Plik dziennika konfiguracji agenta Log Analytics | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Zalecamy użycie naszego narzędzia zbierającego dzienniki do pobrania ważnych dzienników do rozwiązywania problemów lub przed przesłaniem problemu usługi GitHub. Więcej informacji na temat narzędzia i sposobu jego uruchomienia można znaleźć [tutaj](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Ważne pliki konfiguracji

 Kategoria | Lokalizacja pliku
 ----- | -----
 Dziennik systemu | `/etc/syslog-ng/syslog-ng.conf` lub `/etc/rsyslog.conf` lub `/etc/rsyslog.d/95-omsagent.conf`
 Performance, Nagios, Zabbix, Log Analytics Output and General Agent | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Dodatkowe konfiguracje | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Edytowanie plików konfiguracji dla liczników wydajności i dziennika systemowego jest zastępowane, jeśli kolekcja została skonfigurowana z [menu dane log Analytics ustawienia zaawansowane](../agents/agent-data-sources.md#configuring-data-sources) w Azure Portal dla obszaru roboczego. Aby wyłączyć konfigurację dla wszystkich agentów, wyłącz zbieranie danych z **ustawień zaawansowanych** log Analytics lub dla jednego agenta Uruchom następujące polecenie:  
> `sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable && sudo rm /etc/opt/omi/conf/omsconfig/configuration/Current.mof* /etc/opt/omi/conf/omsconfig/configuration/Pending.mof*`

## <a name="installation-error-codes"></a>Kody błędów instalacji

| Kod błędu | Znaczenie |
| --- | --- |
| NOT_DEFINED | Ponieważ niepotrzebne zależności nie są zainstalowane, wtyczka poddawana inspekcji auoms nie zostanie zainstalowana | Instalacja elementu auoms nie powiodła się, zainstaluj pakiet inspekcji. |
| 2 | Podano nieprawidłową opcję dla pakietu powłoki. Uruchom `sudo sh ./omsagent-*.universal*.sh --help` do użycia |
| 3 | Nie dostarczono żadnej opcji do pakietu powłoki. Uruchom `sudo sh ./omsagent-*.universal*.sh --help` w celu użycia. |
| 4 | Nieprawidłowy typ pakietu lub nieprawidłowe ustawienia serwera proxy; pakiety omsagent-*RPM*. sh można zainstalować tylko w systemach z procesorem RPM, a pakiety omsagent-*deb*. sh można zainstalować tylko w systemach opartych na debian. Zalecamy używanie Instalatora uniwersalnego z [najnowszej wersji](../vm/quick-collect-linux-computer.md#install-the-agent-for-linux). Zapoznaj się również z tematem Sprawdzanie ustawień serwera proxy. |
| 5 | Pakiet powłoki należy wykonać jako element główny lub wystąpił błąd 403 podczas dołączania. Uruchom polecenie za pomocą polecenia `sudo` . |
| 6 | Nieprawidłowa Architektura pakietu lub wystąpił błąd 200 podczas dołączania; pakiety omsagent-*x64.sh można zainstalować tylko w systemach 64-bitowych, a pakiety omsagent-* x86.sh można zainstalować tylko w systemach 32-bitowych. Pobierz prawidłowy pakiet dla swojej architektury z [najnowszej wersji](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Instalacja pakietu OMS nie powiodła się. Poszukaj danych wyjściowych polecenia dla błędu głównego. |
| 18 | Instalacja pakietu OMSConfig nie powiodła się. Poszukaj danych wyjściowych polecenia dla błędu głównego. |
| 19 | Instalacja pakietu OMI nie powiodła się. Poszukaj danych wyjściowych polecenia dla błędu głównego. |
| 20 | Instalacja pakietu SCX nie powiodła się. Poszukaj danych wyjściowych polecenia dla błędu głównego. |
| 21 | Instalacja zestawów Providers nie powiodła się. Poszukaj danych wyjściowych polecenia dla błędu głównego. |
| 22 | Instalacja pakietu z pakietem nie powiodła się. Poszukaj danych wyjściowych polecenia dla błędu głównego |
| 23 | Pakiet SCX lub OMI jest już zainstalowany. Użyj `--upgrade` zamiast tego, `--install` Aby zainstalować pakiet powłoki. |
| 30 | Wewnętrzny błąd pakietu. Aby uzyskać szczegółowe informacje z danych wyjściowych, Zastąp problem z usługą [GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 55 | Nieobsługiwana wersja OpenSSL lub nie można nawiązać połączenia z usługą Azure Monitor lub serwerach dpkg jest zablokowana lub brak programu zwinięcie. |
| 61 | Brak biblioteki ctypes języka Python. Zainstaluj bibliotekę lub pakiet języka python ctypes (python-ctypes). |
| 62 | Brak programu tar, zainstaluj pułapkę. |
| 63 | Brak programu SED, zainstaluj SED. |
| 64 | Brak programu zwinięcie, zainstaluj zwinięcie. |
| 65 | Brak programu GPG, zainstaluj GPG. |

## <a name="onboarding-error-codes"></a>Kody błędów dołączania

| Kod błędu | Znaczenie |
| --- | --- |
| 2 | Podano nieprawidłową opcję dla skryptu omsadmin. Uruchom `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` w celu użycia. |
| 3 | Podano nieprawidłową konfigurację dla skryptu omsadmin. Uruchom `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` w celu użycia. |
| 4 | Do skryptu omsadmin został udostępniony nieprawidłowy serwer proxy. Sprawdź serwer proxy i zapoznaj się z [dokumentacją dotyczącą korzystania z serwera proxy HTTP](./log-analytics-agent.md#firewall-requirements). |
| 5 | 403 błąd HTTP odebrany z Azure Monitor. Aby uzyskać szczegółowe informacje, zobacz pełne dane wyjściowe skryptu omsadmin. |
| 6 | Odebrano błąd HTTP inny niż 200 z Azure Monitor. Aby uzyskać szczegółowe informacje, zobacz pełne dane wyjściowe skryptu omsadmin. |
| 7 | Nie można nawiązać połączenia z Azure Monitor. Aby uzyskać szczegółowe informacje, zobacz pełne dane wyjściowe skryptu omsadmin. |
| 8 | Wystąpił błąd podczas dołączania do obszaru roboczego Log Analytics. Aby uzyskać szczegółowe informacje, zobacz pełne dane wyjściowe skryptu omsadmin. |
| 30 | Wewnętrzny błąd skryptu. Aby uzyskać szczegółowe informacje z danych wyjściowych, Zastąp problem z usługą [GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 31 | Wystąpił błąd podczas generowania identyfikatora agenta. Aby uzyskać szczegółowe informacje z danych wyjściowych, Zastąp problem z usługą [GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 32 | Wystąpił błąd podczas generowania certyfikatów. Aby uzyskać szczegółowe informacje, zobacz pełne dane wyjściowe skryptu omsadmin. |
| 33 | Wystąpił błąd podczas generowania konfiguracji usługi omsconfig. Aby uzyskać szczegółowe informacje z danych wyjściowych, Zastąp problem z usługą [GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 34 | Brak skryptu generowania konfiguracji. Ponowienie próby dołączenia za pomocą `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>` . |

## <a name="enable-debug-logging"></a>Włącz rejestrowanie debugowania
### <a name="oms-output-plugin-debug"></a>Debugowanie wtyczki wyjściowej pakietu OMS
 Pozostały w przypadku poziomów rejestrowania specyficznych dla wtyczki pozwala określić różne poziomy dziennika dla danych wejściowych i wyjściowych. Aby określić inny poziom dziennika dla danych wyjściowych pakietu OMS, Edytuj ogólną konfigurację agenta pod adresem `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` .  

 W przypadku wtyczki wyjściowej pakietu OMS przed końcem pliku konfiguracji Zmień `log_level` Właściwość z `info` na `debug` :

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

Rejestrowanie debugowania umożliwia wyświetlenie wsadowych operacji przekazywania do Azure Monitor rozdzielonych według typu, liczby elementów danych i czasu potrzebnego do wysłania:

*Przykładowy dziennik z włączoną obsługą debugowania:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Pełne dane wyjściowe
Zamiast korzystać z wtyczki wyjściowej OMS można także wyprowadzać elementy danych bezpośrednio do programu `stdout` , co jest widoczne w pliku dziennika log Analytics agenta dla systemu Linux.

W pliku konfiguracji agenta ogólnego Log Analytics w programie `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` Dodaj komentarz do wtyczki wyjściowej pakietu OMS przez dodanie `#` przed każdym wierszem:

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

W przypadku wtyczki wyjściowej Usuń komentarz z poniższej sekcji, usuwając z `#` niej przed każdym wierszem:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Problem: nie można nawiązać połączenia przy użyciu serwera proxy w celu Azure Monitor

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Serwer proxy określony podczas dołączania był nieprawidłowy
* Punkty końcowe usługi Azure Monitor i Azure Automation nie są uwzględnione na liście zatwierdzonych w centrum danych 

### <a name="resolution"></a>Rozwiązanie
1. Reonboard Azure Monitor z agentem Log Analytics dla systemu Linux przy użyciu następującego polecenia z `-v` włączoną opcją. Umożliwia pełne dane wyjściowe agenta łączącego się za pomocą serwera proxy w celu Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Przejrzyj sekcję [Aktualizowanie ustawień serwera proxy](agent-manage.md#update-proxy-settings) , aby upewnić się, że Agent jest prawidłowo skonfigurowany do komunikowania się za pomocą serwera proxy.    

3. Sprawdź, czy punkty końcowe opisane na liście [wymagania dotyczące zapory sieciowej](./log-analytics-agent.md#firewall-requirements) Azure monitor są prawidłowo dodawane do listy dozwolonych. Jeśli używasz Azure Automation, należy również połączyć niezbędne czynności konfiguracyjne sieci.

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problem: Wystąpił błąd 403 podczas próby dołączenia

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Data i godzina są niepoprawne na serwerze z systemem Linux 
* Nieprawidłowy identyfikator obszaru roboczego i klucz obszaru roboczego

### <a name="resolution"></a>Rozwiązanie

1. Sprawdź godzinę na serwerze z systemem Linux przy użyciu daty polecenia. Jeśli czas wynosi +/-15 minut od bieżącego czasu, dołączanie kończy się niepowodzeniem. Aby naprawić tę aktualizację, należy zaktualizować datę i/lub strefę czasową serwera z systemem Linux. 
2. Sprawdź, czy zainstalowano najnowszą wersję agenta Log Analytics dla systemu Linux.  Najnowsza wersja powiadamia teraz, gdy pochylenie czasu spowoduje błąd dołączania.
3. Reonboard przy użyciu poprawnego identyfikatora obszaru roboczego i klucza obszaru roboczego zgodnie z instrukcjami dotyczącymi instalacji we wcześniejszej części tego artykułu.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problem: po dołączeniu do pliku dziennika zobaczysz błąd 500 i 404.
Jest to znany problem występujący podczas pierwszego przekazywania danych systemu Linux do obszaru roboczego Log Analytics. Nie ma to wpływu na przesyłane dane lub środowisko usługi.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Problem: widzisz omiagent przy użyciu procesora CPU 100%

### <a name="probable-causes"></a>Prawdopodobne przyczyny
Regresja w pakiecie NSS-PEM [1.0.3 -5. el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) spowodowała poważny problem z wydajnością, dzięki czemu widzimy dużo w dystrybucji RedHat/CentOS 7. x. Aby dowiedzieć się więcej o tym problemie, zapoznaj się z następującą dokumentacją: błąd [1667121 regresji wydajności w libcurl](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

Błędy związane z wydajnością nie zachodzą przez cały czas i są bardzo trudne do odtworzenia. Jeśli wystąpi ten problem z omiagent, należy użyć skryptu omiHighCPUDiagnostics.sh, który będzie zbierać ślad stosu dla omiagent, gdy zostanie przekroczony określony próg.

1. Pobierz skrypt <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Uruchom diagnostykę przez 24 godziny z progiem procesora 30% <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Stosu wywołań będzie zrzucany w pliku omiagent_trace, jeśli zauważysz wiele wywołań funkcji NSS, wykonaj kroki opisane poniżej.

### <a name="resolution-step-by-step"></a>Rozwiązanie (krok po kroku)

1. Uaktualnij pakiet NSS-PEM do wersji [1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Jeśli NSS-PEM nie jest dostępny do uaktualnienia (na przykład w przypadku CentOS), nastąpi obniżenie poziomu do 7.29.0-46. Jeśli przez pomyłkę uruchomisz polecenie "yum update", zwinięcie zostanie uaktualnione do 7.29.0-51, a problem będzie się powtarzać. <br/>
`sudo yum downgrade curl libcurl`

3. Uruchom ponownie OMI: <br/>
`sudo scxadmin -restart`


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problem: nie widzisz przekierowanych komunikatów dziennika systemowego 

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Konfiguracja zastosowana do serwera z systemem Linux nie zezwala na zbieranie wysłanych obiektów i/lub poziomów rejestrowania
* Dziennik systemowy nie jest prawidłowo przekazywany do serwera z systemem Linux
* Liczba przesyłanych dalej komunikatów na sekundę jest zbyt duża, aby można było obsłużyć podstawową konfigurację agenta Log Analytics dla systemu Linux

### <a name="resolution"></a>Rozwiązanie
* Sprawdź, czy konfiguracja w obszarze roboczym Log Analytics dla dziennika systemowego ma wszystkie obiekty i poprawne poziomy dzienników. Przejrzyj temat [Konfigurowanie kolekcji dziennika systemowego w Azure Portal](data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Sprawdź, czy natywne demoy komunikatów dziennika systemu ( `rsyslog` , `syslog-ng` ) mogą odbierać przesyłane dalej wiadomości
* Sprawdź ustawienia zapory na serwerze dziennika systemowego, aby upewnić się, że komunikaty nie są blokowane
* Symuluj komunikat dziennika systemowego, aby Log Analytics przy użyciu `logger` polecenia
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problem: otrzymujesz adres errno, który jest już używany w pliku dziennika omsagent
Jeśli widzisz `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` w omsagent. log.

### <a name="probable-causes"></a>Prawdopodobne przyczyny
Ten błąd oznacza, że rozszerzenie diagnostyczne systemu Linux (LAD) jest zainstalowane obok rozszerzenia maszyny wirtualnej z systemem Log Analytics Linux i używa tego samego portu do zbierania danych dziennika systemowego jako omsagent.

### <a name="resolution"></a>Rozwiązanie
1. Jako katalog główny wykonaj następujące polecenia (należy zauważyć, że 25224 jest przykładem i istnieje możliwość, że w Twoim środowisku zobaczysz inny numer portu używany przez LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Następnie należy zmodyfikować `rsyslogd` plik prawidłowy lub konfiguracyjny `syslog_ng` i zmienić konfigurację powiązaną z lad na zapis w porcie 25229.

2. Jeśli maszyna wirtualna jest uruchomiona `rsyslogd` , plik do zmodyfikowania to: `/etc/rsyslog.d/95-omsagent.conf` (jeśli istnieje, Else `/etc/rsyslog` ). Jeśli maszyna wirtualna jest uruchomiona `syslog_ng` , plik, który ma zostać zmodyfikowany, to: `/etc/syslog-ng/syslog-ng.conf` .
3. Uruchom ponownie omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart` .
4. Uruchom ponownie usługę Dziennik systemowy.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problem: nie można odinstalować omsagent za pomocą opcji przeczyszczania

### <a name="probable-causes"></a>Prawdopodobne przyczyny

* Rozszerzenie diagnostyki systemu Linux jest zainstalowane
* Rozszerzenie diagnostyczne systemu Linux zostało zainstalowane i odinstalowane, ale nadal widzisz błąd dotyczący omsagent używany przez procesem MDSD i nie można go usunąć.

### <a name="resolution"></a>Rozwiązanie
1. Odinstaluj rozszerzenie diagnostyczne systemu Linux (LAD).
2. Usuń pliki rozszerzeń diagnostycznych systemu Linux z maszyny, jeśli znajdują się one w następującej lokalizacji: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` i `/var/opt/microsoft/omsagent/LAD/` .

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problem: nie można wyświetlić danych Nagios danych 

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Użytkownik Omsagent nie ma uprawnień do odczytu z pliku dziennika Nagios
* Nie usunięto komentarzy do źródła i filtru Nagios z pliku omsagent. conf

### <a name="resolution"></a>Rozwiązanie
1. Dodaj użytkownika omsagent do odczytu z pliku Nagios, wykonując te [instrukcje](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. W pliku konfiguracji ogólnej agenta Log Analytics dla systemu Linux w systemie `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` upewnij się  , że dla źródła i filtru Nagios są komentarze.

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problem: nie widzisz żadnych danych systemu Linux 

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Dołączanie do Azure Monitor nie powiodło się
* Połączenie z Azure Monitor zostało zablokowane
* Maszyna wirtualna została ponownie uruchomiona
* Pakiet OMI został ręcznie uaktualniony do nowszej wersji w porównaniu z tym, co zostało zainstalowane przez program Log Analytics Agent dla systemu Linux
* OMI jest zamrożony, blokując agenta pakietu OMS
* Błąd podczas *znajdowania klasy* dzienników zasobów DSC w `omsconfig.log` pliku dziennika
* Utworzono kopię zapasową Log Analytics agenta dla danych
* *Bieżąca konfiguracja dzienników DSC nie istnieje. Wykonaj Start-DscConfiguration polecenie z parametrem-Path, aby określić plik konfiguracji i najpierw utworzyć bieżącą konfigurację.* w `omsconfig.log` pliku dziennika, ale nie istnieje żaden komunikat dziennika dotyczący `PerformRequiredConfigurationChecks` operacji.

### <a name="resolution"></a>Rozwiązanie
1. Zainstaluj wszystkie zależności, takie jak pakiet objęty inspekcją.
2. Sprawdź, czy dołączanie do Azure Monitor powiodło się, sprawdzając, czy istnieje następujący plik: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` .  Jeśli nie, reonboard za pomocą [instrukcji](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)omsadmin.sh wiersza polecenia.
4. W przypadku korzystania z serwera proxy Sprawdź powyższe kroki rozwiązywania problemów z serwerem proxy.
5. W niektórych systemach dystrybucji platformy Azure demon serwera Omid OMI nie zostanie uruchomiony po ponownym uruchomieniu maszyny wirtualnej. Spowoduje to wyświetlenie danych dotyczących rozwiązań dotyczących inspekcji, śledzenia zmian lub UpdateManagement. Obejście polega na ręcznym uruchomieniu serwera OMI przez uruchomienie `sudo /opt/omi/bin/service_control restart` .
6. Po ręcznym uaktualnieniu pakietu OMI do nowszej wersji należy go ręcznie uruchomić ponownie, aby Agent Log Analytics kontynuował działanie. Ten krok jest wymagany w przypadku niektórych dystrybucje, w których serwer OMI nie jest uruchamiany automatycznie po uaktualnieniu. Uruchom `sudo /opt/omi/bin/service_control restart` , aby ponownie uruchomić OMI.
* W niektórych sytuacjach OMI można zablokować. Agent pakietu OMS może wprowadzić zablokowany stan Oczekujący na OMI, blokując wszystkie kolekcje danych. Proces agenta pakietu OMS będzie uruchomiony, ale nie będzie żadnych działań, które są nieodpowiednie dla nowych wierszy dziennika (takich jak wysłane pulsy) `omsagent.log` . Uruchom ponownie program OMI w `sudo /opt/omi/bin/service_control restart` celu odzyskania agenta.
7. Jeśli zostanie wyświetlona błąd *nie znaleziono klasy* zasobów DSC w omsconfig. log, uruchom polecenie `sudo /opt/omi/bin/service_control restart` .
8. W niektórych przypadkach, gdy Agent Log Analytics dla systemu Linux nie może nawiązać komunikacji z Azure Monitor, zostanie utworzona kopia zapasowa danych z agenta do pełnego rozmiaru buforu: 50 MB. Agent należy uruchomić ponownie, uruchamiając następujące polecenie `/opt/microsoft/omsagent/bin/service_control restart` .

    >[!NOTE]
    >Ten problem został rozwiązany w wersji agenta 1.1.0-28 lub nowszej
    >

* Jeśli `omsconfig.log` plik dziennika nie wskazuje, że `PerformRequiredConfigurationChecks` operacje są uruchamiane okresowo w systemie, może wystąpić problem z zadaniem/usługą firmy cronus. Upewnij się, że zadanie firmy CRONUS istnieje w ramach `/etc/cron.d/OMSConsistencyInvoker` . W razie potrzeby uruchom następujące polecenia, aby utworzyć zadanie firmy CRONUS:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Upewnij się również, że usługa firmy CRONUS jest uruchomiona. `service cron status`Aby sprawdzić stan tej usługi, można użyć opcji with Debian, Ubuntu, SUSE lub `service crond status` with RHEL, Oracle Linux CentOS. Jeśli usługa nie istnieje, możesz zainstalować pliki binarne i uruchomić usługę, wykonując następujące czynności:

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SZŁO**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problem: podczas konfigurowania kolekcji z portalu dla liczników wydajności dziennika systemowego lub systemu Linux ustawienia nie są stosowane

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Agent Log Analytics dla systemu Linux nie otrzymał najnowszej konfiguracji
* Zmienione ustawienia w portalu nie zostały zastosowane

### <a name="resolution"></a>Rozwiązanie
**Tło:** `omsconfig` jest agentem Log Analytics agenta konfiguracji dla systemu Linux, który wyszukuje nową konfigurację po stronie portalu co pięć minut. Ta konfiguracja zostanie następnie zastosowana do plików konfiguracji agenta Log Analytics dla systemu Linux znajdujących się w/etc/opt/Microsoft/omsagent/conf/omsagent.conf.

* W niektórych przypadkach Agent usługi Log Analytics dla systemu Linux może nie być w stanie komunikować się z usługą konfiguracji portalu, ponieważ nie jest stosowana Najnowsza konfiguracja.
  1. Sprawdź, czy `omsconfig` Agent jest zainstalowany, uruchamiając program `dpkg --list omsconfig` lub `rpm -qi omsconfig` .  Jeśli nie jest zainstalowana, należy ponownie zainstalować najnowszą wersję agenta Log Analytics dla systemu Linux.

  2. Sprawdź, czy `omsconfig` Agent może komunikować się z Azure monitor, uruchamiając następujące polecenie `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` . To polecenie zwraca konfigurację otrzymywaną przez agenta z usługi, w tym ustawienia dziennika systemu Linux, liczniki wydajności systemu i dzienniki niestandardowe. Jeśli to polecenie nie powiedzie się, uruchom następujące polecenie `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` . To polecenie wymusza, aby Agent omsconfig mógł komunikować się z Azure Monitor i pobrać najnowszą konfigurację.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problem: nie widzisz żadnych niestandardowych danych dziennika 

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Dołączanie do Azure Monitor nie powiodło się.
* Ustawienie **Zastosuj następującą konfigurację do serwerów z systemem Linux** nie zostało wybrane.
* omsconfig nie pobrała najnowszej konfiguracji dziennika niestandardowego z usługi.
* Agent Log Analytics dla użytkownika systemu Linux `omsagent` nie może uzyskać dostępu do dziennika niestandardowego z powodu uprawnień lub nie został znaleziony.  Mogą pojawić się następujące błędy:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Znany problem związany z sytuacją wyścigu ustalony w Log Analytics Agent dla systemu Linux w wersji 1.1.0-217

### <a name="resolution"></a>Rozwiązanie
1. Sprawdź, czy dołączanie do Azure Monitor powiodło się, sprawdzając, czy następujący plik istnieje: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` . W przeciwnym razie:  

  1. Reonboard przy użyciu [instrukcji](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)omsadmin.sh wiersza polecenia.
  2. W obszarze **Ustawienia zaawansowane** w Azure Portal upewnij się, że ustawienie **Zastosuj następującą konfigurację do serwerów z systemem Linux** jest włączona.  

2. Sprawdź, czy `omsconfig` Agent może komunikować się z Azure monitor, uruchamiając następujące polecenie `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` .  To polecenie zwraca konfigurację otrzymywaną przez agenta z usługi, w tym ustawienia dziennika systemu Linux, liczniki wydajności systemu i dzienniki niestandardowe. Jeśli to polecenie nie powiedzie się, uruchom następujące polecenie `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` . To polecenie wymusza, aby Agent omsconfig mógł komunikować się z Azure Monitor i pobrać najnowszą konfigurację.

**Tło:** Zamiast agenta Log Analytics dla systemu Linux działającego jako użytkownik uprzywilejowany `root` , agent działa jako `omsagent` użytkownik. W większości przypadków jawne uprawnienia muszą zostać przyznane temu użytkownikowi w celu odczytania niektórych plików. Aby udzielić uprawnienia `omsagent` użytkownikowi, uruchom następujące polecenia:

1. Dodaj `omsagent` użytkownika do konkretnej grupy `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Udzielanie uniwersalnego dostępu do odczytu do wymaganego pliku `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Istnieje znany problem z sytuacją Log Analytics wyścigu z 1.1.0 agentem dla systemu Linux w wersji starszej niż 217. Po zaktualizowaniu do najnowszego agenta Uruchom następujące polecenie, aby uzyskać najnowszą wersję wtyczki danych wyjściowych `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` .

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problem: próbujesz reonboard z nowym obszarem roboczym
Podczas próby reonboard agenta do nowego obszaru roboczego przed reonboarding należy oczyścić konfigurację agenta Log Analytics. Aby wyczyścić starą konfigurację z agenta, Uruchom pakiet powłoki z `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Lub

```
sudo sh ./onboard_agent.sh --purge
```

Możesz kontynuować reonboard po użyciu `--purge` opcji

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Rozszerzenie agenta Log Analytics w Azure Portal jest oznaczone jako zakończone niepowodzeniem: Inicjowanie obsługi nie powiodło się

### <a name="probable-causes"></a>Prawdopodobne przyczyny
* Agent Log Analytics został usunięty z systemu operacyjnego
* Usługa agenta Log Analytics nie działa, jest wyłączona lub nie została skonfigurowana

### <a name="resolution"></a>Rozwiązanie 
Aby rozwiązać ten problem, wykonaj następujące czynności.
1. Usuń rozszerzenie z Azure Portal.
2. Zainstaluj agenta zgodnie z [instrukcjami](../vm/quick-collect-linux-computer.md).
3. Uruchom ponownie agenta, uruchamiając następujące polecenie: `sudo /opt/microsoft/omsagent/bin/service_control restart` .
* Poczekaj kilka minut, a stan aprowizacji zostanie zmieniony na **zainicjowanie pomyślnie**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problem: uaktualnienie agenta Log Analytics na żądanie

### <a name="probable-causes"></a>Prawdopodobne przyczyny

Pakiety agentów Log Analytics na hoście są nieaktualne.

### <a name="resolution"></a>Rozwiązanie 
Aby rozwiązać ten problem, wykonaj następujące czynności.

1. Sprawdź dostępność najnowszej wersji na [stronie](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Pobierz skrypt instalacyjny (1.4.2-124 jako Przykładowa wersja):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Uaktualnij pakiety przez wykonanie `sudo sh ./omsagent-*.universal.x64.sh --upgrade` .
