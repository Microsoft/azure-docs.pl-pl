---
title: Zaloguj się do wersji zapoznawczej Azure Notebooks
description: Szybko zaloguj się do Azure Notebooks wersja zapoznawcza i ustaw identyfikator użytkownika, który umożliwia dostęp do zapisanych projektów i udostępnianie notesów innym osobom.
ms.topic: quickstart
ms.date: 04/15/2019
ms.openlocfilehash: 168da5d4d36cd4cfadb4ef120ddfabbd261cafaf
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109457"
---
# <a name="quickstart-sign-in-and-set-a-user-id-for-azure-notebooks-preview"></a>Szybki Start: Logowanie i Ustawianie identyfikatora użytkownika na potrzeby wersji zapoznawczej Azure Notebooks

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Mimo że zawsze możesz wyświetlać Azure Notebooks bez logowania, musisz zalogować się, aby uruchomić notesy, uzyskać dostęp do zapisanych projektów i notesów oraz udostępnić Notes innym osobom.

## <a name="sign-in"></a>Zaloguj

1. Wybierz pozycję **Zaloguj się** w prawym górnym rogu [Notebooks.Azure.com](https://notebooks.azure.com/).

    ![Lokalizacja polecenia logowania na Azure Notebooks](media/accounts/sign-in-command.png)

1. Po wyświetleniu monitu wprowadź adres e-mail konta Microsoft lub konta służbowego, a następnie wybierz pozycję **dalej**. Typy kont są opisane na [koncie użytkownika na potrzeby Azure Notebooks](azure-notebooks-user-account.md). Jeśli nie masz konta Microsoft lub chcesz, aby było ono używane w odniesieniu do Azure Notebooks, wybierz pozycję **Utwórz jeden**:

    ![Utwórz nowe polecenie konto Microsoft w monicie logowania](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > Jeśli spróbujesz utworzyć nowe konto przy użyciu adresu e-mail, z którym jest już skojarzone konto, może zostać wyświetlony komunikat "nie można utworzyć konta w tym miejscu przy użyciu służbowego adresu e-mail. Użyj osobistej poczty e-mail, takiej jak Gmail lub Yahoo!, lub Utwórz nową wiadomość e-mail w programie Outlook. W takim przypadku spróbuj zalogować się przy użyciu służbowego adresu e-mail bez tworzenia nowego konta.

1. Po wyświetleniu monitu wprowadź hasło.

1. Jeśli logujesz się po raz pierwszy, Azure Notebooks prosi o zezwolenie na dostęp do konta. Wybierz pozycję **tak** , aby kontynuować:

    ![Monit dotyczący uprawnień konta](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Ustawianie identyfikatora użytkownika

1. Po pierwszym zalogowaniu przypisywany jest tymczasowy identyfikator użytkownika, taki jak "Anon-idrca3". Za każdym razem, gdy masz identyfikator użytkownika rozpoczynający się od ciągu "Anon-", Azure Notebooks zostanie wyświetlony komunikat z prośbą o utworzenie własnego identyfikatora. Identyfikator użytkownika jest używany w dowolnym adresie URL, który można uzyskać, aby udostępnić projekty i notesy, więc wybierz element, który jest unikatowy i zrozumiały dla użytkownika.

    ![Monituj o wprowadzenie identyfikatora użytkownika dla Azure Notebooks](media/accounts/create-user-id.png)

    Jeśli wybierzesz pozycję **nie dziękuję**, Azure Notebooks nadal monitować o podanie identyfikatora użytkownika przy każdym logowaniu. Identyfikator użytkownika można również ustawić w dowolnym momencie w [profilu użytkownika](azure-notebooks-user-profile.md).

1. Po pomyślnym zalogowaniu Azure Notebooks przechodzi do strony profilu publicznego, na której można wybrać opcję **Edytuj informacje o profilu** , aby wypełnić pozostałe informacje (Aby uzyskać więcej informacji, zobacz [profil i identyfikator użytkownika](azure-notebooks-user-profile.md)):

    ![Początkowy widok strony profilu Azure Notebooks](media/accounts/profile-page-new.png)

> [!NOTE]
> Jeśli zobaczysz komunikat "identyfikator użytkownika jest już używany", spróbuj użyć innego identyfikatora. Identyfikatory użytkowników są unikatowe dla wszystkich kont Azure Notebooks, a Azure Notebooks również rezerwują pewne identyfikatory użytkowników, takie jak nazwy marki firmy Microsoft.

## <a name="sign-out"></a>Wyloguj się

Aby się wylogować, wybierz swoją nazwę użytkownika w prawym górnym rogu strony, a następnie wybierz pozycję **Wyloguj się**:

![Lokalizacja polecenia Wyloguj na Azure Notebooks](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Szybki Start: Eksportowanie projektu Jupyter Notebook](quickstart-export-jupyter-notebook-project.md)
