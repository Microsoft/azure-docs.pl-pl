---
title: Rozwiązywanie problemów z certyfikatem maszyny wirtualnej w witrynie Azure Marketplace
description: Rozwiązywanie typowych problemów związanych z testowaniem i certyfikowaniem obrazów maszyn wirtualnych w portalu Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 5124e7084a8e878e54e594424ec0926410f002ca
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682489"
---
# <a name="troubleshoot-virtual-machine-certification"></a>Rozwiązywanie problemów z certyfikatem maszyny wirtualnej

W przypadku publikowania obrazu maszyny wirtualnej w portalu Azure Marketplace zespół platformy Azure sprawdza jego poprawność, aby upewnić się, że jest on rozruchowy, bezpieczny i zgodny z platformą Azure. Jeśli obraz maszyny wirtualnej ulegnie awarii na dowolnym z testów wysokiej jakości, nie zostanie opublikowany. Zostanie wyświetlony komunikat o błędzie opisujący problem.

W tym artykule opisano typowe komunikaty o błędach podczas publikowania obrazów maszyn wirtualnych oraz powiązane rozwiązania.

> [!NOTE]
> Jeśli masz pytania dotyczące tego artykułu lub sugestie dotyczące ulepszeń, skontaktuj się z [pomocą techniczną Centrum partnerskiego](https://aka.ms/marketplacepublishersupport).

## <a name="approved-base-image"></a>Zatwierdzony obraz podstawowy

Gdy wyślesz żądanie ponownego opublikowania obrazu przy użyciu aktualizacji, przypadek testowy weryfikacji liczby częściowej może zakończyć się niepowodzeniem. Jeśli to się nie powiedzie, obraz nie zostanie zatwierdzony.

Ten błąd występuje, gdy używany jest podstawowy obraz, który należy do innego wydawcy i został zaktualizowany. W tej sytuacji nie będzie można opublikować Twojego obrazu.

Aby rozwiązać ten problem, pobierz obraz z witryny Azure Marketplace i wprowadź w nim zmiany. Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Obrazy systemu Linux](../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Obrazy systemu Windows](azure-vm-create-using-approved-base.md)

> [!Note]
> Jeśli używasz podstawowego obrazu systemu Linux, który nie został pobrany z portalu Azure Marketplace, możesz przesunąć pierwszą partycję o 2048 KB. Pozwala to na niesformatowane miejsce na dodanie nowych informacji dotyczących rozliczeń i umożliwia korzystanie z platformy Azure z publikowaniem maszyny wirtualnej w portalu Azure Marketplace.  

## <a name="vm-extension-failure"></a>Niepowodzenie rozszerzenia maszyny wirtualnej

Sprawdź, czy obraz obsługuje rozszerzenia maszyn wirtualnych.

Aby włączyć rozszerzenia maszyn wirtualnych:

1. Wybierz maszynę wirtualną z systemem Linux.
1. Przejdź do pozycji **Ustawienia diagnostyki**.
1. Aby włączyć macierze podstawowe, zaktualizuj **konto magazynu**.
1. Wybierz pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający sposób włączania monitorowania na poziomie gościa.](./media/create-vm/vm-certification-issues-solutions-1.png)

Aby sprawdzić, czy rozszerzenia maszyn wirtualnych są prawidłowo aktywowane:

1. Na maszynie wirtualnej wybierz kartę **rozszerzenia maszyn wirtualnych** , a następnie sprawdź stan **rozszerzenia diagnostyki systemu Linux**.
1. Sprawdź stan aprowizacji.

   - W przypadku *pomyślnego stanu aprowizacji*, przypadek testowy rozszerzeń został zakończony pomyślnie.  
   - W przypadku *niepowodzenia inicjowania obsługi administracyjnej* przypadek testowy rozszerzeń zakończył się niepowodzeniem i należy ustawić flagę z ograniczeniami.

   ![Zrzut ekranu przedstawiający pomyślne zainicjowanie obsługi.](./media/create-vm/vm-certification-issues-solutions-2.png)

   Jeśli rozszerzenie maszyny wirtualnej nie powiedzie się, zobacz [Używanie rozszerzenia diagnostycznego systemu Linux do monitorowania metryk i dzienników](../virtual-machines/extensions/diagnostics-linux.md) , aby je włączyć. Jeśli nie chcesz, aby rozszerzenie maszyny wirtualnej zostało włączone, skontaktuj się z zespołem pomocy technicznej i poproś o jego wyłączenie.

## <a name="vm-provisioning-issue"></a>Problem z obsługą maszyny wirtualnej

Przed przesłaniem oferty upewnij się, że masz rygorystyczne podejście do inicjowania obsługi maszyny wirtualnej. Aby wyświetlić format JSON obsługi administracyjnej maszyny wirtualnej, zobacz [Testowanie obrazu maszyny wirtualnej](azure-vm-image-test.md).

Problemy z aprowizacjim mogą obejmować następujące scenariusze awarii:

|Scenariusz|Błąd|Przyczyna|Rozwiązanie|
|---|---|---|---|
|1|Nieprawidłowy wirtualny dysk twardy (VHD)|Jeśli określona wartość pliku cookie w stopce dysku VHD jest niepoprawna, wirtualny dysk twardy będzie uznawany za nieprawidłowy.|Utwórz ponownie obraz i prześlij żądanie.|
|2|Nieprawidłowy typ obiektu BLOB|Inicjowanie obsługi maszyny wirtualnej nie powiodło się, ponieważ użyty blok jest typem obiektu BLOB, a nie typem strony.|Utwórz ponownie obraz i prześlij żądanie.|
|3|Limit czasu aprowizacji lub nieprawidłowo uogólniony|Wystąpił problem z generalizacją maszyn wirtualnych.|Utwórz ponownie obraz z generalizacją i prześlij żądanie.|

> [!NOTE]
> Aby uzyskać więcej informacji na temat generalizacji maszyn wirtualnych, zobacz:
> - [Dokumentacja systemu Linux](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Dokumentacja systemu Windows](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)


## <a name="vhd-specifications"></a>Specyfikacje wirtualnego dysku twardego

### <a name="conectix-cookie-and-other-vhd-specifications"></a>Conectix plik cookie i inne specyfikacje wirtualnego dysku twardego

Ciąg "conectix" jest częścią specyfikacji wirtualnego dysku twardego. Jest on definiowany jako 8-bajtowy plik cookie w stopce wirtualnego dysku twardego, który identyfikuje twórcę pliku. Wszystkie pliki VHD utworzone przez firmę Microsoft mają ten plik cookie. 

Obiekt BLOB sformatowanego wirtualnego dysku twardego powinien mieć 512-bajtową stopkę w tym formacie:

|Pola stopki dysku twardego|Rozmiar (w bajtach)|
|---|---|
Plików|8
Funkcje|4
Wersja formatu pliku|4
Przesunięcie danych|8
Sygnatura czasowa|4
Aplikacja dla twórców|4
Wersja twórcy|4
System operacyjny hosta twórcy|4
Oryginalny rozmiar|8
Bieżący rozmiar|8
Geometria dysku|4
Typ dysku|4
Suma kontrolna|4
Unikatowy identyfikator|16
Zapisany stan|1
Zarezerwowany|427


### <a name="vhd-specifications"></a>Specyfikacje wirtualnego dysku twardego

Aby zapewnić płynne publikowanie, upewnij się, że wirtualny dysk twardy spełnia następujące kryteria:

- Plik cookie zawiera ciąg "conectix".
- Typ dysku został naprawiony.
- Rozmiar wirtualny wirtualnego dysku twardego wynosi co najmniej 20 MB.
- Wirtualny dysk twardy jest wyrównany. Rozmiar wirtualny musi być wielokrotnością 1 MB.
- Długość obiektu BLOB wirtualnego dysku twardego jest równa rozmiarowi wirtualnemu i długości stopki dysku VHD (512).

Pobierz [specyfikację wirtualnego dysku twardego](https://www.microsoft.com/download/details.aspx?id=23850).

## <a name="software-compliance-for-windows"></a>Zgodność oprogramowania dla systemu Windows

Jeśli żądanie obrazu systemu Windows zostało odrzucone z powodu problemu ze zgodnością oprogramowania, być może utworzono obraz systemu Windows z zainstalowanym wystąpieniem SQL Server. Zamiast tego należy skorzystać z odpowiedniego obrazu podstawowego w wersji SQL Server z witryny Azure Marketplace.

Nie należy tworzyć własnego obrazu systemu Windows z zainstalowanym SQL Server. Użyj zatwierdzonych obrazów podstawowych SQL Server (Enterprise/Standard/Web) z witryny Azure Marketplace.

Jeśli próbujesz zainstalować program Visual Studio lub dowolny produkt licencjonowany w pakiecie Office, skontaktuj się z zespołem pomocy technicznej w celu uzyskania wcześniejszej zgody.

Aby uzyskać więcej informacji na temat wybierania zatwierdzonej bazy, zobacz [Tworzenie maszyny wirtualnej na podstawie zatwierdzonej podstawy](azure-vm-create-using-approved-base.md).

## <a name="toolkit-test-case-execution-failed"></a>Wykonanie przypadku testowego Toolkit nie powiodło się

Zestaw narzędzi certyfikacji firmy Microsoft może pomóc w uruchomieniu przypadków testowych i upewnić się, że dysk VHD lub obraz jest zgodny ze środowiskiem platformy Azure.

Pobierz [zestaw narzędzi certyfikacji firmy Microsoft](azure-vm-image-test.md).

### <a name="linux-test-cases"></a>Przypadki testowe systemu Linux

Poniższa tabela zawiera listę przypadków testowych systemu Linux, które będą uruchamiane przez zestaw narzędzi. Sprawdzanie poprawności testu jest określone w opisie.

|Scenariusz|Przypadek testowy|Opis|
|---|---|---|
|1|Historia bash|Pliki historii bash powinny zostać wyczyszczone przed utworzeniem obrazu maszyny wirtualnej.|
|2|Wersja agenta systemu Linux|Należy zainstalować agenta 2.2.41 systemu Linux lub nowszego.|
|3|Wymagane parametry jądra|Sprawdza, czy ustawiono następujące parametry jądra: <br>Konsola = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300 |
|4|Wymiana partycji na dysku systemu operacyjnego|Sprawdza, czy partycje wymiany nie są tworzone na dysku systemu operacyjnego.|
|5|Partycja główna na dysku systemu operacyjnego|Utwórz jedną partycję główną dla dysku systemu operacyjnego.|
|6|Wersja OpenSSL|Wersja OpenSSL powinna mieć wartość v 0.9.8 lub nowszą.|
|7|Wersja języka Python|Zdecydowanie zaleca się używanie języka Python w wersji 2,6 lub nowszej.|
|8|Interwał aktywności klienta|Ustaw wartość ClientAliveInterval na 180. Na potrzeby aplikacji można ustawić wartość z przeliczania od 30 do 235. W przypadku włączenia protokołu SSH dla użytkowników końcowych należy ustawić tę wartość jako wyjaśnioną.|
|9|Architektura systemu operacyjnego|Obsługiwane są tylko 64-bitowe systemy operacyjne.|
|10|Aktualizuj aktualizacje|Określa, czy jest włączona funkcja autoaktualizacji agenta systemu Linux.|

### <a name="common-test-case-errors"></a>Typowe błędy przypadków testowych

W poniższej tabeli przedstawiono typowe błędy, które mogą pojawić się podczas wykonywania przypadków testowych:

| Scenariusz | Przypadek testowy | Błąd | Rozwiązanie |
| --- | --- | --- | --- |
| 1 | Przypadek testowy wersji agenta systemu Linux | Minimalna wersja agenta systemu Linux to 2.2.41 lub nowsza. To wymaganie jest obowiązkowe od 1 maja 2020. | Zaktualizuj wersję agenta systemu Linux. Powinna być 2,241 lub nowsza. Aby uzyskać więcej informacji, odwiedź [stronę aktualizacji agenta systemu Linux](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support). |
| 2 | Przypadek testowy historii bash | Błąd występuje, jeśli rozmiar historii bash w przesłanym obrazie przekracza 1 kilobajt (KB). Rozmiar jest ograniczony do 1 KB, aby upewnić się, że plik historii bash nie zawiera żadnych potencjalnie poufnych informacji. | Rozwiąż problem, instalując dysk VHD na innej działającej maszynie wirtualnej i wprowadź zmiany, aby zmniejszyć rozmiar do 1 KB lub mniej. Na przykład Usuń `.bash` pliki historii. |
| 3 | Wymagany przypadek testowy parametru jądra | Ten błąd zostanie wyświetlony, gdy wartość parametru `console` nie jest ustawiona na `ttyS0` . Sprawdź, uruchamiając następujące polecenie: <br /> `cat /proc/cmdline` | Ustaw wartość na `console` na `ttyS0` i ponownie prześlij żądanie. |
| 4 | Przypadek testowy interwału ClientAlive | Jeśli zestaw narzędzi daje wynik niepowodzenia dla tego przypadku testowego, istnieje niewłaściwa wartość dla `ClientAliveInterval` . | Ustaw wartość na `ClientAliveInterval` mniejszą lub równą 235, a następnie prześlij żądanie ponownie. |


### <a name="windows-test-cases"></a>Przypadki testowe systemu Windows

W poniższej tabeli przedstawiono przypadki testowe systemu Windows, które będą uruchamiane przez zestaw narzędzi, wraz z opisem weryfikacji testu:

|Scenariusz |Przypadki testowe|Opis|
|---|---|---|---|
|1|Architektura systemu operacyjnego|Platforma Azure obsługuje tylko 64-bitowe systemy operacyjne.|
|2|Zależność konta użytkownika|Wykonanie aplikacji nie powinno być zależne od konta administratora.|
|3|Klaster trybu failover|Funkcja klaster trybu failover systemu Windows Server nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|4|If|Protokół IPv6 nie jest jeszcze obsługiwany w środowisku platformy Azure. Aplikacja nie powinna być zależna od tej funkcji.|
|5|DHCP|Rola serwera Dynamic Host Configuration Protocol nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|6|Hyper-V|Rola serwera funkcji Hyper-V nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|7|Dostęp zdalny|Rola serwera dostępu zdalnego (bezpośredni dostęp) nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|8|Usługi Rights Management|Rights Management Services. Rola serwera nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|9|Usługi wdrażania systemu Windows|Usługi wdrażania systemu Windows. Rola serwera nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|10|Szyfrowanie dysków funkcją BitLocker|Szyfrowanie dysków funkcją BitLocker nie jest obsługiwana na dysku twardym systemu operacyjnego, ale może być używany na dyskach danych.|
|11|Serwer nazw magazynu internetowego|Funkcja serwera nazw internetowych nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|12|Wielościeżkowe We/Wy|Wielościeżkowe we/wy. Ta funkcja serwera nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|13|Równoważenie obciążenia sieciowego|Równoważenie obciążenia sieciowego. Ta funkcja serwera nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|14|Protokół PNRP|Protokół rozpoznawania nazw równorzędnych. Ta funkcja serwera nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|15|Usługi SNMP|Funkcja usług Simple Network Management Protocol (SNMP) nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|16|Usługa nazw internetowych systemu Windows|Usługa nazw internetowych systemu Windows. Ta funkcja serwera nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|17|Usługa bezprzewodowej sieci LAN|Usługa bezprzewodowej sieci LAN. Ta funkcja serwera nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|

Jeśli występują problemy z poprzednimi przypadkami testowymi, zapoznaj się z kolumną **Opis** rozwiązania w tabeli. Aby uzyskać więcej informacji, skontaktuj się z zespołem pomocy technicznej. 

## <a name="data-disk-size-verification"></a>Weryfikacja rozmiaru dysku danych

Żądania dysku danych o rozmiarze większym niż 1023 gigabajtów (GB) nie zostaną zatwierdzone. Ta reguła ma zastosowanie do systemów Linux i Windows.

Prześlij żądanie ponownie o rozmiarze mniejszym lub równym 1023 GB.

## <a name="os-disk-size-validation"></a>Walidacja rozmiaru dysku systemu operacyjnego

Zapoznaj się z następującymi regułami dotyczącymi ograniczeń rozmiaru dysku systemu operacyjnego. Po przesłaniu żądania upewnij się, że rozmiar dysku systemu operacyjnego przekracza ograniczenie dla systemu Linux lub Windows.

|System operacyjny|Zalecany rozmiar wirtualnego dysku twardego|
|---|---|
|Linux|30 GB do 1023 GB|
|Windows|30 GB do 250 GB|

Ponieważ maszyny wirtualne umożliwiają dostęp do podstawowego systemu operacyjnego, upewnij się, że rozmiar dysku VHD jest wystarczająco duży dla dysku VHD. Dyski nie są rozwijane bez przestojów. Użyj rozmiaru dysku z 30 GB do 50 GB.

|Rozmiar dysku VHD|Rzeczywisty rozmiar zajęty|Rozwiązanie|
|---|---|---|
|>500 tebibajtów (TiB)|n/d|Skontaktuj się z zespołem pomocy technicznej w celu zatwierdzenia wyjątku.|
|250-500 TiB|>200 gibibajtach (GiB) — różnica między rozmiarem obiektu BLOB|Skontaktuj się z zespołem pomocy technicznej w celu zatwierdzenia wyjątku.|

> [!NOTE]
> Większe rozmiary dysków wiążą się z wyższymi kosztami i spowoduje to opóźnienie podczas procesu instalacji i replikacji. Ze względu na to opóźnienie i koszt zespół pomocy technicznej może zwrócić się do uzasadnienia zatwierdzenia wyjątku.

## <a name="wannacry-patch-verification-test-for-windows"></a>Test weryfikacji poprawek atak wannacry dla systemu Windows

Aby zapobiec potencjalnym atakom związanym z wirusem atak wannacry, upewnij się, że wszystkie żądania obrazów systemu Windows są aktualizowane przy użyciu najnowszej poprawki.

Możesz sprawdzić wersję pliku obrazu z `C:\windows\system32\drivers\srv.sys` lub `srv2.sys` .

W poniższej tabeli przedstawiono minimalną wersję poprawki systemu Windows Server: 

|System operacyjny|Wersja|
|---|---|
|System Windows obsługuje 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 z dodatkiem R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|Nie dotyczy|

> [!NOTE]
> System Windows Server 2019 nie ma wymagań dotyczących wersji obowiązkowej.

## <a name="sack-vulnerability-patch-verification"></a>Weryfikacja poprawek luk w zabezpieczeniach

Po przesłaniu obrazu systemu Linux żądanie może zostać odrzucone z powodu problemów z wersją jądra.

Zaktualizuj jądro przy użyciu zatwierdzonej wersji i ponownie prześlij żądanie. Zatwierdzoną wersję jądra można znaleźć w poniższej tabeli. Numer wersji musi być równy lub większy od liczby wymienionej w tym miejscu.

Jeśli obraz nie jest zainstalowany z jedną z następujących wersji jądra, zaktualizuj go przy użyciu poprawnych poprawek. Zażądaj niepotrzebnego zatwierdzenia od zespołu pomocy technicznej po zaktualizowaniu obrazu przy użyciu wymaganych poprawek:

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|Rodzina systemów operacyjnych|Wersja|Jądro|
|---|---|---|
|Ubuntu|14,04 LTS|4.4.0 — 151| 
||14,04 LTS|4.15.0-1049-*-Azure|
||16,04 LTS|4.15.0 — 1049|
||18,04 LTS|4.18.0 — 1023|
||18,04 LTS|5.0.0-1025|
||18,10|4.18.0 — 1023|
||19,04|5.0.0 — 1010|
||19,04|5.3.0 — 1004|
|RHEL i Cent systemu operacyjnego|6,10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7,4|3.10.0-693.50.3|
||7,5|3.10.0-862.34.2|
||7,6|3.10.0-957.21.3|
||7,7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0-147|
||"7 — NIEPRZETWORZONY" (7,6)||
||"7-LVM" (7,6)|3.10.0-957.21.3|
||RHEL — SAP 7,4|TBD|
||RHEL — SAP 7,5|TBD|
|SLES|SLES11SP4 (w tym SAP)|3.0.101-108.95.2|
||SLES12SP1 dla SAP|3.12.74-60.64.115.1|
||SLES12SP2 dla SAP|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (jądro — Azure)|
||SLES12SP3 dla SAP|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (jądro — Azure)|
||SLES12SP4 dla SAP|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (jądro — Azure)|
||SLES15 dla SAP|4.12.14-5.30.1 (jądro — Azure)|
||SLES15SP1|4.12.14-5.30.1 (jądro — Azure)|
|Oracle|6,10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RHCK 2.6.32-754.15.3 
||7.0 — 7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>RHCK następuje po RHEL powyżej|
||7,6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|CoreOS stabilny 2079.6.0|4.19.43*|
||2135.3.1 beta|4.19.50*|
||2163.2.1 Alpha|4.19.50*|
|Debian|Jessie (zabezpieczenia)|3.16.68-2|
||Jessie|4.9.168-1 + deb9u3|
||Rozciągnij (zabezpieczenia)|4.9.168-1 + deb9u3|
||Debian GNU/Linux 10 (Buster)|Debian 6.3.0-18 + deb9u1|
||Buster, SID (rozciąganie portów)|4.19.37-5|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>Rozmiar obrazu powinien być wielokrotnością megabajtów

Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do wielokrotności 1 megabajta (MB). Jeśli wirtualny dysk twardy nie jest zgodny z zalecanym rozmiarem wirtualnym, żądanie może zostać odrzucone.

Postępuj zgodnie z zaleceniami podczas konwertowania z dysku surowego na dysk VHD. Upewnij się, że rozmiar dysku surowego jest wielokrotnością 1 MB. Aby uzyskać więcej informacji, zobacz [informacje dotyczące dystrybucji niezatwierdzonych](../virtual-machines/linux/create-upload-generic.md).

## <a name="vm-access-denied"></a>Odmowa dostępu do maszyny wirtualnej

Problem z _odmową dostępu_ dla uruchomienia przypadku testowego na maszynie wirtualnej może być spowodowany przez niewystarczające uprawnienia.

Sprawdź, czy włączono prawidłowy dostęp do konta, na którym działają przypadki samotestowe. Włącz dostęp, aby uruchamiać przypadki testowe, jeśli nie jest włączona. Jeśli nie chcesz włączyć dostępu, możesz udostępnić wyniki przypadków testowych z zespołem pomocy technicznej.

Aby przesłać żądanie z wyłączonym obrazem SSH dla procesu certyfikacji:

1. Uruchom [najnowsze narzędzie do testowania certyfikacji dla maszyn wirtualnych platformy Azure](https://aka.ms/AzureCertificationTestTool) na Twoim obrazie.

2. Zgłoś [bilet pomocy technicznej](https://aka.ms/marketplacepublishersupport). Pamiętaj, aby dołączyć Raport zestawu narzędzi i podać szczegóły oferty:
   - Nazwa oferty
   - Nazwa wydawcy
   - Identyfikator planu/jednostka SKU i wersja

3. Prześlij ponownie żądanie certyfikacji.

## <a name="download-failure"></a>Błąd pobierania
    
Zapoznaj się z poniższą tabelą dotyczącą problemów występujących podczas pobierania obrazu maszyny wirtualnej przy użyciu adresu URL sygnatury dostępu współdzielonego (SAS).

|Scenariusz|Błąd|Przyczyna|Rozwiązanie|
|---|---|---|---|
|1|Nie znaleziono obiektu BLOB|Wirtualny dysk twardy może zostać usunięty lub przeniesiony z określonej lokalizacji.|| 
|2|Obiekt BLOB w użyciu|Wirtualny dysk twardy jest używany przez inny proces wewnętrzny.|Wirtualny dysk twardy powinien znajdować się w stanie używanym podczas pobierania go przy użyciu adresu URL sygnatury dostępu współdzielonego.|
|3|Nieprawidłowy adres URL SAS|Skojarzony adres URL sygnatury dostępu współdzielonego dla wirtualnego dysku twardego jest niepoprawny.|Uzyskaj prawidłowy adres URL sygnatury dostępu współdzielonego.|
|4|Nieprawidłowy podpis|Skojarzony adres URL sygnatury dostępu współdzielonego dla wirtualnego dysku twardego jest niepoprawny.|Uzyskaj prawidłowy adres URL sygnatury dostępu współdzielonego.|
|6|Nagłówek warunkowy HTTP|Adres URL sygnatury dostępu współdzielonego jest nieprawidłowy.|Uzyskaj prawidłowy adres URL sygnatury dostępu współdzielonego.|
|7|Nieprawidłowa nazwa wirtualnego dysku twardego|Sprawdź, czy `%` `"` w nazwie wirtualnego dysku twardego istnieją jakiekolwiek znaki specjalne, takie jak znak procentu czy znak cudzysłowu.|Zmień nazwę pliku VHD, usuwając znaki specjalne.|

## <a name="first-mb-2048-kb-partition-linux-only"></a>Partycja pierwszej pamięci (2048 KB) (tylko system Linux)

Podczas przesyłania wirtualnego dysku twardego upewnij się, że pierwsze 2048 KB dysku VHD jest puste. W przeciwnym razie Twoje żądanie zakończy się niepowodzeniem.

>[!NOTE]
>W przypadku niektórych obrazów specjalnych, takich jak te, które zostały utworzone na podstawie obrazów podstawowych systemu Windows Azure z portalu Azure Marketplace, sprawdzimy znacznik rozliczania i zignorujesz partycję MB, jeśli tag rozliczania jest obecny i jest zgodny z naszymi wewnętrznymi dostępnymi wartościami.

### <a name="create-a-first-mb-2048-kb-partition-on-an-empty-vhd"></a>Tworzenie pierwszej partycji (2048 KB) na pustym dysku VHD

Te kroki dotyczą tylko systemu Linux.

1. Tworzenie dowolnego rodzaju maszyn wirtualnych z systemem Linux, takich jak Ubuntu, Cent OS lub inne. Wypełnij pola wymagane i wybierz pozycję **Dalej: dyski >**.

   ![Zrzut ekranu przedstawiający stronę Tworzenie maszyny wirtualnej z wyróżnionym przyciskiem dalej: dyski polecenie.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Utwórz dysk niezarządzany dla maszyny wirtualnej.

   Użyj wartości domyślnych lub określ dowolną wartość dla pól, takich jak karta sieciowa, sieciowej grupy zabezpieczeń i publiczny adres IP.

   ![Obraz zrzutu ekranu strony "dyski danych" w przepływie tworzenia maszyny wirtualnej.](./media/create-vm/vm-certification-issues-solutions-16.png)

1. Po utworzeniu maszyny wirtualnej wybierz pozycję **dyski** w lewym okienku.

   ![Zrzut ekranu przedstawiający sposób wybierania dysków dla maszyny wirtualnej.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Dołącz wirtualny dysk twardy do maszyny wirtualnej, aby utworzyć tabelę partycji.

   1. Wybierz pozycję **Dodaj**  >  **istniejący obiekt BLOB** dla dysku Datadisk.

      ![Zrzut ekranu przedstawiający sposób dodawania dysku danych do dysku VHD.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Znajdź konto magazynu wirtualnego dysku twardego.
   1. Wybierz **kontener** , a następnie wybierz dysk VHD.
   1. Wybierz przycisk **OK**.

      ![Zrzut ekranu przedstawiający stronę dysk niezarządzany.](./media/create-vm/vm-certification-issues-solutions-19.png)

      Dysk VHD zostanie dodany jako dysk danych LUN 0.

   1. Uruchom ponownie maszynę wirtualną.

1. Po ponownym uruchomieniu maszyny wirtualnej Zaloguj się do maszyny wirtualnej przy użyciu polecenia wypełniania lub innego klienta i uruchom `sudo  -i` polecenie, aby uzyskać dostęp do katalogu głównego.

   ![Wyświetlany jest zrzut ekranu z wierszem polecenia klienta z poleceniem sudo-i.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Utwórz partycję na dysku VHD.

   1. Wprowadź `fdisk /dev/sdb` polecenie.
   1. Aby wyświetlić istniejącą listę partycji z dysku VHD, wpisz polecenie `p` .
   1. Wprowadź `d` , aby usunąć wszystkie istniejące partycje dostępne na dysku VHD. Możesz pominąć ten krok, jeśli nie jest to wymagane.

      ![Wyświetla zrzut ekranu wiersza polecenia klienta z poleceniami służącymi do usuwania istniejących partycji.](./media/create-vm/vm-certification-issues-solutions-21.png)

   1. Wprowadź `n` , aby utworzyć nową partycję, i wybierz pozycję `p` dla (partycja podstawowa).

   1. Wprowadź 2048 jako _pierwszy wartość sektora_ . _Ostatni sektor_ można pozostawić jako wartość domyślną.

      >[!IMPORTANT]
      >Wszystkie istniejące dane zostaną wymazane do 2048 KB. Tworzenie kopii zapasowej wirtualnego dysku twardego przed utworzeniem nowej partycji.

      ![Pokazuje zrzut ekranu wiersza polecenia klienta pokazujący polecenia i dane wyjściowe dla wymazanych danych.](./media/create-vm/vm-certification-issues-solutions-22.png)

   1. Wpisz `w` , aby potwierdzić tworzenie partycji. 

      ![Wyświetla zrzut ekranu wiersza polecenia klienta z poleceniami służącymi do tworzenia partycji.](./media/create-vm/vm-certification-issues-solutions-23.png)

   1. Tabelę partycji można sprawdzić, uruchamiając polecenie `n fdisk /dev/sdb` i wpisując `p` . Zobaczysz, że partycja zostanie utworzona z 2048 wartością przesunięcia. 

      ![Wyświetlona zrzut ekranu wiersza polecenia klienta z poleceniami służącymi do tworzenia przesunięcia 2048.](./media/create-vm/vm-certification-issues-solutions-24.png)

1. Odłącz wirtualny dysk twardy od maszyny wirtualnej i Usuń maszynę wirtualną.

### <a name="create-a-first-mb-2048-kb-partition-by-moving-existing-data-on-vhd"></a>Tworzenie pierwszej partycji MB (2048 KB) przez przeniesienie istniejących danych z dysku VHD

Te kroki dotyczą tylko systemu Linux.

1. Tworzenie dowolnego rodzaju maszyn wirtualnych z systemem Linux, takich jak Ubuntu, Cent OS lub inne. Wypełnij pola wymagane i wybierz pozycję **Dalej: dyski >**.

   ![Zrzut ekranu przedstawiający stronę Tworzenie maszyny wirtualnej z wyróżnionym przyciskiem dalej: dyski polecenie.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Utwórz dysk niezarządzany dla maszyny wirtualnej.

   ![Obraz zrzutu ekranu strony "dyski danych" w przepływie tworzenia maszyny wirtualnej.](./media/create-vm/vm-certification-issues-solutions-16.png)

   Użyj wartości domyślnych lub określ dowolną wartość dla pól, takich jak karta sieciowa, sieciowej grupy zabezpieczeń i publiczny adres IP.

1. Po utworzeniu maszyny wirtualnej wybierz pozycję **dyski** w lewym okienku.

   ![Zrzut ekranu przedstawiający sposób wybierania dysków dla maszyny wirtualnej.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Dołącz wirtualny dysk twardy do maszyny wirtualnej, aby utworzyć tabelę partycji.

   1. Dołącz wirtualny dysk twardy do maszyny wirtualnej, aby utworzyć tabelę partycji.

   1. Wybierz pozycję **Dodaj**  >  **istniejący obiekt BLOB** dla dysku Datadisk.

      ![Zrzut ekranu przedstawiający sposób dodawania dysku danych do dysku VHD.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Znajdź konto magazynu wirtualnego dysku twardego.
   1. Wybierz **kontener** , a następnie wybierz dysk VHD.
   1. Wybierz przycisk **OK**.

      ![Zrzut ekranu przedstawiający stronę dysk niezarządzany.](./media/create-vm/vm-certification-issues-solutions-19.png)

      Dysk VHD zostanie dodany jako dysk danych LUN 0.

   1. Uruchom ponownie maszynę wirtualną.

1. Zaloguj się do maszyny wirtualnej za pomocą polecenia Pozyskaj lub innego klienta i uruchom `sudo  -i` polecenie, aby uzyskać dostęp do katalogu głównego.

   ![Pojawia się zrzut ekranu z wierszem polecenia klienta z zalogowaniem i poleceniem sudo-i.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Uruchom polecenie `echo '+1M,' | sfdisk --move-data /dev/sdc -N 1`.

   ![Pokazywanie zrzutu ekranu wiersza polecenia klienta pokazującego wykonywanie poleceń.](./media/create-vm/vm-certification-issues-solutions-25.png)

   >[!NOTE]
   >Wykonanie tego polecenia może potrwać trochę czasu, ponieważ zależy ono od rozmiaru dysku.

1. Odłącz wirtualny dysk twardy od maszyny wirtualnej i Usuń maszynę wirtualną.


## <a name="default-credentials"></a>Poświadczenia domyślne

Nigdy nie wysyłaj domyślnych poświadczeń przy użyciu przesłanego wirtualnego dysku twardego. Dodanie poświadczeń domyślnych sprawia, że dysk VHD jest bardziej narażony na zagrożenia bezpieczeństwa. Zamiast tego należy utworzyć własne poświadczenia podczas przesyłania wirtualnego dysku twardego.
  
## <a name="datadisk-mapped-incorrectly"></a>Nieprawidłowo mapowany dysk

Problem z mapowaniem może wystąpić, gdy żądanie jest przesyłane z wieloma dyskami danych, które nie są w kolejności. Na przykład kolejność numerów dla trzech dysków danych musi wynosić *0, 1, 2*. Każda inna kolejność jest traktowana jako problem z mapowaniem.

Prześlij ponownie żądanie z prawidłową kolejnością na dyskach danych.

## <a name="incorrect-os-mapping"></a>Nieprawidłowe mapowanie systemu operacyjnego

Po utworzeniu obrazu można go zamapować lub przypisać do nieprawidłowej etykiety systemu operacyjnego. Na przykład po wybraniu **systemu Windows** jako części nazwy systemu operacyjnego podczas tworzenia obrazu dysk systemu operacyjnego powinien być instalowany tylko z systemem Windows. To samo wymaganie dotyczy systemu Linux.

## <a name="vm-not-generalized"></a>Nieuogólniona maszyna wirtualna

Jeśli wszystkie obrazy pobierane z witryny Azure Marketplace będą używane ponownie, należy przeprowadzić uogólniony dysk VHD systemu operacyjnego.

* W przypadku systemu **Linux** następujący proces służy do UOGÓLNIANIA maszyny wirtualnej z systemem Linux i wdrażania jej ponownie jako oddzielnej maszyny wirtualnej.

  W oknie SSH wprowadź następujące polecenie: `sudo waagent -deprovision+user` .

* W przypadku **systemu Windows** można uogólniać obrazy systemu Windows przy użyciu programu `sysreptool` .

  Aby uzyskać więcej informacji na temat `sysreptool` Narzędzia, zobacz temat [przygotowanie systemu (Sysprep) — Omówienie](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

## <a name="datadisk-errors"></a>Błędy dysku datadisks

W przypadku rozwiązań błędów, które są związane z dyskiem danych, należy użyć poniższej tabeli:

|Błąd|Przyczyna|Rozwiązanie|
|---|---|---|
|`DataDisk- InvalidUrl:`|Ten błąd może wystąpić z powodu nieprawidłowego numeru jednostki logicznej (LUN) podczas przesyłania oferty.|Sprawdź, czy sekwencja numerów LUN dla dysku danych znajduje się w centrum partnerskim.|
|`DataDisk- NotFound:`|Ten błąd może wystąpić, ponieważ dysk danych nie znajduje się w określonym adresie URL sygnatury dostępu współdzielonego.|Sprawdź, czy dysk danych znajduje się w określonym adresie URL sygnatury dostępu współdzielonego.|

## <a name="remote-access-issue"></a>Problem z dostępem zdalnym

Ten błąd zostanie wyświetlony, jeśli opcja Remote Desktop Protocol (RDP) nie jest włączona dla obrazu systemu Windows.

Włącz dostęp RDP dla obrazów systemu Windows przed ich przesłaniem.

## <a name="bash-history-failed"></a>Historia bash nie powiodła się

Ten błąd zostanie wyświetlony, jeśli rozmiar historii bash w przesłanym obrazie wynosi więcej niż 1 kilobajt (KB). Rozmiar jest ograniczony do 1 KB, aby ograniczyć plik zawierający potencjalnie wrażliwe informacje.

Aby usunąć historię bash:

1. Wdróż maszynę wirtualną i wybierz opcję **Uruchom polecenie** na Azure Portal.

   ![Zrzut ekranu przedstawiający Azure Portal z opcją "Run command" w lewym okienku.](./media/create-vm/vm-certification-issues-solutions-3.png)

1. Wybierz pierwszą opcję **RunShellScript** , a następnie uruchom polecenie: `cat /dev/null > ~/.bash_history && history -c` .

   ![Zrzut ekranu strony "Uruchom skrypt poleceń" na Azure Portal.](./media/create-vm/vm-certification-issues-solutions-4.png)

1. Po pomyślnym uruchomieniu polecenia Uruchom ponownie maszynę wirtualną.

1. Uogólnij maszynę wirtualną, przejmowanie obrazu wirtualnego dysku twardego i zatrzymanie maszyny wirtualnej.

1. Prześlij ponownie uogólniony obraz.

## <a name="request-an-exception-on-vm-images-for-select-tests"></a>Żądaj wyjątku w obrazach maszyny wirtualnej dla wybranych testów

Wydawcy mogą żądać wyjątków dla kilku testów wykonywanych podczas certyfikacji maszyny wirtualnej. Wyjątki są udostępniane w rzadkich przypadkach, gdy Wydawca dostarcza dowód do obsługi żądania. Zespół certyfikacji zastrzega sobie prawo do odmowy lub zatwierdzenia wyjątków w dowolnym momencie.

W tej sekcji opisano ogólne scenariusze, w których wydawcy żądają wyjątku i sposobu ich żądania.

### <a name="scenarios-for-exception"></a>Scenariusze dla wyjątku

Wydawcy zwykle żądają wyjątków w następujących przypadkach:

- **Wyjątek dla co najmniej jednego przypadku testowego**. Skontaktuj się z [pomocą techniczną Centrum partnerskiego](https://aka.ms/marketplacepublishersupport) , aby zażądać wyjątków dla przypadków testowych.

- **Maszyny wirtualne zablokowane/brak dostępu głównego**. Kilku wydawców ma scenariusze, w których maszyny wirtualne muszą być zablokowane, ponieważ mają takie oprogramowanie jak zapory zainstalowane na maszynie wirtualnej. W takim przypadku Pobierz narzędzie do [testowania certyfikowane](https://aka.ms/AzureCertificationTestTool) i prześlij raport w [centrum partnerskim](https://aka.ms/marketplacepublishersupport).

- **Szablony niestandardowe**. Niektórzy wydawcy publikują obrazy maszyn wirtualnych, które wymagają niestandardowego szablonu Azure Resource Manager (ARM) do wdrażania maszyn wirtualnych. W takim przypadku należy przesłać szablony niestandardowe w [pomocy technicznej Centrum partnerskiego](https://aka.ms/marketplacepublishersupport) , aby umożliwić weryfikację w zespole certyfikacji.

### <a name="information-to-provide-for-exception-scenarios"></a>Informacje, które należy podać dla scenariuszy wyjątków

Skontaktuj się z [pomocą techniczną Centrum partnerskiego](https://aka.ms/marketplacepublishersupport) , aby zażądać wyjątku dla jednego z scenariuszy, i podaj następujące informacje:

- **Identyfikator wydawcy**. Wpisz identyfikator wydawcy portalu centralnego partnera.
- **Identyfikator oferty/nazwa**. Wprowadź identyfikator oferty lub nazwę.
- **Identyfikator jednostki SKU/planu**. Wpisz identyfikator planu oferty maszyny wirtualnej lub jednostkę SKU.
- **Wersja** Wprowadź wersję oferty maszyny wirtualnej, która wymaga wyjątku.
- **Typ wyjątku**. Wybieraj spośród testów, zablokowanych maszyn wirtualnych lub szablonów niestandardowych.
- **Przyczyna żądania**. Uwzględnij przyczynę żądania wyjątku oraz informacje o wykluczeniach testowych.
- **Oś czasu**. Wprowadź datę końcową dla wyjątku.
- **Załącznik**. Załączone ważne dokumenty dowodowe:

  - W przypadku maszyn wirtualnych z blokadą Dołącz raport testowy.
  - W przypadku szablonów niestandardowych Podaj niestandardowy szablon ARM jako załącznik.

  Jeśli nie dołączysz tych załączników, żądanie zostanie odrzucone.

## <a name="address-a-vulnerability-or-an-exploit-in-a-vm-offer"></a>Wykorzystaj lukę w zabezpieczeniach lub w ramach oferty dotyczącej maszyny wirtualnej

W tej sekcji opisano sposób dostarczania nowego obrazu maszyny wirtualnej w przypadku wykrycia luki w zabezpieczeniach lub wykorzystania jej przez jeden z obrazów maszyn wirtualnych. Dotyczy tylko ofert maszyn wirtualnych platformy Azure opublikowanych w portalu Azure Marketplace.

> [!NOTE]
> Nie można usunąć ostatniego obrazu maszyny wirtualnej z planu ani zakończyć sprzedaży ostatniego planu oferty.

Wykonaj jedną z następujących czynności:

- Jeśli masz nowy obraz maszyny wirtualnej w celu zastąpienia zagrożonego obrazu maszyny wirtualnej, zobacz [udostępnianie stałego obrazu maszyny wirtualnej](#provide-a-fixed-vm-image).
- Jeśli nie masz nowego obrazu maszyny wirtualnej w celu zamienienia samego obrazu maszyny wirtualnej w ramach planu lub jeśli planujesz, [Zatrzymaj sprzedawanie planu](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).
- Jeśli nie planujesz zastąpienia jedynego obrazu maszyny wirtualnej w ofercie, zalecamy [zaprzestanie sprzedaży oferty](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="provide-a-fixed-vm-image"></a>Udostępnianie stałego obrazu maszyny wirtualnej

Aby zapewnić stały obraz maszyny wirtualnej w celu zastąpienia obrazu maszyny wirtualnej, który ma lukę w zabezpieczeniach lub wykorzystanie:

1. Podaj nowy obraz maszyny wirtualnej w celu rozwiązania luk w zabezpieczeniach lub wykorzystania.
1. Usuń obraz maszyny wirtualnej z luką w zabezpieczeniach lub za pomocą programu.
1. Opublikuj ponownie ofertę.

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>Podaj nowy obraz maszyny wirtualnej w celu rozwiązania luk w zabezpieczeniach lub wykorzystania

Aby wykonać te kroki, przygotuj zasoby techniczne dla obrazu maszyny wirtualnej, który chcesz dodać. Aby uzyskać więcej informacji, zobacz temat [Tworzenie maszyny wirtualnej przy użyciu zatwierdzonej bazy](azure-vm-create-using-approved-base.md)lub [Tworzenie maszyny wirtualnej przy użyciu własnego obrazu](azure-vm-create-using-own-image.md) oraz [generowanie identyfikatora URI sygnatury dostępu współdzielonego dla obrazu maszyny wirtualnej](azure-vm-get-sas-uri.md).

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
1. W okienku po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.
1. W kolumnie **alias oferty** wybierz ofertę.
1. Na karcie **Przegląd planu** w kolumnie **Nazwa** wybierz odpowiedni plan.
1. Na karcie **konfiguracja techniczna** w obszarze **obrazy maszyn wirtualnych** wybierz pozycję **+ Dodaj obraz maszyny wirtualnej**.

   > [!NOTE]
   > Do jednego planu można dodać tylko jeden obraz maszyny wirtualnej. Aby dodać wiele obrazów maszyn wirtualnych, Opublikuj pierwszy raz przed dodaniem kolejnego obrazu maszyny wirtualnej.

1. W wyświetlonych polach Podaj nową wersję dysku i obraz maszyny wirtualnej.
1. Wybierz pozycję **Zapisz wersję roboczą**.

Następnie Usuń obraz maszyny wirtualnej z luką w zabezpieczeniach.

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>Usuwanie obrazu maszyny wirtualnej z wykorzystaniem luk w zabezpieczeniach lub wykorzystania

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W okienku po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.
3. W kolumnie **alias oferty** wybierz ofertę.
4. Na karcie **Przegląd planu** w kolumnie **Nazwa** wybierz odpowiedni plan.
5. Na karcie **konfiguracja techniczna** w obszarze **obrazy maszyn wirtualnych** obok obrazu maszyny wirtualnej, który chcesz usunąć, wybierz pozycję **Usuń obraz maszyny wirtualnej**.
6. W oknie dialogowym wybierz pozycję **Kontynuuj**.
7. Wybierz pozycję **Zapisz wersję roboczą**.

Następnie ponownie Opublikuj ofertę.

#### <a name="republish-the-offer"></a>Ponowne opublikowanie oferty

1. Wybierz pozycję **Przejrzyj i Opublikuj**.
2. Jeśli musisz podać informacje dotyczące zespołu certyfikacji, Dodaj go do pola **uwagi dotyczące certyfikacji** .
3. Kliknij pozycję **Opublikuj**.

Aby zakończyć proces publikowania, zobacz [Przegląd i publikowanie ofert](review-publish-offer.md).

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie właściwości oferty maszyny wirtualnej](azure-vm-create-properties.md)
- [Aktywne nagrody dotyczące portalu Marketplace](partner-center-portal/marketplace-rewards.md)
- Jeśli masz pytania lub opinie na temat poprawy, skontaktuj się z [pomocą techniczną Centrum partnerskiego](https://aka.ms/marketplacepublishersupport).
