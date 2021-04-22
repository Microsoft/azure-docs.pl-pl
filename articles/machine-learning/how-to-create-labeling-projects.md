---
title: Tworzenie projektu etykietowania danych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć i uruchamiać projekty etykietowania w celu tagowania danych na platformie uczenia maszynowego. Użyj etykietowania wspomaganych przez ml lub ludzkich etykiet pętli, aby pomóc w zadaniu.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: ff1783dd31b8139940e56d24ae82866b428838b1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861161"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Tworzenie projektu etykietowania danych i eksportowanie etykiet 

Dowiedz się, jak tworzyć i uruchamiać projekty etykietowania danych w celu tagowania danych w Azure Machine Learning.  Użyj etykietowania danych wspomaganych przez uczenie maszynowe lub etykietowania "człowiek w pętli", aby pomóc w zadaniu.


## <a name="data-labeling-capabilities"></a>Możliwości etykietowania danych

> [!Important]
> Obrazy danych muszą być dostępne w usłudze Azure Blob DataStore. (Jeśli nie masz istniejącego magazynu danych, możesz przekazać obrazy podczas tworzenia projektu).

Azure Machine Learning danych to centralne miejsce do tworzenia i monitorowania projektów etykietowania oraz zarządzania nimi:
 - Koordynowanie danych, etykiet i członków zespołu w celu efektywnego zarządzania zadaniami etykietowania. 
 - Śledzi postęp i utrzymuje kolejkę niekompletnych zadań etykietowania.
 - Uruchom i zatrzymaj projekt oraz kontroluj postęp etykietowania.
 - Przejrzyj dane oznaczone etykietami i wyeksportuj je w formacie COCO lub jako Azure Machine Learning danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Dane, które chcesz etykietować, w plikach lokalnych lub w usłudze Azure Blob Storage.
* Zestaw etykiet, które chcesz zastosować.
* Instrukcje dotyczące etykietowania.
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLFree).
* Obszar Machine Learning roboczy. Zobacz [Tworzenie Azure Machine Learning roboczego.](how-to-manage-workspace.md)

## <a name="create-a-data-labeling-project"></a>Tworzenie projektu etykietowania danych

Projekty etykietowania są administrowane z Azure Machine Learning. Do zarządzania **projektami należy użyć** strony Etykietowanie projektów.

Jeśli dane są już w usłudze Azure Blob Storage, należy udostępnić je jako magazyn danych przed utworzeniem projektu etykietowania. Aby uzyskać przykład użycia magazynu danych, zobacz [Tutorial: Create your first image classification labeling project (Samouczek: tworzenie pierwszego projektu etykietowania klasyfikacji obrazów).](tutorial-labeling.md)

Aby utworzyć projekt, wybierz **pozycję Dodaj projekt**. Nadaj projektowi odpowiednią nazwę i wybierz typ **zadania Etykietowanie.** Nie można ponownie użyć nazwy projektu, nawet jeśli projekt zostanie usunięty w przyszłości.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="Kreator tworzenia projektu etykietowania":::

* Wybierz **pozycję Klasyfikacja obrazów Wiele klas** dla  projektów, jeśli chcesz zastosować do obrazu tylko jedną etykietę z zestawu etykiet.
* Wybierz **pozycję Klasyfikacja obrazów** Wiele etykiet  dla projektów, jeśli chcesz zastosować co najmniej jedną etykietę z zestawu etykiet do obrazu. Na przykład zdjęcie psa może być oznaczone zarówno psem, jak *i* *dniem.*
* Wybierz **pozycję Identyfikacja obiektu (pole granicy)** dla projektów, jeśli chcesz przypisać etykietę i pole granicy do każdego obiektu w obrazie.
* Wybierz **pozycję Segmentacja wystąpień (wielokąt)** dla projektów, jeśli chcesz przypisać etykietę i narysować wielokąt wokół każdego obiektu na obrazie.

Gdy **wszystko** będzie gotowe do kontynuowania, wybierz przycisk Dalej.

## <a name="specify-the-data-to-label"></a>Określanie danych do etykietowania

Jeśli już utworzono zestaw danych zawierający dane,  wybierz go z listy rozwijanej Wybierz istniejący zestaw danych. Możesz też wybrać pozycję **Utwórz zestaw danych,** aby użyć istniejącego magazynu danych platformy Azure lub przekazać pliki lokalne.

> [!NOTE]
> Projekt nie może zawierać więcej niż 500 000 obrazów.  Jeśli zestaw danych zawiera więcej obrazów, zostanie załadowanych tylko 500 000 pierwszych obrazów.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Tworzenie zestawu danych z magazynu danych platformy Azure

W wielu przypadkach wystarczy tylko przekazać pliki lokalne. Jednak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) zapewnia szybszy i bardziej niezawodny sposób transferu dużej ilości danych. Zaleca się Eksplorator usługi Storage jako domyślnego sposobu przenoszenia plików.

Aby utworzyć zestaw danych na pomocą danych, które zostały już zapisane w usłudze Azure Blob Storage:

1. Wybierz **pozycję Utwórz zestaw danych z** magazynu  >  **danych.**
1. Przypisz **nazwę do** zestawu danych.
1. Wybierz **pozycję Plik** jako typ **zestawu danych.**  Obsługiwane są tylko typy zestawów danych plików.
1. Wybierz magazyn danych.
1. Jeśli dane są w podfolderze w magazynie obiektów blob, wybierz **pozycję Przeglądaj,** aby wybrać ścieżkę.
    * Dołącz "/**" do ścieżki, aby uwzględnić wszystkie pliki w podfolderach wybranej ścieżki.
    * Dołącz "* */* *", aby uwzględnić wszystkie dane w bieżącym kontenerze i jego podfolderach.
1. Podaj opis zestawu danych.
1. Wybierz opcję **Dalej**.
1. Potwierdź szczegóły. Wybierz **pozycję Wstecz,** aby zmodyfikować ustawienia, **lub pozycję Utwórz,** aby utworzyć zestaw danych.


### <a name="create-a-dataset-from-uploaded-data"></a>Tworzenie zestawu danych na pomocą przekazanych danych

Aby bezpośrednio przekazać dane:

1. Wybierz **pozycję Utwórz zestaw danych z** plików  >  **lokalnych.**
1. Przypisz **nazwę do** zestawu danych.
1. Wybierz pozycję "Plik" jako **typ zestawu danych.**
1. *Opcjonalnie:* Wybierz **pozycję Ustawienia zaawansowane,** aby dostosować magazyn danych, kontener i ścieżkę do danych.
1. Wybierz **pozycję Przeglądaj,** aby wybrać pliki lokalne do przekazania.
1. Podaj opis zestawu danych.
1. Wybierz opcję **Dalej**.
1. Potwierdź szczegóły. Wybierz **pozycję Wstecz,** aby zmodyfikować ustawienia, **lub pozycję Utwórz,** aby utworzyć zestaw danych.

Dane są przekazywane do domyślnego magazynu obiektów blob ("workspaceblobstore") Machine Learning obszaru roboczego.

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"></a> Konfigurowanie odświeżania przyrostowego

Jeśli planujesz dodać nowe obrazy do zestawu danych, użyj odświeżania przyrostowego, aby dodać te nowe obrazy do projektu.   Po **włączeniu odświeżania** przyrostowego zestaw danych jest okresowo sprawdzany pod kątem dodaniu nowych obrazów do projektu na podstawie szybkości uzupełniania etykiet.   Sprawdzanie nowych danych zatrzymuje się, gdy projekt zawiera maksymalnie 500 000 obrazów.

Aby dodać więcej obrazów do projektu, użyj [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) do przekazania do odpowiedniego folderu w magazynie obiektów blob. 

Zaznacz pole wyboru Włącz **odświeżanie przyrostowe,** jeśli chcesz, aby projekt stale monitorował nowe dane w sklepie danych. Te dane będą ściągane do projektu raz dziennie po włączeniu, więc trzeba będzie poczekać po dodaniu nowych danych do magazynu danych, zanim będą one pojawiać się w projekcie.  Sygnaturę czasową ostatniego odświeżenia danych można zobaczyć  w sekcji  Odświeżanie przyrostowe na karcie Szczegóły projektu.

Usuń zaznaczenie tego pola, jeśli nie chcesz, aby nowe obrazy wyświetlane w sklepie danych były dodawane do projektu.

## <a name="specify-label-classes"></a>Określanie klas etykiet

Na stronie **Klasy etykiet** określ zestaw klas do kategoryzowania danych. Na dokładność i szybkość etykieterów ma wpływ możliwość wyboru spośród klas. Na przykład zamiast pisowni w pełni chłoniaka i gatunku roślin lub zwierząt, użyj kodu pola lub skracaj go.

Wprowadź jedną etykietę na wiersz. Użyj **+** przycisku , aby dodać nowy wiersz. Jeśli masz więcej niż 3 lub 4 etykiety, ale mniej niż 10, warto poprzearzyć nazwy cyframi ("1: ", "2: "), aby osoby etykietowania mogą przyspieszyć pracę za pomocą klawiszy liczbowych.

## <a name="describe-the-data-labeling-task"></a>Opisywanie zadania etykietowania danych

Ważne jest, aby jasno wyjaśnić zadanie etykietowania. Na stronie **Instrukcje dotyczące etykietowania** możesz dodać link do witryny zewnętrznej w celu etykietowania instrukcji lub podać instrukcje w polu edycji na stronie. Instrukcje są zorientowane na zadania i odpowiednie dla odbiorców. Rozważ następujące pytania:

* Jakie etykiety zobaczą i jak wybiorą spośród nich? Czy istnieje tekst odwołania, do których należy się odwołać?
* Co powinni zrobić, jeśli żadna etykieta nie wydaje się odpowiednia?
* Co powinni zrobić, jeśli wiele etykiet wydaje się odpowiednich?
* Jaki próg ufności powinien mieć zastosowanie do etykiety? Czy chcesz, aby ich "najlepiej odgadnąć", jeśli nie są one pewne?
* Co powinni zrobić z obiektami, które są częściowo okluste lub nakładają się na siebie?
* Co powinni zrobić, jeśli obiekt jest obcięty przy krawędzi obrazu?
* Co powinni zrobić po przesłaniu etykiety, jeśli uważają, że popełnili błąd?

W przypadku pól ograniczonych ważne pytania obejmują:

* Jak jest zdefiniowane pole granicy dla tego zadania? Czy powinien on być całkowicie na wewnętrznym obiekcie, czy powinien być na węzło? Czy należy je przycinać tak ściśle, jak to możliwe, czy jest dopuszczalne?
* Jakiego poziomu ostrożności i spójności oczekujesz od osób etykietujących podczas definiowania pól ograniczonych?
* Jak etykietować obiekt, który jest częściowo wyświetlany na obrazie? 
* Jak etykietować obiekt, który częściowo jest objęty innym obiektem?

>[!NOTE]
> Należy pamiętać, że etykiety będą mogły wybrać pierwsze 9 etykiet przy użyciu kluczy liczbowych 1–9.

## <a name="use-ml-assisted-data-labeling"></a>Korzystanie z etykietowania danych wspomaganych przez ml

Strona **etykietowania wspomagana przez uczenie maszynowe** umożliwia wyzwalanie automatycznych modeli uczenia maszynowego w celu przyspieszenia zadania etykietowania. Na początku projektu etykietowania obrazy są mieszane w kolejności losowej, aby zmniejszyć potencjalne odchylenie. Jednak wszelkie uprzedzenia obecne w zestawie danych zostaną odzwierciedlone w wytrenowany model. Jeśli na przykład 80% obrazów należy do jednej klasy, około 80% danych używanych do trenowania modelu będzie tej klasy. To szkolenie nie obejmuje uczenia aktywnego.


Wybierz *pozycję Włącz etykietowanie wspomagane przez* ml i określ procesor GPU, aby włączyć etykietowanie wspomagane, które składa się z dwóch faz:
* Klastrowanie
* Etykiety wstępne

Dokładna liczba obrazów oznaczonych etykietami niezbędna do rozpoczęcia etykietowania wspomaganych nie jest liczbą stałą.  Może to znacznie różnić się w zależności od projektu etykietowania. W przypadku niektórych projektów czasami można zobaczyć zadania wstępne etykiety lub zadania klastra po ręcznym oznaczeniu etykietami 300 obrazów. Etykietowanie wspomagane ml używa techniki o nazwie *Uczenie* transferowe, która używa wstępnie wytrenowanych modeli do szybkiego rozpoczęcia procesu trenowania. Jeśli klasy zestawu danych są podobne do klas we wstępnie wytrenowanych modelach, etykiety wstępne mogą być dostępne po zaledwie kilkuset ręcznie oznaczonych obrazach. Jeśli zestaw danych znacznie różni się od danych używanych do wstępnego trenowania modelu, może to trwać znacznie dłużej.

Ponieważ końcowe etykiety nadal opierają się na danych wejściowych od etykiety, ta technologia jest czasami nazywana etykietą *"człowiek* w pętli".

> [!NOTE]
> Etykiety danych asystowanych ML nie obsługują domyślnych kont magazynu zabezpieczonych za [siecią wirtualną.](how-to-network-security-overview.md) Do przechowywania danych wspomaganych przez uczenia maszynowego należy użyć konta magazynu, które nie jest domyślne. Konto magazynu inne niż domyślne może być zabezpieczone za siecią wirtualną.

### <a name="clustering"></a>Klastrowanie

Po przesłaniu określonej liczby etykiet model uczenia maszynowego klasyfikacji obrazów zaczyna grupować podobne obrazy.  Te podobne obrazy są prezentowane etykietom na tym samym ekranie w celu przyspieszenia ręcznego tagowania. Klastrowanie jest szczególnie przydatne, gdy etykieter wyświetla siatkę z 4, 6 lub 9 obrazami. 

Po wytrenowania modelu uczenia maszynowego na ręcznie oznaczonych danych model jest obcinany do ostatniej w pełni połączonej warstwy. Obrazy bez etykiet są następnie przekazywane przez obcięty model w procesie powszechnie znanym jako "osadzanie" lub "cechowanie". To osadza każdy obraz w wielowymiarowej przestrzeni zdefiniowanej przez tę warstwę modelu. Obrazy, które znajdują się najbliżej sąsiadów w przestrzeni, są używane do zadań klastrowania. 

Faza klastrowania nie jest wyświetlana dla modeli wykrywania obiektów.

### <a name="prelabeling"></a>Etykiety wstępne

Po przesłaniu wystarczającej ilości etykiet obrazów model klasyfikacji jest używany do przewidywania tagów obrazów. Lub model wykrywania obiektów jest używany do przewidywania pól ograniczonych. Etykieter widzi teraz strony zawierające przewidywane etykiety, które są już obecne na każdym obrazie. W przypadku wykrywania obiektów wyświetlane są również przewidywane pola. Następnie należy przejrzeć te przewidywania i poprawić wszystkie błędnie oznaczone obrazy przed przesłaniem strony.  

Po wytrenowania modelu uczenia maszynowego na ręcznie oznaczonych danych model jest oceniany na zestawie testowym ręcznie oznaczonych obrazów w celu określenia jego dokładności przy różnych progach ufności. Ten proces oceny służy do określenia progu ufności, powyżej którego model jest wystarczająco dokładny, aby pokazać etykiety wstępne. Model jest następnie oceniany pod względem danych bez etykiet. Obrazy z przewidywaniami o większej pewności niż ten próg są używane do wstępnego etykietowania.

## <a name="initialize-the-data-labeling-project"></a>Inicjowanie projektu etykietowania danych

Po zainicjowaniu projektu etykietowania niektóre aspekty projektu są niezmienne. Nie można zmienić typu zadania ani zestawu danych. Możesz *zmodyfikować* etykiety i adres URL opisu zadania. Przed utworzeniem projektu należy dokładnie przejrzeć ustawienia. Po przesłaniu projektu wrócisz do strony głównej **etykietowania** danych, która będzie wyświetlać projekt **jako Inicjowanie**.

> [!NOTE]
> Ta strona może nie być automatycznie odświeżana. Dlatego po wstrzymaniu ręcznie odśwież stronę, aby wyświetlić stan projektu **Utworzony.**

## <a name="run-and-monitor-the-project"></a>Uruchamianie i monitorowanie projektu
Po zainicjowaniu projektu platforma Azure rozpocznie jego uruchamianie. Wybierz projekt na głównej **stronie etykietowania** danych, aby wyświetlić szczegóły projektu

Aby wstrzymać lub ponownie uruchomić projekt, przełącz **stan** Uruchomiony w prawym górnym rogu. Etykiety danych można oznaczać tylko wtedy, gdy projekt jest uruchomiony.

### <a name="dashboard"></a>Pulpit nawigacyjny

Na **karcie Pulpit** nawigacyjny jest przedstawiany postęp zadania etykietowania.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="Pulpit nawigacyjny etykietowania danych":::

Wykres postępu pokazuje, ile elementów zostało oznaczonych etykietą i ile elementów nie zostało jeszcze zrobione.  Elementy oczekujące mogą być:

* Jeszcze nie dodano do zadania
* Uwzględnione w zadaniu, które jest przypisane do etykietki, ale nie zostało jeszcze ukończone 
* W kolejce zadań, które nie zostały jeszcze przypisane

Środkowa sekcja zawiera kolejkę zadań, które nie zostały jeszcze przypisane. Gdy etykietowanie wspomagane przez ml jest wyłączone, w tej sekcji przedstawiono liczbę ręcznych zadań do przypisania. Jeśli etykietowanie wspomagane przez ml jest wł., spowoduje to również pokazanie:

* Zadania zawierające elementy klastrowane w kolejce
* Zadania zawierające elementy ze wstępnie etykietami w kolejce

Ponadto po włączeniu etykietowania wspomaganych przez ml mały pasek postępu pokazuje, kiedy zostanie uruchomiony następny przebieg trenowania.  Sekcje Experiments (Eksperymenty) zawierają linki do każdego z przebiegów uczenia maszynowego.

* Trenowanie — szkolenie modelu w celu przewidywania etykiet
* Walidacja — określa, czy przewidywanie tego modelu będzie używane do wstępnego etykietowania elementów 
* Wnioskowanie — uruchamianie przewidywania dla nowych elementów
* Cechowanie — elementy klastrów (tylko dla projektów klasyfikacji obrazów)

Po prawej stronie znajduje się rozkład etykiet dla zadań, które zostały ukończone.  Należy pamiętać, że w niektórych typach projektów element może mieć wiele etykiet. W takim przypadku łączna liczba etykiet może być większa niż łączna liczba elementów.

### <a name="data-tab"></a>Karta Dane

Na karcie **Dane** możesz zobaczyć zestaw danych i przejrzeć dane z etykietami. Jeśli dane są wyświetlane niepoprawnie oznaczone etykietami, zaznacz je i wybierz pozycję Odrzuć, co spowoduje usunięcie etykiet i oznaczenie danych z powrotem w kolejce bez etykiet.

### <a name="details-tab"></a>Karta Szczegóły

Wyświetl szczegóły projektu.  Na tej karcie można:

* Wyświetlanie szczegółów projektu i wejściowych zestawów danych
* Włączanie odświeżania przyrostowego
* Wyświetlanie szczegółów kontenera magazynu używanego do przechowywania danych wyjściowych z etykietami w projekcie
* Dodawanie etykiet do projektu
* Edytuj instrukcje nadane etykietom
* Edytowanie szczegółów etykietowania wspomaganych przez usługę ML, w tym włączanie/wyłączanie

### <a name="access-for-labelers"></a>Dostęp dla osób etykietujących

Każda osoba, która ma dostęp do obszaru roboczego, może oznaczać dane w projekcie etykietami.  Możesz również dostosować uprawnienia dla etykieterów, aby mieli oni dostęp do etykietowania, ale nie do innych części obszaru roboczego lub projektu etykietowania.  Aby uzyskać więcej informacji, zobacz [Manage access to an Azure Machine Learning workspace](how-to-assign-roles.md)(Zarządzanie dostępem do Azure Machine Learning obszaru roboczego) i dowiedz się, jak utworzyć rolę niestandardową [etykietki](how-to-assign-roles.md#labeler).

## <a name="add-new-label-class-to-a-project"></a>Dodawanie nowej klasy etykiety do projektu

Podczas procesu etykietowania danych może się okazać, że do klasyfikowania obrazów potrzebne są dodatkowe etykiety.  Na przykład możesz dodać etykietę "Nieznany" lub "Inne", aby wskazać mylące obrazy.

Aby dodać co najmniej jedną etykietę do projektu, należy wykonać następujące kroki:

1. Wybierz projekt na głównej **stronie Etykietowanie** danych.
1. W prawym górnym rogu strony  przełącz ustawienie Uruchamianie na **Wstrzymano,** aby zatrzymać działania osób etykietujących.
1. Wybierz kartę **Szczegóły**.
1. Na liście po lewej stronie wybierz pozycję **Klasy etykiet**.
1. W górnej części listy wybierz pozycję **+ Dodaj etykiety Dodaj** ![ etykietę](media/how-to-create-labeling-projects/add-label.png)
1. W formularzu dodaj nową etykietę i wybierz sposób kontynuowania.  Ponieważ zmieniono dostępne etykiety dla obrazu, możesz wybrać sposób traktowania już oznaczonych danych:
    * Zacznij od początku, usuwając wszystkie istniejące etykiety.  Wybierz tę opcję, jeśli chcesz rozpocząć etykietowanie od początku od nowego pełnego zestawu etykiet. 
    * Zacznij od początku, zachowując wszystkie istniejące etykiety.  Wybierz tę opcję, aby oznaczyć wszystkie dane jako bez etykiet, ale zachować istniejące etykiety jako tag domyślny dla obrazów, które były wcześniej oznaczone etykietami.
    * Kontynuuj, zachowując wszystkie istniejące etykiety. Wybierz tę opcję, aby zachować wszystkie dane, które są już oznaczone, i zacznij używać nowej etykiety dla danych, które nie zostały jeszcze oznaczone.
1. Zmodyfikuj stronę instrukcji zgodnie z potrzebami dla nowych etykiet.
1. Po dodaniu wszystkich nowych etykiet w prawym górnym  rogu strony przełącz opcję Wstrzymano na **uruchomioną,** aby ponownie uruchomić projekt.  

## <a name="export-the-labels"></a>Eksportowanie etykiet

Dane etykiety można wyeksportować do Machine Learning w dowolnym momencie. Etykiety obrazów można eksportować w formacie [COCO](http://cocodataset.org/#format-data) lub jako Azure Machine Learning [danych z etykietami](how-to-use-labeled-dataset.md). Użyj **przycisku Eksportuj** na **stronie Szczegóły projektu** projektu etykietowania.

Plik COCO jest tworzony w domyślnym magazynie obiektów blob Azure Machine Learning w folderze *w folderze export/coco.* Dostęp do wyeksportowanego zestawu Azure Machine Learning danych można uzyskać w sekcji **Zestawy** danych Machine Learning. Strona szczegółów zestawu danych zawiera również przykładowy kod, który umożliwia dostęp do etykiet z języka Python.

![Wyeksportowany zestaw danych](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli widzisz którykolwiek z tych problemów, skorzystaj z tych wskazówek.

|Problem  |Rozwiązanie  |
|---------|---------|
|Można używać tylko zestawów danych utworzonych w magazynach danych obiektów blob.     |  Jest to znane ograniczenie bieżącej wersji.       |
|Po utworzeniu projekt będzie przez długi czas wyświetlał "Inicjowanie".     | Ręcznie odśwież stronę. Inicjowanie powinno być kontynuowane z około 20 punktami danych na sekundę. Brak funkcji autorefresh jest znanym problemem.         |
|Podczas przeglądania obrazów nowo oznaczone obrazy nie są wyświetlane.     |   Aby załadować wszystkie obrazy z etykietami, wybierz **przycisk Pierwszy.** Przycisk **Pierwszy** spowoduje powrót na początku listy, ale spowoduje ładowanie wszystkich danych z etykietami.      |
|Naciśnięcie klawisza Esc podczas etykietowania w celu wykrycia obiektu powoduje utworzenie etykiety o rozmiarze zero w lewym górnym rogu. Przesyłanie etykiet w tym stanie kończy się niepowodzeniem.     |   Usuń etykietę, klikając znak krzyżowy obok tej etykiety.  |
|Nie można przypisać zestawu zadań do określonego etykiety.     |   Jest to znane ograniczenie bieżącej wersji.  |

## <a name="next-steps"></a>Następne kroki

* [Samouczek: tworzenie pierwszego projektu etykietowania klasyfikacji obrazów.](tutorial-labeling.md)
* Etykietowanie obrazów [w celu klasyfikacji obrazów lub wykrywania obiektów](how-to-label-images.md)
* Dowiedz się więcej o [Azure Machine Learning i Machine Learning Studio (wersja klasyczna)](./overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
