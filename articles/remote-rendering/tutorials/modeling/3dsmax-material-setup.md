---
title: Konfigurowanie materiałów renderowania opartych na fizycznie w 3DSMax
description: Wyjaśniono, jak skonfigurować fizyczne materiały renderowania w 3DSMax i wyeksportować je do formatu FBX.
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: df4be8963c93199f9fad23ab3f709f691e1da768
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85880116"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3d-studio-max"></a>Samouczek: Konfigurowanie fizycznie opartych na materiałach renderowania w programie 3D Studio Max

## <a name="overview"></a>Omówienie
Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

>[!div class="checklist"]
>
> * Przypisuj materiały z zaawansowanymi oświetleniem do obiektów w scenie.
> * Obsługa wystąpień obiektów i materiałów.
> * Wyeksportuj scenę do formatu FBX i wybierz ważne opcje.

Tworzenie [materiałów renderowania opartego na fizycznie (PBR)](../../overview/features/pbr-materials.md) w programie 3D Studio Max (3dsMax) to stosunkowo proste zadanie. Podobnie jak w przypadku innych aplikacji do tworzenia zawartości, takich jak Maya, jest to podobne na wiele sposobów. Ten samouczek to przewodnik dotyczący podstawowej instalacji modułu cieniującego w programie PBR i eksportowania FBX na potrzeby projektów zdalnego renderowania na platformie Azure.

Przykładowa scena w tym samouczku zawiera wiele obiektów pól wielokątów. Są do nich przypisane różne materiały, takie jak drewno, metal, malowanie metalowe, plastikowe i gumowe. Ogólnie mówiąc, każdy materiał zawiera wszystkie lub większość następujących tekstur:

* **Albedo**, czyli Mapa kolorów materiału, jest również nazywana **rozpraszaniem** lub **BaseColor**.
* **Metalowy**, który określa, czy materiał jest metaliczny i które części są metalowe. 
* **Niesztywność**, która określa, jak sztywno lub gładko powierzchnię.
Ma także wpływ na ostrość lub blurriness odbicia i wyróżnienia na powierzchni.
* **Normalne**, które dodaje szczegóły do powierzchni bez konieczności dodawania większej liczby wielokątów. Przykłady szczegółów mogą być wygrane i mieć wcięcia na powierzchni metalowej lub ziarna w drewnie.
* **Zamknięcia otoczenia**, który służy do dodawania cieniowania miękkiego i cieniowania do modelu. Jest to mapa w skali szarości, która wskazuje, które obszary modelu otrzymują pełne oświetlenie (białe) lub pełny cień (czarny).

## <a name="prepare-the-scene"></a>Przygotowywanie sceny
W programie **3D Studio Max**proces KONFIGUROWANIA materiału PBR jest następujący:.

Aby zacząć od, jak zobaczysz w przykładowej scenie, utworzymy kilka obiektów Box, z których każdy reprezentuje różne rodzaje materiału:

>[!TIP]
>Warto zwrócić uwagę przed rozpoczęciem tworzenia zasobów dla ARR, które wykorzystują **liczniki** do pomiaru.  
>W związku z tym zalecane jest ustawienie **jednostek systemu** sceny na **liczniki**. Ponadto zaleca się, aby podczas eksportowania skonfigurować jednostki do liczników w ustawieniach eksportu FBX.

Na poniższej ilustracji przedstawiono kroki, które należy wykonać, aby ustawić jednostki systemowe do liczników w programie 3D Studio Max. W menu głównym przejdź do pozycji **Dostosuj**  >  **jednostki**konfiguracja  >  **jednostki systemowej** , a następnie na liście rozwijanej **Skala jednostek systemu** wybierz pozycję **liczniki**. 
![jednostki systemowe](media/3dsmax/system-units.jpg)

Z jednostkami systemowymi ustawionymi na liczniki można zacząć tworzyć modele. W naszej przykładowej scenie tworzymy kilka obiektów Box, z których każdy reprezentuje inny typ materiału — na przykład metal, gumowa, plastyk itp. 

>[!TIP]
>Dobrym sposobem jest utworzenie zasobów w celu napisania odpowiednich nazw. Ułatwi to znalezienie ich w przyszłości, jeśli scena ma wiele obiektów

![Zmień nazwę obiektów](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Przypisywanie materiałów

W przypadku niektórych obiektów utworzonych w naszej scenie — w tym przypadku wiele modułów można rozpocząć konfigurację programu PBR:

* Na głównym pasku narzędzi kliknij ikonę **Edytor materiałów** , jak pokazano na poniższej ilustracji. Możesz również nacisnąć klawisz **M** na klawiaturze, aby otworzyć Edytor. Edytor materiału ma dwa tryby, które można wybrać w menu rozwijanym **tryby** — tryb **edytora materiału kompaktowego** i tryb **surowców** . Ponieważ ta scena jest stosunkowo prosta, będziemy używać **trybu kompaktowego**.

* Wewnątrz edytora materiału zobaczysz wiele sfer — te sfery są materiałami. Przypiszemy jeden z tych materiałów do każdego obiektu, który znajduje się w naszej scenie. Aby wykonać to przypisanie, najpierw wybierz jeden z obiektów w głównym okienku ekranu. Po wybraniu tej opcji kliknij pierwszą sferę w oknie Edytor materiału. Po przypisaniu do obiektu wybrany materiał zostanie wyróżniony, jak pokazano na następnym obrazie.

* Kliknij przycisk **Przypisz materiał do zaznaczenia** , jak pokazano. Wybrany materiał został teraz przypisany do wybranego obiektu.
![Przypisz materiał](media/3dsmax/assign-material.jpg)

W edytorze materiałów można wybrać typy materiałów z szerokiego wyboru, w zależności od przypadku użycia. Typowo, typ materiału jest domyślnie ustawiony na wartość **standardowa** . Ten materiał jest elitarnym materiałem, który nie jest odpowiedni dla instalacji usługi PBR, dlatego należy zmienić typ materiału na materiał PBR. Preferowany materiał **3dsMax** dla projektów zdalnego renderowania na platformie Azure jest **materiałem fizycznym**.

* W edytorze materiału kliknij kartę **Standardowy** , a następnie w przeglądarce materiał/mapa, która otwiera wybór **materiału fizycznego**. Ta akcja spowoduje przekonwertowanie przypisanego materiału **standardowego** do **materiału fizycznego**PBR.
![materiał fizyczny](media/3dsmax/physical-material.jpg)

* W edytorze materiału zostaną wyświetlone właściwości materiału fizycznego (patrz poniżej) i można przypisywać tekstury do elementu zawartości.
![tekstury — lista](media/3dsmax/textures-list.jpg)

Jak widać na powyższym obrazie, istnieje szeroki zakres map i tekstur, które można dodać do materiału. W naszym przypadku będziemy używać tylko pięciu gniazd tekstury w materiale.

>[!TIP]
>Dobrym sposobem jest odpowiednie nazywanie materiałów, jak pokazano na powyższym obrazie.

Teraz możemy zacząć rozważyć przypisanie tekstury do materiału. Sposób generowania tekstur może się różnić w zależności od preferencji lub nawet według użycia. Na przykład może być konieczne użycie tekstury sąsiadujących, które można zastosować do dowolnego elementu zawartości, lub wymagać określonych części projektu/elementu zawartości, aby mieć własny niestandardowy zestaw tekstur. Możesz chcieć użyć ogólnych tekstur do rozdzielania uzyskanych w trybie online lub utworzyć je samodzielnie w aplikacjach, takich jak **Photoshop**, **Quixel Suite**, **pakiet substancji** itp. 

Zanim zaczniemy przypisywać nasze tekstury, należy wziąć pod uwagę Współrzędne tekstury naszych zasobów (UVW). Chociaż najlepszym rozwiązaniem jest zastosowanie dowolnych tekstur do modelu, aby upewnić się, że model został rozpakowany (tekstury nie będą wyświetlane prawidłowo, bez konieczności oddzielenia UV), ważne dla naszych celów, jeśli zamierzamy użyć mapy **zamknięcia otoczenia** w naszym modelu. W przeciwieństwie do **modułu cieniującego Stingray** w **Maya**, **materiał fizyczny** w **3dsMax** nie ma dedykowanego gniazda tekstury **zamknięcia otoczenia** . W związku z tym stosujemy mapę AO w innym gnieździe i umożliwimy jej użycie niezależnie od innych tekstur (na przykład tekstury sąsiadujących), dlatego przypiszemy im kanał mapy UVW. 

Zaczniemy od przypisywania **modyfikatora UVW odwinięcia** do naszego modelu, jak pokazano poniżej:

* W obszarze Edytor właściwości wybranych obiektów kliknij listę modyfikatorów, a następnie na liście rozwijanej zostanie otwarte okno przewijania w dół i wybierz polecenie Cofnij Zawijanie UVW. Ta akcja spowoduje zastosowanie odwinięcia modyfikatora UVW do naszego elementu zawartości.
![unotoka — modyfikator](media/3dsmax/unwrap-modifier.jpg)

* Kanał mapy jest ustawiony na jeden. Jest on w kanale mapy, w którym zwykle zostanie wykonane główne odpakowanie. W naszym przypadku obiekt został rozpakowany bez nakładania się współrzędnych tekstury (UV).
![unopakowany — UVW](media/3dsmax/unwrapped-uvw.jpg)

Następnym krokiem jest utworzenie drugiego kanału mapy UV.

* Zamknij Edytor UV, jeśli jest otwarty, i w sekcji kanał w menu **Edytuj UV** Zmień numer kanału na dwa. Kanał mapy 2 jest oczekiwanym kanałem dla map zamknięcia. 

* W otwartym oknie dialogowym **ostrzeżenia o zmianie kanału** zostanie nadana opcja **przenoszenia** istniejącej wartości UV w kanale 1 do nowego kanału 2 lub **zrezygnować** z istniejących **odwinięcia** UV. Wybierz pozycję **Porzuć** tylko, jeśli zamierzasz utworzyć nowe **odwzorowanie UV** dla mapy zamknięcia otaczającej, która różni się od promienia UV w kanale mapy 1 (na przykład jeśli chcesz użyć tekstury sąsiadujących w kanale 1). W naszym celu **przeniesiemy** wartość UV z kanału 1 do kanału 2, ponieważ nie trzeba edytować nowego kanału UV.

>[!NOTE]
>Nawet jeśli skopiowano- **przeniesiono** — odwinięcie UV z kanału mapy 1 do kanału mapy 2, można wprowadzić wszelkie niezbędne zmiany w nowym kanale UV bez wpływu na oryginalny kanał mapy.

![kanał — zmiana](media/3dsmax/channel-change.jpg)

Po utworzeniu nowego kanału mapy można wrócić do materiału fizycznego w edytorze materiału i zacząć dodawać do niego tekstury. Najpierw dodamy mapę otoczenia zamknięcia (**Ao**), ponieważ trzeba będzie wykonać dalsze czynności, aby umożliwić jej poprawne działanie. Gdy mapa AO jest dołączona do naszego materiału, musimy poinstruować, aby użyć kanału mapy 2.

* Jak wspomniano wcześniej, nie istnieje dedykowane miejsce dla map AO w **materiale fizycznym 3dsMax**. Zamiast tego zastosujemy go mapę AO do gniazda **Niesztywności rozpraszania** .

* Na liście **mapy ogólne** materiału fizycznego kliknij pozycję **Brak gniazda mapy** i **Diffuse Roughness**Załaduj mapę Ao.

* We właściwościach tekstury AO domyślnie zostanie wyświetlony kanał mapy ustawiony na **1** . Zmień tę wartość na **2**. Ta akcja wykonuje kroki niezbędne do dodania mapy zamknięcia otoczenia.

>[!IMPORTANT]
>Jest to ważny krok, szczególnie jeśli wartość UV w kanale 2 różni się od tych w kanale 1, ponieważ AO nie zostanie prawidłowo zamapowana przy użyciu niewłaściwego kanału.

![Assign-Ao-map](media/3dsmax/assign-ao-map.jpg)

Będziemy teraz przypisywać nasze normalne mapy do naszego materiału. Ta akcja różni się nieco od **Maya** w tym, że mapa normalna nie jest stosowana bezpośrednio do gniazda mapy nierówności (w rzeczywistości nie istnieje gniazdo zwykłej mapy w **materiale 3dsMax fizycznym** ), ale zamiast tego jest dodawane do modyfikatora zwykłej mapy, który sam jest podłączony do gniazda **normalne** .

* W sekcji **mapy specjalne** naszych właściwości materiału fizycznego (w edytorze materiału) **kliknij gniazdo mapy** **nierówności**. 

* W przeglądarce materiały/Mapa Znajdź i kliknij **normalne nierówności**. Ta akcja spowoduje dodanie do materiału normalnego modyfikatora **nierówności** .

* W modyfikatorze **normalnej równości** kliknij pozycję **nie Mapuj** , a następnie Znajdź, a następnie załaduj mapę normalną. **Normal**

* Sprawdź, czy metoda jest ustawiona na **tangens** (powinna to być wartość domyślna), i w razie potrzeby przełącz opcję **Przerzuć zielony (Y)**.

![normalne-nierówności ](media/3dsmax/normal-bump.jpg)
 ![ obciążenie — Mapa normalna](media/3dsmax/load-normal-map.jpg)

Po poprawnym przypisaniu normalnej mapy można przystąpić do przypisywania pozostałych tekstur, aby ukończyć konfigurację materiału fizycznego. Ten proces jest prostym procesem bez żadnych specjalnych ustawień do uwzględnienia. Na poniższej ilustracji przedstawiono pełen zestaw tekstur przypisanych do naszego materiału: ![ Wszystkie tekstury](media/3dsmax/all-textures.jpg)

Wraz z utworzonymi i skonfigurowanymi materiałami PBR warto zastanowić się nad tworzeniem wystąpień obiektów na scenie. Tworzenie wystąpień podobnych obiektów w scenie — takich jak orzechy, pioruny, spryskiwacze wkrętów — zasadniczo wszystkie obiekty, które są takie same, mogą przynieść znaczne oszczędności pod względem rozmiaru pliku. Wystąpienia obiektu głównego mogą mieć własną skalę, rotację i przekształcenia, aby można je było umieścić zgodnie z wymaganiami w scenie. W programie **3D Studio Max**proces tworzenia **wystąpień** jest prosty.

* W głównym okienku ekranu wybierz obiekt/obiekty, które chcesz wyeksportować.

* Przytrzymaj klawisz **SHIFT** i przeciągnij zasoby w górę przy użyciu narzędzia Przekształcanie (przenoszenie) 

* W oknie dialogowym **Opcje klonowania** , które jest otwierane, ustaw **obiekt** na **wystąpienie** i kliknij przycisk **OK**. 
![wystąpienie-obiekt](media/3dsmax/instance-object.jpg)

Ta akcja spowoduje utworzenie wystąpienia obiektu, które można przenieść obrócone lub skalowane niezależnie od jego elementu nadrzędnego i innych wystąpień tego elementu nadrzędnego.

>[!IMPORTANT]
>Jednak wszelkie zmiany wprowadzone w wystąpieniu w trybie obiektu podrzędnego zostaną przesłane do wszystkich wystąpień obiektu, dlatego w przypadku pracy z wystąpieniami obiektów wystąpień-wierzchołków, wielokątów itp. należy najpierw upewnić się, że wszystkie zmiany mają wpływ na wszystkie te wystąpienia. Należy pamiętać, że każdy obiekt wystąpienia można w dowolnym momencie wprowadzić do unikatowego obiektu. 

>[!TIP]
>Najlepszym rozwiązaniem dotyczącym tworzenia wystąpień w Twojej scenie jest tworzenie ich w miarę ich wykonywania, ponieważ zastępowanie **kopii** z obiektami wystąpienia później jest niezwykle trudne. 

Przed przejściem do procesu eksportu warto zwrócić uwagę na to, w jaki sposób można utworzyć pakiet sceny/zasobów do udostępnienia. W idealnym przypadku, gdy przekazujesz zasób do klienta lub członka zespołu, chcesz, aby użytkownicy mogli otwierać i wyświetlać elementy zawartości, ponieważ powinny być widoczne z minimalną ilością Fuss. W związku z tym ważne jest, aby zachować ścieżki do tekstury elementów zawartości względem pliku sceny. Jeśli ścieżki tekstury dla elementu zawartości wskazują na dysk lokalny lub ścieżkę bezwzględną lub lokalizację, nie będą ładowane do sceny, jeśli są otwarte na innym komputerze, nawet jeśli plik **. Max** znajduje się w tym samym folderze co tekstury. Zastosowanie ścieżek tekstury względem programu 3D Studio Max rozwiązuje ten problem i jest dość proste.

* Na głównym pasku narzędzi przejdź do **File**  >  **pozycji Spis treści odwołania**do pliku  >  **Asset Tracking Toggle**. 

* W przeglądarce śledzenia zasobów, która zostanie otwarta, zobaczysz wszystkie lub większość tekstur zastosowanych do materiałów PBR wymienionych w kolumnie **mapy/cieniowanie** .

* Obok nich w kolumnie **pełna ścieżka** zostanie wyświetlona ścieżka pliku do lokalizacji tekstury najprawdopodobniej do lokalizacji na komputerze lokalnym.

* Na koniec zobaczysz kolumnę o nazwie **status**. Ta kolumna wskazuje, czy dana tekstura została umieszczona i zastosowana do sceny, czy nie, i spowoduje Oflagowanie tej tekstury jednym z następujących warunków: **OK**, **znalezionych**lub **brakujących plików**. Pierwsze dwa wskazują, że plik został znaleziony i załadowany, podczas gdy ostatni oznacza, że moduł śledzący nie mógł zlokalizować pliku.
![tekstury — ścieżki](media/3dsmax/texture-paths.jpg)

Po pierwszym otwarciu narzędzia do śledzenia zasobów można zauważyć, że nie wszystkie tekstury są wyświetlane. Nie ma żadnej informacji o tym, co jest wykonywane przez proces szukania ścieżki raz lub dwa razy zwykle znajduje wszystkie tekstury sceny. Proces szukania ścieżki jest następujący: 

* W oknie śledzenie zasobów **Shift** + **kliknij** górną teksturę na liście **Maps/cieniowanie** i Kontynuuj, przytrzymując klawisz Shift, a następnie kliknij ostatnią teksturę na liście. Ta akcja spowoduje zaznaczenie wszystkich tekstur na liście. Wybrane tekstury zostaną teraz wyróżnione kolorem niebieskim (zobacz Powyższy obraz).

* Kliknij prawym przyciskiem myszy zaznaczenie i w wyświetlonym menu podręcznym wybierz pozycję **Ustaw ścieżkę**.

* W oknie **Określanie ścieżki zasobu** , który zostanie otwarty, wybierz ścieżkę lokalną do wyświetlanych tekstur i Zastąp ją następującym `.\` i kliknij przycisk **OK**. 

* Po upływie tego czasu (co będzie się różnić w zależności od liczby tekstur w scenie i o tym, jak duża scena), śledzenie zasobów powinno zostać rozwiązane w następujący sposób (Zobacz obraz).
![Rozwiąż — tekstury](media/3dsmax/resolve-textures.jpg)

Zwróć uwagę, że kolumna **pełna ścieżka** jest teraz pusta. Oznacza to, że scena nie będzie już szukać odpowiednich tekstur w określonej (bezwzględnie) lokalizacji, ale zawsze znajdzie je, o ile plik maksymalny lub powiązany plik FBX znajduje się w tym samym folderze co tekstury. 

>[!NOTE]
>Czasami może wystąpić konieczność powtarzania tego procesu kilka razy, aby znaleźć i rozwiązać wszystkie tekstury i ścieżki. Nie dotyczy to niczego, co należy zrobić, po prostu Powtórz do momentu, gdy zostaną uwzględnione wszystkie odpowiednie zasoby. Może być również w przypadku, gdy nie można znaleźć niektórych plików. W takim przypadku wystarczy wybrać wszystkie zasoby na liście i kliknąć pozycję **Usuń brakujące ścieżki** (Zobacz obraz powyżej)

## <a name="fbx-export"></a>Eksport FBX

Po zakończeniu śledzenia zasobów możemy teraz przejść do FBX eksportu. Ten proces jest prosty i można go wykonać na kilka sposobów. 

>[!TIP]
>Dobrym sposobem jest, że jeśli nie chcesz eksportować całej sceny, należy wybrać opcję eksportu tylko tych zasobów, które są potrzebne. W szczególności intensywnie korzystające z zasobów, eksportowanie może zająć dużo czasu, dlatego warto wyeksportować tylko te, czego potrzebujesz
>
>Zaleca się, aby w przypadku użycia modyfikatorów, takich jak **Turbosmooth** lub **Open SubDiv** itp., aby je zwinąć przed eksportem, ponieważ mogą one spowodować problemy podczas eksportowania. Zawsze zapisuj swoją scenę przed wykonaniem tej operacji! 

* W obszarze scena wybierz te zasoby, które chcesz wyeksportować, a następnie na głównym pasku narzędzi przejdź do **pliku**  >  **Eksportuj eksportowanie**  >  **zaznaczone**

* W oknie dialogowym **Wybierz plik do eksportu** wpisz lub wybierz opcję Nazwa pliku wyjściowego i w opcji **Zapisz jako typ** wybierz polecenie **Autodesk (*. FBX)**. Ta akcja spowoduje otwarcie menu Eksportuj FBX. 

* Pamiętaj, że jeśli w swojej scenie zostały utworzone wystąpienia, ważne jest, aby **zachować wystąpienia** w ustawieniach eksportu FBX. 
![FBX — eksport](media/3dsmax/fbx-export.jpg)

Należy pamiętać, że wcześniej istniały kilka sposobów eksportowania pliku. Jeśli zamiarem eksportowania jest, że FBX ma być współużytkowany wraz z plikami tekstury w folderze/katalogu, wówczas ustawienia wyświetlane na poniższym obrazie powinny być stosowane i działać prawidłowo. Po wybraniu ustawień kliknij przycisk **OK**.
![FBX — ustawienia](media/3dsmax/fbx-settings.jpg)

Jeśli jednak wolisz nie udostępniać dużych folderów/katalogów tekstury wraz z FBX, możesz **osadzić** tekstury w FBX. Oznacza to, że wszystkie zawarte tekstury elementów zawartości zostaną dodane do jednego FBXu. Należy pamiętać, że podczas łączenia eksportu z pojedynczym zasobem, że plik FBX będzie znacząco większy.

>[!IMPORTANT]
>Jeśli wynikowy plik FBX jest większy niż 2.4 GB, minimalna wersja ustawień eksportu FBX (patrz powyżej) powinna mieć wartość 2016 lub nowszą. Ponieważ nowsze wersje mają obsługę bitową 64, a tym samym obsługują większe pliki.

* W ustawieniach eksportu FBX Przełącz na * * Osadź multimedia, a następnie kliknij przycisk **OK** , aby wyeksportować z uwzględnieniem tekstury. 

Podczas eksportowania do FBX przy użyciu materiału fizycznego prawdopodobnie zobaczysz następujące ostrzeżenie wyskakujące po kliknięciu przycisku "OK" w oknie dialogowym eksportowania: ![ Export-Warnings](media/3dsmax/export-warnings.jpg)

To ostrzeżenie po prostu informuje użytkownika, że eksportowane materiały mogą nie być zgodne z innymi pakietami oprogramowania. Ponieważ materiał fizyczny jest zgodny z renderowaniem zdalnym platformy Azure, nie ma nic do obaw. Po prostu kliknij przycisk **OK** , aby zakończyć proces i zamknąć okno.

## <a name="conclusion"></a>Podsumowanie

Ogólnie rzecz biorąc, ten typ materiału wygląda bardziej realistycznie, ponieważ jest oparty na rzeczywistej fizyki ziemskiej. Tworzy dodatkowy efekt immersyjny, aby sceny pojawiły się w świecie rzeczywistym.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz, jak skonfigurować materiały z zaawansowanymi oświetleniem dla obiektów w scenie. Wiesz również, jak wyeksportować obiekty do formatu FBX, który jest obsługiwany przez renderowanie zdalne na platformie Azure. Następnym krokiem jest przekonwertowanie pliku FBX i wizualizacja go w ramach renderowania zdalnego na platformie Azure.

>[!div class="nextstepaction"]
>[Szybki Start: konwertowanie modelu do renderowania](../../quickstarts\convert-model.md)