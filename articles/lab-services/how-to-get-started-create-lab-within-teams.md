---
title: Rozpocznij pracę i Utwórz laboratorium Azure Lab Services w zespołach
description: Dowiedz się, jak rozpocząć pracę i utworzyć laboratorium Azure Lab Services w ramach zespołów.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: cc4ad604bdf250cc6e4ba2c50c2f7143c921e906
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433975"
---
# <a name="get-started-and-create-a-lab-services-lab-within-teams"></a>Rozpocznij pracę i Utwórz laboratorium usług Lab Services w zespołach

W tym artykule pokazano, jak dodać aplikację **Azure Lab Services** do zespołu, a następnie jak utworzyć laboratorium w środowisku MS Teams.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przedstawiono Konfigurowanie laboratorium z maszynami wirtualnymi dla zespołu. Aby skonfigurować laboratorium na koncie laboratorium, musisz być członkiem jednej z tych ról na koncie laboratorium: właściciel, twórca laboratorium lub współautor. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do roli właściciela. Aby utworzyć laboratorium, możesz użyć konta użytkownika, które zostało użyte do utworzenia konta laboratorium.

Poniżej przedstawiono typowy przepływ pracy w przypadku używania Azure Lab Services w zespołach

1. Użytkownik [tworzy konto laboratorium](tutorial-setup-lab-account.md#create-a-lab-account) na Azure Portal.
1. [Twórca konta laboratorium dodaje innych użytkowników](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) do roli **twórcy laboratorium** . Na przykład twórca konta laboratorium/administrator dodaje wykładowców do roli **twórca laboratorium** , aby mogli tworzyć laboratoria dla ich klas.
1. Następnie usługa wykładowcy tworzy laboratoria, wstępnie konfiguruje szablonową maszynę wirtualną i publikuje laboratorium, aby udostępnić maszynę wirtualną wszystkim członkom zespołu.
1. Po opublikowaniu laboratorium maszyna wirtualna jest przypisana do wszystkich użytkowników na liście członkostwa zespołu przy pierwszym logowaniu do Azure Lab Services, klikając kartę zawierającą aplikację **Azure Lab Services** w programie Teams (SSO) lub uzyskując dostęp do [witryny sieci Web Labs](https://labs.azure.com). Użytkownicy mogą następnie użyć maszyny wirtualnej w celu wykonania klasy i pracy.

> [!IMPORTANT]
> Azure Lab Services mogą być używane w obrębie zespołów tylko wtedy, gdy konta laboratorium są tworzone w tej samej dzierżawie co zespoły.

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>Dodawanie aplikacji Azure Lab Services jako karty do zespołu

Właściciel zespołu może dodawać **Azure Lab Services** aplikację bezpośrednio do kanałów zespołów, a następnie aplikacja jest dostępna dla wszystkich członków zespołu. Wykonaj poniższe trzy kroki:

1. Przejdź do kanału Teams, w którym chcesz dodać aplikację, a następnie wybierz pozycję, **+** Aby dodać kartę. 
1. Wyszukaj **Azure Lab Services** z opcji kart i Dodaj tę aplikację. 

    > [!NOTE]
    > Tylko **właściciele** zespołu będą mogli tworzyć laboratoria dla zespołu.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/add.png" alt-text="Dodaj kartę":::
1. Wybierz konto usługi laboratoryjnej, którego chcesz użyć do tworzenia laboratoriów w tym zespole. 

    Azure Lab Services korzysta z logowania jednokrotnego w [witrynie sieci web Azure Lab Services](https://labs.azure.com) i pobiera wszystkie konta laboratorium, do których masz dostęp. 

    Są wyświetlane konta znajdujące się w tej samej dzierżawie co zespoły i dla których masz uprawnienia **właściciela**, **współautora** lub **twórcy** . 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/welcome.png" alt-text="Czujnik ALS — Zapraszamy!":::
1. Naciśnij przycisk **Zapisz** , a karta zostanie dodana do kanału.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/created.png" alt-text="Utworzono kartę ALS":::

    Teraz możesz wybrać kartę **Azure Lab Services** z kanału i rozpocząć Zarządzanie laboratoriami zgodnie z opisem w poniższych artykułach.

Po wybraniu konta laboratorium właściciele zespołu będą mogli tworzyć laboratoria dla zespołu. Cały proces tworzenia laboratorium i wszystkie zadania na poziomie laboratorium mogą być wykonywane w ramach zespołów. Użytkownicy będą mogli utworzyć wiele laboratoriów w ramach tego samego zespołu i właściciela zespołu, z odpowiednim dostępem na poziomie konta laboratorium, zobaczy tylko laboratoria skojarzone z konkretnym zespołem.

## <a name="next-steps"></a>Następne kroki

Gdy laboratorium jest tworzone w zespołach, lista użytkowników laboratorium jest automatycznie wypełniana i synchronizowana z członkostwem zespołu. Wszyscy użytkownicy zespołu, w tym właściciele, członkowie i Goście, zostaną automatycznie dodani do listy użytkowników laboratorium. Usługi Azure Lab Services będą obsługiwać synchronizację z członkostwem zespołu, a automatyczna synchronizacja jest wyzwalana co 24 godziny. Aby uzyskać szczegółowe informacje, zobacz:

[Zarządzanie listami użytkowników usług Lab Services w ramach zespołów](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Zobacz także

Zobacz również następujące artykuły:

- [Korzystanie z Azure Lab Services w programie Teams — Omówienie](lab-services-within-teams-overview.md)
- [Zarządzaj pulą maszyn wirtualnych laboratorium w zespołach](how-to-manage-vm-pool-within-teams.md)
- [Tworzenie harmonogramów laboratorium w zespołach i zarządzanie nimi](how-to-create-schedules-within-teams.md)
- [Dostęp do maszyny wirtualnej w programie Teams — widok ucznia](how-to-access-vm-for-students-within-teams.md)
- [Usuwanie laboratoriów w zespołach](how-to-delete-lab-within-teams.md)
