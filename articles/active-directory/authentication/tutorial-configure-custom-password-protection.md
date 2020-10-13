---
title: Konfigurowanie niestandardowych list ochrony hasłem Azure Active Directory
description: W ramach tego samouczka nauczysz się konfigurować niestandardowe listy zabronionych ochrony haseł dla Azure Active Directory, aby ograniczyć typowe słowa w środowisku.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24e0cfa4227ba5d7c38449d3cf1304add5ae5c1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88718887"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Samouczek: Konfigurowanie niestandardowych zakazanych haseł Azure Active Directory ochrony hasłem

Użytkownicy często tworzą hasła używające zwykłych słów lokalnych, takich jak szkoła, zespół sportowy lub osoba sławę. Hasła te są łatwe do odgadnięcia i są słabe przed atakami opartymi na słownikach. Aby wymusić silne hasła w organizacji, lista niestandardowych zakazanych haseł Azure Active Directory (Azure AD) umożliwia dodanie określonych ciągów do oszacowania i zablokowania. Żądanie zmiany hasła nie powiedzie się, jeśli na liście niestandardowych zakazanych haseł znajduje się dopasowanie.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włącz niestandardowe zabronione hasła
> * Dodawanie wpisów do listy niestandardowych zakazanych haseł
> * Testuj zmiany hasła z zakazanym hasłem

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Działająca dzierżawa usługi Azure AD z co najmniej Azure AD — wersja Premium włączoną licencją P1 lub próbną.
    * W razie potrzeby [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Konto z uprawnieniami *administratora globalnego* .
* Użytkownik niebędący administratorem z hasłem znanym, takim jak *Użytkownik testowy*. W tym samouczku testujesz zdarzenie zmiany hasła przy użyciu tego konta.
    * Jeśli musisz utworzyć użytkownika, zobacz [Szybki Start: Dodawanie nowych użytkowników do Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
    * Aby przetestować operację zmiany hasła przy użyciu zabronionego hasła, dzierżawa usługi Azure AD musi być skonfigurowana do samoobsługowego [resetowania hasła](tutorial-enable-sspr.md).

## <a name="what-are-banned-password-lists"></a>Co to są listy zabronionych haseł?

Usługa Azure AD zawiera globalną listę wykluczonych haseł. Zawartość globalnej listy zakazanych haseł nie jest oparta na żadnym zewnętrznym źródle danych. Zamiast tego globalna lista wykluczonych haseł jest oparta na bieżących wynikach telemetrii i analizie zabezpieczeń usługi Azure AD. Gdy użytkownik lub administrator próbuje zmienić lub zresetować swoje poświadczenia, wymagane hasło jest sprawdzane na liście zakazanych haseł. Żądanie zmiany hasła nie powiedzie się, jeśli na liście globalnie zabronione hasła znajduje się dopasowanie. Nie można edytować tej domyślnej listy zabronionych haseł.

Aby zapewnić elastyczność, w jaki sposób hasła są dozwolone, można również zdefiniować niestandardową listę wykluczonych haseł. Niestandardowa lista wykluczonych haseł działa obok globalnej listy wykluczonych haseł w celu wymuszenia silnych haseł w organizacji. Warunki specyficzne dla organizacji można dodać do listy niestandardowych zakazanych haseł, takich jak następujące przykłady:

* Nazwy marki
* Nazwy produktów
* Lokalizacje, takie jak oddział firmy
* Terminy wewnętrzne specyficzne dla firmy
* Skróty, które mają określone znaczenie firmy

Gdy użytkownik spróbuje zresetować hasło do elementu znajdującego się na liście globalnych lub niestandardowych zakazanych haseł, zobaczy jeden z następujących komunikatów o błędzie:

* *Niestety, hasło zawiera słowo, frazę lub wzorzec, które ułatwiają odgadnięcie hasła. Spróbuj ponownie, używając innego hasła.*
* *Niestety, nie można użyć tego hasła, ponieważ zawiera słowa lub znaki, które zostały zablokowane przez administratora. Spróbuj ponownie, używając innego hasła.*

Niestandardowa lista wykluczonych haseł jest ograniczona do maksymalnie 1000 warunków. Nie jest ona przeznaczona do blokowania dużych list haseł. Aby zmaksymalizować zalety listy nieużywanych zakazanych haseł, zapoznaj się z tematem Omówienie [niestandardowych zakazanych haseł](concept-password-ban-bad.md#custom-banned-password-list) i [algorytmów oceny haseł](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="configure-custom-banned-passwords"></a>Konfigurowanie niestandardowych zakazanych haseł

Włączmy listę niestandardowych zakazanych haseł i dodamy kilka wpisów. W dowolnym momencie możesz dodać dodatkowe wpisy do listy zakazanych haseł.

Aby włączyć listę niestandardowych zakazanych haseł i dodać do niej wpisy, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta z uprawnieniami *administratora globalnego* .
1. Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **zabezpieczenia** z menu po lewej stronie.
1. W nagłówku menu **Zarządzaj** wybierz opcję **metody uwierzytelniania**, a następnie **Ochrona hasłem**.
1. Dla opcji **Wymuszaj listę niestandardową** Ustaw *wartość tak*.
1. Dodaj ciągi do **listy niestandardowych zakazanych haseł**, jednego ciągu na wiersz. Następujące zagadnienia i ograniczenia mają zastosowanie do listy niestandardowych haseł zabronionych:

    * Niestandardowa lista wykluczonych haseł może zawierać do 1000 warunków.
    * Niestandardowa lista wykluczonych haseł nie uwzględnia wielkości liter.
    * Niestandardowa lista wykluczonych haseł uwzględnia wspólne podstawienie znaków, takie jak "o" i "0", lub "a" i "@".
    * Minimalna długość ciągu to cztery znaki, a maksymalna to 16 znaków.

    Określ własne niestandardowe hasła, aby zablokować, jak pokazano w poniższym przykładzie.

    [![Zmodyfikuj niestandardową listę wykluczonych haseł w obszarze metody uwierzytelniania w Azure Portal ](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. Pozostaw opcję **Włącz ochronę hasłem w systemie Windows Server Active Directory** wartość *nie*.
1. Aby włączyć niestandardowe zabronione hasła i wpisy, wybierz pozycję **Zapisz**.

Aktualizacja listy niestandardowo zakazanych haseł może potrwać kilka godzin.

W przypadku środowiska hybrydowego można także [wdrożyć ochronę hasłem usługi Azure AD w środowisku lokalnym](howto-password-ban-bad-on-premises-deploy.md). Te same globalne i niestandardowe listy haseł zabronione są używane zarówno w przypadku żądań zmiany w chmurze, jak i na Premium.

## <a name="test-custom-banned-password-list"></a>Niestandardowa lista wykluczonych haseł

Aby wyświetlić listę niestandardowych zakazanych haseł w akcji, spróbuj zmienić hasło na odmianę dodaną w poprzedniej sekcji. Gdy usługa Azure AD próbuje przetworzyć zmianę hasła, hasło jest dopasowywane do wpisu na liście niestandardowych zakazanych haseł. Zostanie wyświetlony komunikat o błędzie.

> [!NOTE]
> Aby użytkownik mógł zresetować swoje hasło w portalu internetowym, dzierżawa usługi Azure AD musi być skonfigurowana do samoobsługowego [resetowania hasła](tutorial-enable-sspr.md). W razie potrzeby użytkownik może zarejestrować się [w usłudze SSPR pod https://aka.ms/ssprsetup adresem ](https://aka.ms/ssprsetup).

1. Przejdź do strony **Moje aplikacje** pod adresem [https://myapps.microsoft.com](https://myapps.microsoft.com) .
1. W prawym górnym rogu wybierz swoją nazwę, a następnie wybierz pozycję **profil** z menu rozwijanego.

    ![Wybieranie profilu](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. Na stronie **profil** wybierz pozycję **Zmień hasło**.
1. Na stronie **Zmienianie hasła** wprowadź istniejące hasło (stare). Wprowadź i Potwierdź nowe hasło, które znajduje się na liście niestandardowo zakazanych haseł zdefiniowanej w poprzedniej sekcji, a następnie wybierz pozycję **Prześlij**.
1. Zostanie zwrócony komunikat o błędzie informujący o tym, że hasło zostało zablokowane przez administratora, jak pokazano w następującym przykładzie:

    ![Komunikat o błędzie wyświetlany podczas próby użycia hasła, które jest częścią listy niestandardowo zakazanych haseł](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz już używać niestandardowej listy wykluczonych haseł skonfigurowanej w ramach tego samouczka, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **zabezpieczenia** z menu po lewej stronie.
1. W nagłówku menu **Zarządzaj** wybierz opcję **metody uwierzytelniania**, a następnie **Ochrona hasłem**.
1. Dla opcji **Wymuszaj listę niestandardową** ustaw wartość *nie*.
1. Aby zaktualizować niestandardową konfigurację zakazanych haseł, wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono i skonfigurowano niestandardowe listy ochrony haseł dla usługi Azure AD. W tym samouczku omówiono:

> [!div class="checklist"]
> * Włącz niestandardowe zabronione hasła
> * Dodawanie wpisów do listy niestandardowych zakazanych haseł
> * Testuj zmiany hasła z zakazanym hasłem

> [!div class="nextstepaction"]
> [Włączanie usługi Azure Multi-Factor Authentication bazującej na ryzyku](./tutorial-enable-azure-mfa.md)