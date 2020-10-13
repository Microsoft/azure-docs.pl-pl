---
title: Rozpocznij pracę i Utwórz laboratorium Azure Lab Services z zespołów
description: Dowiedz się, jak rozpocząć pracę i utworzyć laboratorium Azure Lab Services z zespołów.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: b585196fe61a09697cfa203aaa33f08afae2b427
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946739"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>Rozpocznij pracę i Utwórz laboratorium usług Lab Services dla zespołów

W tym artykule pokazano, jak dodać aplikację Azure Lab Services do zespołów. Następnie, jak utworzyć laboratorium z zespołów.

## <a name="add-a-lab-services-app-to-teams"></a>Dodawanie aplikacji usług Lab Services do zespołów

Usługi Lab można dodawać bezpośrednio do kanałów zespołów, a następnie być dostępne dla wszystkich członków zespołu. Wykonaj następujące trzy kroki:

1. Przejdź do kanału Teams, w którym chcesz dodać aplikację, a następnie wybierz pozycję, **+** Aby dodać kartę, klikając pozycję "...". w górnej części okienka po prawej stronie. 
1. Wyszukaj **Azure Lab Services** z opcji kart i Dodaj tę aplikację. 

    > [!NOTE]
    > Tylko **właściciele** zespołu będą mogli tworzyć laboratoria dla zespołu.
1. Wybierz konto usługi laboratoryjnej, którego chcesz użyć do tworzenia laboratoriów zajęć w tym zespole. 

    Azure Lab Services korzysta z logowania jednokrotnego w [witrynie sieci web Azure Lab Services](https://labs.azure.com) i pobiera wszystkie konta laboratorium, do których masz dostęp. 

    Są wyświetlane konta znajdujące się w tej samej dzierżawie co zespoły i dla których masz uprawnienia **właściciela**, **współautora**lub **twórcy** . 

   ![Czujnik ALS — Zapraszamy!](./media/integrate-with-teams/welcome.png) 
1. Naciśnij przycisk **Zapisz** , a aplikacja zostanie dodana do zespołów, a karta zostanie dodana do kanału. 

    Teraz możesz wybrać kartę **Azure Lab Services** z kanału i rozpocząć Zarządzanie laboratoriami zgodnie z opisem w poniższym kroku.

    Gdy jeden z członków zespołu doda kartę, zostanie ona wyświetlona dla wszystkich użytkowników w kanale. Wszyscy użytkownicy, którzy mają dostęp do aplikacji, uzyskują dostęp do logowania jednokrotnego przy użyciu poświadczeń używanych przez program Microsoft Teams. Wszyscy użytkownicy, którzy nie mają dostępu do aplikacji, mogą widzieć kartę w zespołach, ale są Zablokowani do momentu udzielenia im uprawnień do aplikacji lokalnej i Azure Portal opublikowanej wersji aplikacji.

## <a name="create-a-classroom-lab"></a>Tworzenie laboratorium na potrzeby zajęć

Po wybraniu konta laboratorium właściciele zespołu będą mogli tworzyć laboratoria dla zespołu. Cały proces tworzenia laboratorium i wszystkie zadania na poziomie laboratorium mogą być wykonywane w ramach zespołów. Użytkownicy będą mogli utworzyć wiele laboratoriów w ramach tego samego zespołu i właściciela zespołu, z odpowiednim dostępem na poziomie konta laboratorium, zobaczy tylko laboratoria skojarzone z konkretnym zespołem.

## <a name="giving-access-to-users-of-the-lab-account"></a>Udzielanie dostępu użytkownikom konta laboratorium

Udostępnienie użytkownikom na poziomie konta laboratorium musi się zdarzyć w witrynie [Azure](https://ms.portal.azure.com/) Portal.

1. W Azure Portal przejdź do swojego konta Azure Lab Services. 
1. Na stronie **Konto laboratorium** wybierz pozycję **Kontrola dostępu (IAM)**, wybierz pozycję **+ Dodaj** na pasku narzędzi, a następnie wybierz pozycję **+ Dodaj przypisanie roli** na pasku narzędzi. 

    ![Kontrola dostępu -> przycisk Dodaj przypisanie roli](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na stronie **Dodawanie przypisania roli** wybierz pozycję **Twórca laboratorium** w obszarze **Rola**, wybierz użytkownika do dodania do roli Twórcy laboratorium, a następnie wybierz pozycję **Zapisz**. 

    ![Dodawanie twórcy laboratorium](./media/tutorial-setup-lab-account/add-lab-creator.png)

### <a name="creating-classroom-labs"></a>Tworzenie laboratoriów zajęć

Proces tworzenia w ramach platformy zajęć jest taki sam niezależnie od tego, czy tworzysz laboratoria z zespołów lub [witryny sieci Web usług Lab Services](https://labs.azure.com). 

Aby uzyskać szczegółowe informacje na temat konfigurowania tego artykułu, proces tworzenia laboratorium: [Zarządzanie laboratoriami klas w Azure Lab Services](how-to-manage-classroom-labs.md).

## <a name="deleting-classroom-labs"></a>Usuwanie laboratoriów zajęć

Laboratorium utworzone w zespołach można usunąć w [witrynie sieci Web usług Lab Services](https://labs.azure.com) , usuwając laboratorium bezpośrednio, zgodnie z opisem w temacie [Zarządzanie laboratoriami klas w Azure Lab Services](how-to-manage-classroom-labs.md). 

Usuwanie laboratorium jest również wyzwalane, gdy zespół zostanie usunięty. W przypadku usunięcia zespołu, w którym laboratorium zostanie utworzone, laboratorium zostanie automatycznie usunięte po 24 godzinach od momentu wyzwolenia automatycznej synchronizacji listy użytkowników. 

Usunięcie karty lub odinstalowanie aplikacji nie spowoduje usunięcia laboratorium. Jeśli karta zostanie usunięta, użytkownicy na liście członkostwa zespołu nadal będą mogli uzyskiwać dostęp do maszyn wirtualnych w [witrynie sieci Web usług laboratoryjnych](https://labs.azure.com) , chyba że usunięcie laboratorium zostanie jawnie wyzwolone przez usunięcie laboratorium w witrynie sieci Web lub usunięcie zespołu. 

## <a name="next-steps"></a>Następne kroki

Gdy laboratorium jest tworzone w zespołach, lista użytkowników laboratorium jest automatycznie wypełniana i synchronizowana z członkostwem zespołu. Wszyscy użytkownicy zespołu, w tym właściciele, członkowie i Goście, zostaną automatycznie dodani do listy użytkowników laboratorium. Usługi Azure Lab Services będą obsługiwać synchronizację z członkostwem zespołu, a automatyczna synchronizacja jest wyzwalana co 24 godziny. Aby uzyskać szczegółowe informacje, zobacz:

[Zarządzanie listami użytkowników usług Lab Services z zespołów](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Zobacz też

Zobacz również następujące artykuły:

- [Korzystanie z Azure Lab Services w programie Teams — Omówienie](lab-services-within-teams-overview.md)
- [Zarządzanie pulą maszyn wirtualnych w usługach Lab Services z zespołów](how-to-manage-vm-pool-within-teams.md)
- [Tworzenie harmonogramów usług Lab dla zespołów](how-to-create-schedules-within-teams.md)
- [Dostęp do maszyny wirtualnej (widoku ucznia) w usługach laboratoryjnych z zespołów](how-to-access-vm-for-students-within-teams.md)

