---
title: Modyfikowanie konfiguracji urządzenia z serii StorSimple 8000 | Microsoft Docs
description: Opisuje sposób korzystania z usługi StorSimple Menedżer urządzeń w celu ponownego skonfigurowania już wdrożonego urządzenia StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: d0e13f8e66e6035c22c9c2323b9653c5c4a81671
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514683"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Modyfikowanie konfiguracji urządzenia StorSimple przy użyciu usługi StorSimple Menedżer urządzeń

## <a name="overview"></a>Omówienie

Sekcja **Ustawienia urządzenia** Azure Portal w bloku **Ustawienia** zawiera wszystkie parametry urządzeń, które można ponownie skonfigurować na urządzeniu StorSimple zarządzanym przez usługę StorSimple Menedżer urządzeń. W tym samouczku wyjaśniono, jak można użyć bloku **Ustawienia** do wykonywania następujących zadań na poziomie urządzenia:

* Modyfikuj przyjazną nazwę urządzenia
* Modyfikowanie ustawień czasu urządzenia
* Przypisywanie pomocniczego serwera DNS
* Modyfikowanie interfejsów sieciowych
* Zamiana lub zmiana przypisania adresów IP

## <a name="modify-device-friendly-name"></a>Modyfikuj przyjazną nazwę urządzenia

Aby zmienić nazwę urządzenia i przypisać mu unikatową przyjazną nazwę, można użyć Azure Portal. Użyj bloku **Ustawienia ogólne** na urządzeniu, aby zmienić przyjazną nazwę urządzenia. Przyjazna nazwa może zawierać znaki i może składać się z maksymalnie 64 znaków.

> [!NOTE] 
> Nazwę urządzenia można modyfikować tylko w Azure Portal przed ukończeniem konfigurowania urządzenia. Po zakończeniu minimalnej konfiguracji urządzenia nie można zmienić nazwy urządzenia.

![Nazwa urządzenia w ustawieniach ogólnych](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

Do urządzenia StorSimple połączonego z usługą StorSimple Menedżer urządzeń jest przypisana nazwa domyślna. Nazwa domyślna zazwyczaj odzwierciedla numer seryjny urządzenia. Na przykład domyślna nazwa urządzenia o długości 15 znaków, taka jak 8600-SHX0991003G44HT, wskazuje następujące elementy:

* **8600**  — wskazuje model urządzenia.
* **SHX** — wskazuje lokację produkcyjną.
* **0991003** — wskazuje określony produkt.
* **G44HT**— ostatnie 5 cyfr są zwiększane, aby utworzyć unikatowe numery seryjne. Nie może to być zestaw sekwencyjny.

## <a name="modify-device-description"></a>Modyfikowanie opisu urządzenia

Użyj bloku **Ustawienia ogólne** na urządzeniu, aby zmodyfikować opis urządzenia.

![Opis urządzenia w ustawieniach ogólnych](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Opis urządzenia zazwyczaj pomaga identyfikować właściciela i fizyczną lokalizację urządzenia. Pole opisu musi zawierać mniej niż 256 znaków.

## <a name="modify-time-settings"></a>Modyfikowanie ustawień czasu

Urządzenie musi zsynchronizować czas, aby można było uwierzytelnić się u dostawcy usług magazynu w chmurze. Aby zmodyfikować ustawienia czasu urządzenia, użyj bloku **Ustawienia ogólne** na urządzeniu.

![Opis urządzenia w ustawieniach ogólnych](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Z listy rozwijanej wybierz swoją strefę czasową. Można określić maksymalnie dwa serwery protokołu NTP (Network Time Protocol):

 - **Podstawowy serwer NTP** — konfiguracja jest wymagana i jest określana podczas konfigurowania urządzenia za pomocą program Windows PowerShell dla usługi StorSimple. Jako serwer NTP można określić domyślny serwer **Time.Windows.com** systemu Windows Server. Konfigurację podstawowego serwera NTP można wyświetlić za pomocą Azure Portal, ale w celu jego zmiany należy użyć interfejsu programu Windows PowerShell. Użyj `Set-HcsNTPClientServerAddress` polecenia cmdlet, aby zmodyfikować podstawowy serwer NTP na urządzeniu. Aby uzyskać więcej informacji, przejdź do składni polecenia cmdlet [Set-HcsNTPClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) .

- **Pomocniczy serwer NTP** — konfiguracja jest opcjonalna. Za pomocą portalu można skonfigurować pomocniczy serwer NTP.

Podczas konfigurowania serwera NTP upewnij się, że sieć umożliwia przekazywanie ruchu z centrum danych do Internetu. Podczas określania publicznego serwera NTP należy upewnić się, że zapory sieciowe i inne urządzenia zabezpieczeń są skonfigurowane tak, aby zezwalały na ruch NTP do i z sieci zewnętrznej. Jeśli ruch NTP dwukierunkowych nie jest dozwolony, należy użyć wewnętrznego serwera NTP (kontroler domeny systemu Windows udostępnia tę funkcję). Jeśli urządzenie nie może zsynchronizować czasu, może nie być w stanie komunikować się z dostawcą magazynu w chmurze.

Aby wyświetlić listę publicznych serwerów NTP, przejdź do [serwerów NTP w sieci Web](https://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Co się stanie, jeśli urządzenie zostanie wdrożone w innej strefie czasowej?

Jeśli urządzenie zostanie wdrożone w innej strefie czasowej, strefa czasowa urządzenia ulegnie zmianie. Uwzględniając, że wszystkie zasady tworzenia kopii zapasowych używają strefy czasowej urządzenia, zasady tworzenia kopii zapasowych zostaną automatycznie dostosowane zgodnie z nową strefą czasową. Interwencja użytkownika nie jest wymagana.

## <a name="modify-dns-settings"></a>Modyfikowanie ustawień DNS

Serwer DNS jest używany, gdy urządzenie próbuje skomunikować się z dostawcą usługi magazynu w chmurze. Użyj bloku **Ustawienia sieci** na urządzeniu, aby wyświetlić i zmodyfikować SKONFIGUROWANE ustawienia DNS. 

![Ustawienia DNS w ustawieniach sieciowych](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Aby zapewnić wysoką dostępność, należy skonfigurować zarówno podstawowy, jak i pomocniczy serwer DNS podczas początkowego wdrażania urządzenia.

**Podstawowy serwer DNS** — Użyj program Windows PowerShell dla usługi StorSimple, aby najpierw określić podstawowy serwer DNS podczas początkowej konfiguracji. Podstawowy serwer DNS można skonfigurować ponownie tylko za pomocą interfejsu programu Windows PowerShell. Użyj `Set-HcsDNSClientServerAddress` polecenia cmdlet, aby zmodyfikować podstawowy serwer DNS urządzenia. Aby uzyskać więcej informacji, przejdź do składni polecenia cmdlet [Set-HcsDNSClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) .

**Pomocniczy serwer DNS** — aby zmodyfikować pomocniczy serwer DNS, należy użyć `Set-HcsDNSClientServerAddress` polecenia cmdlet w interfejsie programu Windows PowerShell w bloku ustawienia urządzenia lub **sieci** urządzenia StorSimple w Azure Portal.

Aby zmodyfikować pomocniczy serwer DNS w Azure Portal, wykonaj następujące czynności.

1. Przejdź do usługi Menedżer urządzeń StorSimple. Z listy urządzeń wybierz i kliknij urządzenie.

2. W bloku **Ustawienia** przejdź do pozycji **Ustawienia urządzenia > sieć**. Spowoduje to otwarcie bloku **Ustawienia sieci** . Kliknij kafelek **Ustawienia DNS** . Zmodyfikuj adres IP pomocniczego serwera DNS.

    ![Modyfikuj adres IP pomocniczego serwera DNS](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Na pasku poleceń kliknij przycisk **Zapisz** i po wyświetleniu monitu o potwierdzenie kliknij przycisk **OK**.

    ![Zapisz i potwierdź zmiany](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Modyfikowanie interfejsów sieciowych

Urządzenie ma sześć interfejsów sieciowych urządzeń, cztery z nich to 1 GbE i dwa z nich 10 GbE. Te interfejsy są oznaczone jako dane 0 – DATA 5. DANE 0, dane 1, dane 4 i DATA 5 to 1 GbE, natomiast dane 2 i DATA 3 to 10 GbE interfejsów sieciowych.

Użyj bloku **Ustawienia sieciowe** , aby skonfigurować poszczególne interfejsy, które mają być używane.

![Konfigurowanie interfejsów sieciowych za pomocą ustawień sieciowych](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Aby zapewnić wysoką dostępność, zalecamy posiadanie co najmniej dwóch interfejsów iSCSI i dwóch interfejsów z obsługą chmury na urządzeniu. Zalecamy, ale nie wymagają wyłączenia nieużywanych interfejsów.

Dla każdego interfejsu sieciowego wyświetlane są następujące parametry:

* **Szybkość** — nie jest to konfigurowalny przez użytkownika parametr. DANE 0, dane 1, dane 4 i DATA 5 są zawsze 1 GbE, natomiast dane 2 i DATA 3 to 10 GbE.
  
  > [!NOTE]
  > Szybkość i dupleks są zawsze negocjowane ponownie. Duże ramki nie są obsługiwane.
  
* **Stan interfejsu** — interfejs może być włączony lub wyłączony. Jeśli ta funkcja jest włączona, urządzenie podejmie próbę użycia interfejsu. Zaleca się włączenie tylko tych interfejsów, które są podłączone do sieci. Wyłącz wszystkie interfejsy, które nie są używane.
* **Typ interfejsu** — ten parametr umożliwia odizolowanie ruchu iSCSI od ruchu magazynu w chmurze. Ten parametr może mieć jedną z następujących wartości:
  
  * **Cloud Enabled** — gdy ta funkcja jest włączona, urządzenie będzie używać tego interfejsu do komunikowania się z chmurą.
  * Protokół **iSCSI włączony** — po włączeniu tego interfejsu do komunikacji z hostem iSCSI.
    
    Zalecamy odizolowanie ruchu iSCSI od ruchu magazynu w chmurze. Zwróć również uwagę, że host znajduje się w tej samej podsieci co urządzenie, nie musisz przypisywać bramy; Jeśli jednak host znajduje się w innej podsieci niż urządzenie, należy przypisać bramę.
* **Adres IP** — podczas konfigurowania dowolnego interfejsu sieciowego należy skonfigurować wirtualny adres IP (VIP). Może to być adres IPv4 lub IPv6 lub oba te elementy. W przypadku interfejsów sieciowych urządzeń obsługiwane są zarówno rodziny adresów IPv4, jak i IPv6. W przypadku korzystania z protokołu IPv4 należy określić 32-bitowy adres IP (*xxx.xxx.xxx.xxx*) w notacji kropkowo-dziesiętnej. W przypadku korzystania z protokołu IPv6 wystarczy podać 4-cyfrowy prefiks, a adres 128-bitowy zostanie automatycznie wygenerowany dla interfejsu sieciowego urządzenia na podstawie tego prefiksu.
* **Podsieć** — odnosi się do maski podsieci i jest konfigurowana za pomocą interfejsu programu Windows PowerShell.
* **Brama** — Brama domyślna, która powinna być używana przez ten interfejs, gdy próbuje skomunikować się z węzłami, które nie znajdują się w tej samej przestrzeni adresów IP (podsieci). Brama domyślna musi znajdować się w tej samej przestrzeni adresowej (podsieci) co adres IP interfejsu, zgodnie z maską podsieci.
* **Stały adres IP** — to pole jest dostępne tylko podczas KONFIGUROWANIA interfejsu Data 0. W przypadku operacji, takich jak aktualizacje lub Rozwiązywanie problemów z urządzeniem, może być konieczne bezpośrednie połączenie z kontrolerem urządzenia. Stały adres IP może służyć do uzyskiwania dostępu do kontrolera aktywnego i pasywnego na urządzeniu.

> [!NOTE]
> * Aby zapewnić prawidłową operację, sprawdź szybkość interfejsu i dupleks na przełączniku, z którym jest połączony każdy interfejs urządzenia. Interfejsy przełączników powinny być negocjowane z lub skonfigurowane dla sieci Gigabit Ethernet (1000 MB/s) i być w trybie pełnego dupleksu. Interfejsy działające z wolniejszymi szybkościami lub w połowie dupleksu powodują problemy z wydajnością.
> * Aby zminimalizować zakłócenia i przestoje, zalecamy włączenie PortFast na każdym z portów przełącznika, z którymi będzie nawiązywane połączenie interfejs sieciowy iSCSI urządzenia. Dzięki temu będzie można szybko ustalić łączność sieciową w przypadku przejścia w tryb failover.

### <a name="configure-data-0"></a>Konfiguruj dane 0

DANE 0 są domyślnie włączone w chmurze. Podczas konfigurowania danych 0 wymagane jest również skonfigurowanie dwóch stałych adresów IP, po jednym dla każdego kontrolera. Te stałe adresy IP mogą być używane do bezpośredniego dostępu do kontrolerów urządzeń i są przydatne podczas instalowania aktualizacji na urządzeniu, aby odzyskiwanie pamięci działało prawidłowo lub w przypadku uzyskiwania dostępu do kontrolerów na potrzeby rozwiązywania problemów.

Stałe kontrolery IP można skonfigurować ponownie za pomocą bloku ustawienia danych 0.

![Konfigurowanie interfejsu sieciowego — dane 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Stałe adresy IP dla kontrolera są używane do obsługi aktualizacji na urządzeniu oraz do prawidłowego działania algorytmów odzyskiwania pamięci (wyrzucania elementów bezużytecznych). W związku z tym stałe adresy IP muszą być routowalne i mieć możliwość łączenia z Internetem.

### <a name="configure-data-1---data-5"></a>Konfigurowanie danych 1 — DATA 5

W przypadku interfejsów sieciowych DATA 1 — DATA 5 można skonfigurować wszystkie ustawienia sieciowe, jak pokazano na poniższym zrzucie ekranu:

![Konfigurowanie interfejsów sieciowych dane 1 — DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Zamiana lub zmiana przypisania adresów IP

Obecnie Jeśli dowolny interfejs sieciowy na kontrolerze ma przypisany adres VIP używany przez to samo urządzenie lub inne urządzenie w sieci, kontroler przejdzie w tryb failover. W przypadku wymiany lub ponownego przypisywania adresów VIP dla interfejsu sieciowego urządzenia należy wykonać odpowiednią procedurę, ponieważ można utworzyć zduplikowaną sytuację IP.

Wykonaj następujące kroki, aby zamienić lub ponownie przypisać wirtualne adresy IP dla dowolnego z interfejsów sieciowych:

#### <a name="to-reassign-ips"></a>Aby ponownie przypisać adresy IP

1. Wyczyść adres IP dla obu interfejsów.
2. Po wyczyszczeniu adresów IP Przypisz nowe adresy IP do odpowiednich interfejsów.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [skonfigurować wielościeżkowe wejście/wyjście dla urządzenia z StorSimple](storsimple-8000-configure-mpio-windows-server.md).
* Dowiedz się [, jak zarządzać urządzeniem StorSimple przy użyciu usługi StorSimple Menedżer urządzeń](storsimple-8000-manager-service-administration.md).

