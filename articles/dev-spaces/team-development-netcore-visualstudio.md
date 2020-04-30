---
title: Programowanie zespołowe za pomocą platformy .NET Core i programu Visual Studio
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: W tym samouczku pokazano, jak używać Azure Dev Spaces i programu Visual Studio do opracowywania zespołu w aplikacji platformy .NET Core w usłudze Azure Kubernetes Service
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s '
ms.openlocfilehash: c84c77fe7a425318700903427ff1c4aaa4e73a11
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82166040"
---
# <a name="team-development-using-net-core-and-visual-studio-with-azure-dev-spaces"></a>Programowanie zespołowe za pomocą platformy .NET Core i programu Visual Studio z Azure Dev Spaces

Z tego samouczka dowiesz się, jak zespół deweloperów może równocześnie współpracować w tym samym klastrze Kubernetes przy użyciu usługi Dev Spaces.

## <a name="learn-about-team-development"></a>Więcej informacji na temat programowania zespołowego

Do tej pory kod aplikacji był uruchamiany tak, jakby był tylko jeden deweloper pracujący na aplikacji. W tej sekcji dowiesz się, jak usługa Azure Dev Spaces upraszcza programowanie zespołowe:
* Pozwól zespołowi deweloperów pracować w tym samym środowisku: w zależności od potrzeb w udostępnionej przestrzeni deweloperskiej lub w odrębnych przestrzeniach deweloperskich.
* Środowisko umożliwia każdemu deweloperowi iterację na jego kodzie w izolacji i bez obaw o przeszkadzanie pozostałym.
* Przed zatwierdzeniem kodu można go kompleksowo przetestować bez konieczności tworzenia makiet czy symulowania zależności.

### <a name="challenges-with-developing-microservices"></a>Problemy z tworzeniem mikrousług
W tej chwili przykładowa aplikacja nie jest skomplikowana. Jednak w rzeczywistym środowisku deweloperskim w miarę dodawania kolejnych usług i rozwoju zespołu programistycznego szybko pojawiają się problemy.

* Maszyna deweloperska może nie mieć wystarczających zasobów, aby uruchamiać jednocześnie wszystkie potrzebne usługi.
* W przypadku niektórych usług może być wymagana dostępność publiczna. Na przykład w usłudze może być wymagany punkt końcowy, który odpowiada na element webhook.
* Jeśli chcesz uruchomić podzestaw usług, musisz znać pełną hierarchię zależności między wszystkimi swoimi usługami. Określenie tej hierarchii może być trudne, szczególnie, jeśli liczba usług się zwiększa.
* Niektórzy deweloperzy uciekają się do symulowania lub budowania makiet wielu zależności swoich usług. Takie podejście może pomóc, jednak zarządzanie tymi makietami może szybko zwiększyć koszt programowania. Ponadto takie podejście prowadzi do tego, że środowisko deweloperskie jest inne, niż środowisko produkcyjne, co może prowadzić do występowania małych błędów.
* Z tego względu przeprowadzenie jakiegokolwiek testu integracji staje się trudne. Testowanie integracji realnie może nastąpić po zatwierdzeniu, co oznacza, że problemy pojawią się na dalszych etapach cyklu programowania.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Praca w udostępnionej przestrzeni deweloperskiej
W usłudze Azure Dev Spaces możesz skonfigurować *udostępnione* przestrzenie deweloperskie na platformie Azure. Każdy deweloper może skupić się wyłącznie na swojej części aplikacji oraz iteracyjnie tworzyć *wstępnie zatwierdzony kod* w przestrzeni deweloperskiej zawierającej już wszystkie pozostałe usługi i zasoby w chmurze, od których zależą jego scenariusze. Zależności są zawsze aktualne, a deweloperzy pracują w sposób odzwierciedlający środowisko produkcyjne.

### <a name="work-in-your-own-space"></a>Praca we własnym obszarze
Kiedy tworzysz kod dla swojej usługi i nie jest on jeszcze gotowy do zaewidencjonowania, często znajduje się on w niezbyt dobrym stanie. Wciąż w sposób iteracyjny go kształtujesz, testujesz i eksperymentujesz z rozwiązaniami. Usługa Azure Dev Spaces wprowadza pojęcie **miejsca**, które umożliwia pracę w izolacji i bez obaw o przeszkadzanie członkom zespołu.

## <a name="use-dev-spaces-for-team-development"></a>Programowanie zespołowe za pomocą usługi Dev Spaces
Przedstawimy te pomysły w konkretnym przykładzie przy użyciu naszej aplikacji przykładowej *webfrontonu* -> *mywebapi* . Wyobraźmy sobie scenariusz, w którym programista Scott musi wprowadzić zmianę w usłudze *mywebapi* — i *tylko* w tej usłudze. Usługa *webfrontend* nie musi być zmieniana w ramach aktualizacji Scotta.

_Bez_ używania usługi Dev Spaces Scott miałby kilka sposobów na opracowanie i przetestowanie swojej aktualizacji, jednak żaden z nich nie jest idealny:
* Uruchomienie WSZYSTKICH składników lokalnie, co wymaga bardziej zaawansowanego komputera deweloperskiego z zainstalowaną platformą Docker i potencjalnie z rozwiązaniem MiniKube.
* Uruchomienie WSZYSTKICH składników w izolowanej przestrzeni nazw w klastrze Kubernetes. Ponieważ usługa *webfrontend* nie zmienia się, użycie izolowanej przestrzeni nazw jest marnowaniem zasobów klastra.
* Uruchomienie TYLKO usługi *mywebapi* i wykonanie ręcznych wywołań REST w celu testowania. Tego rodzaju testy nie umożliwiają sprawdzenia całego przepływu od początku do końca.
* Dodanie do usługi *webfrontend* skoncentrowanego kodu, który umożliwia deweloperowi wysyłanie żądań do innego wystąpienia usługi *mywebapi*. Dodanie tego kodu komplikuje usługę *webfrontend*.

### <a name="set-up-your-baseline"></a>Konfigurowanie punktu odniesienia
Najpierw musimy wdrożyć punkt odniesienia naszych usług. To wdrożenie będzie reprezentować „ostatni znany dobry” punkt, aby można było łatwo porównać zachowanie kodu lokalnego z wersją zaewidencjonowaną. Następnie, na podstawie tego punktu odniesienia, utworzymy przestrzeń podrzędną, aby umożliwić testowanie zmian w usłudze *mywebapi* w kontekście większej aplikacji.

1. Klonuj [przykładową aplikację do tworzenia miejsc dev](https://github.com/Azure/dev-spaces):`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Zapoznaj się z gałęzią zdalną *azds_updates*:`git checkout -b azds_updates origin/azds_updates`
1. Zamknij wszystkie sesje F5/debugowania dla obu usług, ale nie zamykaj projektów w oknach programu Visual Studio.
1. Przełącz się na okno programu Visual Studio z projektem _mywebapi_.
1. Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Właściwości**.
1. Wybierz kartę **Debugowanie** po lewej stronie, aby wyświetlić ustawienia usługi Azure Dev Spaces.
1. Wybierz pozycję **Zmień**, aby utworzyć przestrzeń, która będzie używana, gdy naciśniesz klawisz F5 lub klawisze Ctrl+F5 dla usługi.
1. Na liście rozwijanej miejsce wybierz pozycję ** \<Utwórz nowe miejsce... \>**.
1. Upewnij się, że w obszarze nadrzędnym jest ustawiona wartość ** \<brak\>**, a następnie wprowadź nazwę obszaru **dev**. Kliknij przycisk OK.
1. Naciśnij klawisze Ctrl+F5, aby uruchomić usługę _mywebapi_ bez w dołączonego debugera.
1. Przełącz się na okno programu Visual Studio z projektem _webfrontend_, a następnie naciśnij klawisze Ctrl+F5, aby także go uruchomić.

> [!Note]
> Czasami po pierwszym wyświetleniu strony internetowej w wyniku naciśnięcia klawiszy Ctrl+F5 trzeba odświeżyć przeglądarkę.

> [!TIP]
> Powyższe kroki pozwalają ręcznie skonfigurować punkt odniesienia, jednak zalecamy, aby zespoły używały ciągłej integracji/ciągłego wdrażania w celu automatycznego aktualizowania punktu odniesienia zgodnie z zatwierdzonym kodem.
>
> Zapoznaj się z naszym [przewodnikiem konfigurowania ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure DevOps](how-to/setup-cicd.md), aby utworzyć przepływ pracy podobny do przedstawionego na poniższym diagramie.
>
> ![Przykładowy diagram ciągłej integracji/ciągłego wdrażania](media/common/ci-cd-complex.png)

Każda osoba otwierająca publiczny adres URL i przechodząca do aplikacji internetowej wywoła napisaną przez Ciebie ścieżkę kodu, która wykona obie usługi, używając domyślnej przestrzeni _dev_. Teraz załóżmy, że chcesz kontynuować tworzenie aplikacji *mywebapi*. Jak możesz to zrobić, nie przerywając pracy innych deweloperów, którzy korzystają z przestrzeni deweloperskiej? W tym celu skonfigurujesz własne miejsce.

### <a name="create-a-new-dev-space"></a>Tworzenie nowego obszaru deweloperskiego
W programie Visual Studio możesz utworzyć dodatkowe miejsca, które będą używane po naciśnięciu klawiszy F5 lub Ctrl+F5 w usłudze. Miejscu możesz nadać dowolnie wybraną nazwę, która może mieć dowolne znaczenie (np. _sprint4_ lub _pokaz_).

Wykonaj następujące czynności, aby utworzyć nowe miejsce:
1. Przełącz się na okno programu Visual Studio z projektem *mywebapi*.
2. Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Właściwości**.
3. Wybierz kartę **Debugowanie** po lewej stronie, aby wyświetlić ustawienia usługi Azure Dev Spaces.
4. W tym miejscu możesz zmienić lub utworzyć klaster i/lub miejsce, które będzie używane po naciśnięciu klawiszy F5 lub Ctrl+F5. *Upewnij się, że jest wybrane utworzone wcześniej miejsce usługi Azure Dev Spaces*.
5. Na liście rozwijanej miejsce wybierz pozycję ** \<Utwórz nowe miejsce... \>**.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. W oknie dialogowym **Dodaj przestrzeń** ustaw dla przestrzeni nadrzędnej wartość **dev**, a następnie wprowadź nazwę nowej przestrzeni. Jako nazwy nowego miejsca możesz użyć swojego imienia (na przykład „scott”), aby Twoi współpracownicy mogli łatwo zidentyfikować miejsce, w którym pracujesz. Kliknij przycisk **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Teraz powinien zostać wyświetlony klaster usługi AKS i nowy obszar wybrany na stronie właściwości projektu.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Zaktualizuj kod dla aplikacji *mywebapi*

1. W projekcie *mywebapi* wprowadź zmianę kodu metody `string Get(int id)` w pliku `Controllers/ValuesController.cs` w następujący sposób:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Ustaw punkt przerwania w tym zaktualizowanym bloku kodu (być może masz już ustawiony wcześniej).
3. Naciśnij klawisz F5, aby uruchomić usługę _mywebapi_, co spowoduje uruchomienie usługi w klastrze przy użyciu wybranej przestrzeni. W tym przypadku wybrana przestrzeń to _scott_.

Poniżej przedstawiono diagram, który pomoże Ci zrozumieć, jak działają różne miejsca. Fioletowa ścieżka przedstawia żądanie za pośrednictwem przestrzeni _dev_, która jest domyślną ścieżką używaną, gdy do adresu URL nie jest dołączona żadna przestrzeń. Różowa ścieżka przedstawia żądanie za pośrednictwem przestrzeni _dev/scott_.

![](media/common/Space-Routing.png)

Ta wbudowana funkcja usługi Azure Dev Spaces umożliwia kompleksowe testowanie kodu w udostępnionym środowisku bez konieczności ponownego tworzenia pełnych stosów usług przez wszystkich deweloperów w ich miejscach. Taki routing wymaga przekazywania nagłówków propagowania w kodzie aplikacji, jak pokazano w poprzednim kroku tego przewodnika.

### <a name="test-code-running-in-the-_devscott_-space"></a>Testowanie kodu uruchamianego w przestrzeni _dev/scott_
Aby przetestować nową wersję *mywebapi* w połączeniu z *webfrontonem*, Otwórz przeglądarkę do adresu URL punktu dostępu publicznego dla *webfrontonu* (na przykład `http://dev.webfrontend.123456abcdef.eus.azds.io`) i przejdź do strony informacje. Powinien zostać wyświetlony pierwotny komunikat „Hello from webfrontend and Hello from mywebapi”.

Teraz dodaj część „scott.s.” należy do adresu URL, który odczytuje coś, takiego\:jak http//Scott.s.dev.webfrontend.123456abcdef.EUS.azds.IO, i Odśwież przeglądarkę. Powinien zostać osiągnięty punkt przerwania ustawiony w projekcie *mywebapi*. Naciśnij klawisz F5, aby kontynuować. W przeglądarce powinien zostać wyświetlony nowy komunikat „Hello from webfrontend and mywebapi now says something new”. Jest to spowodowane tym, że ścieżka do zaktualizowanego kodu w aplikacji *mywebapi* jest uruchamiana w przestrzeni _dev/scott_.

Mamy nadzieję, że po utworzeniu przestrzeni _dev_, która zawsze zawiera najnowsze zmiany, i zakładając, że aplikacja jest zaprojektowana tak, aby wykorzystywać oparty na przestrzeniach routing usługi DevSpace, łatwo będzie zobaczyć, że usługa Dev Spaces może znacznie ułatwić testowanie nowych funkcji w kontekście większej aplikacji. Zamiast wdrażać _wszystkie_ usługi w przestrzeni prywatnej, możesz utworzyć przestrzeń prywatną, która wywodzi się z przestrzeni _dev_, i uruchamiać tylko te usługi, nad którymi tak naprawdę pracujesz. Infrastruktura routingu usługi Dev Space zajmie się resztą, wykorzystując tyle usług spoza przestrzeni prywatnej, ile uda jej się znaleźć, jednocześnie przyjmując domyślnie najnowszą wersję uruchomioną w przestrzeni _dev_. A co najlepsze, _wielu_ deweloperów może aktywnie programować różne usługi w tym samym czasie we własnych przestrzeniach, nie przeszkadzając sobie nawzajem.

### <a name="well-done"></a>Gotowe!
Przewodnik Wprowadzenie został ukończony! W tym samouczku omówiono:

> [!div class="checklist"]
> * Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
> * Iteracyjne tworzenie kodu w kontenerach.
> * Niezależne tworzenie dwóch oddzielnych usług i wywoływanie innej usługi przy użyciu funkcji odnajdywania usług DNS w środowisku Kubernetes.
> * Efektywne tworzenie i testowanie kodu w środowisku zespołu.
> * Ustalanie punktu odniesienia funkcjonalności za pomocą usługi Dev Spaces w celu łatwego testowania izolowanych zmian w kontekście większej aplikacji mikrousług.

Teraz, gdy znasz już usługę Azure Dev Spaces, [udostępnij swoją przestrzeń deweloperów członkowi zespołu](how-to/share-dev-spaces.md) i pokaż, jak łatwo możecie ze sobą współpracować.

## <a name="clean-up"></a>Czyszczenie
Aby całkowicie usunąć z klastra wystąpienie usługi Azure Dev Spaces, w tym wszystkie przestrzenie deweloperów i usługi działające w ich obrębie, należy użyć polecenia `az aks remove-dev-spaces`. Pamiętaj, że ta akcja jest nieodwracalna. Możesz ponownie dodać obsługę usługi Azure Dev Spaces w klastrze, ale wszystko trzeba będzie zacząć od początku. Stare usługi i przestrzenie nie zostaną przywrócone.

Poniższy przykład wyświetla listę kontrolerów usługi Azure Dev Spaces w Twojej aktywnej subskrypcji, a następnie usuwa kontroler usługi Azure Dev Spaces, który jest skojarzony z klastrem usługi AKS „myaks” w grupie zasobów „myaks-rg”.

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
