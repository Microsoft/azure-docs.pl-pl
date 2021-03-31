---
title: Azure Data Catalog często zadawane pytania
description: Często zadawane pytania dotyczące Azure Data Catalog, w tym możliwości odnajdowania źródeł danych, adnotacji i zarządzania nimi.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 98854a4588b59cf0c19877da870e6124fa7c3b9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674686"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Azure Data Catalog często zadawane pytania

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Data Catalog.

## <a name="what-is-azure-data-catalog"></a>Co to jest usługa Azure Data Catalog?
Data Catalog to w pełni zarządzana usługa hostowana w Microsoft Azure, która służy jako system rejestracji i odnajdywania źródeł danych w przedsiębiorstwie. Dzięki Data Catalogm każdy użytkownik, od analityków do badaczy danych i deweloperów, może rejestrować, odnajdywać, zrozumieć i korzystać ze źródeł danych.

## <a name="what-customer-challenges-does-it-solve"></a>Jakie wyzwania dla klientów rozwiązuje?
Data Catalog rozwiązuje problemy z odnajdywaniem źródła danych i "ciemne dane", dzięki czemu użytkownicy mogą odkrywać i zrozumieć źródła danych przedsiębiorstwa.

## <a name="what-are-its-target-audiences"></a>Co to są docelowe odbiorcy?
Data Catalog jest przeznaczona dla użytkowników technicznych i nietechnicznych, w tym:

* Deweloperzy danych i specjaliści w zakresie analizy biznesowej i analizy: osoby odpowiedzialne za tworzenie danych i zawartości analitycznej dla innych osób.
* Data Stewards: ludzie, którzy znają dane, co oznacza i w jaki sposób ma być używany.
* Konsumenci danych: osoby, które muszą mieć możliwość łatwego odnajdywania, zrozumienia i łączenia danych potrzebnych do wykonywania swoich zadań przy użyciu wybranego przez siebie narzędzia.
* Środkowe IT: ludzie, którzy muszą udostępniać setki źródeł danych użytkownikom biznesowym i którzy muszą zachować nadzór nad sposobem używania danych i przez kogo.

## <a name="what-is-its-availability-by-region"></a>Jaka jest dostępność według regionów?
Usługi Data Catalog są obecnie dostępne w następujących centrach danych:

* Zachodnie stany USA
* East US
* West Europe
* Europa Północna
* Australia Wschodnia
* Southeast Asia

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Jakie są limity liczby zasobów danych?
Bezpłatna wersja Data Catalog jest ograniczona do 5 000 zarejestrowanych zasobów danych.

Wersja Standard Data Catalog obsługuje do 100 000 zarejestrowanych zasobów danych.

Każdy obiekt zarejestrowany w Data Catalog, taki jak tabele, widoki, pliki i raporty, liczy się jako zasób danych.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Jakie są obsługiwane typy zasobów i źródeł danych?
Aby zapoznać się z listą obecnie obsługiwanych źródeł danych, zobacz [Data Catalog DSR](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Jak mogę obsługi żądania dla innego źródła danych?
Aby przesłać żądania funkcji i inne opinie, przejdź do [Data Catalog na forach opinii na platformie Azure](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="why-do-i-get-an-error-catalog-already-exists-when-i-try-to-create-a-new-catalog"></a>Dlaczego otrzymuję *wykaz błędów już istnieje* podczas próby utworzenia nowego katalogu?

Gdy kupisz pakiet Office 365 E5 z licencją Power BI Pro, firma Microsoft automatycznie utworzy katalog domyślny w regionie subskrypcji. Ten katalog używa bezpłatnej jednostki SKU. Licencja użytkownika pakietu Office 365/Power BI jest zarządzana na stronie Administracja. 

Jednak ten typ wykazu danych nie ma **opcji administrator** i nie jest widoczny w **Azure Portal**. Nie można usunąć tego typu wykazu danych. Podobnie nie można zmienić nazwy wykazu danych i nie można przenieść go do innego regionu. 

Konta użytkowników, którym przypisano licencję Power BI Pro, automatycznie mają dostęp do usługi Data Catalog ze względu na umowę licencyjną, gdy zarejestrowano się w pakiecie Office 365 E5 z licencją Power BI Pro. Ten typ użytkownika ma pełny dostęp do zasobów wykazu danych bez uprawnień administracyjnych. Ten rodzaj użytkownika *nie* należy do roli **użytkownika katalogu** w Azure Data Catalog.


## <a name="how-do-i-get-started-with-data-catalog"></a>Jak mogę rozpocząć pracę z usługą Data Catalog?
Najlepszym sposobem na rozpoczęcie pracy jest przechodzenie do [wprowadzenie z Data Catalog](data-catalog-get-started.md). Ten artykuł zawiera kompleksowy przegląd możliwości usługi.

## <a name="how-do-i-register-my-data"></a>Jak mogę zarejestrować moje dane?
Aby zarejestrować dane w Data Catalog:
1. W portalu Azure Data Catalog w obszarze **Publikowanie** Uruchom narzędzie rejestracji Azure Data Catalog. 
2. W narzędziu rejestracji źródła danych Data Catalog Zaloguj się przy użyciu tych samych poświadczeń, które są używane w celu uzyskania dostępu do portalu Data Catalog.
3. Wybierz źródło danych i określone zasoby, które chcesz zarejestrować.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Jakie właściwości są wyodrębniane dla zarejestrowanych zasobów danych?
Określone właściwości różnią się od źródła danych do źródła danych, ale ogólnie rzecz biorąc, usługa publikowania Data Catalog wyodrębnia następujące informacje:

* Nazwa zasobu
* Typ zasobu
* Opis zasobu
* Nazwy atrybutów/kolumn
* Typy danych atrybut/kolumna
* Opis atrybutu/kolumny

> [!IMPORTANT]
> Zarejestrowanie zasobów danych za pomocą Data Catalog nie przenosi ani nie kopiuje danych do chmury. Zarejestrowanie zasobów ze źródła danych powoduje skopiowanie metadanych zasobów na platformę Azure, ale dane pozostają w istniejącej lokalizacji źródła danych. Wyjątkiem od tej reguły jest wybranie opcji przekazanie rekordów podglądu lub profilu danych po zarejestrowaniu zasobów. Po dołączeniu do wersji zapoznawczej do 20 rekordów są kopiowane z każdego elementu zawartości i przechowywane jako migawka w Data Catalog. Po dołączeniu profilu danych agregowane informacje są obliczane i uwzględniane w metadanych przechowywanych w katalogu. Informacje zagregowane mogą obejmować rozmiar tabel, wartość procentową wartości null w kolumnie lub wartość minimalną, maksymalną i średnią dla kolumn. 
>
>

> [!NOTE]
> Dla źródeł danych, takich jak SQL Server Analysis Services, które mają właściwość **opisu** pierwszej klasy, narzędzie rejestracji Data Catalog źródła danych wyodrębnia tę wartość właściwości. W przypadku *lokalnych* baz danych SQL Server relacyjnych, które nie posiadają właściwości **opisu** pierwszej klasy, Narzędzie rejestracji Data Catalog źródła danych wyodrębni wartość z właściwości rozszerzonej **MS_Description** dla obiektów i kolumn. Ta właściwość nie jest obsługiwana w przypadku usługi SQL Azure. Aby uzyskać więcej informacji, zobacz [Używanie rozszerzonych właściwości obiektów bazy danych](/previous-versions/sql/sql-server-2008-r2/ms190243(v=sql.105)).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Jak długo powinna zająć się nowo zarejestrowanymi zasobami w katalogu?
Po zarejestrowaniu zasobów przy użyciu Data Catalog może istnieć okres od 5 do 10 sekund, zanim zostaną one wyświetlone w portalu Data Catalog.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Jak mogę dodawać adnotacje i wzbogacać metadane dotyczące moich zarejestrowanych zasobów danych?
Najprostszym sposobem zapewnienia metadanych dla zarejestrowanych zasobów jest wybranie elementu zawartości w portalu Data Catalog, a następnie wprowadzenie wartości w okienku właściwości lub w okienku schematu dla zaznaczonego obiektu.

Podczas procesu rejestracji można także dostarczyć pewne metadane, takie jak eksperci i Tagi. Wartości wprowadzone w ramach usługi publikowania Data Catalog są stosowane do wszystkich zasobów, które są rejestrowane w tym czasie. Aby wyświetlić ostatnio zarejestrowane obiekty w portalu w celu uzyskania dodatkowej adnotacji, wybierz przycisk **Wyświetl Portal** na ekranie końcowym narzędzia rejestracji źródła danych Data Catalog.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Jak mogę usunąć moje zarejestrowane obiekty danych?
Aby usunąć obiekt z Data Catalog, zaznacz obiekt w portalu, a następnie kliknij przycisk **Usuń** . Usunięcie obiektu spowoduje usunięcie jego metadanych z Data Catalog ale nie ma wpływu na bazowe źródło danych.

## <a name="what-is-an-expert"></a>Co to jest ekspert?
Ekspert to osoba, która ma świadomą perspektywę dotyczącą obiektu danych. Obiekt może mieć wielu ekspertów. Ekspert nie musi być "właścicielem" dla obiektu, ale jest po prostu osobą, która wie, jak dane mogą i powinny być używane.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Jak mogę udostępnić informacje z zespołem Data Catalog, jeśli wystąpią problemy?
Aby zgłosić problemy, udostępnić informacje i zadawać pytania, przejdź do [forum Azure Data Catalog](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Czy katalog współpracuje z innym źródłem danych, które interesuje?
Aktywnie pracujemy nad dodaniem kolejnych źródeł danych do Data Catalog. Jeśli chcesz zobaczyć, czy określone źródło danych jest obsługiwane, Sugeruj je (lub skontaktuj się z pomocą techniczną, jeśli jest już sugerowane), przechodząc do [Data Catalog na forach opinii dotyczących platformy Azure](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Jakie uprawnienia muszę zarejestrować zasoby z Data Catalog?
Aby uruchomić Narzędzie rejestracji Data Catalog, musisz mieć uprawnienia do źródła danych, które umożliwia odczytywanie metadanych ze źródła. Aby dołączyć również wersję zapoznawczą, musisz mieć uprawnienia umożliwiające odczytywanie danych z zarejestrowanych obiektów.

Data Catalog pozwala także administratorom wykazu ograniczyć, którzy użytkownicy i grupy mogą dodawać metadane do wykazu. Aby uzyskać dodatkowe informacje, zobacz [jak zabezpieczyć dostęp do wykazu danych i zasobów danych](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Data Catalog będzie również dostępna do wdrożenia lokalnego?
Data Catalog to usługa w chmurze, która może współdziałać z lokalnymi źródłami danych w chmurze i lokalnych w celu zapewnienia hybrydowego rozwiązania do odnajdywania źródeł danych. Obecnie nie ma żadnych planów dla wersji usługi Data Catalog działającej lokalnie.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Czy można wyodrębnić więcej lub bogatsze metadane ze źródeł danych, które są rejestrowane?
Aktywnie pracujemy nad rozwinięciem możliwości Data Catalog. Jeśli chcesz mieć dodatkowe metadane wyodrębnione ze źródła danych podczas rejestracji, Sugeruj je (lub zagłosuj na nie, jeśli została już zasugerowana) w [Data Catalog na forach opinii na platformie Azure](https://feedback.azure.com/forums/906052-data-catalog). 

Jeśli chcesz uwzględnić metadane kolumny/schematu, podglądy lub profile danych, w przypadku źródeł danych, w których te metadane nie są wyodrębniane przez Narzędzie rejestracji źródła danych, możesz dodać te metadane za pomocą interfejsu API Data Catalog. Aby uzyskać dodatkowe informacje, zobacz [Azure Data Catalog interfejsu API REST](/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Jak mogę ograniczyć widoczność zarejestrowanych zasobów danych, tak aby tylko niektóre osoby mogły je wykryć?
Wybierz zasoby danych w Data Catalog, a następnie kliknij przycisk **Przejmij na własność** . Właściciele zasobów danych w Data Catalog mogą zmienić ustawienia widoczności, aby zezwolić wszystkim użytkownikom na odnajdywanie posiadanych zasobów lub ograniczyć widoczność do określonych użytkowników. Aby uzyskać dodatkowe informacje, zobacz [Zarządzanie zasobami danych w Azure Data Catalog](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Jak mogę zaktualizować rejestrację zasobu danych, dzięki czemu zmiany w źródle danych zostaną odzwierciedlone w wykazie?
Aby zaktualizować metadane dla zasobów danych, które są już zarejestrowane w wykazie, po prostu ponownie zarejestruj źródło danych, które zawiera zasoby. Wszelkie zmiany w źródle danych, takie jak kolumny dodawane lub usuwane z tabel lub widoków, są aktualizowane w wykazie, ale wszelkie adnotacje udostępniane przez użytkowników są zachowywane.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Moje pytanie nie ma odpowiedzi w tym miejscu. Gdzie mogę znaleźć odpowiedzi?
Przejdź do [forum Azure Data Catalog](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Pytania na ten temat znajdziesz tutaj.