---
title: Zarządzanie grupami aplikacji dla systemu Windows Virtual Desktop Portal — Azure
description: Jak zarządzać grupami aplikacji klasycznymi systemu Windows przy użyciu Azure Portal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f072ed8a758173645c886cabf0b20f9e123cbbab
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612182"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>Samouczek: Zarządzanie grupami aplikacji przy użyciu Azure Portal

>[!IMPORTANT]
>Ta zawartość ma zastosowanie do aktualizacji wiosennej 2020 z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows, wykorzystaj wersję 2019 bez obiektów Azure Resource Manager, zobacz [ten artykuł](./virtual-desktop-fall-2019/manage-app-groups-2019.md).
>
> Aktualizacja systemu Windows Virtual Desktop wiosna 2020 jest obecnie dostępna w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. 
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Domyślna grupa aplikacji utworzona dla nowej puli hostów systemu Windows Virtual Desktop również publikuje pełny pulpit. Ponadto można utworzyć co najmniej jedną grupę aplikacji RemoteApp dla puli hostów. Postępuj zgodnie z tym samouczkiem, aby utworzyć grupę aplikacji RemoteApp i opublikować poszczególne aplikacje menu Start.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz grupę usługi RemoteApp.
> * Udzielanie dostępu do programów RemoteApp.

## <a name="create-a-remoteapp-group"></a>Tworzenie grupy usługi RemoteApp

Jeśli utworzono już pulę hostów i maszyny wirtualne hosta sesji przy użyciu Azure Portal lub programu PowerShell, można dodać grupy aplikacji z Azure Portal za pomocą następującego procesu:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2.  Wyszukaj i wybierz pozycję **pulpit wirtualny systemu Windows**.

3.  W menu po lewej stronie wybierz pozycję **grupy aplikacji** , a następnie wybierz pozycję **+ Dodaj**.

4. Na karcie **podstawowe** wybierz grupę subskrypcji i grupę zasobów, dla której chcesz utworzyć grupę aplikacji. Możesz również utworzyć nową grupę zasobów zamiast wybierać istniejącą.

5. Wybierz pulę hostów, która zostanie skojarzona z grupą aplikacji z menu rozwijanego obok **puli hostów**.

    >[!NOTE]
    >Musisz wybrać pulę hostów skojarzoną z grupą aplikacji. Grupy aplikacji mają aplikacje lub pulpity, które są obsługiwane przez Host sesji i hosty sesji, są częścią pul hostów. Grupa aplikacji musi być skojarzona z pulą hostów podczas tworzenia.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający kartę podstawowe w Azure Portal.](media/basics-tab.png)

6. Jeśli chcesz dodać grupy aplikacji do puli hostów, wybierz pozycję **Pule hostów** w menu po lewej stronie ekranu.
   
    Następnie wybierz nazwę puli hostów, do której chcesz dodać grupy aplikacji.
   
    Po wybraniu tej opcji wybierz pozycję **grupy aplikacji** z menu po lewej stronie ekranu, a następnie wybierz pozycję **+ Dodaj**.

    Na koniec wybierz grupę subskrypcji i grupę zasobów, w której chcesz utworzyć grupę aplikacji. Możesz wybrać nazwę istniejącej grupy zasobów z menu rozwijanego lub wybrać pozycję **Utwórz nową** , aby utworzyć nową.

      >[!NOTE]
      >Po dodaniu grup aplikacji do puli hostów Pula hostów, która jest powiązana z grupą aplikacji, jest już zaznaczona, ponieważ przejdziesz od niej.
      > 
      > [!div class="mx-imgBorder"]
      >![Zrzut ekranu przedstawiający kartę podstawowe z wybraną pulą hostów.](media/host-pool-selected.png)

7. Wybierz pozycję **RemoteApp** w obszarze Typ grupy aplikacji, a następnie wprowadź nazwę dla usługi RemoteApp.

      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu przedstawiający pola typu grupy aplikacji. Wyróżniono "RemoteApp".](media/remoteapp-button.png)

8.  Wybierz kartę **przypisania** .

9.  Aby opublikować poszczególnych użytkowników lub grupy użytkowników w grupie aplikacji, wybierz pozycję **+ Dodaj użytkowników usługi Azure AD lub grupy użytkowników**.

10.  Wybierz liczbę użytkowników, do których chcesz dodać aplikacje. Można wybrać jednego lub wielu użytkowników i grup użytkowników.

     > [!div class="mx-imgBorder"]
     > ![Zrzut ekranu przedstawiający menu wyboru użytkownika.](media/select-users.png)

11.  Wybierz przycisk **Wybierz**.

12.  Wybierz kartę **aplikacje** , a następnie wybierz pozycję **+ Dodaj aplikacje**.

13.  Aby dodać aplikację z menu Start: 

      - Przejdź do pozycji **Źródło aplikacji** i wybierz **menu Start** z menu rozwijanego. Następnie przejdź do **aplikacji** i wybierz aplikację z menu rozwijanego.

     > [!div class="mx-imgBorder"]
     > ![Zrzut ekranu przedstawiający ekran Dodawanie aplikacji z wybranym menu Start.](media/add-app-start.png)

      - W polu **Nazwa wyświetlana**wprowadź nazwę aplikacji, która będzie wyświetlana użytkownikowi na swoim kliencie.

      - Pozostaw inne opcje jako-is i wybierz pozycję **Zapisz**.

14. Aby dodać aplikację z określonej ścieżki pliku:

      - Przejdź do pozycji **Źródło aplikacji** i wybierz pozycję **ścieżka pliku** z menu rozwijanego.

      - Wprowadź ścieżkę do aplikacji na hoście sesji zarejestrowanej za pomocą skojarzonej puli hostów.

      - Wprowadź szczegóły aplikacji w polach **Nazwa aplikacji**, **Nazwa wyświetlana**, **ścieżka ikony**i **indeks ikony** .

      - Wybierz pozycję **Zapisz**.

     > [!div class="mx-imgBorder"]
     > ![Zrzut ekranu przedstawiający stronę Dodawanie aplikacji z wybraną ścieżką pliku.](media/add-app-file.png)

     Powtórz ten proces dla każdej aplikacji, którą chcesz dodać do grupy aplikacji.

15.  Następnie wybierz kartę **obszar roboczy** .

16.  Jeśli chcesz zarejestrować grupę aplikacji w obszarze roboczym, przejdź do pozycji **zarejestruj grupę aplikacji** i wybierz opcję **tak**. Jeśli wolisz zarejestrować grupę aplikacji w późniejszym czasie, wybierz pozycję **nie**.

17.  Jeśli wybierzesz opcję **tak**, możesz wybrać istniejący obszar roboczy, aby zarejestrować grupę aplikacji.
       
       >[!NOTE]
       >Grupę aplikacji można zarejestrować tylko w obszarach roboczych utworzonych w tej samej lokalizacji, w której znajduje się pula hostów. Być. Jeśli w obszarze roboczym została wcześniej zarejestrowana inna grupa aplikacji z tej samej puli, co Nowa grupa aplikacji, zostanie ona wybrana i nie będzie można jej edytować. Wszystkie grupy aplikacji z puli hostów muszą być zarejestrowane w tym samym obszarze roboczym.

     > [!div class="mx-imgBorder"]
     > ![Zrzut ekranu przedstawiający stronę Zarejestruj grupę aplikacji dla już istniejącego obszaru roboczego. Pula hostów jest wybierana jako prewybrana.](media/register-existing.png)

18. Opcjonalnie, jeśli chcesz utworzyć Tagi, aby ułatwić organizowanie obszaru roboczego, wybierz kartę **Tagi** i wprowadź nazwy tagów.

19. Gdy skończysz, wybierz kartę **Recenzja + tworzenie** .

20. Poczekaj na zakończenie procesu walidacji. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz** , aby wdrożyć grupę aplikacji.

Proces wdrażania wykona następujące czynności:

- Utwórz grupę aplikacji usługi RemoteApp.
- Dodaj wybrane aplikacje do grupy aplikacji.
- Opublikuj grupę aplikacji opublikowaną dla wybranych użytkowników i grup użytkowników.
- Zarejestruj grupę aplikacji, jeśli wybrano tę opcję.
- Utwórz link do szablonu Azure Resource Manager w oparciu o konfigurację, którą możesz pobrać i zapisać na później.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia grupy aplikacji, wypełniania jej przy użyciu programów RemoteApp i przypisywania użytkowników do grupy aplikacji. Aby dowiedzieć się, jak utworzyć pulę hostów weryfikacji, zobacz poniższy samouczek. Można użyć puli hostów weryfikacji do monitorowania aktualizacji usługi przed ich wycofaniem do środowiska produkcyjnego.

> [!div class="nextstepaction"]
> [Tworzenie puli hostów na potrzeby weryfikacji aktualizacji usług](./create-validation-host-pool.md)
