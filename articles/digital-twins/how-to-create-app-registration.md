---
title: Tworzenie rejestracji aplikacji
titleSuffix: Azure Digital Twins
description: Zobacz, jak utworzyć rejestrację aplikacji usługi Azure AD jako opcję uwierzytelniania dla aplikacji klienckich.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bd45bb264f8e29a2aad870a7daff45fdd44e0d3c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478815"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Tworzenie rejestracji aplikacji do użycia z Azure Digital Twins

Podczas pracy z wystąpieniem Azure Digital Twins często wchodzi się w interakcje z tym wystąpieniem za pośrednictwem aplikacji klienckich, takich jak niestandardowa aplikacja klienca lub przykład, taki [jak Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md). Te aplikacje muszą uwierzytelniać się w usłudze Azure Digital Twins, aby [](how-to-authenticate-client.md) wchodzić z nimi w interakcje, a niektóre z mechanizmów uwierzytelniania, których mogą używać aplikacje, obejmują rejestrację aplikacji usługi [Azure Active Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md) 

Nie jest to wymagane w przypadku wszystkich scenariuszy uwierzytelniania. Jeśli jednak używasz strategii uwierzytelniania lub przykładowego kodu, który  wymaga rejestracji aplikacji, w tym identyfikatora klienta i identyfikatora **dzierżawy,** w tym artykule pokazano, jak go skonfigurować.

## <a name="using-azure-ad-app-registrations"></a>Korzystanie z rejestracji aplikacji usługi Azure AD

[Azure Active Directory (Azure AD) to](../active-directory/fundamentals/active-directory-whatis.md) oparta na chmurze usługa firmy Microsoft do zarządzania tożsamościami i dostępem. Konfigurowanie rejestracji **aplikacji w usłudze** Azure AD jest jednym ze sposobów udzielania aplikacji klienckiej dostępu do Azure Digital Twins.

Ta rejestracja aplikacji umożliwia skonfigurowanie uprawnień dostępu do Azure Digital Twins [API.](how-to-use-apis-sdks.md) Później aplikacje klienckie mogą uwierzytelniać się w rejestracji aplikacji przy użyciu wartości identyfikatora klienta i dzierżawy **rejestracji,** a w rezultacie uzyskać skonfigurowane uprawnienia dostępu do interfejsów API.

>[!TIP]
> Możesz chcieć skonfigurować nową rejestrację aplikacji za każdym *razem,* gdy jej potrzebujesz, lub zrobić to tylko raz, ustanawiając rejestrację pojedynczej aplikacji, która zostanie udostępniona wszystkim scenariuszom, które tego wymagają.

## <a name="create-the-registration"></a>Tworzenie rejestracji

Zacznij od nawigowania [do Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) w witrynie Azure Portal (możesz użyć tego linku lub znaleźć go na pasku wyszukiwania portalu). Wybierz *Rejestracje aplikacji* z menu usługi, a następnie *pozycję + Nowa rejestracja.*

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Widok strony usługi Azure AD w witrynie Azure Portal z wyróżnieniami opcji menu &quot;Rejestracje aplikacji&quot; i przycisku &quot;+ Nowa rejestracja&quot;":::

Na stronie *Rejestrowanie aplikacji,* która zostanie poniżej, podaj żądane wartości:
* **Nazwa:** nazwa wyświetlana aplikacji usługi Azure AD do skojarzenia z rejestracją
* **Obsługiwane typy kont:** wybierz *pozycję Konta tylko w tym katalogu organizacyjnym (tylko katalog domyślny — pojedyncza dzierżawa)*
* **Redirect URI (Adres URI** przekierowania): adres URL odpowiedzi aplikacji usługi *Azure AD* dla aplikacji usługi Azure AD. Dodaj *publiczny/natywny (& mobilny) URI* `http://localhost` dla .

Gdy skończysz, naciśnij przycisk *Zarejestruj.*

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Widok strony &quot;Rejestrowanie aplikacji&quot; z wypełnionymi opisanymi wartościami":::

Po zakończeniu konfigurowania rejestracji portal przekieruje Cię do strony szczegółów.

## <a name="collect-client-id-and-tenant-id"></a>Zbieranie identyfikatora klienta i identyfikatora dzierżawy

Następnie zbierz kilka ważnych wartości dotyczących rejestracji aplikacji ze strony szczegółów:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Widok portalu z ważnymi wartościami rejestracji aplikacji":::

Zanotuj _**identyfikator aplikacji (klienta)**_ i _**identyfikator katalogu (dzierżawy)**_ wyświetlany **na** stronie. Są to wartości, które aplikacja kliency będą musieli użyć do uwierzytelnienia za pomocą Azure Digital Twins.

## <a name="provide-azure-digital-twins-api-permission"></a>Zapewnianie Azure Digital Twins interfejsu API

Następnie skonfiguruj utworzoną rejestrację aplikacji przy użyciu uprawnień linii bazowej do Azure Digital Twins API.

Na stronie portalu rejestracji aplikacji wybierz pozycję Uprawnienia *interfejsu API* z menu. Na poniższej stronie uprawnień naciśnij *przycisk + Dodaj uprawnienie.*

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Widok rejestracji aplikacji w aplikacji Azure Portal wyróżnienia opcji menu &quot;Uprawnienia interfejsu API&quot; i przycisku &quot;+ Dodaj uprawnienie&quot;":::

Na stronie *Żądanie uprawnień interfejsu* API  przejdź do karty Interfejsy API używane przez moją organizację i wyszukaj pozycję azure *digital twins.* Wybierz _**Azure Digital Twins**_ w wynikach wyszukiwania, aby kontynuować przypisywanie uprawnień dla Azure Digital Twins API.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Widok wyników wyszukiwania strony &quot;Żądanie uprawnień interfejsu API&quot; z Azure Digital Twins z identyfikatorem aplikacji (klienta) 0b07f429-9f4b-4714-9392-cc5e8e80c8b0.":::

>[!NOTE]
> Jeśli Twoja subskrypcja nadal ma istniejące wystąpienie usługi Azure Digital Twins z poprzedniej publicznej wersji zapoznawczej usługi (do lipca 2020 r.), musisz zamiast tego wyszukać i wybrać usługę _**Azure Smart Spaces.**_ Jest to starsza nazwa dla tego samego zestawu interfejsów API (zwróć uwagę, że identyfikator aplikacji *(klienta)* jest taki sam jak na powyższym zrzucie ekranu), a środowisko użytkownika nie zostanie zmienione poza tym krokiem.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Widok wyników wyszukiwania strony &quot;Żądanie uprawnień interfejsu API&quot; przedstawiający usługę Azure Smart Spaces":::

Następnie wybierz uprawnienia do udzielenia dla tych interfejsów API. Rozwiń **uprawnienie Odczyt (1)** i zaznacz pole wyboru *Read.Write,* aby przyznać tej aplikacji uprawnienia czytelnika rejestracji i zapisu.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Widok strony &quot;Żądanie uprawnień interfejsu API&quot; z uprawnieniami &quot;Odczyt.Zapis&quot; dla interfejsów API Azure Digital Twins API":::

Po *zakończeniu naciśnij pozycję* Dodaj uprawnienia.

### <a name="verify-success"></a>Weryfikowanie powodzenia

Na stronie *Uprawnienia interfejsu API* sprawdź, czy istnieje teraz wpis dla Azure Digital Twins z uprawnieniami do odczytu/zapisu:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Widok w portalu uprawnień interfejsu API dla rejestracji aplikacji usługi Azure AD z wyświetloną widokiem &quot;Dostęp do odczytu/zapisu&quot; dla Azure Digital Twins":::

Możesz również sprawdzić połączenie z usługą Azure Digital Twins w obszarzemanifest.jsrejestracji aplikacji na *stronie*, która została automatycznie zaktualizowana o informacje o Azure Digital Twins po dodaniu uprawnień interfejsu API.

Aby to zrobić, wybierz *pozycję Manifest* z menu, aby wyświetlić kod manifestu rejestracji aplikacji. Przewiń w dół okna kodu i poszukaj tych pól w obszarze `requiredResourceAccess` . Wartości powinny być zgodne z wartościami na poniższym zrzucie ekranu:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Widok manifestu rejestracji aplikacji usługi Azure AD w portalu. Zagnieżdżone w obszarze &quot;requiredResourceAccess&quot;, Istnieje wartość &quot;resourceAppId&quot; 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 i wartość &quot;resourceAccess > id&quot; 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

Jeśli brakuje tych wartości, spróbuj ponownie wykonać kroki opisane w [sekcji dotyczącej dodawania uprawnienia interfejsu API](#provide-azure-digital-twins-api-permission).

## <a name="other-possible-steps-for-your-organization"></a>Inne możliwe kroki dla organizacji

Aby pomyślnie skonfigurować rejestrację aplikacji, organizacja może wymagać dodatkowych akcji od właścicieli/administratorów subskrypcji. Wymagane czynności mogą różnić się zależnie od ustawień specyficznych dla organizacji.

Poniżej przedstawiono niektóre typowe działania właściciela/administratora subskrypcji, jakie mogą być wymagane. Te i inne operacje można wykonać na [*stronie aplikacja usługi Azure AD rejestracji w*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) Azure Portal.
* Udzielenie zgody administratora na rejestrację aplikacji. Twoja organizacja może mieć *globalnie włączona w* usłudze Azure AD wymaganą zgodę administratora dla wszystkich rejestracji aplikacji w ramach subskrypcji. Jeśli tak, właściciel/administrator będzie musiał wybrać ten przycisk dla twojej firmy na stronie uprawnień interfejsu *API* rejestracji aplikacji, aby rejestracja aplikacji była prawidłowa:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Widok portalu przycisku &quot;Przyznaj zgodę administratora&quot; w obszarze Uprawnienia interfejsu API":::
  - Jeśli zgoda została udzielona pomyślnie, wpis dla Azure Digital Twins powinien wtedy pokazywać wartość *Stan* _Udzielono **dla (Twojej firmy)**_
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Widok w portalu zgody administratora udzielonej firmie w obszarze uprawnienia interfejsu API":::
* Aktywowanie dostępu klienta publicznego
* Ustawianie określonych adresów URL odpowiedzi dla dostępu do Internetu i pulpitu
* Zezwalaj na niejawne przepływy uwierzytelniania OAuth2

Aby uzyskać więcej informacji na temat rejestracji aplikacji i jej różnych opcji konfiguracji, zobacz Register an application with the Microsoft identity platform (Rejestrowanie aplikacji [*za pomocą platformy tożsamości firmy Microsoft).*](/graph/auth-register-app-v2)

## <a name="next-steps"></a>Następne kroki

W tym artykule skonfigurujemy rejestrację aplikacji usługi Azure AD, która może służyć do uwierzytelniania aplikacji klienckich za pomocą Azure Digital Twins API.

Następnie przeczytaj o mechanizmach uwierzytelniania, w tym mechanizmach, które wykorzystują rejestracje aplikacji, i innych, które nie:
* [*How-to: Write app authentication code (Uwierzytelnianie aplikacji — kod)*](how-to-authenticate-client.md)