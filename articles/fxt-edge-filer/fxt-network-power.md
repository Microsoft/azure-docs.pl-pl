---
title: 'Samouczek: łączenie kabli z usługą Azure FXT Edge'
description: Jak podłączyć porty sieciowe i dołączyć energię dla sprzętu usługi Azure FXT Edge
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 80458d99ed6ad19c8a837f0989798d4dcc156b61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219534"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Samouczek: tworzenie połączeń sieciowych i dostarczanie mocy do węzła usługi Azure FXT Edge

W tym samouczku przedstawiono sposób podłączenia połączeń sieciowych do węzła sprzętowego usługi Azure FXT Edge.

Z tego samouczka dowiesz się:

> [!div class="checklist"]
>
> * Jak wybrać typ kabla sieciowego dla środowiska
> * Jak podłączyć węzeł usługi Azure FXT Edge do sieci centrum danych
> * Jak kierować kablemi za pośrednictwem ramienia zarządzania kablami (CMA)
> * Jak nawiązać połączenie z urządzeniem z stojakiem i włączyć je

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy zainstalować plik usługi Azure FXT Edge w standardowym stojaku sprzętowym. CMA należy zainstalować w węźle pliku.

## <a name="identify-ports"></a>Identyfikowanie portów

Zidentyfikuj różne porty z tyłu pliku usługi Azure FXT Edge.

![Z tyłu urządzenia z kablem](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Podłączanie kabli urządzenia

* Połącz porty RJ-45 ze źródłem sieciowym centrum danych, zgodnie z opisem w temacie [porty sieciowe](#network-ports).  
* Należy bezpiecznie podłączyć [port iDRAC](#idrac-port) do oddzielnej sieci przy użyciu bezpiecznego serwera DHCP.
* Użyj portów USB i portu VGA, aby połączyć klawiaturę i monitor z węzłem w celu wstępnego skonfigurowania. Należy uruchomić węzeł i [ustawić początkowe hasło](fxt-node-password.md) , aby aktywować inne porty węzła. Aby uzyskać szczegółowe informacje, Przeczytaj [ustawienie wstępne hasła](fxt-node-password.md) .

W tym artykule opisano również, jak [podłączyć zasilanie zasilania](#connect-power-cables) dla węzła.

W tym artykule opisano również sposób nawiązywania połączenia z [portem seryjnym](#serial-port-only-when-necessary)węzła, jeśli jest to konieczne do wyspecjalizowanego rozwiązywania problemów.

### <a name="network-ports"></a>Porty sieciowe

Każdy węzeł usługi Azure FXT Edge obejmuje następujące porty sieciowe:

* Sześć portów danych o podwójnej szybkości 25GbE/10 GbE:

  * Cztery porty udostępniane przez dwie dwuportowe karty sieciowe
  * Dwa porty dostarczone przez kartę sieciową głównej Mezzanine

* Dwa porty 1GbE dostarczone przez kartę sieciową głównej Mezzanine

Szybkie porty danych 25GbE/10GbE mają standardowe, zgodne z SFP28. Aby można było korzystać z kabli optycznych, należy zainstalować moduły SFP28 Optical odbiornika (niedostarczone).

Porty 1GbE mają standardowe łączniki RJ-45.

Aby zapoznać się z pełną listą obsługiwanych kabli, przełączników i urządzeń nadawczych, zapoznaj się z [matrycą współdziałania serii Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

Typ połączeń, które mają być używane przez system, zależy od Twojego środowiska centrum danych.

* W przypadku nawiązywania połączenia z siecią 25GbE Podłącz każdy z szybkich portów danych o jednym z następujących typów kabli:

  * Kabel optyczny i SFP28 optyczny urządzenia nadawczego z możliwością 25GbE lub Dual Rate 25GbE/10
  * SFP28 typ 25GbE z bezpośrednim dołączeniem kabla twinaxial

* W przypadku łączenia się z siecią 10 GbE kabel każdego z szybkich portów danych z jedną z następujących:

  * Kabel optyczny i SFP28 optyczny urządzenia nadawczego z opcją 10 GbE lub dwoma szybkościami 25GbE/10.
  * SFP28 typ 25GbE z bezpośrednim dołączeniem kabla twinaxial
  * Typ SFP28, który umożliwia bezpośrednie dołączenie kabla twinaxial

* Porty sieciowe 1GbE są używane do obsługi ruchu związanego z zarządzaniem klastrami. Zaznacz opcję **Użyj sieci zarządzania 1 GB** podczas tworzenia klastra, jeśli chcesz utworzyć fizycznie oddzielną sieć do konfiguracji klastra (opisana w temacie [Konfigurowanie sieci zarządzania](fxt-cluster-create.md#configure-the-management-network)). Podłącz porty przy użyciu standardowego CAT5 lub lepszego kabla, zgodnie z opisem na liście obsługiwanych kabli.

  Jeśli planujesz używać portów o dużej szybkości dla całego ruchu, możesz pozostawić porty 1GbE. Domyślnie porty sieciowe 1GbE nie są używane, jeśli port danych o wyższej szybkości jest dostępny.  

### <a name="idrac-port"></a>Port iDRAC  

Port oznaczony iDRAC jest połączeniem z 1 GB, która umożliwia komunikację z kontrolerem dostępu zdalnego używanym do zarządzania sprzętem i monitorowania. Oprogramowanie FXT używa interfejsu zarządzania platformą inteligentną (IPMI) z tym kontrolerem do rozwiązywania problemów i odzyskiwania. Można użyć wbudowanego [interfejsu iDRAC](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) do monitorowania sprzętu za pomocą tego portu. dostęp iDRAC i IPMI są domyślnie włączone.

> [!Note]
> Port iDRAC może ominąć system operacyjny i korzystać bezpośrednio z sprzętu w węźle.

Użyj tych strategii zabezpieczeń podczas łączenia i konfigurowania portu iDRAC:

* Połącz tylko porty iDRAC z siecią, która jest fizycznie oddzielona od sieci danych używanej do uzyskiwania dostępu do klastra.
* Ustaw bezpieczne hasło administratora iDRAC w każdym węźle. Należy ustawić to hasło, aby uaktywnić odpowiednie instrukcje na stronie [Ustawianie haseł sprzętowych](fxt-node-password.md).
* Domyślna konfiguracja portu iDRAC używa protokołu DHCP i IPv4 do przypisywania adresów IP. Upewnij się, że środowisko DHCP jest dobrze chronione i że połączenia między klientami DHCP a serwerem DHCP są ograniczone. (Panel sterowania klastra zawiera ustawienia umożliwiające zmianę metody konfiguracji adresów węzłów po utworzeniu klastra).
* Pozostaw port iDRAC ustawiony na "tryb dedykowany" (domyślnie), który ogranicza ruch sieciowy iDRAC/IPMI do dedykowanego portu RJ-45.

Port iDRAC nie wymaga połączenia z siecią o dużej szybkości.
  
### <a name="serial-port-only-when-necessary"></a>Port szeregowy (tylko w razie potrzeby)

W niektórych sytuacjach usługa i pomoc techniczna firmy Microsoft mogą poinstruować o połączeniu terminalu z portem szeregowym węzła w celu zdiagnozowania problemu.

Aby dołączyć konsolę programu:

1. Znajdź port szeregowy (COM1) znajdujący się w tylnej części węzła FXT Edge.
1. Aby połączyć port szeregowy z terminalem skonfigurowanym dla standardu ANSI-115-8N1, użyj kabla modemu o wartości null.
1. Zaloguj się do konsoli programu i wykonaj inne czynności zgodnie z instrukcjami personelu pomocy technicznej.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Trasowanie kabli w bramie do zarządzania kablami (CMA)

Każdy węzeł usługi Azure FXT Edge jest dostarczany z opcjonalną bramą do zarządzania kablami. CMA upraszcza Routing kabli i zapewnia łatwiejszy dostęp do zaplecza obudowy bez konieczności rozłączania kabli.

Postępuj zgodnie z poniższymi instrukcjami, aby kierować kablem do CMA:

1. Korzystając z podanych zawijania, należy powiązać kable w miarę wprowadzania i wypełniania koszyków, aby nie zakłócać systemów sąsiednich (1).
1. Za pomocą CMA w pozycji usługi Roześlij pakiet kabli za pośrednictwem koszyków wewnętrznych i zewnętrznych (2).
1. Aby zabezpieczyć kable (3), użyj preinstalowanych punktów zaczepienia i pasów w pętli.
1. Ustaw CMA z powrotem na półkę (4).
1. Zainstaluj kabel wskaźnika stanu z tyłu systemu i zabezpiecz go przy użyciu usługi CMA. Dołącz drugi koniec kabla do rogu zewnętrznego koszyka CMA (5).

   > [!CAUTION]
   > Aby uniknąć potencjalnego uszkodzenia przy wykorzystaniu kabli, Zabezpiecz wszystkie zapasy w kablach wskaźnika stanu po kierowaniu tego kabla przez CMA.

![Ilustracja przedstawiająca CMA z zainstalowanymi kablami](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
> Jeśli nie zainstalowano CMA, użyj dwóch punktów zaczepienia i pętli, które znajdują się w zestawie szyny do kierowania kabli z tyłu systemu.
>
>  1. Znajdź zewnętrzne nawiasy CMA na wewnętrznych stronach obu kołnierzy stojaków.
>  2. Łagodnie Podziel kable, pobierając ich jasne elementy z łączników systemowych do lewej i prawej strony.
>  3. Zawątkuje punkty zaczepienia i pętle za pośrednictwem gniazd w zewnętrznych nawiasach CMA po każdej stronie systemu, aby zabezpieczyć zestawy kabli.
>
>     ![Kable kierowane bez CMA](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Informacje o wymaganiach adresów IP

W przypadku węzłów sprzętowych w pamięci podręcznej magazynu hybrydowego usługi Azure FXT Edge adresy IP są zarządzane przez oprogramowanie klastra.

Każdy węzeł wymaga co najmniej jednego adresu IP, ale adresy węzłów są przypisywane, gdy węzły są dodawane lub usuwane z klastra.

Łączna liczba wymaganych adresów IP zależy od liczby węzłów znajdujących się w pamięci podręcznej.

Skonfiguruj zakres adresów IP przy użyciu oprogramowania panelu sterowania po zainstalowaniu węzłów. Aby dowiedzieć się więcej, przeczytaj artykuł [zbieranie informacji o klastrze](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Podłącz kable zasilające

Każdy węzeł pliku usługi Azure FXT Edge używa dwóch jednostek zasilacza (PSUs).

> [!TIP]
> Aby skorzystać z dwóch nadmiarowych PSUs, podłącz każdy przewód zasilania do jednostki dystrybucji zasilania (PDU) w ramach niezależnego obwodu gałęzi.
>
> W celu zapewnienia dodatkowej ochrony można użyć zasilacza UPS.

1. Podłącz dołączone przewody zasilające do PSUs w obudowie. Upewnij się, że przewody i PSUs są w pełni umiejscowione.
1. Podłącz przewody zasilające do jednostek dystrybucji mocy w stojaku sprzętowym. Jeśli to możliwe, należy użyć dwóch oddzielnych źródeł napięcia dla dwóch przewodów.

### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Włącz węzeł pliku usługi Azure FXT Edge

Aby wyłączyć węzeł, naciśnij przycisk z góry systemu. Przycisk znajduje się na prawym panelu sterowania.

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Wyłączanie węzła usługi Azure FXT Edge

Przycisk energia może służyć do zamykania systemu podczas testowania i przed dodaniem go do klastra. Jednak gdy węzeł usługi Azure FXT Edge jest używany jako część klastra, należy zamknąć sprzęt przy użyciu oprogramowania panelu sterowania klastra. Aby uzyskać szczegółowe informacje, zapoznaj [się z artykułem jak bezpiecznie wyłączyć sprzęt usługi Azure FXT Edge](fxt-power-off.md) .

## <a name="next-steps"></a>Następne kroki

Po zakończeniu okablowania sprzętu Włącz wszystkie węzły i zainicjuj je przez ustawienie ich haseł głównych.
> [!div class="nextstepaction"]
> [Ustaw hasła początkowe](fxt-node-password.md)
