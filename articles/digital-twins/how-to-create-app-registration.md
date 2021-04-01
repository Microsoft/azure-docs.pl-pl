---
title: Tworzenie rejestracji aplikacji
titleSuffix: Azure Digital Twins
description: Zobacz jak utworzyć rejestrację aplikacji usługi Azure AD jako opcję uwierzytelniania dla aplikacji klienckich.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: acb5457f10c54a741a738dd8a1008e703b0f23b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102051025"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Tworzenie rejestracji aplikacji do użycia z usługą Azure Digital bliźniaczych reprezentacji

Podczas pracy z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji często można korzystać z tego wystąpienia za pomocą aplikacji klienckich, takich jak niestandardowa aplikacja kliencka lub Przykładowa [usługa Azure Digital bliźniaczych reprezentacji Explorer](quickstart-adt-explorer.md). Te aplikacje muszą uwierzytelniać się za pomocą usługi Azure Digital bliźniaczych reprezentacji, aby można było z nią korzystać, a niektóre z [mechanizmów uwierzytelniania](how-to-authenticate-client.md) obsługiwanych przez aplikacje obejmują **rejestrację aplikacji** [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) .

Nie jest to wymagane w przypadku wszystkich scenariuszy uwierzytelniania. Jeśli jednak korzystasz z strategii uwierzytelniania lub przykładu kodu, który wymaga rejestracji aplikacji, w tym **identyfikatora klienta** i **identyfikatora dzierżawy**, w tym artykule opisano, jak ustawić jeden z nich.

## <a name="using-azure-ad-app-registrations"></a>Korzystanie z rejestracji aplikacji usługi Azure AD

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) to usługa zarządzania tożsamościami i dostępem opartymi na chmurze firmy Microsoft. Skonfigurowanie **rejestracji aplikacji** w usłudze Azure AD jest jednym ze sposobów udzielenia aplikacji klienckiej dostępu do usługi Azure Digital bliźniaczych reprezentacji.

Ta rejestracja aplikacji to miejsce, w którym można skonfigurować uprawnienia dostępu do [interfejsów API Digital bliźniaczych reprezentacji platformy Azure](how-to-use-apis-sdks.md). Później aplikacje klienckie mogą uwierzytelniać się przed rejestracją aplikacji za pomocą **wartości identyfikatora klienta i identyfikator dzierżawy**, a w związku z tym uzyskali skonfigurowane uprawnienia dostępu do interfejsów API.

>[!TIP]
> Możesz chcieć skonfigurować nową rejestrację aplikacji za każdym razem, gdy będzie potrzebna, *lub* wykonać tę czynność tylko raz, ustanawiając rejestrację pojedynczej aplikacji, która będzie współużytkowana przez wszystkie scenariusze, które tego wymagają.

## <a name="create-the-registration"></a>Utwórz rejestrację

Zacznij od przechodzenia do [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) w Azure Portal (możesz użyć tego linku lub znaleźć go na pasku wyszukiwania portalu). Wybierz *rejestracje aplikacji* z menu usługi, a następnie pozycję *+ Nowa rejestracja*.

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Wyświetl stronę usługi Azure AD w Azure Portal, podświetl opcję menu &quot;Rejestracje aplikacji&quot; i przycisk &quot;+ Nowa rejestracja&quot;":::

Na stronie *zarejestruj aplikację* w następującej kolejności wprowadź żądane wartości:
* **Nazwa**: Nazwa wyświetlana aplikacji usługi Azure AD, która ma zostać skojarzona z rejestracją
* **Obsługiwane typy kont**: Wybierz *tylko konta w tym katalogu organizacji (tylko katalog domyślny — pojedynczy dzierżawca)*
* **Identyfikator URI przekierowania**: *adres URL odpowiedzi aplikacji usługi Azure AD* dla aplikacji usługi Azure AD. Dodaj identyfikator URI *klienta publicznego/natywnego (mobilnego & pulpitu)* dla `http://localhost` .

Po zakończeniu kliknij przycisk *zarejestruj* .

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Widok strony &quot;Zarejestruj aplikację&quot; przy użyciu opisanych wartości wypełnionych":::

Po zakończeniu konfigurowania rejestracji Portal przekieruje Cię do strony szczegółów.

## <a name="collect-client-id-and-tenant-id"></a>Zbierz identyfikator klienta i identyfikator dzierżawy

Następnie Zbierz istotne wartości dotyczące rejestracji aplikacji na stronie szczegółów:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Widok portalu ważnych wartości rejestracji aplikacji":::

Zanotuj _**Identyfikator**_ _**aplikacji (klienta)**_ na stronie **użytkownika** . Są to wartości, których aplikacja kliencka będzie potrzebować do uwierzytelniania za pomocą usługi Azure Digital bliźniaczych reprezentacji.

## <a name="provide-azure-digital-twins-api-permission"></a>Udostępnianie cyfrowego interfejsu API usługi Azure Digital bliźniaczych reprezentacji

Następnie skonfiguruj rejestrację aplikacji utworzoną za pomocą uprawnień linii bazowej do interfejsów API Digital bliźniaczych reprezentacji platformy Azure.

Na stronie portalu rejestracji aplikacji wybierz pozycję *uprawnienia interfejsu API* z menu. Na następującej stronie Uprawnienia kliknij przycisk *+ Dodaj uprawnienie* .

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Wyświetl rejestrację aplikacji w Azure Portal, wyróżnij opcję menu &quot;uprawnienia interfejsu API&quot; i przycisk &quot;+ Dodaj uprawnienie&quot;":::

Na stronie *uprawnienia do interfejsu API żądania* przejdź do opcji *interfejsy API Moja organizacja używa* karty i Wyszukaj w *usłudze Azure Digital bliźniaczych reprezentacji*. Wybierz pozycję _**Azure Digital bliźniaczych reprezentacji**_ z wyników wyszukiwania, aby kontynuować przypisywanie uprawnień do interfejsów API Digital bliźniaczych reprezentacji platformy Azure.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Widok wyników wyszukiwania strony &quot;Zażądaj interfejsu API&quot; przedstawiający usługę Azure Digital bliźniaczych reprezentacji z IDENTYFIKATORem aplikacji (klienta) 0b07f429-9f4b-4714-9392-cc5e8e80c8b0.":::

>[!NOTE]
> Jeśli Twoja subskrypcja ma nadal istniejące wystąpienie usługi Azure Digital bliźniaczych reprezentacji z poprzedniej publicznej wersji zapoznawczej usługi (przed lipca 2020), musisz wyszukać i wybrać _**usługę Azure Smart Spaces**_ . Jest to Starsza nazwa tego samego zestawu interfejsów API (Zauważ, że *Identyfikator aplikacji (klienta)* jest taki sam jak na poniższym zrzucie ekranu), a Twoje środowisko nie zostanie zmienione poza ten krok.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Widok wyników wyszukiwania strony &quot;żądanie interfejsu API żądania&quot; przedstawiający usługę Azure Smart Spaces":::

Następnie wybierz uprawnienia, które mają zostać przyznane dla tych interfejsów API. Rozwiń uprawnienie **Odczyt (1)** , a następnie zaznacz pole wyboru *Odczytaj. Zapisz* , aby udzielić uprawnienia czytelnikowi rejestracji aplikacji i składnika zapisywania.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Widok strony &quot;uprawnienia interfejsu API żądania&quot; Wybieranie uprawnień &quot;Odczyt. zapis&quot; dla interfejsów API Digital bliźniaczych reprezentacji platformy Azure":::

Po zakończeniu kliknij przycisk *Dodaj uprawnienia* .

### <a name="verify-success"></a>Weryfikowanie sukcesu

Na stronie *uprawnienia interfejsu API* Sprawdź, czy istnieje już wpis do usługi Azure Digital bliźniaczych reprezentacji odzwierciedlający uprawnienia do odczytu/zapisu:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Widok portalu uprawnień interfejsu API dla rejestracji aplikacji usługi Azure AD, pokazujący uprawnienie do odczytu/zapisu dla usługi Azure Digital bliźniaczych reprezentacji":::

Możesz również zweryfikować połączenie z usługą Azure Digital bliźniaczych reprezentacji w *manifest.js* rejestracji aplikacji, która została automatycznie zaktualizowana przy użyciu informacji o usłudze Azure Digital bliźniaczych reprezentacji po dodaniu uprawnień interfejsu API.

W tym celu wybierz pozycję *manifest* z menu, aby wyświetlić kod manifestu rejestracji aplikacji. Przewiń w dół okna kod i poszukaj tych pól w obszarze `requiredResourceAccess` . Wartości powinny być zgodne z poniższymi zrzutu ekranu:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Widok portalu dla rejestracji aplikacji usługi Azure AD. Zagnieżdżone w &quot;requiredResourceAccess&quot; ma wartość &quot;resourceAppId&quot; elementu 0b07f429-9f4b-4714-9392-cc5e8e80c8b0, a wartość &quot;resourceAccess > ID&quot; elementu 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

Jeśli te wartości są niedostępne, spróbuj wykonać kroki opisane w [sekcji dotyczącej dodawania uprawnień interfejsu API](#provide-azure-digital-twins-api-permission).

## <a name="other-possible-steps-for-your-organization"></a>Inne możliwe kroki dla organizacji

Istnieje możliwość, aby organizacja wymagała dodatkowych akcji od właścicieli/administratorów subskrypcji w celu pomyślnego skonfigurowania rejestracji aplikacji. Wymagane czynności mogą różnić się zależnie od ustawień specyficznych dla organizacji.

Poniżej przedstawiono niektóre typowe działania właściciela/administratora subskrypcji, jakie mogą być wymagane. Te i inne operacje można wykonać na stronie [*rejestracje aplikacja usługi Azure AD*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) w Azure Portal.
* Udzielenie zgody administratora na rejestrację aplikacji. Twoja organizacja może mieć *zgodę administratora* , która została zadana globalnie włączona w usłudze Azure AD dla wszystkich rejestracji aplikacji w ramach subskrypcji. Jeśli tak, właściciel/administrator będzie musiał wybrać ten przycisk dla firmy na stronie *uprawnień interfejsu API* rejestracji aplikacji, aby Rejestracja aplikacji była prawidłowa:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Widok portalu przycisku &quot;Udziel zgody administratora&quot; w obszarze uprawnienia interfejsu API":::
  - Jeśli zgoda została udzielona pomyślnie, wpis na potrzeby usługi Azure Digital bliźniaczych reprezentacji powinien wskazywać wartość *stanu* _przyznany dla **(Twoja firma)**_
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Widok portalu zgody administratora udzielonego dla firmy w ramach uprawnień interfejsu API":::
* Aktywuj dostęp klienta publicznego
* Ustawianie określonych adresów URL odpowiedzi dla dostępu do sieci Web i pulpitu
* Zezwalaj na niejawne przepływy uwierzytelniania OAuth2

Aby uzyskać więcej informacji na temat rejestracji aplikacji i jej innych opcji instalacji, zobacz [*Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft*](/graph/auth-register-app-v2).

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano konfigurowanie rejestracji aplikacji usługi Azure AD, której można użyć do uwierzytelniania aplikacji klienckich za pomocą interfejsów API Digital bliźniaczych reprezentacji na platformie Azure.

Następnie przeczytaj informacje o mechanizmach uwierzytelniania, w tym o korzystaniu z rejestracji aplikacji i innych, które nie są następujące:
* [*Instrukcje: zapisywanie kodu uwierzytelniania aplikacji*](how-to-authenticate-client.md)