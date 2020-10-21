---
title: Rozwiązywanie problemów z certyfikacją maszyn wirtualnych do portalu Azure Marketplace
description: W tym artykule opisano zagadnienia dotyczące rozwiązywania problemów typowych dla testowania i certyfikowania obrazów maszyn wirtualnych w portalu Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: ee7a7101740e88d55d77f769263ab03a86b878d4
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284919"
---
# <a name="vm-certification-troubleshooting"></a>Rozwiązywanie problemów z certyfikacją maszyn wirtualnych

W przypadku publikowania obrazu maszyny wirtualnej w portalu Azure Marketplace zespół platformy Azure sprawdza jego poprawność, aby zapewnić jego rozruch, bezpieczeństwo i zgodność z platformą Azure. Jeśli którykolwiek z testów wysokiej jakości zakończy się niepowodzeniem, publikowanie zakończy się niepowodzeniem i zostanie wyświetlony komunikat o błędzie opisujący problem.

W tym artykule opisano typowe komunikaty o błędach podczas publikowania obrazów maszyn wirtualnych oraz powiązane rozwiązania.

> [!NOTE]
> Jeśli masz pytania lub opinie na temat poprawy, skontaktuj się z [pomocą techniczną](https://aka.ms/marketplacepublishersupport)Centrum partnerskiego.

## <a name="approved-base-image"></a>Zatwierdzony obraz podstawowy

Gdy wyślesz żądanie ponownego opublikowania obrazu przy użyciu aktualizacji, przypadek testowy weryfikacji liczby częściowej może zakończyć się niepowodzeniem. Jeśli to się nie powiedzie, obraz nie zostanie zatwierdzony.

Ten błąd występuje, gdy używany jest podstawowy obraz, który należy do innego wydawcy i został zaktualizowany. W tej sytuacji nie będzie można opublikować Twojego obrazu.

Aby rozwiązać ten problem, pobierz obraz z witryny Azure Marketplace i wprowadź w nim zmiany. Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Obrazy systemu Linux](../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Obrazy systemu Windows](azure-vm-create-using-approved-base.md)

> [!Note]
> Jeśli używasz podstawowego obrazu systemu Linux, który nie został pobrany z portalu Azure Marketplace, możesz przesunąć pierwszą partycję o 2048 KB. Pozwala to na niesformatowane miejsce na dodanie nowych informacji dotyczących rozliczeń i umożliwi korzystanie z platformy Azure z publikowaniem maszyny wirtualnej w portalu Azure Marketplace.  

## <a name="vm-extension-failure"></a>Niepowodzenie rozszerzenia maszyny wirtualnej

Sprawdź, czy obraz obsługuje rozszerzenia maszyn wirtualnych.

Aby włączyć rozszerzenia maszyny wirtualnej, wykonaj następujące czynności:

1. Wybierz maszynę wirtualną z systemem Linux.
1. Przejdź do pozycji **Ustawienia diagnostyki**.
1. Aby włączyć macierze podstawowe, zaktualizuj **konto magazynu**.
1. Wybierz pozycję **Zapisz**.

   ![Włącz monitorowanie na poziomie gościa](./media/create-vm/vm-certification-issues-solutions-1.png)

Aby sprawdzić, czy rozszerzenia maszyn wirtualnych zostały prawidłowo aktywowane, wykonaj następujące czynności:

1. Na maszynie wirtualnej wybierz kartę **rozszerzenia maszyn wirtualnych** , a następnie sprawdź stan **rozszerzenia diagnostyki systemu Linux**.
1. 
    * W przypadku *pomyślnego stanu aprowizacji*, przypadek testowy rozszerzeń został zakończony pomyślnie.  
    * W przypadku *niepowodzenia inicjowania obsługi administracyjnej*przypadek testowy rozszerzeń zakończył się niepowodzeniem i należy ustawić flagę z ograniczeniami.

      ![Zrzut ekranu przedstawiający pomyślne zainicjowanie obsługi](./media/create-vm/vm-certification-issues-solutions-2.png)

      Jeśli rozszerzenie maszyny wirtualnej nie powiedzie się, zobacz [Używanie rozszerzenia diagnostycznego systemu Linux do monitorowania metryk i dzienników](../virtual-machines/extensions/diagnostics-linux.md) , aby je włączyć. Jeśli nie chcesz, aby rozszerzenie maszyny wirtualnej zostało włączone, skontaktuj się z zespołem pomocy technicznej i poproś o jego wyłączenie.

## <a name="vm-provisioning-issue"></a>Problem z obsługą maszyny wirtualnej

Przed przesłaniem oferty upewnij się, że masz rygorystyczne podejście do inicjowania obsługi maszyny wirtualnej. Aby wyświetlić format JSON obsługi administracyjnej maszyny wirtualnej, zobacz [Testowanie obrazu maszyny wirtualnej](azure-vm-image-test.md).

Problemy z aprowizacjim mogą obejmować następujące scenariusze awarii:

|Scenariusz|Błąd|Przyczyna|Rozwiązanie|
|---|---|---|---|
|1|Nieprawidłowy wirtualny dysk twardy (VHD)|Jeśli określona wartość pliku cookie w stopce dysku VHD jest niepoprawna, wirtualny dysk twardy będzie uznawany za nieprawidłowy.|Utwórz ponownie obraz i prześlij żądanie.|
|2|Nieprawidłowy typ obiektu BLOB|Inicjowanie obsługi maszyny wirtualnej nie powiodło się, ponieważ użyty blok jest typem obiektu BLOB, a nie typem strony.|Utwórz ponownie obraz i prześlij żądanie.|
|3|Limit czasu aprowizacji lub nieprawidłowo uogólniony|Wystąpił problem z generalizacją maszyn wirtualnych.|Utwórz ponownie obraz z generalizacją i prześlij żądanie.|
|

> [!NOTE]
> Aby uzyskać więcej informacji na temat generalizacji maszyn wirtualnych, zobacz:
> - [Dokumentacja systemu Linux](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Dokumentacja systemu Windows](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

## <a name="software-compliance-for-windows"></a>Zgodność oprogramowania dla systemu Windows

Jeśli żądanie obrazu systemu Windows zostało odrzucone z powodu problemu ze zgodnością oprogramowania, być może utworzono obraz systemu Windows z zainstalowanym wystąpieniem programu SQL Server, zamiast korzystać z odpowiedniego obrazu podstawowego wersji SQL z witryny Azure Marketplace.

Nie należy tworzyć własnego obrazu systemu Windows z zainstalowanym programem SQL Server. Zamiast tego należy użyć zatwierdzonych obrazów podstawowych SQL (Enterprise/Standard/Web) z witryny Azure Marketplace.

Jeśli próbujesz zainstalować program Visual Studio lub dowolny produkt licencjonowany w pakiecie Office, skontaktuj się z zespołem pomocy technicznej w celu uzyskania wcześniejszej zgody.

Aby uzyskać więcej informacji na temat wybierania zatwierdzonej bazy, zobacz [Tworzenie maszyny wirtualnej na podstawie zatwierdzonej podstawy](azure-vm-create-using-approved-base.md).

## <a name="tool-kit-test-case-execution-failed"></a>Wykonanie przypadku testowego zestawu narzędzi nie powiodło się

Zestaw narzędzi certyfikacji firmy Microsoft może pomóc w uruchomieniu przypadków testowych i upewnić się, że dysk VHD lub obraz jest zgodny ze środowiskiem platformy Azure.

Pobierz [zestaw narzędzi certyfikacji firmy Microsoft](azure-vm-image-test.md).

## <a name="linux-test-cases"></a>Przypadki testowe systemu Linux

Poniższa tabela zawiera listę przypadków testowych systemu Linux, które będą uruchamiane przez zestaw narzędzi. Sprawdzanie poprawności testu jest określone w opisie.

|Scenariusz|Przypadek testowy|Opis|
|---|---|---|
|1|Historia bash|Pliki historii bash powinny zostać wyczyszczone przed utworzeniem obrazu maszyny wirtualnej.|
|2|Wersja agenta systemu Linux|Należy zainstalować agenta 2.2.41 systemu Linux lub nowszego.|
|3|Wymagane parametry jądra|Sprawdza, czy ustawiono następujące parametry jądra: <br>Konsola = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300|
|4|Wymiana partycji na dysku systemu operacyjnego|Sprawdza, czy partycje wymiany nie są tworzone na dysku systemu operacyjnego.|
|5|Partycja główna na dysku systemu operacyjnego|Utwórz jedną partycję główną dla dysku systemu operacyjnego.|
|6|Wersja OpenSSL|Wersja OpenSSL powinna mieć wartość v 0.9.8 lub nowszą.|
|7|Wersja języka Python|Zdecydowanie zaleca się używanie języka Python w wersji 2,6 lub nowszej.|
|8|Interwał aktywności klienta|Ustaw wartość ClientAliveInterval na 180. Na potrzeby aplikacji można ustawić wartość z przeliczania od 30 do 235. W przypadku włączenia protokołu SSH dla użytkowników końcowych należy ustawić tę wartość jako wyjaśnioną.|
|9|Architektura systemu operacyjnego|Obsługiwane są tylko 64-bitowe systemy operacyjne.|
|10|Aktualizuj aktualizacje|Określa, czy jest włączona funkcja autoaktualizacji agenta systemu Linux.|

### <a name="common-errors-found-while-executing-previous-test-cases"></a>Napotkano typowe błędy podczas wykonywania poprzednich przypadków testowych

Poniższa tabela zawiera listę typowych błędów znalezionych podczas wykonywania poprzednich przypadków testowych:
 
|Scenariusz|Przypadek testowy|Błąd|Rozwiązanie|
|---|---|---|---|
|1|Przypadek testowy wersji agenta systemu Linux|Minimalna wersja agenta systemu Linux to 2.2.41 lub nowsza. To wymaganie jest obowiązkowe od 1 maja 2020.|Zaktualizuj wersję agenta systemu Linux i powinna być 2,241 lub nowsza. Więcej informacji można znaleźć na [stronie aktualizacji agenta systemu Linux](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).|
|2|Przypadek testowy historii bash|Zobaczysz błąd, jeśli rozmiar historii bash w przesłanym obrazie przekracza 1 kilobajt (KB). Rozmiar jest ograniczony do 1 KB, aby upewnić się, że wszystkie potencjalnie poufne informacje nie są przechwytywane w pliku historii bash.|Aby rozwiązać ten problem, Zainstaluj wirtualny dysk twardy w przypadku dowolnej innej działającej maszyny wirtualnej i wprowadź żądane zmiany (na przykład Usuń pliki historii *. bash* ), aby zmniejszyć rozmiar do 1 KB.|
|3|Wymagany przypadek testowy parametru jądra|Ten błąd zostanie wyświetlony, gdy wartość dla **konsoli** nie zostanie ustawiona na **ttyS0**. Sprawdź, uruchamiając następujące polecenie:<br>`cat /proc/cmdline`|Ustaw wartość dla **konsoli** na **ttyS0**i ponownie prześlij żądanie.|
|4|Przypadek testowy interwału ClientAlive|Jeśli wynik zestawu narzędzi daje wynik niepowodzenia dla tego przypadku testowego, istnieje niewłaściwa wartość dla **ClientAliveInterval**.|Dla opcji **ClientAliveInterval** ustaw wartość mniejszą lub równą 235, a następnie prześlij żądanie ponownie.|

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
|

Jeśli występują problemy z poprzednimi przypadkami testowymi, zapoznaj się z kolumną **Opis** rozwiązania w tabeli. Jeśli potrzebujesz więcej informacji, skontaktuj się z zespołem pomocy technicznej.

## <a name="data-disk-size-verification"></a>Weryfikacja rozmiaru dysku danych

Jeśli rozmiar każdego żądania przesłanego z dyskiem danych przekracza 1023 gigabajtów (GB), żądanie nie zostanie zatwierdzone. Ta reguła ma zastosowanie do systemów Linux i Windows.

Prześlij żądanie ponownie o rozmiarze mniejszym lub równym 1023 GB.

## <a name="os-disk-size-validation"></a>Walidacja rozmiaru dysku systemu operacyjnego

Zapoznaj się z następującymi regułami dotyczącymi ograniczeń rozmiaru dysku systemu operacyjnego. Po przesłaniu żądania upewnij się, że rozmiar dysku systemu operacyjnego przekracza ograniczenie dla systemu Linux lub Windows.

|System operacyjny|Zalecany rozmiar wirtualnego dysku twardego|
|---|---|
|Linux|30 GB do 1023 GB|
|Windows|30 GB do 250 GB|
|

Ponieważ maszyny wirtualne umożliwiają dostęp do podstawowego systemu operacyjnego, upewnij się, że rozmiar dysku VHD jest wystarczająco duży dla dysku VHD. Ponieważ dyski nie mogą być rozwijane bez przestojów, Użyj rozmiaru dysku z zakresu od 30 GB do 50 GB.

|Rozmiar dysku VHD|Rzeczywisty rozmiar zajęty|Rozwiązanie|
|---|---|---|
|>500 tebibajtów (TiB)|nie dotyczy|Skontaktuj się z zespołem pomocy technicznej w celu zatwierdzenia wyjątku.|
|250-500 TiB|>200 gibibajtach (GiB) — różnica między rozmiarem obiektu BLOB|Skontaktuj się z zespołem pomocy technicznej w celu zatwierdzenia wyjątku.|
|

> [!NOTE]
> Większe rozmiary dysków wiążą się z wyższymi kosztami i nastąpi opóźnienie podczas procesu instalacji i replikacji. Ze względu na to opóźnienie i koszt zespół pomocy technicznej może zwrócić się do uzasadnienia zatwierdzenia wyjątku.

## <a name="wannacry-patch-verification-test-for-windows"></a>Test weryfikacji poprawek atak wannacry dla systemu Windows

Aby zapobiec potencjalnym atakom związanym z wirusem atak wannacry, upewnij się, że wszystkie żądania obrazów systemu Windows są aktualizowane przy użyciu najnowszej poprawki.

Aby sprawdzić wersję poprawki systemu Windows Server dla szczegółów systemu operacyjnego i wersji minimalnej, która będzie obsługiwana, zapoznaj się z poniższą tabelą:

Wersja pliku obrazu może być zweryfikowana z `C:\windows\system32\drivers\srv.sys` lub `srv2.sys` .

> [!NOTE]
> System Windows Server 2019 nie ma wymagań dotyczących wersji obowiązkowej.

|System operacyjny|Wersja|
|---|---|
|System Windows obsługuje 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 z dodatkiem R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|Nie dotyczy|
|

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
|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>Rozmiar obrazu powinien być wielokrotnością megabajtów

Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do wielokrotności 1 megabajta (MB). Jeśli wirtualny dysk twardy nie jest zgodny z zalecanym rozmiarem wirtualnym, żądanie może zostać odrzucone.

Postępuj zgodnie z zaleceniami podczas konwertowania z dysku surowego na dysk VHD i upewnij się, że rozmiar dysku surowego jest wielokrotnością 1 MB. Aby uzyskać więcej informacji, zobacz [informacje dotyczące dystrybucji niezatwierdzonych](../virtual-machines/linux/create-upload-generic.md).

## <a name="vm-access-denied"></a>Odmowa dostępu do maszyny wirtualnej

Jeśli występują problemy z odmową dostępu podczas wykonywania przypadków testowych na maszynie wirtualnej, może to być spowodowane niewystarczającymi uprawnieniami do uruchamiania przypadków testowych.

Sprawdź, czy w ramach konta, na którym są uruchomione przypadki samotestowe, jest włączony prawidłowy dostęp. Jeśli dostęp nie jest włączony, włącz go, aby uruchomić przypadki testowe. Jeśli nie chcesz włączyć dostępu, możesz udostępnić wyniki przypadków testowych z zespołem pomocy technicznej.

## <a name="download-failure"></a>Błąd pobierania

Zapoznaj się z poniższą tabelą dotyczącą problemów występujących podczas pobierania obrazu maszyny wirtualnej przy użyciu adresu URL sygnatury dostępu współdzielonego (SAS).

|Scenariusz|Błąd|Przyczyna|Rozwiązanie|
|---|---|---|---|
|1|Nie znaleziono obiektu BLOB|Wirtualny dysk twardy może zostać usunięty lub przeniesiony z określonej lokalizacji.||
|2|Obiekt BLOB w użyciu|Wirtualny dysk twardy jest używany przez inny proces wewnętrzny.|Wirtualny dysk twardy powinien znajdować się w stanie używanym podczas pobierania go przy użyciu adresu URL sygnatury dostępu współdzielonego.|
|3|Nieprawidłowy adres URL SAS|Skojarzony adres URL sygnatury dostępu współdzielonego dla wirtualnego dysku twardego jest niepoprawny.|Uzyskaj prawidłowy adres URL sygnatury dostępu współdzielonego.|
|4|Nieprawidłowy podpis|Skojarzony adres URL sygnatury dostępu współdzielonego dla wirtualnego dysku twardego jest niepoprawny.|Uzyskaj prawidłowy adres URL sygnatury dostępu współdzielonego.|
|6|Nagłówek warunkowy HTTP|Adres URL sygnatury dostępu współdzielonego jest nieprawidłowy.|Uzyskaj prawidłowy adres URL sygnatury dostępu współdzielonego.|
|7|Nieprawidłowa nazwa wirtualnego dysku twardego|Sprawdź, czy znaki specjalne, takie jak znak procentu (%) lub znaki cudzysłowu (") istnieją w nazwie wirtualnego dysku twardego.|Zmień nazwę pliku VHD, usuwając znaki specjalne.|
|

## <a name="first-mb-2048-kb-partition-only-for-linux"></a>Partycja pierwszej pamięci (2048 KB) (tylko dla systemu Linux)

Podczas przesyłania wirtualnego dysku twardego upewnij się, że pierwsze 2048 KB dysku VHD jest puste. W przeciwnym razie Twoje żądanie zakończy się niepowodzeniem *.

>[!NOTE]
>* W przypadku niektórych obrazów specjalnych, takich jak te, które zostały utworzone na podstawie obrazów podstawowych systemu Windows Azure z portalu Azure Marketplace, sprawdzimy tag rozliczania i zignorujesz partycję MB, jeśli tag rozliczania jest obecny i jest zgodny z naszymi wewnętrznymi dostępnymi wartościami.

## <a name="default-credentials"></a>Poświadczenia domyślne

Zawsze upewnij się, że poświadczenia domyślne nie są wysyłane przy użyciu przesłanego wirtualnego dysku twardego. Dodanie poświadczeń domyślnych sprawia, że dysk VHD jest bardziej narażony na zagrożenia bezpieczeństwa. Zamiast tego należy utworzyć własne poświadczenia podczas przesyłania wirtualnego dysku twardego.
  
## <a name="datadisk-mapped-incorrectly"></a>Nieprawidłowo mapowany dysk

Gdy żądanie jest przesyłane z wieloma dyskami danych, ale ich kolejność nie jest w kolejności, jest to traktowane jako problem z mapowaniem. Na przykład jeśli istnieją trzy dyski danych, kolejność numerów musi wynosić *0, 1, 2*. Każda inna kolejność jest traktowana jako problem z mapowaniem.

Prześlij ponownie żądanie z prawidłową kolejnością na dyskach danych.

## <a name="incorrect-os-mapping"></a>Nieprawidłowe mapowanie systemu operacyjnego

Po utworzeniu obrazu można go zamapować lub przypisać do nieprawidłowej etykiety systemu operacyjnego. Na przykład po wybraniu **systemu Windows** jako części nazwy systemu operacyjnego podczas tworzenia obrazu dysk systemu operacyjnego powinien być instalowany tylko z systemem Windows. To samo wymaganie dotyczy systemu Linux.

## <a name="vm-not-generalized"></a>Nieuogólniona maszyna wirtualna

Jeśli wszystkie obrazy pobierane z witryny Azure Marketplace będą używane ponownie, należy przeprowadzić uogólniony dysk VHD systemu operacyjnego.

* W przypadku systemu **Linux**następujący proces służy do UOGÓLNIANIA maszyny wirtualnej z systemem Linux i wdrażania jej ponownie jako oddzielnej maszyny wirtualnej.

  W oknie SSH wprowadź następujące polecenie: `sudo waagent -deprovision+user`

* W przypadku **systemu Windows**można uogólniać obrazy systemu Windows przy użyciu programu `sysreptool` .

Aby uzyskać więcej informacji na temat tego narzędzia, zobacz temat [przygotowanie systemu (Sysprep) — Omówienie]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

## <a name="datadisk-errors"></a>Błędy dysku datadisks

W przypadku rozwiązań błędów, które są związane z dyskiem danych, należy użyć poniższej tabeli:

|Błąd|Przyczyna|Rozwiązanie|
|---|---|---|
|`DataDisk- InvalidUrl:`|Ten błąd może wystąpić z powodu nieprawidłowego numeru określonego dla numeru jednostki logicznej (LUN) podczas przesyłania oferty.|Sprawdź, czy sekwencja numerów LUN dla dysku danych znajduje się w centrum partnerskim.|
|`DataDisk- NotFound:`|Ten błąd może wystąpić z powodu braku lokalizacji dysku danych w określonym adresie URL sygnatury dostępu współdzielonego.|Sprawdź, czy dysk danych znajduje się w adresie URL sygnatury dostępu współdzielonego, który jest określony w żądaniu.|
|

## <a name="remote-access-issue"></a>Problem z dostępem zdalnym

Jeśli opcja Remote Desktop Protocol (RDP) nie jest włączona dla obrazu systemu Windows, zostanie wyświetlony ten błąd.

Włącz dostęp RDP dla obrazów systemu Windows przed ich przesłaniem.

## <a name="bash-history-failed"></a>Historia bash nie powiodła się

Ten błąd zostanie wyświetlony, jeśli rozmiar historii bash w przesłanym obrazie wynosi więcej niż 1 kilobajt (KB). Rozmiar jest ograniczony do 1 KB, aby upewnić się, że wszystkie potencjalnie poufne informacje nie są przechwytywane w pliku historii bash.

Poniżej przedstawiono procedurę usuwania historii "bash".

Krok 1. Wdróż maszynę wirtualną i kliknij opcję "Uruchom polecenie" na Azure Portal.
![Uruchom polecenie na Azure Portal](./media/create-vm/vm-certification-issues-solutions-3.png)

Krok 2. Wybierz pierwszą opcję "RunShellScript" i uruchom poniższe polecenie.

Polecenie: "Cat/dev/null > ~/.bash_history && History-c" ![ polecenie History bash na Azure Portal](./media/create-vm/vm-certification-issues-solutions-4.png)

Krok 3. Po pomyślnym wykonaniu polecenia Uruchom ponownie maszynę wirtualną.

Krok 4. Uogólnij maszynę wirtualną, zrób dysk VHD obrazu i Zatrzymaj maszynę wirtualną.

Krok 5. Re-Submit uogólniony obraz.

## <a name="requesting-exceptions-custom-templates-on-vm-images-for-selective-tests"></a>Żądanie wyjątków (szablonów niestandardowych) w obrazach maszyn wirtualnych dla testów selektywnych

Wydawcy mogą żądać wyjątków dla kilku testów wykonywanych podczas certyfikacji maszyny wirtualnej. Wyjątki są udostępniane w bardzo rzadkich przypadkach, gdy Wydawca dostarcza dowód do obsługi żądania. Zespół certyfikacji zastrzega sobie prawo do odmowy lub zatwierdzenia wyjątków w dowolnym momencie.

W poniższych sekcjach porozmawiamy o głównych scenariuszach, w których żąda się wyjątków, i sposobach ich żądania.

### <a name="scenarios-for-exception"></a>Scenariusze dla wyjątku

Zwykle istnieją trzy scenariusze/przypadki, w których wydawcy żądają wyjątków.

- **Wyjątek dla co najmniej jednego przypadku testowego** — wydawcy kontaktują się z [pomocą techniczną](https://aka.ms/marketplacepublishersupport) Centrum partnerskiego w celu żądania wyjątków dla przypadków testowych.

- **Maszyny wirtualne zablokowane/brak dostępu głównego** — kilku wydawców ma scenariusze, w których maszyny wirtualne muszą być zablokowane, ponieważ mają takie oprogramowanie jak zapory zainstalowane na maszynie wirtualnej. W takim przypadku wydawcy mogą pobrać narzędzie do [testowania certyfikowane](https://aka.ms/AzureCertificationTestTool) i przesłać raport [w centrum partnerskim.](https://aka.ms/marketplacepublishersupport)

- **Szablony niestandardowe** — Niektórzy wydawcy publikują obrazy maszyn wirtualnych, które wymagają NIESTANDARDOWEGO szablonu ARM do wdrożenia maszyn wirtualnych. W takim przypadku wydawcy powinni przesłać niestandardowe szablony w [pomocy technicznej](https://aka.ms/marketplacepublishersupport) Centrum partnerskiego, aby można było użyć tego samego zespołu certyfikacji do weryfikacji.

### <a name="information-to-provide-for-exception-scenarios"></a>Informacje, które należy podać dla scenariuszy wyjątków

Wydawcy powinni skontaktować się z [pomocą techniczną](https://aka.ms/marketplacepublishersupport) Centrum partnerskiego, aby zażądać wyjątków dla powyższego scenariusza z dodatkowymi następującymi informacjami:

   1. IDENTYFIKATOR wydawcy — identyfikator wydawcy w portalu Centrum partnerskiego
   2. Identyfikator/nazwa oferty — identyfikator/nazwę oferty, dla której żądany jest wyjątek
   3. Jednostka SKU/identyfikator planu — identyfikator planu/jednostka SKU oferty maszyny wirtualnej, dla której żądany jest wyjątek
   4. Wersja — wersja oferty maszyny wirtualnej, dla której żądany jest wyjątek
   5. Typ wyjątku — testuje, zablokowano maszynę wirtualną i szablony niestandardowe
   6. Przyczyna żądania — powód tego wyjątku i informacje dotyczące testów do wykluczenia
   7. Oś czasu — Data, do której zażądano tego wyjątku
   8. Załącznik — Dołącz wszelkie dokumenty z dowodem ważności. W przypadku zablokowanych maszyn wirtualnych Dołącz raport testowy i dla szablonów niestandardowych Podaj niestandardowy szablon ARM jako załącznik. Niepowodzenie dołączenia raportu do zablokowanych maszyn wirtualnych i niestandardowego szablonu ARM dla szablonów niestandardowych spowoduje odmowę żądania

## <a name="address-a-vulnerability-or-exploit-in-a-vm-offer"></a>Rozwiązanie luki w zabezpieczeniach lub wykorzystania jej w ramach oferty maszyny wirtualnej

W tej sekcji opisano sposób dostarczania nowego obrazu maszyny wirtualnej w przypadku wykrycia luki w zabezpieczeniach lub wykorzystania jej przez jeden z obrazów maszyn wirtualnych. Dotyczy to tylko ofert z maszynami wirtualnymi platformy Azure opublikowanych w portalu Azure Marketplace.

> [!NOTE]
> Nie można usunąć ostatniego obrazu maszyny wirtualnej z planu ani zakończyć sprzedaży ostatniego planu oferty.

Wykonaj jedną z następujących czynności:

- Jeśli masz nowy obraz maszyny wirtualnej w celu zastąpienia zagrożonego obrazu maszyny wirtualnej, zobacz temat [zapewnianie stałego obrazu maszyny wirtualnej](#provide-a-fixed-vm-image) poniżej.
- Jeśli nie masz nowego obrazu maszyny wirtualnej w celu zamienienia samego obrazu maszyny wirtualnej w ramach planu lub jeśli planujesz, [Zatrzymaj sprzedawanie planu](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).
- Jeśli nie planujesz zastąpienia jedynego obrazu maszyny wirtualnej w ofercie, zalecamy [zaprzestanie sprzedaży oferty](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="provide-a-fixed-vm-image"></a>Udostępnianie stałego obrazu maszyny wirtualnej

Aby zapewnić stały obraz maszyny wirtualnej w celu zastąpienia obrazu maszyny wirtualnej, który ma luki w zabezpieczeniach lub wykorzystania, wykonaj następujące czynności:

1. Podaj nowy obraz maszyny wirtualnej w celu rozwiązania luk w zabezpieczeniach lub wykorzystania.
2. Usuń obraz maszyny wirtualnej z luką w zabezpieczeniach lub za pomocą programu.
3. Opublikuj ponownie ofertę.

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>Podaj nowy obraz maszyny wirtualnej w celu rozwiązania luk w zabezpieczeniach lub wykorzystania

Aby wykonać te kroki, należy przygotować zasoby techniczne dla obrazu maszyny wirtualnej, który ma zostać dodany. Aby uzyskać więcej informacji, zobacz temat [Tworzenie maszyny wirtualnej przy użyciu zatwierdzonej bazy](azure-vm-create-using-approved-base.md) lub [Tworzenie maszyny wirtualnej przy użyciu własnego obrazu](azure-vm-create-using-own-image.md)oraz [generowanie identyfikatora URI sygnatury dostępu współdzielonego dla obrazu maszyny wirtualnej](azure-vm-get-sas-uri.md).

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W menu nawigacji po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.
3. W kolumnie **alias oferty** wybierz ofertę.
4. Na karcie **Przegląd planu** w kolumnie **Nazwa** wybierz plan, do którego chcesz dodać maszynę wirtualną.
5. Na karcie **konfiguracja techniczna** w obszarze **obrazy maszyn wirtualnych** wybierz pozycję **+ Dodaj obraz maszyny wirtualnej**.

> [!NOTE]
> Do jednego planu można dodać tylko jeden obraz maszyny wirtualnej. Aby dodać wiele obrazów maszyn wirtualnych, Opublikuj pierwszy z nich i zaczekaj, aż osiągnie etap wylogowania wydawcy, a następnie Dodaj następny obraz maszyny wirtualnej.

6. W wyświetlonych polach Podaj nową wersję dysku i obraz maszyny wirtualnej.
7. Wybierz pozycję **Zapisz wersję roboczą**.

Przejdź do następnej sekcji poniżej, aby usunąć obraz maszyny wirtualnej z luką w zabezpieczeniach.

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>Usuwanie obrazu maszyny wirtualnej z wykorzystaniem luk w zabezpieczeniach lub wykorzystania

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W menu nawigacji po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.
3. W kolumnie **alias oferty** wybierz ofertę.
4. Na karcie **Przegląd planu** w kolumnie **Nazwa** wybierz plan z maszyną wirtualną, którą chcesz usunąć.
5. Na karcie **konfiguracja techniczna** w obszarze **obrazy maszyn wirtualnych** obok obrazu maszyny wirtualnej, który chcesz usunąć, wybierz pozycję **Usuń obraz maszyny wirtualnej**.
6. W wyświetlonym oknie dialogowym wybierz pozycję **Kontynuuj**.
7. Wybierz pozycję **Zapisz wersję roboczą**.

Aby ponownie opublikować ofertę, przejdź do następnej sekcji poniżej.

#### <a name="republish-the-offer"></a>Ponowne opublikowanie oferty

1. Wybierz pozycję **Przejrzyj i Opublikuj**.
2. Jeśli musisz podać informacje dotyczące zespołu certyfikacji, Dodaj go do pola **uwagi dotyczące certyfikacji** .
3. Kliknij pozycję **Opublikuj**.

Aby zakończyć proces publikowania, zobacz [Przegląd i publikowanie ofert](review-publish-offer.md).

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie właściwości oferty maszyny wirtualnej](azure-vm-create-properties.md)
- [Aktywne nagrody dotyczące portalu Marketplace](partner-center-portal/marketplace-rewards.md)
- Jeśli masz pytania lub opinie na temat poprawy, skontaktuj się z [pomocą techniczną](https://aka.ms/marketplacepublishersupport)Centrum partnerskiego.
