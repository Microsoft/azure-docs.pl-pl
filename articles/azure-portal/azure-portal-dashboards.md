---
title: Tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure Portal
description: W tym artykule opisano sposób tworzenia, dostosowywania, publikowania i udostępniania pulpitów nawigacyjnych w witrynie Azure portal.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: e6eda3b522a5b46cd82ef29ae493891a624d3272
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459291"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure Portal

Pulpity nawigacyjne są skoncentrowanym i zorganizowanym widokiem zasobów w chmurze w witrynie Azure portal. Używaj pulpitów nawigacyjnych jako obszaru roboczego, w którym można szybko uruchamiać zadania na potrzeby codziennych operacji i monitorować zasoby. Na przykład możesz tworzyć niestandardowe pulpity nawigacyjne na podstawie projektów, zadań lub ról użytkownika.

Portal Platformy Azure udostępnia domyślny pulpit nawigacyjny jako punkt wyjścia. Domyślny pulpit nawigacyjny można edytować. Twórz i dostosowuj dodatkowe pulpity nawigacyjne oraz publikuj i udostępniaj pulpity nawigacyjne, aby udostępnić je innym użytkownikom. W tym artykule opisano sposób tworzenia nowego pulpitu nawigacyjnego, dostosowywania interfejsu oraz publikowania i udostępniania pulpitów nawigacyjnych.

## <a name="create-a-new-dashboard"></a>Tworzenie nowego pulpitu nawigacyjnego

W tym przykładzie tworzymy nowy, prywatny pulpit nawigacyjny i przypisujemy nazwę. Wykonaj następujące kroki, aby rozpocząć proces:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Z menu portalu platformy Azure wybierz pozycję **Pulpit nawigacyjny**. Widok domyślny może być już ustawiony na pulpit nawigacyjny.

    ![Otwieranie pulpitu nawigacyjnego](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Wybierz **pozycję Nowy pulpit nawigacyjny**.

    ![Zrzut ekranu przedstawiający nowy pulpit nawigacyjny](./media/azure-portal-dashboards/create-new-dashboard.png)

    Ta akcja powoduje otwarcie **galerii kafelków,** z której można wybrać kafelki, oraz pustej siatki, na której będą rozmieszczać kafelki.

    ![Zrzut ekranu przedstawiający galerię kafelków i pustą siatkę](./media/azure-portal-dashboards/dashboard-name.png)

1. Zaznacz tekst **mój pulpit nawigacyjny** w etykiecie pulpitu nawigacyjnego i wprowadź nazwę, która pomoże Ci łatwo zidentyfikować niestandardowy pulpit nawigacyjny.

1. Wybierz **pozycję Gotowe dostosowywanie** w nagłówku strony, aby zakończyć tryb edycji.

Widok pulpitu nawigacyjnego pokazuje teraz nowy pulpit nawigacyjny. Wybierz strzałkę obok nazwy pulpitu nawigacyjnego, aby wyświetlić dostępne pulpity nawigacyjne. Lista może zawierać pulpity nawigacyjne utworzone i udostępnione przez innych użytkowników.

## <a name="edit-a-dashboard"></a>Edytowanie pulpitu nawigacyjnego

Teraz edytujmy pulpit nawigacyjny, aby dodać, zmienić rozmiar i rozmieścić kafelki reprezentujące zasoby platformy Azure.

### <a name="add-tiles-from-the-dashboard"></a>Dodawanie kafelków z pulpitu nawigacyjnego

Aby dodać kafelki do pulpitu nawigacyjnego, wykonaj następujące czynności:

1. Wybierz ![ikonę](./media/azure-portal-dashboards/dashboard-edit-icon.png) edycji **Edytuj** z nagłówka strony.

    ![Zrzut ekranu przedstawiający wyróżnioną edycję na pulpicie nawigacyjnym](./media/azure-portal-dashboards/dashboard-edit.png)

1. Przejrzyj **Galerię kafelków** lub użyj pola wyszukiwania, aby znaleźć odpowiedni kafelek.

1. Wybierz **pozycję Dodaj,** aby dodać kafelek do pulpitu nawigacyjnego o domyślnym rozmiarze i lokalizacji. Możesz też przeciągnąć kafelek do siatki i umieścić go w odpowiedni sposób.

> [!TIP]
> Jeśli pracujesz z więcej niż jedną organizacją, dodaj kafelek **Tożsamości organizacji** do pulpitu nawigacyjnego, aby wyraźnie pokazać, do której organizacji należą zasoby.

### <a name="add-tiles-from-a-resource-page"></a>Dodawanie kafelków ze strony zasobu

Istnieje alternatywny sposób dodawania kafelków do pulpitu nawigacyjnego. Wiele stron zasobów zawiera ikonę pineki na pasku poleceń. Jeśli wybierzesz ikonę, kafelek reprezentujący stronę źródłową zostanie przypięty do aktualnie aktywnego pulpitu nawigacyjnego. 

![Zrzut ekranu przedstawiający pasek poleceń strony z ikoną pinezki](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>Zmienianie rozmiaru lub zmienianie rozmieszczenia kafelków

Aby zmienić rozmiar kafelka lub zmienić rozmieszczenie kafelków na pulpicie nawigacyjnym, wykonaj następujące czynności:

1. Wybierz ![ikonę](./media/azure-portal-dashboards/dashboard-edit-icon.png) edycji **Edytuj** z nagłówka strony.

1. Wybierz menu kontekstowe w prawym górnym rogu kafelka. Następnie wybierz rozmiar kafelka. Kafelki obsługujące dowolny rozmiar zawierają również "uchwyt" w prawym dolnym rogu, który umożliwia przeciąganie kafelka do żądanego rozmiaru.

    ![Zrzut ekranu przedstawiający pulpit nawigacyjny z otwartym menu rozmiaru kafelka](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Wybierz kafelek i przeciągnij go w nowe miejsce na siatce, aby rozmieścić pulpit nawigacyjny.

### <a name="additional-tile-configuration"></a>Dodatkowa konfiguracja kafelków

Niektóre kafelki mogą wymagać większej konfiguracji, aby wyświetlić żądane informacje. Na przykład kafelek **wykresu metryki** musi być skonfigurowany do wyświetlania metryki z **usługi Azure Monitor**. Można również dostosować dane kafelków, aby zastąpić domyślne ustawienia czasu pulpitu nawigacyjnego.

Każdy kafelek, który musi być skonfigurowany, wyświetla baner **kafelka Konfiguruj** do momentu dostosowania kafelka. Aby dostosować kafelek:

1. Wybierz **pozycję Gotowe dostosowywanie** w nagłówku strony, aby zakończyć tryb edycji.

1. Wybierz baner, a następnie wykonaj wymaganą konfigurację.

    ![Zrzut ekranu przedstawiający kafelek, który wymaga konfiguracji](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Kafelek znaczników umożliwia wyświetlanie niestandardowej, statycznej zawartości na pulpicie nawigacyjnym. Mogą to być podstawowe instrukcje, obraz, zestaw hiperłączy, a nawet informacje kontaktowe. Aby uzyskać więcej informacji na temat używania kafelka znaczników, zobacz [Używanie kafelka znaczników na pulpitach nawigacyjnych platformy Azure do pokazania zawartości niestandardowej](azure-portal-markdown-tile.md).

### <a name="customize-tile-data"></a>Dostosowywanie danych kafelka

Dane na pulpicie nawigacyjnym automatycznie pokazują aktywność z ostatnich 24 godzin. Aby wyświetlić inny przedział czasu dla tylko tego kafelka, wykonaj następujące kroki:

1. Wybierz **pozycję Dostosuj dane kafelka** ![z](./media/azure-portal-dashboards/dashboard-filter.png) menu kontekstowego lub filtr ikony filtru w lewym górnym rogu kafelka.

    ![Zrzut ekranu przedstawiający menu kontekstowe kafelków](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Zaznacz to pole wyboru, aby **zastąpić ustawienia czasu pulpitu nawigacyjnego na poziomie kafelka**.

    ![Zrzut ekranu przedstawiający okno dialogowe w celu skonfigurowania ustawień czasu kafelka](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Wybierz przedział czasu, który ma być pokazywalny dla tego kafelka. Możesz wybrać od ostatnich 30 minut do ostatnich 30 dni lub zdefiniować niestandardowy zakres.

1. Wybierz szczegółowość czasu do wyświetlenia. Możesz wyświetlać w dowolnym miejscu od przyrostów jednominutowych do jednomiesięcznych.

1. Wybierz przycisk **Zastosuj**.

## <a name="delete-a-tile"></a>Usuwanie kafelka

Aby usunąć kafelek z pulpitu nawigacyjnego, wykonaj następujące czynności:

* Wybierz menu kontekstowe w prawym górnym rogu kafelka, a następnie wybierz pozycję **Usuń z pulpitu nawigacyjnego**. Lub:

* Wybierz ![ikonę](./media/azure-portal-dashboards/dashboard-edit-icon.png) **edycji Edytuj,** aby przejść do trybu dostosowywania. Umieść wskaźnik myszy w prawym górnym rogu ![kafelka, a następnie wybierz ikonę usuwania ikony](./media/azure-portal-dashboards/dashboard-delete-icon.png) usuwania, aby usunąć kafelek z pulpitu nawigacyjnego.

   ![Zrzut ekranu przedstawiający sposób usuwania kafelka z pulpitu nawigacyjnego](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Klonowanie pulpitu nawigacyjnego

Aby użyć istniejącego pulpitu nawigacyjnego jako szablonu nowego pulpitu nawigacyjnego, wykonaj następujące czynności:

1. Upewnij się, że w widoku pulpitu nawigacyjnego jest wyświetlany pulpit nawigacyjny, który chcesz skopiować.

1. W nagłówku strony ![wybierz](./media/azure-portal-dashboards/dashboard-clone.png) ikonę **klonuj ikonę Klonuj**.

1. Kopia pulpitu nawigacyjnego o nazwie *Klonuj nazwę pulpitu nawigacyjnego* zostanie otwarta w trybie edycji. **Clone of** Użyj poprzednich kroków w tym artykule, aby zmienić nazwę i dostosować pulpit nawigacyjny.

## <a name="publish-and-share-a-dashboard"></a>Publikowanie i udostępnianie pulpitu nawigacyjnego

Gdy tworzysz pulpit nawigacyjny, jest on domyślnie prywatny, co oznacza, że jesteś jedynym, który może go zobaczyć. Aby udostępnić pulpity nawigacyjne innym osobom, możesz je publikować i udostępniać. Aby uzyskać więcej informacji, zobacz [Udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu kontroli dostępu opartej na rolach](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Otwieranie udostępnionego pulpitu nawigacyjnego

Aby znaleźć i otworzyć udostępniony pulpit nawigacyjny, wykonaj następujące czynności:

1. Wybierz strzałkę obok nazwy pulpitu nawigacyjnego.

1. Wybierz z wyświetlona lista pulpitów nawigacyjnych. Jeśli pulpitu nawigacyjnego, który chcesz otworzyć, nie ma na liście:

    1. wybierz **pozycję Przeglądaj wszystkie pulpity nawigacyjne**.

        ![Zrzut ekranu przedstawiający menu wyboru pulpitu nawigacyjnego](./media/azure-portal-dashboards/dashboard-browse.png)

    1. W polu **Typ** wybierz pozycję **Udostępnione pulpity nawigacyjne**.

        ![Zrzut ekranu przedstawiający menu wyboru wszystkich pulpitów nawigacyjnych](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Wybierz jedną lub więcej subskrypcji. Można również wprowadzić tekst, aby filtrować pulpity nawigacyjne według nazwy.

    1. Wybierz pulpit nawigacyjny z listy udostępnionych pulpitów nawigacyjnych.

## <a name="delete-a-dashboard"></a>Usunięcie pulpitu nawigacyjnego

Aby trwale usunąć prywatny lub udostępniony pulpit nawigacyjny, wykonaj następujące czynności:

1. Wybierz pulpit nawigacyjny, który chcesz usunąć z listy obok nazwy pulpitu nawigacyjnego.

1. Wybierz ![pozycję](./media/azure-portal-dashboards/dashboard-delete-icon.png) Usuń ikonę **Usuń** z nagłówka strony.

1. W przypadku prywatnego pulpitu nawigacyjnego wybierz przycisk **OK** w oknie dialogowym potwierdzenia, aby usunąć pulpit nawigacyjny. W przypadku udostępnionego pulpitu nawigacyjnego w oknie dialogowym potwierdzenia zaznacz pole wyboru, aby potwierdzić, że opublikowany pulpit nawigacyjny nie będzie już widoczny przez inne osoby. Następnie wybierz przycisk **OK**.

    ![Zrzut ekranu przedstawiający potwierdzenie usunięcia](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Następne kroki

* [Udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu kontroli dostępu opartej na rolach](azure-portal-dashboard-share-access.md)
* [Programowo tworzyć pulpity nawigacyjne platformy Azure](azure-portal-dashboards-create-programmatically.md)
