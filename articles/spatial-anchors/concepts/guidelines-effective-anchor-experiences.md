---
title: Wskazówki dotyczące efektywnych środowisk zakotwiczenia
description: Wskazówki i zagadnienia dotyczące efektywnego tworzenia i lokalizowania kotwic przy użyciu kotwic przestrzennych platformy Azure.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: a6bc500516de7e554c38a335ea57519a39c8f602
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95487507"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Tworzenie efektywnego środowiska zakotwiczonego przy użyciu kotwic przestrzennych platformy Azure

Ten artykuł zawiera wskazówki i zagadnienia ułatwiające efektywne tworzenie i lokalizowanie kotwic przy użyciu kotwic przestrzennych platformy Azure.

## <a name="anchor-improvement-over-time"></a>Ulepszanie kotwic w czasie

Dzięki zakotwiczeniem przestrzennym platformy Azure przy każdym lokalizowaniu kotwic próbujemy poprawić jakość przyszłych operacji lokalizowania. W tym celu można użyć zebranych danych środowiska w celu rozszerzenia wizualizacji informacji na temat zakotwiczenia. Ten proces jest uruchamiany w ramach okapu i jest optymalizacją w trybie offline uruchomioną przez usługę kotwice przestrzenne platformy Azure w celu optymalizacji pod kątem środowiska. Dodatkowe dane zbierane podczas każdej operacji kompilują silniejsze zrozumienie środowiska. Poprawia to jakość i umożliwia lepsze lokalizowanie kotwic poprzez zmianę środowiska, przekazanie czasu i dla użytkowników przeglądających kotwicę z różnych kątów i perspektyw.

## <a name="good-anchors"></a>Dobre kotwice

Podczas gdy kotwice przestrzenne platformy Azure podejmują próbę zwiększenia jakości kotwic z upływem czasu, ważne jest również, aby zainwestować użytkowników w środowisko użytkownika (UX) w celu utworzenia dobrych kotwic. Dzięki zainwestowaniu w tworzenie dobrych kotwic należy pomóc użytkownikom końcowym w niezawodnym znajdowaniu kotwic:

- Na różnych urządzeniach.
- W różnym czasie.
- W różnych warunkach oświetlenia.
- Od pożądanych perspektyw w miejscu.

## <a name="static-and-dynamic-locations"></a>Lokalizacje statyczne i dynamiczne

Częścią projektowania środowiska zakotwiczenia jest wybranie lokalizacji. Czy lokalizacje będą statyczne i zdefiniowane przez administratora miejsca? Czy będą one dynamiczne i zdefiniowane przez użytkownika?

Menedżer sklepów detalicznych może chcieć, aby użytkownicy mogli odwiedzać się w środowisku ze sklepu w sklepie. Jednak deweloperzy z planszy z tablicą o rzeczywistości mieszanej prawdopodobnie chcą umożliwić użytkownikom wybranie miejsca do odtworzenia.

W przypadku lokalizacji statycznych można nauczyć administratorów, aby poświęcać czas na nadzorowanie miejsca z dobrymi zakotwiczeniami.

W przypadku lokalizacji dynamicznych należy zastanowić się, jak uczysz się użytkowników w Twoim środowisku użytkownika w celu utworzenia dobrych kotwic.

## <a name="stable-visual-features"></a>Stabilne funkcje wizualizacji

Systemy śledzenia wizualne używane w przypadku urządzeń o rzeczywistości mieszanej i o rzeczywistości rozszerzonej korzystają z wizualnych funkcji środowiska. Aby uzyskać najbardziej niezawodne środowisko:

- *Twórz* kotwice w lokalizacjach, które mają stabilne funkcje wizualne (czyli funkcje, które często nie zmieniają się).

- *Nie* Twórz kotwic na dużych pustych powierzchniach, które nie mają cech odróżniających.

- *Nie* Twórz kotwic na wysoce odbijających materiałach.

- *Nie* Twórz kotwic na powierzchniach, w których wzorzec powtarza się, na przykład dywan lub tapetę.

![Przykłady dobrego środowiska dla kotwic i nieprawidłowego środowiska dla kotwic](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Różne perspektywy wyświetlania

Podczas tworzenia kotwicy zastanów się nad osobami, które później spróbują zlokalizować zakotwiczenie.

Rozważmy na przykład zakotwiczenie w środku pokoju, które ma dwa drzwi. Najkorzystniej jest zezwolić użytkownikom na wprowadzanie pokoju z dowolnego drzwi. Podczas tworzenia kotwicy należy skanować jej położenie z obu Doorways. Należy zmienić perspektywy, aby przechwytywać dane środowiska wokół kotwicy, dzięki czemu użytkownicy mogą zlokalizować zakotwiczenie z dowolnego drzwi.

Ogólnie rzecz biorąc, podczas tworzenia kotwicy należy skanować ją z perspektywy osób, które spróbują go zlokalizować. Dlatego Jeśli umieszczasz zawartość wirtualną na odniesieniu do rzeźby świata, warto zastanowić się nad rzeźbą, podczas gdy tworzysz kotwicę. Jeśli zakotwiczenie znajduje się w rogu pokoju, należy tylko jeden kierunek podejścia do niego. Podczas tworzenia tej kotwicy można skanować ją tylko z tej perspektywy.

## <a name="multiple-anchors"></a>Wiele kotwic

Oświetlenie może być różnicą w funkcjach wizualizacji wykrywanych przez aplikację. Kotwice utworzone w silnym naturalnym oświetleniu mogą być trudne do zlokalizowania w sztucznym oświetleniu i na odwrót.

Jeśli ten problem występuje, może pomóc utworzyć dwie kotwice. W tym samym miejscu Utwórz jedną kotwicę w czasie letnim i inną w sztucznym oświetleniu. Aplikacja może następnie wykonywać zapytania dotyczące obu kotwic. Gdy zakotwiczenie jest zlokalizowane, aplikacja będzie miała ułożenia dla zakotwiczenia.

Podobnie w środowiskach, w których zmiany funkcji wizualnych zmieniają się, ponieważ większość obiektów jest przenoszona, wiele kotwic może pomóc. Gdy zakotwiczenie jest zbyt trudne do znalezienia ze względu na znaczące zmiany w środowisku, można zamienić zakotwiczenie na nową. Można to zrobić na przykład w sklepie detalicznym, w którym układ jest odświeżany co kilka miesięcy.

## <a name="targets-and-rooms"></a>Cele i pokoje

W wielu przypadkach zakotwiczenie jest punktem wejścia do środowiska Twojej aplikacji. W tym kroku warto szybko i niezawodnie za, aby użytkownicy mogli wprowadzać Twoje doświadczenia. Czas poświęcany na sposób, w jaki użytkownicy znajdą Twoje kotwice, to ważny krok projektowania. Warto zastanowić się nad znalezieniem kotwic w dwóch ogólnych scenariuszach: *celach* i *pokojów*.

### <a name="targets"></a>Targets (Obiekty docelowe)

W scenariuszu docelowym lokalizacja kotwicy jest dobrze znana. Na przykład w fikcyjnej aplikacji do malowania w rzeczywistości mieszanej jeden użytkownik umieszcza wirtualną kanwę na tablicy. Powoduje, że inni użytkownicy w pokoju wskazują swoje urządzenia w tym samym miejscu na ścianie, aby zlokalizować zakotwiczenie i rozpocząć pracę.

Innym przykładem scenariusza docelowego może być znak w sklepie kawowym, w którym znajduje się komunikat "skanowanie w poszukiwaniu transakcji". W sklepie kawowym umieszczono tutaj kotwicę. Gdy użytkownicy przeskanują znak, lokalizują kotwicę i wprowadzają środowisko rozszerzone-rzeczywistość, aby znaleźć oferty na kawę.

W scenariuszu docelowym Zdjęcia mogą pomóc. W przypadku pokazywania użytkownikom Zdjęcia zamierzonego miejsca docelowego na urządzeniu mogą szybko określić, co ma zostać przeskanowane w świecie rzeczywistym. Na przykład możesz pomóc użytkownikom w rozbyciu w ogólnym obszarze zamierzonego celu przy użyciu systemu GPS. Po nadejściu użytkownika aplikacja wyświetli zdjęcie obiektu docelowego. Użytkownik przeszuka miejsce, odnajdzie element docelowy i skanuje dla zakotwiczenia.

![Ilustracja zakotwiczenia przedstawiająca zdjęcie elementu docelowego na urządzeniu przenośnym użytkownika](./media/start-here-edit.png)

### <a name="rooms"></a>Natrysk

W scenariuszu pokojowym użytkownicy wprowadzają miejsce w dowolnym miejscu. Użytkownicy skanują miejsce na swoje urządzenie i szybko lokalizują kotwicę.

To środowisko zwykle wymaga utworzenia dobrze nadzorowanych kotwic, zgodnie z opisem w różnych perspektywach wyświetlania. Jeśli przeskanowano pomieszczenie z wielu perspektyw podczas tworzenia kotwicy, użytkownicy mogą skanować niemal w dowolnym miejscu, gdy spróbują go zlokalizować.

![Ilustracja przedstawiająca sposób, w jaki użytkownik może skanować pomieszczenie, aby znaleźć zakotwiczenie](./media/scan-room.png)

W związku z tym poświęcasz więcej czasu na skanowanie miejsca podczas tworzenia kotwicy, tak aby później użytkownicy mogli skanować i szybko lokalizować kotwicę. Podczas tworzenia środowiska należy wziąć pod uwagę ten istotny handel.

Przykład aplikacji do malowania w rzeczywistości mieszanej, która została omówiona wcześniej, nie działa dobrze w przypadku scenariusza pokojowego. W tym miejscu użytkownik, który umieści zakotwiczenie, chce, aby mogli szybko dołączyć do środowiska. Użytkownicy nie chcą czekać na uruchomienie środowiska, dopóki pomieszczenie nie zostanie prawidłowo zeskanowane. Ponieważ wszyscy użytkownicy wiedzą dokładnie, gdzie należy wskazać swoje urządzenie, aby zlokalizować kotwice, ten przykład działa lepiej jako scenariusz docelowy.

## <a name="anchor-location"></a>Lokalizacja zakotwiczenia

Systemy śledzenia wizualizacji są oparte na funkcjach wizualnych w środowisku. Im więcej wizualizacji obejmuje funkcja skanowania, tym większe prawdopodobieństwo znalezienia zakotwiczenia.

Postępuj zgodnie z ogólnymi wskazówkami w tej sekcji, aby utworzyć środowisko użytkownika, które zachęca do przydatnego skanowania środowiska.

Po pierwsze, jeśli użytkownik nie odnajdzie zakotwiczenia w ciągu kilku sekund, aplikacja powinna zachęcić użytkowników do przenoszenia urządzenia, aby przechwycić więcej perspektyw. Aplikacja może również zachęcić użytkowników do przenoszenia się do środowiska w celu przeskanowania pod kątem zakotwiczenia z większej perspektywy. Im więcej cechuje się, że urządzenie widzi, lepsza, ponieważ zwiększy prawdopodobieństwo, że zakotwiczenie znajduje się, a także będzie zbierać więcej danych środowiskowych, które zostaną użyte w celu poprawienia jakości zakotwiczenia.

W przypadku scenariuszy docelowych poproszenie użytkownika o przechodzenie wokół elementu docelowego w celu wyświetlenia go z różnych perspektyw. Innymi słowy poproszenie użytkownika o przechwycenie elementu docelowego z nowych perspektyw do momentu, gdy zakotwiczenie nie zostanie umieszczone.

W przypadku scenariuszy pokojowych poproszenie użytkownika o spowolnienie skanowania pokoju. Na przykład poproszenie użytkownika o przechwycenie 180 stopni lub nawet 360 stopni pokoju. Lub poproszenie użytkownika o wyświetlenie pokoju z nowej perspektywy.

Najbardziej znaczącą metodą jest skanowanie w całym pokoju. Skanowanie w pokoju przechwytuje więcej funkcji wizualizacji środowiska niż skanowanie w pobliżu ściany, na przykład. Skanowanie w pobliżu ściany nie przechwytuje jak wiele przydatnych funkcji wizualnych środowiska.

Podczas wyszukiwania zakotwiczenia nie jest pomocne wielokrotne przeniesienie urządzenia z boku do tyłu. Po prostu przechwytuje te same punkty z tej samej perspektywy.

## <a name="experience-tests"></a>Testy eksploatacyjne

W tym artykule omówiono ogólne wytyczne. Dzięki zakotwiczeniem przestrzennym tworzysz aplikacje, które współpracują z rzeczywistym światem. Z tego względu należy poświęcać czas na testowanie scenariuszy zakotwiczenia aplikacji w rzeczywistych środowiskach. Jest to szczególnie prawdziwe w przypadku środowisk, które reprezentują miejsce, w którym użytkownicy mogą korzystać z aplikacji.
