---
title: Dodawanie użytkowników współpracy B2B jako pracownika przetwarzającego informacje — Azure AD
description: Współpraca B2B umożliwia pracownikom przetwarzającym informacje i właścicielom aplikacji Dodawanie użytkowników-Gości do usługi Azure AD w celu uzyskania dostępu | Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bc3175017e5b26251d1a12d0d1e2c51c4e5f9c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909744"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Jak użytkownicy w organizacji mogą zapraszać użytkowników-Gości do aplikacji

Po dodaniu użytkownika-gościa do katalogu w usłudze Azure AD właściciel aplikacji może wysłać użytkownikowi gość bezpośredni link do aplikacji, którą chcą udostępnić. Administratorzy usługi Azure AD mogą również skonfigurować Samoobsługowe zarządzanie dla galerii lub aplikacji opartych na protokole SAML w swojej dzierżawie usługi Azure AD. Dzięki temu właściciele aplikacji mogą zarządzać własnymi użytkownikami-Gośćmi, nawet jeśli nie zostały jeszcze dodane do katalogu. Gdy aplikacja jest skonfigurowana do samoobsługi, właściciel aplikacji korzysta z panelu dostępu, aby zaprosić użytkownika-gościa do aplikacji lub dodać użytkownika-gościa do grupy, która ma dostęp do aplikacji. Funkcja samoobsługowego zarządzania aplikacjami dla galerii i aplikacji opartych na protokole SAML wymaga instalacji wstępnej przez administratora. Poniżej znajduje się Podsumowanie kroków instalacji (Aby uzyskać bardziej szczegółowe instrukcje, zobacz [wymagania wstępne](#prerequisites) w dalszej części tej strony):

 - Włącz Samoobsługowe zarządzanie grupami dla Twojej dzierżawy
 - Tworzenie grupy do przypisania do aplikacji i nadaje użytkownikowi właściciela
 - Skonfiguruj aplikację do samoobsługi i Przypisz grupę do aplikacji

> [!NOTE]
> W tym artykule opisano sposób konfigurowania samoobsługowego zarządzania dla galerii i aplikacji opartych na protokole SAML, które zostały dodane do dzierżawy usługi Azure AD. Możesz również skonfigurować samoobsługowe [grupy Microsoft 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) , aby użytkownicy mogli zarządzać dostępem do własnych Microsoft 365 grup. Aby uzyskać więcej możliwości udostępniania plików pakietu Office i aplikacji użytkownikom-Gościom, zobacz [dostęp gościa w grupach Microsoft 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) i [udostępnianie plików lub folderów programu SharePoint](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Zapraszanie użytkownika-gościa do aplikacji z poziomu panelu dostępu

Po skonfigurowaniu aplikacji do samoobsługi właściciele aplikacji mogą korzystać z własnego panelu dostępu, aby zapraszać użytkownika-gościa do aplikacji, którą chcą udostępnić. Użytkownik-Gość nie musi być koniecznie dodawany do usługi Azure AD z wyprzedzeniem. 

1. Otwórz panel dostępu, przechodząc do `https://myapps.microsoft.com` .
2. Wskaż aplikację, wybierz wielokropek (**...**), a następnie wybierz pozycję **Zarządzaj aplikacją**.
 
   ![Zrzut ekranu przedstawiający podmenu Zarządzaj aplikacjami dla aplikacji Salesforce](media/add-users-iw/access-panel-manage-app.png)
 
3. W górnej części listy Użytkownicy wybierz pozycję **+** .
   
   ![Zrzut ekranu przedstawiający symbol Plus, aby dodać członków do aplikacji](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. W polu wyszukiwania **Dodaj członków** wpisz adres e-mail użytkownika-gościa. Opcjonalnie możesz dodać komunikat powitalny.
   
   ![Zrzut ekranu przedstawiający okno Dodawanie elementów członkowskich na potrzeby dodawania gościa](media/add-users-iw/access-panel-invitation.png)
   
5. Wybierz pozycję **Dodaj** , aby wysłać zaproszenie do użytkownika-gościa. Po wysłaniu zaproszenia konto użytkownika zostanie automatycznie dodane do katalogu jako gość.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Zapraszanie osoby do dołączenia do grupy mającej dostęp do aplikacji
Po skonfigurowaniu aplikacji do samoobsługi właściciele aplikacji mogą zapraszać użytkowników-Gości do zarządzanych przez nich grup, które mają dostęp do aplikacji, które chcą udostępnić. Użytkownicy-Goście nie muszą już znajdować się w katalogu. Właściciel aplikacji wykonuje następujące kroki, aby zaprosić użytkownika-gościa do grupy w celu uzyskania dostępu do aplikacji.

1. Upewnij się, że jesteś właścicielem grupy samoobsługi, która ma dostęp do aplikacji, którą chcesz udostępnić.
2. Otwórz panel dostępu, przechodząc do `https://myapps.microsoft.com` .
3. Wybierz aplikację **grupy** .
   
   ![Zrzut ekranu przedstawiający aplikację grupy w panelu dostępu](media/add-users-iw/access-panel-groups.png)
   
4. W obszarze **grupy I**wybierz grupę, która ma dostęp do aplikacji, którą chcesz udostępnić.
   
   ![Zrzut ekranu przedstawiający miejsce wybrania grupy w grupie I](media/add-users-iw/access-panel-groups-i-own.png)
   
5. W górnej części listy członków grupy wybierz pozycję **+** .
   
   ![Zrzut ekranu przedstawiający symbol Plus, który umożliwia dodawanie członków do grupy](media/add-users-iw/access-panel-groups-add-member.png)
   
6. W polu wyszukiwania **Dodaj członków** wpisz adres e-mail użytkownika-gościa. Opcjonalnie możesz dodać komunikat powitalny.
   
   ![Zrzut ekranu przedstawiający okno Dodawanie elementów członkowskich na potrzeby dodawania gościa](media/add-users-iw/access-panel-invitation.png)
   
7. Wybierz pozycję **Dodaj** , aby automatycznie wysłać zaproszenie do użytkownika-gościa. Po wysłaniu zaproszenia konto użytkownika zostanie automatycznie dodane do katalogu jako gość.


## <a name="prerequisites"></a>Wymagania wstępne

Samoobsługowe zarządzanie aplikacjami wymaga wprowadzenia wstępnej konfiguracji przez administratora globalnego i administratora usługi Azure AD. W ramach tej konfiguracji skonfigurujesz aplikację do samoobsługi i przypiszesz grupę do aplikacji, którą może zarządzać właściciel aplikacji. Możesz również skonfigurować grupę, aby umożliwić wszystkim użytkownikom zażądanie członkostwa, ale wymaganie zatwierdzenia przez właściciela grupy. (Dowiedz się więcej o funkcji samoobsługowego [zarządzania grupami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)). 

> [!NOTE]
> Nie można dodać użytkowników-Gości do grupy dynamicznej ani do grupy, która jest synchronizowana z lokalnym Active Directory.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Włącz Samoobsługowe zarządzanie grupami dla Twojej dzierżawy
1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny.
2. W panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.
3. Wybierz pozycję **grupy**.
4. W obszarze **Ustawienia**wybierz pozycję **Ogólne**.
5. W obszarze samoobsługowe **Zarządzanie grupami**obok **właścicieli można zarządzać żądaniami członkostwa w grupach w panelu dostępu**, wybierz opcję **tak**.
6. Wybierz pozycję **Zapisz**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Tworzenie grupy do przypisania do aplikacji i nadaje użytkownikowi właściciela
1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator usługi Azure AD lub Administrator globalny.
2. W panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.
3. Wybierz pozycję **grupy**.
4. Wybierz pozycję **Nowa grupa**.
5. W obszarze **Typ grupy**wybierz pozycję **zabezpieczenia**.
6. Wpisz odpowiedni tekst w polach **Nazwa grupy** i **Opis grupy**.
7. W obszarze **Typ członkostwa**wybierz pozycję **przypisano**.
8. Wybierz pozycję **Utwórz**, a następnie zamknij stronę **grupy** .
9. Na stronie **grupy — wszystkie grupy** Otwórz grupę. 
10. W obszarze **Zarządzanie**wybierz pozycję **właściciele**  >  **Dodaj właścicieli**. Wyszukaj użytkownika, który powinien zarządzać dostępem do aplikacji. Wybierz użytkownika, a następnie kliknij przycisk **Wybierz**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Skonfiguruj aplikację do samoobsługi i Przypisz grupę do aplikacji
1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator usługi Azure AD lub Administrator globalny.
2. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
3. W obszarze **Zarządzaj**wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
4. Znajdź aplikację i otwórz ją na liście aplikacji.
5. W obszarze **Zarządzaj**wybierz pozycję **Logowanie jednokrotne**i skonfiguruj aplikację do logowania jednokrotnego. (Aby uzyskać szczegółowe informacje, zobacz [jak zarządzać logowaniem jednokrotnym dla aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications)).
6. W obszarze **Zarządzanie**wybierz pozycję **samoobsługa**i Skonfiguruj dostęp do aplikacji samoobsługowej. (Aby uzyskać szczegółowe informacje, zobacz jak używać samoobsługowego [dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)). 

    > [!NOTE]
    > Aby określić, **do której grupy mają być dodawane użytkownicy?** wybierz grupę utworzoną w poprzedniej sekcji.
7. W obszarze **Zarządzaj**wybierz pozycję **Użytkownicy i grupy**, a następnie sprawdź, czy utworzona grupa samoobsługi zostanie wyświetlona na liście.
8. Aby dodać aplikację do panelu dostępu właściciela grupy, wybierz pozycję **Dodaj**  >  **użytkowników i grupy**użytkowników. Wyszukaj właściciela grupy i wybierz użytkownika, kliknij pozycję **Wybierz**, a następnie kliknij pozycję **Przypisz** , aby dodać użytkownika do aplikacji.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi współpracy B2B w usłudze Azure AD:

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Jak Administratorzy Azure Active Directory dodawać użytkowników współpracy B2B?](add-users-administrator.md)
- [Realizacja zaproszenia do współpracy B2B](redemption-experience.md)
- [Licencjonowanie funkcji współpracy B2B w usłudze Azure AD](licensing-guidance.md)
