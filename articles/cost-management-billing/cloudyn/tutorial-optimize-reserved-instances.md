---
title: Samouczek — optymalizowanie kosztów wystąpień zarezerwowanych za pomocą rozwiązania Cloudyn na platformie Azure
description: W tym samouczku dowiesz się, jak optymalizować koszty wystąpień zarezerwowanych na platformach Azure i Amazon Web Services (AWS).
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.custom: seodec18
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 8488b1f511a3b269f36f3dfe7a351765a4e312aa
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687879"
---
<!-- Intent: As a cloud-consuming administrator, I need to ensure that my reserved instances are optimized for cost and usage
-->

# <a name="tutorial-optimize-reserved-instances"></a>Samouczek: optymalizowanie wystąpień zarezerwowanych

W tym samouczku dowiesz się, jak rozwiązanie Cloudyn może pomóc Ci w optymalizowaniu kosztów i wykorzystania wystąpień zarezerwowanych na platformach Azure i Amazon Web Services (AWS). Wystąpienie zarezerwowane u obu dostawców usług w chmurze oznacza długoterminowe zobowiązanie, w ramach którego z góry zobowiązujesz się do używania maszyny wirtualnej w przyszłości. Potencjalnie oferuje ono znaczne oszczędności w stosunku do standardowego modelu cen maszyny wirtualnej, w którym płatność odbywa się zgodnie z rzeczywistym użyciem. Potencjalne oszczędności stają się rzeczywiste dopiero wtedy, gdy w pełni wykorzystasz pojemność wystąpień zarezerwowanych.

W tym samouczku wyjaśniono, w jaki sposób wystąpienia zarezerwowane platform Azure i AWS są obsługiwane przez rozwiązanie Cloudyn. Opisano w nim również, jak można optymalizować koszty wystąpień zarezerwowanych. Odbywa się to przede wszystkim przez zapewnienie pełnego wykorzystania rezerwacji. W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Poznanie kosztów wystąpień zarezerwowanych na platformie Azure
> * Poznanie zalet wystąpień zarezerwowanych
> * Optymalizowanie kosztów wystąpień zarezerwowanych na platformie Azure
> * Wyświetlanie kosztów wystąpień zarezerwowanych
> * Ocena opłacalności wystąpień zarezerwowanych na platformie Azure
> * Optymalizowanie kosztów wystąpień zarezerwowanych na platformie AWS
> * Kupowanie zalecanych wystąpień zarezerwowanych
> * Modyfikowanie nieużywanych rezerwacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć konto platformy Azure.
- Musisz mieć rejestrację próbną lub płatną subskrypcję rozwiązania Cloudyn.
- Musisz mieć wystąpienia zarezerwowane kupione na platformie Azure lub AWS.

## <a name="understand-azure-ri-costs"></a>Poznanie kosztów wystąpień zarezerwowanych na platformie Azure

Gdy kupujesz wystąpienia w ramach usługi Azure Reserved VM Instances, płacisz z góry za użycie w przyszłości. Płatność z góry obejmuje koszt użycia maszyn wirtualnych w przyszłości, przy czym muszą być spełnione następujące warunki:

- Określony typ
- Określony region
- Okres jednego roku lub trzech lat
- Maksymalna liczba maszyn wirtualnych równa zakupionej

Wystąpienia zakupione w usłudze Azure Reserved VM Instances można wyświetlić w witrynie Azure Portal w bloku [Rezerwacje](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

Termin _wystąpienie usługi Azure Reserved VM Instances_ ma zastosowanie tylko do modelu cen. W żaden sposób nie zmienia on działających maszyn wirtualnych. Termin jest specyficzny dla platformy Azure, a takie wystąpienie jest częściej nazywane _wystąpieniem zarezerwowanym_ lub _rezerwacją_. Zakupione wystąpienia zarezerwowane nie są stosowane dla konkretnych maszyn wirtualnych — są stosowane dla dowolnej zgodnej maszyny wirtualnej. Na przykład rezerwacja typu maszyny wirtualnej działającej w regionie, który wybrano dla zakupionej rezerwacji.

Zakupione wystąpienia zarezerwowane dotyczą tylko podstawowego sprzętu. Nie obejmują one licencji na oprogramowanie maszyny wirtualnej. Na przykład możesz zarezerwować wystąpienie i może istnieć zgodna maszyna wirtualna z systemem Windows. Wystąpienie zarezerwowane obejmuje tylko podstawowy koszt maszyny wirtualnej. W tym przykładzie zapłacisz pełną cenę dowolnej wymaganej licencji systemu Windows. Aby uzyskać rabat na system operacyjny lub inne oprogramowanie działające na maszynach wirtualnych, rozważ zastosowanie [korzyści użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Korzyści użycia hybrydowego oferują typ rabatu na licencje oprogramowania podobny do tego, jaki wystąpienia zarezerwowane zapewniają w przypadku podstawowej maszyny wirtualnej.

Użycie wystąpienia zarezerwowanego nie ma bezpośredniego wpływu na koszt. Innymi słowy, uruchomienie maszyny wirtualnej z użyciem procesora CPU na poziomie 100% ma ten sam efekt, co z użyciem procesora na poziomie 0%: płacisz z góry za alokację maszyny wirtualnej, a nie jej rzeczywiste użycie.

Na poniższej ilustracji przedstawiono, jak standardowe użycie maszyny wirtualnej na żądanie ma się do kosztów względem wystąpień zarezerwowanych:

![Koszty wystąpień na żądanie względem kosztów wystąpień zarezerwowanych](./media/tutorial-optimize-reserved-instances/azure01.png)



Czerwone słupki przedstawiają skumulowany koszt zakupu wystąpień zarezerwowanych. Uiszczana jest tylko jednorazowa opłata. Użycie maszyny wirtualnej jest bezpłatne. Niebieskie paski pokazują skumulowany koszt tej samej maszyny wirtualnej działającej w modelu cen, w którym płatność odbywa się zgodnie z rzeczywistym użyciem (na żądanie). Gdzieś między siódmym i ósmym miesiącem użycia maszyny wirtualnej znajduje się *próg rentowności*. W tym przykładzie zaczynasz oszczędzać pieniądze, począwszy od ósmego miesiąca.

## <a name="benefits-of-ris"></a>Zalety wystąpień zarezerwowanych

Każdy zakup wystąpienia zarezerwowanego ma zastosowanie do maszyny wirtualnej o określonym rozmiarze i w określonej lokalizacji. Na przykład maszyny D2s\_v3 uruchomionej w lokalizacji Zachodnie stany USA, jak pokazano na poniższej ilustracji:

![Szczegóły wystąpienia zarezerwowanego na platformie Azure](./media/tutorial-optimize-reserved-instances/azure02.png)

Zakup wystąpienia zarezerwowanego staje się korzystny, gdy maszyna wirtualna działa przez liczbę godzin wystarczającą do osiągnięcia progu rentowności rezerwacji. Maszyna wirtualna musi być zgodna z rozmiarem i lokalizacją wystąpienia zarezerwowanego. Na przykład na powyższym wykresie próg rentowności jest osiągany po około siedmiu i pół miesiąca. Czyli zakup jest korzystny, jeśli maszyna wirtualna zgodna z rezerwacją będzie działać przez co najmniej 7,5 miesiąca \* 30 dni \* 24 godziny = 5400 godzin. Jeśli zgodna maszyna wirtualna działa przez mniej niż 5400 godzin, rezerwacja jest droższa niż płatność zgodnie z rzeczywistym użyciem.

Próg rentowności może być inny dla każdego rozmiaru i każdej lokalizacji maszyny wirtualnej. Zależy on również od wynegocjowanej ceny maszyny wirtualnej w modelu płatności zgodnie z rzeczywistym użyciem. Przed dokonaniem zakupu należy sprawdzić próg rentowności w danym przypadku.

Inną kwestią, którą należy wziąć pod uwagę podczas zakupu rezerwacji, jest zakres wystąpienia zarezerwowanego. Zakres określa, czy rezerwacja jest współdzielona, czy też ma zastosowanie do konkretnej subskrypcji. Współdzielone wystąpienia zarezerwowane są stosowane losowo w ramach wszystkich Twoich subskrypcji dla zgodnych maszyn wirtualnych, które zostały znalezione w pierwszej kolejności.

Współdzielony zakres zakupu jest najbardziej elastyczny i jest zalecany zawsze, gdy jego użycie jest możliwe. Szanse na wykorzystanie wszystkich wystąpień zarezerwowanych są znacznie wyższe w przypadku współdzielonego zakresu. Jednak gdy właściciel subskrypcji płaci za wystąpienie zarezerwowane, może nie mieć wyboru i być zmuszony do zakupu z zakresem pojedynczej subskrypcji.

## <a name="optimize-azure-ri-costs"></a>Optymalizowanie kosztów wystąpień zarezerwowanych na platformie Azure

Rozwiązanie Cloudyn obsługuje wystąpienia zarezerwowane i korzyści użycia hybrydowego w następujący sposób:

- Przedstawianie kosztów skojarzonych z modelami cen
- Śledzenie użycia wystąpień zarezerwowanych
- Ocena wpływu wystąpień zarezerwowanych
- Przydzielanie kosztów wystąpień zarezerwowanych zgodnie z zasadami

Pierwszą czynnością, którą należy wykonać przed zakupem wystąpienia zarezerwowanego, jest ocena wpływu zakupu wystąpienia zarezerwowanego:

- Jaki będzie koszt?
- Jakie będą oszczędności?
- Jaki jest próg rentowności?

Raport Wpływ zakupu wystąpień zarezerwowanych może pomóc w odpowiedzi na te pytania.

## <a name="assess-azure-ri-cost-effectiveness"></a>Ocena opłacalności wystąpień zarezerwowanych na platformie Azure

W portalu Cloudyn wybierz pozycję **Optymalizator** > **Porównanie wystąpień zarezerwowanych**, a następnie wybierz pozycję **Wpływ zakupu wystąpień zarezerwowanych**.

W raporcie Wpływ zakupu wystąpień zarezerwowanych wybierz rozmiar maszyny wirtualnej (Typ wystąpienia), lokalizację (Region), okres rezerwacji, ilość i oczekiwany czas działania. Następnie będzie można ocenić, czy zakup pozwoli zaoszczędzić pieniądze.

Na przykład w przypadku zakupu rezerwacji maszyny wirtualnej typu DS1\_v2 w regionie Wschodnie stany USA, która będzie działać bez przerwy przez cały rok, można zaoszczędzić 369,48 USD rocznie. Próg rentowności zostanie osiągnięty w piątym miesiącu. Zobacz poniższy obraz:

![Próg rentowności wystąpienia zarezerwowanego na platformie Azure](./media/tutorial-optimize-reserved-instances/azure03.png)

Jeśli jednak maszyna wirtualna będzie działać tylko przez 50% czasu, próg rentowności zostanie osiągnięty w 10 miesiącu, a oszczędności wyniosą jedynie 49,74 USD rocznie. W tym przykładzie zakup rezerwacji dla tego typu wystąpienia może nie być korzystny. Zobacz poniższy obraz:

![Przykład rentowności maszyn wirtualnych platformy Azure](./media/tutorial-optimize-reserved-instances/azure04.png)

## <a name="view-ri-costs"></a>Wyświetlanie kosztów wystąpień zarezerwowanych

Po zakupie rezerwacji dokonujesz jednorazowej płatności. Płatność można wyświetlić w rozwiązaniu Cloudyn na dwa sposoby:

- Rzeczywisty koszt
- Amortyzowany koszt

### <a name="actual-reserved-instance-cost"></a>Rzeczywisty koszt wystąpienia zarezerwowanego

Raporty Analiza rzeczywistego kosztu i Analiza względem czasu pokazują pełne kwoty zapłacone za rezerwację, począwszy od miesiąca zakupu. Ułatwiają one sprawdzanie rzeczywistych wydatków w danym okresie.

Wybierz pozycję **Koszty** > **Analiza kosztów** w portalu Cloudyn, a następnie wybierz pozycję **Analiza rzeczywistego kosztu** lub **Rzeczywisty koszt względem czasu**. Teraz ustaw filtry. Na przykład odfiltruj tylko usługę Azure/VM i pogrupuj wyniki według wartości Typ zasobu i Model cen. Zobacz poniższy obraz:

![Przykład rzeczywistego kosztu wystąpień zarezerwowanych](./media/tutorial-optimize-reserved-instances/azure05.png)

Filtrować można według usługi, w tym przykładzie **Azure/VM**, i pogrupować wyniki według wartości **Model cen** i **Typ zasobu**, jak pokazano na poniższej ilustracji:

![Przykład grup i filtrów raportu rzeczywistego kosztu pogrupowanych według modelu ceny i typu zasobu](./media/tutorial-optimize-reserved-instances/azure06.png)

Ponadto można przeanalizować typy dokonanych płatności, np. jednorazowych opłat, opłat za użycie i opłat za licencje.

### <a name="amortized-reserved-instance-cost"></a>Amortyzowany koszt wystąpienia zarezerwowanego

Podczas zakupu wystąpienia zarezerwowanego wnosisz opłatę z góry, która jest widoczna w miesiącu zakupu. Nie jest ona widoczna na kolejnych fakturach. Dlatego sprawdzanie miesięcznego użycia może wprowadzać w błąd. Prawdziwy koszt w miesiącu składa się z miesięcznego użycia oraz proporcjonalnej (amortyzowanej) części wszelkich poniesionych opłat jednorazowych. Raport amortyzowanego kosztu może pomóc uzyskać prawdziwy obraz.

Amortyzowany koszt wystąpienia zarezerwowanego jest obliczany przez amortyzowanie jednorazowej opłaty za rezerwację w okresie rezerwacji. W raportach rzeczywistego kosztu jednorazowe opłaty są widoczne w miesiącu zakupu rezerwacji. Codzienne i comiesięczne wydatki nie są widoczne w ramach rzeczywistego kosztu wdrożenia. Raporty amortyzowanego kosztu przedstawiają rzeczywisty koszt wdrożenia względem czasu.  Raport amortyzowanego kosztu jest jedynym sposobem na wyświetlenie prawdziwego trendu kosztów. Jest to również jedyny sposób na prognozowanie przyszłych wydatków.

W raporcie rzeczywistego kosztu 16 listopada był widoczny nagły wzrost o kwotę 747 USD w związku z zakupem wystąpienia zarezerwowanego. W raporcie amortyzowanego kosztu (zobacz poniższy obraz) 16 listopada pojawia się częściowy koszt dzienny. Począwszy od 17 listopada, widoczny jest zamortyzowany koszt wystąpienia zarezerwowanego w wysokości 747 USD / 365 = 2,05 USD. Przy okazji można zauważyć, że zakupiona rezerwacja jest nieużywana, więc można dokonać optymalizacji, zmieniając rozmiar maszyny wirtualnej na inny.

Aby wyświetlić ten raport, wybierz pozycję **Koszty** > **Analiza kosztów**, a następnie wybierz pozycję **Analiza amortyzowanego kosztu** lub **Amortyzowany koszt względem czasu**.

![Przykładowy raport pokazujący zamortyzowany koszt wystąpienia zarezerwowanego](./media/tutorial-optimize-reserved-instances/azure07.png)

## <a name="optimize-aws-ri-costs"></a>Optymalizowanie kosztów wystąpień zarezerwowanych na platformie AWS

Wystąpienia zarezerwowane to otwarte zobowiązanie. Są one przydatne w przypadku stałego użycia maszyn wirtualnych, ponieważ wystąpienia zarezerwowane są mniej kosztowne niż wystąpienia na żądanie. Muszą jednak być wystarczająco dużo używane. Zobowiązanie obejmuje używanie zasobów, zwykle maszyn wirtualnych, przez ustalony czas — rok lub trzy lata. Gdy zobowiążesz się do zakupu, płacisz z góry za zasoby w postaci rezerwacji. Zobowiązanie w ramach rezerwacji może jednak nie zostać w pełni wykorzystane.

Na przykład po dokonaniu oceny środowiska ustalono, że przez ostatni rok bez przerwy działało 20 wystąpień Standardowa D2. Można kupić rezerwację dla tych wystąpień i potencjalnie istotnie zredukować koszty. W innym przykładzie zobowiązano się do używania dziesięciu wystąpień MA4 przez rok. Jednak do bieżącego dnia używano tylko pięciu. Oba przykłady ilustrują nieefektywne użycie wystąpień zarezerwowanych. Raporty optymalizacji Cloudyn umożliwiają optymalizowanie kosztów wystąpień zarezerwowanych na dwa sposoby:

- Przeglądanie zaleceń zakupu wskazujących, co można kupić, na podstawie przeszłego użycia
- Modyfikowanie nieużywanych rezerwacji

Raporty _Zalecenia zakupu wystąpień zarezerwowanych usługi EC2_ i _Obecnie nieużywane rezerwacje usługi EC2_ umożliwiają poprawienie użycia i zmniejszenie kosztów wystąpień zarezerwowanych.

## <a name="buy-recommended-ris"></a>Kupowanie zalecanych wystąpień zarezerwowanych

Usługa Cloudyn porównuje użycie wystąpień na żądanie z potencjalnymi wystąpieniami zarezerwowanymi. Gdy znajdzie możliwe oszczędności, odpowiednie zalecenia są wyświetlane w raporcie zaleceń zakupu usługi EC2.

W menu raportów w górnej części portalu kliknij pozycję **Optymalizator** > **Optymalizacja cen** > **Zalecenia zakupu wystąpień zarezerwowanych usługi EC2**.

Poniższy obraz przedstawia zalecenia zakupu z raportu.

![Przykład przedstawiający zalecenia zakupu w raporcie Zalecenia zakupu usługi EC2](./media/tutorial-optimize-reserved-instances/aws01.png)

W tym przykładzie konto Cloudyn\_A ma 32 zalecenia zakupu wystąpień zarezerwowanych. W przypadku postąpienia zgodnie ze wszystkimi zaleceniami zakupu potencjalnie można zaoszczędzić 137 770 USD rocznie. Pamiętaj, że zalecenia zakupu udostępniane w usłudze Cloudyn zakładają, że użycie działających obciążeń pozostanie niezmienione.

Aby wyświetlić szczegóły wyjaśniające, dlaczego dany zakup jest zalecany, kliknij znak plus ( **+** ) w obszarze **Uzasadnienia**. Oto przykład dla pierwszego zalecenia na liście.

![Przykład przedstawiający szczegóły uzasadnienia zakupu](./media/tutorial-optimize-reserved-instances/aws02.png)

Powyższy przykład pokazuje, że uruchamianie obciążenia na żądanie kosztowałoby 90 456 USD rocznie. Jednak w przypadku zakupu rezerwacji z wyprzedzeniem to samo obciążenie kosztowałoby 56 592 USD, co pozwoliłoby zaoszczędzić 33 864 USD rocznie.

Kliknij znak plus obok pozycji **Wpływ zakupu wystąpień zarezerwowanych w usłudze EC2**, aby wyświetlić próg rentowności w okresie roku i zobaczyć, kiedy mniej więcej inwestycja w zakup się zwróci. Po około ośmiu miesiącach od dokonania zakupu skumulowany koszt wystąpień na żądanie zaczyna przekraczać skumulowany koszt wystąpień zarezerwowanych w poniższym przykładzie:

![Przykład przedstawiający szczegóły wpływu zakupu](./media/tutorial-optimize-reserved-instances/aws03.png)

W tym momencie zaczynasz oszczędzać pieniądze.

Aby sprawdzić dokładność proponowanego zalecenia zakupu, możesz przejrzeć obszar **Wystąpienia względem czasu**. W tym przykładzie widać, że w ciągu ostatnich 30 dni średnio dla obciążenia było używanych sześć wystąpień.

![Przykład przedstawiający historyczne użycie wystąpień wraz z upływem czasu](./media/tutorial-optimize-reserved-instances/aws04.png)

## <a name="modify-unused-reservations"></a>Modyfikowanie nieużywanych rezerwacji

Nieużywane rezerwacje są powszechne w wielu środowiskach obliczeniowych użytkowników zasobów w chmurze. Zapewnienie pełnego użycia nieużywanych rezerwacji może zaoszczędzić pieniądze, gdy modyfikujesz rezerwacje, aby spełniały bieżące potrzeby. Na przykład możesz mieć subskrypcję zawierającą wystąpienia Standardowa D3 z systemem Linux. Jeśli rezerwacja nie będzie w pełni wykorzystywana, możesz zmienić typ wystąpienia. Alternatywnie możesz przenieść nieużywane zasoby do innej rezerwacji lub innego konta.

Platforma AWS sprzedaje wystąpienia zarezerwowane dla konkretnych stref dostępności i regionów. Wystąpień zarezerwowanych kupionych dla konkretnej strefy dostępności nie można przenieść do innej strefy. Jednak regionalne wystąpienia zarezerwowane można łatwo przenosić między strefami przy użyciu raportu **Obecnie nieużywane rezerwacje usługi EC2**. Alternatywnie można je zmodyfikować tak, aby miały zakres regionalny. Wtedy zgodne wystąpienia będą stosowane we wszystkich strefach dostępności.

W menu raportów w górnej części portalu kliknij pozycję **Optymalizator** > **Niska efektywność** > **Obecnie nieużywane rezerwacje usługi EC2**.

Na poniższych ilustracjach przedstawiono raport z nieużywanymi wystąpieniami zarezerwowanymi.

![Przykład przedstawiający podsumowanie informacji na temat nieużywanych rezerwacji](./media/tutorial-optimize-reserved-instances/unused-ri01.png)

Kliknij znak plus w obszarze **Szczegóły**, aby wyświetlić szczegóły konkretnej rezerwacji.

![Przykład przedstawiający szczegóły nieużywanych rezerwacji](./media/tutorial-optimize-reserved-instances/unused-ri02.png)

W powyższym przykładzie istnieje łącznie 77 nieużywanych rezerwacji w różnych strefach dostępności. Pierwsza rezerwacja ma 51 nieużywanych wystąpień. Niżej na liście widać, że istnieją potencjalne modyfikacje wystąpień zarezerwowanych, które można wprowadzić przy użyciu typu wystąpienia **m3.2xlarge** w strefie dostępności **us-east-1c**.

Kliknij przycisk **Modyfikuj** dla pierwszej rezerwacji na liście, aby otworzyć stronę **Modyfikowanie wystąpień zarezerwowanych** zawierającą dane o rezerwacji.

![Przykład przedstawiający rezerwacje, które można zmodyfikować](./media/tutorial-optimize-reserved-instances/unused-ri03.png)

Wystąpienia zarezerwowane, które można modyfikować, są wyświetlane na liście. Na poniższej przykładowej ilustracji istnieje 51 nieużywanych rezerwacji, które można zmodyfikować, ale obie rezerwacje powinny łącznie zapewnić 54 wystąpienia. Jeśli zmodyfikujesz nieużywane rezerwacje tak, aby używać ich wszystkich, cztery wystąpienia będą nadal uruchamiane na żądanie. Na potrzeby tego przykładu podziel nieużywane rezerwacje w taki sposób, aby pierwsza rezerwacja używała 30 wystąpień, a druga 21.

Kliknij znak plus dla pozycji pierwszej rezerwacji i ustaw opcję **Ilość rezerwacji** na wartość **30**. W drugiej pozycji ustaw ilość rezerwacji na **21**, a następnie kliknij przycisk **Zastosuj**.

![Przykład przedstawiający zmiany ilości rezerwacji](./media/tutorial-optimize-reserved-instances/unused-ri04.png)

Wszystkie nieużywane wystąpienia rezerwacji są teraz w pełni wykorzystywane i 51 wystąpień nie jest już uruchamianych na żądanie. W tym przykładzie zapewniasz swojej organizacji oszczędność pieniędzy, znacznie ograniczając użycie na żądanie i wykorzystując rezerwacje, za które już zapłacono.

## <a name="next-steps"></a>Następne kroki

W tym samouczku pomyślnie wykonano następujące czynności:

> [!div class="checklist"]
> * Poznano koszty wystąpień zarezerwowanych na platformie Azure
> * Poznano zalety wystąpień zarezerwowanych
> * Zoptymalizowano koszty wystąpień zarezerwowanych na platformie Azure
> * Wyświetlono koszty wystąpień zarezerwowanych
> * Oceniono opłacalność wystąpień zarezerwowanych na platformie Azure
> * Zoptymalizowano koszty wystąpień zarezerwowanych na platformie AWS
> * Zakupiono zalecane wystąpienia zarezerwowane
> * Zmodyfikowano nieużywane rezerwacje


Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat kontrolowania dostępu do danych.

> [!div class="nextstepaction"]
> [Kontrola dostępu do danych](tutorial-user-access.md)
