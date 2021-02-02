---
title: Opis logowania jednokrotnego opartego na haśle (SSO) dla aplikacji w Azure Active Directory
description: Opis logowania jednokrotnego opartego na haśle (SSO) dla aplikacji w Azure Active Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: kenwith
ms.openlocfilehash: bac04bd70469d7b9c4d9485b6a87fd7133967154
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255309"
---
# <a name="understand-password-based-single-sign-on"></a>Informacje na temat logowania jednokrotnego opartego na hasłach

W [serii szybkiego startu](view-applications-portal.md) w zarządzaniu aplikacjami wiesz, jak używać usługi Azure AD jako dostawcy tożsamości (dostawcy tożsamości) dla aplikacji. W przewodniku szybki start opisano konfigurowanie logowania jednokrotnego opartego na protokole SAML lub OIDC. Inną opcją jest logowanie jednokrotne oparte na haśle. W tym artykule opisano opcję logowania jednokrotnego opartego na hasłach. 

Ta opcja jest dostępna dla dowolnej witryny sieci Web ze stroną logowania w formacie HTML. Logowanie jednokrotne oparte na hasłach jest również nazywane magazynem haseł. Logowanie jednokrotne oparte na hasłach umożliwia zarządzanie dostępem użytkowników i hasłami do aplikacji sieci Web, które nie obsługują federacji tożsamości. Jest on również przydatny, gdy kilku użytkowników musi udostępniać pojedyncze konto, na przykład na kontach aplikacji mediów społecznościowych w organizacji.

Logowanie jednokrotne oparte na hasłach to doskonały sposób, aby szybko rozpocząć Integrowanie aplikacji z usługą Azure AD, dzięki czemu można:

- Włączanie logowania jednokrotnego dla użytkowników przez bezpieczne przechowywanie i odtwarzanie nazw użytkowników i haseł

- Obsługa aplikacji, które wymagają wielu pól logowania dla aplikacji, które wymagają więcej niż tylko nazwy użytkownika i hasła do logowania

- Dostosuj etykiety pól username i Password, które użytkownicy widzą w [moich aplikacjach](../user-help/my-apps-portal-end-user-access.md) po wprowadzeniu poświadczeń

- Zezwól użytkownikom na udostępnianie własnych nazw użytkowników i haseł dla wszystkich istniejących kont aplikacji, które są wpisywane ręcznie.

- Zezwól członkowi grupy biznesowej na określanie nazw użytkowników i haseł przypisanych do użytkownika przy użyciu funkcji samoobsługowego [dostępu do aplikacji](./manage-self-service-access.md)

-   Umożliwia administratorowi określenie nazwy użytkownika i hasła, które mają być używane przez osoby lub grupy podczas logowania się do aplikacji za pomocą funkcji aktualizacji poświadczeń 

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Korzystanie z usługi Azure AD jako dostawcy tożsamości (dostawcy tożsamości) i Konfigurowanie logowania jednokrotnego (SSO) może być proste lub złożone w zależności od używanej aplikacji. Niektóre aplikacje można skonfigurować za pomocą zaledwie kilku akcji. Inne wymagają konfiguracji szczegółowej. Aby szybko uzyskać informacje, zapoznaj się z [serią szybkiego startu](view-applications-portal.md) w zarządzaniu aplikacjami. Jeśli dodawana aplikacja jest prosta, prawdopodobnie nie musisz czytać tego artykułu. Jeśli dodawana aplikacja wymaga konfiguracji niestandardowej i musisz użyć logowania jednokrotnego opartego na hasłach, ten artykuł jest dla Ciebie.

> [!IMPORTANT] 
> Istnieją sytuacje, w których opcja **logowania** jednokrotnego nie będzie w nawigacji dla aplikacji w **aplikacjach dla przedsiębiorstw**. 
>
> Jeśli aplikacja została zarejestrowana przy użyciu **rejestracje aplikacji** , funkcja logowania jednokrotnego jest domyślnie skonfigurowana do używania protokołu OAuth OIDC. W takim przypadku opcja **logowania** jednokrotnego nie będzie widoczna w obszarze nawigacji w obszarze **aplikacje dla przedsiębiorstw**. W przypadku dodawania niestandardowej aplikacji przy użyciu **rejestracje aplikacji** można skonfigurować opcje w pliku manifestu. Aby dowiedzieć się więcej na temat pliku manifestu, zobacz [Azure Active Directory manifest aplikacji](../develop/reference-app-manifest.md). Aby dowiedzieć się więcej na temat standardów rejestracji jednokrotnej, zobacz [uwierzytelnianie i autoryzacja przy użyciu platformy tożsamości firmy Microsoft](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform). 
>
> Inne scenariusze, w których nie będzie można korzystać z **logowania** jednokrotnego w nawigacji, obejmują, gdy aplikacja jest hostowana w innej dzierżawie lub że Twoje konto nie ma wymaganych uprawnień (Administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi). Uprawnienia mogą również prowadzić do scenariusza, w którym można otworzyć **Logowanie jednokrotne** , ale nie będzie można go zapisać. Aby dowiedzieć się więcej na temat ról administracyjnych usługi Azure AD, zobacz https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-configuration"></a>Konfiguracja podstawowa

W [serii szybkiego startu](view-applications-portal.md)dowiesz się, jak dodać aplikację do dzierżawy, dzięki czemu usługa Azure AD wie, że jest ona używana jako dostawca tożsamości (dostawcy tożsamości) dla aplikacji. Niektóre aplikacje są już wstępnie skonfigurowane i są wyświetlane w galerii usługi Azure AD. Inne aplikacje nie znajdują się w galerii i musisz utworzyć aplikację rodzajową i skonfigurować ją ręcznie. W zależności od aplikacji opcja logowania jednokrotnego opartego na hasłach może być niedostępna. Jeśli lista opcji oparta na haśle nie jest widoczna na stronie logowania jednokrotnego dla aplikacji, jest niedostępna.

> [!IMPORTANT]
> Rozszerzenie przeglądarki Moje aplikacje jest wymagane w przypadku logowania jednokrotnego opartego na hasłach. Aby dowiedzieć się więcej, zobacz [Planowanie wdrożenia moje aplikacje](my-apps-deployment-plan.md).

Strona konfiguracja logowania jednokrotnego na podstawie hasła jest prosta. Zawiera tylko adres URL strony logowania używanej przez aplikację. Ten ciąg musi być stroną, która zawiera pole wprowadzania nazwy użytkownika.

Po wprowadzeniu adresu URL wybierz pozycję **Zapisz**. Usługa Azure AD analizuje kod HTML strony logowania dla pól wprowadzania nazwy użytkownika i hasła. Jeśli próba powiedzie się, wszystko jest gotowe.
 
Następnym krokiem jest [przypisanie użytkowników lub grup do aplikacji](./assign-user-or-group-access-portal.md). Po przypisaniu użytkowników i grup można podać poświadczenia, które będą używane dla użytkownika podczas logowania się do aplikacji. Wybierz pozycję **Użytkownicy i grupy**, zaznacz pole wyboru dla wiersza użytkownika lub grupy, a następnie wybierz pozycję **Aktualizuj poświadczenia**. Na koniec wprowadź nazwę użytkownika i hasło, które mają być używane dla użytkownika lub grupy. Jeśli tego nie zrobisz, użytkownicy otrzymają monit o wprowadzenie poświadczeń podczas uruchamiania.
 

## <a name="manual-configuration"></a>Konfiguracja ręczna

Jeśli próba analizy usługi Azure AD nie powiedzie się, można skonfigurować logowanie ręcznie.

1. W obszarze **\<application name> Konfiguracja** wybierz pozycję **Skonfiguruj \<application name> Ustawienia logowania** jednokrotnego hasła, aby wyświetlić stronę **Konfigurowanie logowania** . 

2. Wybierz pozycję **ręcznie Wykryj pola logowania**. Pojawią się dodatkowe instrukcje opisujące Ręczne wykrywanie pól logowania.

   ![Ręczna konfiguracja logowania jednokrotnego opartego na hasłach](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Wybierz pozycję **Przechwyć pola logowania**. Zostanie otwarta strona stanu przechwytywania na nowej karcie, w której jest wyświetlany komunikat **przechwytywanie metadanych komunikatów jest obecnie w toku**.

4. Jeśli pole wyboru **Moje aplikacje jest wymagane** na nowej karcie, wybierz pozycję **Zainstaluj teraz** , aby zainstalować rozszerzenie przeglądarki **Moje aplikacje bezpieczne logowanie** . (Rozszerzenie przeglądarki wymaga programu Microsoft Edge, Chrome lub Firefox). Następnie zainstaluj, Uruchom i Włącz rozszerzenie, a następnie Odśwież stronę stanu przechwytywania.

   Następnie rozszerzenie przeglądarki otwiera kolejną kartę, która wyświetla wprowadzony adres URL.
5. Na karcie z podanym adresem URL przejdź przez proces logowania. Wypełnij pola Nazwa użytkownika i hasło, a następnie spróbuj się zalogować. (Nie musisz podawać prawidłowego hasła).

   Zostanie wyświetlony monit z pytaniem o zapisanie przechwyconych pól logowania.
6. Wybierz przycisk **OK**. Rozszerzenie przeglądarki aktualizuje stronę ze stanem przechwytywania przy użyciu metadanych komunikatów, które **zostały zaktualizowane dla aplikacji**. Karta przeglądarka zostanie zamknięta.

7. Na stronie Logowanie do **konfiguracji** usługi Azure AD wybierz pozycję OK. udało **Ci się pomyślnie zalogować się do aplikacji**.

8. Wybierz przycisk **OK**.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie użytkowników lub grup do aplikacji](./assign-user-or-group-access-portal.md)
- [Konfigurowanie automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/configure-automatic-user-provisioning-portal.md)
