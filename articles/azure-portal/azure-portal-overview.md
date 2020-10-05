---
title: Omówienie witryny Azure Portal | Microsoft Docs
description: Azure Portal jest graficznym interfejsem użytkownika, który służy do zarządzania usługami platformy Azure. Dowiedz się, jak nawigować w Azure Portal i znajdować zasoby.
services: azure-portal
keywords: portal
author: mgblythe
ms.author: mblythe
ms.date: 12/20/2019
ms.topic: overview
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 4aa1ccbd9b605850052e190949542eb014fe90a8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "84763928"
---
# <a name="azure-portal-overview"></a>Azure Portal — omówienie

W tym artykule wprowadzono Azure Portal, identyfikują elementy strony portalu i ułatwiają zapoznanie się z możliwościami zarządzania Azure Portal.

## <a name="what-is-the-azure-portal"></a>Co to jest Azure Portal?

Witryna Azure Portal to ujednolicona, oparta na sieci Web konsola, która stanowi alternatywę dla narzędzi wiersza polecenia. Dzięki witrynie Azure Portal możesz zarządzać subskrypcją platformy Azure przy użyciu graficznego interfejsu użytkownika. Możesz tworzyć i monitorować wszystko, począwszy od prostych aplikacji internetowych po złożone wdrożenia w chmurze, a także zarządzać nimi. Twórz niestandardowe pulpity nawigacyjne, aby uzyskać zorganizowany widok zasobów. Konfiguruj opcje ułatwień dostępu w celu zapewnienia optymalnego środowiska.

Azure Portal jest zaprojektowana pod kątem odporności i ciągłej dostępności. Jest ona dostępna w każdym centrum danych platformy Azure. Ta konfiguracja sprawia, że Azure Portal odporne na awarie poszczególnych centrów danych i pozwala uniknąć spowolnienia działania sieci przez zamknięcie użytkowników. Witryna Azure Portal jest stale aktualizowana i nie wymaga przestojów pod kątem działań konserwacyjnych.

## <a name="azure-portal-menu"></a>Menu Azure Portal

Możesz wybrać tryb domyślny dla menu portalu. Może być zadokowane lub może działać jako panel wysuwany.

Gdy menu portalu jest w trybie wysuwania, jest ono ukryte, dopóki nie będzie potrzebne. Wybierz ikonę menu, aby otworzyć lub zamknąć menu.

![Menu Azure Portal w trybie wysuwania](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

W przypadku wybrania opcji Tryb zadokowany dla menu Portal będzie on zawsze widoczny. Możesz zwinąć menu, aby zwiększyć miejsce pracy.

![Menu Azure Portal w trybie zadokowanym](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Strona główna platformy Azure

Jako nowy subskrybent usług platformy Azure pierwszy element widoczny po [zalogowaniu się do portalu](https://portal.azure.com) to **Strona główna platformy Azure**. Ta strona kompiluje zasoby, które pomogą w pełni wykorzystać subskrypcję platformy Azure. Dodaliśmy linki do bezpłatnych kursów online, dokumentacji, usług podstawowych i przydatnych witryn, które umożliwiają bieżące i zarządzanie zmianami w organizacji. Aby szybko i łatwo uzyskać dostęp do pracy w toku, pokazujemy również listę ostatnio odwiedzonych zasobów. Nie możesz dostosować tej strony, ale możesz wybrać, czy ma być wyświetlany **pulpit nawigacyjny** **platformy Azure** , czy Azure jako widok domyślny. Gdy logujesz się po raz pierwszy, w górnej części strony pojawi się monit, na którym możesz zapisać preferencję.

![Zrzut ekranu przedstawiający selektor widoku domyślnego](./media/azure-portal-overview/azure-portal-default-view.png)

Zarówno menu Azure Portal, jak i domyślny widok platformy Azure można zmienić w **ustawieniach portalu**. Jeśli zmienisz wybór, zmiana zostanie natychmiast zastosowana.

![Zrzut ekranu przedstawiający selektor widoku domyślnego](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Pulpit nawigacyjny platformy Azure

Pulpity nawigacyjne zapewniają skoncentrowany widok zasobów w ramach subskrypcji, które najlepiej odpowiadają. Udostępniono domyślny pulpit nawigacyjny, aby rozpocząć pracę. Możesz dostosować ten pulpit nawigacyjny, aby zapewnić często używane zasoby w jednym widoku. Wszelkie zmiany wprowadzone w widoku domyślnym mają wpływ tylko na Twoje doświadczenie. Można jednak utworzyć dodatkowe pulpity nawigacyjne do własnych potrzeb lub opublikować dostosowane pulpity nawigacyjne i udostępnić je innym użytkownikom w organizacji. Aby uzyskać więcej informacji, zobacz [Tworzenie i udostępnianie pulpitów nawigacyjnych w Azure Portal](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Więcej informacji na temat portalu

Pomocne jest zrozumienie podstawowego układu portalu i sposobu korzystania z niego. W tym miejscu wprowadzimy składniki interfejsu użytkownika i część terminologii, której używamy w celu uzyskania instrukcji. Aby zapoznać się z bardziej szczegółowym przewodnikiem dotyczącym portalu, zobacz Lekcja kursu [nawigowanie po portalu](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal).

Menu Azure Portal i nagłówek strony to globalne elementy, które zawsze są obecne. Te funkcje trwałe są "powłoką" dla interfejsu użytkownika skojarzonego z każdą pojedynczą usługą lub funkcją, a nagłówek zapewnia dostęp do kontrolek globalnych. Strona konfiguracji (czasami określana jako "blok") dla zasobu może również mieć menu zasobów ułatwiające przechodzenie między funkcjami.

Na rysunku poniżej przedstawiono podstawowe elementy Azure Portal, z których każdy jest opisany w poniższej tabeli.

![Zrzut ekranu przedstawiający widok portalu pełnoekranowego i klucz do elementów interfejsu użytkownika](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![Zrzut ekranu przedstawiający menu rozwijane portalu](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|Klucz|Opis
|:---:|---|
|1|Nagłówek strony. Pojawia się u góry każdej strony portalu i zawiera elementy globalne.|
|2| Wyszukiwanie globalne. Pasek wyszukiwania umożliwia szybkie znalezienie określonego zasobu, usługi lub dokumentacji.|
|3|Kontrolki globalne. Podobnie jak w przypadku wszystkich elementów globalnych, te funkcje są zachowywane w portalu i obejmują: Cloud Shell, filtr subskrypcji, powiadomienia, ustawienia portalu, pomoc i obsługa techniczna oraz wysyłać do nas opinie.|
|4|Twoje konto. Wyświetl informacje o Twoim koncie, przełącz katalogi, Wyloguj się lub Zaloguj się przy użyciu innego konta.|
|5|Menu portalu. Menu Portal jest elementem globalnym, który ułatwia nawigowanie między usługami. Czasami określany jako pasek boczny, Tryb menu portalu można zmienić w **ustawieniach portalu**.|
|6|Menu zasób. Wiele usług obejmuje menu zasobów ułatwiające zarządzanie usługą. Ten element może zostać wyświetlony jako lewe okienko.|
|7|Pasek poleceń. Formanty na pasku poleceń są kontekstowe dla bieżącego fokusu.|
|8|Okienko robocze.  Wyświetla szczegóły zasobu, który jest aktualnie fokusem.|
|9|Stron. Linki do stron nadrzędnych umożliwiają przeniesienie poziomu w przepływie pracy.|
|10|Formant główny służący do tworzenia nowego zasobu w bieżącej subskrypcji. Rozwiń lub Otwórz menu portalu, aby znaleźć i **utworzyć zasób**. Wyszukaj lub Przeglądaj witrynę Azure Marketplace dla typu zasobu, który chcesz utworzyć.|
|11|Lista ulubionych. Zobacz [Dodawanie, usuwanie i sortowanie ulubionych,](../azure-portal/azure-portal-add-remove-sort-favorites.md) aby dowiedzieć się, jak dostosować listę.|

## <a name="get-started-with-services"></a>Wprowadzenie do usług

Jeśli jesteś nowym subskrybentem, musisz utworzyć zasób, aby móc zarządzać nimi. Wybierz pozycję **+ Utwórz zasób** , aby wyświetlić usługi dostępne w portalu Azure Marketplace. W tym miejscu znajdziesz aplikacje i usługi z setek dostawców, a wszystkie certyfikowane do uruchamiania na platformie Azure.

Twoje ulubione na pasku bocznym są wstępnie wypełnione za pomocą linków do często używanych usług.  Aby wyświetlić wszystkie dostępne usługi, wybierz pozycję **wszystkie usługi** na pasku bocznym.

> [!TIP]
> Najszybszym sposobem znalezienia zasobu, usługi lub dokumentacji jest użycie *wyszukiwania* w nagłówku globalnym. Użyj linków do stron nadrzędnych, aby wrócić do poprzedniej strony.
>
Obejrzyj ten film wideo, aby zapoznać się z pokazem korzystania z wyszukiwania globalnego w Azure Portal.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Jak używać wyszukiwania globalnego w Azure Portal](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o tym, gdzie uruchamiać Azure Portal w [obsługiwanych przeglądarkach i urządzeniach](../azure-portal/azure-portal-supported-browsers-devices.md)
* Pozostawanie połączone z [aplikacja mobilna platformy Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* Dołączanie i Konfigurowanie środowiska chmury przy użyciu [centrum szybkiego startu platformy Azure](../azure-portal/azure-portal-quickstart-center.md)
