---
title: Jak używać że program perfinsights Linux w Microsoft Azure | Microsoft Docs
description: Dowiedz się, jak używać że program perfinsights do rozwiązywania problemów z wydajnością maszyn wirtualnych z systemem Linux.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: f785f44b88c7f0d5f0b6f43114070888bb23146d
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302486"
---
# <a name="how-to-use-perfinsights"></a>Korzystanie z narzędzia PerfInsights

[Że program perfinsights Linux](https://aka.ms/perfinsightslinuxdownload) to narzędzie do samodzielnego rozwiązywania problemów, które zbiera i analizuje dane diagnostyczne i zawiera raport, który pomaga rozwiązywać problemy z wydajnością maszyn wirtualnych z systemem Linux na platformie Azure. Że program perfinsights można uruchamiać na obsługiwanych maszynach wirtualnych jako autonomiczne narzędzie lub bezpośrednio z portalu przy użyciu narzędzia [Diagnostyka wydajności dla maszyn wirtualnych platformy Azure](performance-diagnostics.md).

Jeśli występują problemy z wydajnością maszyn wirtualnych przed skontaktowaniem się z pomocą techniczną, Uruchom to narzędzie.

## <a name="supported-troubleshooting-scenarios"></a>Obsługiwane scenariusze rozwiązywania problemów

Że program perfinsights może zbierać i analizować kilka rodzajów informacji. W poniższych sekcjach omówiono typowe scenariusze.

### <a name="quick-performance-analysis"></a>Analiza szybkiej wydajności

W tym scenariuszu są zbierane podstawowe informacje, takie jak Konfiguracja magazynu i sprzętowa maszyny wirtualnej, różne dzienniki, w tym:

- Informacje o systemie operacyjnym

- Informacje o urządzeniu PCI

- Ogólne Dzienniki systemu operacyjnego gościa

- Pliki konfiguracji

- Informacje o magazynie

- Konfiguracja maszyny wirtualnej platformy Azure (zebrana przy użyciu [usługi azure instance Metadata Service](../windows/instance-metadata-service.md))

- Lista uruchomionych procesów, dysku, pamięci i użycia procesora CPU

- Informacje o sieci

Jest to pasywna kolekcja informacji, która nie powinna mieć wpływu na system.

>[!Note]
>Scenariusz analizy szybkiej wydajności jest automatycznie uwzględniany w każdym z następujących scenariuszy:

### <a name="performance-analysis"></a>Analiza wydajności

Ten scenariusz jest podobny do szybkiej analizy wydajności, ale umożliwia przechwytywanie informacji diagnostycznych przez dłuższy czas.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Jakiego rodzaju informacje są zbierane przez że program perfinsights

Zbierane są informacje o maszynie wirtualnej z systemem Linux, systemie operacyjnym, blokowych urządzeniach, odbiorcach zasobów, konfiguracji i różnych dziennikach. Poniżej przedstawiono więcej informacji:

- System operacyjny
  - Dystrybucja i wersja systemu Linux
  - Informacje o jądrze
  - Informacje o sterowniku

- Sprzęt
  - Urządzenia PCI [ `*` ]

- Procesy i pamięć
  - Lista procesów (nazwa zadania, użyta pamięć, otwarte pliki)
  - Całkowita, dostępna i wolna pamięć fizyczna
  - Całkowita, dostępna i wolna pamięć wymiany
  - Profilowanie przechwytywania użycia procesora CPU i procesów w 5-sekundowych interwałach
  - Profilowanie przechwytywania procesów użycia pamięci w przedziale czasu 5 sekund

- Networking  
  - Lista kart sieciowych z statystykami kart
  - Tabela routingu sieciowego
  - Otwarte porty i stan

- Magazyn
  - Lista zablokowanych urządzeń
  - Lista partycji
  - Lista punktów instalacji
  - Informacje o woluminie MDADM
  - Informacje o woluminie LVM
  - Profilowanie przechwytywania na wszystkich dyskach w interwale 5-sekundowych

- Dzienniki
  - /var/log/messages
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/sysstat lub/var/log/sa [ `**` ]
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/Azure/[folder rozszerzenia]/ \* Dziennik\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Dane wyjściowe journalctl z ostatnich pięciu dni

- [Metadane wystąpienia maszyny wirtualnej platformy Azure](../windows/instance-metadata-service.md)

>[!Note]
>[ `*` ] Informacje o magistrali PCI nie zostały jeszcze zebrane w ramach dystrybucji Debian i SLES.
> 
>[ `**` ]/var/log/sysstat lub/var/log/sa zawiera pliki raportów działania systemu (SAR), które są zbierane przez pakiet sysstat. Jeśli na maszynie wirtualnej nie zainstalowano pakietu sysstat, narzędzie że program perfinsights zapewnia rekomendację do jej zainstalowania.

## <a name="run-the-perfinsights-linux-on-your-vm"></a>Uruchamianie że program perfinsights systemu Linux na maszynie wirtualnej

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Co muszę wiedzieć przed uruchomieniem narzędzia

#### <a name="tool-requirements"></a>Wymagania dotyczące narzędzi

- To narzędzie musi być uruchomione na maszynie wirtualnej, na której występuje problem z wydajnością.
- Na maszynie wirtualnej musi być zainstalowany język Python 3. x lub Python 2,7.

- Obecnie obsługiwane są następujące dystrybucje:

    | Dystrybucja               | Wersja                                         |
    |----------------------------|-------------------------------------------------|
    | Serwer Oracle Linux        | 6,10 [ `*` ], 7,3, 7,6, 7,5 |
    | CentOS                     | 6,5 [ `*` ], 7,6                                    |
    | RHEL                       | 7,6, 7,7, 7,8, 7,9                                |
    | Ubuntu                     | 14,04, 16,04, 18,04, 20,04                               |
    | Debian                     | 8, 9, 10 [ `*` ]                                    |
    | SLES                       | 12 SP4 [ `*` ]                                      |
    |                            |                                                   |

>[!Note]
>[ `*` ] Zapoznaj się z sekcją [znane problemy](#known-issues)

### <a name="known-issues"></a>Znane problemy

- RHEL 8 nie ma domyślnie zainstalowanego języka Python. Aby uruchomić system że program perfinsights Linux, musisz najpierw zainstalować środowisko Python 2,7

- Zbieranie informacji o agencie gościa może zakończyć się niepowodzeniem w witrynie CentOS 6. x

- Informacje o urządzeniach PCI nie są zbierane w ramach dystrybucji opartych na Debian

- Informacje LVM są częściowo zbierane w przypadku niektórych dystrybucji

### <a name="how-do-i-run-perfinsights"></a>Jak mogę Uruchom że program perfinsights

Możesz uruchomić że program perfinsights na maszynie wirtualnej, instalując diagnostykę wydajności Azure z Azure Portal. Można go również uruchomić jako autonomiczne narzędzie.

>[!Note]
>Że program perfinsights po prostu zbiera i analizuje dane. Nie wprowadza żadnych zmian w systemie.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Instalowanie i uruchamianie że program perfinsights z poziomu Azure Portal

Aby uzyskać więcej informacji na temat tej opcji, zobacz [Diagnostyka wydajności Azure](performance-diagnostics.md).  

#### <a name="run-perfinsights-in-standalone-mode"></a>Uruchom że program perfinsights w trybie autonomicznym

Aby uruchomić narzędzie że program perfinsights, wykonaj następujące kroki:

1. Pobierz [że program perfinsights. tar. gz](https://aka.ms/perfinsightslinuxdownload) do folderu na maszynie wirtualnej i Wyodrębnij zawartość przy użyciu poniższych poleceń z terminalu.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Przejdź do folderu, który zawiera `perfinsights.py` plik, a następnie uruchom polecenie, `perfinsights.py` Aby wyświetlić dostępne parametry wiersza polecenia.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Zrzut ekranu przedstawiający dane wyjściowe wiersza polecenia że program perfinsights systemu Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    Podstawowa składnia do uruchamiania scenariuszy że program perfinsights jest następująca:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    Możesz użyć poniższego przykładu, aby uruchomić scenariusz analizy szybkiej wydajności przez 1 minutę i utworzyć wyniki w folderze/tmp/Output:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    Możesz użyć poniższego przykładu, aby uruchomić scenariusz analizy wydajności dla 5 minut i przekazać wynikowe kulki do konta magazynu:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Przed uruchomieniem scenariusza że program perfinsights prosi użytkownika o zgodę na udostępnienie informacji diagnostycznych i zaakceptowanie umowy licencyjnej. Użyj opcji **-a lub--Accept-zrzeczenia i-share-Diagnostics** , aby pominąć te komunikaty.
    >
    >Jeśli masz aktywny bilet pomocy technicznej z firmą Microsoft i uruchomiono że program perfinsights na żądanie inżyniera pomocy technicznej, z którym pracujesz, upewnij się, że numer biletu pomocy technicznej jest używany przy użyciu opcji **-s lub--support-Request** .

Po zakończeniu przebiegu nowy plik tar pojawia się w tym samym folderze co że program perfinsights, chyba że zostanie określony folder wyjściowy. Nazwa pliku to **PerformanceDiagnostics \_ rrrr-mm-dd \_ hh-mm-SS-FFF. tar. gz.** Możesz wysłać ten plik do agenta pomocy technicznej na potrzeby analizy lub otworzyć raport w pliku, aby przejrzeć wyniki i zalecenia.

## <a name="review-the-diagnostics-report"></a>Przejrzyj raport diagnostyczny

W pliku **PerformanceDiagnostics \_ rrrr-mm-dd \_ hh-mm-SS-FFF. tar. gz** można znaleźć raport HTML, który zawiera szczegółowe informacje o wynikach że program perfinsights. Aby przejrzeć raport, rozwiń plik **PerformanceDiagnostics \_ rrrr-mm-dd \_ hh-mm-SS-FFF. tar. gz** , a następnie otwórz plik **że program perfinsights Report.html** .

### <a name="overview-tab"></a>Karta przegląd

Karta **Przegląd** zawiera podstawowe informacje dotyczące uruchamiania i maszyn wirtualnych. Karta **wyniki** zawiera podsumowanie zaleceń ze wszystkich różnych sekcji raportu że program perfinsights.

![Zrzut ekranu przedstawiający kartę Przegląd raportu że program perfinsights.](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Zrzut ekranu przedstawiający kartę systemu Linux raportu że program perfinsights.](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> Ustalenia sklasyfikowane jako duże są znanymi problemami, które mogą powodować problemy z wydajnością. Ustalenia sklasyfikowane jako średnie reprezentują nieoptymalne konfiguracje, które nie muszą powodować problemów z wydajnością. Ustalenia sklasyfikowane jako niskie są tylko w instrukcjach informacyjnych.

Przejrzyj zalecenia i linki dla wszystkich dużych i średnich wyników. Dowiedz się, jak mogą one wpływać na wydajność, a także najlepsze rozwiązania dotyczące konfiguracji zoptymalizowanych pod kątem wydajności.

### <a name="cpu-tab"></a>Karta CPU

Karta **CPU** zawiera informacje dotyczące użycia procesora CPU w całym systemie podczas uruchamiania że program perfinsights. Informacje dotyczące wysokich okresów użycia procesora CPU i najlepszych długotrwałych klientów korzystających z procesora CPU będą pomocne w rozwiązywaniu problemów związanych z PROCESORem.

![Zrzut ekranu przedstawiający kartę CPU raportu że program perfinsights](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Karta magazyn

Sekcja **spostrzeżenia** zawiera różne wyniki i zalecenia dotyczące magazynu.

**Urządzenia blokowe** i inne powiązane sekcje, takie **jak Partitions**, **LVM** i **MDADM** , opisują sposób, w jaki urządzenia blokujące są skonfigurowane i powiązane ze sobą.

![Zrzut ekranu karty magazyn](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Zrzut ekranu przedstawiający kartę MDADM](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Karta systemu Linux

Karta **Linux** zawiera informacje na temat sprzętu i systemu operacyjnego działającego na maszynie wirtualnej. Szczegóły obejmują listę uruchomionych procesów i informacji o agentach gościa, magistrali PCI, PROCESORAch, sterownikach i sterownikach LIS.

![Zrzut ekranu karty z systemem Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Następne kroki

Dzienniki diagnostyczne i raporty można przekazać do pomoc techniczna firmy Microsoft do dalszej analizy. Podczas pracy z pracownikami pomoc techniczna firmy Microsoft mogą zażądać przesyłania danych wyjściowych generowanych przez że program perfinsights w celu uzyskania pomocy w procesie rozwiązywania problemów.

Poniższy zrzut ekranu przedstawia komunikat podobny do następującego:

![Zrzut ekranu przedstawiający przykładowy komunikat z pomoc techniczna firmy Microsoft](media/how-to-use-perfinsights-linux/support-email.png)

Postępuj zgodnie z instrukcjami wyświetlanymi w komunikacie, aby uzyskać dostęp do obszaru roboczego transferu plików. Aby zwiększyć bezpieczeństwo, należy zmienić hasło przy pierwszym użyciu.

Po zalogowaniu zostanie wyświetlone okno dialogowe z przekazaniem pliku **PerformanceDiagnostics \_ rrrr-mm-dd \_ hh-mm-SS-FFF. tar. gz** , który został zebrany przez że program perfinsights.
