---
title: Dodawanie przepływu użytkownika samoobsługowego rejestrowania — Azure AD
description: Tworzenie przepływów użytkowników dla aplikacji, które są tworzone przez organizację. Następnie użytkownicy odwiedzający tę aplikację mogą uzyskać konto gościa przy użyciu opcji skonfigurowanych w przepływie użytkownika.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a59e573c9b24d9a8b5577b55d143fcaca67952f0
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120763"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app"></a>Dodawanie przepływu użytkownika samoobsługowego rejestrowania do aplikacji

> [!NOTE]
> Niektóre funkcje wymienione w tym artykule stanowią publiczną funkcję w wersji zapoznawczej programu Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

W przypadku aplikacji, które tworzysz, możesz utworzyć przepływy użytkownika, które umożliwiają użytkownikowi rejestrowanie się w aplikacji i tworzenie nowego konta gościa. Przepływ użytkownika samoobsługowego tworzenia konta definiuje serię kroków wykonywanych przez użytkownika podczas tworzenia konta, dostawców tożsamości, którzy będą mogli korzystać, oraz atrybutów użytkowników, które mają być zbierane. Można skojarzyć jedną lub więcej aplikacji z jednym przepływem użytkownika.

> [!NOTE]
> Przepływy użytkowników można kojarzyć z aplikacjami skompilowanymi przez organizację. Przepływów użytkowników nie można używać dla aplikacji firmy Microsoft, takich jak SharePoint i zespoły.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

### <a name="add-identity-providers-optional"></a>Dodaj dostawców tożsamości (opcjonalnie)

Usługa Azure AD jest domyślnym dostawcą tożsamości dla samoobsługowego rejestrowania się. Oznacza to, że użytkownicy mogą rejestrować się domyślnie przy użyciu konta usługi Azure AD. W przypadku przepływów użytkowników korzystających z samoobsługowego rejestrowania możesz także dołączyć dostawców tożsamości społecznościowej, takich jak Google i Facebook, konto Microsoft (wersja zapoznawcza) i wiadomości E-mail jednorazowe (wersja zapoznawcza).

- [Dostawca tożsamości konta Microsoft (wersja zapoznawcza)](microsoft-account.md)
- [Wyślij wiadomość e-mail do jednorazowego uwierzytelniania kodu dostępu](one-time-passcode.md)
- [Dodawanie usługi Facebook do listy dostawców tożsamości społecznościowych](facebook-federation.md)
- [Dodaj firmę Google do listy dostawców tożsamości społecznościowych](google-federation.md)

### <a name="define-custom-attributes-optional"></a>Zdefiniuj atrybuty niestandardowe (opcjonalnie)

Atrybuty użytkownika są wartościami zbieranymi od użytkownika podczas samoobsługowego rejestrowania się. Usługa Azure AD jest dostarczana z wbudowanym zestawem atrybutów, ale można utworzyć niestandardowe atrybuty do użycia w przepływie użytkownika. Można również odczytywać i zapisywać te atrybuty przy użyciu interfejsu API Microsoft Graph. Zobacz [Definiowanie atrybutów niestandardowych dla przepływów użytkowników](user-flow-add-custom-attributes.md).

## <a name="enable-self-service-sign-up-for-your-tenant"></a>Włączanie rejestracji samoobsługowej dla dzierżawy

Przed dodaniem przepływu użytkownika samoobsługowego rejestrowania do aplikacji należy włączyć tę funkcję dla dzierżawy. Po włączeniu kontrolki staną się dostępne w przepływie użytkownika, które umożliwiają skojarzenie przepływu użytkownika z aplikacją.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator usługi Azure AD.
2. W obszarze **usługi platformy Azure** wybierz pozycję **Azure Active Directory**.
3. Wybierz pozycję **Ustawienia użytkownika**, a następnie w obszarze **użytkownicy zewnętrzni** wybierz pozycję **Zarządzaj ustawieniami współpracy zewnętrznej**.
4. Ustaw wartość **tak** dla opcji Włącz samoobsługowe **rejestrowanie dla gościa za pomocą przepływów użytkownika** .

   ![Włącz rejestrację samoobsługową gościa](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)
5. Wybierz pozycję **Zapisz**.
## <a name="create-the-user-flow-for-self-service-sign-up"></a>Tworzenie przepływu użytkownika na potrzeby rejestracji samoobsługowej

Następnie utworzysz przepływ użytkownika na potrzeby rejestracji samoobsługowej i dodasz go do aplikacji.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator usługi Azure AD.
2. W obszarze **usługi platformy Azure** wybierz pozycję **Azure Active Directory**.
3. W menu po lewej stronie wybierz pozycję **tożsamości zewnętrzne**.
4. Wybierz pozycję **przepływy użytkownika**, a następnie wybierz pozycję **Nowy przepływ użytkownika**.

   ![Dodaj nowy przycisk przepływu użytkownika](media/self-service-sign-up-user-flow/new-user-flow.png)

5. Na stronie **Tworzenie** wprowadź **nazwę** przepływu użytkownika. Należy pamiętać, że nazwa jest automatycznie prefiksem **B2X_1_**.
6. Na liście **dostawcy tożsamości** wybierz co najmniej jednego dostawcę tożsamości, którego użytkownicy zewnętrzni mogą używać do logowania się do aplikacji. **Konto Azure Active Directory** jest domyślnie zaznaczone. (Zobacz [przed rozpoczęciem](#before-you-begin) wcześniejszego działania w tym artykule, aby dowiedzieć się, jak dodać dostawców tożsamości).
7. W obszarze **atrybuty użytkownika** wybierz atrybuty, które mają być zbierane od użytkownika. Aby uzyskać dodatkowe atrybuty, wybierz pozycję **Pokaż więcej**. Na przykład wybierz pozycję **Pokaż więcej**, a następnie wybierz atrybuty i oświadczenia dla **kraju/regionu**, **Nazwa wyświetlana** i **Kod pocztowy**. Wybierz przycisk **OK**.

   ![Tworzenie nowej strony przepływu użytkownika](media/self-service-sign-up-user-flow/create-user-flow.png)

> [!NOTE]
> Można zbierać atrybuty tylko wtedy, gdy użytkownik loguje się po raz pierwszy. Po zarejestrowaniu się użytkownika nie będzie on już monitowany o zbieranie informacji o atrybutach, nawet w przypadku zmiany przepływu użytkownika.

8. Wybierz przycisk **Utwórz**.
9. Nowy przepływ użytkownika zostanie wyświetlony na liście **przepływy użytkownika** . W razie potrzeby Odśwież stronę.

## <a name="select-the-layout-of-the-attribute-collection-form"></a>Wybierz układ formularza kolekcji atrybutów

Możesz wybrać kolejność, w jakiej atrybuty są wyświetlane na stronie rejestracji. 

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Azure Active Directory**.
2. Wybierz pozycję **tożsamości zewnętrzne**, a następnie wybierz pozycję **przepływy użytkownika**.
3. Wybierz z listy przepływ użytkownika do samoobsługowego rejestrowania.
4. W obszarze **Dostosowywanie** wybierz pozycję **układy stron**.
5. Zostaną wyświetlone atrybuty wybrane do zebrania. Aby zmienić kolejność wyświetlania, wybierz atrybut, a następnie wybierz pozycję Przenieś w **górę**, **Przenieś w dół**, **Przenieś w górę** lub **Przenieś w** dół.
6. Wybierz pozycję **Zapisz**.

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>Dodawanie aplikacji do przepływu użytkownika samoobsługowego rejestrowania

Teraz można kojarzyć aplikacje z przepływem użytkownika.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator usługi Azure AD.
2. W obszarze **usługi platformy Azure** wybierz pozycję **Azure Active Directory**.
3. W menu po lewej stronie wybierz pozycję **tożsamości zewnętrzne**.
4. W obszarze **rejestracja samoobsługowa** wybierz pozycję **przepływy użytkownika**.
5. Wybierz z listy przepływ użytkownika do samoobsługowego rejestrowania.
6. W menu po lewej stronie w obszarze **Użyj** wybierz pozycję **aplikacje**.
7. Wybierz pozycję **Dodaj aplikację**.

   ![Przypisywanie aplikacji do przepływu użytkownika](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. Wybierz aplikację z listy. Lub użyj pola wyszukiwania, aby znaleźć aplikację, a następnie wybierz ją.
9. Kliknij pozycję **Wybierz**.

## <a name="next-steps"></a>Następne kroki

- [Dodaj firmę Google do listy dostawców tożsamości społecznościowych](google-federation.md)
- [Dodawanie usługi Facebook do listy dostawców tożsamości społecznościowych](facebook-federation.md)
- [Używanie łączników interfejsu API do dostosowywania i zwiększania przepływów użytkownika za pośrednictwem interfejsów API sieci Web](api-connectors-overview.md)
- [Dodawanie niestandardowego przepływu pracy zatwierdzenia do przepływu użytkownika](self-service-sign-up-add-approvals.md)
