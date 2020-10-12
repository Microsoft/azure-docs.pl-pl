---
title: Urządzenie StorSimple w chmurze z aktualizacją Update 3| Microsoft Docs
description: Informacje na temat tworzenia i wdrażania urządzenia StorSimple w chmurze oraz zarządzania nim w sieci wirtualnej platformy Microsoft Azure. (Dotyczy urządzenia wirtualnego StorSimple z aktualizacją Update 3 i nowszych).
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2017
ms.author: alkohli
ms.openlocfilehash: 219e2b77a0f6f30307c43f006fcdd3828d3c8fbf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87021379"
---
# <a name="deploy-and-manage-a-storsimple-cloud-appliance-in-azure-update-3-and-later"></a>Wdrażanie urządzenia StorSimple w chmurze oraz zarządzanie nim na platformie Azure (aktualizacja Update 3 i nowsze)

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie

Urządzenie z serii StorSimple 8000 w chmurze oferuje dodatkową funkcję dołączoną do rozwiązania Microsoft Azure StorSimple. Urządzenie StorSimple w chmurze działa na maszynie wirtualnej w sieci wirtualnej platformy Microsoft Azure i służy do tworzenia kopii zapasowych oraz klonowania danych z hostów.

W tym artykule szczegółowo opisano proces wdrażania urządzenia StorSimple w chmurze oraz zarządzania nim na platformie Azure. Zapoznanie się z tym artykułem umożliwi:

* Zrozumienie, czym urządzenie w chmurze różni się od urządzenia fizycznego.
* Tworzenie i konfigurowanie urządzenia w chmurze.
* Łączenie z urządzeniem w chmurze.
* Obsługę urządzenia w chmurze.

Ten samouczek dotyczy wszystkich urządzeń StorSimple w chmurze z aktualizacją Update 3 i nowszych.

#### <a name="cloud-appliance-model-comparison"></a>Porównanie modeli urządzeń w chmurze

Dostępne są dwa modele urządzenia StorSimple w chmurze: standardowy — 8010 (wcześniej znany jako 1100) oraz Premium — 8020 (wprowadzony w aktualizacji Update 2). W poniższej tabeli przedstawiono porównanie tych dwóch modeli.

| Model urządzenia | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **Maksymalna pojemność** |30 TB |64 TB |
| **Maszyna wirtualna platformy Azure** |Standard_A3 (4 rdzenie, 7 GB pamięci)| Standard_DS3 (4 rdzenie, 14 GB pamięci)|
| **Dostępność w danym regionie** |Wszystkie regiony platformy Azure |Regiony świadczenia usługi Azure obsługujące usługę Premium Storage i maszyny wirtualne DS3 platformy Azure<br></br>Przy użyciu [tej listy](https://azure.microsoft.com/regions/services/) sprawdź, czy w Twoim regionie jest dostępna zarówno opcja **Maszyny wirtualne > Seria DS**, jak i opcja **Magazyn > Magazyn na dysku**. |
| **Typ magazynu** |Używa usługi Azure Standard Storage dla dysków lokalnych<br></br> Informacje na temat [tworzenia konta Standard Storage](../storage/common/storage-create-storage-account.md) |Używa Premium Storage platformy Azure dla dysków lokalnych<sup>2</sup> <br></br> |
| **Wskazówki dotyczące obciążenia** |Pobieranie plików z kopii zapasowych na poziomie elementu |Scenariusze tworzenia i testowania w chmurze <br></br>Obciążenia o małych opóźnieniach i większej wydajności<br></br>Urządzenie pomocnicze do odzyskiwania po awarii |

<sup>1</sup> *dawniej znany jako 1100*.

<sup>2</sup> *8010 i 8020 używają usługi Azure Standard Storage w warstwie chmurowej. Różnica istnieje tylko w warstwie lokalnej urządzenia*.

## <a name="how-the-cloud-appliance-differs-from-the-physical-device"></a>Czym urządzenie w chmurze różni się od urządzenia fizycznego

Urządzenie StorSimple w chmurze jest wersją urządzenia StorSimple istniejącą tylko w formie oprogramowania, która działa w jednym węźle maszyny wirtualnej na platformie Microsoft Azure. Urządzenie w chmurze obsługuje scenariusze odzyskiwania po awarii, w których urządzenie fizyczne nie jest dostępne. Urządzenie w chmurze jest przeznaczone do użycia podczas pobierania z kopii zapasowych na poziomie elementu, lokalnego odzyskiwania po awarii, a także na potrzeby scenariuszy tworzenia i testowania w chmurze.

#### <a name="differences-from-the-physical-device"></a>Różnice względem urządzenia fizycznego

W poniższej tabeli przedstawiono niektóre podstawowe różnice między urządzeniem StorSimple w chmurze i urządzeniem fizycznym StorSimple.

|  | Urządzenie fizyczne | Urządzenie w chmurze |
| --- | --- | --- |
| **Lokalizacja** |Znajduje się w centrum danych. |Działa w systemie Azure. |
| **Interfejsy sieciowe** |Ma sześć interfejsów sieciowych: DANE 0 do DANE 5. |Ma tylko jeden interfejs sieciowy: DANE 0. |
| **Rejestracja** |Rejestrowane podczas wykonywania kroku konfiguracji początkowej. |Rejestracja jest osobnym zadaniem. |
| **Klucz szyfrowania danych usługi** |Należy wygenerować ponownie w urządzeniu fizycznym, a następnie zaktualizować urządzenie w chmurze za pomocą nowego klucza. |Nie można ponownie wygenerować z urządzenia w chmurze. |
| **Obsługiwane typy woluminów** |Obsługiwane są zarówno woluminy przypięte lokalnie, jak i woluminy warstwowe. |Obsługiwane są tylko woluminy warstwowe. |

## <a name="prerequisites-for-the-cloud-appliance"></a>Wymagania wstępne dotyczące urządzenia w chmurze

Poniższe sekcje zawierają opis wymagań wstępnych dotyczących konfiguracji urządzenia StorSimple w chmurze. Przed wdrożeniem urządzenia w chmurze zapoznaj się z zagadnieniami dotyczącymi zabezpieczeń związanych z używaniem urządzenia w chmurze.

[!INCLUDE [StorSimple Cloud Appliance security](../../includes/storsimple-8000-cloud-appliance-security.md)]

#### <a name="azure-requirements"></a>Wymagania systemu Azure

Przed aprowizowaniem urządzenia w chmurze wykonaj następujące czynności przygotowawcze w środowisku platformy Azure:

* Upewnij się, że masz urządzenie fizyczne StorSimple z serii 8000 (model 8100 lub 8600) wdrożone i działające w centrum danych. Zarejestruj to urządzenie za pomocą tej samej usługi Menedżera urządzeń StorSimple, dla której zamierzasz utworzyć urządzenie StorSimple w chmurze.
* [Skonfiguruj sieć wirtualną na platformie Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) dla urządzenia w chmurze. W przypadku korzystania z usługi Premium Storage należy utworzyć sieć wirtualną w regionie platformy Azure obsługującym tę usługę. Regiony Premium Storage to regiony, które odpowiadają wierszowi magazynu dyskowego na [liście usług platformy Azure według regionów](https://azure.microsoft.com/regions/services/).
* Zalecamy używanie domyślnego serwera DNS udostępnionego na platformie Azure zamiast określania nazwy własnego serwera DNS. Jeśli nazwa serwera DNS jest nieprawidłowa lub jeśli serwer DNS nie jest w stanie poprawnie rozpoznać adresów IP, tworzenie urządzenia w chmurze kończy się niepowodzeniem.
* Sieci typu punkt do lokacji i lokacja do lokacji są opcjonalne. W razie potrzeby można skonfigurować te opcje dla bardziej zaawansowanych scenariuszy.
* Można utworzyć [maszyny wirtualne Azure Virtual Machines](../virtual-machines/windows/quick-create-portal.md) (serwery hosta) w sieci wirtualnej, która może korzystać z woluminów udostępnionych przez urządzenie w chmurze. Serwery te muszą spełniać następujące wymagania:

  * Muszą być maszynami wirtualnymi z systemem Windows lub Linux z zainstalowanym oprogramowaniem iSCSI Initiator.
  * Muszą działać w tej samej sieci wirtualnej co urządzenie w chmurze.
  * Muszą mieć możliwość połączenia z obiektem docelowym iSCSI urządzenia w chmurze za pośrednictwem wewnętrznego adresu IP urządzenia w chmurze.
  * Należy sprawdzić, czy skonfigurowano obsługę dla ruchu iSCSI i chmury w tej samej sieci wirtualnej.

#### <a name="storsimple-requirements"></a>Wymagania dotyczące urządzenia StorSimple

Przed utworzeniem urządzenia w chmurze przeprowadź następujące aktualizacje usługi Menedżer urządzeń StorSimple:

* Dodaj [rekordy kontroli dostępu](storsimple-8000-manage-acrs.md) do maszyn wirtualnych, które będą serwerami hosta dla urządzenia w chmurze.
* Użyj [konta magazynu](storsimple-8000-manage-storage-accounts.md#add-a-storage-account) w tym samym regionie, w którym działa urządzenie w chmurze. Jeśli konta usługi Storage są w różnych regionach, wydajność może zostać obniżona. W urządzeniu w chmurze można używać konta Standard lub Premium Storage. Więcej informacji na temat [tworzenia konta Standard Storage](../storage/common/storage-create-storage-account.md).
* Do utworzenia urządzenia w chmurze użyj innego konta magazynu niż używane do obsługi danych. Użycie tego samego konta magazynu może spowodować obniżenie wydajności.

Upewnij się, że przed rozpoczęciem masz przygotowane następujące informacje:

* Konto witryny Azure Portal z poświadczeniami dostępu.
* Kopia klucza szyfrowania danych usługi z urządzenia fizycznego zarejestrowanego w usłudze Menedżer urządzeń StorSimple.

## <a name="create-and-configure-the-cloud-appliance"></a>Tworzenie i konfigurowanie urządzenia w chmurze

Przed wykonaniem tych procedur upewnij się, że spełniono [wymagania wstępne dotyczące urządzeń w chmurze](#prerequisites-for-the-cloud-appliance).

Wykonaj poniższe kroki, aby utworzyć urządzenie StorSimple w chmurze.

### <a name="step-1-create-a-cloud-appliance"></a>Krok 1. Tworzenie urządzenia w chmurze

Wykonaj poniższe kroki, aby utworzyć urządzenie StorSimple w chmurze.

[!INCLUDE [Create a cloud appliance](../../includes/storsimple-8000-create-cloud-appliance-u2.md)]

Jeśli tworzenie urządzenia w chmurze zakończy się niepowodzeniem w trakcie tego kroku, może to oznaczać brak łączności z Internetem. Aby uzyskać więcej informacji, przejdź do sekcji [rozwiązywania problemów z błędami łączności internetowej](#troubleshoot-internet-connectivity-errors) podczas tworzenia urządzenia w chmurze.

### <a name="step-2-configure-and-register-the-cloud-appliance"></a>Krok 2. Konfigurowanie i rejestrowanie urządzenia w chmurze

Przed rozpoczęciem tej procedury upewnij się, że masz kopię klucza szyfrowania danych usługi. Klucz szyfrowania danych usługi jest tworzony podczas rejestrowania pierwszego urządzenia fizycznego StorSimple w usłudze Menedżer urządzeń StorSimple. Zgodnie z instrukcją należy przechowywać go w bezpiecznym miejscu. Jeśli nie masz kopii klucza szyfrowania danych usługi, w celu uzyskania pomocy musisz skontaktować się z działem pomocy firmy Microsoft.

Wykonaj poniższe kroki, aby skonfigurować i zarejestrować urządzenie StorSimple w chmurze.

[!INCLUDE [Configure and register a cloud appliance](../../includes/storsimple-8000-configure-register-cloud-appliance.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Krok 3 (opcjonalny): Modyfikowanie ustawień konfiguracji dla urządzeń

W poniższej sekcji opisano ustawienia konfiguracji dla urządzeń wymagane dla urządzenia StorSimple w chmurze, jeśli chcesz użyć protokołu CHAP lub przystawki StorSimple Snapshot Manager albo zmienić hasło administratora urządzenia.

#### <a name="configure-the-chap-initiator"></a>Konfigurowanie inicjatora protokołu CHAP

Ten parametr zawiera poświadczenia, których urządzenie w chmurze (obiekt docelowy) oczekuje od inicjatorów (serwerów) próbujących uzyskać dostęp do woluminów. Inicjatory udostępniają nazwę użytkownika protokołu CHAP i hasło protokołu CHAP w celu zidentyfikowania się względem urządzenia podczas tego uwierzytelniania. Aby uzyskać szczegółowy opis kroków, przejdź do tematu [Configure CHAP for your device](storsimple-8000-configure-chap.md#unidirectional-or-one-way-authentication) (Konfigurowanie protokołu CHAP dla urządzenia).

#### <a name="configure-the-chap-target"></a>Konfigurowanie obiektu docelowego protokołu CHAP

Ten parametr zawiera poświadczenia, które są używane przez urządzenie w chmurze, gdy inicjator z włączonym protokołem CHAP żąda uwierzytelniania wzajemnego lub dwukierunkowego. Urządzenie w chmurze użyje nazwy użytkownika i hasła protokołu odwrotnego uwierzytelniania CHAP w celu zidentyfikowania się względem inicjatora podczas tego procesu uwierzytelniania.

> [!NOTE]
> Ustawienia obiektu docelowego protokołu CHAP są ustawieniami globalnymi. Gdy są one stosowane, wszystkie woluminy połączone z urządzeniem w chmurze magazynu używają uwierzytelniania protokołu CHAP.

Aby uzyskać szczegółowy opis kroków, przejdź do tematu [Configure CHAP for your device](storsimple-8000-configure-chap.md#bidirectional-or-mutual-authentication) (Konfigurowanie protokołu CHAP dla urządzenia).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Konfigurowanie hasła do programu StorSimple Snapshot Manager

Oprogramowanie StorSimple Snapshot Manager jest zainstalowane na hoście z systemem Windows i umożliwia administratorom zarządzanie kopiami zapasowymi urządzenia StorSimple przez tworzenie migawek lokalnych i w chmurze.

> [!NOTE]
> Dla urządzenia w chmurze host z systemem Windows jest maszyną wirtualną na platformie Azure.

Podczas konfigurowania urządzenia w programie StorSimple Snapshot Manager jest wyświetlamy monit o podanie adresu IP i hasła urządzenia StorSimple w celu uwierzytelnienia urządzenia magazynu. Aby uzyskać szczegółowy opis kroków, przejdź do tematu [Configure StorSimple Snapshot Manager password](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password) (Konfigurowanie hasła do programu StorSimple Snapshot Manager).

#### <a name="change-the-device-administrator-password"></a>Zmiana hasła administratora urządzenia

W przypadku uzyskiwania dostępu do urządzenia w chmurze z użyciem interfejsu programu Windows PowerShell trzeba wprowadzić hasło administratora urządzenia. W celu zachowania bezpieczeństwa danych przed użyciem urządzenia w chmurze trzeba to hasło zmienić. Aby uzyskać szczegółowy opis kroków, przejdź do tematu [Configure device administrator password](../storsimple/storsimple-8000-change-passwords.md#change-the-device-administrator-password) (Konfigurowanie hasła administratora urządzenia).

## <a name="connect-remotely-to-the-cloud-appliance"></a>Zdalne łączenie z urządzeniem w chmurze

Zdalny dostęp do urządzenia w chmurze za pośrednictwem interfejsu programu Windows PowerShell nie jest włączony domyślnie. Najpierw należy włączyć zdalne zarządzanie w urządzeniu w chmurze, a następnie na kliencie używanym do uzyskiwania dostępu do urządzenia w chmurze.

Poniższa procedura dwuetapowa opisuje sposób zdalnego nawiązywania połączenia z chmurą.

### <a name="step-1-configure-remote-management"></a>Krok 1: Konfigurowanie zdalnego zarządzania

Wykonaj poniższe kroki, aby skonfigurować zdalne zarządzanie dla urządzenia StorSimple w chmurze.

[!INCLUDE [Configure remote management via HTTP for cloud appliance](../../includes/storsimple-8000-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-cloud-appliance"></a>Krok 2: Zdalne uzyskiwanie dostępu do urządzenia w chmurze

Po włączeniu zdalnego zarządzania w urządzeniu w chmurze użyj programu Windows PowerShell w celu zdalnego połączenia z urządzenia z innej maszyny wirtualnej w tej samej sieci wirtualnej. Możesz na przykład nawiązać połączenie z maszyny wirtualnej hosta, która została skonfigurowana i użyta do połączenia interfejsu iSCSI. W większości wdrożeń otworzysz publiczny punkt końcowy dostępu do maszyny wirtualnej hosta, która służy do uzyskiwania dostępu do urządzenia w chmurze.

> [!WARNING]
> **Zdecydowanie zalecamy, aby w celu zwiększenia bezpieczeństwa używać protokołu HTTPS podczas nawiązywania połączenia z punktami końcowymi, a następnie usunąć punkty końcowe po zakończeniu sesji zdalnej programu PowerShell.**

Aby skonfigurować komunikację zdalną dla urządzenia w chmurze, należy wykonać procedury opisane w temacie [Connecting remotely to your StorSimple device](storsimple-8000-remote-connect.md) (Nawiązywanie połączenia zdalnego z urządzeniem StorSimple).

## <a name="connect-directly-to-the-cloud-appliance"></a>Bezpośrednie łączenie z urządzeniem w chmurze

Można również nawiązać bezpośrednie połączenie z urządzeniem w chmurze. Aby nawiązać bezpośrednie połączenie z urządzeniem w chmurze z innego komputera spoza sieci wirtualnej lub znajdującego się poza środowiskiem platformy Microsoft Azure, musisz utworzyć dodatkowe punkty końcowe.

Wykonaj poniższe kroki, aby utworzyć publiczny punkt końcowy w urządzeniu w chmurze.

[!INCLUDE [Create public endpoints on a cloud appliance](../../includes/storsimple-8000-create-public-endpoints-cloud-appliance.md)]

Firma Microsoft zaleca nawiązywanie połączenia z innej maszyny wirtualnej znajdującej się w tej samej sieci wirtualnej, ponieważ takie rozwiązanie zmniejsza do minimum liczbę publicznych punktów końcowych w sieci wirtualnej. W takiej sytuacji należy nawiązać połączenie z maszyną wirtualną za pośrednictwem sesji pulpitu zdalnego, po czym skonfigurować maszynę wirtualną do użytku, jak w przypadku innych klientów z systemem Windows w sieci lokalnej. Nie trzeba dołączać numeru portu publicznego, ponieważ port jest już znany.

## <a name="get-private-ip-for-the-cloud-appliance"></a>Uzyskiwanie prywatnego adresu IP urządzenia w chmurze

Aby urządzenie w chmurze nawiązało połączenie z serwerem hosta w tej samej sieci wirtualnej, wymagany jest wewnętrzny lub prywatny adres IP dla tego urządzenia w chmurze. Wykonaj następujące kroki, aby uzyskać prywatny adres IP urządzenia w chmurze.

1. Przejdź do maszyny wirtualnej stanowiącej podstawę dla urządzenia w chmurze. Maszyna wirtualna ma taką samą nazwę jak urządzenie w chmurze. Przejdź do sekcji **Wszystkie zasoby**, podaj nazwę urządzenia w chmurze i subskrypcję, a następnie wybierz typ jako maszyny wirtualne. Na liście prezentowanych maszyn wirtualnych wybierz i kliknij maszynę wirtualną powiązaną z urządzeniem w chmurze.

     ![Wybieranie maszyny wirtualnej dla urządzenia w chmurze](./media/storsimple-8000-cloud-appliance-u2/sca-vm.png)

2. Przejdź do opcji **Ustawienia > Sieć**. W okienku po prawej stronie wyświetlany jest prywatny adres IP urządzenia w chmurze. Zanotuj ją.

    ![Uzyskiwanie prywatnego adresu IP dla urządzenia w chmurze](./media/storsimple-8000-cloud-appliance-u2/sca-private-ip-vm-networking.png)

## <a name="work-with-the-storsimple-cloud-appliance"></a>Praca z urządzeniem StorSimple w chmurze

Po utworzeniu i skonfigurowaniu urządzenia StorSimple w chmurze można rozpocząć pracę. W urządzeniu w chmurze można pracować z kontenerami woluminów, woluminami i zasadami tworzenia kopii zapasowych tak samo jak w przypadku urządzenia fizycznego StorSimple. Jedyna różnica polega na tym, że należy upewnić się, że z listy urządzeń wybrano urządzenie w chmurze. Zapoznaj się z tematem dotyczącym [użycia usługi Menedżer urządzeń StorSimple do zarządzania urządzeniem w chmurze](storsimple-8000-manager-service-administration.md), w którym szczegółowo opisano procedury różnych zadań zarządzania dla urządzenia w chmurze.

W poniższych sekcjach omówiono niektóre różnice, które występują podczas pracy z urządzeniem w chmurze.

### <a name="maintain-a-storsimple-cloud-appliance"></a>Konserwacja urządzenia StorSimple w chmurze

Ponieważ urządzenie w chmurze istnieje tylko w formie oprogramowania, jego wymagania w zakresie konserwacji są ograniczone do minimum w porównaniu z konserwacją urządzenia fizycznego.

Urządzenia w chmurze nie można aktualizować. Użyj najnowszej wersji oprogramowania do utworzenia nowego urządzenia w chmurze.


### <a name="storage-accounts-for-a-cloud-appliance"></a>Konta magazynu dla urządzenia w chmurze

Konta magazynu są przeznaczone do użycia przez usługę Menedżer urządzeń StorSimple, urządzenie w chmurze oraz urządzenie fizyczne. Podczas tworzenia kont magazynu zalecamy użycie identyfikatora regionu w przyjaznej nazwie. Pomoże to zapewnić, że region będzie spójny we wszystkich składnikach systemu. W przypadku urządzenia w chmurze ważne jest, aby wszystkie składniki znajdowały się w tym samym regionie, gdyż zapobiega to problemom z wydajnością.

Aby zapoznać się z procedurą krok po kroku, przejdź do tematu dotyczącego [dodawania konta magazynu](storsimple-8000-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-cloud-appliance"></a>Dezaktywacja urządzenia StorSimple w chmurze

Dezaktywacja urządzenia w chmurze powoduje usunięcie maszyny wirtualnej i zasobów utworzonych podczas jej aprowizacji. Po dezaktywacji urządzenia w chmurze nie można go przywrócić do poprzedniego stanu. Przed dezaktywacją urządzenia w chmurze należy koniecznie zatrzymać lub usunąć klientów i hosty, które od niego zależą.

Po dezaktywacji urządzenia w chmurze:

* Urządzenie w chmurze zostanie usunięte.
* Dysk systemu operacyjnego i dyski danych utworzone dla urządzenia w chmurze zostaną usunięte.
* Hostowana usługa i sieć wirtualna, które zostały utworzone podczas inicjowania obsługi zostaną zachowywane. Jeśli nie są używane, należy je usunąć ręcznie.
* Migawki w chmurze utworzone dla urządzenia w chmurze zostaną zachowane.

Aby zapoznać się z procedurą krok po kroku, przejdź do tematu [Deactivate and delete your StorSimple device](storsimple-8000-deactivate-and-delete-device.md) (Dezaktywacja i usuwanie urządzenia StorSimple).

Gdy tylko urządzenie w chmurze zostanie wyświetlone jako dezaktywowane w bloku usługi Menedżer urządzeń StorSimple, możesz je usunąć z listy urządzeń w bloku **Urządzenia**.

### <a name="start-stop-and-restart-a-cloud-appliance"></a>Uruchamianie, zatrzymywanie i ponowne uruchamianie urządzenia w chmurze
Inaczej niż w przypadku urządzenia fizycznego StorSimple w urządzeniu StorSimple w chmurze nie ma przycisku włączania zasilania. Mogą jednak wystąpić sytuacje, w których trzeba będzie zatrzymać i ponownie uruchomić urządzenie w chmurze.

Najprościej jest uruchomić, zatrzymać i ponownie uruchomić urządzenie w chmurze za pośrednictwem bloku usługi Virtual Machines. Przejdź do usługi Virtual Machines. Na liście maszyn wirtualnych zidentyfikuj maszynę wirtualną odpowiadającą urządzeniu w chmurze (o tej samej nazwie), a następnie kliknij nazwę maszyny wirtualnej. Po wyświetleniu bloku maszyny wirtualnej stanem urządzenia w chmurze jest **Uruchomiono**, ponieważ po utworzeniu jest ono domyślnie uruchamiane. Można uruchomić, zatrzymać i ponownie uruchomić maszynę wirtualną w dowolnym momencie.

[!INCLUDE [Stop and restart cloud appliance](../../includes/storsimple-8000-stop-restart-cloud-appliance.md)]

### <a name="reset-to-factory-defaults"></a>Resetowanie do domyślnych ustawień fabrycznych
Jeśli zdecydujesz się zacząć od nowa z urządzeniem w chmurze, dezaktywuj je i usuń, a następnie utwórz nowe.

## <a name="fail-over-to-the-cloud-appliance"></a>Przenoszenie urządzenia w chmurze do trybu failover
Odzyskiwanie po awarii jest jednym z kluczowych scenariuszy, do którego zostało przeznaczone urządzenie StorSimple w chmurze. W tym scenariuszu urządzenie fizyczne StorSimple lub całe centrum danych mogą nie być dostępne. Na szczęście operacje można przywrócić w innej lokalizacji za pomocą urządzenia w chmurze. Podczas odzyskiwania po awarii kontenery woluminów z urządzenia źródłowego zmieniają właściciela i zostają przekazane do urządzenia w chmurze.

Wymagania wstępne dotyczące odzyskiwania po awarii:

* Urządzenie w chmurze zostało utworzone i skonfigurowane.
* Wszystkie woluminy w kontenerze woluminów działają w trybie offline.
* Kontener woluminów przeniesiony do trybu failover ma skojarzoną migawkę w chmurze.

> [!NOTE]
> * W przypadku korzystania z urządzenia w chmurze jako urządzenia pomocniczego do odzyskiwania po awarii należy pamiętać, że urządzenie 8010 ma magazyn o pojemności 30 TB w usłudze Standard Storage, a urządzenie 8020 ma 64 TB w usłudze Premium Storage. Urządzenie 8020 w chmurze o większej pojemności może być bardziej odpowiednie do scenariusza odzyskiwania po awarii.

Aby zapoznać się z procedurą krok po kroku, przejdź do tematu dotyczącego [przechodzenia w tryb failover w urządzeniu w chmurze](storsimple-8000-device-failover-cloud-appliance.md).

## <a name="delete-the-cloud-appliance"></a>Usuwanie urządzenia w chmurze
Jeśli urządzenie StorSimple w chmurze zostało wcześniej skonfigurowane i było używane, ale teraz chcemy wyłączyć naliczanie opłat za obliczenia w ramach jego użycia, musimy zatrzymać urządzenie w chmurze. Zatrzymywanie urządzenia w chmurze powoduje cofnięcie przydziału maszyny wirtualnej. Po wykonaniu tej akcji opłaty związane z subskrypcją nie będą naliczane. Nadal będą jednak naliczane opłaty za magazyn dla dysków systemu operacyjnego i danych.

Aby zatrzymać naliczanie wszystkich opłat, należy usunąć urządzenie w chmurze. Aby usunąć kopie zapasowe utworzone przez urządzenie w chmurze, można dezaktywować lub usunąć urządzenie. Aby uzyskać więcej informacji, zobacz temat [Deactivate and delete a StorSimple device](storsimple-8000-deactivate-and-delete-device.md) (Dezaktywacja i usuwanie urządzenia StorSimple).

[!INCLUDE [Delete a cloud appliance](../../includes/storsimple-8000-delete-cloud-appliance.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>Rozwiązywanie problemów z błędami łączności internetowej
Jeśli podczas tworzenia urządzenia w chmurze nie ma łączności z Internetem, krok związany z tworzeniem kończy się niepowodzeniem. Aby rozwiązać problemy w przypadku awarii wynikających z łączności z Internetem, wykonaj poniższe czynności w witrynie Azure Portal:

1. [Utwórz maszynę wirtualną systemu Windows w witrynie Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal). Ta maszyna wirtualna powinna używać tego samego konta magazynu, sieci wirtualnej i podsieci, które są używane przez urządzenie w chmurze. Jeśli istnieje host systemu Windows Server na platformie Azure używający tego samego konta magazynu, sieci wirtualnej i podsieci, możesz również użyć go, aby rozwiązywać problemy z łącznością z Internetem.
2. Zdalnie zaloguj się do maszyny wirtualnej utworzonej w poprzednim kroku.
3. Otwórz okno poleceń w ramach maszyny wirtualnej (naciśnij klawisze Win + R, a następnie wpisz polecenie `cmd`).
4. W wierszu polecenia wpisz następujące polecenie.

    `nslookup windows.net`
5. Jeśli narzędzie `nslookup` ulegnie awarii, oznacza to, że błąd łączności z Internetem uniemożliwia zarejestrowanie urządzenia w chmurze w usłudze Menedżer urządzeń StorSimple.
6. Wprowadź wymagane zmiany w sieci wirtualnej, aby zapewnić, że urządzenie w chmurze może uzyskać dostęp do witryn platformy Azure, na przykład _windows.net_.

## <a name="next-steps"></a>Następne kroki
* Informacje na temat [używania usługi Menedżer urządzeń StorSimple do zarządzania urządzeniem w chmurze](storsimple-8000-manager-service-administration.md).
* Zapoznanie się ze sposobem [przywracania woluminu StorSimple z zestawu kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md).
