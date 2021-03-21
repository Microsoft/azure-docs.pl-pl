---
title: Niezmienny magazyn obiektów Blob — Azure Storage
description: Usługa Azure Storage oferuje ROBAKa (jednokrotne pisanie, odczyt wielu) obsługi magazynu obiektów BLOB (Object), który umożliwia użytkownikom przechowywanie danych w niemodyfikowalnym stanie dla określonego interwału.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: 8d04d1bd758480ec33a7480e4045d28ed750f22e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102430942"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Przechowywanie danych obiektów blob kluczowych dla działalności za pomocą magazynu niezmiennego

Niezmienny magazyn usługi Azure Blob Storage umożliwia użytkownikom przechowywanie obiektów danych o kluczowym znaczeniu dla firmy w ROBAKu (zapis jeden raz, odczyt wielu). Ten stan sprawia, że dane nie są wymazywane i nie są modyfikowane dla interwału określonego przez użytkownika. W czasie trwania interwału przechowywania obiekty blob mogą być tworzone i odczytywane, ale nie można ich modyfikować ani usuwać. Niezmienny magazyn jest dostępny dla kont ogólnego przeznaczenia w wersji 1, w wersji 2, BlobStorage i BlockBlobStorage we wszystkich regionach świadczenia usługi Azure.

Aby uzyskać informacje na temat sposobu ustawiania i czyszczenia lub tworzenia zasad przechowywania opartych na czasie za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, zobacz [Set and Manage niezmienności Policy for BLOB Storage](storage-blob-immutability-policies-manage.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="about-immutable-blob-storage"></a>Informacje o niezmiennym magazynie obiektów BLOB

Niezmienny magazyn ułatwia organizacjom opieki zdrowotnej, instytucjom finansowym i powiązanym branżom, a &mdash; w szczególności organizację dealerów brokera &mdash; do bezpiecznego przechowywania danych. W każdym scenariuszu można również użyć niezmiennego magazynu, aby chronić krytyczne dane przed modyfikacją lub usunięciem.

Typowe zastosowania tej funkcji to:

- **Zgodność z przepisami**: niezmienny magazyn dla usługi Azure Blob Storage ułatwia organizacjom adresowanie w sek. 4 (f), CFTC 1.31 (d), FINRA i innych. Oficjalny dokument techniczny według Cohasset kojarzy szczegóły dotyczące tego, w jaki sposób magazyny te wymagania prawne są pobierane za pośrednictwem [portalu zaufania usługi firmy Microsoft](https://aka.ms/AzureWormStorage). [Centrum zaufania Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) zawiera szczegółowe informacje dotyczące naszych certyfikatów zgodności.

- **Bezpieczne przechowywanie dokumentów**: niezmienny magazyn dla usługi Azure Blob Storage gwarantuje, że dane nie mogą być modyfikowane ani usuwane przez żadnego użytkownika, w tym użytkowników z uprawnieniami administracyjnymi konta.

- **Wstrzymanie** z przyczyn prawnych: niezmienny magazyn dla usługi Azure Blob Storage umożliwia użytkownikom przechowywanie poufnych informacji, które są istotne dla sporu lub użycia biznesowego w stanie nieprawidłowego potwierdzenia przez czas trwania wstrzymania. Ta funkcja nie jest ograniczona tylko do przypadków użycia prawnego, ale można również traktować ją jako blokadę opartą na zdarzeniach lub blokadę przedsiębiorstwa, w której wymagane jest ochronę danych na podstawie wyzwalaczy zdarzeń lub zasad firmowych.

Niezmienny magazyn obsługuje następujące funkcje:

- **[Obsługa zasad przechowywania opartych na czasie](#time-based-retention-policies)**: użytkownicy mogą ustawiać zasady w celu przechowywania danych przez określony interwał. W przypadku ustawienia zasad przechowywania opartych na czasie obiekty blob można tworzyć i odczytywać, ale nie modyfikować ani usuwać. Po upływie okresu przechowywania obiekty blob mogą być usuwane, ale nie są zastępowane.

- **[Pomoc techniczna dotycząca zasad wstrzymania](#legal-holds)**: Jeśli Interwał przechowywania nie jest znany, użytkownicy mogą ustawić blokady prawne na przechowywanie niemodyfikowalnych danych do momentu wyczyszczenia blokady prawnej.  W przypadku ustawienia zasad wstrzymania, obiekty blob można tworzyć i odczytywać, ale nie modyfikować ani usuwać. Każde wstrzymanie prawne jest skojarzone ze zdefiniowanym przez użytkownika tagiem alfanumerycznym (np. IDENTYFIKATORem przypadku, nazwą zdarzenia itp.), który jest używany jako ciąg identyfikatora. 

- **Obsługa wszystkich warstw magazynowania obiektów blob**: Zasady WORM są niezależne od warstwy magazynowania w usłudze Azure Blob Storage i są stosowane do wszystkich warstw: gorącej, chłodnej i archiwum. Użytkownicy mogą przesyłać dane do warstwy najbardziej zoptymalizowanej pod kątem ich obciążeń, jednocześnie gwarantując ich niezmienność.

- **Konfiguracja na poziomie kontenera**: użytkownicy mogą konfigurować zasady przechowywania oparte na czasie i znaczniki prawne na poziomie kontenera. Dzięki korzystaniu z prostych ustawień na poziomie kontenera użytkownicy mogą tworzyć i blokować zasady przechowywania na podstawie czasu, wydłużać okresy przechowywania, ustawiać i usuwać stan archiwizacji ze względów prawnych itp. Te zasady są stosowane do wszystkich obiektów blob w kontenerze — zarówno istniejących, jak i nowych. W przypadku konta z włączoną usługą SNS te zasady są również stosowane do wszystkich katalogów w kontenerze.

- **Obsługa rejestrowania inspekcji**: Każdy kontener zawiera dziennik inspekcji zasad. Pokazuje do siedmiu poleceń przechowywania opartych na czasie dla zablokowanych zasad przechowywania opartych na czasie i zawiera identyfikator użytkownika, Typ polecenia, sygnatury czasowe i Interwał przechowywania. W przypadku archiwizacji ze względów prawnych dziennik zawiera identyfikator użytkownika, typ polecenia, znaczniki czasu oraz tagi archiwizacji ze względów prawnych. Ten dziennik jest zachowywany przez okres istnienia zasad, zgodnie z wytycznymi dotyczącymi przepisów w zakresie s 17a-4 (f). [Dziennik aktywności platformy Azure](../../azure-monitor/essentials/platform-logs-overview.md) zawiera bardziej obszerny dziennik wszystkich działań płaszczyzny kontroli; podczas włączania [dzienników zasobów platformy Azure](../../azure-monitor/essentials/platform-logs-overview.md) są zachowywane i wyświetlane operacje płaszczyzny danych. Przechowywanie tych dzienników w sposób trwały, zgodnie z obowiązującymi wymogami prawnymi lub innymi, należy do obowiązków użytkownika.

## <a name="how-it-works"></a>Jak to działa

Funkcja magazynu niezmiennego w usłudze Azure Blob Storage obsługuje dwa rodzaje zasad WORM (nazywanych też zasadami magazynu niezmiennego): zasady przechowywania na podstawie czasu i zasady archiwizacji ze względów prawnych. Gdy zasady przechowywania oparte na czasie lub wstrzymanie prawne są stosowane w kontenerze, wszystkie istniejące obiekty blob są przenoszone do niezmiennego stanu ROBAKa w mniej niż 30 sekund. Wszystkie nowe obiekty blob przekazane do tego kontenera chronionego przez zasady również będą przenoszone do niezmiennego stanu. Gdy wszystkie obiekty blob są w niezmiennym stanie, niezmienne zasady są potwierdzane i wszelkie operacje zastępowania lub usuwania w niezmiennym kontenerze są niedozwolone. W przypadku konta z obsługą usługi SNS nie można zmienić nazwy obiektów BLOB ani przenieść ich do innego katalogu.

Usunięcie kontenera i konta magazynu nie jest dozwolone, jeśli istnieją obiekty blob w kontenerze, które są chronione przez blokadę prawną lub zablokowanych zasad czasu. Zasady wstrzymania ochrony są chronione przed usunięciem obiektów blob, kontenerów i kont magazynu. Zarówno odblokowane, jak i zablokowane zasady oparte na czasie będą chronić przed usunięciem obiektów BLOB przez określony czas. Zarówno odblokowane, jak i zablokowane zasady oparte na czasie będą chronić przed usunięciem kontenera tylko wtedy, gdy istnieje co najmniej jeden obiekt BLOB w kontenerze. Tylko kontener z *zablokowanymi* zasadami opartymi na czasie będzie chronić przed usuwaniem kont magazynu; Kontenery z odblokowanymi zasadami dotyczącymi czasu nie oferują ochrony usuwania kont magazynu ani zgodności.

Na poniższym diagramie przedstawiono sposób, w jaki zasady przechowywania oparte na czasie i informacje prawne uniemożliwiają wykonywanie operacji zapisu i usuwania, gdy są one obowiązujące.

:::image type="content" source="media/storage-blob-immutable-storage/worm-diagram.png" alt-text="Diagram przedstawiający sposób, w jaki zasady przechowywania i blokady prawne uniemożliwiają operacje zapisu i usuwania":::

Aby uzyskać więcej informacji na temat sposobu ustawiania i blokowania zasad przechowywania opartych na czasie, zobacz [Ustawianie zasad niezmienności dla usługi BLOB Storage i zarządzanie nimi](storage-blob-immutability-policies-manage.md).

## <a name="time-based-retention-policies"></a>Zasady przechowywania na podstawie czasu

> [!IMPORTANT]
> Zasady przechowywania oparte na czasie muszą być *zablokowane* , aby obiekt BLOB był zgodny z niezmiennym (zapisem i usuwanie chronione) dla sek. 4 (f) i innych zgodności z przepisami. Zalecamy zablokowanie zasad w rozsądnym czasie, zwykle krótszym niż 24 godziny. Początkowy stan zastosowanych zasad przechowywania oparty na czasie jest *odblokowany*, co pozwala na testowanie funkcji i wprowadzanie zmian zasad przed ich zablokowaniem. Gdy *odblokowany* stan zapewnia ochronę niezmienności, nie zalecamy używania *niezablokowanego* stanu do celów innych niż krótkoterminowe wersje próbne funkcji. 

Gdy zasady przechowywania oparte na czasie są stosowane w kontenerze, wszystkie obiekty blob w kontenerze pozostaną w niezmiennym stanie przez okres *obowiązywania obowiązującego* okresu przechowywania. Obowiązujący okres przechowywania obiektów BLOB jest równy różnicy między **czasem utworzenia** obiektu BLOB a interwałem przechowywania określonym przez użytkownika. Ponieważ użytkownicy mogą rozszerzać okres przechowywania, na potrzeby obliczania obowiązującego okresu przechowywania magazyn niezmienny korzysta z ostatniej wartości okresu przechowywania ustawionej przez użytkownika.

Załóżmy na przykład, że użytkownik tworzy zasady przechowywania oparte na czasie z interwałem przechowywania równym pięciu lat. Istniejący obiekt BLOB w tym kontenerze, _testblob1_, został utworzony jeden rok temu; w związku z tym obowiązuje okres przechowywania dla _testblob1_ wynosi cztery lata. Po przekazaniu nowego obiektu BLOB _testblob2_ do kontenera obowiązuje okres przechowywania dla _testblob2_ wynosi pięć lat od momentu jego utworzenia.

Odblokowanie zasad przechowywania opartych na czasie jest zalecane tylko w przypadku testowania funkcji, a zasady muszą być zablokowane, aby były zgodne z godziną 4. (f) i innymi zgodnościami z przepisami. Gdy zasady przechowywania oparte na czasie są zablokowane, nie można usunąć zasad i będzie dozwolone maksymalnie pięć zwiększenie okresu obowiązywania.

Zasady przechowywania dotyczą następujących ograniczeń:

- W przypadku konta magazynu Maksymalna liczba kontenerów z niezmiennymi zasadami opartymi na czasie wynosi 10 000.
- Minimalny interwał przechowywania to 1 dzień. Wartość maksymalna to 146 000 dni (400 lat).
- W przypadku kontenera Maksymalna liczba zmian w celu zwiększenia interwału przechowywania dla zabudowanych, opartych na czasie limitów zasad wynosi 5.
- W przypadku kontenera do zablokowanych zasad są zachowywane maksymalnie siedem dzienników inspekcji zasad przechowywania opartych na czasie.

### <a name="allow-protected-append-blobs-writes"></a>Zezwalaj na chronione operacje dołączania obiektów BLOB

Dołączane obiekty blob składają się z bloków danych i są zoptymalizowane pod kątem operacji dołączania danych wymaganych przez scenariusze inspekcji i rejestrowania. Po zaprojektowaniu, dołączanie obiektów BLOB zezwala tylko na dodanie nowych bloków do końca obiektu BLOB. Niezależnie od niezmienności, modyfikowanie lub usuwanie istniejących bloków w dołączanym obiekcie BLOB jest zasadniczo niedozwolone. Aby dowiedzieć się więcej na temat dołączania obiektów blob, zobacz [temat Dodawanie obiektów BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs).

Tylko zasady przechowywania oparte na czasie mają `allowProtectedAppendWrites` ustawienie, które umożliwia zapisanie nowych bloków do obiektu BLOB dołączania przy zachowaniu ochrony i zgodności niezmienności. Jeśli to ustawienie jest włączone, można utworzyć obiekt BLOB dołączania bezpośrednio w kontenerze chronionym przez zasady i kontynuować dodawanie nowych bloków danych na końcu istniejących dołączanych obiektów BLOB przy użyciu interfejsu API *AppendBlock* . Można dodawać tylko nowe bloki i nie można modyfikować ani usuwać żadnych istniejących bloków. Nadal obowiązuje ochrona niezmiennościa czasu przechowywania, uniemożliwiając usuwanie dołączanego obiektu BLOB do czasu, aż upłynie okres przechowywania. Włączenie tego ustawienia nie wpływa na zachowanie niezmienności blokowych obiektów blob lub stronicowych obiektów BLOB.

Ponieważ to ustawienie jest częścią zasad przechowywania opartych na czasie, Dodawanie obiektów BLOB nadal pozostaje w niezmiennym stanie przez okres *obowiązywania obowiązującego* okresu przechowywania. Ponieważ nowe dane mogą być dołączane poza początkowym tworzeniem obiektu BLOB dołączania, istnieje niewielka różnica w sposobie określania okresu przechowywania. Efektywne przechowywanie jest różnicą między **czasem ostatniej modyfikacji** obiektu BLOB i interwałem przechowywania określonym przez użytkownika. Podobnie gdy Interwał przechowywania jest rozszerzony, niezmienny magazyn używa najnowszej wartości interwału przechowywania określonego przez użytkownika, aby obliczyć obowiązujący okres przechowywania.

Załóżmy na przykład, że użytkownik tworzy zasady przechowywania oparte na czasie z `allowProtectedAppendWrites` włączonym i interwałem przechowywania wynoszącym 90 dni. W kontenerze zostanie utworzony obiekt BLOB append, _logblob1_, nowe dzienniki są nadal dodawane do dołączanego obiektu BLOB przez następne 10 dni; w związku z tym obowiązuje okres przechowywania _logblob1_ przez 100 dni od dzisiaj (godzina ostatniego dołączenia + 90 dni).

Zablokowane zasady przechowywania oparte na czasie umożliwiają `allowProtectedAppendWrites` włączenie i wyłączenie ustawienia w dowolnym momencie. Po zablokowaniu zasad przechowywania na podstawie czasu `allowProtectedAppendWrites` nie można zmienić tego ustawienia.

Zasady wstrzymania prawnego nie mogą być włączone `allowProtectedAppendWrites` , a żadne blokady prawne będą zniesienia Właściwość "allowProtectedAppendWrites". Jeśli dla zasad przechowywania opartych na czasie z włączonym dodatkiem zostanie zastosowana Blokada prawna `allowProtectedAppendWrites` , interfejs API *AppendBlock* zakończy się niepowodzeniem do momentu zniesienia prawnego wstrzymania.

## <a name="legal-holds"></a>Archiwizacja ze względów prawnych

Z przyczyn prawnych są tymczasowe, które mogą być używane do celów badania prawnego lub ogólnych zasad ochrony. Każda z zasad blokady prawnej musi być skojarzona z co najmniej jednym tagiem. Tagi są używane jako nazwane identyfikatory, takie jak identyfikator przypadku lub zdarzenie, do kategoryzowania i opisywania celu wstrzymania.

Kontener może jednocześnie mieć zarówno dozwoloną blokadę, jak i zasady przechowywania oparte na czasie. Wszystkie obiekty blob w tym kontenerze pozostają w stanie niezmiennym do momentu usunięcia stanu archiwizacji ze względów prawnych nawet po upływie obowiązującego okresu przechowywania. I odwrotnie — obiekty blob pozostają w stanie niezmiennym przez cały obowiązujący okres przechowywania nawet po usunięciu stanu archiwizacji ze względów prawnych.

Następujące ograniczenia mają zastosowanie do posiadanych przepisów prawnych:

- W przypadku konta magazynu Maksymalna liczba kontenerów z ustawieniem wstrzymania prawnego to 10 000.
- W przypadku kontenera Maksymalna liczba dozwolonych tagów w blokadzie wynosi 10.
- Minimalna długość urzędowego tagu blokady to trzy znaki alfanumeryczne. Maksymalna długość to 23 znaków alfanumerycznych.
- W przypadku kontenera Maksymalna liczba dzienników inspekcji zasad dozwolonych w ramach systemu 10 jest zachowywana na czas trwania zasad.

## <a name="scenarios"></a>Scenariusze

W poniższej tabeli przedstawiono typy operacji magazynu obiektów blob, które są wyłączone dla różnych niezmiennych scenariuszy. Aby uzyskać więcej informacji, zobacz dokumentację [interfejsu API REST usługi Azure Blob Service](/rest/api/storageservices/blob-service-rest-api) .

| Scenariusz | Stan obiektu BLOB | Odrzucone operacje obiektu BLOB | Ochrona kontenera i konta |
|--|--|--|--|
| Trwa obowiązujący okres przechowywania obiektu blob i/lub ustawiono stan archiwizacji ze względów prawnych | Niezmienny: ochrona przed usuwaniem i zapisem | Put obiekt BLOB<sup>1</sup>, Put blok<sup>1</sup>, Put lista zablokowanych<sup>1</sup>, usuwanie kontenera, usuwanie obiektu BLOB, Ustawianie metadanych obiektów blob, umieszczanie strony, Ustawianie właściwości obiektów blob, obiekt BLOB kopiowania przyrostowego, Dodawanie bloku<sup>2</sup> | Odmowa usunięcia kontenera; Odmowa usunięcia konta magazynu |
| Obowiązujący Interwał przechowywania w obiekcie blob wygasł i nie ustawiono blokady prawnej | Ochrona tylko przed zapisem (operacje usuwania są dozwolone) | Put obiekt BLOB<sup>1</sup>, Put blok<sup>1</sup>, Put Block list<sup>1</sup>, Set Metadata BLOB, Put Page, Set BLOB Properties, Snapshot BLOB, obiekt BLOB Copy, Append Block<sup>2</sup> | Odmowa usuwania kontenera, jeśli w chronionym kontenerze istnieje co najmniej 1 obiekt BLOB; Odmowa usuwania konta magazynu tylko dla *zablokowanych* zasad czasu |
| Nie zastosowano żadnych zasad ROBAKów (przechowywanie na podstawie czasu i brak tagu blokady dozwolone) | Modyfikowalny | Brak | Brak |

<sup>1</sup> usługa BLOB umożliwia wykonywanie tych operacji w celu utworzenia nowego obiektu BLOB raz. Wszystkie kolejne operacje zastępowania w istniejącej ścieżce obiektu BLOB w niezmiennym kontenerze są niedozwolone.

<sup>2</sup> blok Append jest dozwolony tylko dla zasad przechowywania opartych na czasie z `allowProtectedAppendWrites` włączoną właściwością. Aby uzyskać więcej informacji, zobacz sekcję [Zezwalaj na chronione Dodawanie obiektów BLOB](#allow-protected-append-blobs-writes) .

> [!IMPORTANT]
> Niektóre obciążenia, takie jak [kopia zapasowa SQL do adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url), tworzą obiekt BLOB, a następnie Dodaj do niego. Jeśli kontener ma aktywne zasady przechowywania oparte na czasie lub blokadę prawną, ten wzorzec nie powiedzie się.

## <a name="pricing"></a>Ceny

Za korzystanie z tej funkcji nie są naliczane dodatkowe opłaty. Zmienne dane są wyceniane w taki sam sposób jak dane modyfikowalne. Aby uzyskać szczegółowe informacje o cenach usługi Azure Blob Storage, zobacz [stronę z cennikiem usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>Często zadawane pytania

**Czy można udostępnić dokumentację zgodności z ROBAKiem?**

Tak. Aby udokumentować zgodność, firma Microsoft zachowała wiodące niezależne przedsiębiorstwo do oceny, które jest wyspecjalizowane w zakresie zarządzania rekordami i nadzoru informacji, Cohasset Associates do oceny niezmiennego magazynu obiektów blob i jego zgodności z wymaganiami specyficznymi dla branży usług finansowych. Cohasset sprawdzono, że niezmienny magazyn obiektów blob, gdy jest używany do przechowywania obiektów BLOB opartych na czasie w stanie ROBAKa, spełnia odpowiednie wymagania dotyczące magazynu CFTC Rule 1.31 (d), FINRA Rule 4511 i s reguła 17a-4. Ten zestaw reguł jest przeznaczony dla firmy Microsoft, ponieważ reprezentuje on najbardziej szczegółowe wytyczne globalne dla rekordów przechowywanych przez instytucje finansowe. Raport Cohasset jest dostępny w [Centrum zaufania usługi firmy Microsoft](https://aka.ms/AzureWormStorage). Aby zażądać list zaświadczania od firmy Microsoft dotyczących zgodności ROBAKa niezmienności, skontaktuj się z pomocą techniczną platformy Azure.

**Czy funkcja ma zastosowanie tylko do blokowania obiektów blob i dołączania obiektów blob, czy też do stronicowych obiektów BLOB?**

Niezmienny magazyn może być używany z dowolnym typem obiektu BLOB ustawionym na poziomie kontenera, ale zalecamy używanie ROBAKa dla kontenerów, które głównie przechowują blokowe obiekty blob i dołączają obiekty blob. Istniejące obiekty blob w kontenerze będą chronione przy użyciu nowo ustawionych zasad ROBAKa. Należy jednak utworzyć wszystkie nowe, stronicowe obiekty blob poza kontenerem WORM, a następnie skopiować w. Po skopiowaniu do kontenera WORM nie są dozwolone żadne dalsze zmiany w obiekcie blob strony. Ustawienie zasad ROBAKa w kontenerze, w którym są przechowywane wirtualne dyski twarde (stronicowe obiekty blob) dla wszystkich aktywnych maszyn wirtualnych jest niezalecane, ponieważ spowoduje to zablokowanie dysku maszyny wirtualnej. Zalecamy dokładne zapoznanie się z dokumentacją i przetestowanie scenariuszy przed zablokowaniem wszelkich zasad opartych na czasie.

**Czy muszę utworzyć nowe konto magazynu, aby użyć tej funkcji?**

Nie. możesz użyć niezmiennego magazynu z wszelkimi istniejącymi lub nowo utworzonymi kontami ogólnego przeznaczenia w wersji 1, BlobStorage lub BlockBlobStorage. Konta magazynu ogólnego przeznaczenia w wersji 1 są obsługiwane, ale zalecamy uaktualnienie do ogólnego przeznaczenia w wersji 2, aby móc korzystać z większej liczby funkcji. Aby uzyskać informacje dotyczące uaktualniania istniejącego konta magazynu ogólnego przeznaczenia w wersji 1, zobacz [uaktualnianie konta magazynu](../common/storage-account-upgrade.md).

**Czy mogę zastosować zasady przechowywania zarówno z przyczyn prawnych, jak i czasu?**

Tak. kontener może jednocześnie mieć zarówno dozwoloną blokadę, jak i zasady przechowywania oparte na czasie; ustawienie "allowProtectedAppendWrites" nie będzie jednak stosowane do momentu wyczyszczenia blokady z przyczyn prawnych. Wszystkie obiekty blob w tym kontenerze pozostają w stanie niezmiennym do momentu usunięcia stanu archiwizacji ze względów prawnych nawet po upływie obowiązującego okresu przechowywania. I odwrotnie — obiekty blob pozostają w stanie niezmiennym przez cały obowiązujący okres przechowywania nawet po usunięciu stanu archiwizacji ze względów prawnych. 

**Czy zasady są dozwolone tylko w przypadku postępowania sądowego lub czy istnieją inne scenariusze użycia?**

Nie, wstrzymanie prawne jest tylko ogólnym terminem używanym w odniesieniu do zasad przechowywania opartych na czasie. Nie musi być używany tylko w przypadku postępowań związanych z postępowaniem sądowym. Zasady wstrzymania prawnego są przydatne do wyłączania zastąpień i usunięć do ochrony ważnych danych ROBAKa przedsiębiorstwa, gdzie okres przechowywania jest nieznany. Możesz użyć go jako zasad przedsiębiorstwa do ochrony krytycznych obciążeń ROBAKów lub użyć go jako zasad przejściowych przed zastosowaniem przez wyzwalacz zdarzenia niestandardowego zasad przechowywania opartych na czasie. 

**Czy mogę usunąć _zablokowane_ zasady przechowywania oparte na czasie lub wstrzymać z przyczyn prawnych?**

Z kontenera można usuwać tylko nieodblokowane zasady przechowywania oparte na czasie. Gdy zasady przechowywania oparte na czasie są zablokowane, nie można ich usunąć. dozwolone są tylko rozszerzenia okresu przechowywania. Można usunąć Tagi blokady dozwolone. Gdy wszystkie Tagi prawne zostaną usunięte, wstrzymanie prawne zostanie usunięte.

**Co się stanie w przypadku próby usunięcia kontenera z zasadami przechowywania opartymi na czasie lub blokadą prawną?**

Operacja usuwania kontenera zakończy się niepowodzeniem, jeśli w kontenerze istnieje co najmniej jeden obiekt BLOB z zablokowanimi lub odblokowanymi zasadami przechowywania opartymi na czasie lub w przypadku, gdy kontener ma blokadę z przyczyn prawnych. Operacja usuwania kontenera powiedzie się tylko wtedy, gdy nie ma żadnych obiektów BLOB w kontenerze i nie ma żadnych blokad prawnych. 

**Co się stanie w przypadku próby usunięcia konta magazynu z kontenerem z zasadami przechowywania opartymi na czasie lub blokadą prawną?**

Usunięcie konta magazynu zakończy się niepowodzeniem, jeśli istnieje co najmniej jeden kontener z dozwolonym lub **zablokowanymi** zasadami czasu. Kontener z odblokowanymi zasadami czasu nie chroni przed usunięciem konta magazynu. Aby można było usunąć konto magazynu, należy usunąć wszystkie blokady prawne i usunąć wszystkie **zablokowane** kontenery. Aby uzyskać informacje na temat usuwania kontenera, zobacz poprzednie pytanie. Możesz również zastosować dalsze zabezpieczenia dotyczące usuwania dla konta magazynu, korzystając z [Azure Resource Manager blokad](../../azure-resource-manager/management/lock-resources.md).

**Czy mogę przenieść dane między różnymi warstwami obiektów BLOB (gorąca, chłodna, archiwalna), gdy obiekt BLOB jest w niezmiennym stanie?**

Tak, możesz użyć polecenia Set BLOB warstwy, aby przenieść dane między warstwami obiektów blob, zachowując dane w zgodnym niezmiennym stanie. Niezmienny magazyn jest obsługiwany w warstwach obiektów BLOB gorąca, chłodna i archiwalna.

**Co się stanie, jeśli nie uiszczę opłaty, a okres przechowywania jeszcze nie wygasł?**

W przypadku braku płatności normalne zasady przechowywania danych będą stosowane zgodnie z warunkami i postanowieniami kontraktu z firmą Microsoft. Aby uzyskać ogólne informacje, zobacz [Zarządzanie danymi w firmie Microsoft](https://www.microsoft.com/en-us/trust-center/privacy/data-management). 

**Czy jest oferowany okres próbny, umożliwiający przetestowanie tej funkcji?**

Tak. Po pierwszym utworzeniu zasad przechowywania na podstawie czasu jest ona w stanie *odblokowanym* . W tym stanie można wprowadzić dowolną żądaną zmianę interwału przechowywania, na przykład zwiększyć lub zmniejszyć, a nawet usunąć zasady. Po zablokowaniu zasad pozostaje ono zablokowane do momentu wygaśnięcia interwału przechowywania. Te zablokowane zasady uniemożliwiają usunięcie i modyfikację interwału przechowywania. Zdecydowanie zalecamy użycie *niezablokowanego* stanu tylko do celów próbnych i zablokowanie zasad w ciągu 24 godzin. Te praktyki pomagają zapewnić zgodność z SEKUNDą 17a-4 (f) i innymi przepisami.

**Czy można używać nietrwałego usuwania wraz z niezmiennymi zasadami obiektów BLOB?**

Tak, jeśli wymagania dotyczące zgodności umożliwiają włączenie usuwania nietrwałego. [Usuwanie nietrwałe dla magazynu obiektów blob platformy Azure](./soft-delete-blob-overview.md) ma zastosowanie do wszystkich kontenerów w ramach konta magazynu, niezależnie od zasad przechowywania z przyczyn prawnych lub opartych na czasie. Zalecamy włączenie usuwania nietrwałego w celu zapewnienia dodatkowej ochrony przed zastosowaniem i potwierdzeniem jakichkolwiek niemodyfikowalnych zasad ROBAKów.

**Czy w przypadku konta z obsługą usługi SNS można zmienić nazwę lub przenieść obiekt BLOB, gdy obiekt BLOB jest w stanie niezmiennym?**
Nie, zarówno nazwa, jak i struktura katalogów są uznawane za ważne dane na poziomie kontenera, których nie można modyfikować po wprowadzeniu niemodyfikowalnych zasad. Zmiany nazwy i przenoszenia są dostępne tylko dla kont z obsługą usługi SNS.

## <a name="next-steps"></a>Następne kroki

- [Ustawianie zasad niezmienności dla usługi BLOB Storage i zarządzanie nimi](storage-blob-immutability-policies-manage.md)
- [Ustaw reguły automatycznej warstwy i Usuń dane obiektów BLOB za pomocą zarządzania cyklem życia](storage-lifecycle-management-concepts.md)
- [Soft delete for Azure Storage blobs](./soft-delete-blob-overview.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)
- [Ochrona subskrypcji, grup zasobów i zasobów przy użyciu blokad Azure Resource Manager](../../azure-resource-manager/management/lock-resources.md).
