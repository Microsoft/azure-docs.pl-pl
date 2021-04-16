---
title: Informacje na temat logowania jednokrotnego (SSO) opartego na hasłach dla aplikacji w Azure Active Directory
description: Informacje na temat logowania jednokrotnego (SSO) opartego na hasłach dla aplikacji w Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: iangithinji
ms.openlocfilehash: ffa517f068dbc13f2734630216466373d9014ae6
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374560"
---
# <a name="understand-password-based-single-sign-on"></a>Understand password-based single sign-on (Informacje na temat logowania pojedynczego opartego na hasłach)

W serii [przewodników Szybki](view-applications-portal.md) start dotyczącej zarządzania aplikacją opisano, jak używać usługi Azure AD jako dostawcy tożsamości dla aplikacji. W przewodniku Szybki start skonfigurujesz logowanie jednokrotne oparte na saml lub OIDC. Inną opcją jest logowanie jednokrotne oparte na hasłach. W tym artykule bardziej szczegółowo opisano opcję logowania jednokrotnego opartą na hasłach. 

Ta opcja jest dostępna dla każdej witryny internetowej ze stroną logowania HTML. Logowanie jednokrotne oparte na hasłach jest również nazywane magazynem haseł. Logowanie jednokrotne oparte na hasłach umożliwia zarządzanie dostępem użytkowników i hasłami do aplikacji internetowych, które nie obsługują federacji tożsamości. Jest to również przydatne, gdy kilku użytkowników musi udostępnić jedno konto, na przykład na kontach aplikacji mediów społecznościowych w organizacji.

Logowanie jednokrotne oparte na hasłach to doskonały sposób, aby szybko rozpocząć integrowanie aplikacji z usługą Azure AD i umożliwia:

- Włączanie logowania pojedynczego dla użytkowników przez bezpieczne przechowywanie i ponowne odtwarzanie nazw użytkowników i haseł

- Obsługa aplikacji wymagających wielu pól logowania dla aplikacji, które do logowania wymagają więcej niż tylko pól nazwy użytkownika i hasła

- Dostosowywanie etykiet pól nazwy użytkownika i hasła, które użytkownicy widzą Moje aplikacje [po](../user-help/my-apps-portal-end-user-access.md) wprowadzeniu poświadczeń

- Zezwalaj użytkownikom na podanie własnych nazw użytkowników i haseł dla wszystkich istniejących kont aplikacji, które wpisują ręcznie.

- Zezwalaj członce grupy biznesowej na określanie nazw użytkowników i haseł przypisanych do użytkownika przy użyciu funkcji [samoobsługowego dostępu do aplikacji](./manage-self-service-access.md)

-   Zezwalaj administratorowi na określenie nazwy użytkownika i hasła, które mają być używane przez osoby lub grupy podczas logowania się do aplikacji za pomocą funkcji Aktualizacji poświadczeń 

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Korzystanie z usługi Azure AD jako dostawcy tożsamości i konfigurowanie logowania jednokrotnego może być proste lub złożone w zależności od używanej aplikacji. Niektóre aplikacje można skonfigurować za pomocą zaledwie kilku akcji. Inne wymagają szczegółowej konfiguracji. Aby szybko uzyskać wiedzę, należy przejść przez [serię przewodników Szybki start](view-applications-portal.md) na temat zarządzania aplikacją. Jeśli aplikacja, która dodajesz, jest prosta, prawdopodobnie nie musisz czytać tego artykułu. Jeśli dodawania aplikacji wymaga konfiguracji niestandardowej i musisz używać logowania jednokrotnego opartego na hasłach, ten artykuł jest dla Ciebie.

> [!IMPORTANT] 
> Istnieją pewne scenariusze, w **których opcja** logowania pojedynczego nie będzie w nawigacji dla aplikacji w **aplikacjach dla przedsiębiorstw.** 
>
> Jeśli aplikacja została zarejestrowana przy użyciu **Rejestracje aplikacji,** funkcja logowania pojedynczego jest skonfigurowana do domyślnego używania OIDC OAuth. W takim przypadku opcja **Logowanie pojedyncze** nie będzie pokazywana w obszarze nawigacji w obszarze Aplikacje **dla przedsiębiorstw.** Gdy używasz **Rejestracje aplikacji** do dodawania aplikacji niestandardowej, konfigurujesz opcje w pliku manifestu. Aby dowiedzieć się więcej na temat pliku manifestu, [zobacz Azure Active Directory manifestu aplikacji](../develop/reference-app-manifest.md). Aby dowiedzieć się więcej na temat standardów logowania jednokrotnego, zobacz [Authentication and authorization using Microsoft identity platform (Uwierzytelnianie i autoryzacja przy użyciu platformy tożsamości firmy Microsoft).](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Inne scenariusze, w których w nawigacji brakuje logowania pojedynczego, obejmują sytuacje, w których aplikacja jest hostowana w innej dzierżawie lub jeśli Konto nie ma wymaganych uprawnień (administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi).  Uprawnienia mogą również spowodować scenariusz, w którym można otworzyć logowanie **pojedyncze,** ale nie będzie można go zapisać. Aby dowiedzieć się więcej na temat ról administracyjnych usługi Azure AD, zobacz ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-configuration"></a>Konfiguracja podstawowa

W serii [przewodników](view-applications-portal.md)Szybki start opisano sposób dodawania aplikacji do dzierżawy, co umożliwia usłudze Azure AD określenie, że jest ona używana jako dostawca tożsamości dla aplikacji. Niektóre aplikacje są już wstępnie skonfigurowane i są wyświetlane w galerii usługi Azure AD. Inne aplikacje nie znajdują się w galerii i musisz utworzyć aplikację ogólną i skonfigurować ją ręcznie. W zależności od aplikacji opcja logowania jednokrotnego oparta na hasłach może nie być dostępna. Jeśli nie widzisz listy opcji opartych na hasłach na stronie logowania pojedynczego dla aplikacji, nie jest ona dostępna.

> [!IMPORTANT]
> Rozszerzenie Moje aplikacje jest wymagane w przypadku logowania jednokrotnego opartego na hasłach. Aby dowiedzieć się więcej, zobacz [Planowanie Moje aplikacje wdrożenia.](my-apps-deployment-plan.md)

Strona konfiguracji logowania jednokrotnego opartego na hasłach jest prosta. Zawiera tylko adres URL strony logowania, z których korzysta aplikacja. Ten ciąg musi być stroną, która zawiera pole wprowadzania nazwy użytkownika.

Po wprowadzeniu adresu URL wybierz pozycję **Zapisz**. Usługa Azure AD analizuje kod HTML strony logowania dla pól wejściowych nazwy użytkownika i hasła. Jeśli próba zakończy się pomyślnie, wszystko będzie gotowe.
 
Następnym krokiem jest [przypisanie użytkowników lub grup do aplikacji](./assign-user-or-group-access-portal.md). Po przypisaniu użytkowników i grup możesz podać poświadczenia, które będą używane dla użytkownika podczas logowania się do aplikacji. Wybierz **pozycję Użytkownicy i** grupy, zaznacz pole wyboru wiersza użytkownika lub grupy, a następnie wybierz pozycję Zaktualizuj **poświadczenia.** Na koniec wprowadź nazwę użytkownika i hasło, które będą używane dla użytkownika lub grupy. Jeśli tego nie zimkniesz, użytkownicy będą monitowali o wprowadzenie poświadczeń samodzielnie podczas uruchamiania.
 

## <a name="manual-configuration"></a>Konfiguracja ręczna

Jeśli próba analizy w usłudze Azure AD zakończy się niepowodzeniem, możesz skonfigurować logowanie ręcznie.

1. W **\<application name> obszarze** Konfiguracja wybierz pozycję Konfiguruj ustawienia logowania **pojedynczego \<application name> hasła,** aby wyświetlić **stronę Konfigurowanie** logowania. 

2. Wybierz **pozycję Ręcznie wykryj pola logowania.** Zostaną wyświetlone dodatkowe instrukcje opisujące ręczne wykrywanie pól logowania.

   ![Ręczna konfiguracja logowania pojedynczego opartego na hasłach](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Wybierz **pozycję Przechwyć pola logowania.** Na nowej karcie zostanie otwarta strona ze stanem przechwytywania pokazująca, że przechwytywanie metadanych komunikatu **jest obecnie w toku.**

4. Jeśli na **nowej Moje aplikacje** pojawi się pole Wymagane  rozszerzenie rozszerzenia logowania, wybierz pozycję Zainstaluj teraz, aby zainstalować rozszerzenie Moje aplikacje przeglądarki **z rozszerzeniem bezpiecznego** logowania. (Rozszerzenie przeglądarki wymaga Microsoft Edge, Chrome lub Firefox). Następnie zainstaluj, uruchom i włącz rozszerzenie, a następnie odśwież stronę stanu przechwytywania.

   Rozszerzenie przeglądarki otwiera kolejną kartę z wprowadzonym adresem URL.
5. Na karcie z wprowadzonym adresem URL przejdź przez proces logowania. Wypełnij pola nazwy użytkownika i hasła i spróbuj się zalogować. (Nie musisz po prostu podać poprawnego hasła).

   Zostanie wyświetlony monit o zapisanie przechwyconych pól logowania.
6. Wybierz przycisk **OK**. Rozszerzenie przeglądarki aktualizuje stronę stanu przechwytywania przy użyciu komunikatu **Metadane został zaktualizowany dla aplikacji**. Karta przeglądarki jest zamykana.

7. Na stronie logowania konfigurowanie **usługi** Azure AD wybierz przycisk OK. Udało mi się pomyślnie zalogować **do aplikacji.**

8. Wybierz przycisk **OK**.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie użytkowników lub grup do aplikacji](./assign-user-or-group-access-portal.md)
- [Konfigurowanie automatycznego aprowizowania kont użytkowników](../app-provisioning/configure-automatic-user-provisioning-portal.md)
