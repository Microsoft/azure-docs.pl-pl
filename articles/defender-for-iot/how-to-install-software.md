---
title: Instalacja usługi Defender dla IoT
description: Dowiedz się, jak zainstalować czujnik i lokalną konsolę zarządzania na Azure Defender dla IoT.
ms.date: 4/20/2021
ms.topic: how-to
ms.openlocfilehash: e8366a3408e64d95e6c4d50e3ddef84309b4e8e5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829737"
---
# <a name="defender-for-iot-installation"></a>Instalacja usługi Defender dla IoT

W tym artykule opisano sposób instalowania następujących elementów Azure Defender dla IoT:

- **Czujnik:** czujniki usługi Defender dla IoT zbierają ruch sieciowy ICS przy użyciu pasywnego monitorowania (bez agenta). Czujniki pasywne i nieumyślne mają zero wpływu na sieci i urządzenia OT i IoT. Czujnik łączy się z portem SPAN lub interfejsem TAP sieci i natychmiast rozpoczyna monitorowanie sieci. Wykrycia są wyświetlane w konsoli czujników. Można tam wyświetlać, badać i analizować je na mapie sieci, w spisie urządzeń i w szerokim zakresie raportów. Przykłady obejmują raporty oceny ryzyka, zapytania wyszukiwania danych i wektory ataków. Dowiedz się więcej o możliwościach czujników w podręczniku użytkownika [usługi Defender dla czujników IoT (pobieranie bezpośrednie).](./getting-started.md)

- **Lokalna konsola zarządzania:** lokalna konsola zarządzania umożliwia zarządzanie urządzeniami, zarządzanie ryzykiem i zarządzanie lukami w zabezpieczeniach. Można jej również używać do monitorowania zagrożeń i reagowania na zdarzenia w całym przedsiębiorstwie. Zapewnia ujednolicony widok wszystkich urządzeń sieciowych, kluczowych wskaźników ryzyka IoT i OT oraz alertów wykrytych w obiektach, w których są wdrażane czujniki. Lokalna konsola zarządzania umożliwia wyświetlanie czujników w sieciach z dostępem bezprzewodowym i zarządzanie nimi.

Ten artykuł obejmuje następujące informacje dotyczące instalacji:

  - **Sprzęt:** Szczegóły urządzeń fizycznych Dell i HPE.

  - **Oprogramowanie:** Instalacja oprogramowania konsoli zarządzania czujnika i lokalnej konsoli zarządzania.

  - **Urządzenia wirtualne:** Szczegóły maszyny wirtualnej i instalacja oprogramowania.

Po zakończeniu instalacji podłącz czujnik do sieci.

## <a name="about-defender-for-iot-appliances"></a>Informacje o uciece usługi Defender dla urządzeń IoT 

Poniższe sekcje zawierają informacje o urządzeniach czujników usługi Defender dla IoT i urządzeniu lokalnej konsoli zarządzania usługi Defender dla IoT.

### <a name="physical-appliances"></a>Urządzenia fizyczne

Czujnik urządzenia usługi Defender dla IoT łączy się z portem SPAN lub sieciowym interfejsem TAP i natychmiast rozpoczyna zbieranie ruchu sieciowego ICS przy użyciu pasywnego monitorowania (bez agenta). Ten proces nie ma wpływu na sieci i urządzenia OT, ponieważ nie znajduje się w ścieżce danych i nie skanuje aktywnie urządzeń OT.

Dostępne są następujące urządzenia do montowania w stojaku:

| **Typ wdrożenia** | **Firmowe** | **Przedsiębiorstwo** | **SMB** | **Linia** |
|--|--|--|--|--|
| **Model** | HPE ProLiant DL360 | Dell PowerEdge R340 XL | HPE ProLiant DL20 | HPE ProLiant DL20 |
| **Porty monitorowania** | do 15 RJ45 lub 8 OPT | do 9 RJ45 lub 6 OPT | do 8 RJ45 lub 6 OPT | 4 RJ45 |
| **Maksymalna przepustowość\*** | 3 Gb/s | 1 Gb/s | 1 Gb/s | 100 Mb/s |
| **Maksymalna liczba chronionych urządzeń** | 30,000 | 10 000 | 15 000 | 1000 |

*Maksymalna przepustowość może się różnić w zależności od dystrybucji protokołu.

### <a name="virtual-appliances"></a>Urządzenie wirtualne

Dostępne są następujące urządzenia wirtualne:

| **Typ wdrożenia** | **Przedsiębiorstwo** | **SMB** | **Linia** |
|--|--|--|--|
| **Opis** | Urządzenie wirtualne dla wdrożeń w przedsiębiorstwie | Urządzenie wirtualne dla wdrożeń SMB | Urządzenie wirtualne do wdrożeń liniowych |
| **Maksymalna przepustowość\*** | 150 Mb/s | 15 Mb/s | 3 Mb/s |
| **Maksymalna liczba chronionych urządzeń** | 3000 | 300 | 100 |
| **Typ wdrożenia** | Przedsiębiorstwa | SMB | Linia |
| **Opis** | Urządzenie wirtualne dla wdrożeń w przedsiębiorstwie | Urządzenie wirtualne dla wdrożeń SMB | Urządzenie wirtualne do wdrożeń liniowych |

*Maksymalna przepustowość może się różnić w zależności od dystrybucji protokołu.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>Specyfikacje sprzętowe lokalnej konsoli zarządzania

 | Element | Opis |
 |----|--|
 **Opis** | W architekturze wielowarstwowej lokalna konsola zarządzania zapewnia widoczność i kontrolę w lokacjach rozproszonych geograficznie. Integruje się ona ze stosami zabezpieczeń SOC, w tym systemami SIEM, systemami biletów, zaporami nowej generacji, bezpiecznymi platformami dostępu zdalnego i piaskownicą złośliwego oprogramowania usługi Defender for IoT IoT. |
 **Typ wdrożenia** | Przedsiębiorstwa |
 **Typ urządzenia**  | Dell R340, MASZYNA WIRTUALNA |
 **Liczba zarządzanych czujników** | Nieograniczona liczba |

## <a name="prepare-for-the-installation"></a>Przygotowanie do instalacji

### <a name="access-the-iso-installation-image"></a>Uzyskiwanie dostępu do obrazu instalacji ISO

Obraz instalacji jest dostępny w portalu usługi Defender dla IoT.

Aby uzyskać dostęp do pliku:

1. Zaloguj się do konta usługi Defender dla IoT.

1. Przejdź do **strony Czujnik sieci** lub **Lokalna konsola zarządzania** i wybierz wersję do pobrania.

### <a name="install-from-dvd"></a>Instalowanie z dysku DVD

Przed instalacją upewnij się, że masz:

- Przenośny dysk DVD z łącznikiem USB.

- Obraz instalatora ISO.

Aby zainstalować:

1. Nagraj obraz na dysk DVD lub przygotuj dysk na kluczu. Podłącz przenośny dysk DVD do komputera, kliknij prawym przyciskiem myszy obraz ISO i wybierz **polecenie Nagraj na dysku.**

1. Podłącz dysk DVD lub dysk do klucza i skonfiguruj urządzenie do rozruchu z dysku DVD lub dysku na kluczu.

### <a name="install-from-disk-on-a-key"></a>Instalowanie z dysku na kluczu

Przed instalacją upewnij się, że masz:

  - Zainstalowana rufus.
  
  - Dysk na kluczu z portem USB w wersji 3.0 lub nowszej. Minimalny rozmiar to 4 GB.

  - Plik obrazu instalatora ISO.

Dysk klucza zostanie wymazyny w tym procesie.

Aby przygotować dysk na kluczu:

1. Uruchom rufus i wybierz pozycję **SENSOR ISO.**

1. Połącz dysk na kluczu z panelem przednim.

1. Ustaw system BIOS serwera do rozruchu z dysku USB.

## <a name="dell-poweredger340xl-installation"></a>Instalacja Dell PowerEdgeR340XL

Przed zainstalowaniem oprogramowania na urządzeniu Firmy Dell należy dostosować konfigurację systemu BIOS urządzenia:

  - [Zestawy Dell PowerEdge R340 Front Panel](#dell-poweredge-r340-front-panel) i [Dell PowerEdge R340 Back Panel](#dell-poweredge-r340-back-panel) zawierają opis paneli przednich i tylnych oraz informacje wymagane do instalacji, takie jak sterowniki i porty.

  - [Konfiguracja systemu BIOS firmy Dell](#dell-bios-configuration) zawiera informacje dotyczące sposobu nawiązywania połączenia z interfejsem zarządzania urządzeniem firmy Dell i konfigurowania systemu BIOS.

  - [W temacie Instalacja oprogramowania (Dell R340)](#software-installation-dell-r340) opisano procedurę wymaganą do zainstalowania oprogramowania czujnika usługi Defender dla IoT.

### <a name="dell-poweredge-r340xl-requirements"></a>Wymagania firmy Dell PowerEdge R340XL 

Aby zainstalować urządzenie Dell PowerEdge R340XL, potrzebne są:

- Licencja Enterprise dla programu Dell Remote Access Controller (iDrac)

- Plik XML konfiguracji systemu BIOS

- Wersje oprogramowania układowego serwera:

  - SYSTEM BIOS w wersji 2.1.6

  - iDrac w wersji 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Panel frontowy Dell PowerEdge R340

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Panel przedni Dell PowerEdge R340.":::

 1. Lewy panel sterowania 
 1. Dysk optyczne (opcjonalnie) 
 1. Prawy panel sterowania 
 1. Tag informacji 
 1. Napędy  

### <a name="dell-poweredge-r340-back-panel"></a>Panel zapasowy Dell PowerEdge R340

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Panel tylny Dell PowerEdge R340.":::

1. Port szeregowy 
1. Port karty sieciowej (Gb 1) 
1. Port karty sieciowej (Gb 1) 
1. PcIe o połowie wysokości 
1. Gniazdo karty rozszerzenia PCIe o pełnej wysokości 
1. Zasilacz 1 
1. Zasilacz 2 
1. Identyfikacja systemu 
1. Przycisk portu kabla wskaźnika stanu systemu (CMA) 
1. Port USB 3.0 (2) 
1. Dedykowany port sieciowy iDRAC9 
1. Port VGA 

### <a name="dell-bios-configuration"></a>Konfiguracja systemu BIOS firmy Dell

Konfiguracja systemu BIOS firmy Dell jest wymagana do dostosowania urządzenia Firmy Dell do pracy z oprogramowaniem.

Konfiguracja systemu BIOS jest wykonywana za pośrednictwem wstępnie zdefiniowanej konfiguracji. Plik jest dostępny w [Centrum pomocy.](https://help.cyberx-labs.com/)

Zaimportuj plik konfiguracji do urządzenia Dell. Przed użyciem pliku konfiguracji należy ustanowić komunikację między urządzeniem firmy Dell a komputerem zarządzania.

Urządzenie firmy Dell jest zarządzane przez zintegrowany program iDRAC z kontrolerem cyklu życia (LC). Lc jest osadzony na każdym serwerze Dell PowerEdge i zapewnia funkcje, które pomagają wdrażać, aktualizować, monitorować i konserwować urządzenia Dell PowerEdge.

Aby nawiązać komunikację między urządzeniem Dell i komputerem zarządzania, należy zdefiniować adres IP iDRAC i adres IP komputera zarządzania w tej samej podsieci.

Po nawiązaniu połączenia można skonfigurować system BIOS.

Aby skonfigurować system Bios firmy Dell:

1. [Konfigurowanie adresu IP iDRAC](#configure-idrac-ip-address)

1. [Importowanie pliku konfiguracji systemu BIOS](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>Konfigurowanie adresu IP iDRAC

1. Zasilanie czujnika.

1. Jeśli system operacyjny jest już zainstalowany, wybierz klawisz F2, aby wprowadzić konfigurację systemu BIOS.

1. Wybierz **pozycję Ustawienia iDRAC.**

1. Wybierz **pozycję Sieć.**

   > [!NOTE]
   > Podczas instalacji należy skonfigurować domyślny adres IP i hasło iDRAC wymienione w poniższych krokach. Po zakończeniu instalacji należy zmienić te definicje.

1. Zmień statyczny adres IPv4 na **10.100.100.250.**

1. Zmień statyczną maskę podsieci na **255.255.255.0.**

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Zrzut ekranu przedstawiający statyczną maskę podsieci.":::

1. Wybierz **pozycję Wstecz**  >  **Zakończ.**

#### <a name="import-the-bios-configuration-file"></a>Importowanie pliku konfiguracji systemu BIOS

W tym artykule opisano sposób konfigurowania systemu BIOS przy użyciu pliku konfiguracji.

1. Podłącz komputer za pomocą wstępnie skonfigurowanego statycznego adresu IP **10.100.100.200** do portu **iDRAC.**

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="Zrzut ekranu przedstawiający wstępnie skonfigurowany port adresu IP.":::

1. Otwórz przeglądarkę i wprowadź **adres 10.100.100.250,** aby nawiązać połączenie z interfejsem internetowym iDRAC.

1. Zaloguj się przy użyciu domyślnych uprawnień administratora firmy Dell:

   - Nazwa użytkownika: **katalog główny**

   - Hasło: **calvin**

1. Poświadczenia urządzenia to:

   - Nazwa użytkownika: **XXX**

   - Hasło: **XXX**

     Inicjowana jest operacja importowania profilu serwera.

     > [!NOTE]
     > Przed zaimportowaniu pliku upewnij się, że:
     > - Jesteś jedynym użytkownikiem, który jest obecnie połączony z programem iDRAC.
     > - System nie znajduje się w menu systemu BIOS.

1. Przejdź do **opcji Configuration** Server Configuration  >  **Profile (Profil konfiguracji serwera konfiguracji).** Ustaw następujące parametry:

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="Zrzut ekranu przedstawiający konfigurację profilu serwera.":::

   | Parametr | Konfigurowanie |
   |--|--|
   | Typ lokalizacji | Wybierz **pozycję Lokalne**. |
   | Ścieżka pliku | Wybierz **pozycję Wybierz plik** i dodaj plik XML konfiguracji. |
   | Importowanie składników | Wybierz **pozycję BIOS, NIC, RAID.** |
   | Maksymalny czas oczekiwania | Wybierz **pozycję 20 minut.** |

1. Wybierz pozycję **Importuj**.

1. Aby monitorować proces, przejdź do kolejki  >  **zadań konserwacji**.

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="Zrzut ekranu przedstawiający kolejkę zadań.":::

#### <a name="manually-configuring-bios"></a>Ręczne konfigurowanie systemu BIOS 

Należy ręcznie skonfigurować system BIOS urządzenia, jeśli:

- Nie zakupiliśmy urządzenia ze strzałki.

- Masz urządzenie, ale nie masz dostępu do pliku konfiguracji XML.

Po dostępie do systemu BIOS przejdź do **tematu Device Settings (Ustawienia urządzenia).**

Aby ręcznie skonfigurować:

1. Uzyskaj dostęp do systemu BIOS urządzenia bezpośrednio przy użyciu klawiatury i ekranu lub użyj interfejsu iDRAC.

   - Jeśli urządzenie nie jest urządzeniem usługi Defender dla IoT, otwórz przeglądarkę i przejdź do skonfigurowanego wcześniej adresu IP. Zaloguj się przy użyciu domyślnych uprawnień administratora firmy Dell. Użyj **wartości root** jako nazwy użytkownika i wartości **calvin** jako hasła.

   - Jeśli urządzenie jest urządzeniem usługi Defender dla IoT, zaloguj się przy użyciu xxx jako nazwy użytkownika i **XXX** jako hasła. 

1. Po dostępie do systemu BIOS przejdź do **ustawień urządzenia.**

1. Wybierz konfigurację sterowaną przez macierz RAID, wybierając pozycję **Integrated RAID controller 1: Dell PERC \<PERC H330 Adapter\> Configuration Utility**.

1. Wybierz **pozycję Zarządzanie konfiguracją.**

1. Wybierz **pozycję Utwórz dysk wirtualny.**

1. W polu **Wybierz poziom RAID** wybierz pozycję **RAID5.** W polu **Nazwa dysku wirtualnego** wprowadź **root** i wybierz pozycję **Dyski fizyczne.**

1. Wybierz **pozycję Zaznacz wszystko,** a następnie wybierz **pozycję Zastosuj zmiany**

1. Wybierz przycisk **OK**.

1. Przewiń w dół i wybierz **pozycję Utwórz dysk wirtualny.**

1. Zaznacz pole **wyboru Potwierdź,** a następnie wybierz pozycję **Tak.**

1. Wybierz przycisk **OK**.

1. Wróć do ekranu głównego i wybierz pozycję **System BIOS.**

1. Wybierz **pozycję Ustawienia rozruchu.**

1. W przypadku **opcji Tryb rozruchu** wybierz pozycję **BIOS.**

1. Wybierz **pozycję Wstecz,** a następnie wybierz **pozycję Zakończ,** aby zamknąć ustawienia systemu BIOS.

### <a name="software-installation-dell-r340"></a>Instalacja oprogramowania (Dell R340)

Proces instalacji trwa około 20 minut. Po instalacji system jest uruchamiany ponownie kilka razy.

Aby zainstalować:

1. Sprawdź, czy nośnik wersji został zainstalowany na urządzeniu w jeden z następujących sposobów:

   - Połącz zewnętrzny dysk CD lub dysk na kluczu z wydaniem .

   - Zainstaluj obraz ISO przy użyciu programu iDRAC. Po zalogowaniu się do środowiska iDRAC wybierz konsolę wirtualną, a następnie wybierz **pozycję Nośniki wirtualne.**

1. W sekcji **Mapowanie dysku CD/DVD** wybierz pozycję **Wybierz plik**.

1. Wybierz plik obrazu ISO wersji dla tej wersji z otwartego okna dialogowego.

1. Wybierz przycisk **Mapuj** urządzenie.

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Zrzut ekranu przedstawiający zamapowane urządzenie.":::

1. Nośnik jest zainstalowany. Wybierz pozycję **Zamknij**.

1. Uruchom urządzenie. Jeśli używasz serwera iDRAC, możesz ponownie uruchomić serwery, wybierając przycisk **Kontrolka consul.** Następnie w **makrach klawiatury** wybierz przycisk **Zastosuj,** który spowoduje uruchomienie sekwencji Ctrl+Alt+Delete.

1. Wybierz pozycję English ( **Angielski).**

1. Wybierz **pozycję SENSOR-RELEASE- \<version\> Enterprise.**

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Wybierz wersję czujnika i typ przedsiębiorstwa.":::   

1. Zdefiniuj profil urządzenia i właściwości sieci:

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Zrzut ekranu przedstawiający profil urządzenia i właściwości sieci.":::   

   | Parametr | Konfigurowanie |
   |--|--|
   | **Profil sprzętu** | **Przedsiębiorstwo** |
   | **Interfejs zarządzania** | **zajmij się 1** |
   | **Parametry sieci (dostarczane przez klienta)** | - |
   |**adres IP sieci zarządzania:** | - |
   | **maska podsieci:** | - |
   | **nazwa hosta urządzenia:** | - |
   | **Dns:** | - |
   | **domyślny adres IP bramy:** | - |
   | **interfejsy wejściowe:** |  System generuje listę interfejsów wejściowych. Aby dublować interfejsy wejściowe, skopiuj wszystkie elementy na liście za pomocą separatora przecinka. Nie trzeba konfigurować interfejsu mostka. Ta opcja jest używana tylko w specjalnych przypadkach użycia. |

1. Po około 10 minutach zostaną wyświetlone dwa zestawy poświadczeń. Jedno z nich jest **dla użytkownika cyberx,** a jedno dla użytkownika **pomocy** technicznej.  

1. Zapisz identyfikator urządzenia i hasła. Te poświadczenia będą potrzebne do uzyskania dostępu do platformy przy pierwszym jej użyciu.

1. Wybierz **klawisz Enter,** aby kontynuować.

## <a name="hpe-proliant-dl20-installation"></a>Instalacja HPE ProLiant DL20

W tym artykule opisano proces instalacji hpe ProLiant DL20, który obejmuje następujące kroki:

  - Włącz dostęp zdalny i zaktualizuj domyślne hasło administratora.
  - Konfigurowanie ustawień systemu BIOS i RAID.
  - Zainstaluj oprogramowanie.

### <a name="about-the-installation"></a>Informacje o instalacji

  - Można zainstalować urządzenia przedsiębiorstwa i SMB. Proces instalacji jest identyczny dla obu typów urządzeń, z wyjątkiem konfiguracji macierzy.
  - Zostanie podany domyślny użytkownik administracyjny. Zalecamy zmianę hasła podczas procesu konfiguracji sieci.
  - Podczas procesu konfiguracji sieci skonfigurujesz port iLO na porcie sieciowym 1.
  - Proces instalacji trwa około 20 minut. Po instalacji system jest uruchamiany ponownie kilka razy.

### <a name="hpe-proliant-dl20-front-panel"></a>Panel przedni HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="Panel przedni HPE ProLiant DL20.":::

### <a name="hpe-proliant-dl20-back-panel"></a>Panel zapasowy HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="Back panel zestawu HPE ProLiant DL20.":::

### <a name="enable-remote-access-and-update-the-password"></a>Włączanie dostępu zdalnego i aktualizowanie hasła

Użyj poniższej procedury, aby skonfigurować opcje sieci i zaktualizować domyślne hasło.

Aby włączyć i zaktualizować hasło:

1. Podłącz ekran i klawiaturę do urządzenia HP, włącz urządzenie i naciśnij **klawisz F9.**

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Zrzut ekranu przedstawiający okno HPE ProLiant.":::

1. Przejdź do **opcji System Utilities** System Configuration iLO 5 Configuration Utility Network Options (Konfiguracja systemu narzędzi  >    >  **systemowych systemu narzędzi systemowych iLO 5 Configuration Utility** Network  >  **Options).**

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Zrzut ekranu przedstawiający okno Konfiguracja systemu.":::

    1.  Wybierz **pozycję Shared Network Port-LOM (Wspólny port sieciowy LOM)** w polu Karta **interfejsu** sieciowego.
    
    1.  Wyłącz protokół DHCP.
    
    1.  Wprowadź adres IP, maskę podsieci i adres IP bramy.

1. Wybierz **klawisz F10: Zapisz**.

1. Wybierz **pozycję Esc,** aby wrócić do narzędzia **konfiguracji iLO 5,** a następnie wybierz **pozycję Zarządzanie użytkownikami.**

1. Wybierz **pozycję Edytuj/Usuń użytkownika.** Administrator jest jedynym domyślnym użytkownikiem zdefiniowanym. 

1. Zmień domyślne hasło i wybierz klawisz **F10: Zapisz**.

### <a name="configure-the-hpe-bios"></a>Konfigurowanie systemu BIOS HPE

W poniższej procedurze opisano sposób konfigurowania systemu BIOS HPE dla urządzeń przedsiębiorstwa i SMB.

Aby skonfigurować system BIOS HPE:

1. Wybierz **pozycję Konfiguracja systemu** narzędzi  >    >  **systemowych BIOS/konfiguracja platformy (RBSU).**

1. W **formularzu Konfiguracja systemu BIOS/platformy (RBSU)** wybierz **pozycję Opcje rozruchu.**

1. Zmień **tryb rozruchu** na **starszy tryb BIOS,** a następnie wybierz **pozycję F10: Zapisz.**

1. Wybierz dwukrotnie pozycję **Esc,** aby **zamknąć formularz Konfiguracja** systemu.

#### <a name="for-the-enterprise-appliance"></a>Dla urządzenia przedsiębiorstwa

1. Wybierz **pozycję Embedded RAID 1: HPE Smart Array P408i-a SR Gen 10** Array  >  **Configuration** Create  >  **Array**.

1. W **formularzu Tworzenie** tablicy wybierz wszystkie opcje. Dla urządzenia przedsiębiorstwa są dostępne **trzy** opcje.

#### <a name="for-the-smb-appliance"></a>Dla urządzenia SMB

1. Wybierz **pozycję Embedded RAID 1: HPE Smart Array P208i-a SR Gen 10** Array  >  **Configuration**  >  **Create Array**.

1. Wybierz **pozycję Przejdź do następnego formularza.**

1. W **formularzu Ustaw poziom RAID** ustaw poziom **RAID 5** dla wdrożeń przedsiębiorstwa i **RAID 1** dla wdrożeń SMB.

1. Wybierz **pozycję Przejdź do następnego formularza.**

1. W **formularzu Etykieta dysku** logicznego wprowadź **dysk logiczny 1**.

1. Wybierz **pozycję Prześlij zmiany.**

1. W **formularzu Prześlij** wybierz pozycję **Powrót do menu głównego.**

1. Naciśnij **klawisz F10: Zapisz,** a następnie naciśnij dwukrotnie klawisz **Esc.**

1. W **oknie Narzędzia systemowe** wybierz **pozycję Menu rozruchu tylko jeden raz.**

1. W **formularzu Menu jednego rozruchu** wybierz pozycję Starsza wersja systemu **BIOS One-Time menu rozruchu.**

1. Zostaną **wyświetlone okna Rozruch w starszej** wersji i **Przesłonięcie** rozruchu. Wybierz opcję zastąpienia rozruchu; na przykład do dysku CD-ROM, dysku USB, dysku TWARDEgo lub powłoki UEFI.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Zrzut ekranu przedstawiający pierwsze okno przesłonięcia rozruchu.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Zrzut ekranu przedstawiający drugie okno przesłonięcia rozruchu.":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Instalacja oprogramowania (urządzenie HPE ProLiant DL20)

Proces instalacji trwa około 20 minut. Po instalacji system jest uruchamiany ponownie kilka razy.

Aby zainstalować oprogramowanie:

1. Połącz ekran i klawiaturę z urządzeniem, a następnie połącz się z interfejsem wiersza polecenia.

1. Połącz zewnętrzny dysk CD lub dysk z kluczem za  pomocą obrazu ISO pobranego ze strony Aktualizacje w portalu usługi Defender dla IoT.

1. Uruchom urządzenie.

1. Wybierz pozycję English ( **Angielski).**

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="Wybór języka angielskiego w oknie interfejsu wiersza polecenia.":::

1. Wybierz **pozycję SENSOR-RELEASE- <version> Enterprise.**

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Zrzut ekranu przedstawiający ekran wybierania wersji.":::

1. W Kreatorze instalacji zdefiniuj właściwości profilu sprzętu i sieci:

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Zrzut ekranu przedstawiający Kreatora instalacji.":::

    | Parametr | Konfigurowanie |
    | ----------| ------------- |
    | **Profil sprzętu** | Wybierz **pozycję Enterprise** lub Office **dla** wdrożeń SMB. |
    | **Interfejs zarządzania** | **zajmij się 2** |
    | **Domyślne parametry sieci (zazwyczaj parametry są dostarczane przez klienta)** | **adres IP sieci zarządzania:** <br/> <br/>**nazwa hosta urządzenia:** <br/>**Dns:** <br/>**domyślny adres IP bramy:**|
    | **interfejsy wejściowe:** | System generuje listę interfejsów wejściowych.<br/><br/>Aby zdu dublować interfejsy wejściowe, skopiuj wszystkie elementy przedstawione na liście z separatorem przecinka: **owie5, po3, owanych1, separator6, separator4**<br/><br/>**W przypadku HPE DL20: nie wyloguj się z listy z1, enp1s0f4u4 (interfejsy iLo)**<br/><br/>**BRIDGE:** nie ma potrzeby konfigurowania interfejsu mostka. Ta opcja jest używana tylko w specjalnych przypadkach użycia. Naciśnij klawisz **Enter**, aby kontynuować. |

1. Po około 10 minutach zostaną wyświetlone dwa zestawy poświadczeń. Jedna jest dla użytkownika **cyberx,** a jedna dla użytkownika **pomocy** technicznej.

1. Zapisz identyfikator urządzenia i hasła. Aby uzyskać dostęp do platformy po raz pierwszy, będą potrzebne poświadczenia.

1. Wybierz **klawisz Enter,** aby kontynuować.

## <a name="hpe-proliant-dl360-installation"></a>Instalacja HPE ProLiant DL360

  - Zostanie podany domyślny użytkownik administracyjny. Zalecamy zmianę hasła podczas konfigurowania sieci.

  - Podczas konfigurowania sieci skonfigurujesz port iLO.

  - Proces instalacji trwa około 20 minut. Po instalacji system jest uruchamiany ponownie kilka razy.

### <a name="hpe-proliant-dl360-front-panel"></a>HpE ProLiant DL360 front panel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="Panel przedni HPE ProLiant DL360.":::

### <a name="hpe-proliant-dl360-back-panel"></a>Panel zapasowy HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="Panel zapasowy HPE ProLiant DL360.":::

### <a name="enable-remote-access-and-update-the-password"></a>Włączanie dostępu zdalnego i aktualizowanie hasła

Zapoznaj się z poprzednimi sekcjami instalacji HPE ProLiant DL20:

  - "Włącz dostęp zdalny i zaktualizuj hasło"

  - "Konfigurowanie systemu BIOS HPE"

Konfiguracja przedsiębiorstwa jest identyczna.

> [!Note]
> W formularzu tablicy sprawdź, czy są wybrane wszystkie opcje.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>Instalacja zdalna iLO (z dysku wirtualnego)

Ta procedura opisuje instalację iLO z dysku wirtualnego.

Aby zainstalować:

1. Zaloguj się do konsoli iLO, a następnie kliknij prawym przyciskiem myszy ekran serwerów.

1. Wybierz pozycję **Konsola HTML5.**

1. W konsoli wybierz ikonę Cd (Dysk CD), a następnie wybierz opcję CD/DVD (Dysk CD/DVD).

1. Wybierz **pozycję Lokalny plik ISO.**

1. W oknie dialogowym wybierz odpowiedni plik ISO.

1. Przejdź do ikony po lewej stronie, wybierz **pozycję Power** i wybierz pozycję **Resetuj**.

1. Urządzenie zostanie uruchomione ponownie i uruchomi proces instalacji czujnika.

### <a name="software-installation-hpe-dl360"></a>Instalacja oprogramowania (HPE DL360)

Proces instalacji trwa około 20 minut. Po instalacji system jest uruchamiany ponownie kilka razy.

Aby zainstalować:

1. Połącz ekran i klawiaturę z urządzeniem, a następnie połącz się z interfejsem wiersza polecenia.

1. Połącz zewnętrzny dysk CD lub dysk z kluczem za  pomocą obrazu ISO pobranego ze strony Aktualizacje w portalu usługi Defender dla IoT.

1. Uruchom urządzenie.

1. Wybierz pozycję English ( **Angielski).**

1. Wybierz **pozycję SENSOR-RELEASE- <version> Enterprise.**

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Zrzut ekranu przedstawiający wybieranie wersji.":::

1. W Kreatorze instalacji zdefiniuj właściwości profilu urządzenia i sieci.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Zrzut ekranu przedstawiający Kreatora instalacji.":::

    | Parametr | Konfigurowanie |
    | ----------| ------------- |
    | **Profil sprzętu** | Wybierz **firmową .** |
    | **Interfejs zarządzania** | **zajmij się 2** |
    | **Domyślne parametry sieci (dostarczane przez klienta)** | **adres IP sieci zarządzania:** <br/>**maska podsieci:** <br/>**nazwa hosta urządzenia:** <br/>**Dns:** <br/>**domyślny adres IP bramy:**|
    | **interfejsy wejściowe:**  | System generuje listę interfejsów wejściowych.<br/><br/>Aby dublować interfejsy wejściowe, skopiuj wszystkie elementy na liście za pomocą separatora przecinka.<br/><br/> Nie trzeba konfigurować interfejsu mostka. Ta opcja jest używana tylko w specjalnych przypadkach użycia. |

1. Po około 10 minutach zostaną wyświetlone dwa zestawy poświadczeń. Jedno z nich jest **dla użytkownika cyberx,** a jedno dla użytkownika **pomocy** technicznej.

1. Zapisz identyfikator i hasła urządzenia. Te poświadczenia będą potrzebne do uzyskania dostępu do platformy po raz pierwszy.

1. Wybierz **klawisz Enter,** aby kontynuować.

## <a name="sensor-installation-for-the-virtual-appliance"></a>Instalacja czujnika dla urządzenia wirtualnego

Maszynę wirtualną dla czujnika usługi Defender dla IoT można wdrożyć w następujących architekturach:


| Architektura | Specyfikacje | Użycie | Komentarze |
|---|---|---|---|
| **Przedsiębiorstwo** | Procesor CPU: 8<br/>Pamięć: 32 G pamięci RAM<br/>HDD: 1800 GB | Środowisko produkcyjne | Domyślne i najczęściej spotykane |
| **Małe firmy** | Procesor CPU: 4 <br/>Pamięć: 8 GB pamięci RAM<br/>HDD: 500 GB | Środowiska testowe lub małe środowiska produkcyjne | -  |
| **Office** | Procesor CPU: 4<br/>Pamięć: 8 GB pamięci RAM<br/>HDD: 100 GB | Małe środowiska testowe | -  |

### <a name="prerequisites"></a>Wymagania wstępne

Lokalna konsola zarządzania obsługuje zarówno opcje wdrażania oprogramowania VMware, jak i funkcji Hyper-V. Przed rozpoczęciem instalacji upewnij się, że masz następujące elementy:

  - VMware (ESXi 5.5 lub nowszy) lub funkcja hypervisor funkcji Hyper-V (Windows 10 Pro lub Enterprise) zainstalowana i działa

  - Dostępne zasoby sprzętowe dla maszyny wirtualnej

  - Plik instalacyjny ISO czujnika Azure Defender dla IoT obrazu

Upewnij się, że funkcja hypervisor jest uruchomiona.

### <a name="create-the-virtual-machine-esxi"></a>Tworzenie maszyny wirtualnej (ESXi)

1. Zaloguj się do aplikacji ESXi, wybierz odpowiedni **magazyn** danych, a następnie wybierz **pozycję Przeglądarka magazynu danych.**

1. **Przekaż** obraz i wybierz pozycję **Zamknij.**

1. Przejdź do **Virtual Machines**, a następnie wybierz pozycję **Utwórz/zarejestruj maszynę wirtualną.**

1. Wybierz **pozycję Utwórz nową maszynę wirtualną,** a następnie wybierz pozycję **Dalej.**

1. Dodaj nazwę czujnika i wybierz:

   - Zgodność: **&lt; najnowsza wersja &gt; ESXi**

   - Rodzina systemów operacyjnych gościa: **Linux**

   - Wersja systemu operacyjnego **gościa: Ubuntu Linux (64-bitowa)**

1. Wybierz opcję **Dalej**.

1. Wybierz odpowiedni magazyn danych, a następnie wybierz pozycję **Dalej.**

1. Zmień parametry sprzętu wirtualnego zgodnie z wymaganą architekturą.

1. W **przypadku stacji dysków CD/DVD 1** wybierz pozycję **Plik ISO** magazynu danych, a następnie wybierz przekazany wcześniej plik ISO.

1. Wybierz pozycje **Dalej** > **Zakończ**.

### <a name="create-the-virtual-machine-hyper-v"></a>Tworzenie maszyny wirtualnej (Hyper-V)

Ta procedura opisuje sposób tworzenia maszyny wirtualnej przy użyciu funkcji Hyper-V.

Aby utworzyć maszynę wirtualną:

1. Utwórz dysk wirtualny w Menedżerze funkcji Hyper-V.

1. Wybierz **format = VHDX.**

1. Wybierz **typ = Dynamiczne rozwijanie**.

1. Wprowadź nazwę i lokalizację dysku VHD.

1. Wprowadź wymagany rozmiar (zgodnie z architekturą).   

1. Przejrzyj podsumowanie i wybierz pozycję **Zakończ.**

1. W menu **Akcje** utwórz nową maszynę wirtualną.

1. Wprowadź nazwę maszyny wirtualnej.

1. Wybierz **pozycję Określ**  >  **generację 1. generacji.**

1. Określ alokację pamięci (zgodnie z architekturą) i zaznacz pole wyboru dla pamięci dynamicznej.

1. Skonfiguruj adapter sieci zgodnie z topologią sieci serwera.

1. Połącz utworzony wcześniej dysk VHDX z maszyną wirtualną.

1. Przejrzyj podsumowanie i wybierz pozycję **Zakończ.**

1. Kliknij prawym przyciskiem myszy nową maszynę wirtualną i wybierz pozycję **Ustawienia.**

1. Wybierz **pozycję Dodaj sprzęt** i dodaj nową kartę sieciową.

1. Wybierz przełącznik wirtualny, który będzie łączyć się z siecią zarządzania czujnikami.

1. Przydzielanie zasobów procesora CPU (zgodnie z architekturą).

1. Podłącz obraz ISO konsoli zarządzania do wirtualnej stacji dysków DVD.

1. Uruchom maszynę wirtualną.

2. W menu **Akcje** wybierz pozycję **Połącz,** aby kontynuować instalację oprogramowania.

### <a name="software-installation-esxi-and-hyper-v"></a>Instalacja oprogramowania (ESXi i Hyper-V)

W tej sekcji opisano instalację oprogramowania ESXi i funkcji Hyper-V.

Aby zainstalować:

1. Otwórz konsolę maszyny wirtualnej.

1. Maszyna wirtualna będzie uruchamiana z obrazu ISO, a zostanie wyświetlony ekran wyboru języka. Wybierz pozycję English ( **Angielski).**

1. Wybierz wymaganą architekturę.

1. Zdefiniuj profil urządzenia i właściwości sieci:

    | Parametr | Konfigurowanie |
    | ----------| ------------- |
    | **Profil sprzętu** | &lt;wymagana architektura&gt; |
    | **Interfejs zarządzania** | **ens192** |
    | **Parametry sieci (dostarczane przez klienta)** | **adres IP sieci zarządzania:** <br/>**maska podsieci:** <br/>**nazwa hosta urządzenia:** <br/>**Dns:** <br/>**brama domyślna:** <br/>**interfejsy wejściowe:**|
    | **interfejsy mostka:** | Nie ma potrzeby konfigurowania interfejsu mostka. Ta opcja dotyczy tylko specjalnych przypadków użycia. |

1. Wprowadź **Y,** aby zaakceptować ustawienia.

1. Poświadczenia logowania są generowane i prezentowane automatycznie. Skopiuj nazwę użytkownika i hasło w bezpiecznym miejscu, ponieważ są one wymagane do logowania i administrowania.

      - **Pomoc** techniczna: użytkownik administracyjny do zarządzania użytkownikami.

      - **CyberX:** odpowiednik katalogu głównego do uzyskiwania dostępu do urządzenia.

1. Urządzenie zostanie uruchomione ponownie.

1. Uzyskaj dostęp do konsoli zarządzania za pośrednictwem wcześniej skonfigurowanego adresu IP: `https://ip_address` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Zrzut ekranu przedstawiający dostęp do konsoli zarządzania.":::

## <a name="on-premises-management-console-installation"></a>Instalacja lokalnej konsoli zarządzania

Przed zainstalowaniem oprogramowania na urządzeniu należy dostosować konfigurację systemu BIOS urządzenia:

### <a name="bios-configuration"></a>Konfiguracja systemu BIOS

Aby skonfigurować system BIOS dla urządzenia:

1. [Włącz dostęp zdalny i zaktualizuj hasło.](#enable-remote-access-and-update-the-password)

1. [Skonfiguruj system BIOS](#configure-the-hpe-bios).

### <a name="software-installation"></a>Instalacja oprogramowania

Proces instalacji trwa około 20 minut. Po instalacji system jest uruchamiany ponownie kilka razy. 

Podczas procesu instalacji można dodać dodatkową kartę sieciową. Jeśli nie chcesz instalować pomocniczej karty sieciowej podczas instalacji, możesz dodać [dodatkową](#add-a-secondary-nic) kartę sieciową w późniejszym czasie. 

Aby zainstalować oprogramowanie:

1. Wybierz preferowany język procesu instalacji.

   :::image type="content" source="media/tutorial-install-components/on-prem-language-select.png" alt-text="Wybierz preferowany język procesu instalacji.":::     

1. Wybierz **pozycję \<version\> \<deployment type\> MANAGEMENT-RELEASE-**.

   :::image type="content" source="media/tutorial-install-components/on-prem-install-screen.png" alt-text="Wybierz swoją wersję.":::   

1. W Kreatorze instalacji zdefiniuj właściwości sieci:

   :::image type="content" source="media/tutorial-install-components/on-prem-first-steps-install.png" alt-text="Zrzut ekranu przedstawiający profil urządzenia.":::   

   | Parametr | Konfigurowanie |
   |--|--|
   | **konfigurowanie interfejsu sieciowego zarządzania** | Dell: **eth0, eth1** <br /> Dla HP: **enu1, enu2** <br /> lub <br />**możliwa wartość** |
   | **Skonfiguruj adres IP sieci zarządzania:** | **Adres IP dostarczony przez klienta** |
   | **Skonfiguruj maskę podsieci:** | **Adres IP dostarczony przez klienta** |
   | **Konfigurowanie systemu DNS:** | **Adres IP dostarczony przez klienta** |
   | **Skonfiguruj domyślny adres IP bramy:** | **Adres IP dostarczony przez klienta** |
   
1. **(Opcjonalnie)** Jeśli chcesz zainstalować dodatkową kartę sieciową (NIC), zdefiniuj następujący profil urządzenia i właściwości sieci:

    :::image type="content" source="media/tutorial-install-components/on-prem-secondary-nic-install.png" alt-text="Zrzut ekranu przedstawiający pytania dotyczące instalacji dodatkowej karty sieciowej.":::

   | Parametr | Konfigurowanie |
   |--|--|
   | **Konfigurowanie interfejsu monitorowania czujnika (opcjonalnie):** | **eth1** lub możliwa **wartość** |
   | **Skonfiguruj adres IP dla interfejsu monitorowania czujnika:** | **Adres IP dostarczony przez klienta** |
   | **Skonfiguruj maskę podsieci dla interfejsu monitorowania czujnika:** | **Adres IP dostarczony przez klienta** |

1. Zaakceptuj rozliczenia i kontynuuj, wpisując `Y` . 

1. Po około 10 minutach zostaną wyświetlone dwa zestawy poświadczeń. Jedno z nich jest **dla użytkownika cyberx,** a jedno dla użytkownika **pomocy** technicznej.

   :::image type="content" source="media/tutorial-install-components/credentials-screen.png" alt-text="Skopiuj te poświadczenia, ponieważ nie zostaną one ponownie przedstawione.":::  

   Zapisz nazwy użytkowników i hasła. Te poświadczenia będą potrzebne do uzyskania dostępu do platformy przy pierwszym jej użyciu.

1. Wybierz **klawisz Enter,** aby kontynuować.

Aby uzyskać informacje o tym, jak znaleźć port fizyczny na urządzeniu, zobacz [Znajdowanie portu](#find-your-port).

### <a name="add-a-secondary-nic"></a>Dodawanie pomocniczej karty sieciowej

Możesz zwiększyć bezpieczeństwo lokalnej konsoli zarządzania, dodając dodatkową kartę sieciową. Dodając dodatkową kartę sieciową, będziesz mieć jedną dedykowaną dla użytkowników, a druga będzie obsługiwać konfigurację bramy dla sieci tras. Druga karta sieciowa jest przeznaczona dla wszystkich podłączonych czujników w zakresie adresów IP.

Obie karty sieciowe mają włączony interfejs użytkownika. Jeśli routing nie jest konieczny, wszystkie funkcje obsługiwane przez interfejs użytkownika będą dostępne na dodatkowej karty sieciowej. Wysoka dostępność będzie uruchamiana na pomocniczej karty sieciowej.

Jeśli zdecydujesz się nie wdrażać dodatkowej karty sieciowej, wszystkie funkcje będą dostępne za pośrednictwem podstawowej karty sieciowej. 

Jeśli lokalna konsola zarządzania została już skonfigurowana i chcesz dodać dodatkową kartę sieciową do lokalnej konsoli zarządzania, użyj następujących kroków:

1. Użyj polecenia ponownego konfigurowania sieci:

    ```bash
    sudo cyberx-management-network-reconfigure
    ```

1. Wprowadź następujące odpowiedzi na następujące pytania:

    :::image type="content" source="media/tutorial-install-components/network-reconfig-command.png" alt-text="Wprowadź następujące odpowiedzi, aby skonfigurować urządzenie.":::

    | Parametry | Odpowiedź na wprowadzenie |
    |--|--|
    | **Adres IP sieci zarządzania** | `N` |
    | **Maska podsieci** | `N` |
    | **DNS** | `N` |
    | **Domyślny adres IP bramy** | `N` |
    | **Interfejs monitorowania czujnika (opcjonalnie. Ma zastosowanie, gdy czujniki znajdują się w innym segmencie sieci. Aby uzyskać więcej informacji, zobacz Instrukcje dotyczące instalacji.**| `Y`, **wybierz możliwą wartość** |
    | **Adres IP interfejsu monitorowania czujników (dostępny dla czujników)** | `Y`, **adres IP dostarczony przez klienta**|
    | **Maska podsieci dla interfejsu monitorowania czujników (dostępna dla czujników)** | `Y`, **adres IP dostarczony przez klienta** |
    | **Hostname (Nazwa hosta)** | **dostarczone przez klienta** |

1. Przejrzyj wszystkie opcje i wprowadź , `Y` aby zaakceptować zmiany. System jest ponownie uruchamiany.

### <a name="find-your-port"></a>Znajdowanie portu

Jeśli masz problemy ze znalezieniem portu fizycznego na urządzeniu, możesz użyć następującego polecenia, aby:

```bash
sudo ethtool -p <port value> <time-in-seconds>
```

To polecenie spowoduje, że światło na porcie będzie migać przez określony czas. Na przykład wprowadzenie ciągu spowoduje, że port 1 będzie flashować przez 2 minuty, co pozwoli znaleźć port z `sudo ethtool -p eno1 120` tyłu urządzenia. 

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Urządzenie wirtualne: instalacja lokalnej konsoli zarządzania

Maszyna wirtualna lokalnej konsoli zarządzania obsługuje następujące architektury:

| Architektura | Specyfikacje | Użycie | 
|--|--|--|
| Przedsiębiorstwa <br/>(Domyślne i najczęściej spotykane) | Procesor CPU: 8 <br/>Pamięć: 32 G pamięci RAM<br/> HDD: 1,8 TB | Duże środowiska produkcyjne | 
| Przedsiębiorstwa | Procesor CPU: 4 <br/> Pamięć: 8 GB pamięci RAM<br/> HDD: 500 GB | Duże środowiska produkcyjne |
| Przedsiębiorstwa | Procesor CPU: 4 <br/>Pamięć: 8 GB pamięci RAM <br/> HDD: 100 GB | Małe środowiska testowe | 
   
### <a name="prerequisites"></a>Wymagania wstępne

Lokalna konsola zarządzania obsługuje zarówno opcje wdrażania oprogramowania VMware, jak i funkcji Hyper-V. Przed rozpoczęciem instalacji sprawdź następujące kwestie:

- Oprogramowanie VMware (ESXi 5.5 lub nowsze) lub funkcja hypervisor funkcji Hyper-V (Windows 10 Pro lub Enterprise) jest zainstalowana i działa.

- Zasoby sprzętowe są dostępne dla maszyny wirtualnej.

- Masz plik instalacyjny ISO lokalnej konsoli zarządzania.
    
- Funkcja hypervisor jest uruchomiona.

### <a name="create-the-virtual-machine-esxi"></a>Tworzenie maszyny wirtualnej (ESXi)

Aby utworzyć maszynę wirtualną (ESXi):

1. Zaloguj się do aplikacji ESXi, wybierz odpowiedni **magazyn** danych, a następnie wybierz **pozycję Przeglądarka magazynu danych.**

1. Przekaż obraz i wybierz pozycję **Zamknij.**

1. Przejdź do **Virtual Machines**.

1. Wybierz pozycję **Utwórz/zarejestruj maszynę wirtualną.**

1. Wybierz **pozycję Utwórz nową maszynę wirtualną,** a następnie wybierz **pozycję Dalej.**

1. Dodaj nazwę czujnika i wybierz:

   - Zgodności: \<latest ESXi version>

   - Rodzina systemów operacyjnych gościa: Linux

   - Wersja systemu operacyjnego gościa: Ubuntu Linux (64-bitowa)

1. Wybierz opcję **Dalej**.

1. Wybierz odpowiedni magazyn danych i wybierz pozycję **Dalej.**

1. Zmień parametry sprzętu wirtualnego zgodnie z wymaganą architekturą.

1. W **przypadku stacji dysków CD/DVD 1** wybierz pozycję **Plik ISO** magazynu danych, a następnie wybierz przekazany wcześniej plik ISO.

1. Wybierz pozycje **Dalej** > **Zakończ**.

### <a name="create-the-virtual-machine-hyper-v"></a>Tworzenie maszyny wirtualnej (Hyper-V)

Aby utworzyć maszynę wirtualną przy użyciu funkcji Hyper-V:

1. Utwórz dysk wirtualny w Menedżerze funkcji Hyper-V.

1. Wybierz format **VHDX.**

1. Wybierz opcję **Dalej**.

1. Wybierz typ **Dynamiczne rozwijanie**.

1. Wybierz opcję **Dalej**.

1. Wprowadź nazwę i lokalizację dysku VHD.

1. Wybierz opcję **Dalej**.

1. Wprowadź wymagany rozmiar (zgodnie z architekturą).

1. Wybierz opcję **Dalej**.

1. Przejrzyj podsumowanie i wybierz pozycję **Zakończ.**

1. W menu **Akcje** utwórz nową maszynę wirtualną.

1. Wybierz opcję **Dalej**.

1. Wprowadź nazwę maszyny wirtualnej.

1. Wybierz opcję **Dalej**.

1. Wybierz **pozycję Generacja** i ustaw ją na **generację 1.**

1. Wybierz opcję **Dalej**.

1. Określ alokację pamięci (zgodnie z architekturą) i zaznacz pole wyboru dla pamięci dynamicznej.

1. Wybierz opcję **Dalej**.

1. Skonfiguruj adapter sieci zgodnie z topologią sieci serwera.

1. Wybierz opcję **Dalej**.

1. Połącz utworzony wcześniej dysk VHDX z maszyną wirtualną.

1. Wybierz opcję **Dalej**.

1. Przejrzyj podsumowanie i wybierz pozycję **Zakończ.**

1. Kliknij prawym przyciskiem myszy nową maszynę wirtualną, a następnie wybierz pozycję **Ustawienia.**

1. Wybierz **pozycję Dodaj sprzęt** i dodaj nową kartę **sieciową**.

1. W **przypadku przełącznika** wirtualnego wybierz przełącznik, który będzie łączyć się z siecią zarządzania czujnikami.

1. Przydzielanie zasobów procesora CPU (zgodnie z architekturą).

1. Podłącz obraz ISO konsoli zarządzania do wirtualnej stacji dysków DVD.

1. Uruchom maszynę wirtualną.

1. W menu **Akcje** wybierz pozycję **Połącz,** aby kontynuować instalację oprogramowania.

### <a name="software-installation-esxi-and-hyper-v"></a>Instalacja oprogramowania (ESXi i Hyper-V)

Uruchomienie maszyny wirtualnej spowoduje rozpoczęcie procesu instalacji z obrazu ISO.

Aby zainstalować oprogramowanie:

1. Wybierz pozycję English ( **Angielski).**

1. Wybierz wymaganą architekturę dla wdrożenia.

1. Zdefiniuj interfejs sieciowy dla sieci zarządzania czujnikami: interfejs, adres IP, podsieć, serwer DNS i bramę domyślną.

1. Poświadczenia logowania są generowane automatycznie. Zapisz nazwę użytkownika i hasła. Te poświadczenia będą potrzebne do uzyskania dostępu do platformy przy pierwszym użyciu.

   Urządzenie zostanie następnie ponownie uruchomiony.

1. Uzyskaj dostęp do konsoli zarządzania za pośrednictwem wcześniej skonfigurowanego adresu IP: `<https://ip_address>` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="Zrzut ekranu przedstawiający ekran logowania konsoli zarządzania.":::

## <a name="post-installation-validation"></a>Weryfikacja po instalacji

Aby zweryfikować instalację urządzenia fizycznego, należy wykonać wiele testów. Ten sam proces weryfikacji ma zastosowanie do wszystkich typów urządzeń.

Przekonuj walidację przy użyciu graficznego interfejsu użytkownika lub interfejsu wiersza polecenia. Walidacja jest dostępna dla pomocy **technicznej użytkownika** i użytkownika **CyberX.**

Weryfikacja po instalacji musi obejmować następujące testy:

  - **Test sanity**: sprawdź, czy system jest uruchomiony.

  - **Wersja:** sprawdź, czy wersja jest poprawna.

  - **ifconfig:** sprawdź, czy wszystkie interfejsy wejściowe skonfigurowane podczas procesu instalacji są uruchomione.

### <a name="checking-system-health-by-using-the-gui"></a>Sprawdzanie kondycji systemu przy użyciu graficznego interfejsu użytkownika

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Zrzut ekranu przedstawiający sprawdzanie kondycji systemu.":::

#### <a name="sanity"></a>Poczytalności

- **Urządzenie:** uruchamia sprawdzanie sanity urządzenia. To samo sprawdzenie można wykonać za pomocą polecenia interfejsu wiersza polecenia `system-sanity` .

- **Wersja:** wyświetla wersję urządzenia.

- **Właściwości sieci:** wyświetla parametry sieci czujników.

#### <a name="redis"></a>Redis

- **Pamięć:** przedstawia ogólny obraz użycia pamięci, na przykład ilość użytej pamięci i ilość pozostały.

- **Najdłuższy klucz:** wyświetla najdłuższe klucze, które mogą powodować duże użycie pamięci.

#### <a name="system"></a>System

- **Dziennik podstawowy:** zawiera 500 ostatnich wierszy dziennika podstawowego, umożliwiając wyświetlanie ostatnich wierszy dziennika bez eksportowania całego dziennika systemu.

- **Menedżer zadań:** tłumaczy zadania, które pojawiają się w tabeli procesów, na następujące warstwy: 
  
  - Warstwa trwała (Redis) 
  - Warstwa gotówkowa (SQL)

- **Statystyka sieci:** wyświetla statystyki sieci.

- **TOP:** przedstawia tabelę procesów. Jest to polecenie systemu Linux, które zapewnia dynamiczny widok działającego systemu w czasie rzeczywistym.

- **Sprawdzanie pamięci kopii** zapasowej: zawiera stan pamięci kopii zapasowej, sprawdzając następujące kwestie:
  - Lokalizacja folderu kopii zapasowej 
  - Rozmiar folderu kopii zapasowej
  - Ograniczenia folderu kopii zapasowej
  - Kiedy miała miejsce ostatnia kopia zapasowa
  - Ilość miejsca na dodatkowe pliki kopii zapasowej

- **ifconfig:** wyświetla parametry interfejsów fizycznych urządzenia.

- **CyberX nload:** wyświetla ruch sieciowy i przepustowość przy użyciu testów z sześciu sekund.

- **Błędy z podstawowego dziennika:** wyświetla błędy z podstawowego pliku dziennika.

Aby uzyskać dostęp do narzędzia:

1. Zaloguj się do czujnika przy użyciu **poświadczeń** użytkownika pomocy technicznej.

1. Wybierz **pozycję Statystyka** systemu w **oknie Ustawienia** systemu.

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>Sprawdzanie kondycji systemu przy użyciu interfejsu wiersza polecenia

**Test 1. Sanity**

Sprawdź, czy system jest uruchomiony:

1. Połącz się z interfejsem wiersza polecenia za pomocą terminalu systemu Linux (na przykład PuTTY) i pomocy **technicznej użytkownika**.

1. Wprowadź `system sanity`.

1. Sprawdź, czy wszystkie usługi są zielone (uruchomione).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="Zrzut ekranu przedstawiający uruchomione usługi.":::

1. Sprawdź, **czy system jest w stanie UP! (prod)** w dolnej części ekranu.

**Test 2. Sprawdzanie wersji**

Sprawdź, czy jest używana poprawna wersja:

1. Połącz się z interfejsem wiersza polecenia za pomocą terminalu systemu Linux (na przykład PuTTY) i pomocy **technicznej użytkownika**.

1. Wprowadź `system version`.

1. Sprawdź, czy jest wyświetlana poprawna wersja.

**Test 3. Walidacja sieci**

Sprawdź, czy wszystkie interfejsy wejściowe skonfigurowane podczas procesu instalacji są uruchomione:

1. Połącz się z interfejsem wiersza polecenia za pomocą terminalu systemu Linux (na przykład PuTTY) i pomocy **technicznej użytkownika**.

1. Wprowadź `network list` (odpowiednik polecenia systemu Linux `ifconfig` ).

1. Sprawdź, czy są wyświetlane wymagane interfejsy wejściowe. Jeśli na przykład są zainstalowane dwie miedziane karty sieciowe czworokąt, lista powinna mieć 10 interfejsów.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Zrzut ekranu przedstawiający listę interfejsów.":::

**Test 4. Zarządzanie dostępem do interfejsu użytkownika**

Sprawdź, czy możesz uzyskać dostęp do internetowego graficznego interfejsu użytkownika konsoli:

1. Podłącz laptop za pomocą kabla Ethernet do portu zarządzania **(Gb1).**

1. Zdefiniuj adres karty sieciowej komputera przenośnego, aby był w tym samym zakresie co urządzenie.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Zrzut ekranu przedstawiający dostęp zarządzania do interfejsu użytkownika.":::

1. Wyślij polecenie ping do adresu IP urządzenia z komputera przenośnego, aby zweryfikować łączność (domyślnie: 10.100.10.1).

1. Otwórz przeglądarkę Chrome na laptopie i wprowadź adres IP urządzenia.

1. W **oknie Połączenie nie jest prywatne** wybierz pozycję **Zaawansowane** i kontynuuj.

1. Test jest pomyślny, gdy zostanie wyświetlony ekran logowania usługi Defender dla IoT.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Zrzut ekranu przedstawiający dostęp do konsoli zarządzania.":::

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="you-cant-connect-by-using-a-web-interface"></a>Nie można nawiązać połączenia przy użyciu interfejsu internetowego

1. Sprawdź, czy komputer, który próbujesz nawiązać połączenie, znajduje się w tej samej sieci co urządzenie.

1. Sprawdź, czy sieć graficznego interfejsu użytkownika jest podłączona do portu zarządzania.

1. Wyślij polecenie ping do adresu IP urządzenia. Jeśli nie ma polecenia ping:

   1. Podłącz monitor i klawiaturę do urządzenia.

   1. Użyj hasła **i** użytkownika pomocy technicznej, aby się zalogować.

   1. Użyj polecenia , `network list` aby wyświetlić bieżący adres IP.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="Zrzut ekranu przedstawiający listę sieci.":::

1. Jeśli parametry sieci są nieprawidłowo skonfigurowane, użyj następującej procedury, aby je zmienić:

   1. Użyj polecenia `network edit-settings` .

   1. Aby zmienić adres IP sieci zarządzania, wybierz **pozycję Y**.

   1. Aby zmienić maskę podsieci, wybierz **pozycję Y**.

   1. Aby zmienić system DNS, wybierz **pozycję Y**.

   1. Aby zmienić domyślny adres IP bramy, wybierz **pozycję Y**.

   1. W przypadku zmiany interfejsu wejściowego (tylko czujnik) wybierz **pozycję N**.

   1. Aby zastosować ustawienia, wybierz **pozycję Y**.

1. Po ponownym uruchomieniu połącz się przy użyciu poświadczeń użytkownika pomocy technicznej i użyj polecenia , aby `network list` sprawdzić, czy parametry zostały zmienione.

1. Spróbuj wysłać polecenie ping i ponownie nawiązać połączenie z graficznego interfejsu użytkownika.

### <a name="the-appliance-isnt-responding"></a>Urządzenie nie odpowiada

1. Podłącz monitor i klawiaturę do urządzenia lub za pomocą aplikacji PuTTY połącz się zdalnie z interfejsem wiersza polecenia.

1. Użyj **poświadczeń** użytkownika pomocy technicznej, aby się zalogować.

1. Użyj polecenia `system sanity` i sprawdź, czy wszystkie procesy są uruchomione.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Zrzut ekranu przedstawiający polecenie sanity systemu.":::

W przypadku innych problemów skontaktuj się z [Pomoc techniczna Microsoft](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>Dodatek A: dublowanie portu na przełączniku vSwitch (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>Konfigurowanie portu SPAN na istniejącym przełączniku vSwitch

Przełącznik vSwitch nie ma możliwości dublowania, ale można użyć obejścia w celu zaimplementowania portu SPAN.

Aby skonfigurować port SPAN:

1. Otwórz właściwości przełącznika wirtualnego.

1. Wybierz pozycję **Dodaj**.

1. Wybierz **pozycję Maszyna**  >  **wirtualna Dalej.**

1. Wstaw etykietę **sieciOWĄ SPAN Network,** wybierz pozycję **Wszystkie identyfikatory sieci VLAN,** a następnie wybierz pozycję  >   **Dalej.**

1. Wybierz pozycję **Zakończ**.

1. Wybierz **pozycję SPAN Network** > **Edytuj*.

1. Wybierz **pozycję** Zabezpieczenia i sprawdź, czy zasady Tryb **promiscuous (Tryb promiscuous)** są ustawione na wartość Accept mode **(Akceptuj** tryb).

1. Wybierz **przycisk OK,** a następnie wybierz **pozycję Zamknij,** aby zamknąć właściwości przełącznika wirtualnego.

1. Otwórz właściwości **maszyny wirtualnej XSense.**

1. W **przypadku karty sieciowej 2** wybierz sieć **SPAN.**

1. Wybierz przycisk **OK**.

1. Połącz się z czujnikiem i sprawdź, czy dublowanie działa.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>Dodatek B: Dostęp do czujników z lokalnej konsoli zarządzania

Możesz zwiększyć bezpieczeństwo systemu, uniemożliwiając bezpośredni dostęp użytkowników do czujnika. Zamiast tego użyj tunelowania serwera proxy, aby pozwolić użytkownikom na dostęp do czujnika z lokalnej konsoli zarządzania przy użyciu pojedynczej reguły zapory. Ta technika zawęża możliwość nieautoryzowanego dostępu do środowiska sieciowego poza czujnikiem. Środowisko użytkownika podczas logowania do czujnika pozostaje takie samo.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Zrzut ekranu przedstawiający dostęp do czujnika.":::

Aby włączyć tunelowanie:

1. Zaloguj się do interfejsu wiersza polecenia lokalnej konsoli  zarządzania przy użyciu poświadczeń **użytkownika cyberx** lub pomocy technicznej.

1. Wprowadź `sudo cyberx-management-tunnel-enable`.

1. Naciśnij klawisz **Enter**.

1. Wprowadź `--port 10000`.

### <a name="next-steps"></a>Następne kroki

[Konfigurowanie sieci](how-to-set-up-your-network.md)