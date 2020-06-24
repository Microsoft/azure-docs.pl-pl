---
title: Certyfikacja maszyn wirtualnych — problemy i rozwiązania
description: W tym artykule opisano typowe komunikaty o błędach dla obrazów maszyn wirtualnych. Omówiono w nim również powiązane rozwiązania
author: v-miegge
ms.author: v-krmall
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
ms.date: 06/16/2020
ms.openlocfilehash: dcf687a369b32b2055f579f2599a0c3097f9f9f3
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977764"
---
# <a name="virtual-machine-certification---issues-and-solutions"></a>Certyfikacja maszyn wirtualnych — problemy i rozwiązania

Podczas publikowania obrazów maszyn wirtualnych w portalu Azure Marketplace zespół platformy Azure sprawdza poprawność obrazu maszyny wirtualnej, aby zapewnić jego rozruch, bezpieczeństwo i zgodność z platformą Azure. Jeśli którykolwiek z testów wysokiej jakości zakończy się niepowodzeniem, opublikowanie zakończy się niepowodzeniem z komunikatem zawierającym błąd.

W tym artykule opisano typowe komunikaty o błędach dla obrazów maszyn wirtualnych. Omówiono w nim również powiązane rozwiązania:

> [!NOTE]
> Jeśli masz pytania lub opinie na temat poprawy, skontaktuj się z [pomocą techniczną Centrum partnerskiego](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="approved-base-image"></a>Zatwierdzony obraz podstawowy

Gdy wyślesz żądanie ponownego opublikowania obrazu przy użyciu aktualizacji, przypadek testowy weryfikacji liczby części może zakończyć się niepowodzeniem. W takim przypadku obraz nie zostanie zatwierdzony.

Ten błąd wystąpi, gdy użyto obrazu podstawowego, który należy do innego wydawcy i Zaktualizowano obraz. W tej sytuacji nie będzie można opublikować Twojego obrazu.

Aby rozwiązać ten problem, Pobierz najnowszy obraz z witryny Azure Marketplace i wprowadź zmiany w tym obrazie. Zapoznaj się z następującymi tematami, aby wyświetlić zatwierdzone obrazy podstawowe, w których można wyszukać obraz:

- [Linux — obrazy](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros?toc=/azure/virtual-machines/linux/toc.json)
- [Windows — obrazy](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd#select-an-approved-base)

## <a name="vm-extension-failure"></a>Niepowodzenie rozszerzenia maszyny wirtualnej

Aby sprawdzić, czy obraz obsługuje rozszerzenie maszyny wirtualnej, wykonaj następujące kroki:

Włącz rozszerzenia maszyn wirtualnych:

1. Wybierz maszynę wirtualną z systemem Linux.
2. Przejdź do pozycji **Ustawienia diagnostyki**.
3. Aby włączyć macierze podstawowe, zaktualizuj **konto magazynu**.
4. Wybierz pozycję **Zapisz**.

   ![Włącz monitorowanie na poziomie gościa](./media/vm-certification-issues-solutions-1.png)

Sprawdź, czy rozszerzenia maszyn wirtualnych są prawidłowo aktywowane:

5. Przejdź do karty **rozszerzenia maszyny wirtualnej** na maszynie wirtualnej i sprawdź **rozszerzenie diagnostyki systemu Linux**.
6. W przypadku **pomyślnego zainicjowania obsługi administracyjnej** należy pomyślnie przekazywać przypadki testowe.
7. W przypadku **niepowodzenia inicjowania obsługi administracyjnej** nie można wykonać przypadku testowego rozszerzeń i należy ustawić flagę z ograniczeniami.

   ![Inicjowanie obsługi powiodło się](./media/vm-certification-issues-solutions-2.png)

   Jeśli rozszerzenie maszyny wirtualnej nie powiedzie się, przejdź do pozycji [Użyj rozszerzenia diagnostycznego systemu Linux do monitorowania metryk i dzienników](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux) , aby je włączyć. Jeśli nie chcesz, aby rozszerzenie maszyny wirtualnej zostało włączone, skontaktuj się z zespołem pomocy technicznej i poproś o wyłączenie rozszerzenia.

## <a name="virtual-machine-provisioning-issue"></a>Problem z obsługą maszyny wirtualnej

Przed przesłaniem oferty Sprawdź, czy proces aprowizacji jest ściśle przestrzegany dla maszyny wirtualnej. Aby wyświetlić format JSON obsługi administracyjnej maszyny wirtualnej, przejdź do [certyfikatu obrazu maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template).

Problemy z aprowizacjim mogą obejmować następujące scenariusze awarii:

|S.NO|error|reason|Narzędzie|
|---|---|---|---|
|1|Nieprawidłowy wirtualny dysk twardy (VHD)|Jeśli określona wartość pliku cookie w stopce wirtualnego dysku twardego nie jest poprawna, wirtualny dysk twardy będzie uznawany za nieprawidłowy.|Utwórz ponownie obraz i prześlij żądanie.|
|2|Nieprawidłowy typ obiektu BLOB|Inicjowanie obsługi maszyny wirtualnej nie powiodło się, ponieważ użyty blok jest typem obiektu BLOB, a nie typem strony.|Utwórz ponownie obraz i prześlij żądanie.|
|3|Limit czasu aprowizacji lub nieprawidłowo uogólniony|Wystąpił problem z generalizacją maszyn wirtualnych.|Utwórz ponownie obraz z generalizacją i prześlij żądanie.|

> [!NOTE]
> Skorzystaj z poniższych linków, aby uzyskać dokumentację dotyczącą generalizacji maszyn wirtualnych:
> - [Linux](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-configure-vm#generalize-the-image)
> - [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)

## <a name="software-compliance-for-windows"></a>Zgodność oprogramowania dla systemu Windows

Jeśli żądanie obrazu systemu Windows zostało odrzucone z powodu zgodności oprogramowania, być może utworzono obraz systemu Windows z zainstalowanym programem SQL Server, zamiast korzystać z odpowiedniego obrazu podstawowego wersji SQL z witryny Azure Marketplace.

Nie należy tworzyć własnego obrazu systemu Windows z zainstalowanym programem SQL Server. Zamiast tego należy użyć zatwierdzonych obrazów podstawowych SQL (Enterprise/Standard/Web) z witryny Azure Marketplace.

Jeśli próbujesz zainstalować program Visual Studio lub dowolny licencjonowany produkt pakietu Office, skontaktuj się z zespołem pomocy technicznej w celu uzyskania wcześniejszej zgody.

Aby uzyskać więcej informacji, odwiedź stronę [Tworzenie zasobów technicznych maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd#select-an-approved-base) w celu wybrania zatwierdzonej podstawy.

## <a name="tool-kit-test-case-execution-failed"></a>Wykonanie przypadku testowego zestawu narzędzi nie powiodło się

Zestaw narzędzi certyfikacji firmy Microsoft pomoże Ci wykonać przypadki testowe, aby upewnić się, że dysk VHD/obraz jest zgodny ze środowiskiem platformy Azure.

Pobierz [zestaw narzędzi certyfikacji firmy Microsoft](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

## <a name="linux-test-cases"></a>Przypadki testowe systemu Linux

Poniżej przedstawiono przypadki testowe systemu Linux, które będą wykonywane przez zestaw narzędzi. Sprawdzanie poprawności testu jest określone w opisie.

|L.p.|przypadki testowe|description|
|---|---|---|
|1|Historia bash|Pliki historii bash powinny zostać wyczyszczone przed utworzeniem obrazu maszyny wirtualnej.|
|2|Wersja agenta systemu Linux|Należy zainstalować agenta 2.2.41 i nowszych agentów platformy Azure.|
|3|Wymagane parametry jądra|Weryfikuje następujące parametry jądra są ustawione: <br>Konsola = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300|
|4|Wymiana partycji na dysku systemu operacyjnego|Sprawdza, czy partycje wymiany nie są tworzone na dysku systemu operacyjnego.|
|5|Partycja główna na dysku systemu operacyjnego|Utwórz jedną partycję główną dla dysku systemu operacyjnego.|
|6|Wersja OpenSSL|Wersja OpenSSL powinna być większa lub równa 0.9.8.|
|7|Wersja języka Python|Zaleca się używanie języka Python w wersji 2.6 i.|
|8|Interwał aktywności klienta|Ustaw wartość ClientAliveInterval na 180. Na potrzeby aplikacji można ustawić wartość z zakresu od 30 do 235. W przypadku włączenia protokołu SSH dla użytkowników końcowych należy ustawić tę wartość jako wyjaśnioną.|
|9|Architektura systemu operacyjnego|Obsługiwane są tylko 64-bitowe systemy operacyjne.|
|10|Aktualizuj aktualizacje|Określa, czy jest włączona funkcja autoaktualizacji agenta systemu Linux.|

### <a name="common-errors-found-while-executing-the-previous-test-cases"></a>Napotkano typowe błędy podczas wykonywania poprzednich przypadków testowych

Napotkano typowe błędy podczas wykonywania poprzednich przypadków testowych.
 
|S.NO|przypadek testowy|error|Narzędzie|
|---|---|---|---|
|1|Przypadek testowy wersji agenta systemu Linux|Minimalna wersja agenta systemu Linux to 2,241 lub nowsza. To wymaganie jest obowiązkowe od 1 maja 2020|Aby [przesłać żądanie](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support), należy zaktualizować obraz przy użyciu wymaganej wersji.|
|2|Przypadek testowy historii bash|Zobaczysz błąd, jeśli rozmiar historii bash w przesłanym obrazie jest większy niż 1 KB. Rozmiar jest ograniczony do 1 KB, aby upewnić się, że wszystkie potencjalnie poufne informacje nie są przechwytywane w pliku historii bash.|Aby rozwiązać ten problem, należy zainstalować wirtualny dysk twardy w przypadku dowolnej innej działającej maszyny wirtualnej i wprowadzić wszelkie zmiany (na przykład usunąć `.bash` pliki historii), aby zmniejszyć rozmiar do wartości mniejszej niż 1 KB.|
|3|Wymagany przypadek testowy parametru jądra|Ten błąd zostanie wyświetlony, gdy wartość dla **konsoli** nie zostanie ustawiona na **ttyS0**. Sprawdź, wykonując polecenie:<br>`cat /proc/cmdline`|Ustaw wartość **konsoli** na **ttyS0** i ponownie prześlij żądanie.|
|4|Przypadek testowy interwału ClientAlive|Jeśli wynik zestawu narzędzi daje wynik niepowodzenia dla tego przypadku testowego, istnieje niewłaściwa wartość dla **ClientAliveInterval**.|Dla opcji **ClientAliveInterval** ustaw wartość mniejszą lub równą 235, a następnie prześlij żądanie ponownie.|

### <a name="windows-test-cases"></a>Przypadki testowe systemu Windows

Poniżej przedstawiono przypadki testowe systemu Windows, które będą wykonywane przez zestaw narzędzi. Sprawdzanie poprawności testu jest określone w opisie.

|L.p.|przypadki testowe|description|
|---|---|---|---|
|1|Architektura systemu operacyjnego|Platforma Azure obsługuje tylko 64 bitowe systemy operacyjne.|
|2|Zależność konta użytkownika|Wykonanie aplikacji nie powinno być zależne od konta administratora.|
|3|Klaster trybu failover|Funkcja klaster trybu failover systemu Windows Server nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|4|If|Protokół IPv6 nie jest jeszcze obsługiwany w środowisku platformy Azure. Aplikacja nie powinna być zależna od tej funkcji.|
|5|DHCP|Rola serwera Dynamic Host Configuration Protocol nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|6|Funkcja Hyper-V|Rola serwera funkcji Hyper-V nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|7|Dostęp zdalny|Rola serwera dostępu zdalnego (bezpośredni dostęp) nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|8|Usługi Rights Management|Rights Management Services. Rola serwera nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|9|Usługi wdrażania systemu Windows|Usługi wdrażania systemu Windows. Rola serwera nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|10|Szyfrowanie dysków funkcją BitLocker|Szyfrowanie dysków funkcją BitLocker nie jest obsługiwane na dysku twardym systemu operacyjnego, ale mogą być używane na dyskach danych.|
|11|Serwer nazw magazynu internetowego|Funkcja serwera nazw internetowych nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|12|Wielościeżkowe We/Wy|Wielościeżkowe we/wy. Ta funkcja serwera nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|13|Równoważenie obciążenia sieciowego|Równoważenie obciążenia sieciowego. Ta funkcja serwera nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|14|Protokół PNRP|Protokół rozpoznawania nazw równorzędnych. Ta funkcja serwera nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|15|Usługi SNMP|Funkcja usług SNMP nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|16|Usługa nazw internetowych systemu Windows|Usługa nazw internetowych systemu Windows. Ta funkcja serwera nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|
|17|Usługa bezprzewodowej sieci LAN|Usługa bezprzewodowej sieci LAN. Ta funkcja serwera nie jest jeszcze obsługiwana. Aplikacja nie powinna być zależna od tej funkcji.|

W przypadku wystąpienia błędów z powyższymi przypadkami testowymi zapoznaj się z kolumną **Opis** w poprzedniej tabeli dla rozwiązania. Jeśli potrzebujesz więcej informacji, skontaktuj się z zespołem pomocy technicznej. 

## <a name="data-disk-size-verification"></a>Weryfikacja rozmiaru dysku danych

Jeśli rozmiar każdego żądania przesłanego z dyskiem danych przekracza 1023 GB, żądanie nie zostanie zatwierdzone. Ta reguła ma zastosowanie do obu okien systemu Linux &.

Prześlij żądanie ponownie o rozmiarze mniejszym lub równym 1023 GB.

## <a name="os-disk-size-validation"></a>Walidacja rozmiaru dysku systemu operacyjnego

Zapoznaj się z następującymi regułami dotyczącymi ograniczeń rozmiaru dysku systemu operacyjnego. Po przesłaniu żądania upewnij się, że rozmiar dysku systemu operacyjnego przekracza ograniczenie dla systemu Linux lub Windows.

|System operacyjny|Zalecany rozmiar wirtualnego dysku twardego|
|---|---|
|Linux|30 GB do 1023 GB|
|Windows|30 GB do 250 GB|

Ponieważ maszyny wirtualne umożliwiają dostęp do podstawowego systemu operacyjnego, upewnij się, że rozmiar dysku VHD jest wystarczająco duży dla dysku VHD. Ponieważ dyski nie są rozwijane bez przestojów, Użyj rozmiaru dysku z zakresu od 30 do 50 GB.

|Rozmiar dysku VHD|rzeczywisty rozmiar zajęty|Narzędzie|
|---|---|---|
|>500 TiB|nie dotyczy|Skontaktuj się z zespołem pomocy technicznej w celu zatwierdzenia wyjątku.|
|250-500 TiB|>200 GiB różne od rozmiaru obiektu BLOB|Skontaktuj się z zespołem pomocy technicznej w celu zatwierdzenia wyjątku.|

> [!NOTE]
> Większe rozmiary dysków wiążą się z wyższymi kosztami i nastąpi opóźnienie podczas wykonywania czynności związanych z inicjowaniem i replikacją. Ze względu na to opóźnienie i koszt zespół pomocy technicznej może zwrócić się do uzasadnienia zatwierdzenia wyjątku.

## <a name="wannacry-patch-verification-test-for-windows"></a>Test weryfikacji poprawek atak wannacry dla systemu Windows

Aby zapobiec potencjalnym atakom związanym z wirusem atak wannacry, upewnij się, że wszystkie żądania obrazów systemu Windows są aktualizowane przy użyciu najnowszej poprawki.

Aby sprawdzić wersję poprawki systemu Windows Server, zapoznaj się z poniższą tabelą dotyczącą szczegółów systemu operacyjnego i wersji minimalnej, która będzie obsługiwana. 

Wersja pliku obrazu może być zweryfikowana z `C:\windows\system32\drivers\srv.sys` lub `srv2.sys` .

> [!NOTE]
> WindowsServer2019 nie ma wymagań dotyczących wersji obowiązkowej.

|System operacyjny|version|
|---|---|
|WindowsServer2008R2|6.1.7601.23689|
|WindowsServer2012|6.2.9200.22099|
|WindowsServer2012R2|6.3.9600.18604|
|WindowsServer2016|10.0.14393.953|
|WindowsServer2019|Nie dotyczy|

## <a name="sack-vulnerability-patch-verification"></a>Weryfikacja poprawek luk w zabezpieczeniach

Po przesłaniu obrazu systemu Linux żądanie może zostać odrzucone z powodu problemów z wersją jądra.

Zaktualizuj jądro przy użyciu zatwierdzonej wersji i ponownie prześlij żądanie. Zatwierdzoną wersję jądra można znaleźć w poniższej tabeli. Numer wersji musi być równy lub większy niż wymieniony poniżej.

Jeśli obraz nie został zainstalowany z jedną z następujących wersji jądra, zaktualizuj obraz przy użyciu odpowiednich poprawek. Więcej informacji można znaleźć na stronie z poniższych linków. Zażądaj niepotrzebnego zatwierdzenia od zespołu pomocy technicznej po zaktualizowaniu obrazu przy użyciu wymaganych poprawek:

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|Rodzina systemów operacyjnych|version|Palm|
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
||7.4|3.10.0-693.50.3|
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

Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do wielokrotności 1 MB. Jeśli wirtualny dysk twardy nie jest zgodny z zalecanym rozmiarem wirtualnym, żądanie może zostać odrzucone.

Postępuj zgodnie z zaleceniami podczas konwertowania z dysku surowego na dysk VHD i upewnij się, że rozmiar dysku pierwotnego jest wielokrotnością 1 MB. Aby uzyskać więcej informacji, zobacz [informacje dotyczące dystrybucji niepotwierdzonych](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)

## <a name="vm-access-denied"></a>Odmowa dostępu do maszyny wirtualnej

Jeśli występują problemy z odmową dostępu podczas wykonywania przypadków testowych na maszynie wirtualnej, może to być spowodowane niewystarczającymi uprawnieniami do wykonywania przypadków testowych.

Sprawdź, czy w ramach konta, na którym są wykonywane przypadki testowe, jest włączony odpowiedni dostęp. Jeśli nie, Włącz dostęp do wykonywania przypadków testowych. Jeśli nie chcesz włączyć dostępu, możesz udostępnić wyniki przypadków testowych z zespołem pomocy technicznej.

## <a name="download-failure"></a>Błąd pobierania
    
W poniższej tabeli zawarto informacje dotyczące problemów występujących podczas pobierania obrazu maszyny wirtualnej przy użyciu adresu URL sygnatury dostępu współdzielonego.

|S.NO|error|reason|Narzędzie|
|---|---|---|---|
|1|Nie znaleziono obiektu BLOB|Wirtualny dysk twardy może zostać usunięty lub przeniesiony z określonej lokalizacji|| 
|2|Obiekt BLOB w użyciu|Wirtualny dysk twardy jest używany przez inny proces wewnętrzny|Wirtualny dysk twardy powinien znajdować się w stanie używanym podczas pobierania adresu URL sygnatury dostępu współdzielonego.|
|3|Nieprawidłowy adres URL SAS|Skojarzony adres URL sygnatury dostępu współdzielonego dla tego wirtualnego dysku twardego jest niepoprawny.|Uzyskaj prawidłowy adres URL sygnatury dostępu współdzielonego.|
|4|Nieprawidłowy podpis|Skojarzony adres URL sygnatury dostępu współdzielonego dla tego wirtualnego dysku twardego jest niepoprawny.|Uzyskaj prawidłowy adres URL sygnatury dostępu współdzielonego.|
|6|Nagłówek warunkowy HTTP|Nieprawidłowy adres URL sygnatury dostępu współdzielonego.|Uzyskaj prawidłowy adres URL sygnatury dostępu współdzielonego.|
|7|Nieprawidłowa nazwa wirtualnego dysku twardego|Sprawdź, czy wszystkie znaki specjalne, takie jak **%** lub **""** istnieją w nazwie wirtualnego dysku twardego|Zmień nazwę pliku VHD, usuwając znaki specjalne|

## <a name="first-1-mb-partition"></a>Pierwsza partycja 1 MB

Podczas przesyłania wirtualnego dysku twardego upewnij się, że pierwsza partycja dysku VHD 1 MB jest pusta. W przeciwnym razie Twoje żądanie zakończy się niepowodzeniem.

## <a name="default-credentials"></a>Poświadczenia domyślne

Zawsze upewnij się, że poświadczenia domyślne nie są wysyłane przy użyciu przesłanego wirtualnego dysku twardego. Dodanie poświadczeń domyślnych sprawia, że dysk VHD jest bardziej narażony na zagrożenia bezpieczeństwa. Zamiast tego należy utworzyć własne poświadczenia podczas przesyłania wirtualnego dysku twardego.
  
## <a name="datadisk-mapped-incorrectly"></a>Nieprawidłowo mapowany dysk

Gdy żądanie jest przesyłane z wieloma dyskami danych, ale ich kolejność nie jest w kolejności, jest to traktowane jako problem z mapowaniem. Na przykład jeśli istnieją trzy dyski danych, kolejność numerów musi wynosić **0, 1, 2**. Każda inna kolejność będzie traktowana jako problem z mapowaniem.

Prześlij ponownie żądanie z prawidłową kolejnością na dyskach danych.

## <a name="incorrect-os-mapping"></a>Nieprawidłowe mapowanie systemu operacyjnego

Po utworzeniu obrazu można go zamapować lub przypisać do nieprawidłowej etykiety systemu operacyjnego. Na przykład po wybraniu **systemu Windows** jako części nazwy systemu operacyjnego podczas tworzenia obrazu dysk systemu operacyjnego powinien być instalowany tylko z systemem Windows. To samo dotyczy systemu Linux.

## <a name="vm-not-generalized"></a>Nieuogólniona maszyna wirtualna

Jeśli wszystkie obrazy pobrane z portalu Azure Marketplace będą używane ponownie, należy przeprowadzić uogólniony dysk VHD systemu operacyjnego.

W systemie Linux:

Poniższy proces uogólni maszynę wirtualną z systemem Linux i ponownie wdraża ją jako oddzielną maszynę wirtualną.

W oknie SSH wprowadź następujące polecenie:`sudo waagent -deprovision+user`

W systemie Windows:

Obrazy systemu Windows są uogólnione za pomocą programu `sysreptool` .

Więcej informacji na temat tego narzędzia można znaleźć w temacie [Sysprep (Przygotowanie systemu) — Omówienie]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)

## <a name="datadisk-error"></a>Błąd dysku Datadisk

Zapoznaj się z poniższą tabelą dotyczącą problemów z błędami związanymi z dyskiem danych.

|Błąd|reason|Narzędzie|
|---|---|---|
|`DataDisk- InvalidUrl:`|Przyczyną tego błędu może być nieprawidłowa liczba określona dla jednostki LUN podczas przesyłania oferty.|Sprawdź, czy sekwencja numerów LUN dla dysku danych znajduje się w centrum partnerskim.|
|`DataDisk- NotFound:`|Ten błąd może być spowodowany brakiem dysku z danymi w określonym adresie URL sygnatury dostępu współdzielonego|Sprawdź, czy dysk danych znajduje się w adresie URL sygnatury dostępu współdzielonego określonego w żądaniu.|

## <a name="remote-access-issue"></a>Problem z dostępem zdalnym

Jeśli opcja RDP nie jest włączona dla obrazu systemu Windows, zostanie wyświetlony ten błąd. 

Włącz dostęp RDP dla obrazów systemu Windows przed przesłaniem.

## <a name="next-steps"></a>Następne kroki

Jeśli masz pytania lub opinie na temat poprawy, skontaktuj się z [pomocą techniczną Centrum partnerskiego](https://partner.microsoft.com/support/v2/?stage=1).
