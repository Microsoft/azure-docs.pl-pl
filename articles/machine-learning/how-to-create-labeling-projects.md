---
title: Tworzenie projektu etykietowania danych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć i uruchamiać projekty etykietowania w celu tagowania danych na potrzeby uczenia maszynowego. Użyj etykiety z asystą lub ludzkimi w pętli etykieta, aby pomóc w zadaniu.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: 854504347409efb4f0eafff0d776db23ca9fda07
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2021
ms.locfileid: "98059844"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Utwórz projekt etykietowania danych i Eksportuj etykiety 

Dowiedz się, jak tworzyć i uruchamiać projekty etykietowania danych w celu tagowania danych w Azure Machine Learning.  Używaj etykiet danych z obsługą uczenia maszynowego lub etykiet ludzkich w pętli, aby pomóc w zadaniu.


## <a name="data-labeling-capabilities"></a>Możliwości etykietowania danych

> [!Important]
> Obecnie obsługiwane są tylko projekty klasyfikacji obrazów i identyfikacji obiektów. Ponadto obrazy danych muszą być dostępne w magazynie datastorage obiektów blob platformy Azure. (Jeśli nie masz istniejącego magazynu danych, możesz przekazać obrazy podczas tworzenia projektu).

Etykiety danych Azure Machine Learning to centralne miejsce do tworzenia i monitorowania projektów etykietowania oraz zarządzania nimi:
 - Koordynuj dane, etykiety i członków zespołu, aby efektywnie zarządzać zadaniami etykietowania. 
 - Śledzi postęp i utrzymuje kolejkę niekompletnych zadań etykietowania.
 - Uruchom i Zatrzymaj projekt i kontroluj postęp etykietowania.
 - Zapoznaj się z etykietą dane i Eksportuj etykiety w formacie COCO lub jako zestaw danych Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

* Dane, które chcesz oznaczyć, w plikach lokalnych lub w usłudze Azure Blob Storage.
* Zestaw etykiet, które mają zostać zastosowane.
* Instrukcje dotyczące etykietowania.
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLFree).
* Obszar roboczy Machine Learning. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-data-labeling-project"></a>Tworzenie projektu etykietowania danych

Projekty etykiet są administrowane przy użyciu Azure Machine Learning. Strona **projekty etykiet** służy do zarządzania projektami.

Jeśli dane są już w usłudze Azure Blob Storage, należy udostępnić ją jako magazyn danych przed utworzeniem projektu etykietowania. Aby zapoznać się z przykładem korzystania z magazynu danych, zobacz [Samouczek: Tworzenie pierwszego projektu etykietowania klasyfikacji obrazu](tutorial-labeling.md).

Aby utworzyć projekt, wybierz pozycję **Dodaj projekt**. Nadaj projektowi odpowiednią nazwę i wybierz pozycję **etykieta typ zadania**. Nie można ponownie użyć nazwy projektu, nawet jeśli projekt zostanie usunięty w przyszłości.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="Kreator tworzenia etykiet dla projektu":::

* Wybierz **wiele klas klasyfikacji obrazów** dla projektów, gdy chcesz zastosować tylko *jedną etykietę* z zestawu etykiet do obrazu.
* Wybierz opcję **Klasyfikacja obrazu wieloetykietowego** dla projektów, gdy chcesz zastosować *jedną lub więcej* etykiet z zestawu etykiet do obrazu. Na przykład zdjęcie psa może mieć etykietę z obu *pies* i *Daytime*.
* Wybierz pozycję **Identyfikacja obiektu (pole ograniczenia)** dla projektów, gdy chcesz przypisać etykietę i pole ograniczenia do każdego obiektu w obrazie.

Wybierz pozycję **dalej** , gdy wszystko będzie gotowe do kontynuowania.

## <a name="specify-the-data-to-label"></a>Określ dane do etykiet

Jeśli utworzono już zestaw danych, który zawiera dane, wybierz go z listy rozwijanej **Wybierz istniejący zestaw danych** . Lub wybierz pozycję **Utwórz zestaw danych** , aby użyć istniejącego magazynu usługi Azure datastore lub do przekazywania plików lokalnych.

> [!NOTE]
> Projekt nie może zawierać więcej niż 500 000 obrazów.  Jeśli zestaw danych zawiera więcej, zostaną załadowane tylko pierwsze obrazy 500 000.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Tworzenie zestawu danych na podstawie usługi Azure datastore

W wielu przypadkach warto tylko przekazać pliki lokalne. Jednak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) zapewnia szybszy i bardziej niezawodny sposób transferu dużej ilości danych. Zalecamy Eksplorator usługi Storage jako domyślny sposób przenoszenia plików.

Aby utworzyć zestaw danych na podstawie danych, które zostały już zapisane w usłudze Azure Blob Storage:

1. Wybierz pozycję **Utwórz zestaw danych**  >  **ze sklepu datastore**.
1. Przypisz **nazwę** do zestawu danych.
1. Wybierz pozycję **plik** jako **Typ zestawu danych**.  Obsługiwane są tylko typy danych plików.
1. Wybierz magazyn danych.
1. Jeśli dane są w podfolderze w magazynie obiektów blob, wybierz pozycję **Przeglądaj** , aby wybrać ścieżkę.
    * Dołącz "/* *" do ścieżki, aby uwzględnić wszystkie pliki w podfolderach wybranej ścieżki.
    * Dołącz "* */* . *", aby uwzględnić wszystkie dane w bieżącym kontenerze i jego podfolderach.
1. Podaj opis zestawu danych.
1. Wybierz pozycję **Dalej**.
1. Potwierdź szczegóły. Wybierz pozycję **Wstecz** , aby zmodyfikować ustawienia, lub **Utwórz** , aby utworzyć zestaw danych.


### <a name="create-a-dataset-from-uploaded-data"></a>Tworzenie zestawu danych na podstawie przekazanych danych

Aby bezpośrednio przekazać dane:

1. Wybierz pozycję **Utwórz zestaw danych**  >  **z plików lokalnych**.
1. Przypisz **nazwę** do zestawu danych.
1. Wybierz opcję "plik" jako **Typ zestawu danych**.
1. *Opcjonalne:* Wybierz pozycję **Ustawienia zaawansowane** , aby dostosowywać magazyn danych, kontener i ścieżkę do swoich potrzeb.
1. Wybierz pozycję **Przeglądaj** , aby wybrać pliki lokalne do przekazania.
1. Podaj opis zestawu danych.
1. Wybierz pozycję **Dalej**.
1. Potwierdź szczegóły. Wybierz pozycję **Wstecz** , aby zmodyfikować ustawienia, lub **Utwórz** , aby utworzyć zestaw danych.

Dane są przekazywane do domyślnego magazynu obiektów BLOB ("workspaceblobstore") obszaru roboczego Machine Learning.

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"></a> Skonfiguruj odświeżanie przyrostowe

Jeśli planujesz dodać nowe obrazy do zestawu danych, użyj odświeżania przyrostowego, aby dodać nowe obrazy do projektu.   Po włączeniu **odświeżania przyrostowego** zestaw danych jest okresowo sprawdzany w celu dodania nowych obrazów do projektu w oparciu o wskaźnik ukończenia etykietowania.   Sprawdzanie, czy nowe dane są zatrzymane, gdy projekt zawiera maksymalną 500 000 obrazów.

Aby dodać więcej obrazów do projektu, użyj [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) do przekazania do odpowiedniego folderu w magazynie obiektów BLOB. 

Zaznacz pole wyboru **Włącz odświeżanie przyrostowe** , jeśli chcesz, aby projekt stale monitorował się pod kątem nowych danych w magazynie danych. Te dane zostaną pobrane do projektu raz dziennie po włączeniu.

Usuń zaznaczenie tego pola, jeśli nie chcesz, aby nowe obrazy, które pojawiają się w magazynie danych, były dodawane do projektu.

Sygnaturę czasową ostatniego odświeżenia można znaleźć w sekcji **odświeżanie przyrostowe** karty **szczegóły** dla projektu.


## <a name="specify-label-classes"></a>Określanie klas etykiet

Na stronie **klasy etykiet** Określ zestaw klas do kategoryzacji danych. Dokładność i szybkość etykiet są zależne od ich możliwości wyboru między klasami. Na przykład zamiast wyszukać pełną rodzaj i gatunek dla roślin lub zwierząt, użyj kodu pola lub skrócić rodzaj.

Wprowadź jedną etykietę na wiersz. Użyj **+** przycisku, aby dodać nowy wiersz. Jeśli masz więcej niż 3 lub 4 etykiety, ale mniej niż 10, możesz chcieć utworzyć prefiks nazw z cyframi ("1:", "2:"), aby etykietki mogły używać klawiszy numerycznych do przyspieszenia ich pracy.

## <a name="describe-the-data-labeling-task"></a>Opisywanie zadania etykietowania danych

Ważne jest jasne wyjaśnienie zadania etykietowania. Na stronie **instrukcje etykietowania** można dodać link do zewnętrznej witryny w celu etykietowania instrukcji lub podać instrukcje w polu edycji na stronie. Należy zachować zorientowane na zadania instrukcje i odpowiednie dla odbiorców. Weź pod uwagę następujące pytania:

* Co to są etykiety, które zobaczysz i w jaki sposób będą wybierać między nimi? Czy istnieje tekst referencyjny, do którego się odwołuje?
* Co należy zrobić, jeśli etykieta nie wydaje się odpowiednia?
* Co należy zrobić, jeśli wiele etykiet wydaje się stosowne?
* Jaki próg ufności ma mieć zastosowanie do etykiety? Czy chcesz, aby "Najlepsza wartość", jeśli nie są one określone?
* Co należy zrobić przy użyciu częściowo zamknięte lub nakładających się obiektów?
* Co należy zrobić, jeśli obiekt zainteresowania jest przycinany przez brzeg obrazu?
* Co należy zrobić po przesłaniu etykiety, jeśli uważasz, że popełniono błąd?

W przypadku pól ograniczenia ważne pytania obejmują:

* Jak jest zdefiniowane pole ograniczenia dla tego zadania? Czy powinien znajdować się w całości na wewnętrznej stronie obiektu, czy powinien znajdować się na zewnątrz? Czy powinien być przycięty jak najlepiej jak to możliwe?
* Jakiego poziomu opieka i spójność ma oczekiwać, że etykiety mają być stosowane przy definiowaniu powiązanych pól?
* Jak dodać etykietę do obiektu, który jest częściowo pokazywany w obrazie? 
* Jak dodać etykietę do obiektu, który częściowo został objęty przez inny obiekt?

>[!NOTE]
> Pamiętaj, że etykiety będą mogły wybrać pierwszych 9 etykiet przy użyciu klawiszy Number 1-9.

## <a name="use-ml-assisted-data-labeling"></a>Korzystanie z etykietowania danych z obsługą ML

Strona **etykietowania** z pomocąą ml umożliwia wyzwalanie automatycznych modeli uczenia maszynowego w celu przyspieszenia zadania etykietowania. Na początku projektu etykietowania obrazy są ustawiane losowo w kolejności losowej, aby zmniejszyć liczbę potencjalnych odchyleń. Jednak wszelkie bias, które znajdują się w zestawie danych, zostaną odzwierciedlone w modelu przeszkolonym. Na przykład jeśli 80% obrazów ma jedną klasę, wówczas około 80% danych używanych do uczenia modelu będzie tej klasy. To szkolenie nie obejmuje aktywnego uczenia się.

Wybierz opcję *Włącz oznaczenie ml z etykietami* i określ procesor GPU, aby włączyć obsługę etykietowania, która składa się z dwóch faz:
* Klastrowanie
* Etykietowanie

Dokładna liczba obrazów z etykietami, które są niezbędne do rozpoczęcia etykietowania pomocy, nie jest ustalona.  Może się to różnić w zależności od jednego projektu etykietowego do innego. W przypadku niektórych projektów czasami możliwe jest wyświetlenie przednich lub zadań klastra po ręcznym etykietowaniu obrazów 300. Przy pomocy zatytułowanej do uczenia maszynowego jest stosowana technika o nazwie *nauka przekazu*, która korzysta ze wstępnie przeszkolonego modelu, aby przeskoczyć proces szkolenia. Jeśli klasy zestawu danych są podobne do tych w modelu wstępnie szkolonym, etykietki wstępne mogą być dostępne po tylko kilku setkach obrazów oznaczonych ręcznie. Jeśli zestaw danych znacznie różni się od danych używanych do wstępnego uczenia modelu, może to zająć dużo czasu.

Ponieważ końcowe etykiety nadal opierają się na danych wejściowych z Labeler, Technologia ta jest czasami wywoływana *przez człowieka w pętli* etykiet.

> [!NOTE]
> Oznakowanie danych wspomaganych przez ML nie obsługuje domyślnych kont magazynu zabezpieczonych za [siecią wirtualną](how-to-network-security-overview.md). Musisz użyć konta magazynu innego niż domyślne do etykietowania danych z asystą w ML. Konto magazynu inne niż domyślne można zabezpieczyć za siecią wirtualną. 

### <a name="clustering"></a>Klastrowanie

Po przesłaniu pewnej liczby etykiet model uczenia maszynowego dla klasyfikacji obrazów zaczyna grupować podobne obrazy.  Te podobne obrazy są prezentowane na etykietach na tym samym ekranie w celu przyspieszenia ręcznego tagowania. Klastrowanie jest szczególnie przydatne, gdy Labeler przegląda siatkę obrazów 4, 6 lub 9. 

Gdy model uczenia maszynowego został przeszkolony na ręcznie oznaczonych danych, model zostanie obcięty do jego ostatniej w pełni połączonej warstwy. Obrazy bez etykiet są następnie przenoszone przez obcięty model w procesie powszechnie znanym jako "osadzanie" lub "cechowania". Spowoduje to osadzenie każdego obrazu w miejscu o dużej wymiarze zdefiniowanym przez tę warstwę modelu. Obrazy, które są najbliższe sąsiadów w miejscu, są używane na potrzeby zadań klastrowania. 

Faza klastrowania nie jest wyświetlana dla modeli wykrywania obiektów.

### <a name="prelabeling"></a>Etykietowanie

Po przesłaniu wystarczającej liczby etykiet obrazu model klasyfikacji jest używany do przewidywania tagów obrazu. Lub model wykrywania obiektów służy do przewidywania pól ograniczenia. Labeler teraz widzi strony, które zawierają przewidywane etykiety, które znajdują się już w każdym obrazie. W przypadku wykrywania obiektów wyświetlane są również pola predykcyjne. Zadanie jest następnie, aby przejrzeć te przewidywania i skorygować wszystkie nieoznaczone obrazy przed przesłaniem strony.  

Po przeszkoleniu modelu uczenia maszynowego na ręcznie etykietowanych danych model jest oceniany na zestawie testów z ręcznie oznaczonymi obrazami, aby określić jego dokładność przy różnych progach ufności. Ten proces oceny jest używany do określenia progu pewności, powyżej którego model jest wystarczająco dokładny, aby pokazać etykietki wstępne. Model jest następnie oceniany pod kątem braku etykietowania danych. Obrazy z przewidywaniami bardziej wątpliwości niż ten próg są używane do wstępnego etykietowania.

## <a name="initialize-the-data-labeling-project"></a>Inicjowanie projektu etykietowania danych

Po zainicjowaniu projektu etykietowania niektóre aspekty projektu są niezmienne. Nie można zmienić typu zadania lub zestawu danych. *Możesz* zmodyfikować etykiety i adres URL opisu zadania. Uważnie przejrzyj ustawienia przed utworzeniem projektu. Po przesłaniu projektu nastąpi powrót do strony głównej **etykietowania danych** , co spowoduje wyświetlenie projektu jako **inicjującego**.

> [!NOTE]
> Ta strona może nie być odświeżana automatycznie. Dlatego po wstrzymaniu ręcznie Odśwież stronę, aby zobaczyć stan projektu jako **utworzony**.

## <a name="run-and-monitor-the-project"></a>Uruchamianie i monitorowanie projektu
Po zainicjowaniu projektu platforma Azure rozpocznie działanie. Wybierz projekt na stronie **etykietowanie danych** głównych, aby wyświetlić szczegóły projektu

Aby wstrzymać lub ponownie uruchomić projekt, przełącz stan **działania** w prawym górnym rogu. Dane można etykietować tylko wtedy, gdy projekt jest uruchomiony.

### <a name="dashboard"></a>Pulpit nawigacyjny

Karta **pulpit nawigacyjny** pokazuje postęp zadania etykietowania.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="Pulpit nawigacyjny etykiet danych":::

Wykres postępu pokazuje, ile elementów zostało oznaczonych i ile nie zostało to jeszcze zrobione.  Elementy oczekujące mogą być następujące:

* Jeszcze nie dodano do zadania
* Uwzględnione w zadaniu, które jest przypisane do elementu Labeler, ale jeszcze nie zostało ukończone 
* W kolejce zadań, które zostały jeszcze przypisane

Środkowa sekcja pokazuje kolejkę zadań, które zostały jeszcze przypisane. Gdy etykieta z asystą jest wyłączona, w tej sekcji przedstawiono liczbę ręcznych zadań do przypisania. Gdy etykieta z asystą jest włączona, zostanie ona również wyświetlona:

* Zadania zawierające elementy klastrowane w kolejce
* Zadania zawierające elementy, które są oznaczone jako "w kolejce"

Ponadto, gdy jest włączona etykieta z asystą, na małym przebiegu szkolenia pojawia się mały pasek postępu.  Sekcje eksperymenty zawierają linki do poszczególnych przebiegów uczenia maszynowego.

* Szkolenia — pociąga za model do przewidywania etykiet
* Sprawdzanie poprawności — określa, czy funkcja przewidywania tego modelu będzie używana do wstępnego etykietowania elementów 
* Wnioskowanie — przebieg prognozowania dla nowych elementów
* Cechowania — elementy klastrów (tylko dla projektów klasyfikacji obrazów)

Po prawej stronie jest dystrybuowanie etykiet dla tych zadań, które zostały ukończone.  Należy pamiętać, że w przypadku niektórych typów projektów element może mieć wiele etykiet, w tym przypadku całkowita liczba etykiet może być większa niż całkowita liczba elementów.

### <a name="data-tab"></a>Karta dane

Na karcie **dane** można zobaczyć zestaw danych i przejrzeć dane z etykietami. Jeśli zobaczysz nieprawidłowe etykiety danych, zaznacz ją i wybierz polecenie **Odrzuć**, co spowoduje usunięcie etykiet i umieszczenie danych z powrotem w kolejce bez etykiety.

### <a name="details-tab"></a>Karta Szczegóły

Wyświetl szczegóły projektu.  Na tej karcie można:

* Wyświetlanie szczegółów projektu i wejściowych zestawów danych
* Włącz odświeżanie przyrostowe
* Wyświetl szczegóły kontenera magazynu używanego do przechowywania danych wyjściowych z etykietą w projekcie
* Dodawanie etykiet do projektu
* Edytuj instrukcje nadające się do etykiet
* Edytuj szczegóły etykiet z asystą, w tym Włączanie/wyłączanie

### <a name="access-for-labelers"></a>Dostęp do etykiet

Każdy, kto ma dostęp do obszaru roboczego, może oznaczyć dane w projekcie.  Możesz również dostosować uprawnienia dla etykiet, tak aby mogły uzyskiwać dostęp do etykiet, ale nie innych części obszaru roboczego lub projektu etykietowania.  Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do obszaru roboczego Azure Machine Learning](how-to-assign-roles.md)i Dowiedz się, jak utworzyć [rolę niestandardową Labeler](how-to-assign-roles.md#labeler).

## <a name="add-new-label-class-to-a-project"></a>Dodaj nową klasę etykiet do projektu

Podczas tworzenia etykietowania danych może się okazać, że do klasyfikowania obrazów są potrzebne dodatkowe etykiety.  Na przykład możesz chcieć dodać etykietę "nieznany" lub "inne", aby wskazać mylące obrazy.

Wykonaj następujące kroki, aby dodać jedną lub więcej etykiet do projektu:

1. Wybierz projekt na stronie **etykietowanie danych** głównych.
1. W prawym górnym rogu strony Przełącz **działanie** na **wstrzymane** , aby zatrzymać etykietowanie na podstawie ich działania.
1. Wybierz kartę **Szczegóły**.
1. Na liście po lewej stronie wybierz pozycję **klasy etykiet**.
1. W górnej części listy wybierz pozycję **+ Dodaj etykiety** ![ Dodaj etykietę](media/how-to-create-labeling-projects/add-label.png)
1. W formularzu Dodaj nową etykietę i wybierz sposób wykonywania czynności.  Ze względu na to, że zmieniono etykiety dostępne dla obrazu, wybierz sposób traktowania danych, które zostały oznaczone etykietą:
    * Zacznij od początku, usuwając wszystkie istniejące etykiety.  Wybierz tę opcję, jeśli chcesz zacząć od początku do nowego pełnego zestawu etykiet. 
    * Zacznij od początku, zachowując wszystkie istniejące etykiety.  Wybierz tę opcję, aby oznaczyć wszystkie dane jako bez etykiet, ale zachować istniejące etykiety jako tag domyślny dla obrazów, które zostały wcześniej oznaczone etykietami.
    * Kontynuuj, zachowując wszystkie istniejące etykiety. Wybierz tę opcję, aby zapewnić, że wszystkie dane mają już etykietę, i Zacznij używać nowej etykiety dla danych, które nie zostały jeszcze oznaczone etykietami.
1. Zmodyfikuj stronę z instrukcjami w razie potrzeby dla nowych etykiet.
1. Po dodaniu wszystkich nowych etykiet w prawym górnym rogu strony Przełącz **wstrzymanie** do **działania** w celu ponownego uruchomienia projektu.  

## <a name="export-the-labels"></a>Eksportowanie etykiet

W dowolnym momencie możesz wyeksportować dane etykiet dla Machine Learning eksperymentowania. Etykiety obrazów można eksportować w [formacie Coco](http://cocodataset.org/#format-data) lub jako [zestaw danych Azure Machine Learning z etykietami](how-to-use-labeled-dataset.md). Użyj przycisku **Eksportuj** na stronie **szczegóły projektu** w projekcie etykietowania.

Plik COCO jest tworzony w domyślnym magazynie obiektów BLOB obszaru roboczego Azure Machine Learning w folderze w obszarze *Export/Coco*. Możesz uzyskać dostęp do wyeksportowanego zestawu danych Azure Machine Learning w sekcji **zestawy** danych w Machine Learning. Strona szczegóły zestawu danych zawiera również przykładowy kod umożliwiający uzyskanie dostępu do etykiet z poziomu języka Python.

![Wyeksportowany zestaw danych](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Skorzystaj z tych wskazówek, Jeśli zobaczysz którykolwiek z tych problemów.

|Problem  |Rozwiązanie  |
|---------|---------|
|Można używać tylko zestawów danych utworzonych w magazynach danych obiektów BLOB.     |  Jest to znane ograniczenie bieżącej wersji.       |
|Po utworzeniu projekt pokazuje "Inicjowanie" przez długi czas.     | Ręcznie Odśwież stronę. Inicjalizacja powinna być w przybliżeniu 20 punktów, na sekundę. Brak autoodświeżania to znany problem.         |
|Podczas recenzowania obrazów nie są wyświetlane nowe obrazy z etykietami.     |   Aby załadować wszystkie obrazy z etykietami, wybierz **pierwszy** przycisk. **Pierwszy** przycisk przeprowadzi Cię z powrotem do początku listy, ale ładuje wszystkie dane z etykietami.      |
|Naciśnięcie klawisza Esc podczas etykietowania dla wykrywania obiektów tworzy etykietę o zerowej wielkości w lewym górnym rogu. Przesyłanie etykiet w tym stanie nie powiodło się.     |   Usuń etykietę, klikając znak krzyżyka obok niego.  |
|Nie można przypisać zestawu zadań do określonego Labeler.     |   Jest to znane ograniczenie bieżącej wersji.  |

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Tworzenie pierwszego projektu etykietowania klasyfikacji obrazu](tutorial-labeling.md).
* Obrazy etykiet do [wykrywania klasyfikacji obrazów lub obiektów](how-to-label-images.md)
* Dowiedz się więcej o [Azure Machine Learning i Machine Learning Studio (klasyczne)](./overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
