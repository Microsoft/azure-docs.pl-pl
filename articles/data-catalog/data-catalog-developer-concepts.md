---
title: Azure Data Catalog koncepcje dla deweloperów
description: Wprowadzenie do kluczowych pojęć dotyczących modelu koncepcyjnego Azure Data Catalog, które są udostępniane za pomocą interfejsu API REST katalogu.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: ddeab4838feb07d1101993cab4ebc86581b4d8b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674703"
---
# <a name="azure-data-catalog-developer-concepts"></a>Azure Data Catalog koncepcje dla deweloperów

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Microsoft **Azure Data Catalog** to w pełni zarządzana usługa w chmurze, która zapewnia możliwości odnajdowania źródeł danych i crowdsourcing metadanych źródła danych. Deweloperzy mogą korzystać z usługi za pośrednictwem interfejsów API REST. Zrozumienie koncepcji zaimplementowanych w usłudze jest ważne, aby deweloperzy mogli pomyślnie zintegrować się z **Azure Data Catalog**.

## <a name="key-concepts"></a>Kluczowe pojęcia 
Model koncepcyjny **Azure Data Catalog** jest oparty na czterech najważniejszych założeniach: **katalogu**, **użytkowników**, **zasobów** i **adnotacjach**.

![Ilustracja przedstawiająca model koncepcyjny Azure Data Catalog](./media/data-catalog-developer-concepts/concept2.png)

### <a name="catalog"></a>Wykaz
**Katalog** jest kontenerem najwyższego poziomu dla wszystkich metadanych przechowywanych w organizacji. Jeden **wykaz** jest dozwolony dla konta platformy Azure. Wykazy są powiązane z subskrypcją platformy Azure, ale dla danego konta platformy Azure można utworzyć tylko jeden **wykaz** , mimo że konto może mieć wiele subskrypcji.

Wykaz zawiera **użytkowników** i **zasoby**.

### <a name="users"></a>Użytkownicy
Użytkownicy są podmiotami zabezpieczeń, które mają uprawnienia do wykonywania akcji (wyszukiwanie katalogu, Dodawanie, edytowanie lub usuwanie elementów itp.) w wykazie.

Istnieje kilka różnych ról, które użytkownik może mieć. Aby uzyskać informacje na temat ról, zobacz sekcję role i autoryzacja.

Można dodać poszczególnych użytkowników i grupy zabezpieczeń.

Azure Data Catalog używa Azure Active Directory do zarządzania tożsamościami i dostępem. Każdy użytkownik katalogu musi być członkiem Active Directory konta.

### <a name="assets"></a>Elementy zawartości
**Wykaz** zawiera zasoby danych. **Elementy zawartości** są jednostką stopnia szczegółowości zarządzaną przez wykaz.

Stopień szczegółowości elementu zawartości różni się w zależności od źródła danych. W przypadku SQL Server lub Oracle Database zasób może być tabelą lub widokiem. W przypadku SQL Server Analysis Services element zawartości może być miarą, wymiarem lub kluczowym wskaźnikiem wydajności (KPI). W przypadku SQL Server Reporting Services zasób jest raportem.

Element **zawartości** jest dodawany lub usuwany z wykazu. Jest to jednostka wyniku z **przeszukiwania**.

**Zasób** jest tworzony na podstawie jego nazwy, lokalizacji i typu oraz adnotacji, które dodatkowo opisują ją.

### <a name="annotations"></a>Adnotacje
Adnotacje to elementy reprezentujące metadane dotyczące zasobów.

Przykłady adnotacji to opis, Tagi, schemat, dokumentacja itp. Zapoznaj się z [sekcją model obiektów zasobów](#asset-object-model) , aby uzyskać pełną listę typów zasobów i typów adnotacji.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Adnotacje crowdsourcing i perspektywa użytkownika (liczebność opinii)
Kluczowym aspektem Azure Data Catalog jest sposób obsługi crowdsourcing metadanych w systemie. W przeciwieństwie do podejścia wiki — w przypadku gdy istnieje tylko jedna opinia i ostatni składnik zapisywania usługi WINS — model Azure Data Catalog umożliwia korzystanie z wielu opinii na żywo obok siebie w systemie.

Takie podejście odzwierciedla rzeczywiste świecie danych przedsiębiorstwa, w których różni użytkownicy mogą mieć różne perspektywy dotyczące danego elementu zawartości:

* Administrator bazy danych może podać informacje dotyczące umów dotyczących poziomu usług lub dostępnego okna przetwarzania dla operacji zbiorczych ETL
* Steward danych może dostarczyć informacji o procesach działalności biznesowej, do których stosuje się zasoby, lub klasyfikacji, do których zastosowano firma
* Analityk finansowy może dostarczyć informacji na temat sposobu używania danych podczas kończenia okresu zadań raportowania

Aby zapewnić obsługę tego przykładu, każdy użytkownik — administrator, Steward danych i analityk — może dodać opis do pojedynczej tabeli, która została zarejestrowana w katalogu. Wszystkie opisy są przechowywane w systemie, a w portalu Azure Data Catalog są wyświetlane wszystkie opisy.

Ten wzorzec jest stosowany do większości elementów w modelu obiektów, dlatego typy obiektów w ładunku JSON często są tablicami dla właściwości, w których może być oczekiwany pojedynczy.

Na przykład w obszarze głównym elementu zawartości jest to tablica obiektów opisu. Właściwość Array ma nazwę "descriptions". Obiekt Description ma jeden opis właściwości. Wzorzec polega na tym, że każdy użytkownik, który wpisze opis pobiera obiekt opisu dla wartości dostarczonej przez użytkownika.

ŚRODOWISKO użytkownika może następnie wybrać sposób wyświetlania kombinacji. Istnieją trzy różne wzorce do wyświetlania.

* Najprostszym wzorcem jest "Pokaż wszystko". W tym wzorcu wszystkie obiekty są wyświetlane w widoku listy. ŚRODOWISKO użytkownika portalu Azure Data Catalog używa tego wzorca do opisu.
* Inny wzorzec to "merge". W tym wzorcu wszystkie wartości z różnych użytkowników są scalane razem z usuniętymi duplikatami. Przykłady tego wzorca w interfejsie użytkownika portalu Azure Data Catalog są właściwościami tagów i ekspertów.
* Trzeci wzorzec to "ostatni składnik zapisywania usługi WINS". W tym wzorcu wyświetlana jest tylko najnowsza wartość wprowadzona w polu. FriendlyName jest przykładem tego wzorca.

## <a name="asset-object-model"></a>Model obiektów zasobów
Zgodnie z opisem w sekcji kluczowe pojęcia **Azure Data Catalog** model obiektów zawiera elementy, które mogą być zasobami lub adnotacjami. Elementy mają właściwości, które mogą być opcjonalne lub wymagane. Niektóre właściwości są stosowane do wszystkich elementów. Niektóre właściwości mają zastosowanie do wszystkich zasobów. Niektóre właściwości mają zastosowanie tylko do określonych typów zasobów.

### <a name="system-properties"></a>Właściwości systemu
<table><tr><td><b>Nazwa właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr><tr><td>sygnatura czasowa</td><td>DateTime</td><td>Godzina ostatniej modyfikacji elementu. To pole jest generowane przez serwer, gdy element zostanie wstawiony i za każdym razem, gdy element zostanie zaktualizowany. Wartość tej właściwości jest ignorowana podczas wprowadzania operacji publikowania.</td></tr><tr><td>ID (Identyfikator)</td><td>Adresu</td><td>Bezwzględny adres URL elementu (tylko do odczytu). Jest to unikatowy adres URI dla elementu.  Wartość tej właściwości jest ignorowana podczas wprowadzania operacji publikowania.</td></tr><tr><td>typ</td><td>Ciąg</td><td>Typ zasobu (tylko do odczytu).</td></tr><tr><td>element ETag</td><td>Ciąg</td><td>Ciąg odpowiadający wersji elementu, który może być używany do optymistycznej kontroli współbieżności podczas wykonywania operacji, które aktualizują elementy w wykazie. znaku "*" można użyć do dopasowania dowolnej wartości.</td></tr></table>

### <a name="common-properties"></a>Wspólne właściwości
Te właściwości mają zastosowanie do wszystkich typów zasobów głównych i wszystkich typów adnotacji.

<table>
<tr><td><b>Nazwa właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr>
<tr><td>fromSourceSystem</td><td>Wartość logiczna</td><td>Wskazuje, czy dane elementu są wyprowadzane z systemu źródłowego (na przykład SQL Server Database, Oracle Database) lub utworzone przez użytkownika.</td></tr>
</table>

### <a name="common-root-properties"></a>Wspólne właściwości główne
<p>
Te właściwości mają zastosowanie do wszystkich typów zasobów głównych.

<table><tr><td><b>Nazwa właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr><tr><td>name</td><td>Ciąg</td><td>Nazwa pochodząca z informacji o lokalizacji źródła danych</td></tr><tr><td>DSL</td><td>DataSourceLocation</td><td>Jednoznacznie opisuje źródło danych i jest jednym z identyfikatorów dla elementu zawartości. (Zobacz sekcję o podwójnej tożsamości).  Struktura DSL jest zależna od protokołu i typu źródła.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Więcej szczegółów na temat typu elementu zawartości.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Opisuje użytkownika, który ostatnio zarejestrował ten element zawartości.  Zawiera unikatowy identyfikator użytkownika (nazwy UPN) i nazwę wyświetlaną (lastName i firstName).</td></tr><tr><td>containerID</td><td>Ciąg</td><td>Identyfikator zasobu kontenera dla źródła danych. Ta właściwość nie jest obsługiwana dla typu kontenera.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Wspólne właściwości adnotacji, które nie są singleton
Te właściwości mają zastosowanie do wszystkich typów adnotacji niepojedynczej (adnotacje, które mogą być wielokrotne dla każdego elementu zawartości).

<table>
<tr><td><b>Nazwa właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr>
<tr><td>key</td><td>Ciąg</td><td>Klucz określony przez użytkownika, który jednoznacznie identyfikuje adnotację w bieżącej kolekcji. Długość klucza nie może przekraczać 256 znaków.</td></tr>
</table>

### <a name="root-asset-types"></a>Typy zasobów głównych
Typy zasobów głównych to typy reprezentujące różne typy zasobów danych, które mogą być rejestrowane w wykazie. Dla każdego typu głównego istnieje widok, który zawiera opis elementów zawartości i adnotacji zawartych w tym widoku. Nazwa widoku powinna być używana w odpowiadającym segmencie {view_name} URL podczas publikowania elementu zawartości przy użyciu interfejsu API REST.

<table><tr><td><b>Typ zasobu (nazwa widoku)</b></td><td><b>Dodatkowe właściwości</b></td><td><b>Typ danych</b></td><td><b>Dozwolone adnotacje</b></td><td><b>Komentarze</b></td></tr><tr><td>Tabela ("tabele")</td><td></td><td></td><td>Opis<p>FriendlyName<p>Tag<p>Schemat<p>ColumnDescription<p>ColumnTag<p> Ekspert<p>Wersja zapoznawcza<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Dokumentacja<p></td><td>Tabela reprezentuje wszystkie dane tabelaryczne.  Na przykład: tabela SQL, Widok SQL, Analysis Services tabeli tabelarycznej, Analysis Services wymiar wielowymiarowych, tabela Oracle itd.   </td></tr><tr><td>Miara ("miary")</td><td></td><td></td><td>Opis<p>FriendlyName<p>Tag<p>Ekspert<p>AccessInstruction<p>Dokumentacja<p></td><td>Ten typ reprezentuje miarę Analysis Services.</td></tr><tr><td></td><td>mierzyć</td><td>Kolumna</td><td></td><td>Metadane opisujące miarę</td></tr><tr><td></td><td>isobliczeniowe </td><td>Wartość logiczna</td><td></td><td>Określa, czy miara jest obliczana, czy nie.</td></tr><tr><td></td><td>measureGroup</td><td>Ciąg</td><td></td><td>Kontener fizyczny dla miary</td></tr><td>KWW ("KPI")</td><td></td><td></td><td>Opis<p>FriendlyName<p>Tag<p>Ekspert<p>AccessInstruction<p>Dokumentacja</td><td></td></tr><tr><td></td><td>measureGroup</td><td>Ciąg</td><td></td><td>Kontener fizyczny dla miary</td></tr><tr><td></td><td>goalExpression</td><td>Ciąg</td><td></td><td>Wyrażenie liczbowe MDX lub obliczenie zwracające wartość docelową wskaźnika KPI.</td></tr><tr><td></td><td>valueExpression</td><td>Ciąg</td><td></td><td>Wyrażenie liczbowe MDX zwracające rzeczywistą wartość wskaźnika KPI.</td></tr><tr><td></td><td>statusExpression</td><td>Ciąg</td><td></td><td>Wyrażenie MDX, które reprezentuje stan wskaźnika KPI w określonym momencie.</td></tr><tr><td></td><td>trendExpression</td><td>Ciąg</td><td></td><td>Wyrażenie MDX, które oblicza wartość wskaźnika KPI w czasie. Trend może być dowolnym kryterium opartym na czasie, który jest przydatny w określonym kontekście biznesowym.</td>
<tr><td>Raport ("Raporty")</td><td></td><td></td><td>Opis<p>FriendlyName<p>Tag<p>Ekspert<p>AccessInstruction<p>Dokumentacja<p></td><td>Ten typ reprezentuje raport SQL Server Reporting Services </td></tr><tr><td></td><td>assetCreatedDate</td><td>Ciąg</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Ciąg</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Ciąg</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Ciąg</td><td></td><td></td></tr><tr><td>Kontener ("kontenery")</td><td></td><td></td><td>Opis<p>FriendlyName<p>Tag<p>Ekspert<p>AccessInstruction<p>Dokumentacja<p></td><td>Ten typ reprezentuje kontener innych zasobów, takich jak baza danych SQL, kontener obiektów blob platformy Azure lub model Analysis Services.</td></tr></table>

### <a name="annotation-types"></a>Typy adnotacji
Typy adnotacji reprezentują typy metadanych, które można przypisać do innych typów w katalogu.

<table>
<tr><td><b>Typ adnotacji (nazwa widoku zagnieżdżonego)</b></td><td><b>Dodatkowe właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr>

<tr><td>Opis ("opisy")</td><td></td><td></td><td>Ta właściwość zawiera opis elementu zawartości. Każdy użytkownik systemu może dodać własny opis.  Tylko ten użytkownik może edytować obiekt Description.  (Administratorzy i właściciele zasobów mogą usunąć obiekt opisu, ale go nie edytować). System obsługuje opisy użytkowników osobno.  W ten sposób istnieje tablica opisów dla każdego elementu zawartości (po jednym dla każdego użytkownika, który pobrał swoją wiedzę na temat zasobu, oprócz możliwego, który zawiera informacje pochodzące ze źródła danych).</td></tr>
<tr><td></td><td>description (opis)</td><td>ciąg</td><td>Krótki opis (2-3 wierszy) elementu zawartości</td></tr>

<tr><td>Tag ("Tagi")</td><td></td><td></td><td>Ta właściwość definiuje tag dla elementu zawartości. Każdy użytkownik systemu może dodać wiele tagów dla elementu zawartości.  Tylko użytkownik, który utworzył obiekty tagów, może je edytować.  (Administratorzy i właściciele zasobów mogą usunąć obiekt tagów, ale nie edytować go). System obsługuje znaczniki użytkowników osobno.  W ten sposób istnieje tablica obiektów tagów dla każdego elementu zawartości.</td></tr>
<tr><td></td><td>tag</td><td>ciąg</td><td>Tag opisujący element zawartości.</td></tr>

<tr><td>FriendlyName ("FriendlyName")</td><td></td><td></td><td>Ta właściwość zawiera przyjazną nazwę elementu zawartości. FriendlyName jest pojedynczą adnotacją — tylko jeden element FriendlyName można dodać do elementu zawartości.  Tylko użytkownik, który utworzył obiekt FriendlyName, może go edytować. (Administratorzy i właściciele zasobów mogą usunąć obiekt FriendlyName, ale go nie edytować). System zachowuje przyjazne nazwy dla użytkowników osobno.</td></tr>
<tr><td></td><td>friendlyName</td><td>ciąg</td><td>Przyjazna nazwa elementu zawartości.</td></tr>

<tr><td>Schemat ("schemat")</td><td></td><td></td><td>Schemat opisuje strukturę danych.  Wyświetla listę nazw atrybutów (kolumn, atrybutów, pól itp.), typów i innych metadanych.  Wszystkie te informacje pochodzą ze źródła danych.  Schemat jest pojedynczą adnotacją — można dodać tylko jeden schemat dla elementu zawartości.</td></tr>
<tr><td></td><td>columns</td><td>Kolumna []</td><td>Tablica obiektów kolumn. Opisują kolumnę z informacjami uzyskanymi na podstawie źródła danych.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Ta właściwość zawiera opis kolumny.  Każdy użytkownik systemu może dodać własne opisy dla wielu kolumn (najwyżej jednej na kolumnę). Tylko użytkownik, który utworzył obiekty ColumnDescription, może je edytować.  (Administratorzy i właściciele zasobów mogą usunąć obiekt ColumnDescription, ale go nie edytować). System zachowuje opisy kolumn tego użytkownika osobno.  W ten sposób istnieje tablica obiektów ColumnDescription dla każdego elementu zawartości (po jednej dla każdego użytkownika, który utworzył swoją wiedzę o kolumnie, a nie może być taka, która zawiera informacje pochodzące ze źródła danych).  ColumnDescription jest luźno powiązany ze schematem, dzięki czemu może nie być zsynchronizowany. ColumnDescription może opisywać kolumnę, która już nie istnieje w schemacie.  Aby zachować opis i schemat w synchronizacji, należy do składnika zapisywania.  Źródło danych może również zawierać informacje o opisach kolumn i są dodatkowymi obiektami ColumnDescription, które zostaną utworzone podczas uruchamiania narzędzia.</td></tr>
<tr><td></td><td>nazwa_kolumny</td><td>Ciąg</td><td>Nazwa kolumny, do której odwołuje się ten opis.</td></tr>
<tr><td></td><td>description (opis)</td><td>Ciąg</td><td>Krótki opis (2-3 wierszy) kolumny.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Ta właściwość zawiera tag dla kolumny. Każdy użytkownik systemu może dodać wiele tagów dla danej kolumny i może dodać Tagi dla wielu kolumn. Tylko użytkownik, który utworzył obiekty ColumnTag, może je edytować. (Administratorzy i właściciele zasobów mogą usunąć obiekt ColumnTag, ale go nie edytować). System przechowuje znaczniki kolumn użytkowników osobno.  W ten sposób istnieje tablica obiektów ColumnTag dla każdego elementu zawartości.  ColumnTag jest luźno powiązany ze schematem, dzięki czemu może nie być zsynchronizowany. ColumnTag może opisywać kolumnę, która już nie istnieje w schemacie.  Jest on do składnika zapisywania, aby zachować synchronizację tagów i schematu kolumn.</td></tr>
<tr><td></td><td>nazwa_kolumny</td><td>Ciąg</td><td>Nazwa kolumny, do której odwołuje się ten tag.</td></tr>
<tr><td></td><td>tag</td><td>Ciąg</td><td>Tag opisujący kolumnę.</td></tr>

<tr><td>Ekspert ("eksperci")</td><td></td><td></td><td>Ta właściwość zawiera użytkownika, który jest traktowany jak ekspert w zestawie danych. Podczas tworzenia listy opisów jest bąbelkowy dymek (opisy) ekspertów na górze środowiska użytkownika. Każdy użytkownik może określić swoich własnych ekspertów. Tylko ten użytkownik może edytować obiekt ekspertów. (Administratorzy i właściciele zasobów mogą usunąć obiekty, ale nie edytować ich).</td></tr>
<tr><td></td><td>fachow</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Wersja zapoznawcza ("wersje zapoznawcze")</td><td></td><td></td><td>Wersja zapoznawcza zawiera migawkę 20 najważniejszych wierszy danych dla elementu zawartości. Wersja zapoznawcza ma sens tylko w przypadku niektórych typów zasobów (jest to zrozumiałe dla tabeli, ale nie dla miary).</td></tr>
<tr><td></td><td>preview</td><td>Obiekt []</td><td>Tablica obiektów, które reprezentują kolumnę.  Każdy obiekt ma mapowanie właściwości do kolumny z wartością dla tej kolumny w wierszu.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>ciąg</td><td>Typ MIME zawartości.</td></tr>
<tr><td></td><td>zawartość</td><td>ciąg</td><td>Instrukcje dotyczące uzyskiwania dostępu do tego zasobu danych. Zawartość może być adresem URL, adresem e-mail lub zestawem instrukcji.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Liczba wierszy w zestawie danych</td></tr>
<tr><td></td><td>size</td><td>długi</td><td>Rozmiar w bajtach zestawu danych.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>ciąg</td><td>Czas ostatniej modyfikacji schematu</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>ciąg</td><td>Czas ostatniej modyfikacji zestawu danych (dane zostały dodane, zmodyfikowane lub usunięte)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columns</td></td><td>ColumnDataProfile[]</td><td>Tablica profilów danych kolumn.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>nazwa_kolumny</td><td>Ciąg</td><td>Nazwa kolumny, do której odwołuje się ta Klasyfikacja.</td></tr>
<tr><td></td><td>klasyfikacja</td><td>Ciąg</td><td>Klasyfikacja danych w tej kolumnie.</td></tr>

<tr><td>Dokumentacja ("Dokumentacja")</td><td></td><td></td><td>Z danym zasobem może być skojarzona tylko jedna dokumentacja.</td></tr>
<tr><td></td><td>mimeType</td><td>ciąg</td><td>Typ MIME zawartości.</td></tr>
<tr><td></td><td>zawartość</td><td>ciąg</td><td>Zawartość dokumentacji.</td></tr>

</table>

### <a name="common-types"></a>Typy wspólne
Typy wspólne mogą służyć jako typy właściwości, ale nie są elementami.

<table>
<tr><td><b>Typ wspólny</b></td><td><b>Właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>ciąg</td><td>Opisuje typ źródła danych.  Na przykład: SQL Server, Oracle Database itd.  </td></tr>
<tr><td></td><td>objectType</td><td>ciąg</td><td>Opisuje typ obiektu w źródle danych. Na przykład: tabela, widok dla SQL Server.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protokol</td><td>ciąg</td><td>Wymagane. Opisuje protokół używany do komunikacji ze źródłem danych. Na przykład: `tds` dla SQL Server, `oracle` dla Oracle itd. Zapoznaj się ze [specyfikacją odwołania do źródła danych — strukturą DSL](data-catalog-dsr.md) dla listy aktualnie obsługiwanych protokołów.</td></tr>
<tr><td></td><td>adres</td><td>&lt;Ciąg słownika, obiekt&gt;</td><td>Wymagane. Address to zestaw danych specyficznych dla protokołu, który jest używany do identyfikowania źródła danych, do którego się odwołuje. Dane adresowe z zakresu określonego protokołu, co oznacza, że jest to bez znajomości protokołu.</td></tr>
<tr><td></td><td>uwierzytelnianie</td><td>ciąg</td><td>Opcjonalny. Schemat uwierzytelniania używany do komunikacji ze źródłem danych. Na przykład: Windows, OAuth itp.</td></tr>
<tr><td></td><td>connectionProperties</td><td>&lt;Ciąg słownika, obiekt&gt;</td><td>Opcjonalny. Dodatkowe informacje na temat nawiązywania połączenia ze źródłem danych.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>Zaplecze nie wykonuje żadnej weryfikacji podanych właściwości dla Azure Active Directory podczas publikowania.</td></tr>
<tr><td></td><td>głównej</td><td>ciąg</td><td>Unikatowy adres e-mail użytkownika. Musi być określony, jeśli objectId nie został podany lub w kontekście właściwości "lastRegisteredBy", w przeciwnym razie opcjonalny.</td></tr>
<tr><td></td><td>objectId</td><td>Guid (identyfikator GUID)</td><td>Użytkownik lub grupa zabezpieczeń Azure Active Directory tożsamość. Opcjonalny. Należy określić, jeśli nie podano nazwy UPN, w przeciwnym razie opcjonalnie.</td></tr>
<tr><td></td><td>firstName</td><td>ciąg</td><td>Imię użytkownika (na potrzeby wyświetlania). Opcjonalny. Prawidłowy tylko w kontekście właściwości "lastRegisteredBy". Nie można określić w przypadku podawania podmiotu zabezpieczeń dla "ról", "uprawnień" i "ekspertów".</td></tr>
<tr><td></td><td>lastName</td><td>ciąg</td><td>Nazwisko użytkownika (na potrzeby wyświetlania). Opcjonalny. Prawidłowy tylko w kontekście właściwości "lastRegisteredBy". Nie można określić w przypadku podawania podmiotu zabezpieczeń dla "ról", "uprawnień" i "ekspertów".</td></tr>

<tr><td>Kolumna</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>ciąg</td><td>Nazwa kolumny lub atrybutu.</td></tr>
<tr><td></td><td>typ</td><td>ciąg</td><td>Typ danych kolumny lub atrybutu. Dozwolone typy zależą od sourceType danych elementu zawartości.  Obsługiwany jest tylko podzbiór typów.</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>Maksymalna dozwolona długość kolumny lub atrybutu. Pochodzące ze źródła danych. Dotyczy tylko niektórych typów źródłowych.</td></tr>
<tr><td></td><td>precision</td><td>byte</td><td>Precyzja dla kolumny lub atrybutu. Pochodzące ze źródła danych. Dotyczy tylko niektórych typów źródłowych.</td></tr>
<tr><td></td><td>isNullable</td><td>Wartość logiczna</td><td>Określa, czy kolumna może mieć wartość null, czy nie. Pochodzące ze źródła danych. Dotyczy tylko niektórych typów źródłowych.</td></tr>
<tr><td></td><td>expression</td><td>ciąg</td><td>Jeśli wartość jest kolumną obliczeniową, to pole zawiera wyrażenie, które wyraża wartość. Pochodzące ze źródła danych. Dotyczy tylko niektórych typów źródłowych.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>nazwa_kolumny </td><td>ciąg</td><td>Nazwa kolumny.</td></tr>
<tr><td></td><td>typ </td><td>ciąg</td><td>Typ kolumny</td></tr>
<tr><td></td><td>min </td><td>ciąg</td><td>Minimalna wartość w zestawie danych</td></tr>
<tr><td></td><td>max </td><td>ciąg</td><td>Maksymalna wartość w zestawie danych</td></tr>
<tr><td></td><td>śr </td><td>double</td><td>Średnia wartość w zestawie danych</td></tr>
<tr><td></td><td>STDEV </td><td>double</td><td>Odchylenie standardowe dla zestawu danych</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Liczba wartości null w zestawie danych</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>Liczba różnych wartości w zestawie danych</td></tr>
</table>

## <a name="asset-identity"></a>Tożsamość zasobu
Azure Data Catalog używa "protokołu" i właściwości tożsamości z zbioru właściwości "Address" właściwości "DSL" DataSourceLocation, aby wygenerować tożsamość elementu zawartości, który jest używany do adresowania zasobu w katalogu.
Na przykład protokół strumienia danych tabelarycznych (TDS) ma właściwości tożsamości "serwer", "baza danych", "schemat" i "Object". Kombinacje protokołu i właściwości tożsamości są używane do generowania tożsamości zasobu tabeli SQL Server.
Azure Data Catalog udostępnia kilka wbudowanych protokołów źródła danych, które są wymienione w [specyfikacji odwołania do źródła danych — struktura DSL](data-catalog-dsr.md).
Zestaw obsługiwanych protokołów można rozszerzyć programowo (zobacz Dokumentacja interfejsu API REST Data Catalog). Administratorzy wykazu mogą rejestrować niestandardowe protokoły źródła danych. W poniższej tabeli opisano właściwości, które są konieczne do zarejestrowania niestandardowego protokołu.

### <a name="custom-data-source-protocol-specification"></a>Specyfikacja niestandardowego protokołu źródła danych
<table>
<tr><td><b>Typ</b></td><td><b>Właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namespace</td><td>ciąg</td><td>Przestrzeń nazw protokołu. Przestrzeń nazw musi mieć długość od 1 do 255 znaków, zawierać co najmniej jedną niepustą część oddzieloną kropką (.). Każda część musi mieć długość od 1 do 255 znaków, rozpoczynać się od litery i może zawierać tylko litery i cyfry.</td></tr>
<tr><td></td><td>name</td><td>ciąg</td><td>Nazwa protokołu. Nazwa musi mieć długość od 1 do 255 znaków, rozpoczynać się od litery i może zawierać tylko litery, cyfry i znak kreski (-).</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>Lista właściwości tożsamości musi zawierać co najmniej jedną, ale nie więcej niż 20 właściwości. Na przykład: "serwer", "baza danych", "schemat", "Object" są właściwościami tożsamości protokołu "TDS".</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>Lista zestawów tożsamości. Definiuje zestawy właściwości tożsamości, które reprezentują prawidłową tożsamość elementu zawartości. Musi zawierać co najmniej jeden, ale nie więcej niż 20 zestawów. Na przykład: {"Server", "Database", "Schema" i "Object"} to tożsamość ustawiona dla protokołu TDS, która definiuje tożsamość elementu SQL Server tabeli.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>ciąg</td><td>Nazwa właściwości. Nazwa musi mieć długość od 1 do 100 znaków, rozpoczynać się od litery i może zawierać tylko litery i cyfry.</td></tr>
<tr><td></td><td>typ</td><td>ciąg</td><td>Typ właściwości. Obsługiwane wartości: "bool", Boolean "," Byte "," GUID "," int "," Integer "," Long "," String "," URL "</td></tr>
<tr><td></td><td>ignoreCase</td><td>bool</td><td>Wskazuje, czy przypadek ma być ignorowany podczas korzystania z wartości właściwości. Można określić tylko dla właściwości typu "String". Wartość domyślna to false.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool []</td><td>Wskazuje, czy przypadek ma być ignorowany dla każdego segmentu ścieżki adresu URL. Można określić tylko dla właściwości z typem "URL". Wartość domyślna to [false].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>ciąg</td><td>Nazwa zestawu tożsamości.</td></tr>
<tr><td></td><td>properties</td><td>ciąg []</td><td>Lista właściwości tożsamości uwzględnionych w tym zestawie tożsamości. Nie może zawierać duplikatów. Każda właściwość, do której odwołuje się zestaw tożsamości, musi być zdefiniowana na liście "identityProperties" protokołu.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Role i autoryzacja
Microsoft Azure Data Catalog udostępnia funkcje autoryzacji dla operacji CRUD na zasobach i adnotacjach.

Azure Data Catalog używa dwóch mechanizmów autoryzacji:

* Autoryzacja oparta na rolach
* Autoryzacja oparta na uprawnieniach

### <a name="roles"></a>Role
Istnieją trzy role: **administrator**, **właściciel** i **współautor**.  Każda rola ma swój zakres i prawa, które zostały podsumowane w poniższej tabeli.

<table><tr><td><b>Role</b></td><td><b>Zakres</b></td><td><b>Rights</b></td></tr><tr><td>Administrator</td><td>Katalog (wszystkie elementy zawartości/adnotacje w wykazie)</td><td>Odczytaj Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Właściciel</td><td>Każdy zasób (element główny)</td><td>Odczytaj Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Współautor</td><td>Każdy indywidualny element zawartości i adnotacja</td><td>Odczytaj aktualizację Delete ViewRoles Uwaga: wszystkie prawa są odwoływane, jeśli prawo odczyt z elementu zostało odwołane z współautora</td></tr></table>

> [!NOTE]
> Prawa do **odczytu**, **aktualizacji**, **usuwania**, **ViewRoles** mają zastosowanie do dowolnych elementów (zasobów lub adnotacji), natomiast **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** są stosowane tylko do głównego elementu zawartości.
> 
> Polecenie **Usuń** prawo dotyczy elementu oraz wszystkich elementów podrzędnych lub pojedynczych elementów poniżej. Na przykład usunięcie elementu zawartości spowoduje również usunięcie wszelkich adnotacji dla tego elementu zawartości.
> 

### <a name="permissions"></a>Uprawnienia
Uprawnienie jest listą wpisów kontroli dostępu. Każdy wpis kontroli dostępu przypisuje zestaw praw do podmiotu zabezpieczeń. Uprawnienia można określić tylko dla zasobu (czyli elementu głównego) i zastosować je do elementów zawartości i dowolnych podelementów.

W wersji zapoznawczej **Azure Data Catalog** tylko do **odczytu** jest obsługiwana na liście uprawnień, aby umożliwić scenariusz ograniczenia widoczności elementu zawartości.

Domyślnie każdy uwierzytelniony użytkownik ma prawo **odczytywać** każdy element w wykazie, chyba że widoczność jest ograniczona do zestawu podmiotów zabezpieczeń w uprawnieniach.

## <a name="rest-api"></a>Interfejs API REST
Żądania **Put** i **po** wyświetleniu elementów mogą służyć do kontrolowania ról i uprawnień: oprócz ładunku elementu, dwie właściwości systemu mogą być określone **rolami** i **uprawnieniami**.

> [!NOTE]
> **uprawnienia** mające zastosowanie tylko do elementu głównego.
> 
> Rola **właściciela** ma zastosowanie tylko do elementu głównego.
> 
> Domyślnie po utworzeniu elementu w katalogu jego **współautor** jest ustawiony na aktualnie uwierzytelniony użytkownik. Jeśli element powinien być aktualizowalny przez wszystkich użytkowników, **współautor** powinien zostać ustawiony na wartość &lt; Wszyscy &gt; specjalny podmiot zabezpieczeń w właściwości **role** , gdy element jest po raz pierwszy opublikowany (zobacz Poniższy przykład). Nie można zmienić **współautora** i pozostaje taki sam podczas okresu istnienia elementu (nawet **administrator** lub **właściciel** nie ma prawa do zmiany **współautora**). Jedyną wartością obsługiwaną przez jawne ustawienie **współautor** jest &lt; Wszyscy &gt; : **współautor** może być tylko użytkownikiem, który utworzył element lub &lt; Wszyscy &gt; .
> 

### <a name="examples"></a>Przykłady
**Ustaw współautora na &lt; wszystkich &gt; podczas publikowania elementu.**
Specjalny podmiot zabezpieczeń &lt; każdy &gt; ma identyfikator objectId "00000000-0000-0000-0000-000000000201".
  **Publikuj** https: \/ /API.azuredatacatalog.com/Catalogs/default/views/Tables/?API-Version=2016-03-30

> [!NOTE]
> Niektóre implementacje klienta HTTP mogą automatycznie ponownie wydać żądania w odpowiedzi na 302 z serwera, ale zazwyczaj nagłówki autoryzacji paska z żądania. Ponieważ nagłówek autoryzacji jest wymagany do żądania Azure Data Catalog, należy upewnić się, że nagłówek autoryzacji jest nadal podany podczas ponownego wydawania żądania do lokalizacji przekierowania określonej przez Azure Data Catalog. Następujący przykładowy kod demonstruje przy użyciu obiektu .NET HttpWebRequest.
> 

**Treść**
```json
    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }
```

  **Przypisywanie właścicieli i ograniczanie widoczności dla istniejącego elementu głównego**: **Put** https: \/ /API.azuredatacatalog.com/Catalogs/default/views/Tables/042297b0...1be45ecd462a?API-Version=2016-03-30

```json
    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }
```

> [!NOTE]
> W polu nie musisz określać ładunku elementu w treści: funkcji PUT można używać do aktualizowania tylko ról i/lub uprawnień.
> 

## <a name="next-steps"></a>Następne kroki
[Dokumentacja interfejsu API REST Azure Data Catalog](/rest/api/datacatalog/)
