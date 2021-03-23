---
title: Tworzenie pulpitu nawigacyjnego w Azure Portal
description: W tym artykule opisano sposób tworzenia i dostosowywania pulpitu nawigacyjnego w Azure Portal.
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: fa7f1813d86571b568d23d64cab5705f8a117faa
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774662"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>Tworzenie pulpitu nawigacyjnego w Azure Portal

Pulpity nawigacyjne to skoncentrowany i zorganizowany widok zasobów w chmurze w Azure Portal. Używaj pulpitów nawigacyjnych jako obszaru roboczego, w którym można monitorować zasoby i szybko uruchamiać zadania dla codziennych operacji. Na przykład możesz tworzyć niestandardowe pulpity nawigacyjne na podstawie projektów, zadań lub ról użytkownika.

Azure Portal udostępnia domyślny pulpit nawigacyjny jako punkt początkowy. Można edytować domyślnego pulpitu nawigacyjnego i tworzyć i dostosowywać dodatkowe pulpity nawigacyjne. W tym artykule opisano sposób tworzenia nowego pulpitu nawigacyjnego i dostosowywania go. Aby uzyskać informacje na temat udostępniania pulpitów nawigacyjnych, zobacz [udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu kontroli dostępu opartej na rolach platformy Azure](azure-portal-dashboard-share-access.md).

## <a name="create-a-new-dashboard"></a>Tworzenie nowego pulpitu nawigacyjnego

W tym przykładzie utworzymy nowy prywatny pulpit nawigacyjny i przypiszesz nazwę. Wykonaj następujące kroki, aby rozpocząć proces:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Z menu Azure Portal wybierz pozycję **pulpit nawigacyjny**. Domyślny widok może już być ustawiony na pulpit nawigacyjny.

    ![Otwieranie pulpitu nawigacyjnego](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Wybierz pozycję **nowy pulpit nawigacyjny** i przycisk **pusty pulpit nawigacyjny**.

    ![Zrzut ekranu przedstawiający nowy pulpit nawigacyjny](./media/azure-portal-dashboards/create-new-dashboard.png)

    Ta akcja powoduje otwarcie **galerii kafelków**, z której będą wybierane kafelki, i pustej siatki, w której będą rozmieszczane kafelki.

1. Wybierz **mój tekst mojego pulpitu nawigacyjnego** w etykiecie pulpitu nawigacyjnego i wprowadź nazwę, która ułatwi Ci łatwą identyfikację niestandardowego pulpitu nawigacyjnego.

    ![Zrzut ekranu przedstawiający galerię kafelków i pustą siatkę](./media/azure-portal-dashboards/dashboard-name.png)

1. W nagłówku strony wybierz pozycję **gotowe do dostosowania** , aby wyjść z trybu edycji, a następnie wybierz pozycję **Zapisz**.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="Zrzut ekranu procesu zapisywania pulpitu nawigacyjnego":::

W widoku pulpitu nawigacyjnego zostanie wyświetlony nowy pulpit nawigacyjny. Wybierz strzałkę obok nazwy pulpitu nawigacyjnego, aby wyświetlić dostępne pulpity nawigacyjne. Lista może zawierać pulpity nawigacyjne, które zostały utworzone i udostępnione przez innych użytkowników.

## <a name="edit-a-dashboard"></a>Edytowanie pulpitu nawigacyjnego

Teraz Zmodyfikujmy pulpit nawigacyjny, aby dodawać, zmieniać rozmiar i rozmieszczać kafelki, które reprezentują zasoby platformy Azure.

### <a name="add-tiles-from-the-tile-gallery"></a>Dodawanie kafelków z galerii kafelków

Aby dodać kafelki do pulpitu nawigacyjnego, wykonaj następujące kroki:

1. Wybierz pozycję ![ Edytuj ikonę ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Edytuj** w nagłówku strony.

    ![Zrzut ekranu przedstawiający edytowanie na pulpicie nawigacyjnym](./media/azure-portal-dashboards/dashboard-edit.png)

1. Przeglądaj **galerię kafelków** lub użyj pola wyszukiwania, aby znaleźć żądany kafelek.

1. Wybierz pozycję **Dodaj** , aby dodać kafelek do pulpitu nawigacyjnego przy użyciu domyślnego rozmiaru i lokalizacji. Możesz też przeciągnąć kafelek do siatki i umieścić go w dowolnym miejscu. Dodaj dowolne kafelki, ale poniżej przedstawiono kilka pomysłów:

    - Dodaj **wszystkie zasoby** , aby wyświetlić wszystkie zasoby, które zostały już utworzone.

    - Jeśli pracujesz z więcej niż jedną organizacją, Dodaj kafelek **tożsamość organizacji** do pulpitu nawigacyjnego, aby wyraźnie pokazać, do której organizacji należą zasoby.

1. W nagłówku strony wybierz pozycję **Zapisz**.

### <a name="add-tiles-from-a-resource-page"></a>Dodawanie kafelków ze strony zasobów

Istnieje alternatywny sposób dodawania kafelków do pulpitu nawigacyjnego. Wiele stron zasobów zawiera ikonę pinezki na pasku poleceń. Jeśli wybierzesz ikonę, kafelek reprezentujący stronę źródłową zostanie przypięty do pulpitu nawigacyjnego, który jest obecnie aktywny. 

![Zrzut ekranu przedstawiający pasek poleceń strony z ikoną pinezki](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>Zmiana rozmiaru lub zmiany układu kafelków

Aby zmienić rozmiar kafelka lub ponownie rozmieścić kafelki na pulpicie nawigacyjnym, wykonaj następujące kroki:

1. Wybierz pozycję ![ Edytuj ikonę ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Edytuj** w nagłówku strony.

1. Wybierz menu kontekstowe w prawym górnym rogu kafelka. Następnie wybierz rozmiar kafelka. Kafelki obsługujące dowolny rozmiar obejmują również "uchwyt" w prawym dolnym rogu, które umożliwiają przeciągnięcie kafelka do żądanego rozmiaru.

    ![Zrzut ekranu pulpitu nawigacyjnego z otwartym menu Rozmiar kafelka](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Wybierz kafelek i przeciągnij go do nowej lokalizacji w siatce, aby rozmieścić pulpit nawigacyjny.

### <a name="additional-tile-configuration"></a>Dodatkowa konfiguracja kafelka

Niektóre kafelki mogą wymagać więcej konfiguracji w celu wyświetlenia potrzebnych informacji. Na przykład kafelek **wykresu metryk** musi być skonfigurowany do wyświetlania metryki z Azure monitor. Możesz również dostosować dane kafelków, aby zastąpić domyślne ustawienia czasu pulpitu nawigacyjnego.

Wszystkie kafelki, które należy skonfigurować, są wyświetlane na transparencie do momentu dostosowania kafelka. W przypadku **wykresu metryk** transparent jest **edytowany w metrykach**. Aby dostosować kafelek:

1. W nagłówku strony wybierz pozycję **Zapisz** , aby wyjść z trybu edycji.

1. Wybierz transparent, a następnie wykonaj wymagane czynności konfiguracyjne.

    ![Zrzut ekranu przedstawiający kafelek, który wymaga konfiguracji](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Kafelek promocji pozwala na Wyświetlanie niestandardowej, statycznej zawartości na pulpicie nawigacyjnym. Mogą to być podstawowe instrukcje, obraz, zestaw hiperłączy, a nawet informacje kontaktowe. Aby uzyskać więcej informacji na temat korzystania z kafelka z promocji, zobacz temat [Używanie kafelka promocji na pulpitach nawigacyjnych platformy Azure do wyświetlania zawartości niestandardowej](azure-portal-markdown-tile.md).

### <a name="customize-tile-data"></a>Dostosuj dane kafelka

Dane na pulpicie nawigacyjnym automatycznie pokazują aktywność dla ostatnich 24 godzin. Aby wyświetlić inny przedział czasu dla tylko tego kafelka, wykonaj następujące kroki:

1. Wybierz opcję **Dostosuj dane kafelków** z menu kontekstowego lub z ![ filtra ikon filtru ](./media/azure-portal-dashboards/dashboard-filter.png) w lewym górnym rogu kafelka.

    ![Zrzut ekranu przedstawiający menu kontekstowe kafelka](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Zaznacz pole wyboru, aby **zastąpić ustawienia czasu pulpitu nawigacyjnego na poziomie kafelka**.

    ![Zrzut ekranu okna dialogowego, aby skonfigurować ustawienia czasu kafelka](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Wybierz przedział czasu, który ma być wyświetlany dla tego kafelka. Możesz wybrać od ostatnich 30 minut do ostatnich 30 dni lub zdefiniować zakres niestandardowy.

1. Wybierz stopień szczegółowości czasu do wyświetlenia. Możesz pokazać dowolne miejsce od 1 minuty do jednego miesiąca.

1. Wybierz przycisk **Zastosuj**.

## <a name="delete-a-tile"></a>Usuwanie kafelka

Aby usunąć kafelek z pulpitu nawigacyjnego, wykonaj następujące kroki:

* Wybierz menu kontekstowe w prawym górnym rogu kafelka, a następnie wybierz pozycję **Usuń z pulpitu nawigacyjnego**. Lub:

* Wybierz pozycję ![ Edytuj ikonę ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Edytuj** , aby wprowadzić tryb dostosowywania. Umieść kursor w prawym górnym rogu kafelka, a następnie wybierz ![ ikonę Usuń ikona ](./media/azure-portal-dashboards/dashboard-delete-icon.png) Usuń, aby usunąć kafelek z pulpitu nawigacyjnego.

   ![Zrzut ekranu przedstawiający sposób usuwania kafelka z pulpitu nawigacyjnego](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Klonowanie pulpitu nawigacyjnego

Aby użyć istniejącego pulpitu nawigacyjnego jako szablonu dla nowego pulpitu nawigacyjnego, wykonaj następujące kroki:

1. Upewnij się, że widok pulpitu nawigacyjnego pokazuje pulpit nawigacyjny, który chcesz skopiować.

1. W nagłówku strony wybierz klonowanie ikony klonowania ![ ](./media/azure-portal-dashboards/dashboard-clone.png) .

1. Kopia pulpitu nawigacyjnego o nazwie **klon** *nazwy pulpitu nawigacyjnego* otwiera się w trybie edycji. Aby zmienić nazwę i dostosować pulpit nawigacyjny, należy wykonać kroki opisane w tym artykule.

## <a name="publish-and-share-a-dashboard"></a>Publikowanie i udostępnianie pulpitu nawigacyjnego

Gdy tworzysz pulpit nawigacyjny, jest on domyślnie prywatny, co oznacza, że jesteś jedyną osobą, która ją widzi. Aby udostępnić pulpity nawigacyjne innym osobom, możesz je opublikować i udostępnić. Aby uzyskać więcej informacji, zobacz [udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu kontroli dostępu opartej na rolach platformy Azure](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Otwórz udostępniony pulpit nawigacyjny

Aby znaleźć i otworzyć udostępniony pulpit nawigacyjny, wykonaj następujące kroki:

1. Wybierz strzałkę obok nazwy pulpitu nawigacyjnego.

1. Wybierz z wyświetlonej listy pulpitów nawigacyjnych. Jeśli pulpit nawigacyjny, który chcesz otworzyć, nie znajduje się na liście:

    1. Wybierz pozycję **Przeglądaj wszystkie pulpity nawigacyjne**.

        ![Zrzut ekranu przedstawiający menu wyboru pulpitu nawigacyjnego](./media/azure-portal-dashboards/dashboard-browse.png)

    1. W polu **Typ** wybierz pozycję **udostępnione pulpity nawigacyjne**.

        ![Zrzut ekranu przedstawiający menu wyboru wszystkich pulpitów nawigacyjnych](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Wybierz co najmniej jedną subskrypcję. Możesz również wprowadzić tekst, aby filtrować pulpity nawigacyjne według nazwy.

    1. Wybierz pulpit nawigacyjny z listy udostępnionych pulpitów nawigacyjnych.

## <a name="delete-a-dashboard"></a>Usuwanie pulpitu nawigacyjnego

Aby trwale usunąć prywatny lub udostępniony pulpit nawigacyjny, wykonaj następujące kroki:

1. Wybierz pulpit nawigacyjny, który chcesz usunąć z listy obok nazwy pulpitu nawigacyjnego.

1. Wybierz pozycję ![ Usuń ikonę ](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Usuń** z nagłówka strony.

1. W przypadku prywatnego pulpitu nawigacyjnego wybierz pozycję **OK** w oknie dialogowym potwierdzenia, aby usunąć pulpit nawigacyjny. W przypadku udostępnionego pulpitu nawigacyjnego w oknie dialogowym potwierdzenia zaznacz pole wyboru, aby potwierdzić, że opublikowany pulpit nawigacyjny nie będzie już widoczny dla innych użytkowników. Następnie wybierz przycisk **OK**.

    ![Zrzut ekranu przedstawiający potwierdzenie usunięcia](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="recover-a-deleted-dashboard"></a>Odzyskiwanie usuniętego pulpitu nawigacyjnego

Jeśli jesteś w globalnej chmurze platformy Azure i usuniesz _opublikowany_ pulpit nawigacyjny w Azure Portal, możesz odzyskać ten pulpit nawigacyjny w ciągu 14 dni od usunięcia. Aby uzyskać więcej informacji, zobacz [Odzyskiwanie usuniętego pulpitu nawigacyjnego w Azure Portal](recover-shared-deleted-dashboard.md).

## <a name="next-steps"></a>Następne kroki

* [Udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu kontroli dostępu opartej na rolach na platformie Azure](azure-portal-dashboard-share-access.md)
* [Programowe tworzenie pulpitów nawigacyjnych platformy Azure](azure-portal-dashboards-create-programmatically.md)
