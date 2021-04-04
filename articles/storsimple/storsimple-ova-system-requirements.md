---
title: Microsoft Azure StorSimple wymagania systemowe macierzy wirtualnej
description: Dowiedz się więcej o wymaganiach dotyczących oprogramowania i sieci dla macierzy wirtualnej StorSimple
author: alkohli
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 5970e82619667a47ba160c84df2cdeb145b0dab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966177"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Wymagania systemowe macierzy wirtualnej StorSimple

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Omówienie

W tym artykule opisano ważne wymagania systemowe dla Microsoft Azure StorSimple macierzy wirtualnej oraz dla klientów magazynu uzyskujących dostęp do macierzy. Zalecamy dokładne zapoznanie się z informacjami przed wdrożeniem systemu StorSimple, a następnie odwoływanie się do niego w razie potrzeby podczas wdrażania i późniejszej operacji.

Wymagania systemowe obejmują:

* **Wymagania dotyczące oprogramowania dla klientów magazynu** — zawiera opis obsługiwanych platform wirtualizacji, przeglądarek sieci Web, inicjatorów iSCSI, klientów SMB, minimalnych wymagań dotyczących urządzeń wirtualnych oraz wszelkich dodatkowych wymagań dla tych systemów operacyjnych.
* **Wymagania dotyczące sieci dla urządzenia StorSimple** — zawiera informacje o portach, które muszą być otwarte w zaporze, aby umożliwić obsługę ruchu iSCSI, chmury lub zarządzania.

Informacje o wymaganiach systemowych StorSimple opublikowanych w tym artykule mają zastosowanie tylko do tablic wirtualnych StorSimple.

* W przypadku urządzeń z serii 8000 przejdź do [wymagań systemowych dla urządzenia z serii StorSimple 8000](./storsimple-8000-system-requirements.md).
* W przypadku urządzeń z serii 7000 przejdź do [wymagań systemowych dla urządzenia z serii StorSimple 5000-7000](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Wymagania dotyczące oprogramowania
Wymagania dotyczące oprogramowania obejmują informacje dotyczące obsługiwanych przeglądarek sieci Web, wersji protokołu SMB, platform wirtualizacji i minimalnych wymagań dotyczących urządzeń wirtualnych.

### <a name="supported-virtualization-platforms"></a>Obsługiwane platformy wirtualizacji
| **Funkcja hypervisor** | **Wersja** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 z dodatkiem SP1 lub nowszym |
| VMware ESXi |5,0, 5,5, 6,0 i 6,5. |

> [!IMPORTANT]
> Nie instaluj narzędzi VMware w macierzy wirtualnej StorSimple; spowoduje to nieobsługiwaną konfigurację.

### <a name="virtual-device-requirements"></a>Wymagania dotyczące urządzeń wirtualnych
| **Składnik** | **Wymaganie** |
| --- | --- |
| Minimalna liczba procesorów wirtualnych (rdzenie) |4 |
| Minimalna ilość pamięci (RAM) |8 GB <br> Dla serwera plików 8 GB dla mniej niż 2 000 000 plików i 16 GB dla 2-4 milionów plików|
| Miejsce na dysku<sup>1</sup> |Dysk systemu operacyjnego — 80 GB <br></br>Dysk danych — 500 GB do 8 TB |
| Minimalna liczba interfejsów sieciowych |1 |
| Przepustowość internetowa<sup>2</sup> |Wymagana minimalna przepustowość: 5 MB/s <br> Zalecana przepustowość: 100 MB/s <br> Szybkość transferu danych jest skalowana przy użyciu przepustowości Internetu. Na przykład 100 GB danych trwa 2 dni, aby przetransferować o 5 MB/s, co może prowadzić do błędów kopii zapasowych, ponieważ codzienne kopie zapasowe nie zostaną wykonane w ciągu dnia. Przepustowość 100 MB/s, 100 GB danych można przenieść w ciągu 2,5 godzin.   |

<sup>1</sup> — alokowane elastycznie

<sup>2</sup> — wymagania dotyczące sieci mogą się różnić w zależności od dziennego współczynnika zmian danych. Na przykład jeśli urządzenie musi utworzyć kopię zapasową 10 GB lub więcej zmian w ciągu dnia, codzienne wykonywanie kopii zapasowej za pośrednictwem 5 MB/s może trwać do 4,25 godzin (jeśli nie można skompresować lub deduplikowania danych).

### <a name="supported-web-browsers"></a>Obsługiwane przeglądarki sieci Web
| **Składnik** | **Wersja** | **Dodatkowe wymagania/uwagi** |
| --- | --- | --- |
| Microsoft Edge |Najnowsza wersja | |
| Internet Explorer |Najnowsza wersja |Testowanie przy użyciu programu Internet Explorer 11 |
| Google Chrome |Najnowsza wersja |Testowanie przy użyciu programu Chrome 46 |

### <a name="supported-storage-clients"></a>Obsługiwani klienci pamięci masowej
Poniższe wymagania dotyczące oprogramowania dotyczą inicjatorów iSCSI, które uzyskują dostęp do macierzy wirtualnej StorSimple (skonfigurowany jako serwer iSCSI).

| **Obsługiwane systemy operacyjne** | **Wymagana wersja** | **Dodatkowe wymagania/uwagi** |
| --- | --- | --- |
| Windows Server |2008R2 Z DODATKIEM SP1, 2012, 2012R2 |StorSimple mogą tworzyć woluminy alokowane elastycznie i w pełni inicjowane. Nie można utworzyć woluminów częściowo zainicjowanych. Woluminy iSCSI StorSimple są obsługiwane tylko w programie: <ul><li>Proste woluminy na dyskach podstawowych systemu Windows.</li><li>System Windows NTFS służący do formatowania woluminu.</li> |

Poniższe wymagania dotyczące oprogramowania dotyczą klientów SMB, którzy uzyskują dostęp do macierzy wirtualnej StorSimple (skonfigurowany jako serwer plików).

| **Wersja protokołu SMB** |
| --- |
| SMB 2. x |
| SMB 3.0 |
| PROTOKÓŁ SMB 3,02 |

> [!IMPORTANT]
> Nie należy kopiować ani przechowywać plików chronionych przez system Windows system szyfrowania plików (EFS) na serwerze plików macierzy wirtualnej StorSimple. spowoduje to nieobsługiwaną konfigurację.


### <a name="supported-storage-format"></a>Obsługiwany format magazynu
Obsługiwany jest tylko magazyn blokowych obiektów blob platformy Azure. Stronicowe obiekty blob nie są obsługiwane. Więcej informacji na [temat blokowych obiektów blob i stronicowych obiektów BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Wymagania dotyczące sieci
Poniższa tabela zawiera listę portów, które należy otworzyć w zaporze, aby umożliwić obsługę ruchu iSCSI, SMB, chmury lub zarządzania. W tej tabeli *w* lub *przychodzący* odnosi się do kierunku, w którym przychodzące żądania klienta uzyskują dostęp do urządzenia. *Out* lub *wychodzący* odnosi się do kierunku, w którym Urządzenie StorSimple wysyła dane zewnętrznie, poza wdrożeniem: na przykład, ruch wychodzący do Internetu.

| **Port nr <sup>1</sup>** | **Do lub do zewnątrz** | **Zakres portów** | **Wymagane** | **Uwagi** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Out |Sieć WAN |Nie |Port wychodzący jest używany na potrzeby dostępu do Internetu w celu pobierania aktualizacji. <br></br>Wychodzący serwer proxy sieci Web jest konfigurowany przez użytkownika. |
| TCP 443 (HTTPS) |Out |Sieć WAN |Tak |Port wychodzący służy do uzyskiwania dostępu do danych w chmurze. <br></br>Wychodzący serwer proxy sieci Web jest konfigurowany przez użytkownika. |
| UDP 53 (DNS) |Out |Sieć WAN |W niektórych przypadkach: Zobacz uwagi. |Ten port jest wymagany tylko wtedy, gdy jest używany internetowy serwer DNS. <br></br> Należy pamiętać, że w przypadku wdrażania serwera plików zalecamy użycie lokalnego serwera DNS. |
| UDP 123 (NTP) |Out |Sieć WAN |W niektórych przypadkach: Zobacz uwagi. |Ten port jest wymagany tylko wtedy, gdy jest używany internetowy serwer NTP.<br></br> Należy pamiętać, że w przypadku wdrażania serwera plików zalecamy synchronizowanie czasu z Active Directory kontrolerami domeny. |
| TCP 80 (HTTP) |W |Sieć LAN |Tak |Jest to port wejściowy dla lokalnego interfejsu użytkownika na urządzeniu StorSimple na potrzeby lokalnego zarządzania. <br></br> Należy pamiętać, że dostęp do lokalnego interfejsu użytkownika za pośrednictwem protokołu HTTP zostanie automatycznie przekierowany do protokołu HTTPS. |
| TCP 443 (HTTPS) |W |Sieć LAN |Tak |Jest to port wejściowy dla lokalnego interfejsu użytkownika na urządzeniu StorSimple na potrzeby lokalnego zarządzania. |
| TCP 3260 (iSCSI) |W |Sieć LAN |Nie |Ten port służy do uzyskiwania dostępu do danych za pośrednictwem protokołu iSCSI. |

<sup>1</sup> w publicznym Internecie nie trzeba otwierać żadnych portów przychodzących.

> [!IMPORTANT]
> Upewnij się, że Zapora nie modyfikuje ani nie odszyfrowuje żadnego ruchu TLS między urządzeniem StorSimple i platformą Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Wzorce adresów URL dla reguł zapory
Administratorzy sieci mogą często konfigurować zaawansowane reguły zapory na podstawie wzorców adresów URL, aby filtrować ruch przychodzący i wychodzący. Macierz wirtualna i usługa Device Manager StorSimple zależą od innych aplikacji firmy Microsoft, takich jak Azure Service Bus, Azure Active Directory Access Control, konta magazynu i serwery Microsoft Update. Wzorce adresów URL skojarzone z tymi aplikacjami mogą służyć do konfigurowania reguł zapory. Ważne jest, aby zrozumieć, że wzorce adresów URL skojarzone z tymi aplikacjami mogą się zmieniać. To z kolei wymaga od administratora sieci monitorowania i aktualizowania reguł zapory dla StorSimple, jak i w razie potrzeby. 

Zalecamy ustawienie reguł zapory dla ruchu wychodzącego na podstawie stałych adresów IP StorSimple, w większości przypadków. Można jednak użyć poniższych informacji, aby ustawić zaawansowane reguły zapory, które są potrzebne do tworzenia bezpiecznych środowisk.

> [!NOTE]
> 
> * Adresy IP urządzeń (źródłowych) powinny zawsze być ustawione na wszystkie interfejsy sieciowe obsługujące chmurę. 
> * Docelowe adresy IP powinny być ustawiane na [zakresy adresów IPv4 centrum danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| Wzorzec adresu URL | Składnik/funkcjonalność |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|Usługa Menedżer urządzeń StorSimple<br>Access Control Service<br>Usługa Azure Service Bus<br>Usługa uwierzytelniania|
| `http://*.backup.windowsazure.com` |Rejestracja urządzenia |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odwoływanie certyfikatów |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Konta usługi Azure Storage i monitorowanie |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Serwery Microsoft Update<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Pakiet pomocy technicznej |
| `https://*.data.microsoft.com` |Usługa telemetrii w systemie Windows, zobacz [Aktualizacja dotycząca środowiska klienta i telemetrii diagnostyki](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Następne kroki
* [Przygotowanie portalu do wdrożenia macierzy wirtualnej StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)