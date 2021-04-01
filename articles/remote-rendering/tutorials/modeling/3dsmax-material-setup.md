---
title: Konfigurowanie materiałów PBR w programie 3ds Max
description: Wyjaśniono, jak skonfigurować fizyczne materiały renderowania w formacie 3ds Max i wyeksportować je do formatu FBX.
author: FlorianBorn71
ms.author: flborn
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 12407d6344c69c747230e9db6fa4d53b4520dc82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96020283"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3ds-max"></a>Samouczek: Konfigurowanie materiałów renderowania fizycznie opartych na protokole 3ds Max

## <a name="overview"></a>Omówienie
Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

>[!div class="checklist"]
>
> * Przypisuj materiały z zaawansowanymi oświetleniem do obiektów w scenie.
> * Obsługa wystąpień obiektów i materiałów.
> * Wyeksportuj scenę do formatu FBX i wybierz ważne opcje.

> [!Note]
> Procedura opisana w tym samouczku działa w tabeli 3ds Max 2019 i 3ds Max 2020.
> Zmiana sposobu, w jaki program 3ds Max 2021 eksportuje wartości mapy, oznacza, że normalne mapy nie zostaną znalezione przez usługę konwersji, jeśli ta wersja jest używana.

Tworzenie [materiałów renderowania (PBR)](../../overview/features/pbr-materials.md) w języku 3ds Max jest bardzo proste. Podobnie jak w przypadku innych aplikacji do tworzenia zawartości, takich jak Maya, jest to podobne na wiele sposobów. Ten samouczek to przewodnik dotyczący podstawowej instalacji modułu cieniującego w programie PBR i eksportowania FBX na potrzeby projektów zdalnego renderowania na platformie Azure.

Przykładowa scena w tym samouczku zawiera wiele obiektów pól wielokątów. Są do nich przypisane różne materiały, takie jak drewno, metal, malowanie metalowe, plastikowe i gumowe. Ogólnie mówiąc, każdy materiał zawiera wszystkie lub większość następujących tekstur:

* **Albedo**, czyli Mapa kolorów materiału, i jest również nazywana **rozpraszaniem** i **BaseColor**.
* **Metalowy**, który określa, czy materiał jest metaliczny i które części są metalowe. 
* **Niesztywność**, która określa, jak sztywno lub gładko powierzchnię.
Ma także wpływ na ostrość lub blurriness odbicia i wyróżnienia na powierzchni.
* **Normalne**, które dodaje szczegóły do powierzchni bez dodawania więcej wielokątów. Przykłady szczegółów są wygrane i mają wcięcia na powierzchni metalowej lub ziarna w drewnie.
* **Zamknięcia otoczenia**, który służy do dodawania cieniowania miękkiego i cieniowania do modelu. Jest to mapa w skali szarości, która wskazuje, które obszary modelu otrzymują pełne oświetlenie (białe) lub pełny cień (czarny).

## <a name="prepare-the-scene"></a>Przygotowywanie sceny
W tabeli 3ds Max proces konfigurowania materiału PBR jest następujący:.

Aby rozpocząć, utworzymy kilka obiektów Box, z których każdy reprezentuje inny typ materiału.

>[!TIP]
>Warto zwrócić uwagę przed rozpoczęciem tworzenia zasobów do zdalnego renderowania, który używa liczników do pomiaru.  
>
>Dobrym pomysłem jest ustawienie jednostek systemu sceny na liczniki. Dobrym pomysłem jest również ustawienie **jednostek** na liczniki w ustawieniach eksportu FBX podczas eksportowania sceny.

Poniższy zrzut ekranu przedstawia kroki konfigurowania jednostek systemu do liczników w tabeli 3ds Max. 

1. W menu głównym przejdź do pozycji **Dostosuj**  >  **jednostki** konfiguracja  >  **jednostki systemowe konfiguracja**. W obszarze **Skala jednostki systemowej** wybierz pozycję **liczniki**: ![ zrzut ekranu przedstawiający sposób ustawiania jednostek systemowych.](media/3dsmax/system-units.jpg)

1. Teraz można rozpocząć tworzenie modeli. W przykładowej scenie utworzymy kilka obiektów Box, z których każdy reprezentuje inny typ materiału. Na przykład metalowe, gumowe i plastikowe. 

   >[!TIP]
   >Podczas tworzenia zasobów warto odpowiednio przystąpić do ich nazwy. Ułatwia to znalezienie ich w przyszłości, jeśli scena zawiera wiele obiektów.

1. Zmień nazwę obiektów, jak pokazano na poniższym zrzucie ekranu: 

   ![Zrzut ekranu pokazujący sposób zmiany nazw obiektów.](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Przypisywanie materiałów

Teraz, gdy mamy kilka obiektów w naszej scenie, w tym przypadku wiele modułów można rozpocząć konfigurację programu PBR:

1. Na głównym pasku narzędzi wybierz ikonę **Edytor materiału** , jak pokazano na poniższym zrzucie ekranu. Możesz również wybrać pozycję **M** na klawiaturze, aby otworzyć Edytor. Edytor materiału ma dwa tryby, które można wybrać na liście **tryby** : tryb **edytora materiału kompaktowego** i tryb **edytora surowców** . Ponieważ ta scena jest stosunkowo prosta, będziemy używać trybu kompaktowego.

1. W edytorze materiału zobaczysz wiele sfer. Te sfery są materiałami. Do każdego obiektu (każdego pola) w scenie zostanie przypisany jeden z tych materiałów. Aby przypisać materiały, najpierw wybierz jeden z obiektów w głównym okienku ekranu. Następnie wybierz pierwszą sferę w edytorze materiału. Po przypisaniu do obiektu wybrany materiał zostanie wyróżniony, jak pokazano na następnym obrazie.

1. Wybierz pozycję **Przypisz materiał do zaznaczenia**, jak pokazano. Materiał jest teraz przypisywany do wybranego obiektu.

   ![Zrzut ekranu pokazujący sposób przypisywania materiałów.](media/3dsmax/assign-material.jpg)

    W edytorze materiałów można wybierać spośród szerokiego wyboru typów materiału, w zależności od potrzeb. Typowo, typ materiału jest domyślnie ustawiony na wartość **standardowa** . Ten materiał jest elitarnym materiałem, który nie jest odpowiedni do instalacji usługi PBR. Dlatego musimy zmienić typ materiału na materiał PBR. Materiał fizyczny to preferowany materiał z maksymalnymi 3DS dla projektów zdalnego renderowania na platformie Azure.

1. W edytorze materiału wybierz kartę **Standardowy** . W **przeglądarce materiały/Mapa** wybierz pozycję **materiał fizyczny**. Ta akcja konwertuje przypisany materiał **Standardowy** do materiału fizycznego PBR.

   ![Zrzut ekranu pokazujący sposób zmiany materiału.](media/3dsmax/physical-material.jpg)

    W edytorze materiału są teraz widoczne właściwości materiału fizycznego, jak pokazano na poniższym zrzucie ekranu. Teraz możesz zacząć przypisywać tekstury do elementu zawartości.

   ![Zrzut ekranu przedstawiający listę tekstur.](media/3dsmax/textures-list.jpg)

Jak widać, istnieje szeroki zakres map i tekstur, które można dodać do materiału. W tym samouczku używamy tylko pięciu gniazd tekstury w materiale.

>[!TIP]
>Dobrym sposobem jest odpowiednie nazywanie materiałów, jak pokazano na poprzednim zrzucie ekranu.

Sposób generowania tekstur może się różnić w zależności od preferencji lub użycia. Na przykład możesz chcieć użyć tekstury sąsiadujących, które można zastosować do dowolnego elementu zawartości. Mogą też wymagać określonych części projektu lub elementu zawartości, aby miały własne niestandardowe zestawy tekstur. Możesz chcieć użyć rodzajowych tekstur tekstury, które możesz uzyskać w trybie online. Możesz również tworzyć je samodzielnie w aplikacjach, takich jak Photoshop, Quixel Suite i Suite.

Przed rozpoczęciem przypisywania tekstur należy wziąć pod uwagę Współrzędne tekstury elementu zawartości (UVW). Najlepszym rozwiązaniem jest zastosowanie dowolnych tekstur do modelu, aby upewnić się, że model jest rozpakowany. (Tekstury nie będą wyświetlane prawidłowo bez poprawnego odpakowywania UV). Jest to szczególnie ważne dla naszych celów, ponieważ chcemy używać mapy otoczenia zamknięcia (AO) w naszym modelu. W przeciwieństwie do modułu cieniującego Stingray w Maya, materiał fizyczny w tabeli 3ds Max nie ma dedykowanego gniazda tekstury AO. Dlatego zastosujemy mapę AO do innego gniazda. Aby zezwolić na użycie go niezależnie od innych tekstur (na przykład tekstury sąsiadujących), przypiszemy do niego własny kanał mapy UVW. 

Zaczniemy od przypisywania modyfikatora UVW Cofnij do modelu, jak pokazano na poniższym zrzucie ekranu. 

- W edytorze właściwości wybranych obiektów wybierz listę modyfikatorów. Na wyświetlonej liście rozwijanej, przewiń w dół i wybierz polecenie **Cofnij Zawijanie UVW**. Ta akcja stosuje modyfikator UVW odpakowania do elementu zawartości.
![Zrzut ekranu pokazujący sposób wybierania opcji odpakowania UVW.](media/3dsmax/unwrap-modifier.jpg)

  Kanał mapy jest ustawiony na 1. Zwykle jest to główne rozpakowanie w kanale mapy 1. W takim przypadku obiekt został rozpakowany bez nakładania się współrzędnych tekstury (UV).
![Zrzut ekranu pokazujący współrzędne nieopakowanej tekstury (UVW).](media/3dsmax/unwrapped-uvw.jpg)

Następnym krokiem jest utworzenie drugiego kanału mapy UV.

1. Zamknij Edytor UV, jeśli jest otwarty. W sekcji **kanał** w menu **Edytuj UVs** Zmień numer kanału na **2**. Kanał mapy 2 jest oczekiwanym kanałem dla map AO. 

1. W oknie dialogowym **Ostrzeżenie o zmianie kanału** można **przenieść** istniejące UVs w kanale 1 do nowego kanału 2 lub **zrezygnować** z istniejącej UVs, która automatycznie utworzy nowe odwzorowanie UV. Wybierz pozycję **Abandon** tylko wtedy, gdy planujesz utworzyć nowe odwzorowanie UV dla mapy Ao, która różni się od UVs w kanale mapy 1. (Na przykład, jeśli chcesz użyć tekstury sąsiadujących w kanale 1). W tym samouczku przeniesiemy UVs z kanału 1 do kanału 2, ponieważ nie trzeba edytować nowego kanału UV.

   >[!NOTE]
   >Nawet jeśli skopiowano (przeniesiono) odwinięcie UV z kanału mapy 1 do kanału mapy 2, można wprowadzić wszelkie niezbędne zmiany w nowym kanale UVs bez wpływu na oryginalny kanał mapy.

   ![Zrzut ekranu pokazujący ostrzeżenie o zmianie kanału.](media/3dsmax/channel-change.jpg)

Po utworzeniu nowego kanału mapy możemy wrócić do materiału fizycznego w edytorze materiału i zacząć dodawać do niego tekstury. Najpierw dodamy mapę AO, ponieważ istnieje inny krok, aby umożliwić jej poprawne działanie. Po dołączeniu mapy AO do naszego materiału należy skonfigurować go do używania kanału mapy 2.

Jak wspomniano wcześniej, nie istnieje dedykowane miejsce dla map AO w maksymalnym materiale fizycznym 3DS. Zamiast tego stosujemy mapę AO do gniazda **Niesztywności rozpraszania** .

1. Na liście **Maps (mapy ogólne** materiału fizycznego) zaznacz pole wyboru **Brak gniazda mapy** obok opcji **rozproszenie** i Załaduj mapę Ao.

1. We właściwościach tekstury AO kanał mapy jest domyślnie ustawiany na **1** . Zmień tę wartość na **2**. Ta akcja wykonuje kroki niezbędne do dodania mapy AO.

   >[!IMPORTANT]
   >Jest to ważny krok, szczególnie jeśli UVs w kanale 2 różnią się od tych w kanale 1, ponieważ AO nie będzie prawidłowo mapowany w przypadku wybrania niewłaściwego kanału.

   ![Zrzut ekranu pokazujący sposób przypisywania mapy AO.](media/3dsmax/assign-ao-map.jpg)

Teraz przypiszemy mapę normalną do materiału PBR. Ta akcja różni się nieco od procesu w Maya. Mapa zwykła nie jest stosowana bezpośrednio do gniazda mapy nierówności. (Nie istnieje normalne gniazdo mapy w maksymalnym materiale fizycznym 3DS). Zamiast tego należy dodać mapę normalną do modyfikatora zwykłej mapy, który jest podłączony do gniazda normalne.

1. W sekcji **mapy specjalne** właściwości materiału fizycznego (w edytorze materiału) zaznacz pozycję **Brak gniazda mapy** obok pozycji **Mapa nierówności**. 

1. W **przeglądarce materiały/Mapa** Znajdź i wybierz pozycję **normalne nierówności**. Ta akcja dodaje do materiału **normalny modyfikator nierówności** .

1. W modyfikatorze **normalnych nierówności** wybierz pozycję **Brak mapy** obok pozycji **normalny**. Znajdź i Załaduj mapę normalną.

1. Upewnij się, że metoda jest ustawiona na **tangens**. (Powinno to być domyślnie.) W razie potrzeby przełącz pozycję **Przerzuć zielony (Y)**.

   ![Zrzut ekranu pokazujący sposób wybierania normalnego nierówności. ](media/3dsmax/normal-bump.jpg)
    ![ Zrzut ekranu pokazujący ładowanie mapy normalnej.](media/3dsmax/load-normal-map.jpg)

Po poprawnym przypisaniu normalnej mapy można przypisać pozostałe tekstury, aby ukończyć konfigurację materiału fizycznego. Ten proces jest prosty. Nie ma żadnych specjalnych ustawień do uwzględnienia. Poniższy zrzut ekranu przedstawia pełen zestaw tekstur przypisanych do materiału: 

![Zrzut ekranu pokazujący pełen zestaw tekstur przypisanych do materiału.](media/3dsmax/all-textures.jpg)

Po utworzeniu i skonfigurowaniu materiałów PBR warto pomyśleć o tworzeniu wystąpień obiektów w scenie. Wystąpienia podobnego obiektu w scenie, takich jak orzechy, pioruny, wkręty i spryskiwacze. Wszystkie obiekty, które są takie same, mogą przynieść znaczne oszczędności w zakresie rozmiaru pliku. Wystąpienia obiektu głównego mogą mieć własne skalowanie, rotacje i przekształcenia, dlatego można je umieścić w miarę potrzeb w scenie. W tabeli 3ds Max proces tworzenia wystąpień jest prosty.

1. W głównym okienku ekranu wybierz obiekt lub obiekty, które chcesz wyeksportować.

1. Przytrzymaj klawisz **SHIFT** i przeciągnij zasoby w górę przy użyciu narzędzia Przekształcanie (przenoszenie). 

1. W oknie dialogowym **Opcje klonowania** Ustaw **obiekt** na **wystąpienie** , a następnie wybierz przycisk **OK**:

   ![Zrzut ekranu przedstawiający okno dialogowe Opcje klonowania.](media/3dsmax/instance-object.jpg)

Ta akcja tworzy wystąpienie obiektu, które można przenieść, obrócić lub skalować niezależnie od jego elementu nadrzędnego i innych wystąpień tego elementu nadrzędnego.

>[!IMPORTANT]
>Wszelkie zmiany wprowadzone w wystąpieniu w trybie obiektu podrzędnego są przesyłane do wszystkich wystąpień obiektu. Dlatego jeśli pracujesz z składnikami wystąpienia obiektu, takimi jak wierzchołki i wieloboków, upewnij się, że chcesz, aby wszystkie zmiany miały wpływ na wszystkie wystąpienia. Należy pamiętać, że każdy obiekt wystąpienia można w dowolnym momencie wprowadzić do unikatowego obiektu. 

>[!TIP]
>Podczas tworzenia wystąpienia w scenie dobrym pomysłem jest utworzenie wystąpień. Późniejsze zastępowanie kopii przy użyciu obiektów wystąpień jest trudne. 

Przed przejściem do procesu eksportu należy wziąć pod uwagę, jak warto umieścić scenę/zasób do udostępnienia. Najlepiej, Jeśli przekażesz zasób do klientów lub członków zespołu, chcesz, aby mogli oni otworzyć i wyświetlić element zawartości, ponieważ powinien on być widoczny przy minimalnej liczbie Fuss. Ważne jest, aby zachować ścieżki tekstury zasobów względem pliku sceny. Jeśli ścieżki tekstury dla danego elementu zawartości wskazują dysk lokalny lub ścieżkę bezwzględną/lokalizację, nie będą ładowane do sceny, jeśli są otwarte na innym komputerze, nawet jeśli plik. Max znajduje się w tym samym folderze co tekstury. Zastosowanie ścieżek tekstury względem wartości 3ds Max rozwiązuje ten problem i jest dość proste.

1. Na głównym pasku narzędzi przejdź do   >    >  **pozycji** odwołanie do pliku. 

1. W oknie śledzenia zasobów zobaczysz wszystkie lub większość tekstur, które zostały zastosowane do materiałów PBR wymienionych w kolumnie **mapy/cieniowanie** .

1. Obok nich w kolumnie **pełna ścieżka** zostanie wyświetlona ścieżka lokalizacji tekstury najprawdopodobniej ścieżka lokalizacji na komputerze lokalnym.

1. Na koniec zobaczysz kolumnę o nazwie **status**. Ta kolumna wskazuje, czy dana tekstura została zlokalizowana i zastosowana do Twojej sceny. Jest ona flagą tekstury z jednym z następujących warunków: **OK**, **znaleziono** lub **Brak pliku**. Pierwsze dwa wskazują, że plik został znaleziony i załadowany. Ostatnim oczywiście oznacza, że moduł śledzący nie mógł zlokalizować pliku.
 
   ![Zrzut ekranu przedstawiający okno śledzenia zasobów.](media/3dsmax/texture-paths.jpg)

Możesz zauważyć, że nie wszystkie tekstury są wyświetlane w oknie śledzenia zasobów przy pierwszym otwarciu. Nic nie dotyczy. Uruchamianie przez proces szukania ścieżki raz lub dwa razy zwykle znajduje wszystkie tekstury sceny. Proces szukania ścieżki jest następujący: 

1. W oknie śledzenia zasobów, przytrzymaj wciśnięty klawisz **SHIFT** i wybierz górną teksturę na liście **Maps/cieniowanie** , a następnie przytrzymaj klawisz **SHIFT**, wybierz ostatnią teksturę z listy. Ta akcja powoduje wybranie wszystkich tekstur na liście. Wybrane tekstury są wyróżnione kolorem niebieskim. (Zobacz poprzedni zrzut ekranu).

1. Kliknij prawym przyciskiem myszy zaznaczenie i wybierz polecenie **Ustaw ścieżkę**.

1. W polu **Określ ścieżkę elementu zawartości** wybierz ścieżkę lokalną do tekstury i Zastąp ją opcją `.\` .  Wybierz przycisk **OK**. 

    Zostanie zaktualizowane okno śledzenia zasobów, jak pokazano na poniższym zrzucie ekranu. Ta aktualizacja może chwilę potrwać, w zależności od tego, ile tekstury jest w Twojej scenie, i od dużej sceny.
![Screensthot, który pokazuje zaktualizowane okno śledzenia zasobów.](media/3dsmax/resolve-textures.jpg)

Zwróć uwagę, że kolumna **pełna ścieżka** jest teraz pusta. Oznacza to, że scena nie szuka już odpowiednich tekstur w określonej (bezwzględnie) lokalizacji. Zawsze będzie je znajdować, o ile plik. Max lub powiązany plik FBX znajduje się w tym samym folderze co tekstury. 

>[!NOTE]
>Może być konieczne powtórzenie tego procesu kilka razy, aby znaleźć i rozwiązać wszystkie tekstury i ścieżki. Nic nie dotyczy. Po prostu Powtórz ten proces do momentu uwzględnienia wszystkich odpowiednich zasobów. W niektórych przypadkach nie zostanie znaleziony niektórych plików. W takim przypadku po prostu zaznacz wszystkie elementy zawartości na liście, a następnie wybierz pozycję **Usuń brakujące ścieżki**. (Zobacz Poprzedni obraz).

## <a name="fbx-export"></a>Eksport FBX

Teraz, gdy wprowadziliśmy ścieżki tekstury względem, możemy przejść do FBX Export. Ten proces jest prosty i można go wykonać na kilka sposobów. 

>[!TIP]
>Jeśli nie chcesz eksportować całej sceny, dobrym pomysłem jest wybranie opcji eksportu tylko potrzebnych zasobów. W przypadku scen intensywnie korzystających z zasobów eksportowanie może zająć dużo czasu.
>
>Jeśli użyto modyfikatorów, takich jak Turbosmooth lub Open SubDiv, warto je zwinąć przed eksportem, ponieważ mogą one spowodować problemy podczas eksportowania. Pamiętaj o zapisaniu sceny przed ich zwinięciem. 

1. W obszarze scena Wybierz zasoby, które chcesz wyeksportować. Na głównym pasku narzędzi przejdź do **pliku**  >  **Eksportuj eksport**  >  **zaznaczone**.

1. W oknie dialogowym **Wybierz plik do eksportu** wpisz lub wybierz nazwę pliku wyjściowego. Na liście **Zapisz jako typ** wybierz pozycję **Autodesk (*. FBX)**. Ta akcja powoduje otwarcie okna eksport FBX.

  >[!IMPORTANT] 
  >Jeśli utworzono wystąpienia w scenie, ważne jest, aby wybrać opcję **Zachowaj wystąpienia** w ustawieniach eksportu FBX. 

  ![Zrzut ekranu pokazujący sposób eksportowania do FBX.](media/3dsmax/fbx-export.jpg)

  Należy pamiętać, że istnieje kilka sposobów eksportowania pliku. Jeśli zamiarem jest udostępnienie FBX wraz z plikami tekstury w folderze/katalogu, ustawienia przedstawione na poniższym zrzucie ekranu powinny być odpowiednie. 

   Jeśli wolisz nie udostępniać dużych folderów/katalogów tekstury wraz z FBX, możesz osadzić tekstury w FBX. Jeśli osadzisz tekstury, cały element zawartości, w tym tekstury, zostanie dodany do pojedynczego FBX. Spowoduje to połączenie eksportu z pojedynczym elementem zawartości, ale plik FBX będzie znacząco większy.

   >[!IMPORTANT]
   >Jeśli plik FBX o rozmiarze większym niż 2,4 GB, minimalna wersja określona w ustawieniach eksportu FBX powinna mieć wartość 2016 lub nowszą. (Zobacz poprzedni zrzut ekranu). Nowsze wersje mają 64 bitową, więc obsługują większe pliki.

1. Jeśli chcesz wyeksportować scenę z dołączonymi teksturami, w oknie * FBX Export wybierz opcję **Osadź multimedia**. 

1. Wybierz pozostałe ustawienia, a następnie wybierz przycisk **OK**:

    ![Zrzut ekranu pokazujący ustawienia eksportu FBX.](media/3dsmax/fbx-settings.jpg)


   Podczas eksportowania do FBX przy użyciu materiału fizycznego prawdopodobnie zobaczysz następujące ostrzeżenie po wybraniu przycisku **OK** w oknie eksportowania FBX: 

   ![Zrzut ekranu pokazujący ostrzeżenie eksport materiału nie powiodło się.](media/3dsmax/export-warnings.jpg)

   To ostrzeżenie informuje o tym, że eksportowane materiały mogą nie być zgodne z innymi pakietami oprogramowania. Ponieważ materiał fizyczny jest zgodny z renderowaniem zdalnym platformy Azure, nie trzeba martwić się o to ostrzeżenie. 

1. Wybierz **przycisk OK** , aby zakończyć proces i zamknąć okno.

## <a name="conclusion"></a>Podsumowanie

Ogólnie rzecz biorąc, ten typ materiału wygląda bardziej realistycznie, ponieważ jest oparty na rzeczywistej fizyki ziemskiej. Tworzy dodatkowy efekt immersyjny, aby sceny pojawiły się w świecie rzeczywistym.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz, jak skonfigurować materiały z zaawansowanymi oświetleniem dla obiektów w scenie. Wiesz również, jak eksportować obiekty do formatu FBX, który jest obsługiwany przez zdalne renderowanie na platformie Azure. Następnym krokiem jest przekonwertowanie pliku FBX i wizualizacja go w ramach renderowania zdalnego na platformie Azure.

>[!div class="nextstepaction"]
>[Szybki start: Konwertowanie modelu do renderowania](../../quickstarts\convert-model.md)
