---
title: Zarządzanie grupami aplikacji dla systemu Windows Virtual Desktop Portal — Azure
description: Jak zarządzać grupami aplikacji klasycznymi systemu Windows przy użyciu Azure Portal.
author: Heidilohr
ms.topic: tutorial
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ec26f021ffa581b0713973904c97349df83a08ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91930275"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>Samouczek: Zarządzanie grupami aplikacji przy użyciu Azure Portal

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

Domyślna grupa aplikacji utworzona dla nowej puli hostów systemu Windows Virtual Desktop również publikuje pełny pulpit. Ponadto można utworzyć co najmniej jedną grupę aplikacji RemoteApp dla puli hostów. Postępuj zgodnie z tym samouczkiem, aby utworzyć grupę aplikacji RemoteApp i opublikować poszczególne aplikacje menu Start.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz grupę usługi RemoteApp.
> * Udzielanie dostępu do programów RemoteApp.

## <a name="create-a-remoteapp-group"></a>Tworzenie grupy usługi RemoteApp

Jeśli utworzono już pulę hostów i maszyny wirtualne hosta sesji przy użyciu Azure Portal lub programu PowerShell, można dodać grupy aplikacji z Azure Portal za pomocą następującego procesu:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
   
    >[!NOTE]
    > Jeśli logujesz się do portalu US Gov, przejdź do [https://portal.azure.us/](https://portal.azure.us/) zamiast tego.

2.  Wyszukaj i wybierz pozycję **pulpit wirtualny systemu Windows**.

3. Można dodać grupę aplikacji bezpośrednio lub dodać ją z istniejącej puli hostów. Wybierz opcję poniżej:

    - W menu po lewej stronie wybierz pozycję **grupy aplikacji** , a następnie wybierz pozycję **+ Dodaj**.

    - Wybierz pozycję **Pule hostów** w menu po lewej stronie ekranu, wybierz nazwę puli hostów, wybierz pozycję **grupy aplikacji** z menu po lewej stronie, a następnie wybierz pozycję **+ Dodaj**. W takim przypadku Pula hostów zostanie już wybrana na karcie podstawowe.

4. Na karcie **podstawowe** wybierz **subskrypcję** i **grupę zasobów** , dla których chcesz utworzyć grupę aplikacji. Możesz również utworzyć nową grupę zasobów zamiast wybierać istniejącą.

5. Wybierz **pulę hostów** , która zostanie skojarzona z grupą aplikacji z menu rozwijanego.

    >[!NOTE]
    >Musisz wybrać pulę hostów skojarzoną z grupą aplikacji. Grupy aplikacji mają aplikacje lub pulpity, które są obsługiwane przez Host sesji i hosty sesji, są częścią pul hostów. Grupa aplikacji musi być skojarzona z pulą hostów podczas tworzenia.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający kartę podstawowe w Azure Portal.](media/basics-tab.png)

6. Wybierz pozycję **RemoteApp** w obszarze **Typ grupy aplikacji**, a następnie wprowadź nazwę dla usługi RemoteApp.

      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu przedstawiający pola typu grupy aplikacji. Wyróżniono "RemoteApp".](media/remoteapp-button.png)

7.  Wybierz kolejno pozycje **Dalej: przypisania >** karcie.

8.  Aby przypisać poszczególnych użytkowników lub grupy użytkowników do grupy aplikacji, wybierz pozycję **+ Dodaj użytkowników usługi Azure AD lub grupy użytkowników**.

9.  Wybierz użytkowników, którym chcesz mieć dostęp do aplikacji. Można wybrać jednego lub wielu użytkowników i grup użytkowników.

     > [!div class="mx-imgBorder"]
     > ![Zrzut ekranu przedstawiający menu wyboru użytkownika.](media/select-users.png)

10.  Wybierz pozycję **Wybierz**.

11.  Wybierz pozycję **Dalej: aplikacje >**, a następnie wybierz pozycję **+ Dodaj aplikacje**.

12.  Aby dodać aplikację z menu Start:

      - W obszarze **Źródło aplikacji** wybierz pozycję **menu Start** z menu rozwijanego. Następnie w obszarze **aplikacja** wybierz aplikację z menu rozwijanego.

     > [!div class="mx-imgBorder"]
     > ![Zrzut ekranu przedstawiający ekran Dodawanie aplikacji z wybranym menu Start.](media/add-app-start.png)

      - W polu **Nazwa wyświetlana** wprowadź nazwę aplikacji, która będzie wyświetlana użytkownikowi na swoim kliencie.

      - Pozostaw inne opcje jako-is i wybierz pozycję **Zapisz**.

13.  Aby dodać aplikację z określonej ścieżki pliku:

      - W obszarze **Źródło aplikacji** wybierz pozycję **ścieżka pliku** z menu rozwijanego.

      - W polu **ścieżka aplikacji** wprowadź ścieżkę do aplikacji na hoście sesji zarejestrowanym w skojarzonej puli hostów.

      - Wprowadź szczegóły aplikacji w polach **Nazwa aplikacji**, **Nazwa wyświetlana**, **ścieżka ikony** i **indeks ikony** .

      - Wybierz pozycję **Zapisz**.

     > [!div class="mx-imgBorder"]
     > ![Zrzut ekranu przedstawiający stronę Dodawanie aplikacji z wybraną ścieżką pliku.](media/add-app-file.png)

14.  Powtórz ten proces dla każdej aplikacji, którą chcesz dodać do grupy aplikacji.

15.  Następnie wybierz pozycję **Dalej: obszar roboczy >**.

16.  Jeśli chcesz zarejestrować grupę aplikacji w obszarze roboczym, wybierz pozycję **tak** dla opcji **zarejestruj grupę aplikacji**. Jeśli wolisz zarejestrować grupę aplikacji w późniejszym czasie, wybierz pozycję **nie**.

17.  Jeśli wybierzesz opcję **tak**, możesz wybrać istniejący obszar roboczy, aby zarejestrować grupę aplikacji.

       >[!NOTE]
       >Grupę aplikacji można zarejestrować tylko w obszarach roboczych utworzonych w tej samej lokalizacji, w której znajduje się pula hostów. Być. Jeśli w obszarze roboczym została wcześniej zarejestrowana inna grupa aplikacji z tej samej puli, co Nowa grupa aplikacji, zostanie ona wybrana i nie będzie można jej edytować. Wszystkie grupy aplikacji z puli hostów muszą być zarejestrowane w tym samym obszarze roboczym.

     > [!div class="mx-imgBorder"]
     > ![Zrzut ekranu przedstawiający stronę Zarejestruj grupę aplikacji dla już istniejącego obszaru roboczego. Pula hostów jest wybierana jako prewybrana.](media/register-existing.png)

18.  Opcjonalnie, jeśli chcesz utworzyć Tagi, aby ułatwić organizowanie obszaru roboczego, wybierz pozycję **Dalej: tagi >** i wprowadź nazwy tagów.

19.  Gdy skończysz, wybierz pozycję **Przegląd + Utwórz**.

20.  Poczekaj na zakończenie procesu walidacji. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz** , aby wdrożyć grupę aplikacji.

Proces wdrażania wykona następujące czynności:

- Utwórz grupę aplikacji usługi RemoteApp.
- Dodaj wybrane aplikacje do grupy aplikacji.
- Opublikuj grupę aplikacji opublikowaną dla wybranych użytkowników i grup użytkowników.
- Zarejestruj grupę aplikacji, jeśli wybrano tę opcję.
- Utwórz link do szablonu Azure Resource Manager w oparciu o konfigurację, którą możesz pobrać i zapisać na później.

>[!IMPORTANT]
>Dla każdej dzierżawy Azure Active Directory można utworzyć tylko grupy aplikacji 200. Dodaliśmy ten limit z powodu ograniczeń usługi do pobierania źródeł danych dla naszych użytkowników. Ten limit nie ma zastosowania do grup aplikacji utworzonych na pulpicie wirtualnym systemu Windows (klasyczny).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia grupy aplikacji, wypełniania jej przy użyciu programów RemoteApp i przypisywania użytkowników do grupy aplikacji. Aby dowiedzieć się, jak utworzyć pulę hostów weryfikacji, zobacz poniższy samouczek. Można użyć puli hostów weryfikacji do monitorowania aktualizacji usługi przed ich wycofaniem do środowiska produkcyjnego.

> [!div class="nextstepaction"]
> [Tworzenie puli hostów na potrzeby weryfikacji aktualizacji usług](./create-validation-host-pool.md)
