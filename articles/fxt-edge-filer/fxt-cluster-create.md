---
title: 'Samouczek: Tworzenie klastra pamięci podręcznej usługi Azure FXT Edge'
description: Dowiedz się, jak utworzyć klaster pamięci podręcznej magazynu hybrydowego za pomocą usługi Azure FXT Edge i jak zalogować się do panelu sterowania klastra w celu skonfigurowania ustawień klastra.
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 7a471868bac8f5e0623942c0cc1dc4af4e3881e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88185353"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Samouczek: Tworzenie klastra usługi Azure FXT Edge

Po zainstalowaniu i zainicjowaniu węzłów sprzętowych usługi Azure FXT Edge dla pamięci podręcznej Użyj oprogramowania klastra FXT, aby utworzyć klaster pamięci podręcznej. 

Ten samouczek przeprowadzi Cię przez kroki konfigurowania węzłów sprzętu jako klastra. 

Z tego samouczka dowiesz się: 

> [!div class="checklist"]
> * Jakie informacje są potrzebne przed rozpoczęciem tworzenia klastra
> * Różnica między siecią zarządzania klastra, siecią klastrów i siecią dostępną po stronie klienta
> * Jak nawiązać połączenie z węzłem klastra 
> * Jak utworzyć początkowy klaster przy użyciu jednego węzła usługi Azure FXT Edge
> * Jak zalogować się do panelu sterowania klastra w celu skonfigurowania ustawień klastra

Ta procedura zajmie od 15 do 45 minut, w zależności od tego, ile badań należy zrobić, aby identyfikować adresy IP i zasoby sieciowe.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka wykonaj te wymagania wstępne:

* Instalowanie systemów sprzętowych usługi Azure FXT Edge w centrum danych 

  Potrzebny jest tylko jeden węzeł do utworzenia klastra, ale należy [dodać co najmniej dwa węzły](fxt-add-nodes.md) , aby można było skonfigurować klaster i uzyskać gotowość do użycia. 

* Podłączanie odpowiednich kabli zasilacza i sieci do systemu  
* Włącz co najmniej jeden węzeł usługi Azure FXT Edge i [Ustaw jego hasło główne](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Zbierz informacje dotyczące klastra 

Aby utworzyć klaster usługi Azure FXT Edge, potrzebne są następujące informacje:

* Nazwa klastra

* Hasło administracyjne do ustawienia dla klastra

* Adresy IP:

  * Pojedynczy adres IP dla zarządzania klastrami oraz maska sieci i router, które mają być używane przez sieć zarządzania
  * Pierwsze i ostatnie adresy IP w ciągłym zakresie adresów IP dla komunikacji klastra (węzła do węzła). Aby uzyskać szczegółowe informacje, zobacz temat [dystrybucja adresów IP](#ip-address-distribution)poniżej. 
  * (Adresy IP dostępne po stronie klienta są ustawiane po utworzeniu klastra).

* Informacje o infrastrukturze sieci:

  * Adres IP serwera DNS dla klastra
  * Nazwa domeny DNS dla klastra
  * Nazwa lub adres IP serwerów NTP klastra (jeden serwer lub trzy lub więcej) 
  * Czy chcesz włączyć agregację linków IEEE 802.1 AX-2008 w interfejsach klastra
  * Po włączeniu agregacji łączy, czy ma być używana agregacja dynamiczna IEEE 802.3 AD (LACP)

Te elementy infrastruktury sieciowej można skonfigurować po utworzeniu klastra, ale lepiej jest to zrobić podczas tworzenia. 

### <a name="ip-address-distribution"></a>Dystrybucja adresów IP

Hybrydowy klaster pamięci podręcznej magazynu usługi Azure FXT Edge używa adresów IP w trzech kategoriach:

* IP zarządzania: pojedynczy adres IP do zarządzania klastrami

  Ten adres służy jako punkt wejścia do uzyskiwania dostępu do narzędzi konfiguracji klastra (internetowy Panel sterowania lub interfejs API XML-RPC). Ten adres jest automatycznie przypisywany do węzła podstawowego w klastrze i automatycznie przenoszony w przypadku zmiany węzła podstawowego.

  Inne adresy IP mogą być używane w celu uzyskania dostępu do panelu sterowania, ale adres IP zarządzania został zaprojektowany w celu zapewnienia dostępu nawet w przypadku przechodzenia w tryb failover w poszczególnych węzłach.

* Sieć klastra: zakres adresów IP do komunikacji z klastrem

  Sieć klastra jest używana do komunikacji między węzłami klastra i pobierania plików z magazynu zaplecza (plików podstawowych).

  **Najlepsze rozwiązanie:** Przydziel jeden adres IP na port fizyczny używany do komunikacji klastra w każdym węźle usługi Azure FXT Edge. Klaster automatycznie przypisuje adresy w określonym zakresie do poszczególnych węzłów.

* Sieć połączona z klientem: zakres adresów IP używany przez klientów do żądania i zapisywania plików

  Adresy sieciowe klientów są używane przez klientów do uzyskiwania dostępu do podstawowych danych plików za pomocą klastra. Na przykład klient NFS może zainstalować jeden z tych adresów.

  **Najlepsze rozwiązanie:** Przydziel jeden adres IP na port fizyczny używany do komunikacji z klientem w każdym węźle serii FXT.

  Klaster dystrybuuje adresy IP na klientach w jego węzłach elementów, tak jak to możliwe.

  Dla uproszczenia wielu administratorów konfiguruje pojedynczą nazwę DNS z konfiguracją DNS z działaniem okrężnym (RRDNS), aby ułatwić dystrybuowanie żądań klientów między zakresem adresów. Ta konfiguracja umożliwia również wszystkim klientom korzystanie z tego samego polecenia instalacji w celu uzyskania dostępu do klastra. Aby uzyskać więcej informacji, przeczytaj temat [Konfigurowanie systemu DNS](fxt-configure-network.md#configure-dns-for-load-balancing) .

Aby można było utworzyć nowy klaster, należy określić adres IP zarządzania i zakres adresów sieciowych klastra. Adresy związane z klientem są określane po utworzeniu klastra.

## <a name="connect-to-the-first-node"></a>Połącz z pierwszym węzłem

Aby skonfigurować klaster, można nawiązać połączenie z dowolnym z zainstalowanych węzłów FXT i użyć jego oprogramowania systemu operacyjnego.

Jeśli jeszcze tego nie zrobiono, Włącz co najmniej jeden z węzłów FXT dla klastra i upewnij się, że ma on połączenie sieciowe i adres IP. Należy ustawić nowe hasło główne, aby aktywować węzeł, dlatego wykonaj kroki opisane w sekcji [Ustaw hasła sprzętu](fxt-node-password.md) , jeśli jeszcze tego nie zrobiono.

Aby sprawdzić połączenie sieciowe, upewnij się, że diody LED łącza sieci węzła są oświetlone (i, w razie potrzeby, wskaźniki na przełączniku sieci, do którego jest podłączony). Diody LED wskaźnika są opisane w artykule [monitorowanie stanu sprzętu usługi Azure FXT Edge](fxt-monitor.md).

Gdy węzeł zostanie uruchomiony, zażąda adresu IP. Jeśli jest połączony z serwerem DHCP, akceptuje adres IP podany przez protokół DHCP. (Ten adres IP jest tymczasowy. Zmiana zostanie zmieniona podczas tworzenia klastra.

Jeśli nie jest on połączony z serwerem DHCP lub nie otrzymuje odpowiedzi, węzeł użyje oprogramowania Bonjour do ustawienia samodzielnego adresu IP w formie 169,254. \* . \* . Należy jednak ustawić tymczasowy statyczny adres IP na jednym z kart sieciowych tego węzła przed użyciem go do utworzenia klastra. Instrukcje są zawarte w tym starszym dokumencie; Skontaktuj się z działem pomocy technicznej firmy Microsoft, aby uzyskać zaktualizowane informacje: [dodatek A: ustawienie statycznego adresu IP w WĘŹLE FXT](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Znajdowanie adresu IP

Połącz się z węzłem pliku usługi Azure FXT Edge, aby znaleźć jego adres IP. Można użyć kabla szeregowego, bezpośredniego połączenia z portami USB i VGA lub nawiązywać połączenia za pośrednictwem przełącznika KVM. (Aby uzyskać szczegółowe informacje o połączeniu z portami, zobacz [Ustawianie haseł początkowych](fxt-node-password.md)).

Po nawiązaniu połączenia Zaloguj się przy użyciu nazwy użytkownika `root` i hasła ustawionego podczas uruchamiania węzła po raz pierwszy.  

Po zalogowaniu się należy określić adres IP węzła.

Użyj polecenia, `ifconfig` Aby wyświetlić adresy przypisane do tego systemu.

Na przykład polecenie `ifconfig | grep -B5 inet` wyszukuje porty z adresami internetowymi i zawiera pięć wierszy kontekstu do wyświetlania identyfikatora portu.

Zapisz dowolny adres IP wyświetlany w raporcie ifconfig. Adresy na liście z nazwami portów, takimi jak e0a lub e0b, są dobrymi opcjami. Nie należy używać żadnych adresów IP wymienionych z nazwami E7 *, ponieważ te nazwy są używane tylko dla portów usługi iDRAC/IPMI.  

## <a name="load-the-cluster-configuration-wizard"></a>Załaduj Kreatora konfiguracji klastra

Utwórz klaster przy użyciu narzędzia do konfiguracji klastra opartego na przeglądarce. 

Wprowadź adres IP dla węzła w przeglądarce sieci Web. Jeśli w przeglądarce zostanie wyświetlony komunikat o niezaufanej lokacji, należy kontynuować pracę z lokacją mimo to. (Poszczególne węzły usługi Azure FXT Edge nie mają certyfikatów zabezpieczeń dostarczonych przez urząd certyfikacji).

![Strona logowania panelu sterowania w oknie przeglądarki](media/fxt-cluster-create/unconfigured-node-gui.png)

Pozostaw puste pola **Nazwa użytkownika** i **hasło** . Kliknij pozycję **Zaloguj** , aby załadować stronę tworzenia klastra.

![Ekran początkowej konfiguracji dla nieskonfigurowanego węzła w panelu sterowania graficznego interfejsu użytkownika w przeglądarce. Przedstawiono opcje aktualizacji oprogramowania, konfigurowania klastra ręcznie lub konfigurowania klastra z pliku instalacyjnego.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Tworzenie klastra

Narzędzie konfiguracji klastra przeprowadzi Cię przez zestaw ekranów, aby utworzyć klaster usługi Azure FXT Edge. Przed rozpoczęciem upewnij się, że masz [wymagane informacje](#gather-information-for-the-cluster) . 

### <a name="creation-options"></a>Opcje tworzenia

Pierwszy ekran zawiera trzy opcje. Użyj opcji konfiguracji ręcznej, chyba że masz specjalne instrukcje od personelu pomocy technicznej.

Kliknięcie opcji **konfiguruje klaster ręcznie** , aby załadować nowy ekran Opcje konfiguracji klastra. 

Inne opcje są rzadko używane:

* "Aktualizacja obrazu systemu" powoduje wyświetlenie z prośbą o zainstalowanie nowego oprogramowania systemu operacyjnego przed utworzeniem klastra. (Aktualnie zainstalowana wersja oprogramowania znajduje się w górnej części ekranu). Należy podać plik pakietu oprogramowania — adres URL i nazwę użytkownika/hasło albo przez przekazanie pliku z komputera. 

* Opcja plik instalacji klastra jest czasami używana przez dział obsługi klienta firmy Microsoft. 

## <a name="cluster-options"></a>Opcje klastra

Na następnym ekranie zostanie wyświetlony komunikat z prośbą o skonfigurowanie opcji dla nowego klastra.

Strona jest podzielona na dwie główne sekcje, **konfigurację podstawową** i **konfigurację sieci**. Sekcja konfiguracja sieci zawiera również podsekcje: jeden dla sieci **zarządzania** i jeden dla sieci **klastra** .

### <a name="basic-configuration"></a>Konfiguracja podstawowa

W górnej części Wypełnij podstawowe informacje dotyczące nowego klastra.

![Szczegóły sekcji "Konfiguracja podstawowa" w graficznym interfejsie użytkownika przeglądarki. Pokazuje trzy pola (nazwa klastra, hasło administratora, potwierdzenie hasła)](media/fxt-cluster-create/basic-configuration.png) 

* **Nazwa klastra** — wprowadź unikatową nazwę klastra.

  Nazwa klastra musi spełniać następujące kryteria:
  
  * Długość od 1 do 16 znaków
  * Może zawierać litery, cyfry oraz znaki kreski (-) i podkreślenia (_) 
  * Nie może zawierać innych znaków interpunkcyjnych ani specjalnych
  
  Tę nazwę można zmienić później na **Cluster**  >  stronie Konfiguracja**ogólnej** konfiguracji klastra. (Aby uzyskać więcej informacji na temat ustawień klastra, Przeczytaj [Przewodnik konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), który nie jest częścią tego zestawu dokumentacji).

  > [!NOTE] 
  > Nazwa klastra służy do identyfikowania informacji o systemie przekazanych do obsługi monitorowania lub rozwiązywania problemów, dlatego warto uwzględnić nazwę firmy.

* **Hasło administratora** — Ustaw hasło dla domyślnego użytkownika administracyjnego `admin` .
  
  Należy skonfigurować indywidualne konta użytkowników dla każdej osoby, która administruje klastrem, ale nie można usunąć użytkownika `admin` . Zaloguj się tak, jakby `admin` trzeba utworzyć dodatkowych użytkowników.
 
  Hasło można zmienić na `admin` **Administration**  >  stronie ustawienia**użytkowników** administracyjnych w panelu sterowania klastra. Aby uzyskać szczegółowe informacje, zapoznaj się z dokumentacją **użytkowników** w [podręczniku konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Konfiguracja sieci

W sekcji **Sieć** zostanie wyświetlony komunikat z prośbą o określenie infrastruktury sieciowej, która będzie używana przez klaster. 

Istnieją dwie oddzielne sieci do skonfigurowania:

* *Sieć zarządzania* zapewnia administratorowi dostęp do klastra w celu konfiguracji i monitorowania. Określony tutaj adres IP jest używany podczas nawiązywania połączenia z panelem sterowania lub dostępem do protokołu SSH. 

  W większości klastrów używany jest tylko jeden adres IP zarządzania, ale jeśli chcesz dodać interfejsy, możesz to zrobić po utworzeniu klastra.

* *Sieć klastra* jest używana do komunikacji między węzłami klastra i między węzłami klastra i magazynem zaplecza (podstawowymi plikami programu).

Sieć dołączona do klienta jest konfigurowana później, po utworzeniu klastra.

Ta sekcja obejmuje również konfigurację serwerów DNS i NTP, które są używane przez obie sieci.

### <a name="configure-the-management-network"></a>Konfigurowanie sieci zarządzania

Ustawienia w sekcji **Zarządzanie** dotyczą sieci, która zapewnia administratorowi dostęp do klastra.

![Szczegóły sekcji "Zarządzanie" z polami dla 5 opcji i CheckBox dla sieci zarządzania 1 GB](media/fxt-cluster-create/management-network-filled.png)

* **IP zarządzania** — Określ adres IP, który będzie używany w celu uzyskania dostępu do panelu sterowania klastra. Ten adres zostanie przejęty przez węzeł podstawowy klastra, ale jest automatycznie przenoszony do węzła w dobrej kondycji, jeśli oryginalny węzeł podstawowy stanie się niedostępny.

  W większości klastrów używany jest tylko jeden adres IP zarządzania. Jeśli potrzebujesz więcej niż jednego, możesz je dodać po utworzeniu klastra przy użyciu **Cluster**  >  strony ustawień**sieci administracyjnej** klastra. Więcej informacji można znaleźć w [przewodniku po konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Maska** sieci — umożliwia określenie maski sieciowej do zarządzania.

* **Router** — wprowadź domyślny adres bramy używany przez sieć zarządzania.

* **Tag sieci VLAN (opcjonalnie)** — Jeśli klaster używa tagów sieci VLAN, określ tag dla sieci zarządzania.

  Dodatkowe ustawienia sieci VLAN są konfigurowane na **Cluster**  >  stronie ustawień klastra**sieci VLAN** . Aby dowiedzieć się więcej, zapoznaj się z tematem [Praca z sieciami VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) i [klastrem > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) w przewodniku konfiguracji klastra.

* **Jednostka MTU** — w razie potrzeby Dostosuj maksymalną jednostkę transmisji (MTU) dla sieci zarządzania klastrami.

* **Użyj sieci zarządzania (1 GB** ) — zaznacz to pole wyboru, jeśli chcesz przypisać dwa porty sieciowe 1GbE w węzłach FXT tylko do sieci zarządzania. (Wymagane są porty 25GbE/10GbE dla całego ruchu). Jeśli to pole nie jest zaznaczone, Sieć zarządzania korzysta z najwyższego dostępnego portu. 

### <a name="configure-the-cluster-network"></a>Konfigurowanie sieci klastra 

Ustawienia sieci klastra mają zastosowanie do ruchu między węzłami klastra i między węzłami klastra a podstawowymi plikami programu.

![Szczegóły sekcji "Cluster" z polami, aby wprowadzić sześć wartości](media/fxt-cluster-create/cluster-network-filled.png)

* **Pierwszy adres IP** i **ostatni adres IP** — wprowadź adresy IP definiujące zakres, który ma być używany na potrzeby komunikacji z klastrem wewnętrznym. Używane tutaj adresy IP muszą być ciągłe i nieprzypisane przez protokół DHCP.

  Po utworzeniu klastra można dodać więcej adresów IP. Użyj **Cluster**  >  strony ustawień**sieci klastra** klastrów ([Dokumentacja przewodnika po konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  Wartość w polu **Liczba adresów IP w zakresie** jest obliczana i wyświetlana automatycznie.

* **Maska sieciowa bez zarządzania (opcjonalnie)** — określ maskę sieci klastra. 

  System automatycznie sugeruje wartość maski sieci wprowadzoną przez użytkownika do zarządzania; w razie konieczności zmień ją.

* **Router klastra (opcjonalnie)** — Określ domyślny adres bramy używany przez sieć klastra. 

  System automatycznie sugeruje ten sam adres bramy, który został podany dla sieci zarządzania.

* **Tag sieci VLAN klastra (opcjonalnie)** — Jeśli klaster używa tagów sieci VLAN, określ znacznik dla sieci klastra.

* **Jednostka MTU bez zarządzania (opcjonalnie)** — w razie potrzeby Dostosuj maksymalną jednostkę transmisji (MTU) dla sieci klastra.

### <a name="configure-cluster-dns-and-ntp"></a>Konfigurowanie klastra DNS i NTP 

Poniżej sekcji **klaster** znajdują się pola służące do określania serwerów DNS i NTP oraz do włączania agregacji linków. Te ustawienia mają zastosowanie do wszystkich sieci używanych przez klaster.

![Szczegóły sekcji dotyczącej konfiguracji systemu DNS/NTP z trzema polami dla serwerów DNS, pól dla domeny DNS i wyszukiwania DNS, trzech pól dla serwerów NTP i menu rozwijanego dla opcji agregacji łączy](media/fxt-cluster-create/dns-ntp-filled.png)

* Serwery DNS — wprowadź adres IP co najmniej jednego **serwera** DNS (Domain Name System).

  Usługa DNS jest zalecana dla wszystkich klastrów i jest wymagana, jeśli chcesz użyć protokołu SMB, usługi AD lub protokołu Kerberos. 
  
  W celu uzyskania optymalnej wydajności Skonfiguruj serwer DNS klastra do równoważenia obciążenia z działaniem okrężnym, zgodnie z opisem w temacie [Konfigurowanie systemu DNS dla klastra usługi Azure FXT Edge](fxt-configure-network.md#configure-dns-for-load-balancing).

* **Domena DNS** — wprowadź nazwę domeny sieciowej, która będzie używana przez klaster.

* **Wyszukiwanie DNS** — opcjonalnie wprowadź dodatkowe nazwy domen, które system powinien wyszukać, aby rozwiązać zapytania DNS. Można dodać maksymalnie sześć nazw domen, rozdzielone spacjami.

* Serwery **NTP** — Określ jeden lub trzeci serwer protokołu NTP (Network Time Protocol) w udostępnionych polach. Można użyć nazw hostów lub adresów IP.

* Agregacja **linków** — agregacja linków umożliwia dostosowanie sposobu, w jaki porty Ethernet w węzłach FXT klastra obsługują różne typy ruchu. Aby dowiedzieć się więcej, Przeczytaj [agregację linków](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) w podręczniku konfiguracji klastra.

### <a name="click-the-create-button"></a>Kliknij przycisk Utwórz

Po podaniu wszystkich wymaganych ustawień w formularzu kliknij przycisk **Utwórz klaster** .

![Dolna część ukończonego formularza z kursorem nad przyciskiem Utwórz w prawym dolnym rogu](media/fxt-cluster-create/create-cluster.png)

System wyświetla komunikat podczas tworzenia klastra.

![komunikat o stanie konfiguracji klastra w przeglądarce: "węzeł FXT tworzy teraz klaster. Zajmie to kilka minut. Po utworzeniu klastra przejdź do tego linku, aby zakończyć konfigurację. z hiperłączem "odwiedź ten link"](media/fxt-cluster-create/creating-message.png)

Po kilku chwilach można kliknąć link w wiadomości, aby przejść do panelu sterowania klastra. (Ten link powoduje przejście do adresu IP określonego w temacie Zarządzanie adresem **IP**). Gdy łącze stanie się aktywne po kliknięciu przycisku Utwórz, trwa od 15 sekund do jednej minuty. Jeśli interfejs sieci Web nie zostanie załadowany, poczekaj kilka sekund, a następnie ponownie kliknij link. 

Tworzenie klastra trwa kilka minut, ale możesz zalogować się do panelu sterowania w trakcie procesu. Jest to normalne dla strony pulpitu nawigacyjnego panelu sterowania do wyświetlania ostrzeżeń do momentu zakończenia procesu tworzenia klastra. 

## <a name="open-the-settings-pages"></a>Otwórz strony ustawień 

Po utworzeniu klastra należy dostosować jego konfigurację do sieci i przepływu pracy. 

Użyj interfejsu sieci Web panelu sterowania, aby skonfigurować nowy klaster. Postępuj zgodnie z linkiem na ekranie stanu tworzenia klastra lub przejdź do adresu IP zarządzania ustawionego w klastrze.

Zaloguj się do interfejsu sieci Web przy użyciu nazwy użytkownika `admin` i hasła ustawionego podczas tworzenia klastra.

![Przeglądarka sieci Web przedstawiająca pola logowania w panelu sterowania](media/fxt-cluster-create/admin-login.png)

Zostanie otwarty panel sterowania i zostanie wyświetlona strona **pulpit nawigacyjny** . Po zakończeniu tworzenia klastra wszystkie komunikaty ostrzegawcze powinny zostać wyczyszczone na ekranie.

Kliknij kartę **Ustawienia** , aby skonfigurować klaster.

Na karcie **Ustawienia** lewy pasek boczny ukazuje menu strony konfiguracji. Strony są zorganizowane według kategorii. Kliknij kontrolkę + lub-w górnej części nazwy kategorii, aby rozwinąć lub ukryć poszczególne strony.

![Karta Ustawienia w panelu sterowania (w przeglądarce) z załadowanej stronie konfiguracji ogólnej > klastra](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Kroki konfiguracji klastra

W tym momencie w procesie klaster istnieje, ale ma tylko jeden węzeł, brak adresów IP klienta i magazyn zaplecza. Dodatkowe czynności konfiguracyjne są wymagane do przechodzenia z nowo utworzonego klastra do systemu pamięci podręcznej, który jest gotowy do obsługi przepływu pracy.

### <a name="required-configuration"></a>Wymagana konfiguracja

Te kroki są wymagane dla większości lub wszystkich klastrów. 

* Dodawanie węzłów do klastra 

  Trzy węzły są standardowe, ale wiele klastrów produkcyjnych ma więcej niż maksymalnie 24 węzły.

  Przeczytaj temat [Dodawanie węzłów klastra](fxt-add-nodes.md) , aby dowiedzieć się, jak dodać inne jednostki plików usługi Azure FXT Edge do klastra i zapewnić wysoką dostępność.

* Określ magazyn zaplecza

  Dodaj definicje *podstawowych plików* dla każdego systemu magazynu zaplecza, który będzie używany przez klaster. Przeczytaj temat [Dodawanie magazynu zaplecza i skonfiguruj wirtualną przestrzeń nazw,](fxt-add-storage.md#about-back-end-storage) aby dowiedzieć się więcej.

* Konfigurowanie dostępu klienta i wirtualnej przestrzeni nazw 

  Utwórz co najmniej jeden serwer wirtualny (vserver) i przypisz mu zakres adresów IP do użycia przez komputery klienckie. Należy również skonfigurować przestrzeń nazw klastra (czasami nazywaną globalną przestrzenią nazw lub GNS), wirtualną funkcję systemu plików, która umożliwia mapowanie eksportu magazynu zaplecza na ścieżki wirtualne. Przestrzeń nazw klastra zapewnia klientom spójną i dostępną strukturę systemu plików nawet w przypadku przełączenia nośnika magazynu zaplecza. Przestrzeń nazw może również zapewnić przyjazną dla użytkownika hierarchię magazynu wirtualnego dla kontenerów obiektów blob platformy Azure lub innych obsługiwanych magazynów obiektów w chmurze.

  Aby uzyskać szczegółowe informacje, przeczytaj temat [Konfigurowanie przestrzeni nazw](fxt-add-storage.md#configure-the-namespace) . Ten krok obejmuje:
  * Tworzenie vservers
  * Konfigurowanie połączeń między widokiem sieci klienta i magazynem zaplecza 
  * Definiowanie, które adresy IP klientów są obsługiwane przez poszczególne vserver

  > [!Note] 
  > Przed rozpoczęciem konfigurowania GNS klastra zaleca się istotne planowanie. Zapoznaj się z sekcją [Korzystanie z globalnej przestrzeni nazw](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) i [Tworzenie i praca z](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) sekcjami VServers w przewodniku konfigurowania klastra, aby uzyskać pomoc.

* [Dostosuj ustawienia sieci](fxt-configure-network.md)

  Istnieje kilka ustawień związanych z siecią, które powinny być zweryfikowane lub dostosowane do nowego klastra. Aby uzyskać szczegółowe informacje na temat tych elementów, przeczytaj artykuł [dostosowanie ustawień sieciowych](fxt-configure-network.md) :

  * Weryfikowanie konfiguracji DNS i NTP 
  * Konfigurowanie usług katalogowych, w razie konieczności 
  * Konfigurowanie sieci VLAN
  * Konfigurowanie serwerów proxy
  * Dodawanie adresów IP do sieci klastra
  * Przechowywanie certyfikatów szyfrowania

* [Konfigurowanie monitorowania obsługi](#enable-support)

  Należy zaakceptować zasady ochrony prywatności dla narzędzia konfiguracji i skonfigurować ustawienia przekazywania pomocy technicznej w tym samym czasie.

  Klaster może automatycznie przekazywać dane rozwiązywania problemów dotyczących klastra, w tym dane statystyczne i pliki debugowania. Te operacje przekazywania umożliwiają klientom i działowi pomocy technicznej firmy Microsoft zapewnienie najlepszej możliwej usługi. Możesz dostosować elementy monitorowane i opcjonalnie włączyć obsługę aktywnego i zdalne Rozwiązywanie problemów.  

### <a name="optional-configuration"></a>Konfiguracja opcjonalna

Te kroki nie są wymagane dla wszystkich klastrów. Są one niezbędne dla niektórych typów przepływów pracy lub niektórych stylów zarządzania klastrami. 

* Dostosuj ustawienia węzła

  Można ustawić nazwy węzłów i skonfigurować porty IPMI węzła na poziomie całego klastra lub pojedynczo. Jeśli te ustawienia zostaną skonfigurowane przed dodaniem węzłów do klastra, nowe węzły będą mogły automatycznie pobrać ustawienia podczas ich przyłączania. Opcje są opisane w dokumencie Tworzenie starszych klastrów [Dostosowywanie ustawień węzła](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > Niektóre dokumenty dotyczące tego produktu nie są jeszcze dostępne w witrynie dokumentacji Microsoft Azure. Linki do [przewodnika po konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) i starszej wersji [podręcznika tworzenia klastra](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) przeprowadzą Cię do oddzielnej witryny sieci Web hostowanej w usłudze GitHub. 

* Konfigurowanie protokołu SMB

  Jeśli chcesz zezwolić na dostęp SMB do klastra, a także systemu plików NFS, musisz skonfigurować protokół SMB i włączyć go. Protokół SMB (czasami nazywany CIFS) jest zazwyczaj używany do obsługi klientów systemu Microsoft Windows.

  Planowanie i Konfigurowanie protokołu SMB obejmuje więcej niż klikanie przycisków w panelu sterowania. W zależności od wymagań systemu protokół SMB może mieć wpływ na sposób definiowania plików podstawowych, liczbę utworzonych vservers, sposób konfigurowania połączeń i przestrzeni nazw, uprawnienia dostępu i inne ustawienia.

  Aby uzyskać więcej informacji, zapoznaj się z sekcją Konfiguracja klastra [Konfigurowanie dostępu do protokołu SMB](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) .

* Zainstaluj dodatkowe licencje

  Jeśli chcesz używać magazynu w chmurze innego niż Azure Blob, musisz zainstalować dodatkową licencję funkcji. Skontaktuj się z przedstawicielem firmy Microsoft, aby uzyskać szczegółowe informacje o zakupie licencji FlashCloud<sup>TM</sup> . Szczegółowe informacje znajdują się w temacie [Dodawanie magazynu zaplecza i konfigurowanie wirtualnej przestrzeni nazw](fxt-add-storage.md#about-back-end-storage).


### <a name="enable-support"></a>Włącz obsługę

Klaster plików usługi Azure FXT Edge może automatycznie przekazywać dane dotyczące obsługi klastra. Te operacje przekazywania umożliwiają personelowi dostarczenie najlepszej możliwej usługi klienta.

Wykonaj następujące kroki, aby skonfigurować obsługę przekazywania.

1. Przejdź do **Cluster**  >  strony ustawienia**obsługi** klastra. Zaakceptuj zasady ochrony prywatności. 

   ![Zrzut ekranu przedstawiający panel sterowania i wyskakujące okienko z przyciskiem Potwierdź, aby zaakceptować zasady ochrony prywatności](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Kliknij trójkąt z lewej strony **informacji o kliencie** , aby rozwinąć sekcję.
1. Kliknij przycisk ponownie **Sprawdź poprawność przekazywania informacji** .
1. Ustaw nazwę obsługi klastra na **unikatową nazwę klastra** — upewnij się, że jednoznacznie identyfikuje klaster, aby obsługiwał personel.
1. Zaznacz pola wyboru dotyczące **monitorowania statystyk**, **przekazywania informacji ogólnych**i **przekazywania informacji o awarii**.
1. Kliknij przycisk **Prześlij**.  

   ![Zrzut ekranu przedstawiający sekcję ukończonych informacji o kliencie strony ustawień pomocy technicznej](media/fxt-cluster-create/fxt-support-info.png)

1. Kliknij trójkąt po lewej stronie **bezpiecznej proaktywnej pomocy technicznej (SPS)** , aby rozwinąć sekcję.
1. Zaznacz pole wyboru **Włącz połączenie programu SPS**.
1. Kliknij przycisk **Prześlij**.

   ![Zrzut ekranu przedstawiający sekcję ukończona obsługa bezpiecznych proaktywnie na stronie ustawień obsługi](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Następne kroki

Po utworzeniu klastra podstawowego i zaakceptowaniu zasad ochrony prywatności należy dodać pozostałe węzły klastra. 

> [!div class="nextstepaction"]
> [Dodawanie węzłów klastra](fxt-add-nodes.md)
