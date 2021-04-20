---
title: Tworzenie pulpitu nawigacyjnego w Azure Portal
description: W tym artykule opisano sposób tworzenia i dostosowywania pulpitu nawigacyjnego w Azure Portal.
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.topic: how-to
ms.date: 04/15/2021
ms.openlocfilehash: 0666a9f8ca9df2fa44a7eaa4045c9b5e9a724ff5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726105"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>Tworzenie pulpitu nawigacyjnego w Azure Portal

Pulpity nawigacyjne to skoncentrowany i zorganizowany widok zasobów w chmurze w Azure Portal. Pulpitów nawigacyjnych można używać jako obszaru roboczego, w którym można monitorować zasoby i szybko uruchamiać zadania w celu wykonywania codziennie wykonywanych operacji. Na przykład możesz tworzyć niestandardowe pulpity nawigacyjne na podstawie projektów, zadań lub ról użytkownika.

Interfejs Azure Portal jako punkt początkowy udostępnia domyślny pulpit nawigacyjny. Możesz edytować domyślny pulpit nawigacyjny oraz tworzyć i dostosowywać dodatkowe pulpity nawigacyjne.

> [!NOTE]
> Każdy użytkownik może utworzyć maksymalnie 100 prywatnych pulpitów nawigacyjnych. Jeśli [opublikujesz i udostępnisz](azure-portal-dashboard-share-access.md)pulpit nawigacyjny , zostanie on zaimplementowany jako zasób platformy Azure w ramach subskrypcji i nie będzie wliczony do tego limitu.

W tym artykule opisano sposób tworzenia nowego pulpitu nawigacyjnego i dostosowywania go. Aby uzyskać informacje na temat udostępniania pulpitów nawigacyjnych, zobacz [Share Azure dashboards by using Azure role-based access control](azure-portal-dashboard-share-access.md)(Udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu kontroli dostępu opartej na rolach na platformie Azure).

## <a name="create-a-new-dashboard"></a>Tworzenie nowego pulpitu nawigacyjnego

W tym przykładzie pokazano, jak utworzyć nowy prywatny pulpit nawigacyjny z przypisaną nazwą. Wszystkie pulpity nawigacyjne są prywatne podczas tworzenia, ale jeśli chcesz, możesz opublikować pulpit nawigacyjny i udostępnić go innym użytkownikom w organizacji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Z menu Azure Portal pulpitu **nawigacyjnego.** Domyślny widok może być już ustawiony na pulpit nawigacyjny.

    ![Zrzut ekranu przedstawiający Azure Portal pulpitu nawigacyjnego.](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Wybierz pozycję **Nowy pulpit nawigacyjny,** a następnie **pozycję Pusty pulpit nawigacyjny.**

    ![Zrzut ekranu przedstawiający opcje Nowy pulpit nawigacyjny.](./media/azure-portal-dashboards/create-new-dashboard.png)

    Ta akcja spowoduje otwarcie **galerii kafelków,** z której można wybierać kafelki, oraz pustej siatki, w której będziesz rozmieszczać kafelki.

1. Wybierz tekst **Mój pulpit nawigacyjny** w etykiecie pulpitu nawigacyjnego i wprowadź nazwę, która pomoże Ci łatwo zidentyfikować niestandardowy pulpit nawigacyjny.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-name.png" alt-text="Zrzut ekranu przedstawiający pustą siatkę z galerią kafelków.":::

1. Aby zapisać pulpit nawigacyjny bez zmiany, wybierz pozycję **Gotowe dostosowywanie** w nagłówku strony. Możesz też przejdź do następnej sekcji, aby dodać kafelki i zapisać pulpit nawigacyjny.

W widoku pulpitu nawigacyjnego jest teraz dostępny nowy pulpit nawigacyjny. Wybierz strzałkę obok nazwy pulpitu nawigacyjnego, aby wyświetlić dostępne pulpity nawigacyjne. Lista może zawierać pulpity nawigacyjne utworzone i udostępnione przez innych użytkowników.

## <a name="edit-a-dashboard"></a>Edytowanie pulpitu nawigacyjnego

Teraz zedytujemy pulpit nawigacyjny, aby dodawać i zmieniać rozmiar kafelków reprezentujących zasoby platformy Azure, a także zmieniać ich rozmiar i rozmieszczać je.

### <a name="add-tiles-from-the-tile-gallery"></a>Dodawanie kafelków z galerii kafelków

Aby dodać kafelki do pulpitu nawigacyjnego, wykonaj następujące kroki:

1. Wybierz ![ ikonę ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **edycji Edytuj** w nagłówku strony pulpitu nawigacyjnego.

    ![Zrzut ekranu przedstawiający pulpit nawigacyjny z wyróżnieniami opcji Edytuj.](./media/azure-portal-dashboards/dashboard-edit.png)

1. Przejrzyj **galerię kafelków** lub użyj pola wyszukiwania, aby znaleźć określony kafelek. Wybierz kafelek, który chcesz dodać do pulpitu nawigacyjnego.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-gallery.png" alt-text="Zrzut ekranu przedstawiający galerię kafelków.":::

1. Wybierz **pozycję Dodaj,** aby dodać kafelek do pulpitu nawigacyjnego o domyślnym rozmiarze i lokalizacji. Możesz też przeciągnąć kafelek do siatki i umieścić go w chętnych miejscach. Dodaj dowolne kafelki, ale oto kilka pomysłów:

    - Dodaj **pozycję Wszystkie** zasoby, aby wyświetlić wszystkie utworzone zasoby.

    - Jeśli pracujesz z więcej niż  jedną organizacją, dodaj kafelek Tożsamość organizacji do pulpitu nawigacyjnego, aby wyraźnie pokazać, do której organizacji należą zasoby.

1. W razie potrzeby zmień rozmiar kafelka, przeciągając i upuszczając prawy dolny róg kafelka.

1. Aby zapisać zmiany, wybierz pozycję **Zapisz** w nagłówku strony. Możesz również wyświetlić podgląd zmian bez zapisywania, wybierając pozycję **Podgląd** w nagłówku strony. Na ekranie podglądu  możesz wybrać pozycję Zapisz, aby zachować  zmiany, **odrzuć,** aby je usunąć, lub pozycję Edytuj, aby wrócić do opcji edycji i wprowadzić dalsze zmiany.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="Zrzut ekranu przedstawiający opcje Podgląd, Zapisz i Odrzuć.":::

### <a name="pin-content-from-a-resource-page"></a>Przypinanie zawartości ze strony zasobu

Innym sposobem dodawania kafelków do pulpitu nawigacyjnego jest bezpośrednio ze strony zasobu.

Wiele stron zasobów zawiera ikonę pinezki na pasku poleceń. Jeśli wybierzesz tę ikonę, możesz przypiąć kafelek reprezentujący stronę źródłową do istniejącego pulpitu nawigacyjnego lub do nowo tworzyć pulpitu nawigacyjnego.

![Zrzut ekranu przedstawiający pasek poleceń strony z ikoną pinezki](./media/azure-portal-dashboards/dashboard-pin-blade.png)

W niektórych przypadkach ikona pinezki może być również wyświetlana według określonej zawartości na stronie, co oznacza, że można przypiąć kafelek dla określonej zawartości, a nie całej strony.

### <a name="resize-or-rearrange-tiles"></a>Zmienianie rozmiaru kafelków lub zmienianie ich rozmieszczania

Aby zmienić rozmiar kafelka lub zmienić rozmieszczenie kafelków na pulpicie nawigacyjnym, wykonaj następujące kroki:

1. Wybierz ![ ikonę edycji ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Edytuj** w nagłówku strony.

1. Wybierz menu kontekstowe w prawym górnym rogu kafelka. Następnie wybierz rozmiar kafelka. Kafelki, które obsługują dowolny rozmiar, zawierają również "uchwyt" w prawym dolnym rogu, który umożliwia przeciągnięcie kafelka do odpowiedniego rozmiaru.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-resize.png" alt-text="Zrzut ekranu przedstawiający pulpit nawigacyjny z otwartym menu rozmiaru kafelka.":::

1. Wybierz kafelek i przeciągnij go do nowej lokalizacji na siatce, aby rozmieścić pulpit nawigacyjny.

### <a name="additional-tile-configuration"></a>Dodatkowa konfiguracja kafelka

Niektóre kafelki mogą wymagać większej liczby konfiguracji w celu pokazania wymaganych informacji. Na przykład **kafelek Wykres metryk** musi być ustawiony w celu wyświetlania metryki z Azure Monitor. Możesz również dostosować dane kafelków, aby zastąpić domyślne ustawienia czasu pulpitu nawigacyjnego.

Każdy kafelek, który należy skonfigurować, wyświetla transparent do momentu dostosowania kafelka. W przypadku **wykresu Metryki** transparent to Edit in Metrics (Edytuj **w metrykach).** Aby dostosować kafelek:

1. W nagłówku strony wybierz pozycję **Zapisz,** aby wyjść z trybu edycji.

1. Wybierz baner, a następnie wykonaj wymaganą konfigurację.

    ![Zrzut ekranu przedstawiający kafelek, który wymaga konfiguracji](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Kafelek markdown umożliwia wyświetlanie niestandardowej, statycznej zawartości na pulpicie nawigacyjnym. Mogą to być podstawowe instrukcje, obraz, zestaw hiperlinków, a nawet informacje kontaktowe. Aby uzyskać więcej informacji na temat używania kafelka markdown, zobacz Używanie kafelka markdown na pulpitach nawigacyjnych platformy [Azure do pokazywania zawartości niestandardowej.](azure-portal-markdown-tile.md)

### <a name="customize-tile-data"></a>Dostosowywanie danych kafelka

Dane na pulpicie nawigacyjnym automatycznie pokazują aktywność z ostatnich 24 godzin. Aby wyświetlić inny zakres czasu tylko dla tego kafelka, wykonaj następujące kroki:

1. Wybierz **pozycję Dostosuj dane kafelka** z menu kontekstowego lub z filtru ikony ![ ](./media/azure-portal-dashboards/dashboard-filter.png) filtru w lewym górnym rogu kafelka.

    ![Zrzut ekranu przedstawiający menu kontekstowe kafelka.](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Zaznacz pole wyboru, aby **zastąpić ustawienia czasu pulpitu nawigacyjnego na poziomie kafelka**.

    ![Zrzut ekranu przedstawiający okno dialogowe służące do konfigurowania ustawień czasu kafelka.](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Wybierz zakres czasu do pokazania dla tego kafelka. Możesz wybrać zakres od ostatnich 30 minut do ostatnich 30 dni lub zdefiniować zakres niestandardowy.

1. Wybierz poziom szczegółowości czasu do wyświetlenia. Możesz wyświetlić dowolne miejsce od jednominutowych przyrostów do jednego miesiąca.

1. Wybierz przycisk **Zastosuj**.

## <a name="delete-a-tile"></a>Usuwanie kafelka

Aby usunąć kafelek z pulpitu nawigacyjnego, wykonaj jedną z następujących czynności:

- Wybierz menu kontekstowe w prawym górnym rogu kafelka, a następnie wybierz **pozycję Usuń z pulpitu nawigacyjnego.**

- Wybierz ![ ikonę edycji ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Edytuj,** aby wprowadzić tryb dostosowywania. Umieść kursor w prawym górnym rogu kafelka, a następnie wybierz ikonę usuwania ikona usuwania, ![ ](./media/azure-portal-dashboards/dashboard-delete-icon.png) aby usunąć kafelek z pulpitu nawigacyjnego.

   ![Zrzut ekranu przedstawiający sposób usuwania kafelka z pulpitu nawigacyjnego.](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Klonowanie pulpitu nawigacyjnego

Aby użyć istniejącego pulpitu nawigacyjnego jako szablonu nowego pulpitu nawigacyjnego, wykonaj następujące kroki:

1. Upewnij się, że w widoku pulpitu nawigacyjnego jest pokazywany pulpit nawigacyjny, który chcesz skopiować.

1. W nagłówku strony wybierz ![ ikonę klonowania ](./media/azure-portal-dashboards/dashboard-clone.png) **Klonuj**.

1. Kopia pulpitu nawigacyjnego o nazwie **Klon nazwy** *pulpitu nawigacyjnego zostanie* otwarta w trybie edycji. Aby zmienić nazwę pulpitu nawigacyjnego i dostosować go, skorzystaj z powyższych kroków w tym artykule.

## <a name="publish-and-share-a-dashboard"></a>Publikowanie i udostępnianie pulpitu nawigacyjnego

Po utworzeniu pulpitu nawigacyjnego jest on domyślnie prywatny, co oznacza, że jesteś jedynym, który go widzi. Aby udostępnić pulpity nawigacyjne innym osobom, możesz je publikować i udostępniać. Aby uzyskać więcej informacji, zobacz [Share Azure dashboards by using Azure role-based access control](azure-portal-dashboard-share-access.md)(Udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu kontroli dostępu opartej na rolach platformy Azure).

### <a name="open-a-shared-dashboard"></a>Otwieranie udostępnionego pulpitu nawigacyjnego

Aby znaleźć i otworzyć udostępniony pulpit nawigacyjny, wykonaj następujące kroki:

1. Wybierz strzałkę obok nazwy pulpitu nawigacyjnego.

1. Wybierz pozycję z wyświetlonej listy pulpitów nawigacyjnych. Jeśli pulpitu nawigacyjnego, który chcesz otworzyć, nie ma na liście:

    1. Wybierz **pozycję Przeglądaj wszystkie pulpity nawigacyjne.**

        ![Zrzut ekranu przedstawiający menu wyboru pulpitu nawigacyjnego](./media/azure-portal-dashboards/dashboard-browse.png)

    1. W polu **Typ** wybierz pozycję **Udostępnione pulpity nawigacyjne.**

        ![Zrzut ekranu przedstawiający menu wyboru wszystkich pulpitów nawigacyjnych](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Wybierz co najmniej jedną subskrypcję. Możesz również wprowadzić tekst, aby filtrować pulpity nawigacyjne według nazwy.

    1. Wybierz pulpit nawigacyjny z listy udostępnionych pulpitów nawigacyjnych.

## <a name="delete-a-dashboard"></a>Usuwanie pulpitu nawigacyjnego

Aby trwale usunąć prywatny lub udostępniony pulpit nawigacyjny, wykonaj następujące kroki:

1. Wybierz pulpit nawigacyjny, który chcesz usunąć, z listy obok nazwy pulpitu nawigacyjnego.

1. Wybierz ![ ikonę usuń ](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Usuń** z nagłówka strony.

1. W przypadku prywatnego pulpitu nawigacyjnego wybierz **przycisk OK** w oknie dialogowym potwierdzenia, aby usunąć pulpit nawigacyjny. W przypadku udostępnionego pulpitu nawigacyjnego w oknie dialogowym potwierdzenia zaznacz pole wyboru, aby potwierdzić, że opublikowany pulpit nawigacyjny nie będzie już można wyświetlić dla innych osób. Następnie wybierz przycisk **OK**.

    ![Zrzut ekranu przedstawiający potwierdzenie usunięcia.](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="recover-a-deleted-dashboard"></a>Odzyskiwanie usuniętego pulpitu nawigacyjnego

Jeśli jesteś w globalnej chmurze platformy  Azure i usuniesz opublikowany pulpit nawigacyjny w usłudze Azure Portal, możesz odzyskać ten pulpit nawigacyjny w ciągu 14 dni od usunięcia. Aby uzyskać więcej informacji, zobacz [Odzyskiwanie usuniętego pulpitu nawigacyjnego w Azure Portal](recover-shared-deleted-dashboard.md).

## <a name="next-steps"></a>Następne kroki

- [Udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu kontroli dostępu opartej na rolach platformy Azure](azure-portal-dashboard-share-access.md)
- [Programowe tworzenie pulpitów nawigacyjnych platformy Azure](azure-portal-dashboards-create-programmatically.md)
