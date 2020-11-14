---
title: Dodawanie znakowania do strony logowania w organizacji — Azure AD
description: Instrukcje dotyczące sposobu dodawania znakowania organizacji do strony logowania Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/24/2020
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04e1d35ab17a49dd1c4e9bd2bd19289de2b8658a
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "89565858"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Dodawanie znakowania do strony logowania Azure Active Directory organizacji
Użyj logo organizacji i niestandardowych schematów kolorów, aby zapewnić spójny wygląd i działanie na stronach logowania Azure Active Directory (Azure AD). Strony logowania są wyświetlane, gdy użytkownicy logują się do aplikacji sieci Web w organizacji, takich jak Microsoft 365, które korzystają z usługi Azure AD jako dostawcy tożsamości.

>[!NOTE]
>Dodanie oznakowania niestandardowego wymaga użycia wersji Azure Active Directory — wersja Premium 1, Premium 2 lub Basic lub posiadania licencji Microsoft 365. Aby uzyskać więcej informacji na temat licencjonowania i wydań, zobacz [Rejestrowanie się w usłudze Azure AD — wersja Premium](active-directory-get-started-premium.md).<br><br>Klienci w Chinach mogą używać wersji Premium i Podstawowa usługi Azure AD za pośrednictwem wystąpienia usługi Azure Active Directory dostępnego na całym świecie. Wersje Premium i Podstawowa usługi Azure AD nie są obecnie obsługiwane w usłudze platformy Azure świadczonej przez firmę 21Vianet w Chinach. Aby uzyskać więcej informacji, porozmawiaj z nami, korzystając z [forum usługi Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customize-your-azure-ad-sign-in-page"></a>Dostosowywanie strony logowania do usługi Azure AD
Możesz dostosować strony logowania usługi Azure AD, które są wyświetlane, gdy użytkownicy logują się do aplikacji specyficznych dla dzierżawy w organizacji, takich jak `https://outlook.com/contoso.com` lub podczas przekazywania zmiennej domeny, takiej jak `https://passwordreset.microsoftonline.com/?whr=contoso.com` .

Niestandardowa znakowanie nie zostanie natychmiast wyświetlone, gdy użytkownicy przejdą do witryn, takich jak \. Office.com www. Zamiast tego użytkownik musi się zalogować, aby pojawiło się niestandardowe oznakowanie. Po zalogowaniu się użytkownika znakowanie może potrwać 15 minut lub dłużej. 

> [!NOTE]
> Wszystkie elementy znakowania są opcjonalne. Na przykład jeśli określisz logo transparentu bez obrazu tła, na stronie logowania zostanie wyświetlone logo z domyślnym obrazem tła z lokacji docelowej (na przykład Microsoft 365).<br><br>Ponadto znakowanie strony logowania nie jest przenoszone na osobiste konta Microsoft. Jeśli użytkownicy lub Goście biznesowi zalogują się przy użyciu osobistego konto Microsoft, Strona logowania nie będzie odzwierciedlała znakowania organizacji.

### <a name="to-customize-your-branding"></a>Aby dostosować znakowanie
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz pozycję **Azure Active Directory** , a następnie wybierz pozycję **znakowanie firmowe** , a następnie wybierz pozycję **Konfiguruj**.

    ![Strona firmy Contoso — znakowanie firmowe, opcja Konfiguruj wyróżnione](media/customize-branding/company-branding-configure-button.png)

3. Na stronie **Konfiguruj znakowanie firmowe** Podaj dowolne lub wszystkie z poniższych informacji.

    >[!IMPORTANT]
    >Wszystkie niestandardowe obrazy, które dodasz na tej stronie, mają rozmiar obrazu (w pikselach), rozmiar pliku (KB), ograniczenia. Ze względu na te ograniczenia najprawdopodobniej trzeba będzie użyć edytora zdjęć do utworzenia obrazów o odpowiednim rozmiarze.

    - **Ustawienia ogólne**

        ![Skonfiguruj stronę znakowania firmowego z ustawieniami ogólnymi](media/customize-branding/configure-company-branding-general-settings.png)

        - **Językowe.** Język jest automatycznie ustawiany jako domyślny i nie można go zmienić.
        
        - **Obraz tła strony logowania.** Wybierz plik obrazu PNG lub jpg, który ma być wyświetlany jako tło dla stron logowania. Obraz zostanie zakotwiczony do środka przeglądarki i będzie skalowany do rozmiaru widocznego obszaru. Nie można wybrać obrazu o rozmiarze większym niż 1920 x 1080 pikseli lub pliku o rozmiarze większym niż 300 KB.
        
            Zalecane jest korzystanie z obrazów bez silnego punktu skupienia, np. nieprzezroczyste, białe pole na środku ekranu i może obejmować dowolną część obrazu w zależności od wymiarów widocznego miejsca.

        - **Logo transparentu.** Wybierz plik PNG lub jpg logo do wyświetlania na stronie logowania, gdy użytkownik wprowadzi nazwę użytkownika i na stronie portalu **Moje aplikacje** .
            
            Rozmiar obrazu nie może być większy niż 60 pikseli lub większy niż 280 pikseli. Zalecamy używanie przezroczystego obrazu, ponieważ tło może nie być zgodne z tłem logo. Zalecamy również, aby nie dodawać dopełnienia obrazu lub spowodować, że logo jest nieco małe.

        - **Wskazówka nazwy użytkownika.** Wpisz tekst wskazówki, który pojawia się dla użytkowników w przypadku zapomnienia ich nazwy użytkownika. Ten tekst musi być w formacie Unicode, bez linków lub kodu i nie może przekraczać 64 znaków. Jeśli Goście logują się do aplikacji, sugerujemy, aby nie dodawać tej wskazówki.

        - **Tekst i formatowanie strony logowania.** Wpisz tekst wyświetlany w dolnej części strony logowania. Możesz użyć tego tekstu do przekazania dodatkowych informacji, takich jak numer telefonu do działu pomocy technicznej lub oświadczenia prawnego. Ten tekst musi być w formacie Unicode i nie może przekraczać 1024 znaków.

           Można dostosować wprowadzony tekst strony logowania. Aby rozpocząć nowy akapit, użyj klawisza ENTER dwa razy. Możesz również zmienić formatowanie tekstu tak, aby obejmowało pogrubienie, kursywę, podkreślenie lub kliknięcie. Aby dodać formatowanie do tekstu, użyj następującej składni: 

          > Łącza ```[text](link)``` 
          
          > Pogrubienie: ``` **text** ``` lub ``` __text__ ``` 
          
          > Kursywa: ``` *text* ``` lub ``` _text_ ``` 
          
          > Podkreślenie ``` ++text++ ``` 

    - **Ustawienia zaawansowane**
            
        ![Skonfiguruj stronę znakowania firmowego, używając zaawansowanych ustawień](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Kolor tła strony logowania.** Określ kolor szesnastkowy (na przykład biały jest #FFFFFF), który będzie wyświetlany zamiast obrazu tła w sytuacjach połączeń o niskiej przepustowości. Zalecamy użycie podstawowego koloru logo transparentu lub koloru organizacji.

        - **Obraz z logo kwadratowego.** Wybierz obraz PNG (preferowany) lub jpg logo organizacji, który będzie widoczny dla użytkowników podczas procesu instalacji nowych urządzeń z systemem Windows 10 Enterprise. Ten obraz jest używany tylko na potrzeby uwierzytelniania systemu Windows i pojawia się tylko w dzierżawcach korzystających z funkcji [autopilotażu systemu Windows]( /windows/deployment/windows-autopilot/windows-10-autopilot) w celu wdrożenia lub stron wprowadzania haseł w innych środowiskach systemu Windows 10. W niektórych przypadkach może również pojawić się w oknie dialogowym zgody.
        
            Rozmiar obrazu nie może być większy niż 240x240 pikseli i musi mieć rozmiar pliku mniejszy niż 10 KB. Zalecamy używanie przezroczystego obrazu, ponieważ tło może nie być zgodne z tłem logo. Zalecamy również, aby nie dodawać dopełnienia obrazu lub spowodować, że logo jest nieco małe.
    
        - **Kwadratowy obraz logo, motyw ciemny.** Analogicznie jak powyżej. Ten obraz logo umieszcza symbol logo kwadratowego, gdy jest używany z ciemnym tłem, na przykład w przypadku ekranów przyłączonych do usługi Azure AD systemu Windows 10 podczas korzystania z funkcji OOBE (out-of-Box Experience).  Jeśli logo wygląda dobrze na białym, ciemnoniebieskim i czarnym tle, nie musisz dodawać tego obrazu. 
        
        - **Pokaż opcję, która ma pozostać zalogowana.** Możesz zezwolić użytkownikom na logowanie się do usługi Azure AD do momentu jawnego wylogowania. Jeśli wybierzesz opcję **nie** , ta opcja jest ukryta, a użytkownicy muszą logować się za każdym razem, gdy przeglądarka zostanie zamknięta i ponownie otwarta.

            Ta funkcja jest dostępna tylko dla domyślnego obiektu znakowania, a nie w żadnym obiekcie specyficznym dla języka. Aby dowiedzieć się więcej o konfigurowaniu i rozwiązywaniu problemów z opcją pozostawania zalogowania, zobacz [Konfigurowanie monitu "pozostanie zalogowanym?" dla kont usługi Azure AD](keep-me-signed-in.md)
        
            >[!NOTE]
            >Niektóre funkcje usługi SharePoint Online oraz pakietu Office 2010 zależą od tego, czy użytkownicy mogą wybrać opcję pozostania zalogowanym. Jeśli ta opcja zostanie ustawiona na **Nie** , użytkownicy mogą otrzymywać dodatkowe i nieoczekiwane monity o zalogowanie.
   

3. Po zakończeniu dodawania znakowania wybierz pozycję **Zapisz**.

    Jeśli ten proces spowoduje utworzenie pierwszej niestandardowej konfiguracji znakowania, zostanie ona domyślnie utworzona dla dzierżawy. Jeśli masz dodatkowe konfiguracje, będziesz mieć możliwość wybrania konfiguracji domyślnej.
    
    >[!IMPORTANT]
    >Aby dodać więcej konfiguracji znakowania firmowego do dzierżawy, należy wybrać pozycję **nowy język** na stronie **contoso-Company marking** . Spowoduje to otwarcie strony **Konfigurowanie znakowania firmowego** , na której można wykonać te same czynności jak powyżej.

## <a name="update-your-custom-branding"></a>Aktualizowanie marki niestandardowej
Po utworzeniu marki niestandardowej można wrócić i zmienić dowolne elementy.

### <a name="to-edit-your-custom-branding"></a>Aby edytować niestandardowe znakowanie
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz pozycję **Azure Active Directory** , a następnie wybierz pozycję **znakowanie firmowe** , a następnie wybierz pozycję **Konfiguruj**.

    ![Firma Contoso — Strona znakowania firmy z domyślną konfiguracją](media/customize-branding/company-branding-default-config.png)

3. Na stronie **Konfiguruj znakowanie firmowe** Dodaj, Usuń lub zmień dowolne informacje na podstawie opisów w sekcji [Dostosowywanie strony logowania do usługi Azure AD w](#customize-your-azure-ad-sign-in-page) tym artykule.

4. Wybierz pozycję **Zapisz**.

   Uwzględnienie zmian znakowania na stronie logowania może potrwać do godziny.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Dodawanie specyficznego dla języka znakowania firmowego do katalogu
Nie można zmienić języka oryginalnej konfiguracji z poziomu języka domyślnego. Jeśli jednak potrzebna jest konfiguracja w innym języku, można utworzyć nową konfigurację.

### <a name="to-add-a-language-specific-branding-configuration"></a>Aby dodać konfigurację oznakowania specyficzną dla języka

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz pozycję **Azure Active Directory** , a następnie wybierz pozycję **znakowanie firmowe** , a następnie wybierz pozycję **nowy język**.

    ![Strona firmy Contoso — znakowanie firmowe z wyróżnioną opcją nowego języka](media/customize-branding/company-branding-new-language.png)

3. Na stronie **Konfiguruj znakowanie firmowe** wybierz swój język (na przykład francuski), a następnie Dodaj przetłumaczone informacje na podstawie opisów w sekcji [Dostosowywanie strony logowania do usługi Azure AD w](#customize-your-azure-ad-sign-in-page) tym artykule.

4. Wybierz pozycję **Zapisz**.

    Strona **contoso — firmowy znakowanie** jest aktualizowana, aby pokazać nową konfigurację języka francuskiego.

    ![Strona firmy Contoso — znakowanie firmowe z nową konfiguracją języka](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Dodawanie niestandardowej marki do stron
Dodaj niestandardowe znakowanie do stron, modyfikując koniec adresu URL z tekstem `?whr=yourdomainname` . Ta modyfikacja działa na kilku stronach, w tym na stronie konfiguracji Multi-Factor Authentication (MFA), stronie Konfiguracja samoobsługowego resetowania hasła (SSPR) oraz na stronie logowania.

**Przykłady:**

**Oryginalny adres URL:**https://aka.ms/MFASetup<br>
**Niestandardowy adres URL:**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**Oryginalny adres URL:**https://aka.ms/SSPR<br>
**Niestandardowy adres URL:**`https://passwordreset.microsoftonline.com/?whr=contoso.com`