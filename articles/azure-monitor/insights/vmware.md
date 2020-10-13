---
title: VMware Monitoring rozwiązanie w Azure Monitor | Microsoft Docs
description: Dowiedz się, w jaki sposób rozwiązanie VMware Monitoring może pomóc w zarządzaniu dziennikami i monitorowaniu hostów ESXi.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: be50deb836082354db899e84ef24d75c4d403432
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450393"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Rozwiązanie VMware Monitoring (przestarzałe) w Azure Monitor

![Symbol VMware](./media/vmware/vmware-symbol.png)

> [!NOTE]
> Rozwiązanie VMware Monitoring zostało wycofane.  Klienci, którzy już zainstalowali rozwiązanie, mogą nadal z niego korzystać, ale nie można dodać VMware Monitoring do żadnych nowych obszarów roboczych.

VMware Monitoring rozwiązanie w Azure Monitor to rozwiązanie, które ułatwia tworzenie scentralizowanego podejścia do rejestrowania i monitorowania dużych dzienników VMware. W tym artykule opisano sposób rozwiązywania problemów, przechwytywania i zarządzania hostami ESXi w jednej lokalizacji przy użyciu rozwiązania. Dzięki rozwiązaniu można zobaczyć szczegółowe dane dla wszystkich hostów ESXi w jednej lokalizacji. Można wyświetlić największe liczby zdarzeń, stan i trendy maszyn wirtualnych i hostów ESXi dostarczonych za pomocą dzienników hosta ESXi. Aby rozwiązać problemy, można wyświetlić i przeszukać scentralizowane dzienniki hosta ESXi. I można tworzyć alerty oparte na zapytaniach wyszukiwania w dziennikach.

Rozwiązanie używa natywnej funkcji dziennika systemu hosta ESXi do wypychania danych do docelowej maszyny wirtualnej, która ma Log Analytics agenta. Jednak rozwiązanie nie zapisuje plików w dzienniku systemowym na docelowej maszynie wirtualnej. Agent Log Analytics otwiera port 1514 i nasłuchuje na tym. Po odebraniu danych Agent Log Analytics wypycha dane do Azure Monitor.

## <a name="install-and-configure-the-solution"></a>Instalowanie i Konfigurowanie rozwiązania
Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie.

* Dodaj rozwiązanie VMware Monitoring do subskrypcji przy użyciu procesu opisanego w artykule [Instalowanie rozwiązania monitorowania](./solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Obsługiwane hosty VMware ESXi
vSphere ESXi host 5,5, 6,0 i 6,5

#### <a name="prepare-a-linux-server"></a>Przygotowywanie serwera z systemem Linux
Utwórz maszynę wirtualną z systemem operacyjnym Linux, aby otrzymywać wszystkie dane dziennika systemu z hostów ESXi. [Agent log Analytics Linux](../learn/quick-collect-linux-computer.md) to punkt kolekcji dla wszystkich danych dziennika systemowego hosta ESXi. Do przesyłania dzienników do jednego serwera z systemem Linux można użyć wielu hostów ESXi, jak w poniższym przykładzie.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![przepływ dziennika systemowego](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Konfigurowanie kolekcji dziennika systemowego
1. Skonfiguruj przekazywanie dziennika systemowego do usługi VSphere. Aby uzyskać szczegółowe informacje ułatwiające skonfigurowanie przekazywania dziennika systemu, zobacz [Konfigurowanie dziennika systemowego na ESXi 5,0 i wyższych (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Przejdź do pozycji **Konfiguracja hosta ESXi**  >  **oprogramowanie**  >  **Zaawansowane ustawienia**  >  **dziennika**systemowego.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. W polu *Dziennik system. Global. logHost* Dodaj serwer z systemem Linux i numer portu *1514*. Na przykład `tcp://hostname:1514` lub `tcp://123.456.789.101:1514`
1. Otwórz Zaporę hosta ESXi dla dziennika systemowego. **Konfiguracja**  >  hosta ESXi **Oprogramowanie**  >  **Profil zabezpieczeń**  >  **Zapora** i otwarte **Właściwości**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Sprawdź konsolę vSphere, aby sprawdzić, czy dziennik systemowy jest prawidłowo skonfigurowany. Na hoście ESXI upewnij się, że skonfigurowano port **1514** .
1. Pobierz i Zainstaluj agenta Log Analytics dla systemu Linux na serwerze z systemem Linux. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją dla programu log Analytics Agent dla systemu Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Po zainstalowaniu agenta Log Analytics dla systemu Linux przejdź do katalogu/etc/opt/Microsoft/omsagent/sysconf/omsagent.d i skopiuj plik vmware_esxi. conf do katalogu/etc/opt/Microsoft/omsagent/conf/omsagent.d i Zmień właściciela/grupę i uprawnienia do pliku. Na przykład:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Uruchom ponownie agenta Log Analytics dla systemu Linux, uruchamiając `sudo /opt/microsoft/omsagent/bin/service_control restart` .
1. Przetestuj połączenie między serwerem z systemem Linux i hostem ESXi przy użyciu `nc` polecenia na hoście ESXi. Na przykład:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. W Azure Portal wykonaj zapytanie dziennika dla `VMware_CL` . Gdy Azure Monitor zbiera dane dziennika systemu, zachowuje format dziennika systemowego. W portalu są przechwytywane określone pola, takie jak *Nazwa hosta* i *procesname*.  

    ![Zrzut ekranu przedstawia zapytanie dziennika dla typu = VMware_CL z wynikiem sygnatury czasowej.](./media/vmware/type.png)  

    Jeśli wyniki przeszukiwania dzienników widoku są podobne do powyższego obrazu, można użyć pulpitu nawigacyjnego rozwiązania VMware Monitoring.  

## <a name="vmware-data-collection-details"></a>Szczegóły zbierania danych VMware
Rozwiązanie VMware Monitoring zbiera różne metryki wydajności i dane dzienników z hostów ESXi przy użyciu agentów Log Analytics dla systemu Linux, które zostały włączone.

W poniższej tabeli przedstawiono metody zbierania danych oraz inne szczegóły dotyczące sposobu zbierania danych.

| platforma | Agent Log Analytics dla systemu Linux | Agent System Center Operations Manager | Azure Storage | Operations Manager jest wymagane? | Dane agenta Operations Manager wysyłane przez grupę zarządzania | częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |co 3 minuty |

W poniższej tabeli przedstawiono przykłady pól danych zbieranych przez rozwiązanie VMware Monitoring:

| Nazwa pola | description |
| --- | --- |
| Device_s |Urządzenia magazynujące VMware |
| ESXIFailure_s |typy błędów |
| EventTime_t |czas wystąpienia zdarzenia |
| HostName_s |Nazwa hosta ESXi |
| Operation_s |Tworzenie maszyny wirtualnej lub usuwanie maszyny wirtualnej |
| ProcessName_s |Nazwa zdarzenia |
| ResourceId_s |Nazwa hosta VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Stan SCSI VMware |
| SyslogMessage_s |Dane dziennika systemu |
| UserName_s |Użytkownik, który utworzył lub usunął maszynę wirtualną |
| VMName_s |Nazwa maszyny wirtualnej |
| Computer (Komputer) |komputer-host |
| TimeGenerated |godzina wygenerowania danych |
| DataCenter_s |Oprogramowanie VMware Datacenter |
| StorageLatency_s |opóźnienie magazynu (MS) |

## <a name="vmware-monitoring-solution-overview"></a>Przegląd rozwiązania VMware Monitoring
Kafelek VMware zostanie wyświetlony w obszarze roboczym Log Analytics. Zapewnia ogólny widok wszelkich błędów. Po kliknięciu kafelka przejdziesz do widoku pulpitu nawigacyjnego.

![Zrzut ekranu przedstawia kafelek VMware, który wyświetla dziewięć błędów.](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Nawigowanie w widoku pulpitu nawigacyjnego
W widoku pulpitu nawigacyjnego **VMware** bloki są zorganizowane według:

* Liczba Stanów niepowodzenia
* Najpopularniejsze hosty według liczb zdarzeń
* Najważniejsze zliczenia zdarzeń
* Działania maszyny wirtualnej
* Zdarzenia dysku hosta ESXi

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Kliknij dowolny blok, aby otworzyć okienko wyszukiwania Log Analytics, które zawiera szczegółowe informacje specyficzne dla bloku.

W tym miejscu można edytować zapytanie dziennika w celu zmodyfikowania go dla określonego elementu. Aby uzyskać szczegółowe informacje na temat tworzenia zapytań dziennika, zobacz [Znajdowanie danych przy użyciu zapytań dzienników w Azure monitor](../log-query/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Znajdź zdarzenia hosta ESXi
Pojedynczy Host ESXi generuje wiele dzienników w oparciu o ich procesy. Rozwiązanie VMware Monitoring pozwala na ich scentralizowane i podsumowywanie liczby zdarzeń. Ten scentralizowany widok pomaga zrozumieć, który host ESXi ma dużą ilość zdarzeń i jakie zdarzenia często występują w danym środowisku.

![event](./media/vmware/events.png)

Możesz przejść do szczegółów, klikając hosta ESXi lub typ zdarzenia.

Po kliknięciu nazwy hosta ESXi można wyświetlić informacje z tego hosta ESXi. Jeśli chcesz zawęzić wyniki przy użyciu typu zdarzenia, Dodaj `“ProcessName_s=EVENT TYPE”` zapytanie wyszukiwania. W filtrze wyszukiwania można wybrać wartość **ProcessName** . Pozwala to zawęzić informacje dla Ciebie.

![Zrzut ekranu przedstawiający hosta ESXi na liczbę zdarzeń i podział na bloki typu zdarzenia w widoku pulpitu nawigacyjnego VMware Monitoring.](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Znajdowanie działań o dużej maszynie wirtualnej
Maszynę wirtualną można utworzyć i usunąć na dowolnym hoście ESXi. Pomaga administratorowi określić, ile maszyn wirtualnych tworzy Host ESXi. To z kolei pomaga zrozumieć planowanie wydajności i pojemności. Śledzenie zdarzeń aktywności maszyn wirtualnych jest niezwykle ważne w przypadku zarządzania środowiskiem.

![Zrzut ekranu bloku działania maszyny wirtualnej na pulpicie nawigacyjnym VMware Monitoring, przedstawiający wykres tworzenia i usuwania maszyn wirtualnych przez hosta ESXi.](./media/vmware/vmactivities1.png)

Jeśli chcesz zobaczyć dodatkowe dane dotyczące tworzenia maszyny wirtualnej hosta ESXi, kliknij nazwę hosta ESXi.

![Zrzut ekranu okienka z pulpitu nawigacyjnego VMware Monitoring przedstawiający tabelę z wierszem danych dla każdej maszyny wirtualnej na potrzeby tworzenia przez hosta ESXi.](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Typowe zapytania dzienników
Rozwiązanie obejmuje inne przydatne zapytania, które mogą ułatwić Zarządzanie hostami ESXi, takimi jak duże miejsce do magazynowania, opóźnienie magazynu i awaria ścieżki.

![Zrzut ekranu przedstawia zalecane wyszukiwania, które są przydatne do przechowywania zapytań.](./media/vmware/queries.png)


#### <a name="save-queries"></a>Zapisywanie zapytań
Zapisywanie zapytań dzienników jest standardową funkcją w Azure Monitor i ułatwia zachowanie wszelkich zapytań, które okazały się przydatne. Po utworzeniu kwerendy, która jest przydatna, Zapisz ją, klikając przycisk **Ulubione**. Zapisane zapytanie pozwala z łatwością użyć go później na stronie [mój pulpit nawigacyjny](../learn/tutorial-logs-dashboards.md) , na której można tworzyć własne niestandardowe pulpity nawigacyjne.

![Zrzut ekranu przedstawia część niestandardowego pulpitu nawigacyjnego z etykietą wyszukiwanie w dzienniku z ikonami Cofnij, Eksportuj, alert, Save, Ulubione i History.](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Tworzenie alertów z zapytań
Po utworzeniu zapytań możesz chcieć użyć zapytań w celu wygenerowania alertów w przypadku wystąpienia określonych zdarzeń. Aby uzyskać informacje o sposobach tworzenia alertów, zobacz [alerty w log Analytics](../platform/alerts-overview.md) . Przykłady zapytań dotyczących alertów i innych przykładów zapytań można znaleźć w blogu [monitorowanie oprogramowania VMware przy użyciu log Analytics](/archive/blogs/msoms/monitor-vmware-using-oms-log-analytics) .

## <a name="frequently-asked-questions"></a>Często zadawane pytania
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Co należy zrobić w ustawieniu hosta ESXi? Jaki wpływ będzie miał w bieżącym środowisku?
Rozwiązanie używa natywnego mechanizmu przekazujący hosta ESXi. Do przechwycenia dzienników nie jest potrzebne żadne dodatkowe oprogramowanie firmy Microsoft na hoście ESXi. Powinien mieć niewielki wpływ na istniejące środowisko. Należy jednak skonfigurować przekazywanie dziennika systemowego, które jest funkcją ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Czy muszę ponownie uruchomić mój Host ESXi?
Nie. Ten proces nie wymaga ponownego uruchomienia. Czasami usługa vSphere nie aktualizuje prawidłowo dziennika systemowego. W takim przypadku Zaloguj się do hosta ESXi i ponownie załaduj dziennik systemowy. Ponownie nie trzeba ponownie uruchamiać hosta, więc ten proces nie zakłóca pracy w danym środowisku.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Czy mogę zwiększyć lub zmniejszyć ilość danych dziennika wysyłanych do Log Analytics?
Tak. Możesz użyć ustawień poziomu dziennika hosta ESXi w vSphere. Zbieranie dzienników opiera się na poziomie *informacji* . Aby przeprowadzić inspekcję tworzenia lub usuwania maszyn wirtualnych, należy zachować poziom *informacji* na hoście. Aby uzyskać więcej informacji, zobacz [bazę wiedzy programu VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Dlaczego hostowanie nie udostępnia danych do Log Analytics? Ustawienie mój dziennik jest ustawione na informacje.
Wystąpił błąd ESXi hosta dla sygnatury czasowej dziennika systemowego. Aby uzyskać więcej informacji, zobacz [bazę wiedzy programu VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Po zastosowaniu obejścia host powinien działać normalnie.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Czy można mieć wiele ESXiów przekazujących dane dziennika systemowego do pojedynczej maszyny wirtualnej z omsagent?
Tak. Można korzystać z wielu ESXi hostów przesyłanych dalej do pojedynczej maszyny wirtualnej z omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Dlaczego dane przepływają do Log Analytics?
Może istnieć wiele przyczyn:

* Host ESXi nie wypychanie danych do maszyny wirtualnej z systemem omsagent. Aby przetestować, wykonaj następujące czynności:

  1. Aby potwierdzić, zaloguj się do hosta ESXi przy użyciu protokołu SSH i uruchom następujące polecenie: `nc -z ipaddressofVM 1514`

      Jeśli to się nie powiedzie, ustawienia vSphere w zaawansowanej konfiguracji prawdopodobnie nie są poprawne. Zobacz [Konfigurowanie kolekcji dziennika](#configure-syslog-collection) systemowego, aby uzyskać informacje na temat sposobu konfigurowania hosta ESXi na potrzeby przesyłania dalej dziennika systemowego.
  1. Jeśli połączenie z portem dziennika systemowego zakończyło się pomyślnie, ale nie widzisz żadnych danych, następnie ponownie załaduj dziennik systemowy na hoście ESXi przy użyciu protokołu SSH, aby uruchomić następujące polecenie: `esxcli system syslog reload`
* Maszyna wirtualna z agentem Log Analytics nie jest ustawiona poprawnie. Aby to przetestować, wykonaj następujące czynności:

  1. Log Analytics nasłuchuje na porcie 1514. Aby sprawdzić, czy jest otwarty, uruchom następujące polecenie: `netstat -a | grep 1514`
  1. Powinien być widoczny `1514/tcp` otwarty port. Jeśli nie, sprawdź, czy omsagent jest prawidłowo zainstalowany. Jeśli nie widzisz informacji o porcie, wówczas port dziennika systemu nie jest otwarty na maszynie wirtualnej.

    a. Sprawdź, czy Agent Log Analytics jest uruchomiony przy użyciu programu `ps -ef | grep oms` . Jeśli nie jest uruchomiona, uruchom proces, uruchamiając polecenie `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Otwórz plik `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

     c. Sprawdź, czy odpowiednie ustawienie użytkownika i grupy są prawidłowe, podobnie jak w przypadku: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Jeśli plik nie istnieje lub ustawienia użytkownika i grupy są błędne, wykonaj działania naprawcze, [przygotowując serwer z systemem Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Następne kroki
* Użyj [zapytań dzienników](../log-query/log-query-overview.md) w log Analytics, aby wyświetlić szczegółowe dane hosta VMware.
* [Utwórz własne pulpity nawigacyjne](../learn/tutorial-logs-dashboards.md) przedstawiające dane hosta VMware.
* [Utwórz alerty](../platform/alerts-overview.md) w przypadku wystąpienia określonych zdarzeń hosta VMware.

