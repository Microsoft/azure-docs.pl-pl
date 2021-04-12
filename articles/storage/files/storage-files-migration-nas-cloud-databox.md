---
title: Migracja lokalnego serwera NAS do udziałów plików platformy Azure
description: Dowiedz się, jak migrować pliki z lokalizacji magazynu w sieci lokalnej (NAS) do udziałów plików platformy Azure za pomocą usługi Azure DataBox.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 666e9f01d090acf29b8013470ed0264cd83f6d47
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2021
ms.locfileid: "106293638"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-azure-file-shares"></a>Użyj DataBox do migracji z magazynu dołączonego do sieci (NAS) do udziałów plików platformy Azure

Ten artykuł migracji jest jednym z kilku obejmujących słowa kluczowe websites NAS i Azure DataBox. Sprawdź, czy ten artykuł dotyczy Twojego scenariusza:

> [!div class="checklist"]
> * Źródło danych: magazyn dołączony do sieci (NAS)
> * Marszruta migracji: &rArr; DataBox &rArr; usługi Azure File Share
> * Buforowanie plików lokalnie: nie, ostatecznym celem jest używanie udziałów plików platformy Azure bezpośrednio w chmurze. Brak planu do użycia Azure File Sync.

Jeśli Twój scenariusz jest inny, przejrzyj [tabelę przewodników migracji](storage-files-migration-overview.md#migration-guides).

W tym artykule przedstawiono kompleksową konfigurację planowania, wdrażania i sieci wymaganych do migracji z urządzenia NAS do funkcjonalnych udziałów plików platformy Azure. Ten przewodnik używa usługi Azure DataBox do transportu danych zbiorczych (transport danych w trybie offline).

## <a name="migration-goals"></a>Cele migracji

Celem jest przeniesienie udziałów znajdujących się na urządzeniu NAS na platformę Azure i udostępnienie ich jako natywnych udziałów plików platformy Azure, których można użyć bez potrzeby korzystania z systemu Windows Server. Migracja musi być realizowana w sposób gwarantujący integralność danych produkcyjnych i dostępności podczas migracji. Druga z nich wymaga utrzymywania przestoju w minimalnym stopniu, dzięki czemu może być dłuższa niż w zwykłych oknach obsługi lub tylko nieco.

## <a name="migration-overview"></a>Omówienie migracji

Proces migracji składa się z kilku faz. Musisz wdrożyć konta usługi Azure Storage i udziały plików, skonfigurować sieć, przeprowadzić migrację za pomocą usługi Azure DataBox, przechwycić zmiany za pośrednictwem RoboCopy, a wreszcie wyciąć użytkowników do nowo utworzonych udziałów plików platformy Azure. W poniższych sekcjach szczegółowo opisano etapy procesu migracji.

> [!TIP]
> Jeśli powrócisz do tego artykułu, użyj nawigacji po prawej stronie, aby przejść do fazy migracji, w której zostało przerwane.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Faza 1: określenie, ile potrzebnych udziałów plików platformy Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>Faza 2: wdrażanie zasobów usługi Azure Storage

W tej fazie zapoznaj się z tabelą mapowania z fazy 1 i użyj jej do aprowizacji poprawnej liczby kont usługi Azure Storage i udziałów plików w nich.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>Faza 3: Określanie, ile urządzeń usługi Azure DataBox jest potrzebnych

Uruchom ten krok tylko po zakończeniu poprzedniej fazy. W tej chwili należy utworzyć zasoby usługi Azure Storage (konta magazynu i udziały plików). W ramach kolejności DataBox należy określić, do których kont magazynu DataBox przenosi dane.

W tej fazie należy zmapować wyniki planu migracji z poprzedniej fazy do limitów dostępnych opcji DataBox. Te zagadnienia pomogą Ci utworzyć plan, dla którego należy wybrać opcje DataBox i ile z nich należy przenieść udziały serwerów NAS do udziałów plików platformy Azure.

Aby określić liczbę urządzeń tego typu, należy wziąć pod uwagę następujące ważne limity:

* Każda usługa Azure DataBox może przenosić dane do maksymalnie 10 kont magazynu. 
* Każda opcja DataBox ma własną użyteczną pojemność. Zobacz [Opcje DataBox](#databox-options).

Zapoznaj się z planem migracji liczby kont magazynu, które zamierzasz utworzyć, oraz udziałów w każdej z nich. Następnie Przyjrzyj się rozmiarowi poszczególnych udziałów na serwerze NAS. Połączenie tych informacji umożliwi optymalizację i decyzję o tym, które urządzenie powinno wysyłać dane do tych kont magazynu. Na tym samym koncie magazynu mogą być przenoszone dwa urządzenia DataBox, ale zawartość pojedynczego udziału plików nie zostanie podzielona na 2 pola danych.

### <a name="databox-options"></a>Opcje DataBox

W przypadku standardowej migracji należy wybrać jedną lub kombinację tych trzech opcji DataBox: 

* DataBox dyski, firma Microsoft wyśle Ci i maksymalnie pięć dysków SSD z pojemnością 8 TiB każdy, dla maksymalnie 40 TiB. Wydajność użyteczną jest około 20% mniej, z powodu narzutu na szyfrowanie i system plików. Aby uzyskać więcej informacji, zobacz [Dokumentacja dysków DataBox](../../databox/data-box-disk-overview.md).
* DataBox to najbardziej typowe rozwiązanie. Urządzenie DataBox, które działa podobnie jak NAS, zostanie wysłane do Ciebie. Ma możliwość użycia pojemności 80 TiB. Aby uzyskać więcej informacji, zobacz [dokumentację DataBox](../../databox/data-box-overview.md).
* DataBox silnie ta opcja zapewnia wzmocnione urządzenie DataBox na kołach, które działa podobnie jak w przypadku serwera NAS o pojemności 1 PiB. Wydajność użyteczną jest około 20% mniej, z powodu narzutu na szyfrowanie i system plików. Aby uzyskać więcej informacji, zobacz [DataBox ciężki dokumentacja](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-provision-a-temporary-windows-server"></a>Faza 4. Udostępnianie tymczasowego systemu Windows Server

Podczas oczekiwania na dotarcie do usługi Azure DataBox można już wdrożyć co najmniej jeden serwer z systemem Windows, który będzie potrzebny do uruchamiania zadań RoboCopy. 

- Pierwsze użycie tych serwerów będzie miało na celu skopiowanie plików na DataBox.
- Drugie użycie tych serwerów będzie miało na celu podwyższenie poziomu zmian, które wystąpiły na urządzeniu NAS, podczas gdy DataBox był w trakcie transportu. Takie podejście powoduje, że czas przestoju po stronie źródła jest minimalny.

Szybkość, z jaką zadania RoboCopy działają, zależy głównie od następujących czynników:

* Liczba operacji we/wy na magazynie źródłowym i docelowym
* dostępna przepustowość sieci między nimi </br> Więcej szczegółów znajduje się w sekcji Rozwiązywanie problemów: liczby [IOPS i informacje dotyczące przepustowości](#iops-and-bandwidth-considerations)
* możliwość szybkiego przetwarzania plików i folderów w przestrzeni nazw </br> Więcej szczegółów znajduje się w sekcji Rozwiązywanie problemów: [szybkość przetwarzania](#processing-speed)
* Liczba zmian między RoboCopy uruchomieniami </br> Więcej szczegółów znajduje się w sekcji Rozwiązywanie problemów: [Unikaj niepotrzebnej pracy](#avoid-unnecessary-work)

Ważne jest, aby przed podjęciem decyzji dotyczącej ilości pamięci RAM i liczby wątków zapewnić obsługę tymczasowych serwerów systemu Windows.

## <a name="phase-5-preparing-to-use-azure-file-shares"></a>Faza 5: przygotowywanie do korzystania z udziałów plików platformy Azure

Aby zaoszczędzić czas, należy kontynuować tę fazę podczas oczekiwania na dotarcie DataBox. Korzystając z informacji w tej fazie, będziesz mieć możliwość decydowania, w jaki sposób serwery i użytkownicy na platformie Azure oraz poza platformą Azure będą mogli korzystać z udziałów plików platformy Azure. Najważniejsze decyzje to:

- **Sieć:** Zezwól sieci na kierowanie ruchu SMB.
- **Uwierzytelnianie:** Skonfiguruj konta usługi Azure Storage na potrzeby uwierzytelniania Kerberos. AdConnect i dołączanie do konta magazynu umożliwi Twoim aplikacjom i użytkownikom używanie ich tożsamości usługi AD do uwierzytelniania
- **Autoryzacja:** Listy ACL na poziomie udziału dla każdego udziału plików platformy Azure umożliwią użytkownikom i grupom usługi AD dostęp do danego udziału i w udziale plików platformy Azure. Autoryzacja oparta na listach ACL plików i folderów działa podobnie jak w przypadku lokalnych udziałów SMB.
- **Ciągłość działalności biznesowej:** Integracja udziałów plików platformy Azure z istniejącym środowiskiem często wiąże się z zachowaniem istniejących adresów udziału. Jeśli nie korzystasz jeszcze z przestrzeni nazw systemu plików DFS, rozważ ustanowienie tego w środowisku. Można zachować dostęp do udziału, który jest używany przez użytkowników i skrypty bez zmian. Użyj systemu plików DFS-N jako usługi routingu przestrzeni nazw dla protokołu SMB przez przekierowanie DFS-Namespace obiektów docelowych do udziałów plików platformy Azure po ich migracji.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Ten film wideo to przewodnik i demonstracja, jak bezpiecznie ujawniać udziały plików platformy Azure bezpośrednio pracownikom przetwarzającym informacje i aplikacje w pięciu prostych krokach.</br>
        Film wideo odwołuje się do dedykowanej dokumentacji dotyczącej niektórych tematów:

* [Przegląd tożsamości](storage-files-active-directory-overview.md)
* [Dołączanie do domeny do konta magazynu](storage-files-identity-auth-active-directory-enable.md)
* [Omówienie sieci dla udziałów plików platformy Azure](storage-files-networking-overview.md)
* [Jak skonfigurować publiczne i prywatne punkty końcowe](storage-files-networking-endpoints.md)
* [Jak skonfigurować sieć VPN S2S](storage-files-configure-s2s-vpn.md)
* [Jak skonfigurować sieć VPN z systemem Windows P2S](storage-files-configure-p2s-vpn-windows.md)
* [Jak skonfigurować sieć VPN z systemem Linux P2S](storage-files-configure-p2s-vpn-linux.md)
* [Jak skonfigurować przekazywanie DNS](storage-files-networking-dns.md)
* [Konfigurowanie systemu plików DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

## <a name="phase-6-copy-files-onto-your-databox"></a>Faza 6. Kopiowanie plików na DataBox

Po nadejściu DataBox musisz skonfigurować swoje DataBox w linii wglądu do urządzenia NAS. Postępuj zgodnie z dokumentacją Instalatora dla uporządkowanego typu DataBox.

* [Konfigurowanie usługi Data Box](../../databox/data-box-quickstart-portal.md)
* [Konfigurowanie usługi Data Box Disk](../../databox/data-box-disk-quickstart-portal.md)
* [Konfigurowanie usługi Data Box Heavy](../../databox/data-box-heavy-quickstart-portal.md)

W zależności od typu DataBox dostępne są DataBox narzędzia do kopiowania. W tym momencie nie zaleca się przeprowadzania migracji do udziałów plików platformy Azure, ponieważ nie kopiują one plików z pełną dokładnością do DataBox. Zamiast tego użyj RoboCopy.

Po odebraniu usługi DataBox będą dostępne wstępnie udostępniane udziały SMB dla każdego konta magazynu określonego w momencie jego uporządkowania.

* Jeśli pliki znajdują się w udziale plików platformy Azure w warstwie Premium, na koncie magazynu "Magazyn plików Premium" będzie dostępny jeden udział SMB.
* Jeśli pliki znajdują się na koncie magazynu w warstwie Standardowa, będą dostępne trzy udziały SMB na standardowym koncie magazynu (GPv1 i GPv2). Tylko udziały plików kończące `_AzFiles` się na są istotne dla migracji. Ignoruj wszystkie udziały blokowe i obiekty blob typu Page.

Wykonaj kroki opisane w dokumentacji usługi Azure DataBox:

1. [Nawiązywanie połączenia z urządzeniem Data Box](../../databox/data-box-deploy-copy-data.md)
1. Kopiowanie danych na urządzenie Data Box
1. [Przygotuj DataBox do wyjścia na platformę Azure](../../databox/data-box-deploy-picked-up.md)

Połączona dokumentacja DataBox Określa polecenie RoboCopy. Jednak polecenie nie jest odpowiednie, aby zachować pełną wierność plików i folderów. Użyj tego polecenia zamiast:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-7-catch-up-robocopy-from-your-nas"></a>Faza 7: Przechwytywanie RoboCopy z serwera NAS

Gdy DataBox raporty, że wszystkie pliki i foldery zostały umieszczone w planowanych udziałach plików platformy Azure, możesz kontynuować tę fazę.
RoboCopy przechwytywania jest wymagana tylko wtedy, gdy dane na serwerze NAS mogły ulec zmianie od momentu uruchomienia kopii DataBox. W niektórych scenariuszach, w których jest używany udział do archiwizowania, można zatrzymać zmiany udziału na serwerze NAS do momentu zakończenia migracji. Możesz również mieć możliwość spełnienia wymagań firmy przez ustawienie udziałów NAS w trybie tylko do odczytu podczas migracji.

W przypadkach, gdy jest potrzebny udział do odczytu i zapisu podczas migracji i może on tylko wchłonąć małe okno przestojów, ten krok RoboCopy zostanie ważny przed przejściem do trybu failover użytkownika bezpośrednio do udziału plików platformy Azure.

W tym kroku zostaną uruchomione zadania RoboCopy, aby przechwycić udziały w chmurze przy użyciu najnowszych zmian wprowadzonych na serwerze NAS od czasu przetworzenia rozwidlenia udziałów na DataBox.
Ten RoboCopy może zakończyć się szybko lub trochę potrwać, w zależności od liczby zmian, które wystąpiły w udziałach serwera NAS.

Uruchom pierwszą kopię lokalną w folderze docelowym systemu Windows Server:

1. Zidentyfikuj pierwszą lokalizację na urządzeniu NAS.
1. Zidentyfikuj pasujący udział plików platformy Azure.
1. Instalowanie udziału plików platformy Azure jako lokalnego dysku sieciowego na tymczasowym serwerze z systemem Windows
1. Rozpocznij kopiowanie za pomocą RoboCopy zgodnie z opisem

### <a name="mounting-an-azure-file-share"></a>Instalowanie udziału plików platformy Azure

Aby można było korzystać z RoboCopy, należy udostępnić udział plików platformy Azure za pośrednictwem protokołu SMB. Najprostszym sposobem jest zainstalowanie udziału jako lokalnego dysku sieciowego na serwerze z systemem Windows Server, na którym planujesz korzystać z usługi RoboCopy. 

> [!IMPORTANT]
> Aby można było pomyślnie zainstalować udział plików platformy Azure na lokalnym serwerze z systemem Windows, należy ukończyć fazę: przygotowywanie do korzystania z udziałów plików platformy Azure!

Gdy wszystko będzie gotowe, zapoznaj się z [artykułem "jak to zrobić" przy użyciu udziału plików platformy Azure](storage-how-to-use-files-windows.md) i zainstaluj udział plików platformy Azure, dla którego chcesz uruchomić serwer nas Robocopy.

### <a name="robocopy"></a>RoboCopy

Następujące polecenie RoboCopy skopiuje tylko różnice (zaktualizowane pliki i foldery) z magazynu NAS do udziału plików platformy Azure. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> [Zapoznaj się z sekcją rozwiązywania problemów](#troubleshoot) , jeśli Robocopy ma wpływ na środowisko produkcyjne, zgłasza wiele błędów lub nie postępuje zgodnie z oczekiwaniami.

### <a name="user-cut-over"></a>Wytnij użytkownika

Po pierwszym uruchomieniu polecenia RoboCopy Użytkownicy i aplikacje nadal uzyskują dostęp do plików na serwerze NAS i mogą je zmienić. Istnieje możliwość, że RoboCopy przetworzył katalog, przechodzi do następnego, a następnie użytkownik w lokalizacji źródłowej (NAS) dodaje, zmienia lub usuwa plik, który nie zostanie przetworzony w bieżącym przebiegu RoboCopy. Takie zachowanie jest oczekiwane.

Pierwszy przebieg ma na celu przeniesienie zbiorczych danych do udziału plików platformy Azure. Ta pierwsza kopia może chwilę potrwać. Zapoznaj się z [sekcją rozwiązywania problemów](#troubleshoot) , aby uzyskać więcej informacji o tym, co może mieć wpływ na szybkości Robocopy.

Po zakończeniu pierwszego uruchomienia Uruchom polecenie ponownie.

Drugi raz uruchamiasz RoboCopy dla tego samego udziału, zostanie on szybszy, ponieważ wymaga tylko zmiany, które wystąpiły od momentu ostatniego uruchomienia. Można uruchamiać powtarzające się zadania dla tego samego udziału.

Po rozważeniu niedopuszczalnego przestoju należy usunąć dostęp użytkowników do udziałów opartych na protokole NAS. Można to zrobić, wykonując czynności, które uniemożliwiają użytkownikom zmianę struktury plików i folderów oraz zawartości. Przykładem jest wskazanie nieistniejącej lokalizacji DFS-Namespace lub zmianę głównych list kontroli dostępu w udziale.

Uruchom jeden ostatni RoboCopy zaokrąglenie. Spowoduje to pobranie wszelkich zmian, które mogły zostać pominięte.
Jak długo trwa ten ostatni krok, zależy od szybkości skanowania RoboCopy. Możesz oszacować czas (który jest równy przestoju), mierząc, jak długo trwało wykonywanie poprzedniego uruchomienia.

Utwórz udział w folderze systemu Windows Server i ewentualnie Dostosuj wdrożenie systemu plików DFS-N, aby wskazywało na niego. Pamiętaj, aby ustawić te same uprawnienia na poziomie udziału jak w udziale SMB NAS. Jeśli masz serwer NAS przyłączony do domeny przedsiębiorstwa, identyfikatory SID użytkownika będą automatycznie zgodne, ponieważ użytkownicy znajdują się w Active Directory i RoboCopy kopiuje pliki i metadane z pełną dokładnością. Jeśli używasz lokalnych użytkowników na serwerze NAS, należy ponownie utworzyć tych użytkowników jako lokalnych użytkowników systemu Windows Server i zmapować istniejące identyfikatory SID RoboCopy przenoszone do systemu Windows Server na identyfikatory SID nowych użytkowników lokalnych systemu Windows Server.

Zakończono Migrowanie udziału/grupy udziałów do wspólnego katalogu głównego lub woluminu. (W zależności od mapowania od fazy 1)

Można spróbować uruchomić kilka z tych kopii równolegle. Zalecamy przetwarzanie zakresu jednego udziału plików platformy Azure w danym momencie.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Szybkość i szybkość powodzeń danego uruchomienia RoboCopy będą zależeć od kilku czynników:

* Liczba operacji we/wy na magazynie źródłowym i docelowym
* dostępna przepustowość sieci między nimi
* możliwość szybkiego przetwarzania plików i folderów w przestrzeni nazw
* Liczba zmian między RoboCopy uruchomieniami


### <a name="iops-and-bandwidth-considerations"></a>Informacje o liczbie IOPS i przepustowości

W tej kategorii należy wziąć pod uwagę możliwości **źródłowe** (nas), **miejsce docelowe** (Azure DataBox i nowsze udziały plików platformy Azure) oraz **sieci** łączące się z nimi. Maksymalna możliwa przepływność jest określana na podstawie najwolniejszych z tych trzech składników. Standardowa DataBox obejmuje dwa interfejsy sieciowe 10 GB/s. W zależności od serwera NAS może być możliwe dopasowanie tego programu. Upewnij się, że infrastruktura sieciowa jest skonfigurowana tak, aby obsługiwała optymalne szybkości transferu do swoich najlepszych umiejętności.

> [!CAUTION]
> Podczas kopiowania tak szybko, jak to możliwe jest często najbardziej trudniejsze, rozważ użycie sieci lokalnej i urządzenia NAS dla innych, często ważnych zadań dla działalności biznesowej.

Kopiowanie tak szybko, jak to możliwe może być niepożądane, gdy istnieje ryzyko, że migracja może monopolize dostępne zasoby.

* Należy wziąć pod uwagę, kiedy w środowisku najlepiej jest uruchamiać migracje: w ciągu dnia, godziny lub w weekendy.
* Należy również rozważyć ustawienia QoS sieci na serwerze z systemem Windows, aby ograniczyć szybkość RoboCopyi, a tym samym wpływ na NAS i sieć.
* Unikaj niepotrzebnych zadań dla narzędzi migracji.

RobCopy może również mieć możliwość wstawiania opóźnień między pakietami przez określenie przełącznika, `/IPG:n` gdzie `n` jest mierzony w milisekundach między pakietami Robocopy. Użycie tego przełącznika może pomóc w uniknięciu monopolization zasobów na urządzeniach NAS z ograniczeniami we/wy i wysoce wykorzystywanych łączach sieciowych. 

`/IPG:n` nie można użyć do precyzyjnego ograniczania przepustowości sieci do określonej liczby MB/s. Zamiast tego użyj funkcji QoS sieci systemu Windows Server. RoboCopy całkowicie opiera się na protokole SMB dla całej sieci i w ten sposób nie ma możliwości wywierania wpływu na przepływność sieci, ale może spowolnić jego wykorzystanie. 

Podobna linia myśli dotyczy operacji we/wy zaobserwowanej na serwerze NAS. Rozmiar klastra w woluminie NAS, rozmiary pakietów i tablica innych czynników wpływają na zaobserwowane operacje we/wy na sekundę. Wprowadzenie opóźnienia między pakietami jest często najprostszym sposobem sterowania obciążeniem serwera NAS. Przetestuj wiele wartości, na przykład od około 20 milisekund (n = 20) do wielokrotności, aby zobaczyć, ile opóźnienia pozwala na obsługę innych wymagań, przy jednoczesnym zachowaniu maksymalnej szybkości RoboCopy na potrzeby ograniczeń.

### <a name="processing-speed"></a>Szybkość przetwarzania

RoboCopy przejdzie do obszaru nazw, do którego się odnosi, i oceni każdy plik i folder do kopiowania. Każdy plik zostanie oszacowany podczas kopiowania wstępnego, na przykład kopii za pośrednictwem sieci lokalnej do DataBox, a nawet podczas przechwytywania kopii za pośrednictwem łącza sieci WAN do udziału plików platformy Azure.

Często domyślnie rozważamy wykorzystanie przepustowości jako czynnik najbardziej ograniczającego w migracji — i to może być prawdziwe. Jednak możliwość wyliczenia przestrzeni nazw może mieć wpływ na łączny czas kopiowania jeszcze więcej dla większych przestrzeni nazw o mniejszych plikach. Należy wziąć pod uwagę, że kopiowanie 1 TiB małych plików zajmie znacznie dłużej niż kopiowanie 1 TiB mniejszej liczby plików, a wszystkie inne zmienne są takie same.

Przyczyną tej różnicy jest moc obliczeniowa, która jest wymagana do przeszukania w przestrzeni nazw. RoboCopy obsługuje kopie wielowątkowe za pomocą parametru, `/MT:n` gdzie n oznacza liczbę wątków procesora. Dlatego w przypadku inicjowania obsługi maszyny przeznaczonej dla RoboCopy należy wziąć pod uwagę liczbę rdzeni procesora i ich powiązania z liczbą wątków, które zapewnia. Najczęstsze są dwa wątki na rdzeń. Liczba rdzeni i wątków maszyny jest ważnym punktem danych, aby zdecydować, jakie wartości wielowątkowości należy `/MT:n` określić. Należy również wziąć pod uwagę liczbę zadań RoboCopy, które planujesz uruchamiać równolegle na danym komputerze.

Więcej wątków skopiujemy nasz przykład 1Tib małych plików znacznie szybciej niż w przypadku mniejszych wątków. W tym samym czasie istnieje spadek spadku inwestycji w 1Tib większych plików. Nadal będą kopiować więcej wątków, które przypiszesz, ale zwiększenie przepustowości sieci lub operacji we/wy jest ograniczone.

### <a name="avoid-unnecessary-work"></a>Unikaj niepotrzebnej pracy

Unikaj zmian w dużej skali w przestrzeni nazw. Obejmuje to przeniesienie plików między katalogami, zmianę właściwości na dużą skalę lub zmianę uprawnień (list ACL systemu plików NTFS), ponieważ często mają efekt kaskadowy, gdy listy ACL folderów bliżej katalogu głównego udziału są zmienione. Konsekwencje mogą być następujące:

* rozszerzony czas wykonywania zadania RoboCopy ze względu na wszystkie pliki i foldery, do których ma wpływ zmiana listy kontroli dostępu
* skuteczność używania DataBox w pierwszym miejscu może ulec zmniejszeniu, gdy struktury folderów uległy zmianie po skopiowaniu plików do DataBox. Zadanie RoboCopy nie będzie mogło odwrócić zmiany przestrzeni nazw i należy przeczyścić pliki transportowane do udziału plików platformy Azure i ponownie przekazać pliki w nowej strukturze folderów do platformy Azure.

Innym ważnym aspektem jest użycie narzędzia RoboCopy efektywnie. Zalecanym skryptem RoboCopy jest utworzenie i zapisanie pliku dziennika pod kątem błędów. Błędy kopiowania mogą wystąpić — to jest normalne. Te błędy często sprawiają, że konieczne jest uruchomienie wielu rund narzędzia kopiowania, takiego jak RoboCopy. Początkowy przebieg, powiedzmy od serwera NAS do DataBox i co najmniej jedną dodatkową z przełącznikiem/MIR, aby przechwytywać i ponawiać pliki, które nie zostały skopiowane.

Należy przygotować się do uruchamiania wielu rund RoboCopy dla danego zakresu przestrzeni nazw. Kolejne uruchomienia zakończą się szybciej, ponieważ mają mniej do skopiowania, ale są ograniczone przez szybkość przetwarzania przestrzeni nazw. W przypadku uruchamiania wielu operacji zaokrąglania można przyspieszyć poszczególne operacje, nie mając RoboCopy, spróbuj bardzo trudne do skopiowania wszystkiego przy pierwszej próbie. Te przełączniki RoboCopy mogą mieć znaczącą różnicę:

* `/R:n` n = częstotliwość ponawiania próby skopiowania pliku zakończonego niepowodzeniem i 
* `/W:n` n = liczba sekund oczekiwania między ponownymi próbami

`/R:5 /W:5` jest odpowiednim ustawieniem, które można dostosować do własnych potrzeb. W tym przykładzie plik, który uległ awarii, zostanie ponowiony pięć razy, z pięciu sekund czasu oczekiwania między ponownymi próbami. Jeśli plik nadal nie może zostać skopiowany, następne zadanie RoboCopy zostanie ponowione i często trwające pliki, które nie powiodły się, ponieważ są używane lub problemy z limitem czasu mogą zostać pomyślnie skopiowane w ten sposób.


## <a name="next-steps"></a>Następne kroki

Istnieje więcej informacji o udziałach plików platformy Azure. Poniższe artykuły ułatwiają zapoznanie się z zaawansowanymi opcjami, najlepszymi rozwiązaniami, a także zawiera pomoc dotyczącą rozwiązywania problemów. Te artykuły zawierają link do [dokumentacji udziału plików platformy Azure](storage-files-introduction.md) zgodnie z potrzebami.

* [Omówienie migracji](storage-files-migration-overview.md)
* [Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage](../common/storage-monitoring-diagnosing-troubleshooting.md)
* [Zagadnienia dotyczące pracy w sieci na potrzeby dostępu bezpośredniego](storage-files-networking-overview.md)
* [Kopia zapasowa: migawki udziałów plików platformy Azure](storage-snapshots-files.md)
