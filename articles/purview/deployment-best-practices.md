---
title: Najlepsze praktyki dotyczące wdrażania
description: W tym artykule przedstawiono najlepsze rozwiązania dotyczące wdrażania usługi Azure kontrolą. Usługa Azure kontrolą umożliwia każdemu użytkownikowi rejestrowanie, odnajdowanie i poznawanie źródeł danych oraz korzystanie z nich.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 48966e481f9cf8796c866b5c15a4e2a8616eade7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97695860"
---
# <a name="azure-purview-deployment-best-practices"></a>Najlepsze rozwiązania dotyczące wdrażania usługi Azure kontrolą

W tym artykule przedstawiono typowe zadania, które mogą pomóc w wdrożeniu kontrolą w środowisku produkcyjnym. Te zadania mogą być wykonywane w fazach, w ciągu miesiąca lub więcej. Nawet organizacje, które już wdrożyły kontrolą, mogą korzystać z tego przewodnika, aby upewnić się, że najprawdopodobniej wykorzystają one z inwestycji.

Prawidłowo planowane wdrożenie platformy do zarządzania danymi (na przykład Azure kontrolą) może mieć następujące zalety:

- Lepsze odnajdywanie danych
- Ulepszona współpraca analityczna
- Zmaksymalizowany zwrot z inwestycji.

## <a name="prerequisites"></a>Wymagania wstępne

* Dostęp do Microsoft Azure z subskrypcją programistyczną lub produkcyjną
* Możliwość tworzenia zasobów platformy Azure, w tym kontrolą
* Dostęp do źródeł danych, takich jak Azure Data Lake Storage lub Azure SQL w środowisku testowym, programistycznym lub produkcyjnym
  * W przypadku Data Lake Storage wymagana rola do skanowania jest rolą czytnika
  * W przypadku bazy danych SQL tożsamość musi być w stanie zbadać tabele na potrzeby próbkowania klasyfikacji
* Dostęp do Azure Security Center lub możliwość współpracy z administratorem Security Center w celu etykietowania danych

## <a name="identify-objectives-and-goals"></a>Zidentyfikuj cele i cele

Wiele organizacji rozpoczęło swoją drogę zarządzania danymi przez opracowywanie indywidualnych rozwiązań, które odpowiadają konkretnym wymaganiom izolowanych grup i domen danych w całej organizacji. Chociaż środowiska mogą się różnić w zależności od branży, produktu i kultury, większość organizacji utrudnia zachowanie spójnych kontroli i zasad dla tych typów rozwiązań.

Niektóre typowe cele zarządzania danymi, które można znaleźć w wczesnych fazach, obejmują:

* Maksymalizowanie wartości biznesowej danych
* Umożliwienie kulturcy danych, w której konsumenci danych mogą łatwo znajdować, interpretować i ufać danymi
* Zwiększenie współpracy między różnymi jednostkami biznesowymi w celu zapewnienia spójnego środowiska danych
* Wzmacnianie innowacji przez przyspieszenie analizy danych w celu skorzystać korzyści z chmury
* Skrócenie czasu odnajdywania danych za poorednictwem samoobsługowych opcji dla różnych grup umiejętności
* Skrócenie czasu wprowadzenia na rynek na potrzeby dostarczania rozwiązań analitycznych, które zwiększają liczbę klientów.
* Zmniejszenie zagrożeń operacyjnych spowodowanych użyciem narzędzi specyficznych dla domeny i nieobsługiwanej technologii

Ogólnie rzecz biorąc, należy podzielić te cele na różne kategorie i cele. Przykłady to:

|Kategoria|Cel|
|---------|---------|
|Odnajdywanie|Użytkownicy będący administratorami powinni mieć możliwość skanowania źródeł danych platformy Azure i innych niż platformy Azure (w tym źródeł lokalnych) w celu automatycznego zebrania informacji o zasobach danych.|
|Klasyfikacja|Platforma powinna automatycznie klasyfikować dane na podstawie próbkowania danych i zezwalać na ręczne przesłonięcie przy użyciu klasyfikacji niestandardowych.|
|Zużycie|Użytkownicy biznesowi powinni mieć możliwość znalezienia informacji o poszczególnych elementach zawartości zarówno w przypadku metadanych biznesowych, jak i technicznych.|
|Pochodzenie|Każdy element zawartości musi zawierać graficzny widok bazowych zestawów danych, dzięki czemu użytkownicy będą zrozumieć oryginalne źródła i jakie zmiany zostały wprowadzone.|
|Współpraca|Platforma musi zezwalać użytkownikom na współpracę, dostarczając dodatkowe informacje o poszczególnych zasobach danych.|
|Raportowanie|Użytkownicy muszą być w stanie wyświetlać raporty na temat obejmujący dane, w tym dane poufne i dane wymagające dodatkowego wzbogacania.|
|Ład dotyczący danych|Platforma musi zezwalać administratorowi na definiowanie zasad kontroli dostępu i automatyczne wymuszanie dostępu do danych na podstawie poszczególnych użytkowników.|
|Przepływ pracy|Platforma musi mieć możliwość tworzenia i modyfikowania przepływu pracy, aby można było łatwo skalować w poziomie i automatyzować różne zadania na platformie.|
|Integracja|Inne technologie innych firm, takie jak bilety lub aranżacja, muszą być w stanie zintegrować platformę za pośrednictwem skryptów lub interfejsów API REST.|

## <a name="top-questions-to-ask"></a>Najpopularniejsze pytania, które należy zadać

Gdy organizacja wyrazi zgodę na cele wysokiego poziomu i cele, będzie wiele pytań z wielu grup. Ważne jest, aby zebrać te pytania w celu założenia planu, aby rozwiązać wszystkie wątpliwości. Niektóre Przykładowe pytania, które można napotkać podczas fazy początkowej:

1. Jakie są główne źródła danych i systemy danych organizacji?
2. Jakie są moje opcje dla źródeł danych, które nie są jeszcze obsługiwane przez kontrolą.
3. Ile wystąpień kontrolą potrzebuje?
4. Kim są użytkownicy?
5. Kto może skanować nowe źródła danych?
6. Kto może modyfikować zawartość wewnątrz kontrolą?
7. Jakiego procesu można użyć, aby poprawić jakość danych w programie kontrolą?
8. Jak przeprowadzić Bootstrap platformy z istniejącymi, krytycznymi zasobami, terminologią i kontaktami?
9. Jak przeprowadzić integrację z istniejącymi systemami?
10. Jak zebrać opinie i utworzyć trwały proces?

Mimo że odpowiedź na większość tych pytań może nie być od razu dostępna, może pomóc organizacji w podziale tego projektu i upewnić się, że wszystkie wymagania mogą być spełnione.

## <a name="include-the-right-stakeholders"></a>Uwzględnij odpowiednie strony zainteresowane

Aby zapewnić pomyślne wdrożenie kontrolą dla całego przedsiębiorstwa, ważne jest, aby pozostały zainteresowane strony. Faza początkowa obejmuje tylko kilka osób. Jednak w miarę rozszerzania zakresu będzie wymagane dodatkowe osób do współtworzenia projektu i przesyłania opinii.

Niektórzy z najważniejszych udziałowców, którzy mogą chcieć uwzględnić:

|Osoba|Role|
|---------|---------|
|**Dyrektor ds. danych**|Składnik CDO widzi szereg funkcji, które mogą obejmować zarządzanie danymi, jakość danych, zarządzanie danymi głównymi, naukę o danych i analizę biznesową oraz strategię tworzenia danych. Mogą to być sponsor projektu implementacji kontrolą.|
|**Właściciel domeny/firmy**|Osoba biznesowa, która ma wpływ na użycie narzędzi i ma kontrolę budżetu|
|**Analityk danych**|Możliwość podzielenia problemu biznesowego i analizowania danych, aby pomóc liderom podejmować decyzje biznesowe|
|**Architekt danych**|Projektowanie baz danych dla aplikacji biznesowych o kluczowym znaczeniu oraz projektowanie i implementowanie zabezpieczeń danych|
|**Inżynier danych**|Obsługa i konserwowanie stosu danych, ściąganie danych z różnych źródeł, Integrowanie i przygotowywanie danych, Konfigurowanie potoków danych|
|**Analityk danych**|Twórz modele analityczne i Konfiguruj produkty danych, do których mają dostęp interfejsy API|
|**Administrator bazy danych**|Własne, śledź i rozwiązuj zdarzenia związane z bazą danych oraz żądania w ramach umów dotyczących poziomu usług (umowy SLA); Może skonfigurować potoki danych|
|**DevOps**|Projektowanie i wdrażanie aplikacji biznesowych; może obejmować pisanie skryptów i możliwości aranżacji|
|**Specjalista ds. zabezpieczeń danych**|Oceń ogólne zabezpieczenia sieci i danych, które obejmują dane pochodzące z kontrolą i z nich|

## <a name="identify-key-scenarios"></a>Zidentyfikuj kluczowe scenariusze

Kontrolą może służyć do centralnego zarządzania nadzorem danych w całej organizacji, obejmującą środowisko w chmurze i lokalne. Aby zapewnić pomyślną implementację, należy zidentyfikować kluczowe scenariusze, które mają kluczowe znaczenie dla firmy. Te scenariusze mogą przekraczać granice jednostek biznesowej lub mieć wpływ na wielu użytkowników osób albo nadrzędnych, jak i podrzędnych.

Te scenariusze można napisać na różne sposoby, ale należy uwzględnić co najmniej następujące pięć wymiarów:

1. Osoba-użytkownicy?
2. System źródłowy — jakie są źródła danych, takie jak Azure Data Lake Storage Gen2 czy Azure SQL Database?
3. Obszar wpływu — co to jest kategoria tego scenariusza?
4. Scenariusze szczegółowe — jak użytkownicy używają kontrolą do rozwiązywania problemów?
5. Oczekiwany wynik — jakie są kryteria sukcesu?

Scenariusze muszą być specyficzne, funkcjonalne i wykonywalne z wymiernymi wynikami. Przykładowe scenariusze, których można użyć:

|Scenariusz|Szczegóły|Osoba|
|---------|---------|---------|
|Zasoby o kluczowym znaczeniu dla firmy|Muszę mieć informacje o poszczególnych zestawach danych, aby dobrze zrozumieć ich znaczenie. Ten scenariusz obejmuje dane biznesowe i techniczne dotyczące zestawu danych w wykazie. Źródła danych obejmują Azure Data Lake Storage Gen2, Azure Synapse DW i/lub Power BI. Ten scenariusz obejmuje również zasób lokalny, taki jak SQL Server.|Analityk biznesowy, Naukowc danych, inżynier ds. danych|
|Odkryj zasoby o krytycznym znaczeniu dla firmy|Muszę mieć wyszukiwarkę, która może przeszukiwać wszystkie metadane w wykazie. Muszę mieć możliwość wyszukiwania przy użyciu warunków technicznych, warunków roboczych z prostym lub złożonym wyszukiwaniem przy użyciu symboli wieloznacznych.|Analityk biznesowy, Naukowc danych, inżynier danych, administrator danych|
|Śledzenie danych w celu zrozumienia ich pochodzenia i rozwiązywania problemów z danymi|Muszę mieć dane odłożenia, aby śledzić dane w raportach, prognozach lub modelach z powrotem do oryginalnego źródła i zrozumieć zmiany oraz miejsce, w którym dane znajdują się w cyklu życia danych. W tym scenariuszu należy obsługiwać priorytetowe potoki danych Azure Data Factory i kostki.|Inżynier danych, analityk danych|
|Wzbogacanie metadanych o krytyczne zasoby danych|Muszę wzbogacać zestaw danych z wykazu o metadane techniczne, które są generowane automatycznie. Przykłady klasyfikacji i etykietowania.|Inżynier ds. danych, domena/właściciel firmy|
|Zarządzanie zasobami danych przy użyciu przyjaznego środowiska użytkownika|Muszę mieć słownik biznesowy dla metadanych specyficznych dla firmy. Użytkownicy biznesowi mogą używać kontrolą do scenariuszy samoobsługi w celu dodawania adnotacji do danych i umożliwiania łatwego odnajdowania danych za pośrednictwem wyszukiwania.|Właściciel domeny/firmy, analityk biznesowy, Naukowc danych, inżynier danych|

## <a name="deployment-models"></a>Modele wdrażania

Jeśli masz tylko jedną małą grupę używającą kontrolą z podstawowymi przypadkami użycia, podejście może być proste, ponieważ jedno wystąpienie kontrolą do obsługi całej grupy. Można jednak również zazastanawiać się, czy organizacja wymaga więcej niż jednego wystąpienia kontrolą. A jeśli korzystasz z wielu wystąpień kontrolą, w jaki sposób pracownicy mogą promować zasoby z jednego etapu do drugiego.

### <a name="determine-the-number-of-purview-instances"></a>Określanie liczby wystąpień kontrolą

W większości przypadków powinno istnieć tylko jedno konto kontrolą dla całej organizacji. Takie podejście wykorzystuje maksymalną korzyść "efektów sieciowych", gdzie wartość platformy rośnie wykładniczo jako funkcję danych, które znajdują się wewnątrz platformy.

Istnieją jednak wyjątki dla tego wzorca:

1. **Testowanie nowych konfiguracji** — organizacje mogą chcieć utworzyć wiele wystąpień do testowania konfiguracji skanowania lub klasyfikacji w środowiskach izolowanych. Chociaż w niektórych obszarach platformy, takich jak słownik, jest dostępna funkcja "Obsługa wersji", łatwiej będzie przetestować "jednorazowe" wystąpienie.
2. **Oddzielanie testów, przedprodukcyjnych i produkcyjnych** — organizacje chcą tworzyć różne platformy dla różnych rodzajów danych przechowywanych w różnych środowiskach. Nie jest zalecane, ponieważ te rodzaje danych są różnymi typami zawartości. Możesz użyć terminu słownika na górnym poziomie hierarchii lub kategorii, aby rozdzielić typy zawartości.
3. **Konglomeraty i model federacyjny** — konglomeraty często mają wiele jednostek (magistrali), które działają oddzielnie, a w niektórych przypadkach nie będą nawet udostępniać rozliczeń. W takich przypadkach organizacja zakończy tworzenie wystąpienia kontrolą dla każdego KOMPu. Ten model nie jest idealny, ale może być konieczny, szczególnie dlatego, że magistrala często nie chce udostępniać rozliczeń.
4. **Zgodność** — istnieją pewne rygorystyczne systemy zgodności, które traktują nawet metadane jako poufne i wymagają ich w określonej lokalizacji geograficznej. Jeśli firma ma wiele lokalizacje geograficzne, jedyne rozwiązanie ma wiele wystąpień kontrolą, po jednym dla każdej lokalizacji geograficznej.

### <a name="create-a-process-to-move-to-production"></a>Utwórz proces do przeniesienia do środowiska produkcyjnego

Niektóre organizacje mogą zdecydować się na zapewnienie prostoty pracy z jedną wersją produkcyjną kontrolą. Prawdopodobnie nie muszą wykraczać poza scenariusze odnajdywania, wyszukiwania i przeglądania. Jeśli niektóre zasoby mają nieprawidłowe warunki słownikowe, to całkiem łagodniejszej, aby umożliwić użytkownikom samoobsługowe poprawność. Jednak większość organizacji, które chcą wdrożyć kontrolą między różnymi jednostkami biznesowymi, będzie miała pewną formę procesu i kontroli.

Innym ważnym aspektem do uwzględnienia w procesie produkcyjnym jest możliwość migrowania klasyfikacji i etykiet. Kontrolą jest ponad 90 klasyfikatorami systemowymi. Można stosować klasyfikacje systemowe lub niestandardowe dla zasobów pliku, tabeli lub kolumny. Klasyfikacje są podobne do tagów podmiotu i służą do oznaczania i identyfikowania zawartości określonego typu znalezionych w obrębie danych podczas skanowania. Etykiety czułości są używane do identyfikowania kategorii typów klasyfikacji w danych organizacji, a następnie grupowania zasad, które mają być stosowane do każdej kategorii. Wykorzystuje ona te same typy informacji poufnych co Microsoft 365, co pozwala rozciągnąć istniejące zasady zabezpieczeń i ochronę całej zawartości i danych. Umożliwia skanowanie i automatyczne klasyfikowanie dokumentów. Na przykład jeśli masz plik o nazwie multiple.docx i ma on numer IDENTYFIKACYJNy krajowej w swojej zawartości, kontrolą doda klasyfikację, taką jak Narodowy numer identyfikacyjny UE na stronie szczegółów zasobu.

W programie kontrolą istnieje kilka obszarów, w których administratorzy wykazu muszą zapewnić najlepszą praktykę w zakresie spójności i konserwacji:

* **Zasoby danych** — źródła danych muszą być przeskanowane między środowiskami. Nie zaleca się przeprowadzania skanowania tylko w środowisku deweloperskim, a następnie wygenerowania ich ponownie przy użyciu interfejsów API w produkcji. Głównym powodem jest to, że skanery kontrolą wykonują wiele więcej "okablowania" w tle zasobów danych, które mogą być złożone, aby przenieść je do innego wystąpienia kontrolą. Znacznie łatwiej jest dodać to samo źródło danych w środowisku produkcyjnym i ponownie przeskanować źródła. Ogólnie najlepszym rozwiązaniem jest posiadanie dokumentacji wszystkich skanów, połączeń i mechanizmów uwierzytelniania.
* **Skanuj zestawy reguł** — jest to kolekcja reguł przypisanych do określonych skanów, takich jak typ pliku i klasyfikacje do wykrycia. Jeśli nie masz wielu zestawów reguł skanowania, można po prostu ponownie utworzyć je ręcznie za pomocą produkcji. Będzie to wymagało procesu wewnętrznego i dobrej dokumentacji. Jednakże jeśli reguły są ustawiane codziennie lub co tydzień, może to być rozkierowane przez Eksplorowanie trasy interfejsu API REST.
* **Klasyfikacje niestandardowe** — klasyfikacje mogą nie być regularnie zmieniane. Podczas początkowej fazy wdrożenia może upłynąć trochę czasu, aby poznać różne wymagania, które są dostępne w przypadku klasyfikacji niestandardowych. Jednak po rozliczeniu tego wymagania będzie wymagało niewielkiej zmiany. Dlatego zalecane jest ręczne Migrowanie wszelkich klasyfikacji niestandardowych do programu lub korzystanie z interfejsu API REST.
* **Słownik** — można eksportować i importować terminy słownika za POŚREDNICTWem środowiska użytkownika. W przypadku scenariuszy automatyzacji można również użyć interfejsu API REST.
* **Zasady wzorca zestawu zasobów** — ta funkcja jest bardzo zaawansowana dla wszystkich typowych organizacji, które mają zostać zastosowane. W niektórych przypadkach Azure Data Lake Storage ma konwencje nazewnictwa folderów i konkretną strukturę, które mogą powodować problemy z kontrolą w celu wygenerowania zestawu zasobów. Twoja jednostka biznesowa może również chcieć zmienić konstrukcję zestawu zasobów z dodatkowymi dostosowaniami, aby dopasować je do potrzeb firmy. W tym scenariuszu najlepiej śledzić wszystkie zmiany za pośrednictwem interfejsu API REST i dokumentować zmiany za pośrednictwem platformy zewnętrznej obsługi wersji.
* **Przypisanie roli** — to miejsce, w którym kontrolujesz, kto ma dostęp do kontrolą i jakie posiadane uprawnienia. Kontrolą ma także interfejs API REST do obsługi eksportowania i importowania użytkowników i ról, ale nie jest to zgodne z interfejsem API. Zalecenie polega na przypisaniu grupy zabezpieczeń platformy Azure i zarządzania członkostwem w grupie.

### <a name="plan-and-implement-different-integration-points-with-purview"></a>Planowanie i implementowanie różnych punktów integracji za pomocą kontrolą

Istnieje najprawdopodobniej, że w organizacji już istnieje istniejący wykaz danych. Kluczowym pytaniem jest to, czy nadal używać istniejącej technologii i synchronizować ją z kontrolą. Kontrolą umożliwia publikowanie informacji za pośrednictwem interfejsów API w Atlasie, ale naprawdę nie są one przeznaczone do obsługi tego rodzaju scenariusza. Niektóre organizacje mogą początkowo zdecydować się na wstępne użycie kontrolą przez Migrowanie istniejących zasobów danych z innych rozwiązań usługi Data Catalog. Można to zrobić za pośrednictwem interfejsów API Atlas jako podejścia jednokierunkowego. Aby synchronizować między różnymi technologiami katalogowymi, nie należy brać pod uwagę długoterminowego projektu. Zwykle w przypadku, gdy każda jednostka biznesowa może nadal korzystać z istniejących rozwiązań dla starszych zasobów danych, podczas gdy kontrolą będzie służyć do skanowania w poszukiwaniu nowszych źródeł danych.

W przypadku innych scenariuszy integracji, takich jak bilety, niestandardowy interfejs użytkownika i aranżacja, można używać interfejsów API i Kafka punktów końcowych w Atlasie. Ogólnie rzecz biorąc, istnieją cztery punkty integracji z kontrolą:

* **Zasób danych** — umożliwia kontrolą skanowanie zasobów sklepu w celu wyszukania tych zasobów i zebrania wszelkich łatwo dostępnych metadanych. W związku z tym SQL może to być lista baz danych, tabel, procedur składowanych, widoków i danych konfiguracji, które są przechowywane w miejscach takich jak `sys.tables` . W przypadku takich elementów jak Azure Data Factory (ADF) może to spowodować Wyliczenie wszystkich potoków i pobranie danych podczas ich tworzenia, ostatniego uruchomienia, bieżącego stanu.
* Elementy zależne **— umożliwia** kontrolą zebranie informacji z systemu mutacji analizy/danych w celu poruszania się danych. Podobnie jak w przypadku platformy Spark, może to spowodować zebranie informacji z wykonywania notesu w celu sprawdzenia, jakie dane zostały pozyskane, jak zostało ono przekształcone i w którym zostały wprowadzone. Na przykład w przypadku języka SQL można analizować dzienniki zapytań, aby odtworzyć, jakie operacje mutacji zostały wykonane i jakie były. Obsługiwane są zarówno wypychanie, jak i ściąganie, zależnie od potrzeb.
* **Klasyfikacja** — umożliwia kontrolą pobieranie fizycznych próbek ze źródeł danych i uruchamianie ich w systemie klasyfikacji. System klasyfikacji jest ilustracją semantyki danych. Na przykład firma Microsoft może wiedzieć, że plik jest plikiem Parquet i ma trzy kolumny, a trzeci jest ciągiem. Jednak klasyfikatory, które są uruchamiane na przykładach, informują nas, że ciąg jest nazwą, adresem lub numerem telefonu. Oświetlenie tego punktu integracji oznacza, że definiujemy, jak kontrolą może otwierać obiekty, takie jak notesy, potoki, pliki Parquet, tabele i kontenery.
* **Wbudowane środowisko** — produkty, które mają "Studio" (takie jak ADF, Synapse, SQL Studio, PBI i Dynamics), zwykle chcą umożliwić użytkownikom odnajdywanie danych, z którymi chcą korzystać, i Znajdowanie miejsc do danych wyjściowych. Wykaz kontrolą może pomóc w przyspieszeniu tych środowisk, oferując środowisko osadzania. To środowisko może wystąpić w interfejsie API lub na poziomie środowiska użytkownika w opcji partnera. Osadzenie wywołania do kontrolą, organizacja może wykorzystać mapę kontrolą danych do znajdowania zasobów danych, zapoznać się z tematem elementy powiązane, sprawdź schematy, przyjrzyj się klasyfikacji, kontaktom itd.

## <a name="phase-1-pilot"></a>Faza 1: pilotaż

W tej fazie kontrolą należy utworzyć i skonfigurować dla bardzo małego zestawu użytkowników. Zwykle jest to grupa 2-3 osób pracujących ze sobą w celu przeprowadzania kompleksowych scenariuszy. Są one uznawane za rzeczników kontrolą w organizacji. Głównym celem tej fazy jest upewnienie się, że kluczowe funkcje mogą być spełnione, a odpowiednie osoby zainteresowane wiedzą o projekcie.

### <a name="tasks-to-complete"></a>Zadania do ukończenia

|Zadanie|Szczegóły|Czas trwania|
|---------|---------|---------|
|Zbierz & zgodę na wymagania|Dyskusja ze wszystkimi uczestnikami projektu w celu zebrania pełnego zestawu wymagań. Różne osób muszą być w stanie wyrazić zgodę na podzbiór wymagań do ukończenia dla każdej fazy projektu.|1 tydzień|
|Skonfiguruj zestaw startowy|Przejdź przez [kontrolą szybki start](create-catalog-portal.md) i skonfiguruj zestaw [kontrolą Start](tutorial-scan-data.md) do pokazania kontrolą wszystkim uczestnikom projektu.|1 dzień|
|Nawigowanie po kontrolą|Zapoznaj się z tematem jak używać kontrolą ze strony głównej.|1 dzień|
|Konfigurowanie ADF dla elementu powiązanego|Zidentyfikuj potoki kluczy i zasoby danych. Zbierz wszystkie informacje wymagane do nawiązania połączenia z wewnętrznym kontem ADF.|1 dzień|
|Skanowanie źródła danych, takiego jak Azure Data Lake Storage|Dodaj źródło danych i Skonfiguruj skanowanie. Upewnij się, że skanowanie pomyślnie wykryje wszystkie zasoby.|2 dzień|
|Wyszukaj i Przeglądaj|Zezwalaj użytkownikom końcowym na dostęp do kontrolą i wykonywanie kompleksowych operacji wyszukiwania i przeglądania.|1 dzień|

### <a name="acceptance-criteria"></a>Kryteria akceptacji

* Konto kontrolą zostało pomyślnie utworzone w subskrypcji organizacji w ramach dzierżawy organizacji.
* Niewielka grupa użytkowników z wieloma rolami może uzyskać dostęp do kontrolą.
* Kontrolą jest skonfigurowany do skanowania co najmniej jednego źródła danych.
* Użytkownicy powinni mieć możliwość wyodrębnienia wartości klucza kontrolą, takich jak:
  * Wyszukaj i Przeglądaj
  * Pochodzenie
* Użytkownicy powinni mieć możliwość przypisania własności zasobów na stronie zasobów.
* Prezentacja i pokaz mające na celu podnoszenie świadomości dla najważniejszych uczestników projektu.
* Zakup z zarządzania w celu zatwierdzenia dodatkowych zasobów dla fazy MVP.

## <a name="phase-2-minimum-viable-product"></a>Faza 2: minimalny produkt żywotny

Po zastosowaniu uzgodnionych wymagań i współpracujących jednostek gospodarczych w celu dołączenia kontrolą następnym krokiem jest zaplanowanie minimalnej wersji produktu (MVP). W tej fazie zostanie rozszerzone użycie kontrolą na większą liczbę użytkowników, którzy będą mieli dodatkowe potrzeby w poziomie i w pionie. Istnieją kluczowe scenariusze, które muszą być spełnione w poziomie dla wszystkich użytkowników, takich jak warunki słownika, wyszukiwanie i przeglądanie. W przypadku każdej jednostki biznesowej lub grupy zostaną również spełnione szczegółowe wymagania dotyczące kompleksowych scenariuszy, takich jak elementy odnoszące się Azure Data Lake Storage do usługi Azure Synapse DW, aby Power BI.

### <a name="tasks-to-complete"></a>Zadania do ukończenia

|Zadanie|Szczegóły|Czas trwania|
|---------|---------|---------|
|[Skanuj usługę Azure Synapse Analytics](register-scan-azure-synapse-analytics.md)|Rozpocznij pracę ze źródłami bazy danych i przeprowadź skanowanie w celu wypełnienia najważniejszych zasobów|2 dni|
|[Tworzenie niestandardowych klasyfikacji i reguł](create-a-custom-classification-and-classification-rule.md)|Po przeskanowaniu zasobów użytkownicy mogą dowiedzieć się, że istnieją dodatkowe przypadki użycia do większej klasyfikacji obok klasyfikacji domyślnych z kontrolą.|2-4 tygodni|
|[Power BI skanowania](register-scan-power-bi-tenant.md)|Jeśli Twoja organizacja używa Power BI, można skanować Power BI w celu zebrania wszystkich zasobów danych używanych przez analityków danych lub Analityców danych, które mają wymagania dotyczące dołączenia do warstwy magazynowania.|1-2 tygodni|
|[Importuj terminy słownika](how-to-create-import-export-glossary.md)|W większości przypadków organizacja może już opracować zbiór terminów i terminów tworzenia słownika do zasobów. Będzie to wymagało procesu importowania do kontrolą za pośrednictwem pliku CSV.|1 tydzień|
|Dodaj kontakty do zasobów|W przypadku najważniejszych zasobów możesz chcieć ustalić proces, aby zezwolić innym osób na przypisanie kontaktów lub importowanie za pośrednictwem interfejsów API REST.|1 tydzień|
|Dodawanie poufnych etykiet i skanowanie|Może to być opcjonalne w niektórych organizacjach, w zależności od użycia etykietowania z M365.|1-2 tygodni|
|Uzyskaj informacje dotyczące klasyfikacji i poufnych informacji|Do raportowania i wglądu w kontrolą można uzyskać dostęp do tej funkcji, aby uzyskać różne raporty i udostępnić prezentację do zarządzania.|1 dzień|
|Dodawanie użytkowników do dołączania przy użyciu użytkowników zarządzanych przez kontrolą|Ten krok wymaga, aby administrator kontrolą współpracował z administratorem Azure Active Directory w celu ustanowienia nowych grup zabezpieczeń w celu udzielenia dostępu do kontrolą.|1 tydzień|

### <a name="acceptance-criteria"></a>Kryteria akceptacji

* Pomyślnie dołączymy większą grupę użytkowników do kontrolą (50 +)
* Skanuj krytyczne źródła danych biznesowe
* Zaimportuj i przypisz wszystkie najważniejsze terminy słownika
* Pomyślnie przeprowadzono testy ważnych etykiet dotyczących zasobów kluczy
* Pomyślnie spełniono minimalne scenariusze dla użytkowników jednostek biznesowych

## <a name="phase-3-pre-production"></a>Faza 3: przedprodukcyjny

Po przekazaniu fazy programu MVP czas na planowanie przedprodukcyjnego punktu kontrolnego. Organizacja może zdecydować się na posiadanie oddzielnego wystąpienia kontrolą do przedprodukcyjnego i produkcyjnego, a także zachowania tego samego wystąpienia, ale ograniczenia dostępu. Ponadto w tej fazie warto uwzględnić skanowanie lokalnych źródeł danych, takich jak SQL Server. W przypadku braku przerwy w źródłach danych, które nie są obsługiwane przez kontrolą, jest to czas na zapoznanie się z interfejsem API usługi Atlas w celu zrozumienia dodatkowych opcji.

### <a name="tasks-to-complete"></a>Zadania do ukończenia

|Zadanie|Szczegóły|Czas trwania|
|---------|---------|---------|
|Uściślij skanowanie przy użyciu zestawu reguł skanowania|Organizacja będzie mieć wiele źródeł danych do przedprodukcyjnego środowiska. Ważne jest wstępne zdefiniowanie kluczowych kryteriów skanowania, aby klasyfikacje i rozszerzenia plików mogły być stosowane spójnie na tablicy.|1-2 dni|
|Ocenianie dostępności regionu do skanowania|W zależności od regionu źródeł danych i wymagań organizacyjnych dotyczących zgodności i zabezpieczeń warto rozważyć, które regiony muszą być dostępne do skanowania.|1 dzień|
|Zrozumienie koncepcji zapory podczas skanowania|Ten krok wymaga pewnego rodzaju eksploracji, w jaki sposób organizacja konfiguruje zaporę i jak kontrolą może uwierzytelnić się w celu uzyskania dostępu do źródeł danych do skanowania.|1 dzień|
|Zrozumienie koncepcji powiązań prywatnych podczas skanowania|Jeśli Twoja organizacja korzysta z linku prywatnego, należy ustalić podstawę zabezpieczenia sieci w celu uwzględnienia prywatnego linku w ramach wymagań.|1 dzień|
|[Skanuj SQL Server lokalny](register-scan-on-premises-sql-server.md)|Jest to opcjonalne w przypadku SQL Server lokalnego. Skanowanie będzie wymagało skonfigurowania [Integration Runtime samoobsługowego](manage-integration-runtimes.md) i dodania SQL Server jako źródła danych.|1-2 tygodni|
|Korzystanie z interfejsu API REST kontrolą na potrzeby scenariuszy integracji|Jeśli masz wymagania, aby zintegrować kontrolą z innymi technologiami innych firm, takimi jak organizacja lub system biletów, możesz zapoznać się z obszarem interfejsu API REST.|1-4 tygodni|
|Informacje o cenach kontrolą|Ten krok zapewni organizacji ważne informacje finansowe umożliwiające podejmowanie decyzji.|1-5 dni|

### <a name="acceptance-criteria"></a>Kryteria akceptacji

* Pomyślnie dołączono co najmniej jedną jednostkę biznesową ze wszystkimi użytkownikami
* Skanowanie lokalnego źródła danych, takiego jak SQL Server
* Zatwierdzanie koncepcji co najmniej jednego scenariusza integracji przy użyciu interfejsu API REST
* Wykonaj plan, aby przejść do środowiska produkcyjnego, który powinien obejmować kluczowe obszary infrastruktury i zabezpieczeń

## <a name="phase-4-production"></a>Faza 4: produkcja

Powyższe fazy należy wykonać w celu utworzenia efektywnego nadzoru informacji, który stanowi podstawę dla lepszych programów do zarządzania. Zarządzanie danymi pomoże organizacji w przygotowaniu się do rosnących trendów, takich jak AI, Hadoop, IoT i łańcucha bloków. Jest to tylko początek dla wielu rzeczy i danych analitycznych. istnieje wiele więcej informacji, które można omawiać. Wynikiem tego rozwiązania będzie dostarczenie:

* **Ukierunkowane na działalność biznesową** — rozwiązanie, które jest dostosowane do wymagań firmy i scenariuszy w porównaniu z wymaganiami technicznymi.
* **Przyszłe gotowe** — rozwiązanie maksymalizuje domyślne funkcje platformy i korzysta ze standardowych praktyk branżowych na potrzeby konfiguracji lub tworzenia skryptów w celu obsługi zaawansowanych/ewolucji platformy.

### <a name="tasks-to-complete"></a>Zadania do ukończenia

|Zadanie|Szczegóły|Czas trwania|
|---------|---------|---------|
|Skanuj produkcyjne źródła danych z włączoną zaporą|Jeśli jest to opcjonalne, gdy Zapora jest na miejscu, ale ważne jest, aby poznać opcje ograniczające infrastrukturę.|1-5 dni|
|Włącz link prywatny|Jeśli jest to opcjonalne, gdy używane jest łącze prywatne. W przeciwnym razie można pominąć ten element, ponieważ jest to kryterium, gdy jest włączona funkcja Private.|1-5 dni|
|Utwórz zautomatyzowany przepływ pracy|Przepływ pracy jest ważny do automatyzowania procesu, takiego jak zatwierdzenie, eskalacja, przegląd i zarządzanie problemami.|2-3 tygodni|
|Dokumentacja operacji|Zarządzanie danymi nie jest projektem jednorazowym. Jest to ciągły program do podejmowania decyzji opartych na danych i tworzenia szans biznesowych. Ważne jest, aby dokumentować kluczowe procedury i standardy biznesowe.|1 tydzień|

### <a name="acceptance-criteria"></a>Kryteria akceptacji

* Pomyślnie dołączymy całą jednostkę biznesową i ich użytkowników
* Pomyślnie Spełniaj wymagania dotyczące infrastruktury i zabezpieczeń dla środowiska produkcyjnego
* Pomyślnie spełniono wszystkie przypadki użycia wymagane przez użytkowników

## <a name="platform-hardening"></a>Ograniczanie funkcjonalności platformy

Można wykonać dodatkowe czynności związane z ograniczaniem funkcjonalności:

* Zwiększ bezpieczeństwo stan przez włączenie skanowania zasobów zapory lub Użyj linku prywatnego
* Dostosuj skanowanie zakresu w celu zwiększenia wydajności skanowania
* Używanie interfejsów API REST do eksportowania krytycznych metadanych i właściwości na potrzeby tworzenia kopii zapasowych i odzyskiwania
* Korzystanie z przepływu pracy do automatyzowania biletów i zdarzeń, aby uniknąć problemów ludzkich

## <a name="next-steps"></a>Następne kroki

- [Samouczek: uruchamianie zestawu startowego i Skanuj dane](tutorial-scan-data.md)
- [Samouczek: nawigowanie po stronie głównej i wyszukiwanie elementu zawartości](tutorial-asset-search.md)