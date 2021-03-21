---
title: Wymagania systemowe serii StorSimple 8000 | Microsoft Docs
description: Zawiera opis wymagań dotyczących oprogramowania, sieci i wysokiej dostępności oraz najlepszych rozwiązań dla Microsoft Azure StorSimple rozwiązania.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: fa7616a740e8246fa08e950494428095f41ee404
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382858"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>Oprogramowanie StorSimple 8000 Series, wysoka dostępność i wymagania dotyczące sieci

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Witamy w Microsoft Azure StorSimple. W tym artykule opisano ważne wymagania systemowe i najlepsze rozwiązania dotyczące urządzenia StorSimple oraz dla klientów magazynu uzyskujących dostęp do urządzenia. Zalecamy dokładne zapoznanie się z informacjami przed wdrożeniem systemu StorSimple, a następnie odwoływanie się do niego w razie potrzeby podczas wdrażania i późniejszej operacji.

Wymagania systemowe obejmują:

* **Wymagania dotyczące oprogramowania dla klientów magazynu** — opisuje obsługiwane systemy operacyjne i dodatkowe wymagania dotyczące tych systemów operacyjnych.
* **Wymagania dotyczące sieci dla urządzenia StorSimple** — zawiera informacje o portach, które muszą być otwarte w zaporze, aby umożliwić obsługę ruchu iSCSI, chmury lub zarządzania.
* **Wymagania dotyczące wysokiej dostępności dla programu StorSimple** — opisuje wymagania dotyczące wysokiej dostępności i najlepszych rozwiązań dla urządzenia StorSimple i komputera-hosta.

## <a name="software-requirements-for-storage-clients"></a>Wymagania dotyczące oprogramowania dla klientów magazynu

Poniższe wymagania dotyczące oprogramowania dotyczą klientów magazynu, którzy uzyskują dostęp do urządzenia z systemem StorSimple.

| Obsługiwane systemy operacyjne | Wymagana wersja | Dodatkowe wymagania/uwagi |
| --- | --- | --- |
| Windows Server |2008 R2 Z DODATKIEM SP1, 2012, 2012 R2, 2016 |StorSimple woluminów iSCSI są obsługiwane tylko na następujących typach dysków systemu Windows:<ul><li>Wolumin prosty na dysku podstawowym</li><li>Wolumin prosty i dublowany na dysku dynamicznym</li></ul>Obsługiwane są tylko inicjatory iSCSI oprogramowania obecne w systemie operacyjnym. Sprzętowe inicjatory iSCSI nie są obsługiwane.<br></br>W przypadku korzystania z StorSimple woluminu iSCSI obsługiwane są funkcje alokowania elastycznego i ODX systemu Windows Server 2012 i 2016.<br><br>StorSimple mogą tworzyć woluminy alokowane elastycznie i w pełni inicjowane. Nie można utworzyć woluminów częściowo zainicjowanych.<br><br>Ponowne formatowanie woluminu alokowanego elastycznie może zająć dużo czasu. Zalecamy usunięcie woluminu, a następnie utworzenie nowego, a nie ponowne formatowanie. Jeśli jednak wolisz ponownie sformatować wolumin:<ul><li>Przed ponownym formatowaniem uruchom następujące polecenie, aby uniknąć opóźnień odzyskiwania miejsca:  <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Po zakończeniu formatowania użyj następującego polecenia, aby ponownie włączyć odzyskiwanie miejsca: <br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Zastosuj poprawkę systemu Windows Server 2012, zgodnie z opisem w artykule [KB 2878635](https://support.microsoft.com/kb/2870270) na komputerze z systemem Windows Server.</li></ul></li></ul></ul> Jeśli konfigurujesz StorSimple Snapshot Manager lub StorSimple adapter dla programu SharePoint, przejdź do pozycji [wymagania dotyczące oprogramowania dla składników opcjonalnych](#software-requirements-for-optional-components). <br> Jeśli klient systemu Windows Server korzysta z protokołu SMB w celu uzyskania dostępu do urządzenia StorSimple, przejdź do [dostrajania wydajności dla serwerów plików SMB](/windows-server/administration/performance-tuning/role/file-server/smb-file-server) , aby uzyskać wskazówki dotyczące zwiększania przetwarzania równoległego.|
| VMware ESX |5,5 i 6,0 |Obsługiwane w przypadku VMware vSphere jako klienta iSCSI. Funkcja VAAI-Block jest obsługiwana w VMware vSphere na urządzeniach StorSimple. |
| Linux RHEL/CentOS |5, 6 i 7 |Obsługa klientów iSCSI z systemem Linux z użyciem inicjatora Open-iSCSI w wersji 5, 6 i 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> Program IBM AIX nie jest obecnie obsługiwany w programie StorSimple.


## <a name="software-requirements-for-optional-components"></a>Wymagania dotyczące oprogramowania dla składników opcjonalnych

Poniższe wymagania dotyczące oprogramowania dotyczą opcjonalnych składników StorSimple (StorSimple Snapshot Manager i StorSimple adapter for SharePoint).

| Składnik | Platforma hosta | Dodatkowe wymagania/uwagi |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 z dodatkiem SP1, 2012, 2012 R2 |Korzystanie z StorSimple Snapshot Manager w systemie Windows Server jest wymagane do tworzenia kopii zapasowych/przywracania dublowanych dysków dynamicznych oraz wszelkich kopii zapasowych spójnych na poziomie aplikacji.<br> StorSimple Snapshot Manager jest obsługiwana tylko w systemie Windows Server 2008 R2 z dodatkiem SP1 (64-bitowym), Windows Server 2012 R2 i Windows Server 2012.<ul><li>W przypadku korzystania z systemu Windows Server 2012 przed zainstalowaniem StorSimple Snapshot Manager należy zainstalować program .NET 3.5 – 4.5.</li><li>Jeśli używasz systemu Windows Server 2008 R2 z dodatkiem SP1, przed zainstalowaniem StorSimple Snapshot Manager należy zainstalować program Windows Management Framework 3,0.</li></ul> |
| Adapter usługi StorSimple dla programu SharePoint |Windows Server 2008 R2 z dodatkiem SP1, 2012, 2012 R2 |<ul><li>Adapter StorSimple dla programu SharePoint jest obsługiwany tylko w programach SharePoint 2010 i SharePoint 2013.</li><li>Struktura SPZ wymaga wersji SQL Server Enterprise w wersji 2008 R2 lub 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Wymagania sieciowe dotyczące urządzenia StorSimple

Urządzenie StorSimple jest urządzeniem zablokowanym. Należy jednak otworzyć porty w zaporze, aby umożliwić obsługę ruchu iSCSI, chmury i zarządzania. Poniższa tabela zawiera listę portów, które należy otworzyć w zaporze. W tej tabeli *w* lub *przychodzący* odnosi się do kierunku, w którym przychodzące żądania klienta uzyskują dostęp do urządzenia. *Out* lub *wychodzący* odnosi się do kierunku, w którym Urządzenie StorSimple wysyła dane zewnętrznie, poza wdrożeniem: na przykład, ruch wychodzący do Internetu.

| Numer portu<sup>1, 2</sup> | Do lub do zewnątrz | Zakres portów | Wymagane | Uwagi |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Out |Sieć WAN |Nie |<ul><li>Port wychodzący jest używany na potrzeby dostępu do Internetu w celu pobierania aktualizacji.</li><li>Wychodzący serwer proxy sieci Web jest konfigurowany przez użytkownika.</li><li>Aby zezwolić na aktualizacje systemu, ten port musi być również otwarty dla stałych adresów IP kontrolera.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Out |Sieć WAN |Tak |<ul><li>Port wychodzący służy do uzyskiwania dostępu do danych w chmurze.</li><li>Wychodzący serwer proxy sieci Web jest konfigurowany przez użytkownika.</li><li>Aby zezwolić na aktualizacje systemu, ten port musi być również otwarty dla stałych adresów IP kontrolera.</li><li>Ten port jest również używany na kontrolerach do wyrzucania elementów bezużytecznych.</li></ul> |
| UDP 53 (DNS) |Out |Sieć WAN |W niektórych przypadkach: Zobacz uwagi. |Ten port jest wymagany tylko wtedy, gdy jest używany internetowy serwer DNS. |
| UDP 123 (NTP) |Out |Sieć WAN |W niektórych przypadkach: Zobacz uwagi. |Ten port jest wymagany tylko wtedy, gdy jest używany internetowy serwer NTP. |
| TCP 9354 |Out |Sieć WAN |Tak |Port wychodzący jest używany przez urządzenie StorSimple do komunikowania się z usługą StorSimple Device Manager. |
| 3260 (iSCSI) |W |Sieć LAN |Nie |Ten port służy do uzyskiwania dostępu do danych za pośrednictwem protokołu iSCSI. |
| 5985 |W |Sieć LAN |Nie |Port wejściowy jest używany przez StorSimple Snapshot Manager do komunikowania się z urządzeniem StorSimple.<br>Ten port jest również używany podczas zdalnego łączenia się z program Windows PowerShell dla usługi StorSimple za pośrednictwem protokołu HTTP. |
| 5986 |W |Sieć LAN |Nie |Ten port jest używany podczas zdalnego łączenia się z program Windows PowerShell dla usługi StorSimple za pośrednictwem protokołu HTTPS. |

<sup>1</sup> w publicznym Internecie nie trzeba otwierać żadnych portów przychodzących.

<sup>2</sup> Jeśli wiele portów przeniesie konfigurację bramy, przychodząca kolejność ruchu kierowanego jest określana na podstawie kolejności routingu portów opisanej w obszarze [Routing portów](#routing-metric)poniżej.

<sup>3</sup> stałe adresy IP kontrolera na urządzeniu StorSimple muszą być trasowane i mogły łączyć się z Internetem bezpośrednio lub za pośrednictwem skonfigurowanego serwera proxy sieci Web. Stałe adresy IP są używane do obsługi aktualizacji urządzenia i wyrzucania elementów bezużytecznych. Jeśli kontrolery urządzeń nie mogą połączyć się z Internetem za pośrednictwem stałych adresów IP, nie będzie można zaktualizować urządzenia StorSimple i wyrzucania elementów bezużytecznych nie będzie działać prawidłowo.

> [!IMPORTANT]
> Upewnij się, że Zapora nie modyfikuje ani nie odszyfrowuje żadnego ruchu TLS między urządzeniem StorSimple i platformą Azure.


### <a name="url-patterns-for-firewall-rules"></a>Wzorce adresów URL dla reguł zapory

Administratorzy sieci mogą często konfigurować zaawansowane reguły zapory na podstawie wzorców adresów URL, aby filtrować ruch przychodzący i wychodzący. Urządzenie StorSimple i Usługa StorSimple Device Manager są zależne od innych aplikacji firmy Microsoft, takich jak Azure Service Bus, Azure Active Directory Access Control, konta magazynu i serwery Microsoft Update. Wzorce adresów URL skojarzone z tymi aplikacjami mogą służyć do konfigurowania reguł zapory. Ważne jest, aby zrozumieć, że wzorce adresów URL skojarzone z tymi aplikacjami mogą się zmieniać. To z kolei wymaga od administratora sieci monitorowania i aktualizowania reguł zapory dla StorSimple, jak i w razie potrzeby.

Zalecamy ustawienie reguł zapory dla ruchu wychodzącego na podstawie stałych adresów IP StorSimple, w większości przypadków. Można jednak użyć poniższych informacji, aby ustawić zaawansowane reguły zapory, które są potrzebne do tworzenia bezpiecznych środowisk.

> [!NOTE]
> Adresy IP urządzeń (źródłowych) powinny zawsze być ustawione na wszystkie włączone interfejsy sieciowe. Docelowe adresy IP powinny być ustawiane na [zakresy adresów IPv4 centrum danych platformy Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>Wzorce adresów URL dla Azure Portal

| Wzorzec adresu URL | Składnik/funkcjonalność | Adresy IP urządzeń |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |Usługa Menedżer urządzeń StorSimple<br>Access Control Service<br>Usługa Azure Service Bus<br>Usługa uwierzytelniania |Interfejsy sieciowe obsługujące chmurę |
| `https://*.backup.windowsazure.com` |Rejestracja urządzenia |Tylko dane 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odwoływanie certyfikatów |Interfejsy sieciowe obsługujące chmurę |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Konta usługi Azure Storage i monitorowanie |Interfejsy sieciowe obsługujące chmurę |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Serwery Microsoft Update<br> |Tylko stałe adresy IP kontrolera |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Tylko stałe adresy IP kontrolera |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pakiet pomocy technicznej |Interfejsy sieciowe obsługujące chmurę |

#### <a name="url-patterns-for-azure-government-portal"></a>Wzorce adresów URL dla portalu Azure Government

| Wzorzec adresu URL | Składnik/funkcjonalność | Adresy IP urządzeń |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |Usługa Menedżer urządzeń StorSimple<br>Access Control Service<br>Usługa Azure Service Bus<br>Usługa uwierzytelniania |Interfejsy sieciowe obsługujące chmurę |
| `https://*.backup.windowsazure.us` |Rejestracja urządzenia |Tylko dane 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odwoływanie certyfikatów |Interfejsy sieciowe obsługujące chmurę |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Konta usługi Azure Storage i monitorowanie |Interfejsy sieciowe obsługujące chmurę |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Serwery Microsoft Update<br> |Tylko stałe adresy IP kontrolera |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Tylko stałe adresy IP kontrolera |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pakiet pomocy technicznej |Interfejsy sieciowe obsługujące chmurę |

### <a name="routing-metric"></a>Metryka routingu

Metryka routingu jest skojarzona z interfejsami i bramą, która kieruje dane do określonych sieci. Metryka routingu jest używana przez protokół routingu do obliczania najlepszej ścieżki do danego miejsca docelowego, jeśli poznanie wielu ścieżek istnieje w tym samym miejscu docelowym. Im niższa jest Metryka routingu, tym wyższy poziom preferencji.

W kontekście StorSimple, jeśli skonfigurowano wiele interfejsów sieciowych i bram do ruchu sieciowego, metryki routingu będą odtwarzane w celu określenia względnej kolejności, w której będą używane interfejsy. Metryki routingu nie mogą być zmieniane przez użytkownika. Można jednak użyć `Get-HcsRoutingTable` polecenia cmdlet do drukowania tabeli routingu (i metryk) na urządzeniu StorSimple. Więcej informacji na temat polecenia cmdlet Get-HcsRoutingTable w [rozwiązywaniu problemów z wdrażaniem StorSimple](./storsimple-8000-troubleshoot-deployment.md).

Algorytm metryk routingu używany do aktualizacji Update 2 i nowszych można wyjaśnić w następujący sposób.

* Zestaw wstępnie zdefiniowanych wartości został przypisany do interfejsów sieciowych.
* Rozważmy przykładową tabelę pokazaną poniżej z wartościami przypisanymi do różnych interfejsów sieciowych, gdy są one włączone w chmurze lub w chmurze, ale ze skonfigurowaną bramą. Należy pamiętać, że wartości przypisane tutaj są tylko przykładowymi wartościami.

    | Interfejs sieciowy | Obsługa chmury | Chmura wyłączona z bramą |
    |-----|---------------|---------------------------|
    | Dane 0  | 1            | -                        |
    | Dane 1  | 2            | 20                       |
    | Dane 2  | 3            | 30                       |
    | Dane 3  | 4            | 40                       |
    | Dane 4  | 5            | 50                       |
    | Dane 5  | 6            | 60                       |


* Kolejność, w jakiej ruch w chmurze będzie kierowany przez interfejsy sieciowe, to:
  
    *Data 0 > Data 1 > Data 2 > dane 3 > Data 4 > Data 5*
  
    Można to wyjaśnić przy użyciu poniższego przykładu.
  
    Rozważ użycie urządzenia StorSimple z dwoma interfejsami sieciowymi z obsługą chmury, danymi 0 i datami 5. Dane 1 za pomocą danych 4 są wyłączone w chmurze, ale skonfigurowano bramę. Kolejność, w której będzie kierowany ruch dla tego urządzenia, będzie:
  
    *Data 0 (1) > Data 5 (6) > dane 1 (20) > dane 2 (30) > Data 3 (40) > Data 4 (50)*
  
    *Liczby w nawiasach oznaczają odpowiednie metryki routingu.*
  
    Jeśli data 0 nie powiedzie się, ruch w chmurze zostanie rozesłany przez dane 5. Jeśli Brama jest skonfigurowana w całej innej sieci, a dane 0 i 5 były niepowodzeniem, ruch w chmurze przejdzie przez dane 1.
* Jeśli interfejs sieciowy obsługujący chmurę zakończy się niepowodzeniem, to 3 ponawianie prób przy użyciu 30-sekundowego opóźnienia do nawiązania połączenia z interfejsem. Jeśli wszystkie próby nie powiodą się, ruch jest kierowany do następnego dostępnego interfejsu z obsługą chmury zgodnie z opisem w tabeli routingu. Jeśli wszystkie interfejsy sieciowe z obsługą chmury zakończą się niepowodzeniem, urządzenie przejdzie w tryb failover do innego kontrolera (w tym przypadku nie zostanie uruchomiony ponowny rozruch).
* Jeśli wystąpi błąd VIP dla interfejsu sieciowego z obsługą iSCSI, nastąpi 3 ponowne próby z opóźnieniem 2 sekund. Takie zachowanie jest takie samo jak w poprzednich wersjach. Jeśli wszystkie interfejsy sieciowe iSCSI zakończą się niepowodzeniem, nastąpi przejście do trybu failover kontrolera (wraz z ponownym uruchomieniem).
* Alert jest również uruchamiany na urządzeniu z systemem StorSimple, gdy wystąpi błąd adresu VIP. Aby uzyskać więcej informacji, przejdź do [szybkiego odwołania do alertu](storsimple-8000-manage-alerts.md).
* W przypadku ponownych prób Usługa iSCSI będzie mieć pierwszeństwo przed chmurą.
  
    Rozważmy następujący przykład: Urządzenie StorSimple ma włączone dwa interfejsy sieciowe, dane 0 i Data 1. Data 0 jest włączona w chmurze, a Data 1 to zarówno w chmurze, jak i iSCSI. Żadne inne interfejsy sieciowe na tym urządzeniu nie są włączone dla chmury lub iSCSI.
  
    Jeśli dane 1 nie powiedzie się, na przykład gdy jest to ostatni interfejs sieciowy iSCSI, spowoduje to przejście w tryb failover kontrolera do danych 1 na drugim kontrolerze.

### <a name="networking-best-practices"></a>Najlepsze rozwiązania dotyczące sieci

Oprócz wymagań dotyczących sieci w celu uzyskania optymalnej wydajności rozwiązania StorSimple należy przestrzegać następujących najlepszych rozwiązań:

* Upewnij się, że urządzenie StorSimple ma dedykowaną przepustowość 40 MB/s (lub więcej). Ta przepustowość nie powinna być udostępniana (lub Alokacja powinna być gwarantowana przy użyciu zasad QoS) z innymi aplikacjami.
* Upewnij się, że połączenie sieciowe z Internetem jest dostępne przez cały czas. Sporadyczne lub zawodne połączenia internetowe z urządzeniami, w tym bez łączności z Internetem, spowodują nieobsługiwaną konfigurację.
* Izoluj ruch iSCSI i w chmurze, korzystając z dedykowanych interfejsów sieciowych na urządzeniu na potrzeby dostępu iSCSI i w chmurze. Aby uzyskać więcej informacji, zobacz jak [modyfikować interfejsy sieciowe](storsimple-8000-modify-device-config.md#modify-network-interfaces) na urządzeniu StorSimple.
* Nie należy używać konfiguracji linku protokołu kontroli agregacji (LACP) dla interfejsów sieciowych. Taka konfiguracja nie jest obsługiwana.

## <a name="high-availability-requirements-for-storsimple"></a>Wymagania dotyczące wysokiej dostępności dla StorSimple

Platforma sprzętowa dołączona do rozwiązania StorSimple ma funkcje dostępności i niezawodności, które stanowią podstawę dla infrastruktury magazynu o wysokiej dostępności, która jest odporna na uszkodzenia w centrum danych. Istnieją jednak wymagania i najlepsze rozwiązania, które należy spełnić, aby zapewnić dostępność rozwiązania StorSimple. Przed wdrożeniem StorSimple należy uważnie zapoznać się z następującymi wymaganiami i najlepszymi rozwiązaniami dotyczącymi urządzeń StorSimple i podłączonych komputerów hosta.

Aby uzyskać więcej informacji na temat monitorowania i konserwowania składników sprzętowych urządzenia StorSimple, przejdź do pozycji [Korzystanie z usługi StorSimple Device Manager do monitorowania składników sprzętowych i stanu](storsimple-8000-monitor-hardware-status.md) i [StorSimple składników sprzętowych](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Wymagania i procedury wysokiej dostępności dla urządzenia StorSimple

Uważnie Przejrzyj poniższe informacje, aby zapewnić wysoką dostępność urządzenia StorSimple.

#### <a name="pcms"></a>PCMs

Urządzenia StorSimple obejmują nadmiarowe, z możliwością wymiany i moduły chłodzenia (PCMs). Każdy moduł PCM ma wystarczającą pojemność, aby zapewnić usługę dla całej obudowy. Aby zapewnić wysoką dostępność, należy zainstalować obie PCMs.

* Połącz PCMs z różnymi źródłami zasilanymi, aby zapewnić dostępność, jeśli źródło nie powiedzie się.
* W przypadku niepowodzenia modułu PCM należy natychmiast zażądać zamiany.
* Usuń moduł PCM zakończony niepowodzeniem tylko wtedy, gdy zastąpisz i jest gotowy do instalacji.
* Nie usuwaj jednocześnie obu PCMs. Moduł PCM zawiera moduł baterii tworzenia kopii zapasowych. Usunięcie obu PCMs spowoduje wyłączenie ochrony baterii, a stan urządzenia nie zostanie zapisany. Aby uzyskać więcej informacji na temat baterii, przejdź do pozycji [Obsługa zapasowego modułu pracy](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module)awaryjnej.

#### <a name="controller-modules"></a>Moduły kontrolera

Urządzenia StorSimple obejmują nadmiarowe, wymienialne moduły kontrolerów. Moduły kontrolera działają w sposób aktywny/pasywny. W dowolnym momencie jeden moduł kontrolera jest aktywny i udostępnia usługę, podczas gdy drugi moduł kontrolera jest pasywny. Moduł kontrolera pasywnego jest włączony i stanie się działać w przypadku niepowodzenia lub usunięcia modułu aktywnego kontrolera. Każdy moduł kontrolera ma wystarczającą pojemność, aby zapewnić usługę dla całej obudowy. Oba moduły kontrolera muszą być zainstalowane, aby zapewnić wysoką dostępność.

* Upewnij się, że oba moduły kontrolera są zainstalowane przez cały czas.
* W przypadku niepowodzenia modułu kontrolera należy natychmiast zażądać zamiany.
* Usuń moduł kontrolera zakończonego niepowodzeniem tylko wtedy, gdy zastąpisz i jest gotowy do instalacji. Usunięcie modułu dla rozszerzonych okresów wpłynie na przepływ powietrza, a tym samym chłodzenie systemu.
* Upewnij się, że połączenia sieciowe do obu modułów kontrolera są identyczne, a podłączone interfejsy sieciowe mają identyczną konfigurację sieci.
* Jeśli moduł kontrolera ulegnie awarii lub wymaga zastąpienia, upewnij się, że inny moduł kontrolera jest w stanie aktywnym przed zastąpieniem modułu, który uległ awarii. Aby sprawdzić, czy kontroler jest aktywny, przejdź do pozycji [Identyfikowanie aktywnego kontrolera na urządzeniu](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Nie należy usuwać obu modułów kontrolera jednocześnie. Jeśli przełączenie kontrolera w tryb failover jest w toku, nie zamykaj modułu stanu gotowości lub usuń go z obudowy.
* Po przejściu do trybu failover kontrolera odczekaj co najmniej pięć minut przed usunięciem dowolnego modułu kontrolera.

#### <a name="network-interfaces"></a>Interfejsy sieciowe

W przypadku modułów kontrolera urządzenia StorSimple każdy ma 4 1 Gigabit i 2 10 Gigabit interfejsów sieciowych Ethernet.

* Upewnij się, że połączenia sieciowe do obu modułów kontrolera są identyczne, a interfejsy sieciowe, które są podłączone do interfejsów modułu kontrolera, mają identyczną konfigurację sieci.
* Jeśli to możliwe, należy wdrożyć połączenia sieciowe między różnymi przełącznikami, aby zapewnić dostępność usługi w przypadku awarii urządzenia sieciowego.
* Podczas odłączania tylko lub ostatniego pozostałego interfejsu iSCSI (z przypisanymi adresami IP) należy najpierw wyłączyć interfejs, a następnie odłączyć kable. Jeśli interfejs zostanie odłączony jako pierwszy, spowoduje to przełączenie aktywnego kontrolera do trybu failover na kontrolerze pasywnym. Jeśli kontroler pasywny ma także powiązane z nią interfejsy, oba te kontrolery zostaną przełączone wielokrotnie przed rozliczeniem na jednym kontrolerze.
* Połącz co najmniej dwa interfejsy danych z siecią z każdego modułu kontrolera.
* Jeśli włączono interfejsy 2 10 GbE, wdróż je w różnych przełącznikach.
* Jeśli to możliwe, użyj funkcji MPIO na serwerach, aby upewnić się, że serwery mogą tolerować awarie łącza, sieci lub interfejsu.

Aby uzyskać więcej informacji na temat sieci urządzenia pod kątem wysokiej dostępności i wydajności, przejdź do [instalacji urządzenia z StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) lub [zainstaluj urządzenie StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>Dysków SSD i HDD

Urządzenia StorSimple obejmują dyski półprzewodnikowe (dysków SSD) i dyski twarde (HDD), które są chronione za pomocą spacji dublowanych. Użycie spacji dublowanych zapewnia, że urządzenie może tolerować awarię co najmniej jednego dysków SSD lub HDD.

* Upewnij się, że wszystkie moduły dysków SSD i dysków twardych są zainstalowane.
* Jeśli dysk SSD lub dysk twardy ulegnie awarii, zażądaj natychmiastowego zastępowania.
* Jeśli dysk SSD lub dysk twardy ulegnie awarii lub wymaga zastąpienia, należy usunąć tylko dysk SSD lub dysk twardy wymagający zamiany.
* Nie usuwaj więcej niż jednego SSD lub dysku twardego z systemu w dowolnym momencie.
  Awaria 2 lub większa liczba dysków pewnego typu (dysk twardy, SSD) lub kolejne awarie w krótkim czasie może skutkować awarią systemu i potencjalną utratą danych. W takim przypadku [skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) , aby uzyskać pomoc.
* Podczas zamiany należy monitorować **składniki udostępnione** w bloku **kondycja sprzętu** dla dysków w dysków SSD i HDD. Zielony wskaźnik wyboru wskazuje, że dyski są w dobrej kondycji, a czerwony wykrzyknik wskazuje na uszkodzony dysk SSD lub dysk twardy.
* Zalecamy skonfigurowanie migawek w chmurze dla wszystkich woluminów, które należy chronić w przypadku awarii systemu.

#### <a name="ebod-enclosure"></a>Obudowa EBOD

Model urządzenia StorSimple 8600 zawiera rozszerzoną część obudowy dysków (EBOD) oprócz obudowy podstawowej. EBOD zawiera kontrolery EBOD i dyski twarde (HDD), które są chronione za pomocą spacji dublowanych. Użycie funkcji miejsca dublowane zapewnia, że urządzenie może tolerować awarię co najmniej jednego HDD. Obudowa EBOD jest połączona z obudową podstawową za pomocą nadmiarowych kabli SAS.

* Upewnij się, że zarówno moduły EBOD, jak i wszystkie stacje dysków twardych są zainstalowane przez cały czas.
* Jeśli moduł kontrolera obudów EBOD nie powiedzie się, zażądaj natychmiastowego zastępowania.
* Jeśli moduł kontrolera obudów EBOD kończy się niepowodzeniem, upewnij się, że inny moduł kontrolera jest aktywny przed zastąpieniem modułu, który się nie powiódł. Aby sprawdzić, czy kontroler jest aktywny, przejdź do pozycji [Identyfikowanie aktywnego kontrolera na urządzeniu](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Podczas zastępowania modułu kontrolera EBOD w sposób ciągły monitoruje stan składnika w usłudze StorSimple Device Manager, uzyskując dostęp do **monitorowania**  >  **kondycji sprzętu**.
* Jeśli kabel SAS ulegnie awarii lub wymaga zastąpienia (pomoc techniczna firmy Microsoft powinien zostać użyty do dokonania takiego ustalenia), pamiętaj, aby usunąć tylko kabel SAS wymagający zamiany.
* Nie usuwaj równocześnie obu kabli SAS z systemu w dowolnym momencie.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Zalecenia dotyczące wysokiej dostępności dla komputerów-hostów

Uważnie zapoznaj się z najlepszymi rozwiązaniami, aby zapewnić wysoką dostępność hostów podłączonych do urządzenia StorSimple.

* Skonfiguruj StorSimple z [konfiguracją klastra serwera plików z dwoma węzłami][1]. Dzięki usunięciu pojedynczych punktów awarii i utworzeniu nadmiarowości na stronie hosta całe rozwiązanie stanie się wysoce dostępne.
* Użyj stale dostępnych udziałów (CA) dostępnych w systemie Windows Server 2012 (SMB 3,0) w celu zapewnienia wysokiej dostępności podczas pracy w trybie failover kontrolerów magazynu. Aby uzyskać dodatkowe informacje na temat konfigurowania klastrów serwerów plików i ciągle dostępnych udziałów w systemie Windows Server 2012, zobacz ten [pokaz wideo](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat limitów systemu StorSimple](storsimple-8000-limits.md).
* [Dowiedz się, jak wdrożyć rozwiązanie StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731844(v=ws.10)