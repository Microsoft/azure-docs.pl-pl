---
title: Instalacja usługi Defender for IoT
description: Dowiedz się, jak zainstalować czujnik i lokalną konsolę zarządzania dla usługi Azure Defender dla IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/2/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 49a0129ff26d4a12392066aa6304317d71fdb0f1
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247594"
---
# <a name="defender-for-iot-installation"></a>Instalacja usługi Defender for IoT

W tym artykule opisano sposób instalowania następujących elementów usługi Azure Defender dla IoT:

- **Czujnik**: usługa Defender for IoT zbiera dane o ruchu sieciowym za pomocą pasywnego (bezagentowego) monitorowania. Czujniki pasywne i nieinwazyjne nie mają wpływu na sieci i urządzenia IoT. Czujnik nawiązuje połączenie z portem SPAN lub NACIŚNIĘCIem sieci i natychmiast rozpoczyna monitorowanie sieci. Wykrycia są wyświetlane w konsoli czujnika. W tym miejscu możesz wyświetlać, badać i analizować je w mapie sieciowej, spisie urządzeń i wielu różnych raportach. Przykłady obejmują raporty oceny ryzyka, zapytania wyszukiwania danych i wektory ataków. Przeczytaj więcej na temat funkcji czujników w [podręczniku użytkownika czujnika usługi Defender for IoT (Pobieranie bezpośrednie)](./getting-started.md).

- **Lokalna Konsola zarządzania**: lokalna Konsola zarządzania umożliwia przeprowadzenie zarządzania urządzeniami, zarządzania ryzykiem i zarządzania lukami w zabezpieczeniach. Można go również używać do przeprowadzania monitorowania zagrożeń i reagowania na zdarzenia w całym przedsiębiorstwie. Zapewnia ujednolicony widok wszystkich urządzeń sieciowych, najważniejszych rzeczy oraz wskaźników ryzyka i wykrytych alertów w miejscach, w których są wdrażane czujniki. Lokalna Konsola zarządzania umożliwia wyświetlanie czujników w sieciach gapped i zarządzanie nimi.

W tym artykule omówiono następujące informacje dotyczące instalacji:

  - **Sprzęt:** Szczegóły urządzenia fizycznego firmy Dell i HPE.

  - **Oprogramowanie:** Instalowanie czujnika i lokalnej konsoli zarządzania.

  - **Urządzenia wirtualne:** Szczegóły maszyny wirtualnej i instalacja oprogramowania.

Po zakończeniu instalacji podłącz czujnik do sieci.

## <a name="about-defender-for-iot-appliances"></a>Informacje o usłudze Defender dla urządzeń IoT 

W poniższych sekcjach znajdują się informacje o urządzeniach czujnika IoT i urządzeniu dla usługi Defender for IoT w lokalnej konsoli zarządzania.

### <a name="physical-appliances"></a>Urządzenia fizyczne

Czujnik urządzenia usługi Defender for IoT łączy się z portem lub siecią obejmującą zakres, a następnie natychmiast rozpoczyna zbieranie ruchu sieciowego w usłudze ICS przy użyciu pasywnego (bezagentowego monitorowania). Ten proces ma zerowy wpływ na niedziałające sieci i urządzenia, ponieważ nie znajduje się w ścieżce danych i aktywnie nie skanuje urządzeń.

Dostępne są następujące urządzenia instalujące stojaki:

| **Typ wdrożenia** | **Firmowe** | **Przedsiębiorstwo** | **SMB** |  |
|--|--|--|--|--|
| **Model** | HPE ProLiant DL360 | Dell PowerEdge R340 XL | HPE ProLiant DL20 | HPE ProLiant DL20 |
| **Monitorowanie portów** | do 15 RJ45 lub 8 OPT | maksymalnie 9 RJ45 lub 6 OPT | do 8 RJ45 lub 6 OPT | 4 RJ45 |
| **Maksymalna przepustowość \** _ | 3 GB/s | 1 GB/s | 1 GB/s | 100 MB/s |
| _ *Maks. liczba chronionych urządzeń** | 30 000 | 10 000 | 15 000 | 1000 |

* Maksymalna pojemność przepustowości może się różnić w zależności od dystrybucji protokołu.

### <a name="virtual-appliances"></a>Urządzenie wirtualne

Dostępne są następujące urządzenia wirtualne:

| **Typ wdrożenia** | **Przedsiębiorstwo** | **SMB** | **Wiersz** |
|--|--|--|--|
| **Opis** | Wirtualne urządzenie do wdrożeń w przedsiębiorstwie | Wirtualne urządzenie do wdrażania protokołu SMB | Wirtualne urządzenie do wdrożeń liniowych |
| **Maksymalna przepustowość \** _ | 150 MB/s | 15 MB/s | 3 MB/s |
| _ *Maks. liczba chronionych urządzeń** | 3000 | 300 | 100 |
| **Typ wdrożenia** | Przedsiębiorstwa | SMB | Wiersz |
| **Opis** | Wirtualne urządzenie do wdrożeń w przedsiębiorstwie | Wirtualne urządzenie do wdrażania protokołu SMB | Wirtualne urządzenie do wdrożeń liniowych |

* Maksymalna pojemność przepustowości może się różnić w zależności od dystrybucji protokołu.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>Specyfikacje sprzętu dla lokalnej konsoli zarządzania

 | Element | Opis |
 |----|--|
 **Opis** | W architekturze wielowarstwowej lokalna Konsola zarządzania zapewnia widoczność i kontrolę w witrynach rozproszonych geograficznie. Integruje się z stosami zabezpieczeń SOC, w tym rozwiązań Siem, systemami biletów, zaporami nowej generacji, bezpiecznymi platformami dostępu zdalnego i piaskownicą programu Defender dla usługi IoT w przypadku złośliwego oprogramowania. |
 **Typ wdrożenia** | Przedsiębiorstwa |
 **Typ urządzenia**  | Dell R340, maszyna wirtualna |
 **Liczba czujników zarządzanych** | Nieograniczona liczba |

## <a name="prepare-for-the-installation"></a>Przygotowanie do instalacji

### <a name="access-the-iso-installation-image"></a>Dostęp do obrazu instalacji ISO

Obraz instalacji jest dostępny w portalu usługi Defender for IoT.

Aby uzyskać dostęp do pliku:

1. Zaloguj się do swojego konta usługi Defender for IoT.

2. Przejdź do strony **Czujnik sieci** lub **lokalna Konsola zarządzania** i wybierz wersję do pobrania.

### <a name="install-from-dvd"></a>Instalowanie z dysku DVD

Przed instalacją upewnij się, że masz:

- Przenośna stacja dysków DVD z łącznikiem USB.

- Obraz Instalatora ISO.

Aby zainstalować program:

1. Nagraj obraz na dysku DVD lub przygotuj dysk w kluczu. Podłącz przenośną stację dysków DVD do komputera, kliknij prawym przyciskiem myszy obraz ISO, a następnie wybierz pozycję **wypalanie na dysku**.

1. Połącz dysk DVD lub dysk przy użyciu klucza i Skonfiguruj urządzenie pod kątem rozruchu z dysku DVD lub na dysk w kluczu.

### <a name="install-from-disk-on-a-key"></a>Instalowanie z dysku na kluczu

Przed instalacją upewnij się, że masz:

  - Rufus.
  
  - Dysk na kluczu z USB w wersji 3,0 lub nowszej. Minimalny rozmiar to 4 GB.

  - Plik obrazu Instalatora ISO.

Dysk w kluczu zostanie wymazany w tym procesie.

Aby przygotować dysk na kluczu:

1. Uruchom Rufus i wybierz opcję **czujnik ISO**.

2. Połącz dysk w kluczu z panelem przednim.

3. Ustaw system BIOS serwera na rozruch z USB.

## <a name="dell-poweredger340xl-installation"></a>Instalacja PowerEdgeR340XL firmy Dell

Przed zainstalowaniem oprogramowania na urządzeniu Dell należy dostosować konfigurację systemu BIOS urządzenia:

  - Panel [Dell PowerEdge R340 Front](#dell-poweredge-r340-front-panel) i [Dell PowerEdge R340 back panel](#dell-poweredge-r340-back-panel) zawiera opis paneli front i back wraz z informacjami wymaganymi do instalacji, takimi jak sterowniki i porty.

  - [Konfiguracja systemu BIOS firmy Dell](#dell-bios-configuration) zawiera informacje dotyczące sposobu nawiązywania połączenia z interfejsem zarządzania urządzeniami firmy Dell i konfigurowania systemu BIOS.

  - [Instalacja oprogramowania (Dell R340)](#software-installation-dell-r340) opisuje procedurę wymaganą do zainstalowania usługi Defender for IoT sensor.

### <a name="dell-poweredge-r340xl-requirements"></a>Wymagania dotyczące oprogramowania Dell PowerEdge R340XL 

Aby zainstalować urządzenie Dell PowerEdge R340XL, potrzebne są:

- Licencja przedsiębiorstwa na kontroler dostępu zdalnego firmy Dell (iDrac)

- Plik XML konfiguracji systemu BIOS

- Wersje oprogramowania układowego serwera:

  - Wersja systemu BIOS 2.1.6

  - iDrac wersja 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Panel przedni Dell PowerEdge R340

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Panel przedni Dell PowerEdge R340.":::

 1. Lewy panel sterowania 
 2. Dysk optyczny (opcjonalnie) 
 3. Prawy panel sterowania 
 4. Tag informacji 
 5. Napędy  

### <a name="dell-poweredge-r340-back-panel"></a>Panel tylny R340 firmy Dell PowerEdge

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Dell PowerEdge R340 back panel.":::

1. Port szeregowy 
2. Port kart sieciowych (GB 1) 
3. Port kart sieciowych (GB 1) 
4. PCIe o połowie wysokości 
5. Gniazdo kart rozszerzeń PCIe o pełnej wysokości 
6. Jednostka zasilacza 1 
7. Zasilacz (jednostka zasilacz 2) 
8. Identyfikacja systemu 
9. Przycisk portu przewodu wskaźnika stanu systemu (CMA) 
10. Port USB 3,0 (2) 
11. Dedykowany port sieciowy iDRAC9 
12. Port VGA 

### <a name="dell-bios-configuration"></a>Konfiguracja systemu BIOS firmy Dell

Aby dostosować urządzenie firmy Dell do pracy z oprogramowaniem, wymagana jest konfiguracja systemu BIOS firmy Dell.

Konfiguracja systemu BIOS jest przeprowadzana za pomocą wstępnie zdefiniowanej konfiguracji. Plik jest dostępny z [centrum pomocy](https://help.cyberx-labs.com/).

Zaimportuj plik konfiguracji do urządzenia Dell. Przed użyciem pliku konfiguracji należy ustanowić komunikację między urządzeniem firmy Dell a komputerem zarządzania.

Urządzenie Dell jest zarządzane przez zintegrowaną iDRAC z kontrolerem cyklu życia (LC). LC jest osadzony w każdym serwerze Dell PowerEdge i oferuje funkcje, które ułatwiają wdrażanie, aktualizowanie, monitorowanie i konserwowanie urządzeń PowerEdge firmy Dell.

Aby nawiązać komunikację między urządzeniem firmy Dell a komputerem zarządzania, należy zdefiniować adres IP iDRAC i adres IP komputera zarządzania w tej samej podsieci.

Po nawiązaniu połączenia można skonfigurować system BIOS.

Aby skonfigurować system BIOS firmy Dell:

1. [Skonfiguruj adres IP iDRAC](#configure-idrac-ip-address)

2. [Zaimportuj plik konfiguracyjny systemu BIOS](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>Konfigurowanie adresu IP iDRAC

1. Włącz czujnik.

2. Jeśli system operacyjny jest już zainstalowany, wybierz klawisz F2, aby wprowadzić konfigurację systemu BIOS.

3. Wybierz pozycję **Ustawienia iDRAC**.

4. Wybierz pozycję **Sieć**.

   > [!NOTE]
   > Podczas instalacji należy skonfigurować domyślny adres IP iDRAC i hasło wymienione w poniższych krokach. Po zakończeniu instalacji należy zmienić te definicje.

5. Zmień statyczny adres IPv4 na **10.100.100.250**.

6. Zmień statyczną maskę podsieci na **255.255.255.0**.

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Zrzut ekranu pokazujący statyczną maskę podsieci.":::

7. Wybierz pozycję **zaplecze**  >  .

#### <a name="import-the-bios-configuration-file"></a>Zaimportuj plik konfiguracyjny systemu BIOS

W tym artykule opisano sposób konfigurowania systemu BIOS przy użyciu pliku konfiguracji.

1. Podłącz komputer ze statycznym wstępnie skonfigurowanym adresem IP **10.100.100.200** do portu **iDRAC** .

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="Zrzut ekranu przedstawiający wstępnie skonfigurowany port adresu IP.":::

2. Otwórz przeglądarkę i wprowadź **10.100.100.250** , aby nawiązać połączenie z interfejsem sieci Web iDRAC.

3. Zaloguj się przy użyciu domyślnych uprawnień administratora firmy Dell:

   - Nazwa użytkownika: **root**

   - Hasło: **Calvin**

4. Poświadczenia urządzenia są następujące:

   - Nazwa użytkownika: **XXX**

   - Hasło: **XXX**

     Operacja importowania profilu serwera została zainicjowana.

     > [!NOTE]
     > Przed zaimportowaniem pliku upewnij się, że:
     > - Jesteś jedynym użytkownikiem, który jest aktualnie połączony z iDRAC.
     > - System nie znajduje się w menu BIOS.

5. Przejdź do pozycji Konfiguracja  >  **serwera** konfiguracji. Ustaw następujące parametry:

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="Zrzut ekranu pokazujący konfigurację profilu serwera.":::

   | Parametr | Konfiguracja |
   |--|--|
   | Typ lokalizacji | Wybierz pozycję **lokalna**. |
   | Ścieżka pliku | Wybierz pozycję **Wybierz plik** i Dodaj plik XML konfiguracji. |
   | Importuj składniki | Wybierz pozycję **BIOS, kartę sieciową, RAID**. |
   | Maksymalny czas oczekiwania | Wybierz **20 minut**. |

6. Wybierz pozycję **Importuj**.

7. Aby monitorować ten proces, przejdź do   >  **kolejki zadań** konserwacji.

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="Zrzut ekranu przedstawiający kolejkę zadań.":::

#### <a name="manually-configuring-bios"></a>Ręczne konfigurowanie systemu BIOS 

Należy ręcznie skonfigurować system BIOS urządzenia, jeśli:

- Nie zakupiono urządzenia od strzałki.

- Masz urządzenie, ale nie masz dostępu do pliku konfiguracji XML.

Po uzyskaniu dostępu do systemu BIOS przejdź do pozycji **Ustawienia urządzenia**.

Aby ręcznie skonfigurować:

1. Uzyskaj dostęp do systemu BIOS urządzenia bezpośrednio przy użyciu klawiatury i ekranu lub użyj iDRAC.

   - Jeśli urządzenie nie jest urządzeniem usługi Defender for IoT, Otwórz przeglądarkę i przejdź do adresu IP, który został wcześniej skonfigurowany. Zaloguj się przy użyciu domyślnych uprawnień administratora firmy Dell. Użyj **elementu root** dla nazwy użytkownika i **Calvin** dla hasła.

   - Jeśli urządzenie jest urządzeniem usługi Defender for IoT, zaloguj się przy użyciu **XXX** dla nazwy użytkownika i **XXX** dla hasła.

2. Po uzyskaniu dostępu do systemu BIOS przejdź do pozycji **Ustawienia urządzenia**.

3. Wybierz konfigurację sterowaną za pomocą macierzy RAID, wybierając pozycję **zintegrowany kontroler RAID 1: \<PERC H330 Adapter\> Narzędzie konfiguracji PERC firmy Dell**.

4. Wybierz pozycję **Zarządzanie konfiguracją**.

5. Wybierz pozycję **Utwórz dysk wirtualny**.

6. W polu **Wybieranie poziomu RAID** wybierz pozycję **RAID5**. W polu **Nazwa dysku wirtualnego** wprowadź wartość **root** i wybierz pozycję **dyski fizyczne**.

7. Wybierz pozycję zaznacz **wszystko** , a następnie wybierz pozycję **Zastosuj zmiany** .

8. Wybierz przycisk **OK**.

9. Przewiń w dół i wybierz pozycję **Utwórz dysk wirtualny**.

10. Zaznacz pole wyboru **Potwierdź** i wybierz pozycję **tak**.

11. Wybierz przycisk **OK**.

12. Wróć do ekranu głównego i wybierz **system BIOS**.

13. Wybierz pozycję **ustawienia rozruchu**.

14. Dla opcji **tryb rozruchu** wybierz pozycję **BIOS**.

15. Wybierz pozycję **Wstecz**, a następnie wybierz pozycję **Zakończ** , aby wyjść z ustawień systemu BIOS.

### <a name="software-installation-dell-r340"></a>Instalacja oprogramowania (Dell R340)

Proces instalacji trwa około 20 minut. Po instalacji system zostanie uruchomiony ponownie kilka razy.

Aby zainstalować program:

1. Sprawdź, czy nośnik wersji jest zainstalowany na urządzeniu w jeden z następujących sposobów:

   - Podłącz zewnętrzny dysk CD lub dysk przy użyciu klucza z wersją.

   - Zainstaluj obraz ISO przy użyciu iDRAC. Po zalogowaniu się do iDRAC Wybierz konsolę wirtualną, a następnie wybierz pozycję **multimedia wirtualne**.

2. W sekcji **map CD/DVD** wybierz pozycję **Wybierz plik**.

3. Wybierz plik obrazu ISO wersji dla tej wersji w otwartym oknie dialogowym.

4. Wybierz przycisk **Mapuj urządzenie** .

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Zrzut ekranu przedstawiający zamapowane urządzenie.":::

5. Nośnik jest zainstalowany. Wybierz pozycję **Zamknij**.

6. Uruchom urządzenie. Gdy korzystasz z programu iDRAC, możesz ponownie uruchomić serwery, wybierając przycisk **kontrolki Consul** . Następnie na **makrach klawiatury** wybierz przycisk **Zastosuj** , który uruchomi sekwencję CTRL + ALT + DELETE.

7. Wybierz pozycję **angielski**.

8. Wybierz pozycję **sensor-Release- \<version\> Enterprise**.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Zrzut ekranu pokazujący wybór wersji.":::   

9. Zdefiniuj profil urządzenia i właściwości sieci:

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Zrzut ekranu pokazujący profil urządzenia.":::   

   | Parametr | Konfiguracja |
   |--|--|
   | **Profil sprzętu** | **Przedsiębiorstwo** |
   | **Interfejs zarządzania** | **eno1** |
   | **Parametry sieci (dostarczone przez klienta)** | - |
   |**adres IP sieci zarządzania:** | - |
   | **Maska podsieci:** | - |
   | **Nazwa hosta urządzenia:** | - |
   | **USŁUGĄ** | - |
   | **domyślny adres IP bramy:** | - |
   | **interfejsy wejściowe:** |  System generuje listę interfejsów wejściowych. Aby dublować interfejsy wejściowe, skopiuj wszystkie elementy przedstawione na liście z separatorem przecinka. Należy pamiętać, że nie ma potrzeby konfigurowania interfejsu mostka. Ta opcja jest używana tylko w przypadku specjalnych przypadków użycia. |

10. Po około 10 minutach pojawiają się dwa zestawy poświadczeń. Jeden jest przeznaczony dla użytkownika **CyberX** , a drugi jest przeznaczony dla użytkownika **pomocy technicznej** .  

11. Zapisz identyfikator i hasła urządzenia. Te poświadczenia będą potrzebne do uzyskania dostępu do platformy przy pierwszym użyciu.

12. Wybierz **klawisz ENTER** , aby kontynuować.

## <a name="hpe-proliant-dl20-installation"></a>Instalacja HPE ProLiant DL20

W tym artykule opisano proces instalacji programu HPE ProLiant DL20, który obejmuje następujące kroki:

  - Włącz dostęp zdalny i zaktualizuj domyślne hasło administratora.
  - Skonfiguruj ustawienia systemu BIOS i RAID.
  - Zainstaluj oprogramowanie.

### <a name="about-the-installation"></a>Informacje o instalacji

  - Można zainstalować urządzenia firmowe i SMB. Proces instalacji jest identyczny w przypadku obu typów urządzeń, z wyjątkiem konfiguracji macierzy.
  - Zostanie udostępniony domyślny użytkownik administracyjny. Zalecamy zmianę hasła podczas procesu konfiguracji sieci.
  - Podczas procesu konfigurowania sieci Skonfiguruj port MOP na porcie sieciowym 1.
  - Proces instalacji trwa około 20 minut. Po instalacji system zostanie uruchomiony ponownie kilka razy.

### <a name="hpe-proliant-dl20-front-panel"></a>Panel przedni HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="Panel przedni HPE ProLiant DL20.":::

### <a name="hpe-proliant-dl20-back-panel"></a>Panel HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="Tylny panel HPE ProLiant DL20.":::

### <a name="enable-remote-access-and-update-the-password"></a>Włączanie dostępu zdalnego i aktualizowanie hasła

Aby skonfigurować opcje sieciowe i zaktualizować domyślne hasło, należy wykonać poniższą procedurę.

Aby włączyć i zaktualizować hasło:

1. Podłącz ekran i klawiaturę do urządzenia HP, Włącz urządzenie i naciśnij klawisz **F9**.

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Zrzut ekranu przedstawiający okno HPE ProLiant.":::

2. Wybierz kolejno pozycje **system narzędzia**  >  Konfiguracja **Konfiguracja systemu**  >  **MOP 5**  >  **Opcje sieciowe** narzędzia.

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Zrzut ekranu przedstawiający okno Konfiguracja systemu.":::

    1.  Wybierz pozycję **udostępniony port sieciowy — Lom** z **karty interfejsu sieciowego** .
    
    1.  Wyłącz usługę DHCP.
    
    1.  Wprowadź adres IP, maskę podsieci i adres IP bramy.

3. Wybierz **F10: Zapisz**.

4. Wybierz **klawisz ESC** , aby wrócić do **Narzędzia konfiguracji MOP 5**, a następnie wybierz pozycję **Zarządzanie użytkownikami**.

5. Wybierz pozycję **Edytuj/Usuń użytkownika**. Administrator jest jedynym zdefiniowanym domyślnym użytkownikiem. 

6. Zmień hasło domyślne i wybierz **F10: Zapisz**.

### <a name="configure-the-hpe-bios"></a>Konfigurowanie systemu BIOS HPE

Poniższa procedura opisuje sposób konfigurowania HPE systemu BIOS dla urządzeń firmowych i SMB.

Aby skonfigurować system BIOS HPE:

1. Wybierz kolejno pozycje **system narzędzia systemowe**  >  **Konfiguracja systemu**  >  **BIOS/platformy konfiguracja (RBSU)**.

2. W formularzu **Konfiguracja systemu BIOS/platform (RBSU)** wybierz pozycję **Opcje rozruchu**.

3. Zmień **tryb rozruchu** na **starszy w trybie BIOS**, a następnie wybierz klawisz **F10: Zapisz**.

4. Wybierz **klawisz ESC** dwa razy, aby zamknąć formularz **Konfiguracja systemu** .

#### <a name="for-the-enterprise-appliance"></a>Dla urządzenia przedsiębiorstwa

1. Wybierz pozycję **Embedded RAID 1: HPE Smart Array P408i — Konfiguracja macierzy interfejsu SR gen 10**  >    >  **tworzenie tablicy**.

2. W formularzu **Utwórz tablicę** zaznacz wszystkie opcje. Dla urządzenia **przedsiębiorstwa** dostępne są trzy opcje.

#### <a name="for-the-smb-appliance"></a>Dla urządzenia SMB

1. Wybierz pozycję **Embedded RAID 1: HPE Smart Array P208i — Konfiguracja macierzy interfejsu SR gen 10**  >    >  **tworzenie tablicy**.

2. Wybierz **Przejdź do następnego formularza**.

3. W formularzu **Ustawianie poziomu RAID** Ustaw poziom na **RAID 5** dla wdrożeń przedsiębiorstwa i **RAID 1** na potrzeby wdrożeń SMB.

4. Wybierz **Przejdź do następnego formularza**.

5. W formularzu **Etykieta dysku logicznego** wprowadź wartość **dysk logiczny 1**.

6. Wybierz pozycję **Prześlij zmiany**.

7. W formularzu **przesyłania** wybierz pozycję **Powrót do menu głównego**.

8. Wybierz **F10: Zapisz** , a następnie naciśnij klawisz **ESC** dwa razy.

9. W oknie **Narzędzia systemowe** wybierz opcję jednorazowe **menu rozruchu**.

10. W formularzu z **menu rozruchu jednokrotnego** wybierz pozycję **starsze One-Time BIOS menu rozruchu**.

11. Zostaną wyświetlone okna **rozruchowe w starszej wersji** i **przesłonięciu rozruchu** . Wybierz opcję zastąpienia rozruchowego; na przykład, do dysku CD-ROM, USB, dysku twardego lub powłoki UEFI.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Zrzut ekranu pokazujący pierwsze okno przesłonięcia rozruchu.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Zrzut ekranu pokazujący drugie okno przesłonięcia rozruchu.":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Instalacja oprogramowania (urządzenie z HPE ProLiant DL20)

Proces instalacji trwa około 20 minut. Po instalacji system zostanie uruchomiony ponownie kilka razy.

Aby zainstalować oprogramowanie:

1. Podłącz ekran i klawiaturę do urządzenia, a następnie połącz się z interfejsem wiersza polecenia.

2. Podłącz zewnętrzny dysk CD lub dysk przy użyciu obrazu ISO pobranego ze strony **aktualizacje** w portalu usługi Defender for IoT.

3. Uruchom urządzenie.

4. Wybierz pozycję **angielski**.

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="Wybór języka angielskiego w oknie interfejsu wiersza polecenia.":::

5. Wybierz pozycję **sensor-Release- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Zrzut ekranu przedstawiający ekran służący do wybierania wersji.":::

6. W Kreatorze instalacji Zdefiniuj profil urządzenia i właściwości sieci:

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Zrzut ekranu przedstawiający Kreatora instalacji.":::

    | Parametr | Konfiguracja |
    | ----------| ------------- |
    | **Profil sprzętu** | Wybierz pozycję **przedsiębiorstwa** lub **pakiet Office** dla wdrożeń SMB. |
    | **Interfejs zarządzania** | **eno2** |
    | **Domyślne parametry sieci (zwykle parametry są dostarczane przez klienta)** | **adres IP sieci zarządzania:** <br/> <br/>**Nazwa hosta urządzenia:** <br/>**USŁUGĄ** <br/>**domyślny adres IP bramy:**|
    | **interfejsy wejściowe:** | System generuje listę interfejsów wejściowych.<br/><br/>Aby dublować interfejsy wejściowe, skopiuj wszystkie elementy przedstawione na liście z separatorem przecinka: **eno5, eno3, eno1, eno6, eno4**<br/><br/>**HPE DL20: nie wyświetlaj listy eno1, enp1s0f4u4 (interfejsy MOP)**<br/><br/>**Mostek**: nie ma potrzeby konfigurowania interfejsu mostka. Ta opcja jest używana tylko w przypadku specjalnych przypadków użycia. Naciśnij klawisz **Enter**, aby kontynuować. |

7. Po około 10 minutach pojawiają się dwa zestawy poświadczeń. Jeden jest przeznaczony dla użytkownika **CyberX** , a drugi jest przeznaczony dla użytkownika **pomocy technicznej** .

8. Zapisz identyfikator i hasła urządzenia. Musisz mieć poświadczenia, aby uzyskać dostęp do platformy po raz pierwszy.

9. Wybierz **klawisz ENTER** , aby kontynuować.

## <a name="hpe-proliant-dl360-installation"></a>Instalacja HPE ProLiant DL360

  - Zostanie udostępniony domyślny użytkownik administracyjny. Zalecamy zmianę hasła podczas konfiguracji sieci.

  - Podczas konfigurowania sieci należy skonfigurować port MOP.

  - Proces instalacji trwa około 20 minut. Po instalacji system zostanie uruchomiony ponownie kilka razy.

### <a name="hpe-proliant-dl360-front-panel"></a>Panel przedni HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="Panel przedni HPE ProLiant DL360.":::

### <a name="hpe-proliant-dl360-back-panel"></a>Panel HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="Panel HPE ProLiant DL360.":::

### <a name="enable-remote-access-and-update-the-password"></a>Włączanie dostępu zdalnego i aktualizowanie hasła

Zapoznaj się z poprzednimi sekcjami instalacji HPE ProLiant DL20:

  - "Włączanie dostępu zdalnego i aktualizowanie hasła"

  - "Konfigurowanie systemu BIOS HPE"

Konfiguracja przedsiębiorstwa jest taka sama.

> [!Note]
> W formularzu tablica Sprawdź, czy wybrano wszystkie opcje.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>Zdalna instalacja usługi MOP (z dysku wirtualnego)

Ta procedura opisuje instalację usługi MOP z dysku wirtualnego.

Aby zainstalować program:

1. Zaloguj się do konsoli MOP, a następnie kliknij prawym przyciskiem myszy ekran serwery.

2. Wybierz **konsolę HTML5**.

3. W konsoli programu wybierz ikonę CD, a następnie wybierz opcję CD/DVD.

4. Wybierz pozycję **lokalny plik ISO**.

5. W oknie dialogowym wybierz odpowiedni plik ISO.

6. Przejdź do lewej ikony, wybierz pozycję **energia**, a następnie wybierz pozycję **Zresetuj**.

7. Urządzenie zostanie ponownie uruchomione i uruchomi proces instalacji czujnika.

### <a name="software-installation-hpe-dl360"></a>Instalacja oprogramowania (HPE DL360)

Proces instalacji trwa około 20 minut. Po instalacji system zostanie uruchomiony ponownie kilka razy.

Aby zainstalować program:

1. Podłącz ekran i klawiaturę do urządzenia, a następnie połącz się z interfejsem wiersza polecenia.

2. Podłącz zewnętrzny dysk CD lub dysk przy użyciu obrazu ISO pobranego ze strony **aktualizacje** w portalu usługi Defender for IoT.

3. Uruchom urządzenie.

4. Wybierz pozycję **angielski**.

5. Wybierz pozycję **sensor-Release- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Zrzut ekranu pokazujący wybór wersji.":::

6. W Kreatorze instalacji Zdefiniuj profil urządzenia i właściwości sieci.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Zrzut ekranu przedstawiający Kreatora instalacji.":::

    | Parametr | Konfiguracja |
    | ----------| ------------- |
    | **Profil sprzętu** | Wybierz pozycję **firmowe**. |
    | **Interfejs zarządzania** | **eno2** |
    | **Domyślne parametry sieci (dostarczone przez klienta)** | **adres IP sieci zarządzania:** <br/>**Maska podsieci:** <br/>**Nazwa hosta urządzenia:** <br/>**USŁUGĄ** <br/>**domyślny adres IP bramy:**|
    | **interfejsy wejściowe:**  | System generuje listę interfejsów wejściowych.<br/><br/>Aby dublować interfejsy wejściowe, skopiuj wszystkie elementy przedstawione na liście z separatorem przecinka.<br/><br/>Należy pamiętać, że nie ma potrzeby konfigurowania interfejsu mostka. Ta opcja jest używana tylko w przypadku specjalnych przypadków użycia. |

7. Po około 10 minutach pojawiają się dwa zestawy poświadczeń. Jeden jest przeznaczony dla użytkownika **CyberX** , a drugi jest przeznaczony dla użytkownika **pomocy technicznej** .

8. Zapisz identyfikator i hasła urządzenia. Te poświadczenia będą potrzebne do uzyskania dostępu do platformy po raz pierwszy.

9. Wybierz **klawisz ENTER** , aby kontynuować.

## <a name="sensor-installation-for-the-virtual-appliance"></a>Instalacja czujnika dla urządzenia wirtualnego

Maszynę wirtualną usługi Defender for IoT można wdrożyć w następujących architekturach:


| Architektura | Specyfikacje | Użycie | Komentarze |
|---|---|---|---|
| **Przedsiębiorstwo** | PROCESOR: 8<br/>Pamięć: 32G pamięć RAM<br/>DYSK TWARDY: 1800 GB | Środowisko produkcyjne | Domyślne i Najczęstsze |
| **Małe firmy** | PROCESOR CPU: 4 <br/>Pamięć: 8G pamięć RAM<br/>DYSK TWARDY: 500 GB | Testowanie lub małe środowiska produkcyjne | -  |
| **Office** | PROCESOR CPU: 4<br/>Pamięć: 8G pamięć RAM<br/>DYSK TWARDY: 100 GB | Małe środowiska testowe | -  |

### <a name="prerequisites"></a>Wymagania wstępne

Lokalna Konsola zarządzania obsługuje opcje wdrażania programu VMware i funkcji Hyper-V. Przed rozpoczęciem instalacji upewnij się, że masz następujące elementy:

  - Oprogramowanie VMware (ESXi 5,5 lub nowszy) lub funkcja hypervisor funkcji Hyper-V (Windows 10 Pro lub Enterprise) zainstalowana i operacyjna

  - Dostępne zasoby sprzętowe dla maszyny wirtualnej

  - Plik instalacyjny ISO usługi Azure Defender dla czujnika IoT

Upewnij się, że funkcja hypervisor jest uruchomiona.

### <a name="create-the-virtual-machine-esxi"></a>Utwórz maszynę wirtualną (ESXi)

1. Zaloguj się do ESXi, wybierz odpowiedni **Magazyn** danych, a następnie wybierz pozycję **przeglądarka magazynu** danych.

2. **Przekaż** obraz i wybierz pozycję **Zamknij**.

3. Przejdź do **Virtual Machines**, a następnie wybierz pozycję **Utwórz/Zarejestruj maszynę wirtualną**.

4. Wybierz pozycję **Utwórz nową maszynę wirtualną**, a następnie wybierz pozycję **dalej**.

5. Dodaj nazwę czujnika i wybierz:

   - Zgodność: **&lt; Najnowsza wersja &gt; ESXi**

   - Rodzina systemów operacyjnych gościa: **Linux**

   - Wersja systemu operacyjnego gościa: **Ubuntu Linux (64-bitowa)**

6. Wybierz pozycję **Dalej**.

7. Wybierz odpowiedni magazyn danych i wybierz pozycję **dalej**.

8. Zmień parametry sprzętu wirtualnego zgodnie z wymaganą architekturą.

9. W przypadku stacji **dysków CD/DVD 1** wybierz pozycję **plik ISO magazynu** danych i wybierz plik ISO, który został wcześniej przekazany.

10. Wybierz pozycje **Dalej** > **Zakończ**.

### <a name="create-the-virtual-machine-hyper-v"></a>Tworzenie maszyny wirtualnej (funkcja Hyper-V)

W tej procedurze opisano sposób tworzenia maszyny wirtualnej za pomocą funkcji Hyper-V.

Aby utworzyć maszynę wirtualną:

1. Utwórz dysk wirtualny w Menedżerze funkcji Hyper-V.

2. Wybierz **Format = VHDX**.

3. Wybierz **Typ = rozszerzanie dynamiczne**.

4. Wprowadź nazwę i lokalizację dysku VHD.

5. Wprowadź wymagany rozmiar (zgodnie z architekturą).   

6. Przejrzyj podsumowanie i wybierz pozycję **Zakończ**.

7. W menu **Akcje** Utwórz nową maszynę wirtualną.

8. Wprowadź nazwę dla maszyny wirtualnej.

9. Wybierz pozycję **Określ** generację generacji  >  **1**.

10. Określ alokację pamięci (zgodnie z architekturą) i zaznacz pole wyboru dla pamięci dynamicznej.

11. Skonfiguruj adapter sieciowy zgodnie z topologią sieci serwera.

12. Połącz dysk VHDX utworzony wcześniej z maszyną wirtualną.

13. Przejrzyj podsumowanie i wybierz pozycję **Zakończ**.

14. Kliknij prawym przyciskiem myszy nową maszynę wirtualną, a następnie wybierz pozycję **Ustawienia**.

15. Wybierz pozycję **Dodaj sprzęt** i Dodaj nową kartę sieciową.

16. Wybierz przełącznik wirtualny, który będzie łączyć się z siecią zarządzania czujnikami.

17. Przydzielanie zasobów procesora CPU (zgodnie z architekturą).

18. Podłącz obraz ISO konsoli zarządzania do wirtualnej stacji dysków DVD.

19. Uruchom maszynę wirtualną.

20. W menu **Akcje** wybierz opcję **Połącz** , aby kontynuować instalację oprogramowania.

### <a name="software-installation-esxi-and-hyper-v"></a>Instalacja oprogramowania (ESXi i Hyper-V)

W tej sekcji opisano instalację oprogramowania ESXi i funkcji Hyper-V.

Aby zainstalować program:

1. Otwórz konsolę maszyny wirtualnej.

2. Maszyna wirtualna rozpocznie się z obrazu ISO, a zostanie wyświetlony ekran wybór języka. Wybierz pozycję **angielski**.

3. Wybierz wymaganą architekturę.

4. Zdefiniuj profil urządzenia i właściwości sieci:

    | Parametr | Konfiguracja |
    | ----------| ------------- |
    | **Profil sprzętu** | &lt;wymagana architektura&gt; |
    | **Interfejs zarządzania** | **ens192** |
    | **Parametry sieci (dostarczone przez klienta)** | **adres IP sieci zarządzania:** <br/>**Maska podsieci:** <br/>**Nazwa hosta urządzenia:** <br/>**USŁUGĄ** <br/>**Brama domyślna:** <br/>**interfejsy wejściowe:**|
    | **interfejsy mostka:** | Nie ma potrzeby konfigurowania interfejsu mostka. Ta opcja dotyczy tylko specjalnych przypadków użycia. |

5. Wprowadź wartość **Y** , aby zaakceptować ustawienia.

6. Poświadczenia logowania są generowane automatycznie i wyświetlane. Skopiuj nazwę użytkownika i hasło w bezpiecznym miejscu, ponieważ są one wymagane do logowania i administrowania.

   - **Pomoc techniczna**: użytkownik administracyjny do zarządzania użytkownikami.

   - **CyberX**: odpowiednik elementu głównego na potrzeby uzyskiwania dostępu do urządzenia.

7. Urządzenie zostanie ponownie uruchomione.

8. Dostęp do konsoli zarządzania za pośrednictwem wcześniej skonfigurowanego adresu IP: `https://ip_address` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Zrzut ekranu, który pokazuje dostęp do konsoli zarządzania.":::

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Urządzenie wirtualne: Instalacja lokalnej konsoli zarządzania

Maszyna wirtualna lokalnej konsoli zarządzania obsługuje następujące architektury:

| Architektura | Specyfikacje | Użycie | 
|--|--|--|
| Przedsiębiorstwa <br/>(Domyślne i najbardziej typowe) | PROCESOR: 8 <br/>Pamięć: 32G pamięć RAM<br/> DYSK TWARDY: 1,8 TB | Duże środowiska produkcyjne | 
| Przedsiębiorstwa | PROCESOR CPU: 4 <br/> Pamięć: 8G pamięć RAM<br/> DYSK TWARDY: 500 GB | Duże środowiska produkcyjne |
| Przedsiębiorstwa | PROCESOR CPU: 4 <br/>Pamięć: 8G pamięć RAM <br/> DYSK TWARDY: 100 GB | Małe środowiska testowe | 
   
### <a name="prerequisites"></a>Wymagania wstępne

Lokalna Konsola zarządzania obsługuje opcje wdrażania programu VMware i funkcji Hyper-V. Przed rozpoczęciem instalacji sprawdź następujące kwestie:

- Oprogramowanie VMware (ESXi 5,5 lub nowszy) lub funkcja hypervisor funkcji Hyper-V (Windows 10 Pro lub Enterprise) jest zainstalowana i działa.

- Zasoby sprzętowe są dostępne dla maszyny wirtualnej.

- Masz plik instalacyjny ISO dla lokalnej konsoli zarządzania.
    
- Funkcja hypervisor jest uruchomiona.

### <a name="create-the-virtual-machine-esxi"></a>Utwórz maszynę wirtualną (ESXi)

Do tworzenia maszyny wirtualnej (ESXi):

1. Zaloguj się do ESXi, wybierz odpowiedni **Magazyn** danych, a następnie wybierz pozycję **przeglądarka magazynu** danych.

2. Przekaż obraz i wybierz pozycję **Zamknij**.

3. Przejdź do **Virtual Machines**.

4. Wybierz pozycję **Utwórz/Zarejestruj maszynę wirtualną**.

5. Wybierz pozycję **Utwórz nową maszynę wirtualną** , a następnie wybierz pozycję **dalej**.

6. Dodaj nazwę czujnika i wybierz:

   - Zgodna \<latest ESXi version>

   - Rodzina systemów operacyjnych gościa: Linux

   - Wersja systemu operacyjnego gościa: Ubuntu Linux (64-bitowa)

7. Wybierz pozycję **Dalej**.

8. Wybierz odpowiedni magazyn danych i wybierz pozycję **dalej**.

9. Zmień parametry sprzętu wirtualnego zgodnie z wymaganą architekturą.

10. W przypadku stacji **dysków CD/DVD 1** wybierz pozycję **plik ISO magazynu** danych i wybierz plik ISO, który został wcześniej przekazany.

11. Wybierz pozycje **Dalej** > **Zakończ**.

### <a name="create-the-virtual-machine-hyper-v"></a>Tworzenie maszyny wirtualnej (funkcja Hyper-V)

Aby utworzyć maszynę wirtualną przy użyciu funkcji Hyper-V:

1. Utwórz dysk wirtualny w Menedżerze funkcji Hyper-V.

2. Wybierz format **VHDX**.

3. Wybierz pozycję **Dalej**.

4. Wybierz typ **dynamiczne rozszerzanie**.

5. Wybierz pozycję **Dalej**.

6. Wprowadź nazwę i lokalizację dysku VHD.

7. Wybierz pozycję **Dalej**.

8. Wprowadź wymagany rozmiar (zgodnie z architekturą).

9. Wybierz pozycję **Dalej**.

10. Przejrzyj podsumowanie i wybierz pozycję **Zakończ**.

11. W menu **Akcje** Utwórz nową maszynę wirtualną.

12. Wybierz pozycję **Dalej**.

13. Wprowadź nazwę dla maszyny wirtualnej.

14. Wybierz pozycję **Dalej**.

15. Wybierz pozycję **generacja** i ustaw ją na wartość **generacja 1**.

16. Wybierz pozycję **Dalej**.

17. Określ alokację pamięci (zgodnie z architekturą) i zaznacz pole wyboru dla pamięci dynamicznej.

18. Wybierz pozycję **Dalej**.

19. Skonfiguruj adapter sieciowy zgodnie z topologią sieci serwera.

20. Wybierz pozycję **Dalej**.

21. Połącz dysk VHDX utworzony wcześniej z maszyną wirtualną.

22. Wybierz pozycję **Dalej**.

23. Przejrzyj podsumowanie i wybierz pozycję **Zakończ**.

24. Kliknij prawym przyciskiem myszy nową maszynę wirtualną, a następnie wybierz pozycję **Ustawienia**.

25. Wybierz pozycję **Dodaj sprzęt** i Dodaj nową kartę dla **karty sieciowej**.

26. Dla **przełącznika wirtualnego** wybierz przełącznik, który będzie łączyć się z siecią zarządzania czujnikami.

27. Przydzielanie zasobów procesora CPU (zgodnie z architekturą).

28. Podłącz obraz ISO konsoli zarządzania do wirtualnej stacji dysków DVD.

29. Uruchom maszynę wirtualną.

30. W menu **Akcje** wybierz opcję **Połącz** , aby kontynuować instalację oprogramowania.

### <a name="software-installation-esxi-and-hyper-v"></a>Instalacja oprogramowania (ESXi i Hyper-V)

Uruchomienie maszyny wirtualnej rozpocznie proces instalacji z obrazu ISO.

Aby zainstalować oprogramowanie:

1. Wybierz pozycję **angielski**.

2. Wybierz architekturę wymaganą do wdrożenia.

3. Zdefiniuj interfejs sieciowy dla sieci zarządzania czujnikami: interfejs, adres IP, podsieć, serwer DNS i Brama domyślna.

4. Poświadczenia logowania są generowane automatycznie i wyświetlane. Przechowuj te poświadczenia w bezpiecznym miejscu, ponieważ są one wymagane do logowania i administrowania.

  - **Pomoc techniczna**: użytkownik administracyjny do zarządzania użytkownikami.

  - **CyberX**: odpowiednik elementu głównego na potrzeby uzyskiwania dostępu do urządzenia.

5. Urządzenie zostanie ponownie uruchomione.

6. Dostęp do konsoli zarządzania za pośrednictwem wcześniej skonfigurowanego adresu IP: `<https://ip_address>` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="Zrzut ekranu przedstawiający ekran logowania konsoli zarządzania.":::

## <a name="post-installation-validation"></a>Weryfikacja po instalacji

Aby sprawdzić poprawność instalacji urządzenia fizycznego, należy wykonać kilka testów. Ten sam proces sprawdzania poprawności dotyczy wszystkich typów urządzeń.

Wykonaj walidację przy użyciu graficznego interfejsu użytkownika lub interfejsu wiersza polecenia. Sprawdzanie poprawności jest dostępne dla użytkowników **pomocy technicznej** i **CyberX** użytkownika.

Weryfikacja po instalacji musi obejmować następujące testy:

  - **Test Sanity**: Sprawdź, czy system działa.

  - **Wersja**: Sprawdź, czy wersja jest poprawna.

  - **ifconfig**: Sprawdź, czy wszystkie interfejsy wejściowe skonfigurowane w procesie instalacji są uruchomione.

### <a name="checking-system-health-by-using-the-gui"></a>Sprawdzanie kondycji systemu przy użyciu graficznego interfejsu użytkownika

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Zrzut ekranu przedstawiający Sprawdzanie kondycji systemu.":::

#### <a name="sanity"></a>Sanity

- **Urządzenie**: uruchamia sprawdzenie Sanity urządzenia. Tę samą kontrolę można wykonać przy użyciu polecenia CLI `system-sanity` .

- **Wersja**: Wyświetla wersję urządzenia.

- **Właściwości sieci**: Wyświetla parametry sieci czujnika.

#### <a name="redis"></a>Redis

- **Pamięć**: zawiera ogólny obraz użycia pamięci, taki jak ilość używanej pamięci i pozostała część.

- **Najdłuższy klucz**: wyświetla najdłuższe klucze, które mogą powodować duże użycie pamięci.

#### <a name="system"></a>System

- **Podstawowy dziennik**: zawiera ostatnie 500 wierszy dziennika podstawowego, co pozwala na wyświetlanie ostatnich wierszy dziennika bez eksportowania całego dziennika systemu.

- **Menedżer zadań**: tłumaczy zadania, które są wyświetlane w tabeli procesów, do następujących warstw: 
  
  - Warstwa trwała (Redis) 
  - Warstwa gotówkowa (SQL)

- **Statystyka sieci**: wyświetla dane statystyczne sieci.

- **Góra**: pokazuje tabelę procesów. Jest to polecenie systemu Linux, które udostępnia dynamiczny widok działającego systemu w czasie rzeczywistym.

- **Sprawdzanie pamięci kopii zapasowej**: zapewnia stan pamięci kopii zapasowej, sprawdzając następujące informacje:
  - Lokalizacja folderu kopii zapasowej 
  - Rozmiar folderu kopii zapasowej.
  - Ograniczenia folderu kopii zapasowej
  - Kiedy Ostatnia kopia zapasowa zaszło
  - Ilość miejsca dla dodatkowych plików kopii zapasowych

- **ifconfig**: Wyświetla parametry interfejsów fizycznych urządzenia.

- **CyberX obierz**: wyświetla ruch sieciowy i przepustowość przy użyciu sześciu sekund testów.

- **Błędy z rdzeń, dziennik**: wyświetla błędy z podstawowego pliku dziennika.

Aby uzyskać dostęp do narzędzia:

1. Zaloguj się do czujnika przy użyciu poświadczeń użytkownika **pomocy technicznej** .

2. Wybierz pozycję **Statystyka systemu** z okna **Ustawienia systemu** .

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>Sprawdzanie kondycji systemu przy użyciu interfejsu wiersza polecenia

**Test 1: Sanity**

Sprawdź, czy system działa i działa:

1. Połącz się z interfejsem wiersza polecenia przy użyciu terminalu z systemem Linux (na przykład "tutaj") i **pomocy technicznej** dla użytkowników.

2. Wprowadź `system sanity`.

3. Sprawdź, czy wszystkie usługi są zielone (uruchomione).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="Zrzut ekranu przedstawiający uruchomione usługi.":::

4. Sprawdź, czy **system działa. (prod)** pojawia się u dołu.

**Test 2: Sprawdzanie wersji**

Sprawdź, czy jest używana prawidłowa wersja:

1. Połącz się z interfejsem wiersza polecenia przy użyciu terminalu z systemem Linux (na przykład "tutaj") i **pomocy technicznej** dla użytkowników.

2. Wprowadź `system version`.

3. Sprawdź, czy jest wyświetlana poprawna wersja.

**Test 3: sprawdzanie poprawności sieci**

Sprawdź, czy wszystkie interfejsy wejściowe skonfigurowane podczas procesu instalacji są uruchomione:

1. Połącz się z interfejsem wiersza polecenia przy użyciu terminalu z systemem Linux (na przykład "tutaj") i **pomocy technicznej** dla użytkowników.

2. Wprowadź `network list` (odpowiednik polecenia systemu Linux `ifconfig` ).

3. Sprawdź, czy są wyświetlane wymagane interfejsy wejściowe. Na przykład jeśli są zainstalowane dwie karty sieciowe miedziane, na liście powinny znajdować się 10 interfejsów.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Zrzut ekranu przedstawiający listę interfejsów.":::

**Test 4: dostęp do zarządzania do interfejsu użytkownika**

Sprawdź, czy możesz uzyskać dostęp do graficznego interfejsu użytkownika konsoli:

1. Podłącz komputer przenośny przy użyciu kabla Ethernet do portu zarządzania (**Gb1**).

2. Zdefiniuj adres karty sieciowej laptopa, aby znajdować się w tym samym zakresie co urządzenie.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Zrzut ekranu, który pokazuje dostęp do zarządzania do interfejsu użytkownika.":::

3. Wyślij polecenie ping do adresu IP urządzenia z laptopa, aby sprawdzić łączność (domyślnie: 10.100.10.1).

4. Otwórz przeglądarkę Chrome w laptopie i wprowadź adres IP urządzenia.

5. W oknie **połączenie nie jest oknem prywatnym** wybierz opcję **Zaawansowane** i wykonaj.

6. Test zakończy się pomyślnie, gdy zostanie wyświetlony ekran logowania do usługi Defender for IoT.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Zrzut ekranu pokazujący dostęp do konsoli zarządzania.":::

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="you-cant-connect-by-using-a-web-interface"></a>Nie można nawiązać połączenia przy użyciu interfejsu sieci Web

1. Sprawdź, czy komputer, który próbujesz nawiązać połączenie, znajduje się w tej samej sieci co urządzenie.

2. Sprawdź, czy sieć graficznego interfejsu użytkownika jest połączona z portem zarządzania.

3. Wyślij polecenie ping do adresu IP urządzenia. Jeśli nie ma polecenia ping:

   1. Połącz monitor i klawiaturę z urządzeniem.

   1. Zaloguj się przy użyciu użytkownika i hasła do **pomocy technicznej** .

   1. Użyj polecenia, `network list` Aby wyświetlić bieżący adres IP.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="Zrzut ekranu przedstawiający listę sieci.":::

4. Jeśli parametry sieciowe są błędnie skonfigurowane, wykonaj następującą procedurę, aby je zmienić:

   1. Użyj polecenia `network edit-settings` .

   1. Aby zmienić adres IP sieci zarządzania, wybierz pozycję **Y**.

   1. Aby zmienić maskę podsieci, wybierz pozycję **Y**.

   1. Aby zmienić serwer DNS, wybierz pozycję **Y**.

   1. Aby zmienić domyślny adres IP bramy, wybierz pozycję **Y**.

   1. Dla zmiany interfejsu wejściowego (tylko czujnik) wybierz **N**.

   1. Aby zastosować ustawienia, wybierz pozycję **Y**.

5. Po ponownym uruchomieniu programu Połącz się z poświadczeniami użytkownika pomocy technicznej i Użyj `network list` polecenia, aby sprawdzić, czy parametry zostały zmienione.

6. Spróbuj ponownie wykonać polecenie ping i nawiązać połączenie z graficznym interfejsem użytkownika.

### <a name="the-appliance-isnt-responding"></a>Urządzenie nie odpowiada

1. Podłącz monitor i klawiaturę do urządzenia lub użyj polecenia "Wykorzystaj", aby połączyć się zdalnie z interfejsem wiersza polecenia.

2. Zaloguj się przy użyciu poświadczeń użytkownika **pomocy technicznej** .

3. Użyj `system sanity` polecenia i sprawdź, czy wszystkie procesy są uruchomione.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Zrzut ekranu przedstawiający polecenie System Sanity.":::

W przypadku innych problemów skontaktuj się z firmą [Pomoc techniczna firmy Microsoft](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>Dodatek A: Port dublowania na komputerze vSwitch (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>Konfigurowanie portu SPAN na istniejącym przełączniku vSwitch

Przełącznik vSwitch nie ma możliwości dublowania, ale można użyć prostego obejścia, aby zaimplementować port SPAN.

Aby skonfigurować port SPAN:

1. Otwórz właściwości przełącznika vSwitch.

2. Wybierz pozycję **Dodaj**.

3. Wybierz pozycję **maszyna wirtualna**  >  **dalej**.

4. Wstaw Sieć **obejmującą zakres** etykiet sieci, wybierz pozycję **VLAN ID**  >  **wszystkie**, a następnie wybierz pozycję **dalej**.

5. Wybierz pozycję **Zakończ**.

6. Wybierz pozycję **span Network** > **Edytuj*.

7. Wybierz pozycję **zabezpieczenia**, a następnie sprawdź, czy zasady **trybu ogólnego** są ustawione na **Zaakceptuj** tryb.

8. Wybierz przycisk **OK**, a następnie wybierz przycisk **Zamknij** , aby zamknąć właściwości przełącznika vSwitch.

9. Otwórz właściwości **maszyny wirtualnej XSense** .

10. Dla **karty sieciowej 2** wybierz Sieć **span** .

11. Wybierz przycisk **OK**.

12. Połącz się z czujnikiem i sprawdź, czy dublowanie działa.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>Dodatek B: czujniki dostępu z lokalnej konsoli zarządzania

Aby zwiększyć bezpieczeństwo systemu, można uniemożliwić bezpośredni dostęp użytkownika do czujnika. Zamiast tego należy użyć tunelowania proxy, aby umożliwić użytkownikom dostęp do czujnika z lokalnej konsoli zarządzania z pojedynczą regułą zapory. Ta technika ogranicza możliwość nieautoryzowanego dostępu do środowiska sieciowego poza czujnikiem. Środowisko użytkownika podczas logowania się do czujnika pozostaje takie samo.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Zrzut ekranu, który pokazuje dostęp do czujnika.":::

Aby włączyć tunelowanie:

1. Zaloguj się do interfejsu wiersza polecenia lokalnej konsoli zarządzania przy użyciu **CyberX** lub **obsługi** poświadczeń użytkownika.

2. Wprowadź `sudo cyberx-management-tunnel-enable`.

3. Naciśnij klawisz **Enter**.

4. Wprowadź `--port 10000`.

### <a name="next-steps"></a>Następne kroki

[Konfigurowanie sieci](how-to-set-up-your-network.md)