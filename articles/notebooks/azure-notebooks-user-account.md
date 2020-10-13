---
title: Zaloguj się do wersji zapoznawczej Azure Notebooks
description: Skonfiguruj konto użytkownika dla Azure Notebooks przy użyciu konto Microsoft lub konta służbowego.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 929a21785b5b48c81c2d2016874feed61021d3c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85832189"
---
# <a name="your-user-account-for-azure-notebooks-preview"></a>Twoje konto użytkownika na potrzeby Azure Notebooks wersji zapoznawczej

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Możesz użyć Azure Notebooks z lub bez logowania się przy użyciu konta użytkownika:

- Bez logowania można tworzyć i uruchamiać notesy, ale nie można zachować notesów ani plików danych w ramach projektów. Użytkownicy, którzy otrzymują link do notesu platformy Azure, na przykład mogą korzystać z notesu bez konieczności logowania się.
- Po zalogowaniu Azure Notebooks zachowuje wszystkie projekty z Twoim kontem. Zalogowani użytkownicy mają także identyfikator użytkownika, który umożliwia im udostępnianie swoich projektów i notesów innym osobom.
- Jeśli konto używane dla Azure Notebooks jest również skojarzone z subskrypcją platformy Azure, uzyskasz dodatkowe korzyści, takie jak uruchamianie notesów na bardziej wydajnych serwerach, tworzenie prywatnych notesów i udzielanie uprawnień dla notesów poszczególnym użytkownikom.

Zalogowanie się do Azure Notebooks wymaga konta Microsoft lub konta służbowego. Po wybraniu polecenia **Zaloguj** w prawym górnym rogu strony Notes zostanie wyświetlony monit o podanie konta:

![Polecenie logowania dla Azure Notebooks](media/accounts/sign-in-command.png)

Wszystkie prace wykonywane w Azure Notebooks są skojarzone z kontem używanym do logowania. Każde konto musi mieć unikatowy identyfikator użytkownika w [profilu użytkownika](azure-notebooks-user-profile.md). W związku z tym możesz zalogować się do Azure Notebooks z różnymi kontami, jeśli konieczne jest zachowanie oddzielnych zestawów projektów i osobnych tożsamości. Na przykład każdy członek zespołu ds. analizy danych może mieć zarówno pojedyncze konta, jak i konto grupy udostępnionej, za pomocą których można przedstawić swoją służbę pracownikom spoza swojej firmy. Instruktorzy, podobnie, mogą zachować konto dla swojej roli nauczania, która różni się od konta używanego w ramach konsultacji zewnętrznych lub pracy Open Source.

## <a name="microsoft-accounts"></a>Konta Microsoft

Konta Microsoft są używane do logowania się do dowolnej liczby produktów i usług firmy Microsoft, takich jak Windows, Azure, outlook.com, OneDrive i XBox Live. Jeśli używasz dowolnej z tych usług, prawdopodobnie masz już konto Microsoft, którego możesz używać z Azure Notebooks.

Jeśli nie masz pewności, wybierz polecenie **Utwórz jedno** w monicie konta. Nowy konto Microsoft można utworzyć przy użyciu dowolnego adresu e-mail od dowolnego dostawcy.

![Polecenie tworzenia nowego konto Microsoft](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Jeśli spróbujesz utworzyć nowe konto przy użyciu adresu e-mail, z którym jest już skojarzone konto, może zostać wyświetlony komunikat "nie można utworzyć konta w tym miejscu przy użyciu służbowego adresu e-mail. Użyj osobistej poczty e-mail, takiej jak Gmail lub Yahoo!, lub Utwórz nową wiadomość e-mail w programie Outlook. W takim przypadku spróbuj zalogować się przy użyciu służbowego adresu e-mail bez tworzenia nowego konta.

W przypadku kont podrzędnych dostęp do Azure Notebooks jest blokowany domyślnie. Zalogowanie się przy użyciu konta podrzędnego spowoduje wyświetlenie poniższego błędu:

![Wystąpił błąd podczas próby zalogowania się przy użyciu konta podrzędnego: Wystąpił problem, rodzic zablokował dostęp](media/accounts/child-account-error.png)

Aby włączyć dostęp, obiekt nadrzędny musi wykonać następujące czynności:

1. Odwiedź stronę `https://account.live.com/mk` i zaloguj się przy użyciu konta nadrzędnego.
1. W sekcji dla danego elementu podrzędnego wybierz pozycję **Zarządzaj dostępem tego elementu podrzędnego do aplikacji innych**firm.
1. Na następnej stronie wybierz pozycję **Włącz dostęp**.
1. Po następnym użyciu konta podrzędnego do zalogowania się do Azure Notebooks wybierz pozycję **tak** w wyświetlonym monicie o uprawnienia.

> [!Warning]
> Umożliwienie dostępu do aplikacji innych firm dla Azure Notebooks umożliwia również dostęp do wszystkich innych aplikacji innych firm. Rodzicom zaleca się użycie uznania podczas włączania dostępu i może chcieć lepiej monitorować działanie dziecka.

## <a name="work-or-school-accounts"></a>Konta służbowe

Konto służbowe jest tworzone przez administratora organizacji, aby umożliwić członkowi organizacji dostęp do usług w chmurze firmy Microsoft, takich jak Microsoft 365, a także konta do logowania się do systemu Windows na komputerze przyłączonym do domeny. Konto służbowe zwykle używa adresu e-mail organizacji, takiego jak any-user@contoso.com .

Zalogowanie się do Azure Notebooks przy użyciu konta służbowego może wymagać zgody administratora, ponieważ Azure Notebooks zbiera lub używa (ale nie ujawnia) informacji, takich jak adres e-mail konta i informacje o przeglądarce użytkownika. (Dane przeglądarki służą do optymalizowania funkcji zgodnie z popularnym użyciem).

Administrator konta organizacyjnego musi wyrazić zgodę w imieniu użytkowników, jeśli użytkownicy są ograniczeni do poszczególnych użytkowników. W takim przypadku użytkownicy zobaczą komunikat "nie możesz uzyskać dostępu do tej aplikacji":

![Komunikat "nie można uzyskać dostępu do tej aplikacji" w przypadku korzystania z konta służbowego](media/accounts/consent-permissions-denied.png)

Aby wyrazić zgodę jako administrator, użyj [strony zgody administratora](https://notebooks.azure.com/account/adminConsent), która przeprowadzi Cię przez proces.

## <a name="next-steps"></a>Następne kroki  

> [!div class="nextstepaction"]
> [Edytuj swój profil i identyfikator użytkownika](azure-notebooks-user-profile.md)
