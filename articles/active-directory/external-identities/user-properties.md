---
title: Właściwości użytkownika-gościa B2B — Azure Active Directory | Microsoft Docs
description: Azure Active Directory właściwości użytkownika-gościa B2B i Stany przed i po wykonaniu zaproszenia
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd0a2b44fd54eb716b5e1b8f9eabc923ccd7977f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285851"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Właściwości Azure Active Directory użytkownika współpracy B2B

W tym artykule opisano właściwości i Stany obiektu użytkownika Gość B2B w Azure Active Directory (Azure AD) przed i po wykonaniu zaproszenia. Użytkownik współpracy z usługą Azure AD Business-to-Business (B2B) jest użytkownikiem o nazwie UserType = gość. Ten użytkownik-Gość zazwyczaj należy do organizacji partnera i ma domyślnie ograniczone uprawnienia w katalogu zapraszania.

W zależności od potrzeb organizacji użytkownik współpracy B2B usługi Azure AD może mieć jeden z następujących stanów kont:

- Stan 1: domowa w zewnętrznym wystąpieniu usługi Azure AD i reprezentowana jako użytkownik-Gość w organizacji zapraszanie. W takim przypadku użytkownik B2B loguje się przy użyciu konta usługi Azure AD należącego do zaproszonej dzierżawy. Jeśli organizacja partnera nie korzysta z usługi Azure AD, nadal jest tworzony użytkownik-Gość w usłudze Azure AD. Wymagania polegają na tym, że zrealizują swoje zaproszenia, a usługa Azure AD weryfikuje swój adres e-mail. To rozwiązanie jest również nazywane dzierżawcą just-in-Time (JIT) lub dzierżawą "wirusową".

   > [!IMPORTANT]
   > **Od 31 marca 2021** firma Microsoft nie będzie już obsługiwać wykupu zaproszeń przez tworzenie niezarządzanych kont usługi Azure AD i dzierżawców na potrzeby scenariuszy współpracy B2B. W przygotowaniu Zachęcamy klientów do samodzielnego [uwierzytelniania kodu dostępu za pośrednictwem poczty e-mail](one-time-passcode.md). Prosimy o opinię na temat tej publicznej funkcji w wersji zapoznawczej i przyjemnością, aby utworzyć jeszcze więcej sposobów współpracy.

- Stan 2: nastawione na konto Microsoft lub inne i reprezentowane jako użytkownik-Gość w organizacji hosta. W takim przypadku użytkownik Gość loguje się przy użyciu konto Microsoft lub konta społecznościowego (google.com lub podobny). Tożsamość zaproszonego użytkownika jest tworzona jako konto Microsoft w katalogu zapraszanej organizacji podczas realizacji oferty.

- Stan 3: adres lokalny Active Directory organizacji hosta i zsynchronizowany z usługą Azure AD w organizacji hosta. Za pomocą Azure AD Connect można synchronizować konta partnerów z chmurą jako użytkownicy usługi Azure AD B2B z elementem UserType = gość. Zobacz [udzielanie dostępu do zasobów w chmurze przez zarządzane lokalnie konta partnerów](hybrid-on-premises-to-cloud.md).

- Stan 4: adres zamieszkania w usłudze Azure AD w organizacji hosta z elementem UserType = gość i poświadczeniami zarządzanymi przez organizację hosta.

  ![Diagram przedstawiający cztery Stany użytkownika](media/user-properties/redemption-diagram.png)


Teraz zobaczmy, jak wygląda użytkownik współpracy B2B usługi Azure AD w usłudze Azure AD.

### <a name="before-invitation-redemption"></a>Przed wykupumi zaproszeń

Konta o stanie 1 i 2 są wynikiem zapraszania użytkowników-Gości do współpracy przy użyciu własnych poświadczeń użytkowników-Gości. Po wstępnym wysłaniu zaproszenia do użytkownika-gościa w katalogu zostanie utworzone konto. Z tym kontem nie są skojarzone żadne poświadczenia, ponieważ uwierzytelnianie jest wykonywane przez dostawcę tożsamości użytkownika-gościa. Właściwość **Source** konta użytkownika-gościa w katalogu jest ustawiona na **zaproszony użytkownik**. 

![Zrzut ekranu przedstawiający właściwości użytkownika przed realizacją oferty](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Po wykonaniu zaproszenia

Po zaakceptowaniu zaproszenia przez użytkownika-Gość Właściwość **Source** zostanie zaktualizowana na podstawie dostawcy tożsamości użytkownika-gościa.

Dla użytkowników-Gości w stanie 1 **źródłem** jest **zewnętrzna Azure Active Directory**.

![Użytkownik-Gość o stanie 1 po utworzeniu oferty](media/user-properties/after-redemption-state1.png)

Dla użytkowników-Gości w stanie 2, **źródłem** jest **konto Microsoft**.

![Użytkownik-Gość stanu 2 po utworzeniu oferty](media/user-properties/after-redemption-state2.png)

Dla użytkowników-Gości w stanie 3 i stan 4 Właściwość **Source** ma wartość **Azure Active Directory** lub **Windows Server Active Directory** , zgodnie z opisem w następnej sekcji.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Właściwości klucza użytkownika współpracy B2B usługi Azure AD
### <a name="usertype"></a>UserType
Ta właściwość wskazuje relację użytkownika z dzierżawcą hosta. Ta właściwość może mieć dwie wartości:
- Członek: Ta wartość wskazuje pracownika organizacji hosta i użytkownika z listy płac w organizacji. Na przykład ten użytkownik oczekuje na dostęp do wewnętrznych witryn. Ten użytkownik nie jest traktowany jako współpracownik zewnętrzny.

- Gość: Ta wartość wskazuje użytkownika, który nie jest uznawany za wewnętrzny dla firmy, taki jak zewnętrzny współpracownik, partner lub klient. Użytkownik nie powinien otrzymać na przykład wewnętrznej noty dyrektora naczelnego ani otrzymać korzyści firmy.

  > [!NOTE]
  > Użytkownik nie ma żadnego powiązania z logowaniem użytkownika, rolą katalogu użytkownika i tak dalej. Ta właściwość po prostu wskazuje relację użytkownika z organizacją hosta i umożliwia organizacji wymuszanie zasad, które są zależne od tej właściwości.

Aby uzyskać szczegółowe informacje dotyczące cennika, zapoznaj się z [cennikiem Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

### <a name="source"></a>Źródło
Ta właściwość wskazuje, w jaki sposób użytkownik loguje się.

- Zaproszony użytkownik: ten użytkownik został zaproszony, ale jeszcze nie zakończył zaproszenia.

- Azure Active Directory zewnętrzne: ten użytkownik należy do organizacji zewnętrznej i uwierzytelnia się przy użyciu konta usługi Azure AD należącego do innej organizacji. Ten typ logowania odpowiada stanie 1.

- Konto Microsoft: ten użytkownik jest w konto Microsoft i uwierzytelniany przy użyciu konto Microsoft. Ten typ logowania odpowiada stanie 2.

- Active Directory systemu Windows Server: ten użytkownik jest zalogowany z lokalnej Active Directory należącej do tej organizacji. Ten typ logowania odpowiada stanowi stanu 3.

- Azure Active Directory: ten użytkownik jest uwierzytelniany przy użyciu konta usługi Azure AD należącego do tej organizacji. Ten typ logowania odpowiada stanowi 4.
  > [!NOTE]
  > Właściwości source i UserType są niezależne. Wartość źródła nie implikuje określonej wartości dla elementu UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Czy użytkownicy B2B usługi Azure AD mogą dodawać jako członków zamiast Gości?
Zazwyczaj użytkownik B2B i gość usługi Azure AD jest tożsamy. W związku z tym użytkownik współpracy B2B usługi Azure AD jest domyślnie dodawany jako użytkownik, który jest użytkownikiem = gość. Jednak w niektórych przypadkach organizacja partnera jest członkiem większej organizacji, do której należy również organizacja hosta. Jeśli tak, organizacja hosta może chcieć traktować użytkowników w organizacji partnerskiej jako członków zamiast Gości. Użyj interfejsów API Menedżera zaproszeń B2B usługi Azure AD, aby dodać lub zaprosić użytkownika z organizacji partnera do organizacji hosta jako członka.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtrowanie dla użytkowników-Gości w katalogu

![Zrzut ekranu przedstawiający filtr dla użytkowników-Gości](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Konwertuj użytkownika
Istnieje możliwość konwersji typu UserType z elementu członkowskiego na gościa i odwrotnie przy użyciu programu PowerShell. Jednak Właściwość UserType reprezentuje relację użytkownika z organizacją. W związku z tym należy zmienić tę właściwość tylko wtedy, gdy zmieni się relacja użytkownika z organizacją. Jeśli relacja użytkownika ulegnie zmianie, powinna zostać zmieniona nazwa główna użytkownika (UPN)? Czy użytkownik powinien nadal mieć dostęp do tych samych zasobów? Czy chcesz przypisać skrzynkę pocztową? Nie zalecamy zmiany elementu UserType przy użyciu programu PowerShell jako działania niepodzielnego. Ponadto w przypadku, gdy ta właściwość zmienia się w sposób niezmienny przy użyciu programu PowerShell, nie zalecamy wykonywania zależności od tej wartości.

## <a name="remove-guest-user-limitations"></a>Usuń ograniczenia użytkownika-gościa
Mogą jednak wystąpić sytuacje, w których użytkownicy-Goście mają wyższe uprawnienia. Możesz dodać użytkownika-gościa do dowolnej roli, a nawet usunąć domyślne ograniczenia użytkownika-gościa w katalogu, aby nadać użytkownikowi te same uprawnienia co członkowie.

Istnieje możliwość wyłączenia ograniczeń domyślnych, aby użytkownik-Gość w katalogu firmy miał takie same uprawnienia, jak użytkownik będący członkiem.

![Zrzut ekranu przedstawiający opcję użytkowników zewnętrznych w ustawieniach użytkownika](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Czy można sprawić, aby użytkownicy-Goście widoczni na globalnej liście adresów programu Exchange?
Tak. Domyślnie obiekty gościa nie są widoczne na globalnej liście adresowej organizacji, ale można użyć programu Azure Active Directory PowerShell, aby je wyświetlić. Aby uzyskać szczegółowe informacje, zobacz temat **czy obiekty gościa są widoczne na globalnej liście adresów?** w obszarze [Zarządzanie dostępem gościa w grupach Microsoft 365](/office365/admin/create-groups/manage-guest-access-in-groups).

## <a name="can-i-update-a-guest-users-email-address"></a>Czy mogę zaktualizować adres e-mail użytkownika-gościa?

Jeśli użytkownik-Gość zaakceptuje Twoje zaproszenie i zmieni swój adres e-mail, nowy adres e-mail nie zostanie automatycznie zsynchronizowany z obiektem użytkownika-gościa w katalogu. Właściwość mail jest tworzona za pośrednictwem [interfejsu API Microsoft Graph](/graph/api/resources/user?view=graph-rest-1.0). Możesz zaktualizować Właściwość poczty za pośrednictwem interfejsu API Microsoft Graph, centrum administracyjnego programu Exchange lub programu [Exchange Online PowerShell](/powershell/module/exchange/users-and-groups/set-mailuser?view=exchange-ps). Zmiana zostanie odzwierciedlona w obiekcie użytkownika gościa usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Tokeny użytkownika współpracy B2B](user-token.md)
* [Mapowanie oświadczeń użytkowników współpracy B2B](claims-mapping.md)
