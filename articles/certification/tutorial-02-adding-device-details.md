---
title: Program certyfikowany na platformie Azure — samouczek — Dodawanie szczegółów urządzenia
description: Przewodnik krok po kroku dotyczący dodawania szczegółów urządzenia do projektu w portalu Azure Certified Device
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: c2a67e794932504150a1eacedd5418642623a20c
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969774"
---
# <a name="tutorial-add-device-details"></a>Samouczek: Dodawanie szczegółów urządzenia

Teraz utworzono projekt dla urządzenia i wszystko gotowe do rozpoczęcia procesu certyfikacji. Najpierw Dodajmy szczegóły urządzenia. Będą one zawierać specyfikacje techniczne, które mogą być wyświetlane klientom w wykazie certyfikowanych urządzeń platformy Azure, oraz szczegóły marketingowe, które będą używane do zakupu po dokonaniu decyzji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie szczegółów urządzenia za pomocą funkcji składniki i zależności
> * Przekazywanie przewodnika Wprowadzenie do urządzenia
> * Określ szczegóły marketingowe dla klientów w celu zakupu urządzenia
> * Opcjonalnie Zidentyfikuj wszelkie certyfikaty branżowe

## <a name="prerequisites"></a>Wymagania wstępne

* Należy się zalogować i utworzyć projekt dla swojego urządzenia w [portalu certyfikowanego urządzenia platformy Azure](https://certify.azure.com). Aby uzyskać więcej informacji, zobacz [samouczek](tutorial-01-creating-your-project.md).
* Powinieneś mieć Przewodnik wprowadzenie do urządzenia w formacie PDF. Udostępniamy kilka szablonów rozpoczęcia do użycia, w zależności od programu certyfikacji i preferowanego języka. [Szablony są dostępne w naszej lokalizacji](https://aka.ms/GSTemplate "Wprowadzenie do szablonów") w witrynie GitHub.

## <a name="adding-technical-device-details"></a>Dodawanie szczegółów urządzeń technicznych

Pierwsza sekcja strony projektu o nazwie "Szczegóły urządzenia wejściowego" umożliwia podanie informacji o podstawowych możliwościach sprzętowych urządzenia, takich jak nazwa urządzenia, opis, procesor, system operacyjny, opcje łączności, interfejsy sprzętu, protokoły branżowe i inne. Chociaż wiele pól jest opcjonalnych, większość tych informacji zostanie udostępniona potencjalnym klientom w katalogu certyfikowanych urządzeń platformy Azure w przypadku wybrania opcji opublikowania urządzenia po jego certyfikowaniu.

1. Kliknij `Add` sekcję "Szczegóły urządzenia wejściowego" na stronie Podsumowanie projektu, aby otworzyć sekcję Szczegóły urządzenia. Zobaczysz pięć sekcji do ukończenia.

![Obraz strony szczegółów projektu](./media/images/device-details-menu.png)

2. Przejrzyj informacje podane wcześniej podczas tworzenia projektu na `Basics` karcie.
1. Zapoznaj się z certyfikatami, które są stosowane do urządzenia, na `Certifications` karcie.
1. Otwórz `Product details` kartę i wybierz co najmniej jeden system operacyjny.
1. Dodaj **co najmniej** jeden składnik dyskretny opisujący Twoje urządzenie. Dodatkowe wskazówki dotyczące użycia składników można znaleźć [tutaj](how-to-using-the-components-feature.md).
1. Kliknij pozycję `Save` (Dalej). Następnie będzie można edytować urządzenie składowe i dodać bardziej zaawansowane szczegóły.
1. Wyświetl listę dodatkowych informacji o urządzeniu, które nie są przechwytywane przez szczegóły składnika poniżej `Additional product details` .
1. Jeśli oznaczono `Other` w którymkolwiek z pól składników lub masz szczególne okoliczności, które chcesz oflagować w zespole certyfikacji platformy Azure, pozostaw komentarz wyjaśniający w `Comments for reviewer` sekcji.
1. Użyj `Dependencies` karty, aby wyświetlić wszystkie zależności, jeśli urządzenie wymaga dodatkowego sprzętu lub usług do wysyłania danych do platformy Azure. Dodatkowe wskazówki dotyczące wyświetlania zależności można znaleźć [tutaj](how-to-indirectly-connected-devices.md).
1. Po spełnieniu podanych przez Ciebie informacji możesz użyć `Review` karty w celu uzyskania przeglądu dotyczącego pełnego zestawu szczegółowych informacji dotyczących urządzeń.
1. Kliknij `Project summary` w górnej części strony, aby powrócić do strony podsumowania.

![Przegląd strony szczegółów projektu](./media/images/sample-device-details.png)

## <a name="uploading-a-get-started-guide"></a>Przekazywanie przewodnika Wprowadzenie

Przewodnik wprowadzenie jest dokumentem PDF ułatwiającym instalację i konfigurację oraz zarządzanie produktem. Celem jest ułatwienie klientom łączenia i obsługi urządzeń na platformie Azure przy użyciu urządzenia. W ramach procesu certyfikacji firma Microsoft wymaga od naszych partnerów udostępnienia **jednego** przewodnika Wprowadzenie do ich najbardziej odpowiedniego programu certyfikacji.

1. Sprawdź dokładnie, czy zostały podane wszystkie żądane informacje w pliku PDF przewodnika Wprowadzenie, zgodnie z podanymi [szablonami](https://aka.ms/GSTemplate). Szablon, który ma być używany, powinien być określony na podstawie identyfikatora certyfikacji, który ma być stosowany. (Na przykład urządzenie IoT Plug and Play będzie używać szablonu Plug and Play IoT. Urządzenia mające zastosowanie *tylko* do certyfikacji podstawowej urządzenia z certyfikatem platformy Azure będą używać szablonu urządzenia z certyfikatem platformy Azure.
1. Kliknij `Add` w sekcji "wprowadzenie" przewodnika po stronie podsumowania projektu.

![Obraz przycisku GSG](./media/images/gsg-menu.png)

2. Kliknij pozycję "Wybierz plik", aby przekazać plik PDF.
1. Zapoznaj się z dokumentem w wersji zapoznawczej w celu formatowania.
1. Zapisz przekazywanie, klikając przycisk "Zapisz".
1. Kliknij `Project summary` w górnej części strony, aby powrócić do strony podsumowania.

## <a name="providing-marketing-details"></a>Udostępnianie szczegółów marketingowych

W tym obszarze uzyskasz informacje marketingowe dla Twojego urządzenia. Te pola będą prezentowane w katalogu certyfikowanych urządzeń platformy Azure, jeśli zdecydujesz się opublikować certyfikowane urządzenie.

1. Kliknij `Add` sekcję "Dodawanie szczegółów marketingowych", aby otworzyć stronę szczegóły marketingu.

![Ilustracja — sekcja szczegółów marketingowych](./media/images/marketing-details.png)

1. Przekaż zdjęcie produktu w formacie JPEG lub PNG, które będą używane w wykazie.
1. Napisz krótki opis urządzenia, który będzie wyświetlany na stronie opis produktu w wykazie.
1. Wskaż geograficzną dostępność urządzenia.
1. Podaj link do strony marketingowej producenta dla tego urządzenia. Powinien to być link do lokacji, która zawiera dodatkowe informacje o urządzeniu.
    > [!Note]
    > Upewnij się, że wszystkie podane adresy URL są prawidłowe lub będą aktywne w momencie publikacji po zatwierdzeniu. *)

1. Wskaż maksymalnie 3 branże docelowe, dla których Twoje urządzenie jest zoptymalizowane.
1. Podaj informacje dla maksymalnie 5 dystrybutorów urządzenia. Może to obejmować własną lokację producenta.

    > [!Note]
    > Jeśli nie zostanie podany adres URL strony produktu dystrybutora, wówczas `Shop` przycisk w wykazie będzie domyślnie używany link dostarczony dla `Distributor page` , który może nie być specyficzny dla urządzenia. Najlepiej, gdy adres URL dystrybutora powinien prowadzić do określonej strony, na której klient może zakupić urządzenie, ale nie jest to wymagane. Jeśli dystrybutor jest taki sam jak producent, ten adres URL może być taki sam, jak strona marketingowa producenta. *)

1. Kliknij `Save` , aby potwierdzić swoje informacje.
1. Kliknij `Project summary` w górnej części strony, aby powrócić do strony podsumowania.

## <a name="declaring-additional-industry-certifications"></a>Deklarowanie dodatkowych certyfikatów branżowych

Możesz również promować dodatkowe certyfikaty branżowe, które mogły zostać odebrane na urządzenie. Te certyfikaty mogą pomóc w zapewnieniu większej przejrzystości urządzenia i będzie można je przeszukiwać w katalogu certyfikowanych urządzeń platformy Azure.

1. Kliknij `Add` w sekcji "Podaj certyfikaty branżowe".
1. Kliknij, `Add a certification` Aby wybrać z listy typowych programów certyfikacji branżowej. Jeśli Twój produkt osiągnął certyfikat, którego nie ma na liście, możesz określić niestandardową wartość ciągu, wybierając opcję `Other (please specify)` .
1. Opcjonalnie podaj opis lub uwagi do recenzenta. Te informacje nie będą jednak dostępne publicznie do wyświetlania w wykazie.
1. Kliknij `Save` , aby potwierdzić swoje informacje.
1. Kliknij `Project summary` w górnej części strony, aby powrócić do strony podsumowania.

## <a name="next-steps"></a>Następne kroki

Teraz ukończono proces opisywania urządzenia. Pomoże to zespołowi z certyfikatem platformy Azure i klientom lepiej zrozumieć Twój produkt. Gdy podano informacje, możesz teraz przystąpić do przejścia do fazy testowania procesu certyfikacji.
> [!div class="nextstepaction"]
> [Samouczek: testowanie urządzenia](tutorial-03-testing-your-device.md)
