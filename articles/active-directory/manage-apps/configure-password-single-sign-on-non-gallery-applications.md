---
title: Jak skonfigurować Logowanie jednokrotne haseł dla aplikacji usługi Azure AD | Microsoft Docs
description: Jak skonfigurować Logowanie jednokrotne (SSO) haseł do aplikacji usługi Azure AD dla przedsiębiorstw w usłudze Microsoft Identity platform (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/10/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 043adc309c3480865eb9aa7a7bff8d35e85bc78a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763503"
---
# <a name="configure-password-single-sign-on"></a>Konfigurowanie logowania jednokrotnego przy użyciu hasła

Po [dodaniu aplikacji galerii](add-gallery-app.md) lub [aplikacji sieci Web bez galerii](add-non-gallery-app.md) do aplikacji usługi Azure AD Enterprise jedną z opcji logowania jednokrotnego jest dostęp do [logowania jednokrotnego opartego na hasłach](what-is-single-sign-on.md#password-based-sso). Ta opcja jest dostępna dla dowolnej sieci Web ze stroną logowania w formacie HTML. Logowanie jednokrotne oparte na hasłach, nazywane również magazynem haseł, umożliwia zarządzanie dostępem użytkowników i hasłami do aplikacji sieci Web, które nie obsługują federacji tożsamości. Jest to również przydatne w scenariuszach, w których kilku użytkowników musi udostępniać pojedyncze konto, na przykład na kontach aplikacji Media społecznościowych w organizacji. 

Logowanie jednokrotne oparte na hasłach to doskonały sposób, aby szybko rozpocząć Integrowanie aplikacji z usługą Azure AD, dzięki czemu można:

-   Włączanie **logowania jednokrotnego dla użytkowników** przez bezpieczne przechowywanie i odtwarzanie nazw użytkownika i haseł dla aplikacji zintegrowanej z usługą Azure AD

-   **Obsługa aplikacji, które wymagają wielu pól logowania** dla aplikacji, które wymagają więcej niż tylko nazwy użytkownika i hasła do logowania

-   **Dostosuj etykiety** pól wprowadzania nazwy użytkownika i hasła, które użytkownicy widzą w [panelu dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) po wprowadzeniu poświadczeń

-   Zezwól **użytkownikom** na udostępnianie własnych nazw użytkowników i haseł dla wszystkich istniejących kont aplikacji, które są wpisywane ręcznie w [panelu dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Zezwól **członkowi grupy biznesowej** na określanie nazw użytkowników i haseł przypisanych do użytkownika przy użyciu funkcji samoobsługowego [dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Zezwalaj **administratorowi** na określenie nazwy użytkownika i hasła, które mają być używane przez osoby lub grupy podczas logowania się do aplikacji za pomocą funkcji aktualizacji poświadczeń 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli aplikacja nie została dodana do dzierżawy usługi Azure AD, zobacz [Dodawanie aplikacji galerii](add-gallery-app.md) lub Dodawanie aplikacji niebędącej [galerią](add-non-gallery-app.md).

## <a name="open-the-app-and-select-password-single-sign-on"></a>Otwórz aplikację i wybierz pozycję Logowanie jednokrotne hasła

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator aplikacji w chmurze lub administrator aplikacji dla dzierżawy usługi Azure AD.

2. Przejdź do **Azure Active Directory**  >  **aplikacji przedsiębiorstwa**. Zostanie wyświetlona Losowa przykład aplikacji w dzierżawie usługi Azure AD. 

3. W menu **Typ aplikacji** wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Zastosuj**.

4. Wprowadź nazwę aplikacji w polu wyszukiwania, a następnie wybierz aplikację z wyników.

5. W sekcji **Zarządzanie** wybierz pozycję **Logowanie jednokrotne**. 

6. Wybierz pozycję **oparte na hasłach**.

7. Wprowadź adres URL strony logowania opartej na sieci Web aplikacji. Ten ciąg musi być stroną, która zawiera pole wprowadzania nazwy użytkownika.

   ![Logowanie jednokrotne oparte na hasłach](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Wybierz pozycję **Zapisz**. Usługa Azure AD próbuje przeanalizować stronę logowania dla danych wejściowych nazwy użytkownika i hasła. Jeśli próba powiedzie się, wszystko jest gotowe. 
 
> [!NOTE]
> Następnym krokiem jest [przypisanie użytkowników lub grup do aplikacji](methods-for-assigning-users-and-groups.md). Po przypisaniu użytkowników i grup można podać poświadczenia, które będą używane w imieniu użytkownika podczas logowania się do aplikacji. Wybierz pozycję **Użytkownicy i grupy**, zaznacz pole wyboru dla wiersza użytkownika lub grupy, a następnie kliknij przycisk **Aktualizuj poświadczenia**. Następnie wprowadź nazwę użytkownika i hasło, które będą używane w imieniu użytkownika lub grupy. W przeciwnym razie użytkownicy otrzymają monit o wprowadzenie poświadczeń podczas uruchamiania.
 

## <a name="manual-configuration"></a>Konfiguracja ręczna

Jeśli próba analizy usługi Azure AD nie powiedzie się, można skonfigurować logowanie ręcznie.

1. W obszarze ** \<application name> Konfiguracja**wybierz pozycję **Skonfiguruj \<application name> Ustawienia logowania** jednokrotnego hasła, aby wyświetlić stronę **Konfigurowanie logowania** . 

2. Wybierz pozycję **ręcznie Wykryj pola logowania**. Pojawią się dodatkowe instrukcje opisujące Ręczne wykrywanie pól logowania.

   ![Ręczna konfiguracja logowania jednokrotnego opartego na hasłach](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Wybierz pozycję **Przechwyć pola logowania**. Zostanie otwarta strona stanu przechwytywania na nowej karcie, w której jest wyświetlany komunikat **przechwytywanie metadanych komunikatów jest obecnie w toku**.

4. Jeśli pole wyboru **wymagane rozszerzenie panelu dostępu** pojawia się na nowej karcie, wybierz pozycję **Zainstaluj teraz** , aby zainstalować rozszerzenie przeglądarki **Moje aplikacje bezpieczne logowanie** . (Rozszerzenie przeglądarki wymaga programu Microsoft Edge, Chrome lub Firefox). Następnie zainstaluj, Uruchom i Włącz rozszerzenie, a następnie Odśwież stronę stanu przechwytywania.

   Następnie rozszerzenie przeglądarki otwiera kolejną kartę, która wyświetla wprowadzony adres URL.
5. Na karcie z podanym adresem URL przejdź przez proces logowania. Wypełnij pola Nazwa użytkownika i hasło, a następnie spróbuj się zalogować. (Nie musisz podawać prawidłowego hasła).

   Zostanie wyświetlony monit z pytaniem o zapisanie przechwyconych pól logowania.
6. Wybierz przycisk **OK**. Rozszerzenie przeglądarki aktualizuje stronę ze stanem przechwytywania przy użyciu metadanych komunikatów, które **zostały zaktualizowane dla aplikacji**. Karta przeglądarka zostanie zamknięta.

7. Na stronie Logowanie do **konfiguracji** usługi Azure AD wybierz pozycję OK. udało **Ci się pomyślnie zalogować się do aplikacji**.

8. Wybierz przycisk **OK**.

Po przechwyceniu strony logowania można przypisać użytkowników i grupy, a także skonfigurować zasady poświadczeń, podobnie jak regularne [aplikacje SSO hasła](what-is-single-sign-on.md).

> [!NOTE]
> Możesz przekazać logo kafelka aplikacji za pomocą przycisku **Przekaż logo** na karcie **Konfiguracja** dla aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie użytkowników lub grup do aplikacji](methods-for-assigning-users-and-groups.md)
- [Konfigurowanie automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/configure-automatic-user-provisioning-portal.md)
