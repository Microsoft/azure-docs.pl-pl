---
title: Ulepszanie modelu — Custom Vision Service
titleSuffix: Azure Cognitive Services
description: W tym artykule dowiesz się, jak ilość, jakość i różnorodność danych mogą poprawić jakość modelu w usłudze Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: ae0112292994fdcf88e80abff8ab52e5971cb0ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "101731089"
---
# <a name="how-to-improve-your-custom-vision-model"></a>Jak ulepszyć model Custom Vision

W tym przewodniku dowiesz się, jak poprawić jakość modelu Custom Vision Service. Jakość usługi [klasyfikator](./getting-started-build-a-classifier.md) lub [detektora obiektów](./get-started-build-detector.md) zależy od ilości, jakości i różnorodności danych z etykietami, które zapewniasz i jak jest zrównoważony ogólny zestaw danych. Dobry model ma zrównoważony zestaw danych szkoleniowych, który jest reprezentatywny dla tego, co zostanie przesłane do niego. Proces tworzenia takiego modelu jest iteracyjny; często Poświęć kilka rund szkolenia, aby dotrzeć do oczekiwanych wyników.

Poniżej znajduje się ogólny wzorzec, który ułatwia uczenie się bardziej precyzyjnym modelem:

1. Szkolenie z pierwszego zaokrąglenia
1. Dodaj więcej obrazów i Zrównoważ dane; ponowne szkolenie
1. Dodawanie obrazów z różnymi tłem, oświetleniem, rozmiarem obiektu, kątem kamery i stylem; ponowne szkolenie
1. Użyj nowych obrazów do przewidywania testów
1. Modyfikuj istniejące dane szkoleniowe zgodnie z wynikami przewidywania

## <a name="prevent-overfitting"></a>Zapobiegaj przestępowaniu

Czasami model zapoznaje się z wykonywaniem prognoz na podstawie wszelkich cech, które są wspólne dla obrazów. Na przykład, jeśli tworzysz klasyfikator dla jabłek i owoców cytrusowych i używasz obrazów jabłek w ręce i owoców cytrusowych na białych płytach, Klasyfikator może dawać nieuzasadnione znaczenie dla rąk i płyt, a nie z jabłek i owoców cytrusowych.

![Obraz nieoczekiwanej klasyfikacji](./media/getting-started-improving-your-classifier/unexpected.png)

Aby rozwiązać ten problem, podaj obrazy z różnymi kątami, tłem, rozmiarem obiektu, grupami i innymi odmianami. Poniższe sekcje rozszerzają się w oparciu o te pojęcia.

## <a name="data-quantity"></a>Ilość danych

Liczba obrazów szkoleniowych jest najważniejszym czynnikiem dla zestawu danych. Zalecamy używanie co najmniej 50 obrazów na etykietę jako punkt wyjścia. Dzięki mniejszej liczbie obrazów istnieje większe ryzyko związane z przepełnieniem, a w czasie, gdy liczby wydajności mogą zasugerować dobrą jakość, model może mieć problemy z rzeczywistymi danymi. 

## <a name="data-balance"></a>Saldo danych

Ważne jest również uwzględnienie względnych ilości danych szkoleniowych. Na przykład za pomocą obrazów 500 dla jednej etykiety i obrazów 50 dla innej etykiety tworzy niezrównoważony zestaw danych szkoleniowych. Spowoduje to dokładniejsze określenie modelu w celu przewidzenia jednej etykiety od innej. Prawdopodobnie widzisz lepsze wyniki, jeśli zachowasz co najmniej 1:2 współczynnik między etykietą z najmniejszą liczbą obrazów i etykietą z większością obrazów. Na przykład jeśli etykieta z większością obrazów zawiera 500 obrazów, etykieta z najmniejszą liczbą obrazów powinna mieć co najmniej 250 obrazów do szkolenia.

## <a name="data-variety"></a>Odmiana danych

Upewnij się, że używasz obrazów reprezentatywnych dla elementów, które zostaną przesłane do klasyfikatora podczas normalnego użytkowania. W przeciwnym razie model może dowiedzieć się, jak tworzyć przewidywania na podstawie wszelkich cech, które są wspólne dla obrazów. Na przykład, jeśli tworzysz klasyfikator dla jabłek i owoców cytrusowych i używasz obrazów jabłek w ręce i owoców cytrusowych na białych płytach, Klasyfikator może dawać nieuzasadnione znaczenie dla rąk i płyt, a nie z jabłek i owoców cytrusowych.

![Obraz nieoczekiwanej klasyfikacji](./media/getting-started-improving-your-classifier/unexpected.png)

Aby rozwiązać ten problem, należy dołączyć różne obrazy, aby upewnić się, że model może być odpowiednio uogólnienie. Poniżej przedstawiono kilka sposobów, w których można ustawić więcej różnorodnych szkoleń:

* __Tło:__ Dostarczaj obrazy obiektu przed różnymi tłem. Fotografie w kontekście naturalnym są lepsze niż zdjęcia przed neutralnym tłem, ponieważ zawierają więcej informacji dotyczących klasyfikatora.

    ![Obraz przykładów w tle](./media/getting-started-improving-your-classifier/background.png)

* __Oświetlenie:__ Dostarczaj obrazy z różnymi oświetleniem (czyli z użyciem technologii Flash, wysokiego narażenia itd.), zwłaszcza jeśli obrazy używane do przewidywania mają różne oświetlenie. Warto również używać obrazów z różnymi nasyceniami, odcieniami i jasnością.

    ![Obraz przedstawiający próbki oświetlenia](./media/getting-started-improving-your-classifier/lighting.png)

* __Rozmiar obiektu:__ Podaj obrazy, w których obiekty różnią się wielkością i liczbie (na przykład zdjęcie pęczków bananów i Closeup jednego bananu). Różne rozmiary ułatwiają uogólnienie klasyfikatora.

    ![Obraz przedstawiający rozmiar próbek](./media/getting-started-improving-your-classifier/size.png)

* __Kąt kamery:__ Dostarczaj obrazy z różnymi kątami aparatu. Alternatywnie, jeśli wszystkie zdjęcia muszą zostać pobrane przy użyciu stałych kamer (takich jak aparaty nadzoru), należy przypisać inną etykietę do każdego regularnego obiektu, aby uniknąć zamontowania &mdash; interpretowania niepowiązanych obiektów (takich jak lampposts) jako funkcji klucza.

    ![Obraz przykładów kątowych](./media/getting-started-improving-your-classifier/angle.png)

* __Styl:__ Dostarczaj obrazy o różnych stylach tej samej klasy (na przykład różne odmiany tego samego owocu). Jeśli jednak masz obiekty z istotnie różnymi stylami (np. myszą Mickey a myszą w czasie rzeczywistym), zalecamy etykietowanie ich jako oddzielnych klas w celu lepszego reprezentowania ich odrębnych funkcji.

    ![Obraz przykładów stylu](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images-classifiers-only"></a>Obrazy ujemne (tylko klasyfikatory)

Jeśli używasz klasyfikatora obrazu, może być konieczne dodanie _próbek ujemnych_ , aby zwiększyć dokładność klasyfikatora. Próbki negatywne to obrazy, które nie pasują do żadnego z innych tagów. Po przekazaniu tych obrazów Zastosuj do nich specjalną **ujemną** etykietę.

Wykrywacze obiektów automatycznie obsługują próbki ujemne, ponieważ wszystkie obszary obrazu poza rysowanymi polami są uznawane za ujemne.

> [!NOTE]
> Custom Vision Service obsługuje automatyczną obsługę obrazów ujemnych. Na przykład jeśli tworzysz klasyfikator winogron a banany i przesyłasz obraz butów do prognozowania, klasyfikator powinien wyrównać ten obraz jak blisko 0% dla moszczu gronowego i bananu.
> 
> Z drugiej strony, w przypadkach, gdy obrazy negatywne są tylko odmianą obrazów używanych w szkoleniu, prawdopodobnie model klasyfikowanie obrazów negatywnych jako klasy oznaczonej przez bardzo podobne. Na przykład jeśli masz klasyfikatora pomarańczowego i grejpfrutowego, a następnie utworzysz obraz Clementine, może to spowodować wygenerowanie Clementine jako pomarańczowego, ponieważ wiele funkcji Clementine przypomina te dla pomarańczy. Jeśli nie ma tego rodzaju obrazów negatywnych, zalecamy utworzenie co najmniej jednego dodatkowego znacznika (na przykład **inne**) i etykietowanie obrazów negatywnych z tym tagiem podczas szkolenia, aby umożliwić modelowi lepsze rozróżnienie między tymi klasami.

## <a name="consider-occlusion-and-truncation-object-detectors-only"></a>Rozważ zamknięcia i obcinanie (tylko wykrywacze obiektów)

Jeśli chcesz, aby detektor obiektów wykrył obcinane obiekty (obiekt jest częściowo wycięty z obrazu) lub obiekty zamknięte (obiekt jest częściowo zablokowany przez inny obiekt w obrazie), należy dołączyć obrazy szkoleniowe, które obejmują te przypadki.

> [!NOTE]
> Problem z obiektami zamknięte przez inne obiekty nie należy mylić z **progiem nakładania** się, czyli parametrem wydajności modelu oceniania. Suwak **nakładania się progu** w [witrynie sieci Web Custom Vision](https://customvision.ai) zawiera informacje o tym, jak długo przewidywane pole ograniczające musi pokrywać się z rzeczywistym polem ograniczenia, które ma być uznawane za poprawne.

## <a name="use-prediction-images-for-further-training"></a>Korzystanie z obrazów predykcyjnych do dalszych szkoleń

W przypadku używania lub testowania modelu przez przesłanie obrazów do punktu końcowego przewidywania usługa Custom Vision przechowuje te obrazy. Można ich następnie użyć do usprawnienia modelu.

1. Aby wyświetlić obrazy przesłane do modelu, Otwórz [stronę sieci web Custom Vision](https://customvision.ai), przejdź do projektu i wybierz kartę __przewidywania__ . Widok domyślny pokazuje obrazy z bieżącej iteracji. Możesz użyć menu rozwijanego __iteracja__ , aby wyświetlić obrazy przesłane podczas poprzednich iteracji.

    ![zrzut ekranu przedstawiający kartę przewidywania z obrazami w widoku](./media/getting-started-improving-your-classifier/predictions.png)

2. Umieść kursor na obrazie, aby zobaczyć znaczniki, które zostały przewidywalne przez model. Obrazy są sortowane w taki sposób, że te, które mogą przynieść największą poprawę modelu, znajdują się u góry. Aby użyć innej metody sortowania, dokonaj wyboru w sekcji __sortowania__ . 

    Aby dodać obraz do istniejących danych szkoleniowych, wybierz obraz, ustaw poprawne Tagi, a następnie kliknij przycisk __Zapisz i Zamknij__. Obraz zostanie usunięty z __prognoz__ i dodany do zestawu obrazów szkoleniowych. Możesz ją wyświetlić, wybierając kartę __obrazy szkoleniowe__ .

    ![Obraz strony tagowania](./media/getting-started-improving-your-classifier/tag.png)

3. Następnie użyj przycisku __uczenie__ , aby ponownie przeprowadzić uczenie modelu.

## <a name="visually-inspect-predictions"></a>Wizualne badanie prognoz

Aby sprawdzić przewidywania obrazu, przejdź do karty __obrazy szkoleniowe__ , wybierz poprzednią iterację szkoleniową w menu rozwijanym **iteracja** i sprawdź co najmniej jeden tag w sekcji **Tagi** . Widok powinien teraz wyświetlać czerwone pole wokół każdego obrazu, dla którego model nie mógł prawidłowo przewidzieć danego tagu.

![Obraz historii iteracji](./media/getting-started-improving-your-classifier/iteration.png)

Czasami Inspekcja wizualizacji może identyfikować wzorce, które można następnie poprawić, dodając więcej danych szkoleniowych lub modyfikując istniejące dane szkoleniowe. Na przykład klasyfikator dla jabłek i WAPN może nieprawidłowo oznaczyć wszystkie zielone jabłka jako wapna. Możesz rozwiązać ten problem, dodając i dostarczając dane szkoleniowe, które zawierają oznakowane obrazy zielonych jabłek.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku przedstawiono kilka technik, które umożliwiają dokładniejsze Tworzenie modelu klasyfikacji obrazów niestandardowych lub modelu wykrywania obiektów. Następnie Dowiedz się, jak programowo przetestować obrazy przez przesłanie ich do interfejsu API przewidywania.

> [!div class="nextstepaction"]
> [Używanie interfejsu API prognozowania](use-prediction-api.md)