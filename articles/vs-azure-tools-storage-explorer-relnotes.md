---
title: Informacje o wersji Eksploratora usługi Microsoft Azure Storage
description: Zapoznaj się z informacjami o wersji najnowszej wersji programu Eksplorator usługi Microsoft Azure Storage. Są również wyświetlane informacje o wersji poprzednich wersji.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: a53f947eb2a44cc8773be8ee2b2bd03ca899be22
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95521006"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Informacje o wersji Eksploratora usługi Microsoft Azure Storage

Ten artykuł zawiera najnowsze informacje o wersji dla programu Eksplorator usługi Azure Storage, a także informacje o wersji dla poprzednich wersji. 

[Eksplorator usługi Microsoft Azure Storage](./vs-azure-tools-storage-manage-with-storage-explorer.md) jest aplikacją autonomiczną, która umożliwia łatwe współdziałanie z danymi usługi Azure Storage w systemach Windows, MacOS i Linux.

Aby pobrać wcześniejsze wersje Eksplorator usługi Storage, odwiedź [stronę wydań](https://github.com/microsoft/AzureStorageExplorer/releases) w naszym repozytorium GitHub.

## <a name="version-1110"></a>1.11.0 wersja
11/4/2019

### <a name="new"></a>Nowy
* Operacje dla obiektów blob, ADLS Gen2 i Managed Disks używają zintegrowanych AzCopy. Dokładniej mówiąc, następujące operacje są wykonywane przy użyciu AzCopy:
   * Obiekty blob
      * Otwórz do edycji i przekazywania
      * Przekaż, w tym przeciągnij & upuść
      * Pobierz
      * Kopiuj & wklej #1249
      * Usuń
   * ADLS Gen2 obiektów BLOB
      * Przekaż, w tym przeciągnij & upuść
      * Pobierz
      * Kopiuj & wklej
      * Usuń, łącznie z usunięciem folderu
   * Dyski zarządzane
      * Przekazywanie
      * Pobierz
      * Kopiuj & wklej

   Ponadto do zintegrowanego środowiska AzCopy dodano kilka często pożądanych funkcji:
   * Rozwiązywanie konfliktów — podczas transferów zostanie wyświetlony monit o rozwiązanie konfliktów. #1455
   * Przekaż jako stronicowe obiekty blob — możesz wybrać, czy AzCopy przekazuje pliki VHD i VHDX jako stronicowe obiekty blob. #1164 i #1601
   * Konfigurowalne parametry AzCopy — dodano kilka ustawień w celu dostrajania wydajności i użycia zasobów AzCopy. Zobacz więcej szczegółów poniżej.

* Aby włączyć dostęp do ADLS Gen2 i obiektów BLOB dla wielu protokołów i jeszcze bardziej ulepszyć ADLS Gen2 środowiska, dodaliśmy następujące funkcje dla kont ADLS Gen2:
   * Wyszukaj przy użyciu przyjaznych nazw, aby ustawić uprawnienia listy ACL
   * Wyświetlanie ukrytych kontenerów, takich jak $logs i $web
   * Pozyskiwanie i przerywanie dzierżawy kontenera
   * Pobieranie i przerywanie dzierżawy obiektów BLOB #848
   * Zarządzanie zasadami dostępu do kontenera
   * Konfigurowanie warstw dostępu do obiektów BLOB
   * Kopiuj & wklej obiekty blob

* W tej wersji zapoznajemy 17 dodatkowych języków. Możesz przełączyć się do wybranego języka na stronie Ustawienia w obszarze "aplikacja" → "ustawienia regionalne" → "(wersja zapoznawcza)". Nadal pracujemy nad tłumaczeniem dodatkowych ciągów i ulepszaniem jakości tłumaczenia. Jeśli masz jakiekolwiek opinie dotyczące tłumaczenia lub jeśli zauważysz ciąg, który nie został jeszcze przetłumaczony, [Otwórz problem w usłudze GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* W każdej wersji próbujemy dołączyć kilka ustawień, aby umożliwić precyzyjne Eksplorator usługi Storage. W tej wersji dodaliśmy ustawienia w celu dodatkowego skonfigurowania AzCopy oraz ukrycia węzłów usługi:
   * Limit przepustowości AzCopy — pomaga kontrolować, ile sieci AzCopy używa. To ustawienie można znaleźć w "transfery" → "AzCopy" → "Maksymalna szybkość transferu". #1099
   * Sprawdzanie AzCopy MD5 — umożliwia skonfigurowanie, czy i jak ścisłe AzCopy sprawdzają skróty MD5 podczas pobierania. To ustawienie można znaleźć w "transfers" → "AzCopy" → "Check MD5".
   * AzCopy rozmiar buforu współbieżności i pamięci — domyślnie AzCopy przeanalizuje maszynę, aby określić rozsądne wartości domyślne dla tych ustawień. Jednak jeśli wystąpią problemy z wydajnością, te zaawansowane ustawienia mogą służyć do dokładniejszego dostosowywania sposobu działania AzCopy na komputerze. Te ustawienia można znaleźć w obszarze "transfery" → "AzCopy". #994
   * Wyświetlanie i ukrywanie węzłów usługi — te ustawienia umożliwiają wyświetlanie lub ukrywanie dowolnych usług platformy Azure obsługiwanych przez Eksplorator usługi Storage. Te ustawienia można znaleźć w sekcji "usługi". #1877

* Podczas tworzenia migawki dysku zarządzanego jest teraz dostępna nazwa domyślna. #1847
* W przypadku dołączania do usługi Azure AD, jeśli dołączysz kontener ADLS Gen2 obiektów blob, obok węzła zostanie wyświetlony komunikat "(ADLS Gen2)". #1861

### <a name="fixes"></a>Poprawki
* Podczas kopiowania, przekazywania lub pobierania dużych dysków Eksplorator usługi Storage czasami niepowodzeniem odwołać dostęp do dysków należących do tej operacji. Ten problem został rozwiązany. #2048
* Statystyka tabeli nie powiodła się podczas wyświetlania zapytania klucza partycji. Ten problem został rozwiązany. #1886

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage 1.11.0 teraz wymaga punktu końcowego systemu plików DFS (takiego jak "myaccount.dfs.core.windows.net") w celu dołączenia do kontenerów ADLS Gen2. Poprzednie wersje Eksplorator usługi Storage mogą korzystać z punktu końcowego obiektu BLOB. Te załączniki mogą przestać działać po uaktualnieniu do 1.11.0. Jeśli wystąpi ten problem, dołącz go ponownie przy użyciu punktu końcowego systemu plików DFS.
* Ustawienia liczbowe nie są sprawdzane, czy znajdują się w prawidłowym zakresie. #2140
* Kopiowanie kontenerów obiektów blob z jednego konta magazynu do innego w widoku drzewa może zakończyć się niepowodzeniem. Badamy problem. #2124
* Ustawienie AutoRefresh nie ma jeszcze wpływu na wszystkie operacje w Eksploratorze obiektów BLOB.
* Funkcje dysków zarządzanych nie są obsługiwane w Azure Stack.
* Jeśli przekazanie lub wklejenie dysku zakończy się niepowodzeniem i przed awarią zostanie utworzony nowy dysk, Eksplorator usługi Storage nie spowoduje usunięcia dysku.
* W zależności od tego, kiedy anulujesz przekazywanie lub wklejanie dysku, można pozostawić nowy dysk w uszkodzonym stanie. W takim przypadku należy usunąć nowy dysk lub ręcznie wywołać interfejsy API dysku, aby zastąpić zawartość dysku, tak że nie jest już uszkodzona.
* W przypadku korzystania z funkcji RBAC Eksplorator usługi Storage wymaga pewnych uprawnień do warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md) .
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, po prostu Odśwież węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
   * Dyski zarządzane
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchomienie Eksplorator usługi Storage w systemie Linux wymaga wcześniejszego zainstalowania niektórych zależności. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) Eksplorator usługi Storage.

## <a name="previous-releases"></a>Poprzednie wersje

* [1.10.1 wersja](#version-1101)
* [1.10.0 wersja](#version-1100)
* [1.9.0 wersja](#version-190)
* [1.8.1 wersja](#version-181)
* [1.8.0 wersja](#version-180)
* [Wersja 1.7.0](#version-170)
* [Wersja 1.6.2](#version-162)
* [1.6.1 wersja](#version-161)
* [1.6.0 wersja](#version-160)
* [1.5.0 wersja](#version-150)
* [1.4.4 wersja](#version-144)
* [Wersja 1.4.3](#version-143)
* [Wersja 1.4.2](#version-142)
* [Wersja 1.4.1](#version-141)
* [1.3.0 wersja](#version-130)
* [1.2.0 wersja](#version-120)
* [1.1.0 wersja](#version-110)
* [Wersja 1.0.0](#version-100)
* [0.9.6 wersja](#version-096)
* [0.9.5 wersja](#version-095)
* [Wersja 0.9.4 i 0.9.3](#version-094-and-093)
* [0.9.2 wersja](#version-092)
* [Wersja od 0.9.1 i 0.9.0](#version-091-and-090)
* [0.8.16 wersja](#version-0816)
* [0.8.14 wersja](#version-0814)
* [0.8.13 wersja](#version-0813)
* [Wersje 0.8.12 i 0.8.11 i 0.8.10](#version-0812-and-0811-and-0810)
* [Wersja 0.8.9 i 0.8.8](#version-089-and-088)
* [0.8.7 wersja](#version-087)
* [0.8.6 wersja](#version-086)
* [0.8.5 wersja](#version-085)
* [0.8.4 wersja](#version-084)
* [0.8.3 wersja](#version-083)
* [0.8.2 wersja](#version-082)
* [0.8.0 wersja](#version-080)
* [0.7.20160509.0 wersja](#version-07201605090)
* [0.7.20160325.0 wersja](#version-07201603250)
* [0.7.20160129.1 wersja](#version-07201601291)
* [0.7.20160105.0 wersja](#version-07201601050)
* [0.7.20151116.0 wersja](#version-07201511160)

## <a name="version-1101"></a>1.10.1 wersja
9/19/2019

### <a name="hotfix"></a>Rozwiązującą
* Niektórzy użytkownicy napotkały błąd w 1.10.0 podczas próby wyświetlenia ich danych na kontach ADLS generacji 1. Ten błąd uniemożliwił prawidłowe renderowanie panelu Eksploratora. Ten problem został rozwiązany. #1853 #1865

### <a name="new"></a>Nowy
* Eksplorator usługi Storage ma teraz interfejs użytkownika dedykowanych ustawień. Możesz uzyskać do niego dostęp przy użyciu opcji Edytuj ustawienia → lub klikając ikonę Ustawienia (koła zębatego) na pasku narzędzi po lewej stronie. Ta funkcja jest pierwszą czynnością, którą należy wykonać, aby zapewnić różne [Ustawienia użytkownika](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). Począwszy od tej wersji, obsługiwane są następujące ustawienia:
  * Motyw
  * Serwer proxy
  * Wyloguj się #6 zakończenia
  * Włącz logowanie przy użyciu przepływu kodu urządzenia
  * #1526 AutoRefresh
  * Włącz AzCopy
  * AzCopy czas trwania SAS Jeśli istnieją inne ustawienia, które chcesz zobaczyć, [Otwórz problem w usłudze GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) z opisem ustawienia, które chcesz zobaczyć.
* Eksplorator usługi Storage teraz obsługuje Managed Disks. Możesz:
  * Przekazywanie lokalnego wirtualnego dysku twardego do nowego dysku
  * Pobieranie dysku
  * Kopiowanie i wklejanie dysków między grupami zasobów i regionami
  * Usuwanie dysków
  * Tworzenie migawki dysku

Przekazywanie, pobieranie i między regionami kopiowanie dysków jest obsługiwane przez AzCopy v10.
* Eksplorator usługi Storage można teraz zainstalować za pośrednictwem magazynu Snap w systemie Linux. Podczas instalacji za pomocą usługi Snap są instalowane wszystkie zależności, w tym .NET Core. Obecnie sprawdzono, że Eksplorator usługi Storage działa dobrze w Ubuntu i CentOS. Jeśli wystąpią problemy z instalacją z magazynu Snap w innym systemie Linux dystrybucje, należy [otworzyć problem w witrynie GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Aby dowiedzieć się więcej o instalowaniu z magazynu Snap, zobacz nasz [Przewodnik wprowadzający](./vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux). #68
* Wprowadzono dwa istotne zmiany w celu dołączenia do Azure Active Directory (Azure AD), które mają być bardziej przydatne dla ADLS Gen2 użytkowników:
  * Teraz wybierz dzierżawcę, do którego jest dołączany zasób. Oznacza to, że nie trzeba już uzyskiwać dostępu RBAC do subskrypcji zasobu.
  * W przypadku dołączania kontenera ADLS Gen2 obiektów BLOB możesz teraz dołączyć do określonej ścieżki w kontenerze.
* Podczas zarządzania listami ACL dla ADLS Gen2 plików i folderów, Eksplorator usługi Storage będzie teraz pokazywał przyjazne nazwy dla jednostek na liście ACL. #957
* Po dodaniu za pomocą identyfikatora OID do ADLS Gen2 listy kontroli dostępu Eksplorator usługi Storage zostanie teraz zweryfikowana, że identyfikator OID należy do prawidłowej jednostki w dzierżawie. #1603
* Skróty klawiaturowe do nawigowania między kartami teraz używają bardziej standardowych kombinacji klawiszy. #1018
* Drugie kliknięcie karty spowoduje jej zamknięcie. #1348
* Jeśli transfer AzCopy zawiera pomijania i nie ma błędów, Eksplorator usługi Storage będzie teraz wyświetlał ikonę ostrzeżenia, aby wyróżnić pominięte. #1490
* Zintegrowana AzCopy została zaktualizowana do wersji 10.2.1. Ponadto można wyświetlić wersję programu AzCopy zainstalowaną w oknie dialogowym informacje. #1343

### <a name="fixes"></a>Poprawki
* Wielu użytkowników w różnych "nie można odczytać wersji niezdefiniowanej" lub "nie można odczytać połączenia niezdefiniowanych" podczas pracy z dołączonymi kontami magazynu. Mimo że nadal kontynuujemy badanie głównej przyczyny tego problemu, w 1.10.0 Ulepszono obsługę błędów podczas ładowania dołączonych kont magazynu. #1626, #985 i #1532
* Być może drzewo Eksploratora (po lewej stronie), aby przejść w stan, w którym fokus przechodzi wielokrotnie do górnego węzła. Ten problem został rozwiązany. #1596
* Podczas zarządzania migawkami obiektu BLOB czytniki zawartości ekranu nie odczytują sygnatury czasowej skojarzonej z migawką. Ten problem został rozwiązany. #1202
* Ustawienie serwera proxy w usłudze macOS nie zostało ustawione na czas na ich użycie przez proces uwierzytelniania. Ten problem został rozwiązany. #1567
* Jeśli konto magazynu w chmurze suwerennej zostało dołączone przy użyciu nazwy i klucza, AzCopy nie będzie działała. Ten problem został rozwiązany. #1544
* W przypadku dołączania za pomocą parametrów połączenia, Eksplorator usługi Storage usunie teraz spacje końcowe. #1387

### <a name="known-issues"></a>Znane problemy
* Ustawienie AutoRefresh nie ma jeszcze wpływu na wszystkie operacje w Eksploratorze obiektów BLOB.
* Funkcje dysków zarządzanych nie są obsługiwane w Azure Stack.
* Jeśli przekazanie lub wklejenie dysku zakończy się niepowodzeniem i przed awarią zostanie utworzony nowy dysk, Eksplorator usługi Storage nie spowoduje usunięcia dysku.
* W zależności od tego, kiedy anulujesz przekazywanie lub wklejanie dysku, można pozostawić nowy dysk w uszkodzonym stanie. W takim przypadku należy usunąć nowy dysk lub ręcznie wywołać interfejsy API dysku, aby zastąpić zawartość dysku, tak że nie jest już uszkodzona.
* W zależności od tego, kiedy anulujesz przekazywanie lub wklejanie dysku, można pozostawić nowy dysk w uszkodzonym stanie. W takim przypadku należy usunąć nowy dysk lub ręcznie wywołać interfejsy API dysku, aby zastąpić zawartość dysku, tak że nie jest już uszkodzona.
* Podczas pobierania obiektu BLOB, który nie jest AzCopy, MD5 dla dużych plików nie jest weryfikowany. Jest to spowodowane usterką w zestawie SDK magazynu. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* W przypadku korzystania z funkcji RBAC Eksplorator usługi Storage wymaga pewnych uprawnień do warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md) .
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, po prostu Odśwież węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
   * Dyski zarządzane
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchomienie Eksplorator usługi Storage w systemie Linux wymaga wcześniejszego zainstalowania niektórych zależności. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) Eksplorator usługi Storage.


## <a name="version-1100"></a>1.10.0 wersja
9/12/2019

### <a name="new"></a>Nowy

* Eksplorator usługi Storage ma teraz interfejs użytkownika dedykowanych ustawień. Możesz uzyskać do niego dostęp przy użyciu opcji Edytuj ustawienia → lub klikając ikonę Ustawienia (koła zębatego) na pasku narzędzi po lewej stronie. Ta funkcja jest pierwszą czynnością, którą należy wykonać, aby zapewnić różne [Ustawienia użytkownika](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). Począwszy od tej wersji, obsługiwane są następujące ustawienia:
    * Motyw
    * Serwer proxy
    * Wyloguj się [#6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6) zakończenia
    * Włącz logowanie przy użyciu przepływu kodu urządzenia
    * [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526) AutoRefresh
    * Włącz AzCopy
    * Czas trwania AzCopy SAS

    Jeśli istnieją inne ustawienia, które chcesz zobaczyć, [Otwórz problem w usłudze GitHub z opisem ustawienia, które chcesz zobaczyć](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* Eksplorator usługi Storage teraz obsługuje Managed Disks. Możesz:
    * Przekazywanie lokalnego wirtualnego dysku twardego do nowego dysku
    * Pobieranie dysku
    * Kopiowanie i wklejanie dysków między grupami zasobów i regionami
    * Usuwanie dysków
    * Tworzenie migawki dysku

    Przekazywanie, pobieranie i między regionami kopiowanie dysków jest obsługiwane przez AzCopy v10.
* Eksplorator usługi Storage można teraz zainstalować za pośrednictwem magazynu Snap w systemie Linux. Podczas instalacji za pomocą usługi Snap są instalowane wszystkie zależności, w tym .NET Core. Obecnie sprawdzono, że Eksplorator usługi Storage działa dobrze w Ubuntu i CentOS. Jeśli wystąpią problemy z instalacją z magazynu Snap w innym systemie Linux dystrybucje, należy [otworzyć problem w witrynie GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Aby dowiedzieć się więcej o instalowaniu z magazynu Snap, zobacz nasz [Przewodnik wprowadzający](./vs-azure-tools-storage-manage-with-storage-explorer.md). [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Wprowadzono dwa istotne zmiany w celu dołączenia do Azure Active Directory (Azure AD), które mają być bardziej przydatne dla ADLS Gen2 użytkowników: * teraz wybierz dzierżawę, do której jest dołączany zasób. Oznacza to, że nie trzeba już uzyskiwać dostępu RBAC do subskrypcji zasobu.
        * Jeśli dołączysz kontener ADLS Gen2 obiektów blob, możesz teraz dołączyć do określonej ścieżki w kontenerze.
* Podczas zarządzania listami ACL dla ADLS Gen2 plików i folderów, Eksplorator usługi Storage będzie teraz pokazywał przyjazne nazwy dla jednostek na liście ACL. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* Po dodaniu za pomocą identyfikatora OID do ADLS Gen2 listy kontroli dostępu Eksplorator usługi Storage zostanie teraz zweryfikowana, że identyfikator OID należy do prawidłowej jednostki w dzierżawie. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* Skróty klawiaturowe do nawigowania między kartami teraz używają bardziej standardowych kombinacji klawiszy. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* Drugie kliknięcie karty spowoduje jej zamknięcie. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Jeśli transfer AzCopy zawiera pomijania i nie ma błędów, Eksplorator usługi Storage będzie teraz wyświetlał ikonę ostrzeżenia, aby wyróżnić pominięte. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* Zintegrowana AzCopy została zaktualizowana do wersji 10.2.1. Ponadto można wyświetlić wersję programu AzCopy zainstalowaną w oknie dialogowym informacje. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Poprawki

* Wielu użytkowników w różnych "nie można odczytać wersji niezdefiniowanej" lub "nie można odczytać połączenia niezdefiniowanych" podczas pracy z dołączonymi kontami magazynu. Mimo że nadal kontynuujemy badanie głównej przyczyny tego problemu, w 1.10.0 Ulepszono obsługę błędów podczas ładowania dołączonych kont magazynu. [#1626](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626), [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)i [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Być może drzewo Eksploratora (po lewej stronie), aby przejść w stan, w którym fokus przechodzi wielokrotnie do górnego węzła. Ten problem został rozwiązany. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Podczas zarządzania migawkami obiektu BLOB czytniki zawartości ekranu nie odczytują sygnatury czasowej skojarzonej z migawką. Ten problem został rozwiązany. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* Ustawienie serwera proxy w usłudze macOS nie zostało ustawione na czas na ich użycie przez proces uwierzytelniania. Ten problem został rozwiązany. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Jeśli konto magazynu w chmurze suwerennej zostało dołączone przy użyciu nazwy i klucza, AzCopy nie będzie działała. Ten problem został rozwiązany. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* W przypadku dołączania za pomocą parametrów połączenia, Eksplorator usługi Storage usunie teraz spacje końcowe. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Znane problemy

* Ustawienie AutoRefresh nie ma jeszcze wpływu na wszystkie operacje w Eksploratorze obiektów BLOB.
* Funkcje dysków zarządzanych nie są obsługiwane w Azure Stack.
* Jeśli przekazanie lub wklejenie dysku zakończy się niepowodzeniem i przed awarią zostanie utworzony nowy dysk, Eksplorator usługi Storage nie spowoduje usunięcia dysku.
* W zależności od tego, kiedy anulujesz przekazywanie lub wklejanie dysku, można pozostawić nowy dysk w uszkodzonym stanie. W takim przypadku należy usunąć nowy dysk lub ręcznie wywołać interfejsy API dysku, aby zastąpić zawartość dysku, tak że nie jest już uszkodzona.
* Podczas pobierania obiektu BLOB, który nie jest AzCopy, MD5 dla dużych plików nie jest weryfikowany. Jest to spowodowane usterką w zestawie SDK magazynu. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* W przypadku korzystania z funkcji RBAC Eksplorator usługi Storage wymaga pewnych uprawnień do warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md) .
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, po prostu Odśwież węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
   * Dyski zarządzane
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchomienie Eksplorator usługi Storage w systemie Linux wymaga wcześniejszego zainstalowania niektórych zależności. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) Eksplorator usługi Storage.

## <a name="version-190"></a>1.9.0 wersja
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Pobierz Eksplorator usługi Azure Storage 1.9.0
- [Eksplorator usługi Azure Storage 1.9.0 dla systemu Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Eksplorator usługi Azure Storage 1.9.0 dla komputerów Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Eksplorator usługi Azure Storage 1.9.0 dla systemu Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nowy

* Teraz możesz dołączać kontenery obiektów BLOB za pośrednictwem usługi Azure AD (uprawnienia RBAC lub ACL). Ta funkcja ma pomóc użytkownikom mającym dostęp do kontenerów, ale nie kont magazynu, w których znajdują się kontenery. Aby uzyskać więcej informacji na temat tej funkcji, zobacz nasz przewodnik Wprowadzenie.
* Uzyskaj i Przerwij dzierżawę teraz pracują z RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* Zarządzanie zasadami dostępu i ustawianie poziomu dostępu publicznego teraz działa z RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* Usuwanie folderów obiektów BLOB działa teraz z RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* Zmiana warstwy dostępu do obiektów BLOB teraz współpracuje z RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* Teraz możesz szybko zresetować szybki dostęp poprzez "pomoc" → "reset". [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Funkcje w wersji zapoznawczej

* Logowanie do przepływu kodu urządzenia jest teraz dostępne w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj logowania do przepływu kodu urządzenia". Zachęcamy użytkowników, którzy mieli problemy z pustymi oknami logowania, aby wypróbować tę funkcję, ponieważ może to być bardziej niezawodna forma logowania.
* Eksplorator usługi Storage zintegrowany z usługą AzCopy jest obecnie dostępny w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj AzCopy do ulepszonego przekazywania i pobierania obiektów BLOB". Transfery obiektów BLOB zakończonych za pomocą AzCopy powinny być szybsze i bardziej wydajne.

### <a name="fixes"></a>Poprawki

* Ustalono, że nie można załadować więcej niż 50 subskrypcji dla jednego konta. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Naprawiono przycisk "Zaloguj się" nie działa na pasku informacyjnym, który pojawia się, gdy łącze bezpośrednie nie powiedzie się. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Naprawiono brak przekazywania plików aplikacji w witrynie macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* Naprawiono "ponawianie wszystkich" nie działa dla nieudanej zmiany nazwy obiektu BLOB. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* Naprawiono "Cancel", podczas otwierania obiektu BLOB. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Naprawiono wiele problemów dotyczących pisowni i etykietek narzędzi w całym produkcie. Wiele podziękowania dla wszystkich użytkowników, którzy zgłosili te problemy! [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328), [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329), [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331), [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336), [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352), [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368), [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>Znane problemy

* Podczas pobierania obiektu BLOB, który nie jest AzCopy, MD5 dla dużych plików nie jest weryfikowany. Jest to spowodowane usterką w zestawie SDK magazynu. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* W przypadku korzystania z funkcji RBAC Eksplorator usługi Storage wymaga pewnych uprawnień do warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md) .
* Próba uzyskania dostępu do ADLS Gen2 obiektów blob, gdy serwer proxy może zakończyć się niepowodzeniem.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, po prostu Odśwież węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchomienie Eksplorator usługi Storage w systemie Linux wymaga wcześniejszego zainstalowania niektórych zależności. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) Eksplorator usługi Storage.

## <a name="version-181"></a>1.8.1 wersja
5/13/2019

### <a name="hotfixes"></a>Poprawki
* W niektórych przypadkach kliknięcie opcji "Załaduj więcej" na poziomie zasobu nie zwróci następnej strony zasobów. Ten problem został rozwiązany. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* W systemie Windows pobieranie AzCopy kończy się niepowodzeniem w przypadku pobrania pojedynczego pliku lub folderu, a nazwa pliku lub folderu miała znak, który był nieprawidłowy dla ścieżki systemu Windows. Ten problem został rozwiązany. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* W bardzo rzadkich przypadkach podczas przeprowadzania zmiany nazwy udziału plików lub zmiany nazwy w udziale plików, jeśli nie można wykonać operacji zmiany nazwy, lub jeśli usługa Storage nie mogła potwierdzić sukcesu kopii na platformie Azure, istnieje możliwość, że Eksplorator usługi Storage usunąć oryginalne pliki przed ukończeniem kopiowania. Ten problem został rozwiązany.

### <a name="new"></a>Nowy

* Zintegrowana wersja AzCopy została zaktualizowana do wersji 10.1.0.
* Za pomocą klawiszy Ctrl/CMD + R można teraz odświeżyć aktualnie fokusowy Edytor. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Wersja interfejsu API magazynu Azure Stack została zmieniona na 2017-04-17.
* Okno dialogowe Zarządzanie dostępem dla ADLS Gen2 będzie teraz przechowywać maskę w sposób podobny do innych narzędzi dostępu POSIX. Interfejs użytkownika wyświetli również ostrzeżenie, gdy zostanie wprowadzona zmiana, która powoduje, że uprawnienia użytkownika lub grupy przekraczają granice maski. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* W przypadku operacji przekazywania AzCopy flaga obliczania i ustawiania skrótu MD5 jest teraz włączona. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funkcje w wersji zapoznawczej

* Logowanie do przepływu kodu urządzenia jest teraz dostępne w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj logowania do przepływu kodu urządzenia". Zachęcamy użytkowników, którzy mieli problemy z pustymi oknami logowania, aby wypróbować tę funkcję, ponieważ może to być bardziej niezawodna forma logowania.
* Eksplorator usługi Storage zintegrowany z usługą AzCopy jest obecnie dostępny w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj AzCopy do ulepszonego przekazywania i pobierania obiektów BLOB". Transfery obiektów BLOB zakończonych za pomocą AzCopy powinny być szybsze i bardziej wydajne.

### <a name="fixes"></a>Poprawki

* W oknie dialogowym zasady dostępu nie będzie już ustawiana Data wygaśnięcia zasad dostępu do magazynu, które nie mają wygaśnięcia. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Wprowadzono pewne zmiany w oknie dialogowym generowanie sygnatury dostępu współdzielonego, aby upewnić się, że są one używane prawidłowo podczas generowania SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Podczas próby przekazania pliku wyrównanego bajtem o postaci innej niż 512 do obiektu BLOB stronicowania Eksplorator usługi Storage będzie teraz uwidaczniał bardziej istotny błąd. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Kopiowanie kontenera obiektów blob, które używało nazwy wyświetlanej, nie powiedzie się. Teraz jest używana rzeczywista nazwa kontenera obiektów BLOB. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Podjęto próbę wykonania pewnych akcji w folderze ADLS Gen2, w którym występują znaki Unicode w nazwie. Wszystkie akcje powinny teraz funkcjonować. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Znane problemy

* Podczas pobierania obiektu BLOB, który nie jest AzCopy, MD5 dla dużych plików nie jest weryfikowany. Jest to spowodowane usterką w zestawie SDK magazynu. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* W przypadku korzystania z funkcji RBAC Eksplorator usługi Storage wymaga pewnych uprawnień do warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md) .
* Próba uzyskania dostępu do ADLS Gen2 obiektów blob, gdy serwer proxy może zakończyć się niepowodzeniem.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, po prostu Odśwież węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchomienie Eksplorator usługi Storage w systemie Linux wymaga wcześniejszego zainstalowania niektórych zależności. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) Eksplorator usługi Storage.

## <a name="version-180"></a>1.8.0 wersja
1.05.2019

### <a name="new"></a>Nowy

* Zintegrowana wersja AzCopy została zaktualizowana do wersji 10.1.0.
* Za pomocą klawiszy Ctrl/CMD + R można teraz odświeżyć aktualnie fokusowy Edytor. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Wersja interfejsu API magazynu Azure Stack została zmieniona na 2017-04-17.
* Okno dialogowe Zarządzanie dostępem dla ADLS Gen2 będzie teraz przechowywać maskę w sposób podobny do innych narzędzi dostępu POSIX. Interfejs użytkownika wyświetli również ostrzeżenie, gdy zostanie wprowadzona zmiana, która powoduje, że uprawnienia użytkownika lub grupy przekraczają granice maski. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* W przypadku operacji przekazywania AzCopy flaga obliczania i ustawiania skrótu MD5 jest teraz włączona. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funkcje w wersji zapoznawczej

* Logowanie do przepływu kodu urządzenia jest teraz dostępne w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj logowania do przepływu kodu urządzenia". Zachęcamy użytkowników, którzy mieli problemy z pustymi oknami logowania, aby wypróbować tę funkcję, ponieważ może to być bardziej niezawodna forma logowania.
* Eksplorator usługi Storage zintegrowany z usługą AzCopy jest obecnie dostępny w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj AzCopy do ulepszonego przekazywania i pobierania obiektów BLOB". Transfery obiektów BLOB zakończonych za pomocą AzCopy powinny być szybsze i bardziej wydajne.

### <a name="fixes"></a>Poprawki

* W oknie dialogowym zasady dostępu nie będzie już ustawiana Data wygaśnięcia zasad dostępu do magazynu, które nie mają wygaśnięcia. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Wprowadzono pewne zmiany w oknie dialogowym generowanie sygnatury dostępu współdzielonego, aby upewnić się, że są one używane prawidłowo podczas generowania SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Podczas próby przekazania pliku wyrównanego bajtem o postaci innej niż 512 do obiektu BLOB stronicowania Eksplorator usługi Storage będzie teraz uwidaczniał bardziej istotny błąd. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Kopiowanie kontenera obiektów blob, które używało nazwy wyświetlanej, nie powiedzie się. Teraz jest używana rzeczywista nazwa kontenera obiektów BLOB. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Podjęto próbę wykonania pewnych akcji w folderze ADLS Gen2, w którym występują znaki Unicode w nazwie. Wszystkie akcje powinny teraz funkcjonować. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Znane problemy

* Podczas pobierania obiektu BLOB, który nie jest AzCopy, MD5 dla dużych plików nie jest weryfikowany. Jest to spowodowane usterką w zestawie SDK magazynu. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* W przypadku korzystania z funkcji RBAC Eksplorator usługi Storage wymaga pewnych uprawnień do warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md) .
* Próba uzyskania dostępu do ADLS Gen2 obiektów blob, gdy serwer proxy może zakończyć się niepowodzeniem.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, po prostu Odśwież węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchomienie Eksplorator usługi Storage w systemie Linux wymaga wcześniejszego zainstalowania niektórych zależności. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) Eksplorator usługi Storage.

## <a name="version-170"></a>Wersja 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Pobierz Eksplorator usługi Azure Storage 1.7.0
- [Eksplorator usługi Azure Storage 1.7.0 dla systemu Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Eksplorator usługi Azure Storage 1.7.0 dla komputerów Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Eksplorator usługi Azure Storage 1.7.0 dla systemu Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nowy

* Teraz można zmienić właściciela i grupę będącą właścicielem podczas zarządzania dostępem do kontenera ADLS Gen2, pliku lub folderu.
* W systemie Windows Aktualizowanie Eksplorator usługi Storage z poziomu produktu jest teraz instalacją przyrostową. Powinno to spowodować szybsze działanie aktualizacji. Jeśli wolisz czystą instalację, możesz pobrać [Instalatora](https://azure.microsoft.com/features/storage-explorer/) samodzielnie, a następnie zainstalować ręcznie. #1089

### <a name="preview-features"></a>Funkcje w wersji zapoznawczej

* Logowanie do przepływu kodu urządzenia jest teraz dostępne w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj logowania do przepływu kodu urządzenia". Zachęcamy użytkowników, którzy mieli problemy z pustymi oknami logowania, aby wypróbować tę funkcję, ponieważ może to być bardziej niezawodna forma logowania. #938
* Eksplorator usługi Storage zintegrowany z usługą AzCopy jest obecnie dostępny w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj AzCopy do ulepszonego przekazywania i pobierania obiektów BLOB". Transfery obiektów BLOB zakończonych za pomocą AzCopy powinny być szybsze i bardziej wydajne.

### <a name="fixes"></a>Poprawki

* Teraz można wybrać typ obiektu BLOB, który ma zostać przekazany, gdy AzCopy jest włączony. #1111
* Wcześniej, jeśli włączono statyczne witryny sieci Web dla konta magazynu ADLS Gen2, a następnie dołączono je przy użyciu nazwy i klucza, Eksplorator usługi Storage nie wykryła, że włączono hierarchiczną przestrzeń nazw. Ten problem został rozwiązany. #1081
* W Edytorze obiektów BLOB Sortuj według pozostałego okresu przechowywania lub jego stanu. Ten problem został rozwiązany. #1106
* Po 1.5.0, Eksplorator usługi Storage już nie czekać na zakończenie wykonywania kopii po stronie serwera przed zgłoszeniem sukcesu podczas zmiany nazwy lub kopiowania & wklejania. Ten problem został rozwiązany. #976
* W przypadku korzystania z funkcji eksperymentalnej AzCopy polecenie skopiowane po kliknięciu polecenia "Kopiuj polecenie do schowka" nigdy nie zawsze możliwy do uruchomienia. Teraz wszystkie polecenia, które są konieczne do ręcznego uruchomienia transferu, zostaną skopiowane. #1079
* Wcześniej ADLS Gen2 obiekty blob były niedostępne, jeśli jesteś za serwerem proxy. Zostało to spowodowane usterką w nowej bibliotece sieciowej używanej przez zestaw SDK magazynu. W programie 1.7.0 podjęto próbę wyeliminowania tego problemu, ale niektóre osoby mogą nadal widzieć problemy. Pełna poprawka zostanie wydana w przyszłej aktualizacji. #1090
* W 1.7.0, w oknie dialogowym Zapisz plik jest teraz poprawnie zapisywana ostatnio zapisana lokalizacja pliku. #16
* W panelu Właściwości warstwa SKU konta magazynu była wyświetlana jako rodzaj konta. Ten problem został rozwiązany. #654
* Czasami nie było możliwe przerwanie dzierżawy obiektu BLOB, nawet w przypadku poprawnego wprowadzenia nazwy obiektu BLOB. Ten problem został rozwiązany. #1070

### <a name="known-issues"></a>Znane problemy

* W przypadku korzystania z funkcji RBAC Eksplorator usługi Storage wymaga pewnych uprawnień do warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md) .
* Próba uzyskania dostępu do ADLS Gen2 obiektów blob, gdy serwer proxy może zakończyć się niepowodzeniem.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, po prostu Odśwież węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, po prostu Odśwież węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>Wersja 1.6.2
1/9/2019

### <a name="hotfixes"></a>Poprawki
* W 1.6.1 jednostki dodane do ADLS Gen2 list ACL według identyfikatora ObjectId, które nie były użytkownikami były zawsze dodawane jako grupy. Teraz tylko grupy są dodawane jako grupy, a jednostki, takie jak aplikacje przedsiębiorstwa andService podmioty zabezpieczeń, są dodawane jako użytkownicy. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Jeśli konto magazynu ADLS Gen2 nie zawierało kontenerów i zostało dołączone przy użyciu nazwy i klucza, Eksplorator usługi Storage nie wykryje, że konto magazynu zostało ADLS Gen2. Ten problem został rozwiązany. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* W programie 1.6.0 konflikty podczas kopiowania i wklejania nie monitują o rozwiązanie. Zamiast tego kopia powodująca konflikt może po prostu kończyć się niepowodzeniem. Teraz podczas pierwszego konfliktu zostanie wyświetlony monit o jego rozwiązanie. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Ze względu na ograniczenia interfejsu API wszystkie walidacje identyfikatora ObjectId w oknie dialogowym Zarządzanie dostępem zostały wyłączone. Sprawdzanie poprawności będzie teraz odbywać się tylko dla UPN użytkownika. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* W oknie dialogowym ADLS Gen2 zarządzanie dostępem nie można zmodyfikować uprawnień dla grupy. Ten problem został rozwiązany. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Dodano obsługę funkcji przeciągania i upuszczania w edytorze ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Właściwość adresu URL w oknie dialogowym właściwości dla ADLS Gen2 plików i folderów czasami brakuje znaku "/". Ten problem został rozwiązany. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Jeśli pobranie bieżących uprawnień do kontenera ADLS Gen2, pliku lub folderu nie powiedzie się, w dzienniku aktywności zostanie wyświetlony komunikat o błędzie. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Ścieżka tymczasowa utworzona na potrzeby otwierania plików została skrócona, aby zmniejszyć prawdopodobieństwo utworzenia ścieżki, która jest dłuższa niż MAX_PATH w systemie Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Okno dialogowe Połącz jest teraz wyświetlane, gdy nie ma żadnych zalogowanych użytkowników, a żadne zasoby nie zostały dołączone. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* W 1.6.0, zapisywanie właściwości dla obiektów blob i plików innych niż SNS spowoduje zakodowanie wartości każdej właściwości. Spowodowało to niepotrzebne kodowanie wartości, które zawierają tylko znaki ASCII. Teraz wartości będą kodowane tylko wtedy, gdy zawierają znaki inne niż ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Przekazanie folderu do kontenera obiektów blob, który nie jest SNS, zakończy się niepowodzeniem, jeśli użyto sygnatury dostępu współdzielonego i nie ma uprawnień do odczytu. Ten problem został rozwiązany. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Anulowanie transferu AzCopy nie zadziałało. Ten problem został rozwiązany. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy Wystąpił błąd podczas próby pobrania folderu z kontenera obiektów BLOB ADLS Gen2, jeśli jego nazwa zawiera spacje. Ten problem został rozwiązany. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Edytor CosmosDB został przerwany w 1.6.0. Został on rozwiązany. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nowy

* Teraz możesz używać Eksplorator usługi Storage, aby uzyskiwać dostęp do danych obiektów BLOB za pomocą [RBAC](./storage/common/storage-auth-aad-rbac-portal.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). Jeśli użytkownik jest zalogowany i Eksplorator usługi Storage nie może pobrać kluczy dla konta magazynu, zostanie użyty token OAuth do uwierzytelniania podczas korzystania z danych.
* Eksplorator usługi Storage teraz obsługuje konta magazynu ADLS Gen2. Gdy Eksplorator usługi Storage wykryje, że dla konta magazynu jest włączona hierarchiczna przestrzeń nazw, zobaczysz "(ADLS Gen2 wersja zapoznawcza)" obok nazwy konta magazynu. Eksplorator usługi Storage jest w stanie wykryć, czy hierarchiczna przestrzeń nazw jest włączona, gdy użytkownik jest zalogowany, lub czy konto magazynu zostało dołączone przy użyciu nazwy i klucza. W przypadku kont usługi ADLS Gen2 Storage można użyć Eksplorator usługi Storage do:
  * Tworzenie i usuwanie kontenerów
  * Zarządzanie właściwościami i uprawnieniami kontenera (po lewej stronie)
  * Wyświetlanie danych i nawigowanie w kontenerach
  * Utwórz nowe foldery
  * Przekazywanie, pobieranie, zmiana nazwy i usuwanie plików i folderów
  * Zarządzanie właściwościami i uprawnieniami plików i folderów (po prawej stronie).
    
    Inne typowe funkcje obiektów blob, takie jak usuwanie nietrwałe i migawki, nie są obecnie dostępne. Zarządzanie uprawnieniami jest również dostępne tylko po zalogowaniu się. Ponadto podczas pracy na koncie magazynu ADLS Gen2 Eksplorator usługi Storage będzie używać AzCopy do wszystkich operacji przekazywania i pobierania oraz domyślnie do korzystania z poświadczeń nazw i kluczy dla wszystkie operacje, jeśli są dostępne.
* Po silnych opiniach użytkowników przerwanie dzierżawy może być ponownie używane do jednoczesnego przerwania dzierżaw w wielu obiektach Blob.

### <a name="known-issues"></a>Znane problemy

* W przypadku pobierania z konta magazynu ADLS Gen2, jeśli jeden z transferowanych plików już istnieje, AzCopy czasami ulegnie awarii. Ten problem zostanie rozwiązany w nadchodzącej poprawce.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, po prostu Odśwież węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>1.6.1 wersja
12/18/2018

### <a name="hotfixes"></a>Poprawki
* Ze względu na ograniczenia interfejsu API wszystkie walidacje identyfikatora ObjectId w oknie dialogowym Zarządzanie dostępem zostały wyłączone. Sprawdzanie poprawności będzie teraz odbywać się tylko dla UPN użytkownika. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* W oknie dialogowym ADLS Gen2 zarządzanie dostępem nie można zmodyfikować uprawnień dla grupy. Ten problem został rozwiązany. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Dodano obsługę funkcji przeciągania i upuszczania w edytorze ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Właściwość adresu URL w oknie dialogowym właściwości dla ADLS Gen2 plików i folderów czasami brakuje znaku "/". Ten problem został rozwiązany. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Jeśli pobranie bieżących uprawnień do kontenera ADLS Gen2, pliku lub folderu nie powiedzie się, w dzienniku aktywności zostanie wyświetlony komunikat o błędzie. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Ścieżka tymczasowa utworzona na potrzeby otwierania plików została skrócona, aby zmniejszyć prawdopodobieństwo utworzenia ścieżki, która jest dłuższa niż MAX_PATH w systemie Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Okno dialogowe Połącz jest teraz wyświetlane, gdy nie ma żadnych zalogowanych użytkowników, a żadne zasoby nie zostały dołączone. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* W 1.6.0, zapisywanie właściwości dla obiektów blob i plików innych niż SNS spowoduje zakodowanie wartości każdej właściwości. Spowodowało to niepotrzebne kodowanie wartości, które zawierają tylko znaki ASCII. Teraz wartości będą kodowane tylko wtedy, gdy zawierają znaki inne niż ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Przekazanie folderu do kontenera obiektów blob, który nie jest SNS, zakończy się niepowodzeniem, jeśli użyto sygnatury dostępu współdzielonego i nie ma uprawnień do odczytu. Ten problem został rozwiązany. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Anulowanie transferu AzCopy nie zadziałało. Ten problem został rozwiązany. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy Wystąpił błąd podczas próby pobrania folderu z kontenera obiektów BLOB ADLS Gen2, jeśli jego nazwa zawiera spacje. Ten problem został rozwiązany. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Edytor CosmosDB został przerwany w 1.6.0. Został on rozwiązany. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nowy

* Teraz możesz używać Eksplorator usługi Storage, aby uzyskiwać dostęp do danych obiektów BLOB za pomocą [RBAC](./storage/common/storage-auth-aad-rbac-portal.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). Jeśli użytkownik jest zalogowany i Eksplorator usługi Storage nie może pobrać kluczy dla konta magazynu, zostanie użyty token OAuth do uwierzytelniania podczas korzystania z danych.
* Eksplorator usługi Storage teraz obsługuje konta magazynu ADLS Gen2. Gdy Eksplorator usługi Storage wykryje, że dla konta magazynu jest włączona hierarchiczna przestrzeń nazw, zobaczysz "(ADLS Gen2 wersja zapoznawcza)" obok nazwy konta magazynu. Eksplorator usługi Storage jest w stanie wykryć, czy hierarchiczna przestrzeń nazw jest włączona, gdy użytkownik jest zalogowany, lub czy konto magazynu zostało dołączone przy użyciu nazwy i klucza. W przypadku kont usługi ADLS Gen2 Storage można użyć Eksplorator usługi Storage do:
  * Tworzenie i usuwanie kontenerów
  * Zarządzanie właściwościami i uprawnieniami kontenera (po lewej stronie)
  * Wyświetlanie danych i nawigowanie w kontenerach
  * Utwórz nowe foldery
  * Przekazywanie, pobieranie, zmiana nazwy i usuwanie plików i folderów
  * Zarządzanie właściwościami i uprawnieniami plików i folderów (po prawej stronie).
    
    Inne typowe funkcje obiektów blob, takie jak usuwanie nietrwałe i migawki, nie są obecnie dostępne. Zarządzanie uprawnieniami jest również dostępne tylko po zalogowaniu się. Ponadto podczas pracy na koncie magazynu ADLS Gen2 Eksplorator usługi Storage będzie używać AzCopy do wszystkich operacji przekazywania i pobierania oraz domyślnie do korzystania z poświadczeń nazw i kluczy dla wszystkie operacje, jeśli są dostępne.
* Po silnych opiniach użytkowników przerwanie dzierżawy może być ponownie używane do jednoczesnego przerwania dzierżaw w wielu obiektach Blob.

### <a name="known-issues"></a>Znane problemy

* W przypadku pobierania z konta magazynu ADLS Gen2, jeśli jeden z transferowanych plików już istnieje, AzCopy czasami ulegnie awarii. Ten problem zostanie rozwiązany w nadchodzącej poprawce.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, po prostu Odśwież węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>1.6.0 wersja
2018-12-05

### <a name="new"></a>Nowy

* Teraz możesz używać Eksplorator usługi Storage, aby uzyskiwać dostęp do danych obiektów BLOB za pomocą [RBAC](./storage/common/storage-auth-aad-rbac-portal.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). Jeśli użytkownik jest zalogowany i Eksplorator usługi Storage nie może pobrać kluczy dla konta magazynu, zostanie użyty token OAuth do uwierzytelniania podczas korzystania z danych.
* Eksplorator usługi Storage teraz obsługuje konta magazynu ADLS Gen2. Gdy Eksplorator usługi Storage wykryje, że dla konta magazynu jest włączona hierarchiczna przestrzeń nazw, zobaczysz "(ADLS Gen2 wersja zapoznawcza)" obok nazwy konta magazynu. Eksplorator usługi Storage jest w stanie wykryć, czy hierarchiczna przestrzeń nazw jest włączona, gdy użytkownik jest zalogowany, lub czy konto magazynu zostało dołączone przy użyciu nazwy i klucza. W przypadku kont usługi ADLS Gen2 Storage można użyć Eksplorator usługi Storage do:
  * Tworzenie i usuwanie kontenerów
  * Zarządzanie właściwościami i uprawnieniami kontenera (po lewej stronie)
  * Wyświetlanie danych i nawigowanie w kontenerach
  * Utwórz nowe foldery
  * Przekazywanie, pobieranie, zmiana nazwy i usuwanie plików i folderów
  * Zarządzanie właściwościami i uprawnieniami plików i folderów (po prawej stronie).
    
    Inne typowe funkcje obiektów blob, takie jak usuwanie nietrwałe i migawki, nie są obecnie dostępne. Zarządzanie uprawnieniami jest również dostępne tylko po zalogowaniu się. Ponadto podczas pracy na koncie magazynu ADLS Gen2 Eksplorator usługi Storage będzie używać AzCopy do wszystkich operacji przekazywania i pobierania oraz domyślnie do korzystania z poświadczeń nazw i kluczy dla wszystkie operacje, jeśli są dostępne.
* Po silnych opiniach użytkowników przerwanie dzierżawy może być ponownie używane do jednoczesnego przerwania dzierżaw w wielu obiektach Blob.

### <a name="known-issues"></a>Znane problemy

* W przypadku pobierania z konta magazynu ADLS Gen2, jeśli jeden z transferowanych plików już istnieje, AzCopy czasami ulegnie awarii. Ten problem zostanie rozwiązany w nadchodzącej poprawce.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, po prostu Odśwież węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>1.5.0 wersja
10/29/2018

### <a name="new"></a>Nowy

* Teraz można używać [AzCopy v10 (wersja zapoznawcza)](https://github.com/Azure/azure-storage-azcopy) do przekazywania i pobierania obiektów BLOB. Aby włączyć tę funkcję, przejdź do menu "eksperymentalne", a następnie kliknij polecenie "Użyj AzCopy, aby uzyskać ulepszone przekazywanie i pobieranie obiektów BLOB". Gdy ta funkcja jest włączona, AzCopy będzie używana w następujących scenariuszach:
   * Przekazywanie folderów i plików do kontenerów obiektów BLOB za pośrednictwem paska narzędzi lub przeciągnij i upuść.
   * Pobieranie folderów i plików za pośrednictwem paska narzędzi lub menu kontekstowego.

* Ponadto w przypadku korzystania z AzCopy:
   * Można skopiować polecenie AzCopy użyte do przetransferowania do Schowka. Po prostu kliknij polecenie "Copy AzCopy polecenie to Clipboard" w dzienniku aktywności.
   * Po przekazaniu należy odświeżyć Edytor obiektów BLOB ręcznie.
   * Przekazywanie plików do dołączania obiektów BLOB nie jest obsługiwane, a pliki VHD zostaną przekazane jako stronicowe obiekty blob, a wszystkie inne pliki zostaną przekazane jako blokowe obiekty blob.
   * Błędy i konflikty występujące podczas przekazywania lub pobierania nie będą należeć do momentu zakończenia przekazywania lub pobrania.

Na koniec obsługa używania AzCopy z udziałami plików będzie w przyszłości dostępna.
* Eksplorator usługi Storage jest teraz używana wersja elektronów 2.0.11.
* Przerywanie dzierżaw można teraz wykonywać tylko na jednym obiekcie blob naraz. Ponadto należy wprowadzić nazwę obiektu BLOB, którego przerywasz. Ta zmiana została wprowadzona w celu zmniejszenia prawdopodobieństwa przypadkowego przerwania dzierżawy, szczególnie w przypadku maszyn wirtualnych. #394
* Jeśli kiedykolwiek napotykasz problemy z logowaniem, możesz teraz spróbować zresetować uwierzytelnianie. Przejdź do menu "pomoc" i kliknij przycisk "Resetuj", aby uzyskać dostęp do tej funkcji. #419

### <a name="fix"></a>Wprowadzanie poprawek

* Po silnych opiniach użytkowników domyślny węzeł emulatora został jeszcze włączony. Nadal można dodać dodatkowe połączenia emulatora za pośrednictwem okna dialogowego Połącz, ale jeśli emulator jest skonfigurowany do używania portów domyślnych, można również użyć węzła "porty domyślne" emulator * "w obszarze" konta dołączone/magazynu lokalnego &. #669
* Eksplorator usługi Storage nie pozwoli już ustawiać wartości metadanych obiektów blob, które mają wiodące lub końcowe spacje. #760
* Przycisk "Zaloguj się" był zawsze włączony na tych samych stronach okna dialogowego łączenia. Jest ona teraz wyłączona, gdy jest to konieczne. #761
* Szybki dostęp nie będzie już generował błędu w konsoli, gdy nie dodano żadnych elementów szybkiego dostępu.

### <a name="known-issues"></a>Znane problemy

* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, po prostu Odśwież węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>1.4.4 wersja
10/15/2018

### <a name="hotfixes"></a>Poprawki
* Wersja interfejsu API usługi Azure Resource Management została wycofana w celu odblokowania użytkowników programu Azure USA dla instytucji rządowych. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Ładowanie pokręteł teraz używa animacji CSS, aby zmniejszyć liczbę procesorów GPU używanych przez Eksplorator usługi Storage. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nowy
* Zewnętrzne załączniki zasobów, na przykład dla połączeń SAS i emulatorów, zostały znacząco udoskonalone. Co możesz teraz zrobić:
   * Dostosuj nazwę wyświetlaną zasobu, który jest dołączany. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Dołącz do wielu lokalnych emulatorów przy użyciu różnych portów. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Dodaj dołączone zasoby do paska Szybki dostęp. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Eksplorator usługi Storage obsługuje teraz usuwanie nietrwałe. Możesz:
   * Skonfiguruj zasady usuwania nietrwałego, klikając prawym przyciskiem myszy węzeł kontenery obiektów BLOB dla konta magazynu.
   * Wyświetl nietrwałe usunięte obiekty blob w Edytorze obiektów blob, wybierając pozycję "aktywne i usunięte obiekty blob" na liście rozwijanej obok paska nawigacyjnego.
   * Cofnij usunięcie usuniętych nietrwałych obiektów BLOB.

### <a name="fixes"></a>Poprawki
* Akcja "Konfiguruj ustawienia CORS" nie jest już dostępna na kontach Premium Storage, ponieważ konta Premium Storage nie obsługują mechanizmu CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Istnieje teraz właściwość sygnatury dostępu współdzielonego dla usług dołączonych SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akcja "Ustaw domyślną warstwę dostępu" jest teraz dostępna dla kont magazynu obiektów blob i GPV2, które zostały przypięte do paska Szybki dostęp. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Czasami Eksplorator usługi Storage nie będzie wyświetlać klasycznych kont magazynu. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Znane problemy
* W przypadku korzystania z emulatorów, takich jak emulator usługi Azure Storage lub azurite, konieczne będzie nasłuchiwanie połączeń na ich domyślnych portach. W przeciwnym razie Eksplorator usługi Storage nie będzie w stanie nawiązać połączenia z nimi.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, należy dodać komentarz dotyczący [tego problemu](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Wersja 1.4.3
10/11/2018

### <a name="hotfixes"></a>Poprawki
* Wersja interfejsu API usługi Azure Resource Management została wycofana w celu odblokowania użytkowników programu Azure USA dla instytucji rządowych. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Ładowanie pokręteł teraz używa animacji CSS, aby zmniejszyć liczbę procesorów GPU używanych przez Eksplorator usługi Storage. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nowy
* Zewnętrzne załączniki zasobów, na przykład dla połączeń SAS i emulatorów, zostały znacząco udoskonalone. Co możesz teraz zrobić:
   * Dostosuj nazwę wyświetlaną zasobu, który jest dołączany. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Dołącz do wielu lokalnych emulatorów przy użyciu różnych portów. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Dodaj dołączone zasoby do paska Szybki dostęp. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Eksplorator usługi Storage obsługuje teraz usuwanie nietrwałe. Możesz:
   * Skonfiguruj zasady usuwania nietrwałego, klikając prawym przyciskiem myszy węzeł kontenery obiektów BLOB dla konta magazynu.
   * Wyświetl nietrwałe usunięte obiekty blob w Edytorze obiektów blob, wybierając pozycję "aktywne i usunięte obiekty blob" na liście rozwijanej obok paska nawigacyjnego.
   * Cofnij usunięcie usuniętych nietrwałych obiektów BLOB.

### <a name="fixes"></a>Poprawki
* Akcja "Konfiguruj ustawienia CORS" nie jest już dostępna na kontach Premium Storage, ponieważ konta Premium Storage nie obsługują mechanizmu CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Istnieje teraz właściwość sygnatury dostępu współdzielonego dla usług dołączonych SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akcja "Ustaw domyślną warstwę dostępu" jest teraz dostępna dla kont magazynu obiektów blob i GPV2, które zostały przypięte do paska Szybki dostęp. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Czasami Eksplorator usługi Storage nie będzie wyświetlać klasycznych kont magazynu. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Znane problemy
* W przypadku korzystania z emulatorów, takich jak emulator usługi Azure Storage lub azurite, konieczne będzie nasłuchiwanie połączeń na ich domyślnych portach. W przeciwnym razie Eksplorator usługi Storage nie będzie w stanie nawiązać połączenia z nimi.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, należy dodać komentarz dotyczący [tego problemu](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Wersja 1.4.2
09/24/2018

### <a name="hotfixes"></a>Poprawki
* Zaktualizuj wersję interfejsu API usługi Azure Resource Management do wersji 2018-07-01 w celu dodania obsługi nowych rodzajów kont usługi Azure Storage. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Nowy
* Zewnętrzne załączniki zasobów, na przykład dla połączeń SAS i emulatorów, zostały znacząco udoskonalone. Co możesz teraz zrobić:
   * Dostosuj nazwę wyświetlaną zasobu, który jest dołączany. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Dołącz do wielu lokalnych emulatorów przy użyciu różnych portów. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Dodaj dołączone zasoby do paska Szybki dostęp. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Eksplorator usługi Storage obsługuje teraz usuwanie nietrwałe. Możesz:
   * Skonfiguruj zasady usuwania nietrwałego, klikając prawym przyciskiem myszy węzeł kontenery obiektów BLOB dla konta magazynu.
   * Wyświetl nietrwałe usunięte obiekty blob w Edytorze obiektów blob, wybierając pozycję "aktywne i usunięte obiekty blob" na liście rozwijanej obok paska nawigacyjnego.
   * Cofnij usunięcie usuniętych nietrwałych obiektów BLOB.

### <a name="fixes"></a>Poprawki
* Akcja "Konfiguruj ustawienia CORS" nie jest już dostępna na kontach Premium Storage, ponieważ konta Premium Storage nie obsługują mechanizmu CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Istnieje teraz właściwość sygnatury dostępu współdzielonego dla usług dołączonych SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akcja "Ustaw domyślną warstwę dostępu" jest teraz dostępna dla kont magazynu obiektów blob i GPV2, które zostały przypięte do paska Szybki dostęp. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Czasami Eksplorator usługi Storage nie będzie wyświetlać klasycznych kont magazynu. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Znane problemy
* W przypadku korzystania z emulatorów, takich jak emulator usługi Azure Storage lub azurite, konieczne będzie nasłuchiwanie połączeń na ich domyślnych portach. W przeciwnym razie Eksplorator usługi Storage nie będzie w stanie nawiązać połączenia z nimi.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, należy dodać komentarz dotyczący [tego problemu](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Wersja 1.4.1
08/28/2018

### <a name="hotfixes"></a>Poprawki
* Przy pierwszym uruchomieniu Eksplorator usługi Storage nie mógł wygenerować klucza używanego do szyfrowania poufnych danych. Może to powodować problemy podczas korzystania z szybkiego dostępu i dołączania zasobów. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Jeśli Twoje konto nie wymagało uwierzytelniania wieloskładnikowego dla dzierżawy głównej, ale w przypadku innych dzierżawców Eksplorator usługi Storage nie będzie można wyświetlić listy subskrypcji. Teraz po zalogowaniu się przy użyciu takiego konta Eksplorator usługi Storage zostanie poproszony o wprowadzenie poświadczeń i przeprowadzenie uwierzytelniania MFA. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Eksplorator usługi Storage nie mógł dołączyć zasobów z platformy Azure (Niemcy) i platformy Azure dla instytucji rządowych. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Jeśli zalogowano się do dwóch kont, które mają ten sam adres e-mail, Eksplorator usługi Storage czasami niepowodzenie wyświetlania zasobów w widoku drzewa. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Na wolniejszych maszynach z systemem Windows ekran powitalny może czasami trwać znaczną ilość czasu. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* Zostanie wyświetlone okno dialogowe Połącz, nawet jeśli zostały dołączone konta lub usługi. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Nowy
* Zewnętrzne załączniki zasobów, na przykład dla połączeń SAS i emulatorów, zostały znacząco udoskonalone. Co możesz teraz zrobić:
   * Dostosuj nazwę wyświetlaną zasobu, który jest dołączany. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Dołącz do wielu lokalnych emulatorów przy użyciu różnych portów. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Dodaj dołączone zasoby do paska Szybki dostęp. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Eksplorator usługi Storage obsługuje teraz usuwanie nietrwałe. Możesz:
   * Skonfiguruj zasady usuwania nietrwałego, klikając prawym przyciskiem myszy węzeł kontenery obiektów BLOB dla konta magazynu.
   * Wyświetl nietrwałe usunięte obiekty blob w Edytorze obiektów blob, wybierając pozycję "aktywne i usunięte obiekty blob" na liście rozwijanej obok paska nawigacyjnego.
   * Cofnij usunięcie usuniętych nietrwałych obiektów BLOB.

### <a name="fixes"></a>Poprawki
* Akcja "Konfiguruj ustawienia CORS" nie jest już dostępna na kontach Premium Storage, ponieważ konta Premium Storage nie obsługują mechanizmu CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Istnieje teraz właściwość sygnatury dostępu współdzielonego dla usług dołączonych SAS. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akcja "Ustaw domyślną warstwę dostępu" jest teraz dostępna dla kont magazynu obiektów blob i GPV2, które zostały przypięte do paska Szybki dostęp. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Czasami Eksplorator usługi Storage nie będzie wyświetlać klasycznych kont magazynu. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Znane problemy
* W przypadku korzystania z emulatorów, takich jak emulator usługi Azure Storage lub azurite, konieczne będzie nasłuchiwanie połączeń na ich domyślnych portach. W przeciwnym razie Eksplorator usługi Storage nie będzie w stanie nawiązać połączenia z nimi.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, należy dodać komentarz dotyczący [tego problemu](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>1.3.0 wersja
07/09/2018

### <a name="new"></a>Nowy
* Teraz jest obsługiwane uzyskiwanie dostępu do kontenerów $web używanych przez statyczne witryny sieci Web. Dzięki temu można łatwo przekazywać pliki i foldery używane przez witrynę sieci Web oraz zarządzać nimi. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* Pasek aplikacji na macOS został zreorganizowany. Zmiany obejmują menu plik, niektóre zmiany klawiszy skrótów i kilka nowych poleceń w menu aplikacji. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Punkt końcowy urzędu certyfikacji w celu zalogowania się do instytucji rządowych usługi Azure USA został zmieniony na https://login.microsoftonline.us/
* Ułatwienia dostępu: gdy czytnik ekranu jest aktywny, Nawigacja klawiatury działa teraz z tabelami używanymi do wyświetlania elementów po prawej stronie. Możesz użyć klawiszy strzałek, aby nawigować po wierszach i kolumnach, wprowadzić, aby wywoływać akcje domyślne, klucz menu kontekstowego, aby otworzyć menu kontekstowe dla elementu, a następnie przesunąć lub wybrać opcję wyboru wieloselect. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Poprawki
*  Na niektórych komputerach proces podrzędny miał dużo czasu na uruchomienie. W takim przypadku pojawia się błąd "proces podrzędny nie zostanie uruchomiony w odpowiednim czasie". Czas przeznaczony na rozpoczęcie procesu podrzędnego został zwiększony od 20 do 90 sekund. Jeśli nadal ma to wpływ na ten problem, Dodaj komentarz dotyczący połączonego problemu z usługą GitHub. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* W przypadku korzystania z sygnatury dostępu współdzielonego, które nie ma uprawnień do odczytu, nie można przekazać dużego obiektu BLOB. Logika przekazywania została zmodyfikowana w celu pracy w tym scenariuszu. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Ustawienie poziomu dostępu publicznego dla kontenera spowoduje usunięcie wszystkich zasad dostępu i odwrotnie. Teraz ustawienia poziomu dostępu publicznego i zasad dostępu są zachowywane podczas ustawiania jednego z tych dwóch. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* W oknie dialogowym właściwości została obcięta wartość "AccessTierChangeTime". Ten problem został rozwiązany. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* Brak prefiksu "Eksplorator usługi Microsoft Azure Storage-" w oknie dialogowym Tworzenie nowego katalogu. Ten problem został rozwiązany. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Ułatwienia dostępu: w oknie dialogowym Dodawanie jednostki trudno było nawigować podczas korzystania z VoiceOver. Wprowadzono ulepszenia. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Dostępność: kolor tła przycisku Zwiń/rozwiń dla okienka Akcje i właściwości jest niespójny z podobnymi kontrolkami interfejsu użytkownika w duży kontrast czarnym motywie. Kolor został zmieniony. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Ułatwienia dostępu: w duży kontrast czarnym motywie style fokusu dla przycisku "X" w oknie dialogowym właściwości nie były widoczne. Ten problem został rozwiązany. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Ułatwienia dostępu: na kartach akcje i właściwości brakuje kilku wartości Aria, co spowodowało subpare działanie czytnika ekranu. Brakujące wartości Aria zostały już dodane. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Dostępność: zwinięte węzły drzewa po lewej stronie nie uzyskały Aria wartości false. Ten problem został rozwiązany. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Znane problemy
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, po prostu Odśwież węzeł grupy. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/Microsoft/AzureStorageExplorer/issues/537) .
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, należy dodać komentarz dotyczący [tego problemu](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Azure Stack nie obsługuje następujących funkcji i próba ich użycia podczas pracy z Azure Stack może spowodować nieoczekiwane błędy:
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>1.2.0 wersja
06/12/2018

### <a name="new"></a>Nowy
* Jeśli usługa Eksplorator usługi Storage nie może załadować subskrypcji tylko z podzbioru dzierżawców, wszystkie pomyślnie załadowane subskrypcje będą wyświetlane wraz z komunikatem o błędzie przeznaczonym dla dzierżawców zakończonych niepowodzeniem. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* W systemie Windows po udostępnieniu aktualizacji można teraz wybrać opcję "Aktualizuj przy zamykaniu". Po wybraniu tej opcji Instalator aktualizacji zostanie uruchomiony po zamknięciu Eksplorator usługi Storage. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* Migawka przywracania została dodana do menu kontekstowego edytora udziału plików podczas wyświetlania migawki udziału plików. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Przycisk czyszczenia kolejki jest teraz zawsze włączony. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Włączono ponowną obsługę logowania do usług ADFS Azure Stack. Wymagany jest Azure Stack w wersji 1804 lub nowszej. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Poprawki
* W przypadku wyświetlenia migawek dla udziału plików, którego nazwa była prefiksem innego udziału plików na tym samym koncie magazynu, zostaną również wyświetlone migawki dla tego udziału plików. Ten problem został rozwiązany. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Po dołączeniu za pośrednictwem sygnatury dostępu współdzielonego, przywrócenie pliku z migawki udziału plików spowoduje wystąpienie błędu. Ten problem został rozwiązany. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Podczas wyświetlania migawek dla obiektu BLOB akcja podwyższania poziomu migawek została włączona po wybraniu podstawowego obiektu BLOB i pojedynczej migawki. Akcja jest teraz włączona tylko wtedy, gdy wybrano jedną migawkę. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Jeśli pojedyncze zadanie (takie jak pobieranie obiektu BLOB) zostało uruchomione i nie powiodło się, nie będzie automatycznie ponawiać próby do momentu rozpoczęcia kolejnego zadania tego samego typu. Wszystkie zadania powinny teraz być autoponawiane, niezależnie od liczby zadań, które zostały dodane do kolejki.
* Edytory otwarte dla nowo utworzonych kontenerów obiektów BLOB w GPV2, a konta Blob Storage nie miały kolumny warstwy dostępu. Ten problem został rozwiązany. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Kolumna warstwy dostępu prawdopodobnie nie zostanie wyświetlona, gdy konto magazynu lub kontener obiektów BLOB zostało dołączone za pośrednictwem SAS. Kolumna będzie teraz zawsze wyświetlana, ale z pustą wartością, jeśli nie ma zestawu warstwy dostępu. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Ustawienie warstwy dostępu nowo przekazanego obiektu BLOB bloku zostało wyłączone. Ten problem został rozwiązany. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Jeśli przycisk "Pozostaw otwartą kartę" został wywołany za pomocą klawiatury, fokus klawiatury zostanie utracony. Teraz fokus zostanie przeniesiony na kartę, która była otwarta. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* W przypadku zapytania w Konstruktor zapytań VoiceOver nie mógł uzyskać użytecznego opisu bieżącego operatora. Jest teraz bardziej opisowa. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* Linki stronicowania dla różnych edytorów nie były opisami. Zostały zmienione tak, aby były bardziej opisowe. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* W oknie dialogowym Dodawanie jednostki VoiceOver nie zapowiedź do kolumny, do której należy element wejściowy. Nazwa bieżącej kolumny jest teraz uwzględniona w opisie elementu. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Przyciski radiowe i pola wyboru nie miały widocznego obramowania w przypadku fokusu. Ten problem został rozwiązany. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Znane problemy
* W przypadku korzystania z emulatorów, takich jak emulator usługi Azure Storage lub azurite, konieczne będzie nasłuchiwanie połączeń na ich domyślnych portach. W przeciwnym razie Eksplorator usługi Storage nie będzie w stanie nawiązać połączenia z nimi.
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, należy dodać komentarz dotyczący [tego problemu](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>1.1.0 wersja
05/09/2018

### <a name="new"></a>Nowy
* Eksplorator usługi Storage obsługuje teraz korzystanie z azurite. Uwaga: połączenie z usługą azurite jest stałe do domyślnych punktów końcowych deweloperskich.
* Eksplorator usługi Storage teraz obsługuje warstwy dostępu tylko dla kont obiektów blob i GPV2. Więcej informacji na temat warstw dostępu [znajdziesz tutaj](./storage/blobs/storage-blob-storage-tiers.md).
* Godzina rozpoczęcia nie jest już wymagana podczas generowania sygnatury dostępu współdzielonego.

### <a name="fixes"></a>Poprawki
* Pobieranie subskrypcji dla kont rządowych Stanów Zjednoczonych zostało przerwane. Ten problem został rozwiązany. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* Czas wygaśnięcia zasad dostępu nie został poprawnie zapisany. Ten problem został rozwiązany. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Podczas generowania adresu URL sygnatury dostępu współdzielonego dla elementu w kontenerze nazwa elementu nie została dołączona do adresu URL. Ten problem został rozwiązany. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Podczas tworzenia sygnatury dostępu współdzielonego czasy wygaśnięcia, które znajdują się w przeszłości, czasami jest wartością domyślną. Było to spowodowane Eksplorator usługi Storage przy użyciu wartości domyślnych czasu ostatniego użycia i wygaśnięcia. Teraz za każdym razem, gdy otworzysz okno dialogowe SAS, generowany jest nowy zestaw wartości domyślnych. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Podczas kopiowania między kontami magazynu jest generowane 24-godzinny dysk SAS. Jeśli kopia była Ostatnia niż 24 godziny, kopia nie powiedzie się. Zwiększono sygnatury dostępu współdzielonego do ostatniego 1 tygodnia, aby zmniejszyć prawdopodobieństwo niepowodzenia kopiowania z powodu wygaśnięcia sygnatury dostępu współdzielonego. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* W przypadku niektórych działań kliknięcie pozycji "Anuluj" nie zawsze będzie działało. Ten problem został rozwiązany. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* W przypadku niektórych działań prędkość transferu była nieprawidłowa. Ten problem został rozwiązany. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* Pisownia "Previous" w menu Widok jest niepoprawna. Jest teraz poprawnie wpisany. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Ostatnia strona Instalatora Windows ma przycisk "dalej". Został on zmieniony na przycisk "Zakończ". [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* Fokus karty nie był widoczny dla przycisków w oknach dialogowych, gdy jest używany kolor HC. Jest ona teraz widoczna. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* Wielkość liter "autorozwiązanie" dla akcji w dzienniku aktywności była nieprawidłowa. Jest ona teraz poprawna. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Podczas usuwania jednostki z tabeli okno dialogowe z monitem o potwierdzenie wyświetli ikonę błędu. Okno dialogowe używa teraz ikony ostrzeżenia. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Znane problemy
* Jeśli używasz programu VS for Mac i kiedykolwiek utworzysz niestandardową konfigurację usługi AAD, możesz nie być w stanie zalogować się. Aby obejść ten problem, Usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, należy dodać komentarz dotyczący [tego problemu](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite jeszcze nie zaimplementowano wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z azurite na potrzeby magazynu deweloperskiego.
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Usterka została naprawiona, ale jeszcze nie została zintegrowana z elektronem.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Wersja 1.0.0
04/16/2018

### <a name="new"></a>Nowy
* Rozszerzone uwierzytelnianie, które umożliwia Eksplorator usługi Storage korzystania z tego samego magazynu kont co program Visual Studio 2017. Aby użyć tej funkcji, należy ponownie zalogować się do kont i ponownie ustawić odfiltrowane subskrypcje.
* W przypadku kont Azure Stack, które są obsługiwane przez usługi AAD, Eksplorator usługi Storage będzie teraz pobierać Azure Stack subskrypcje, gdy zostanie włączony element "Target Azure Stack". Nie trzeba już tworzyć niestandardowego środowiska logowania.
* Dodano kilka skrótów, które umożliwiają szybszą nawigację. Obejmują one przełączanie różnych paneli i przechodzenie między edytorami. Aby uzyskać szczegółowe informacje, zobacz menu Widok.
* Eksplorator usługi Storage Opinie teraz w serwisie GitHub. Aby uzyskać dostęp do strony problemów, kliknij przycisk opinii w lewym dolnym rogu lub przejdź do [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues) . Możesz korzystać z sugestii, zgłaszać problemy, zadawać pytania lub pozostawać dowolną inną formę opinii.
* Jeśli używasz programu do problemów z certyfikatem TLS/SSL i nie można znaleźć poprawnego certyfikatu, możesz teraz uruchomić Eksplorator usługi Storage z wiersza polecenia z `--ignore-certificate-errors` flagą. Po uruchomieniu z tą flagą Eksplorator usługi Storage zignoruje błędy certyfikatu TLS/SSL.
* W menu kontekstowym dla elementów BLOB i plików istnieje teraz opcja "Download".
* Ulepszona obsługa ułatwień dostępu i czytnika ekranu. Jeśli korzystasz z funkcji ułatwień dostępu, zapoznaj się z [dokumentacją ułatwień dostępu](./vs-azure-tools-storage-explorer-accessibility.md) , aby uzyskać więcej informacji.
* Eksplorator usługi Storage używa teraz elektronów 1.8.3

### <a name="breaking-changes"></a>Zmiany powodujące niezgodność
* Eksplorator usługi Storage przełączono do nowej biblioteki uwierzytelniania. W ramach przełącznika do biblioteki należy ponownie zalogować się do swoich kont i ponownie ustawić odfiltrowane subskrypcje.
* Metoda używana do szyfrowania poufnych danych została zmieniona. Może to spowodować konieczność ponownego dołączenia niektórych elementów szybkiego dostępu i/lub niektórych dołączonych zasobów.

### <a name="fixes"></a>Poprawki
* Niektórzy użytkownicy znajdujący się za serwerami proxy mogą przeciążać lub pobierać obiekty blob grup, które zostały przerwane przez komunikat o błędzie "nie można rozpoznać". Ten problem został rozwiązany.
* Jeśli podczas korzystania z linku bezpośredniego jest wymagana logowanie, kliknięcie monitu "Logowanie" spowoduje wyskakujące puste okno dialogowe. Ten problem został rozwiązany.
* W systemie Linux jeśli nie można uruchomić Eksplorator usługi Storage z powodu awarii procesu procesora GPU, użytkownik zostanie poinformowany o awarii, powiadomieniu o użyciu przełącznika "--Disable-GPU", a następnie Eksplorator usługi Storage zostanie automatycznie uruchomione ponownie z włączonym przełącznikiem.
* Nieprawidłowe zasady dostępu były trudne do zidentyfikowania w oknie dialogowym zasady dostępu. Nieprawidłowe identyfikatory zasad dostępu są teraz podane na czerwono w celu zapewnienia większej widoczności.
* Dziennik aktywności czasami ma duże obszary odstępu między różnymi częściami działania. Ten problem został rozwiązany.
* W edytorze zapytań tabeli, jeśli pozostawiono klauzulę sygnatury czasowej w nieprawidłowym stanie, a następnie podjęto próbę zmodyfikowania kolejnej klauzuli, Edytor zostanie zamrożony. Edytor będzie teraz przywracał klauzulę timestamp do ostatniego prawidłowego stanu, gdy zostanie wykryta zmiana w innej klauzuli.
* Jeśli wstrzymasz w trakcie wpisywania zapytania wyszukiwania w widoku drzewa, wyszukiwanie zostanie rozpoczęte i fokus zostanie skradziony z pola tekstowego. Teraz musisz jawnie rozpocząć wyszukiwanie, naciskając klawisz ENTER lub klikając przycisk Rozpocznij wyszukiwanie.
* Polecenie "Pobierz sygnaturę dostępu współdzielonego" może być czasami wyłączone po kliknięciu prawym przyciskiem myszy pliku w udziale plików. Ten problem został rozwiązany.
* Jeśli węzeł drzewa zasobów z fokusem został odfiltrowany podczas wyszukiwania, nie można nawiązać karty w drzewie zasobów i użyć klawiszy strzałek, aby nawigować po drzewie zasobów. Teraz, jeśli węzeł drzewa zasobów skoncentrowanych jest ukryty, pierwszy węzeł drzewa zasobów zostanie automatycznie skoncentrowany.
* Dodatkowy separator mógłby być czasami widoczny na pasku narzędzi edytora. Ten problem został rozwiązany.
* Pole tekstowe do stron nadrzędnych może czasami przepełnić. Ten problem został rozwiązany.
* Edytory obiektów blob i udziałów plików czasami są ciągle odświeżane podczas przekazywania wielu plików jednocześnie. Ten problem został rozwiązany.
* Funkcja "statystyki folderów" nie miała zastosowania w widoku zarządzania migawkami udziałów plików. Zostało ono już wyłączone.
* W systemie Linux menu plik nie pojawiło się. Ten problem został rozwiązany.
* Podczas przekazywania folderu do udziału plików domyślnie tylko zawartość folderu została przekazana. Teraz domyślne zachowanie polega na przekazaniu zawartości folderu do zgodnego folderu w udziale plików.
* Kolejność przycisków w kilku oknach dialogowych została odwrócona. Ten problem został rozwiązany.
* Różne poprawki związane z zabezpieczeniami.

### <a name="known-issues"></a>Znane problemy
* W rzadkich przypadkach fokus drzewa może ulec zawieszeniu na szybki dostęp. Aby odkleić fokus, można odświeżyć wszystko.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>0.9.6 wersja
02/28/2018

### <a name="fixes"></a>Poprawki
* Problem uniemożliwił wystawienie oczekiwanych obiektów BLOB/plików w edytorze. Ten problem został rozwiązany.
* Problem spowodował przełączenie się między widokami migawek w celu wyświetlenia niepoprawnych elementów. Ten problem został rozwiązany.

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage nie obsługuje kont usług AD FS.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Panel Ustawienia konta może wskazywać, że należy ponownie wprowadzić poświadczenia, aby odfiltrować subskrypcje.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>0.9.5 wersja
02/06/2018

### <a name="new"></a>Nowy

* Obsługa migawek udziałów plików:
    * Twórz migawki dla udziałów plików i zarządzaj nimi.
    * Łatwo przełączaj widoki między migawkami udziałów plików podczas eksplorowania.
    * Przywróć poprzednie wersje plików.
* Obsługa podglądu Azure Data Lake Store:
    * Połącz się z zasobami ADLS na wielu kontach.
    * Łączenie i udostępnianie zasobów ADLS za pomocą identyfikatorów URI ADL.
    * Wykonaj cyklicznie podstawowe operacje na plikach/folderach.
    * Przypnij poszczególne foldery do paska Szybki dostęp.
    * Wyświetl statystyki folderów.

### <a name="fixes"></a>Poprawki
* Ulepszenia wydajności uruchomienia.
* Różne poprawki.

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage nie obsługuje kont usług AD FS.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Panel Ustawienia konta może wskazywać, że należy ponownie wprowadzić poświadczenia, aby odfiltrować subskrypcje.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Wersja 0.9.4 i 0.9.3
01/21/2018

### <a name="new"></a>Nowy
* Istniejące okno Eksplorator usługi Storage zostanie ponownie użyte, gdy:
    * Otwieranie bezpośrednich linków wygenerowanych w Eksplorator usługi Storage.
    * Otwieranie Eksplorator usługi Storage z poziomu portalu.
    * Otwieranie Eksplorator usługi Storage z rozszerzenia usługi Azure Storage VS Code (wkrótce).
* Dodano możliwość otwierania nowego okna Eksplorator usługi Storage w programie Eksplorator usługi Storage.
    * W przypadku systemu Windows istnieje opcja "nowe okno" w menu plik i w menu kontekstowym paska zadań.
    * Dla komputerów Mac istnieje opcja "nowe okno" w menu aplikacji.

### <a name="fixes"></a>Poprawki
* Rozwiązano problem z zabezpieczeniami. Najpierw przeprowadź uaktualnienie do 0.9.4.
* Stare działania nie zostały odpowiednio oczyszczone. Miało to na celu wydajność długotrwałych zadań. Są one teraz oczyszczane poprawnie.
* Działania dotyczące dużej liczby plików i katalogów sporadycznie spowodują, że Eksplorator usługi Storage zamrozić. Żądania do platformy Azure dla udziałów plików są teraz ograniczane w celu ograniczenia użycia zasobów systemowych.

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage nie obsługuje kont usług AD FS.
* Klawisze skrótów dla "View Explorer" i "Wyświetl konto zarządzania" powinny mieć wartość Ctrl/Cmd + Shift + E i Ctrl/Cmd + Shift + A.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Panel Ustawienia konta może wskazywać, że należy ponownie wprowadzić poświadczenia, aby odfiltrować subskrypcje.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer --disable-gpu
    ```

* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>0.9.2 wersja
11/01/2017

### <a name="hotfixes"></a>Poprawki
* Podczas edytowania wartości EDM. DateTime dla jednostek tabeli w zależności od lokalnej strefy czasowej było możliwe nieoczekiwane zmiany danych. Edytor używa teraz zwykłego pola tekstowego, dając precyzyjną, spójną kontrolę nad wartościami EDM. DateTime.
* Przekazanie/pobranie grupy obiektów BLOB po dołączeniu do nazwy i klucza nie zostanie rozpoczęte. Ten problem został rozwiązany.
* Wcześniej Eksplorator usługi Storage będzie monitować o ponowne uwierzytelnienie starego konta, jeśli wybrano co najmniej jedną subskrypcję konta. Teraz Eksplorator usługi Storage wyświetli monit, nawet jeśli konto jest w pełni odfiltrowane.
* Domena punktów końcowych dla instytucji rządowych usługi Azure USA była nieprawidłowa. Został naprawiony.
* Przycisk Zastosuj na panelu Zarządzaj kontami czasami trudno go kliknąć. Nie powinno to już być możliwe.

### <a name="new"></a>Nowy
* Obsługa podglądu Azure Cosmos DB:
    * [Dokumentacja online](./cosmos-db/storage-explorer.md)
    * Tworzenie baz danych i kolekcji
    * Manipulowanie danymi
    * Zapytania, tworzenie lub usuwanie dokumentów
    * Aktualizuj procedury składowane, funkcje zdefiniowane przez użytkownika lub wyzwalacze
    * Używanie parametrów połączenia do łączenia się z bazami danych i zarządzania nimi
* Ulepszono wydajność przekazywania/pobierania wielu małych obiektów BLOB.
* Dodano akcję "Ponów wszystko", jeśli występują błędy w grupie lub grupie plików BLOB przekazywania obiektów BLOB.
* Eksplorator usługi Storage będzie teraz wstrzymywać iteracje podczas przekazywania/pobierania obiektów blob, jeśli wykryje, że połączenie sieciowe zostało utracone. Następnie można wznowić iterację po ponownym nawiązaniu połączenia sieciowego.
* Dodano możliwość "Zamknij wszystkie", "Zamknij inne" i "Zamknij" z menu kontekstowego.
* Eksplorator usługi Storage teraz używa natywnych okien dialogowych i natywnych menu kontekstowych.
* Eksplorator usługi Storage jest teraz bardziej dostępny. Wprowadzono następujące ulepszenia:
    * Ulepszona obsługa czytnika ekranu dla NVDA w systemie Windows i dla VoiceOver na komputerze Mac
    * Ulepszone aplikacje z dużym kontrastem
    * Poprawki dotyczące kart i fokus klawiatury

### <a name="fixes"></a>Poprawki
* Jeśli podjęto próbę otwarcia lub pobrania obiektu BLOB z nieprawidłową nazwą pliku systemu Windows, operacja zakończy się niepowodzeniem. Eksplorator usługi Storage wykryje teraz, czy nazwa obiektu BLOB jest nieprawidłowa i pyta, czy chcesz je kodować czy pominąć obiekt BLOB. Eksplorator usługi Storage wykryje również, czy nazwa pliku jest zaszyfrowana, i poproszony o to, aby je zdekodować przed przekazaniem.
* Podczas przekazywania obiektów BLOB edytor dla docelowego kontenera obiektów BLOB prawdopodobnie nie zostanie prawidłowo odświeżony. Ten problem został rozwiązany.
* Obsługa kilku form parametrów połączenia i identyfikatorów URI sygnatury dostępu współdzielonego uległa pogorszeniu. Wszystkie znane problemy zostały rozwiązane, ale w przypadku wystąpienia dalszych problemów Wyślij opinię.
* Powiadomienie o aktualizacji zostało przerwane dla niektórych użytkowników w 0.9.0. Ten problem został rozwiązany, a dla tych, których dotyczy usterka, można ręcznie pobrać najnowszą [wersję Eksplorator usługi Storage tego](https://azure.microsoft.com/features/storage-explorer/)problemu.

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage nie obsługuje kont usług AD FS.
* Klawisze skrótów dla "View Explorer" i "Wyświetl konto zarządzania" powinny mieć wartość Ctrl/Cmd + Shift + E i Ctrl/Cmd + Shift + A.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Panel Ustawienia konta może wskazywać, że należy ponownie wprowadzić poświadczenia, aby odfiltrować subskrypcje.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer --disable-gpu
    ```

* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Wersja od 0.9.1 i 0.9.0
10/20/2017
### <a name="new"></a>Nowy
* Obsługa podglądu Azure Cosmos DB:
    * [Dokumentacja online](./cosmos-db/storage-explorer.md)
    * Tworzenie baz danych i kolekcji
    * Manipulowanie danymi
    * Zapytania, tworzenie lub usuwanie dokumentów
    * Aktualizuj procedury składowane, funkcje zdefiniowane przez użytkownika lub wyzwalacze
    * Używanie parametrów połączenia do łączenia się z bazami danych i zarządzania nimi
* Ulepszono wydajność przekazywania/pobierania wielu małych obiektów BLOB.
* Dodano akcję "Ponów wszystko", jeśli występują błędy w grupie lub grupie plików BLOB przekazywania obiektów BLOB.
* Eksplorator usługi Storage będzie teraz wstrzymywać iteracje podczas przekazywania/pobierania obiektów blob, jeśli wykryje, że połączenie sieciowe zostało utracone. Następnie można wznowić iterację po ponownym nawiązaniu połączenia sieciowego.
* Dodano możliwość "Zamknij wszystkie", "Zamknij inne" i "Zamknij" z menu kontekstowego.
* Eksplorator usługi Storage teraz używa natywnych okien dialogowych i natywnych menu kontekstowych.
* Eksplorator usługi Storage jest teraz bardziej dostępny. Wprowadzono następujące ulepszenia:
    * Ulepszona obsługa czytnika ekranu dla NVDA w systemie Windows i dla VoiceOver na komputerze Mac
    * Ulepszone aplikacje z dużym kontrastem
    * Poprawki dotyczące kart i fokus klawiatury

### <a name="fixes"></a>Poprawki
* Jeśli podjęto próbę otwarcia lub pobrania obiektu BLOB z nieprawidłową nazwą pliku systemu Windows, operacja zakończy się niepowodzeniem. Eksplorator usługi Storage wykryje teraz, czy nazwa obiektu BLOB jest nieprawidłowa i pyta, czy chcesz je kodować czy pominąć obiekt BLOB. Eksplorator usługi Storage wykryje również, czy nazwa pliku jest zaszyfrowana, i poproszony o to, aby je zdekodować przed przekazaniem.
* Podczas przekazywania obiektów BLOB edytor dla docelowego kontenera obiektów BLOB prawdopodobnie nie zostanie prawidłowo odświeżony. Ten problem został rozwiązany.
* Obsługa kilku form parametrów połączenia i identyfikatorów URI sygnatury dostępu współdzielonego uległa pogorszeniu. Wszystkie znane problemy zostały rozwiązane, ale w przypadku wystąpienia dalszych problemów Wyślij opinię.
* Powiadomienie o aktualizacji zostało przerwane dla niektórych użytkowników w 0.9.0. Ten problem został rozwiązany, a dla tych, których dotyczy usterka, można ręcznie pobrać najnowszą wersję Eksplorator usługi Storage [tutaj](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage nie obsługuje kont usług AD FS.
* Klawisze skrótów dla "View Explorer" i "Wyświetl konto zarządzania" powinny mieć wartość Ctrl/Cmd + Shift + E i Ctrl/Cmd + Shift + A.
* Podczas określania wartości docelowej Azure Stack przekazanie niektórych plików jako obiektów BLOB może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Panel Ustawienia konta może wskazywać, że należy ponownie wprowadzić poświadczenia, aby odfiltrować subskrypcje.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* Powłoka elektronów używana przez Eksplorator usługi Storage ma problemy z pewnym przyspieszeniem sprzętowym procesora GPU (procesorem graficznym). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając `--disable-gpu` przełącznik:

    ```
    ./StorageExplorer --disable-gpu
    ```

* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>0.8.16 wersja
8/21/2017

### <a name="new"></a>Nowy
* Po otwarciu obiektu BLOB Eksplorator usługi Storage wyświetli monit o przekazanie pobranego pliku w przypadku wykrycia zmiany
* Ulepszone środowisko logowania Azure Stack
* Ulepszona wydajność przekazywania/pobierania wielu małych plików w tym samym czasie


### <a name="fixes"></a>Poprawki
* W przypadku niektórych typów obiektów BLOB wybranie opcji "Zastąp" podczas konfliktu przekazywania może czasami spowodować ponowne uruchomienie przekazywania.
* W wersji 0.8.15, operacje przekazywania mogłyby się zdarzyć na 99%.
* W przypadku przekazywania plików do udziału plików, jeśli wybrano opcję przekazania do katalogu, który jeszcze nie istnieje, przekazywanie zakończy się niepowodzeniem.
* Eksplorator usługi Storage nieprawidłowo generować sygnatury czasowe dla sygnatur dostępu współdzielonego i zapytań tabel.


### <a name="known-issues"></a>Znane problemy
* Użycie nazwy i parametrów połączenia klucza nie działa obecnie. Zostanie on rozwiązany w następnej wersji. Do tego celu można użyć dołączenia z nazwą i kluczem.
* Jeśli spróbujesz otworzyć plik z nieprawidłową nazwą pliku systemu Windows, pobranie spowoduje błąd nie znaleziono pliku.
* Po kliknięciu przycisku "Anuluj" w zadaniu może upłynąć trochę czasu, zanim zadanie zostanie anulowane. Jest to ograniczenie biblioteki węzłów usługi Azure Storage.
* Po ukończeniu przekazywania obiektów BLOB karta, która zainicjowała przekazywanie, jest odświeżana. Jest to zmiana w stosunku do poprzedniego zachowania i spowoduje to przełączenie się z powrotem do katalogu głównego kontenera, w którym się znajdujesz.
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji.
* Panel Ustawienia konta może wskazywać, że należy ponownie wprowadzić poświadczenia, aby odfiltrować subskrypcje.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* W przypadku użytkowników korzystających z usługi Ubuntu 14,04 należy zapewnić aktualność programu w zatoce — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* W przypadku użytkowników z systemem Ubuntu 17,04 należy zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie uruchamiając ponownie maszynę:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>0.8.14 wersja
06/22/2017

### <a name="new"></a>Nowy

* Zaktualizowano wersję elektronów do 1.7.2 w celu skorzystania z kilku krytycznych aktualizacji zabezpieczeń
* Teraz możesz szybko uzyskać dostęp do przewodnika rozwiązywania problemów online z menu Pomoc
* [Przewodnik][2] rozwiązywania problemów Eksplorator usługi Storage
* [Instrukcje][3] dotyczące nawiązywania połączenia z subskrypcją Azure Stack

### <a name="known-issues"></a>Znane problemy

* Przyciski w oknie dialogowym potwierdzania usuwania folderu nie są rejestrowane przy użyciu myszy w systemie Linux. Obejście polega na użyciu klawisza ENTER
* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji
* Jednoczesne przekazanie więcej niż 3 grup obiektów blob lub plików może spowodować błędy
* Panel Ustawienia konta może wskazywać, że należy ponownie wprowadzić poświadczenia, aby filtrować subskrypcje
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Chociaż Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* Instalacja programu Ubuntu 14,04 wymaga zaktualizowanej lub uaktualnionej wersji systemu

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>0.8.13 wersja
05/12/2017

#### <a name="new"></a>Nowy

* [Przewodnik][2] rozwiązywania problemów Eksplorator usługi Storage
* [Instrukcje][3] dotyczące nawiązywania połączenia z subskrypcją Azure Stack

#### <a name="fixes"></a>Poprawki

* Naprawiono: przekazywanie plików miało wysoką szansę spowodowania błędu braku pamięci
* Rozwiązano: Możesz teraz zalogować się przy użyciu numeru PIN/karty inteligentnej
* Naprawiono: otwarta w portalu teraz współpracuje z platformą Azure Chiny 21Vianet, platformą Azure (Niemcy), platformą Azure dla instytucji rządowych i Azure Stack
* Naprawiono: podczas przekazywania folderu do kontenera obiektów BLOB czasami wystąpi błąd niedozwolonej operacji
* Rozwiązano: opcja Zaznacz wszystko została wyłączona podczas zarządzania migawkami
* Naprawiono: metadane podstawowego obiektu BLOB mogą zostać nadpisywane po wyświetleniu właściwości jego migawek

#### <a name="known-issues"></a>Znane problemy

* W przypadku wybrania niewłaściwego certyfikatu numeru PIN/karty inteligentnej należy ponownie uruchomić program, aby Eksplorator usługi Storage zapominać o decyzji
* Podczas powiększania lub wypełniania poziom powiększenia może chwilowo zostać zresetowany do poziomu domyślnego
* Jednoczesne przekazanie więcej niż 3 grup obiektów blob lub plików może spowodować błędy
* Panel Ustawienia konta może wskazywać, że należy ponownie wprowadzić poświadczenia, aby filtrować subskrypcje
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* Instalacja programu Ubuntu 14,04 wymaga zaktualizowanej lub uaktualnionej wersji systemu

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Wersje 0.8.12 i 0.8.11 i 0.8.10
04/07/2017

#### <a name="new"></a>Nowy

* Eksplorator usługi Storage zostanie teraz automatycznie zamknięte po zainstalowaniu aktualizacji z powiadomienia o aktualizacji
* Szybki dostęp w miejscu zapewnia ulepszone środowisko pracy z często używanymi zasobami
* W edytorze kontenera obiektów BLOB można teraz zobaczyć, do której maszyny wirtualnej należy dzierżawiony obiekt BLOB
* Teraz można zwinąć panel po lewej stronie
* Odnajdywanie jest teraz uruchamiane w tym samym czasie jak pobieranie
* Użyj statystyk w kontenerze obiektów blob, udziale plików i edytorach tabel, aby wyświetlić rozmiar zasobu lub zaznaczenia
* Teraz możesz logować się do kont Azure Stack opartych na usłudze Azure Active Directory (AAD).
* Teraz można przekazywać pliki archiwalne za pośrednictwem 32MB do kont magazynu w warstwie Premium
* Ulepszona obsługa ułatwień dostępu
* Możesz teraz dodawać zaufane certyfikaty protokołu TLS (X. 509) z kodowaniem Base-64, przechodząc do edycji &gt; Certyfikaty SSL — &gt; Importowanie certyfikatów

#### <a name="fixes"></a>Poprawki

* Naprawiono: po odświeżeniu poświadczeń konta widok drzewa prawdopodobnie nie zostanie automatycznie odświeżony
* Naprawiono: generowanie sygnatury dostępu współdzielonego dla kolejek i tabel emulatorów spowoduje nieprawidłowy adres URL
* Naprawiono: konta magazynu w warstwie Premium mogą być teraz rozszerzane, gdy jest włączony serwer proxy
* Naprawiono: przycisk Zastosuj na stronie Zarządzanie kontami nie będzie działał, jeśli wybrano 1 lub 0 kont
* Naprawiono: przekazywanie obiektów blob, które wymagają rozpoznawania konfliktów, może zakończyć się niepowodzeniem w 0.8.11
* Naprawiono: wysyłanie opinii zostało przerwane w 0.8.11 — naprawione w 0.8.12

#### <a name="known-issues"></a>Znane problemy

* Po uaktualnieniu do 0.8.10 należy odświeżyć wszystkie poświadczenia.
* Podczas powiększania lub wypełniania poziom powiększenia może chwilowo zostać zresetowany do poziomu domyślnego.
* Jednoczesne przekazanie więcej niż 3 grup obiektów blob lub plików może spowodować błędy.
* Panel Ustawienia konta może wskazywać, że należy ponownie wprowadzić poświadczenia, aby przefiltrować subskrypcje.
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że Azure Stack nie obsługuje obecnie udziałów plików, węzeł udziały plików nadal pojawia się w obszarze dołączonego konta magazynu Azure Stack Storage.
* Instalacja programu Ubuntu 14,04 wymaga zaktualizowanej lub uaktualnionej wersji systemu

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Wersja 0.8.9 i 0.8.8
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Nowy

* Program Eksplorator usługi Storage 0.8.9 automatycznie pobierze najnowszą wersję dla aktualizacji.
* Poprawka: użycie identyfikatora URI sygnatury dostępu współdzielonego portalu w celu dołączenia konta magazynu spowoduje wystąpienie błędu.
* Teraz można tworzyć i wspierać migawki obiektów blob oraz zarządzać nimi.
* Możesz teraz zalogować się do konta platformy Azure z Chin, platformy Azure (Niemcy) i usługi Azure USA.
* Teraz można zmienić poziom powiększenia. Użyj opcji w menu Widok, aby powiększyć, pomniejszyć i zresetować powiększenie.
* Znaki Unicode są teraz obsługiwane w metadanych użytkownika dla obiektów blob i plików.
* Ulepszenia ułatwień dostępu.
* Informacje o wersji następnej wersji mogą być wyświetlane na podstawie powiadomienia o aktualizacji. Bieżące informacje o wersji można także wyświetlić z menu Pomoc.

#### <a name="fixes"></a>Poprawki

* Naprawiono: numer wersji jest teraz poprawnie wyświetlany w panelu sterowania w systemie Windows
* Naprawiono: wyszukiwanie nie jest już ograniczone do 50 000 węzłów
* Naprawiono: Przekaż do udziału plików Spuninst w nieskończoność, jeśli katalog docelowy jeszcze nie istnieje
* Naprawiono: Ulepszona stabilność dla długich operacji przekazywania i pobierania

#### <a name="known-issues"></a>Znane problemy

* Podczas powiększania lub wypełniania poziom powiększenia może chwilowo zostać zresetowany do poziomu domyślnego.
* Szybki dostęp działa tylko z elementami opartymi na subskrypcji. Zasoby lokalne lub zasoby dołączone przez token Key lub SAS nie są obsługiwane w tej wersji.
* Przejście do zasobu docelowego może potrwać kilka sekund, w zależności od liczby posiadanych zasobów.
* Jednoczesne przekazanie więcej niż 3 grup obiektów blob lub plików może spowodować błędy.

12/16/2016
### <a name="version-087"></a>0.8.7 wersja

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Nowy

* Możesz wybrać sposób rozwiązywania konfliktów na początku aktualizacji, pobierania lub kopiowania sesji w oknie działania
* Umieść kursor na karcie, aby wyświetlić pełną ścieżkę zasobu magazynu
* Gdy klikniesz kartę, zsynchronizuje się z jej lokalizacją w okienku nawigacji po lewej stronie

#### <a name="fixes"></a>Poprawki

* Naprawiono: Eksplorator usługi Storage jest teraz zaufaną aplikacją na komputerze Mac
* Naprawiono: Ubuntu 14,04 jest ponownie obsługiwana
* Naprawiono: czasami interfejs użytkownika dodawania konta jest przebłyska podczas ładowania subskrypcji
* Naprawiono: czasami nie wszystkie zasoby magazynu zostały wymienione w okienku nawigacji po lewej stronie
* Naprawiono: w okienku akcji czasami wyświetlane są puste akcje
* Naprawiono: rozmiar okna z ostatniej zamkniętej sesji jest teraz zachowywany
* Naprawiono: w menu kontekstowym można otworzyć wiele kart dla tego samego zasobu.

#### <a name="known-issues"></a>Znane problemy

* Szybki dostęp działa tylko z elementami opartymi na subskrypcji. Zasoby lokalne lub zasoby dołączone przez token Key lub SAS nie są obsługiwane w tej wersji
* Przejście do zasobu docelowego może potrwać kilka sekund, w zależności od liczby posiadanych zasobów
* Jednoczesne przekazanie więcej niż 3 grup obiektów blob lub plików może spowodować błędy
* Wyszukiwanie obsługuje wyszukiwanie w około 50 000 węzłach — po wykonaniu tej procedury może to mieć wpływ na wydajność lub może to spowodować nieobsługiwany wyjątek
* Po raz pierwszy przy użyciu Eksplorator usługi Storage w macOS może pojawić się wiele monitów o zezwolenie użytkownikowi na dostęp do łańcucha kluczy. Zalecamy wybranie opcji zawsze Zezwalaj, aby monit nie był ponownie widoczny

11/18/2016
### <a name="version-086"></a>0.8.6 wersja

#### <a name="new"></a>Nowy

* Teraz Możesz przypinać najczęściej używane usługi do szybkiego dostępu w celu ułatwienia nawigacji
* Teraz można otworzyć wiele edytorów na różnych kartach. Pojedyncze kliknięcie, aby otworzyć kartę tymczasową; Kliknij dwukrotnie, aby otworzyć kartę trwałą. Możesz również kliknąć kartę tymczasową, aby uczynić ją stałą kartą
* Wprowadziliśmy zauważalne ulepszenia wydajności i stabilności dotyczące przekazywania i pobierania, szczególnie w przypadku dużych plików na szybkich maszynach
* Puste foldery "wirtualne" można teraz tworzyć w kontenerach obiektów BLOB
* Firma Microsoft ponownie wprowadziła przeszukiwany zakres przy użyciu nowego, ulepszonego wyszukiwania podciągów, dzięki czemu masz teraz dwie opcje wyszukiwania:
    * Wyszukiwanie globalne — po prostu wprowadź termin wyszukiwania do pola tekstowego wyszukiwania
    * Wyszukiwanie w zakresie — kliknij ikonę lupy obok węzła, a następnie Dodaj termin wyszukiwania na końcu ścieżki lub kliknij prawym przyciskiem myszy i wybierz polecenie "Wyszukaj z tego miejsca"
* Dodaliśmy różne motywy: jasne (domyślne), ciemne, duży kontrast czarne i duży kontrast białe. Przejdź do pozycji Edytuj &gt; motywy, aby zmienić preferencję z motywami
* Można modyfikować właściwości obiektów blob i plików
* Obsługujemy teraz zakodowane (base64) i niezaszyfrowane komunikaty w kolejce
* W systemie Linux wymagany jest 64-bitowy system operacyjny. W tej wersji obsługujemy tylko 64-bitowe Ubuntu 16.04.1 LTS
* Zaktualizowaliśmy logo!

#### <a name="fixes"></a>Poprawki

* Naprawiono: problemy z zablokowaniem ekranu
* Naprawiono: ulepszone zabezpieczenia
* Naprawiono: czasami powielone dołączone konta mogą być wyświetlane
* Naprawiono: obiekt BLOB z niezdefiniowanym typem zawartości może generować wyjątek
* Naprawiono: nie można otworzyć panelu zapytania w pustej tabeli.
* Naprawiono: różne błędy w wyszukiwaniu
* Naprawiono: zwiększono liczbę zasobów załadowanych z 50 do 100 po kliknięciu przycisku "Załaduj więcej"
* Naprawiono: przy pierwszym uruchomieniu, jeśli konto jest zalogowane, teraz wybieramy domyślnie wszystkie subskrypcje dla tego konta

#### <a name="known-issues"></a>Znane problemy

* Ta wersja Eksplorator usługi Storage nie działa w systemie Ubuntu 14,04
* Aby otworzyć wiele kart dla tego samego zasobu, nie należy ciągle klikać tego samego zasobu. Kliknij inny zasób, a następnie wróć, a następnie kliknij oryginalny zasób, aby otworzyć go ponownie na innej karcie
* Szybki dostęp działa tylko z elementami opartymi na subskrypcji. Zasoby lokalne lub zasoby dołączone przez token Key lub SAS nie są obsługiwane w tej wersji
* Przejście do zasobu docelowego może potrwać kilka sekund, w zależności od liczby posiadanych zasobów
* Jednoczesne przekazanie więcej niż 3 grup obiektów blob lub plików może spowodować błędy
* Wyszukiwanie obsługuje wyszukiwanie w około 50 000 węzłach — po wykonaniu tej procedury może to mieć wpływ na wydajność lub może to spowodować nieobsługiwany wyjątek

03.10.2016
### <a name="version-085"></a>0.8.5 wersja

#### <a name="new"></a>Nowy

* Za pomocą kluczy SAS generowanych przez portal można teraz dołączać do kont magazynu i zasobów

#### <a name="fixes"></a>Poprawki

* Naprawiono: sytuacja wyścigu podczas wyszukiwania czasami powodowała, że węzły stają się nierozszerzalne
* Naprawione: funkcja "Użyj protokołu HTTP" nie działa podczas nawiązywania połączenia z kontami magazynu z nazwą konta i kluczem
* Naprawiono: klucze SAS (specjalnie generowane przez portal) zwracają błąd "końcowego ukośnika"
* Naprawiono: problemy z importowaniem tabel
    * Czasami klucze partycji i klucz wiersza zostały cofnięte
    * Nie można odczytać kluczy partycji "null"

#### <a name="known-issues"></a>Znane problemy

* Wyszukiwanie obsługuje wyszukiwanie w około 50 000 węzłach — po tym przypadku może to mieć wpływ na wydajność
* Azure Stack nie obsługuje obecnie plików, dlatego próba rozwinięcia plików spowoduje wyświetlenie błędu

09/12/2016
### <a name="version-084"></a>0.8.4 wersja

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Nowy

* Generuj bezpośrednie linki do kont magazynu, kontenerów, kolejek, tabel lub udziałów plików w celu udostępniania i łatwego dostępu do zasobów — Windows i obsługa Mac OS
* Wyszukiwanie kontenerów obiektów blob, tabel, kolejek, udziałów plików lub kont magazynu w polu wyszukiwania
* Można teraz grupować klauzule w tabeli konstruktora zapytań
* Zmienianie nazw i kopiowanie/wklejanie kontenerów obiektów blob, udziałów plików, tabel, obiektów blob, folderów obiektów blob, plików i katalogów z poziomu dołączonych do nich kont i kontenerów
* Zmiana nazwy i skopiowanie kontenerów obiektów blob oraz udziałów plików teraz zachowuje właściwości i metadane

#### <a name="fixes"></a>Poprawki

* Naprawiono: nie można edytować jednostek tabeli, jeśli zawierają one właściwości logiczne lub binarne.

#### <a name="known-issues"></a>Znane problemy

* Wyszukiwanie obsługuje wyszukiwanie w około 50 000 węzłach — po tym przypadku może to mieć wpływ na wydajność

08/03/2016
### <a name="version-083"></a>0.8.3 wersja

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Nowy

* Zmień nazwę kontenerów, tabel, udziałów plików
* Udoskonalone środowisko konstruktora zapytań
* Możliwość zapisywania i ładowania zapytań
* Bezpośrednie linki do kont magazynu lub kontenerów, kolejek, tabel lub udziałów plików w celu udostępniania i łatwego uzyskiwania dostępu do zasobów (dostępna tylko dla systemu Windows — Pomoc techniczna macOS)
* Możliwość zarządzania i konfigurowania reguł CORS

#### <a name="fixes"></a>Poprawki

* Naprawiono: konta Microsoft wymagają ponownego uwierzytelnienia co 8-12 godzin

#### <a name="known-issues"></a>Znane problemy

* Czasami interfejs użytkownika może wydawać się zamrożone — Maksymalizowanie okna pozwala rozwiązać ten problem
* Instalacja macOS może wymagać podwyższonego poziomu uprawnień
* Panel Ustawienia konta może wskazywać, że należy ponownie wprowadzić poświadczenia, aby filtrować subskrypcje
* Zmiana nazw udziałów plików, kontenerów obiektów blob i tabel nie zachowuje metadanych ani innych właściwości kontenera, takich jak przydział udziału plików, poziom dostępu publicznego lub zasady dostępu
* Zmiana nazw obiektów BLOB (pojedyncza lub wewnątrz kontenera obiektów BLOB o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy
* Kopiowanie lub zmiana nazw zasobów nie działa w ramach kont dołączonych do sygnatury dostępu współdzielonego

07/07/2016
### <a name="version-082"></a>0.8.2 wersja

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Nowy

* Konta magazynu są pogrupowane według subskrypcji; Magazyn programistyczny i zasoby dołączone za pośrednictwem klucza lub sygnatury dostępu współdzielonego są wyświetlane w węźle (lokalne i dołączone)
* Wyloguj się z kont w panelu "Ustawienia konta platformy Azure"
* Konfigurowanie ustawień serwera proxy w celu włączenia logowania i zarządzania nim
* Tworzenie i przerywanie dzierżaw obiektów BLOB
* Otwieranie kontenerów obiektów blob, kolejek, tabel i plików za pomocą pojedynczego kliknięcia

#### <a name="fixes"></a>Poprawki

* Naprawiono: komunikaty w kolejce wstawione przy użyciu bibliotek .NET lub Java nie są poprawnie zdekodowane z formatu Base64
* Naprawiono: tabele $metrics nie są wyświetlane dla kont Blob Storage
* Naprawiono: węzeł tabel nie działa w przypadku lokalnego (programistycznego) magazynu

#### <a name="known-issues"></a>Znane problemy

* Instalacja macOS może wymagać podwyższonego poziomu uprawnień

06/15/2016
### <a name="version-080"></a>0.8.0 wersja

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Nowy

* Obsługa udziału plików: przeglądanie, przekazywanie, pobieranie, kopiowanie plików i katalogów, identyfikatory URI sygnatury dostępu współdzielonego (Tworzenie i łączenie)
* Ulepszone środowisko użytkownika do łączenia się z magazynem za pomocą identyfikatorów URI SAS lub kluczy kont
* Eksportuj wyniki zapytania tabeli
* Zmiana kolejności kolumn tabeli i ich dostosowywania
* Wyświetlanie $logs kontenerów obiektów blob i tabel $metrics dla kont magazynu z włączonymi metrykami
* Ulepszone zachowanie eksportu i importu, teraz zawiera typ wartości właściwości

#### <a name="fixes"></a>Poprawki

* Naprawiono: przekazywanie lub pobieranie dużych obiektów BLOB może spowodować niekompletne operacje przekazywania/pobrania
* Naprawiono: edytowanie, Dodawanie lub importowanie jednostki z wartością ciągu liczbowego ("1") spowoduje przekonwertowanie jej na wartość typu Double
* Naprawiono: nie można rozwinąć węzła tabeli w lokalnym środowisku programistycznym

#### <a name="known-issues"></a>Znane problemy

* tabele $metrics nie są widoczne dla kont Blob Storage
* Komunikaty kolejek dodane programowo mogą nie być wyświetlane poprawnie, Jeśli komunikaty są kodowane przy użyciu kodowania base64

05/17/2016
### <a name="version-07201605090"></a>0.7.20160509.0 wersja

#### <a name="new"></a>Nowy

* Lepsza obsługa błędów w przypadku awarii aplikacji

#### <a name="fixes"></a>Poprawki

* Stała usterka, w której wiadomości na pasku informacyjnym czasami nie są wyświetlane, gdy wymagane są poświadczenia logowania

#### <a name="known-issues"></a>Znane problemy

* Tabele: Dodawanie, edytowanie lub importowanie jednostki, która ma właściwość z niejednoznaczną wartością liczbową, taką jak "1" lub "1,0", a użytkownik próbuje wysłać ją jako `Edm.String` , wartość zostanie przywrócona za pomocą interfejsu API klienta jako modelu EDM. Double

03/31/2016

### <a name="version-07201603250"></a>0.7.20160325.0 wersja

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Nowy

* Obsługa tabel: przeglądanie, wykonywanie zapytań, eksportowanie, importowanie i CRUD operacji dla jednostek
* Obsługa kolejki: przeglądanie, Dodawanie, usuwanie z kolejki komunikatów
* Generowanie identyfikatorów URI sygnatury dostępu współdzielonego dla kont magazynu
* Łączenie z kontami magazynu za pomocą identyfikatorów URI SAS
* Aktualizowanie powiadomień o przyszłej aktualizacji do Eksplorator usługi Storage
* Zaktualizowany wygląd i działanie

#### <a name="fixes"></a>Poprawki

* Ulepszenia wydajności i niezawodności

### <a name="known-issues-amp-mitigations"></a>Znane problemy — &amp; ograniczenia

* Pobieranie dużych plików obiektów BLOB nie działa prawidłowo — zalecamy korzystanie z AzCopy podczas rozwiązywania tego problemu
* Poświadczenia konta nie zostaną pobrane ani w pamięci podręcznej, jeśli nie można odnaleźć folderu macierzystego lub nie można zapisać w nim
* Jeśli dodajesz, edytujesz lub importujesz jednostkę, która ma właściwość z niejednoznaczną wartością liczbową, taką jak "1" lub "1,0", a użytkownik spróbuje ją wysłać jako `Edm.String` , wartość zostanie przywrócona za pomocą interfejsu API klienta jako modelu EDM. Double
* Podczas importowania plików CSV z rekordami wielowierszowymi dane mogą zostać zniekształcone lub zaszyfrowane

02/03/2016

### <a name="version-07201601291"></a>0.7.20160129.1 wersja

#### <a name="fixes"></a>Poprawki

* Ulepszona ogólna wydajność podczas przekazywania, pobierania i kopiowania obiektów BLOB

01/14/2016

### <a name="version-07201601050"></a>0.7.20160105.0 wersja

#### <a name="new"></a>Nowy

* Obsługa systemu Linux (funkcje z parzystością do OSX)
* Dodawanie kontenerów obiektów blob z kluczem sygnatury dostępu współdzielonego (SAS)
* Dodawanie kont magazynu dla platformy Azure (Chiny)
* Dodawanie kont magazynu z niestandardowymi punktami końcowymi
* Otwórz i Wyświetl tekst zawartości i obrazy obiektów BLOB
* Wyświetlanie i edytowanie właściwości obiektów blob i metadanych

#### <a name="fixes"></a>Poprawki

* Naprawiono: przekazywanie lub pobieranie dużej liczby obiektów BLOB (500 +) może czasami spowodować, że aplikacja ma biały ekran
* Naprawiono: podczas ustawiania poziomu dostępu publicznego kontenera obiektów BLOB nowa wartość nie zostanie zaktualizowana, dopóki fokus nie zostanie ustawiony na kontenerze. Ponadto w oknie dialogowym zawsze jest domyślnie ustawiona wartość "Brak dostępu publicznego", a nie rzeczywista bieżąca.
* Lepsza ogólna Obsługa klawiatury/dostępności i interfejsu użytkownika
* Tekst historii struktury nawigacyjnej jest zawijany, gdy jest on długi z białym znakiem
* Okno dialogowe SAS obsługuje walidację danych wejściowych
* Magazyn lokalny jest nadal dostępny, nawet jeśli poświadczenia użytkownika wygasły
* Po usunięciu otwartego kontenera obiektów BLOB Eksplorator obiektów BLOB po prawej stronie jest zamknięty

#### <a name="known-issues"></a>Znane problemy

* Instalacja systemu Linux wymaga zaktualizowanej lub uaktualnionej wersji programu w zatoce — kroki do uaktualnienia są następujące:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>0.7.20151116.0 wersja

#### <a name="new"></a>Nowy

* macOS i wersje systemu Windows
* Zaloguj się, aby wyświetlić konta magazynu — Użyj konta organizacji, konta Microsoft, funkcji 2FA itp.
* Lokalny magazyn programistyczny (używaj emulatora magazynu, tylko systemu Windows)
* Obsługa zasobów Azure Resource Manager i klasycznych
* Tworzenie i usuwanie obiektów blob, kolejek lub tabel
* Wyszukiwanie określonych obiektów blob, kolejek lub tabel
* Eksplorowanie zawartości kontenerów obiektów BLOB
* Wyświetlanie katalogów i nawigowanie po nich
* Przekazywanie, pobieranie i usuwanie obiektów blob i folderów
* Wyświetlanie i edytowanie właściwości obiektów blob i metadanych
* Generuj klucze SAS
* Zarządzanie i tworzenie zasad dostępu przechowywanego (SAP)
* Wyszukaj obiekty blob według prefiksu
* Przeciągnij "n upuść pliki do przekazania lub pobrania

#### <a name="known-issues"></a>Znane problemy

* Podczas ustawiania poziomu dostępu publicznego kontenera obiektów BLOB nowa wartość nie jest aktualizowana, dopóki fokus nie zostanie ustawiony na kontenerze
* Po otwarciu okna dialogowego w celu ustawienia poziomu dostępu publicznego zawsze jest wyświetlany stan "Brak dostępu publicznego" jako domyślny, a nie rzeczywista bieżąca wartość
* Nie można zmienić nazwy pobranych obiektów BLOB
* Wpisy dziennika aktywności będą czasami otrzymywać "zablokowane" w stanie w toku w przypadku wystąpienia błędu, a błąd nie jest wyświetlany
* Czasami awarie lub całkowite wyłączenie w trakcie próby przekazania lub pobrania dużej liczby obiektów BLOB
* Czasami anulowanie operacji kopiowania nie działa
* Podczas tworzenia kontenera (obiektu BLOB/Queue/Table), jeśli wprowadzasz nieprawidłową nazwę i chcesz utworzyć inny element w innym typie kontenera, nie można ustawić fokusu dla nowego typu
* Nie można utworzyć nowego folderu lub zmienić nazwy folderu




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md