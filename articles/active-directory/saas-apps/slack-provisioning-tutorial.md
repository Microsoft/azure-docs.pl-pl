---
title: 'Samouczek: Inicjowanie obsługi administracyjnej użytkowników dla zapasu czasu — Azure AD'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie inicjować udostępnianie kont użytkowników i cofać ich obsługę zapasu czasu.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdc912c2df435f9b7e591d7c5475e126e6b0aeb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062833"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie zapasu czasu dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w ramach zapasu czasu i usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD w celu zapewnienia zapasu.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Dzierżawa zapasowa z [planem Plus](https://aadsyncfabric.slack.com/pricing) lub lepsza
* Konto użytkownika w zapasie czasu z uprawnieniami administratora zespołu

Uwaga: integracja z obsługą administracyjną usługi Azure AD opiera się na [interfejsie API Standard scim zapasu](https://api.slack.com/scim), który jest dostępny dla zespołów zapasowych w planie Plus lub lepszy.

## <a name="assigning-users-to-slack"></a>Przypisywanie użytkowników do zapasu czasu

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi konta użytkownika zostaną zsynchronizowane tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji zapasowej. Po ustaleniu tych użytkowników możesz przypisać je do swojej aplikacji zapasowej, postępując zgodnie z poniższymi instrukcjami:

[Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Ważne porady dotyczące przypisywania użytkowników do zapasu czasu

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do zapasu czasu w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do zapasu czasu należy wybrać rolę **użytkownika** lub grupy w oknie dialogowym przypisania. Rola "dostęp domyślny" nie działa w przypadku aprowizacji.

## <a name="configuring-user-provisioning-to-slack"></a>Konfigurowanie aprowizacji użytkowników do zapasu czasu 

Ta sekcja przeprowadzi Cię przez proces łączenia się z interfejsem API inicjowania obsługi konta użytkownika usługi Azure AD i konfigurowania usługi aprowizacji w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w zapasach czasu na podstawie przypisywania użytkowników i grup w usłudze Azure AD.

**Porada:** Możesz również włączyć funkcję logowania jednokrotnego opartego na protokole SAML dla zapasu czasu, postępując zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi konta użytkownika w usłudze Azure AD:

1. W [Azure Portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** .

2. Jeśli już skonfigurowano funkcję zapasową dla logowania jednokrotnego, Wyszukaj wystąpienie zapasu czasu przy użyciu pola wyszukiwania. W przeciwnym razie wybierz opcję **Dodaj** i Wyszukaj pozycję **zapasowy** w galerii aplikacji. Wybierz pozycję zapasowy z wyników wyszukiwania, a następnie dodaj ją do listy aplikacji.

3. Wybierz wystąpienie zapasu czasu, a następnie wybierz kartę **Inicjowanie obsługi** .

4. Ustaw **tryb aprowizacji** na **automatyczny**.

   ![Inicjowanie obsługi zapasowej](./media/slack-provisioning-tutorial/slack1.png)

5. W sekcji **poświadczenia administratora** kliknij przycisk **Autoryzuj**. Spowoduje to otwarcie okna dialogowego autoryzacji zapasowych w nowym oknie przeglądarki.

6. W nowym oknie, zaloguj się do zapasu czasu przy użyciu konta administratora Twojego zespołu. w oknie dialogowym uzyskana autoryzacja wybierz zespół zapasowy, dla którego chcesz włączyć obsługę administracyjną, a następnie wybierz pozycję **Autoryzuj**. Po zakończeniu Wróć do Azure Portal, aby zakończyć konfigurację aprowizacji.

    ![Okno dialogowe autoryzacji](./media/slack-provisioning-tutorial/slackauthorize.png)

7. W Azure Portal kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z aplikacją czasu zapasu. Jeśli połączenie nie powiedzie się, upewnij się, że konto zapasowe ma uprawnienia administratora zespołu, a następnie spróbuj ponownie wykonać krok "Autoryzuj".

8. Wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji w polu **E-mail powiadomienia** , a następnie zaznacz pole wyboru poniżej.

9. Kliknij przycisk **Zapisz**.

10. W sekcji mapowania wybierz pozycję **synchronizuj Azure Active Directory użytkownicy do zapasu czasu**.

11. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które zostaną zsynchronizowane z usługi Azure AD do zapasu czasu. Należy zauważyć, że atrybuty wybrane jako **pasujące** właściwości będą używane w celu dopasowania do kont użytkowników w zapasach czasu dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

12. Aby włączyć usługę Azure AD Provisioning dla zapasu czasu, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

13. Kliknij przycisk **Zapisz**.

Spowoduje to rozpoczęcie synchronizacji początkowej dla wszystkich użytkowników i/lub grup przypisanych do zapasu czasu w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacja początkowa zajmie więcej czasu niż kolejne synchronizacje, co wystąpi około 10 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **szczegóły synchronizacji** można monitorować postęp i wykonywać linki do raportów dotyczących działań związanych z obsługą administracyjną, które opisują wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji zapasowej.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>Obowiązkowe Konfigurowanie aprowizacji obiektów grupy do zapasu czasu

Opcjonalnie można włączyć obsługę administracyjną obiektów grup z usługi Azure AD do zapasowego czasu. Różni się to od "przypisywania grup użytkowników", ponieważ rzeczywisty obiekt grupy oprócz jego członków będzie replikowany z usługi Azure AD do zapasu czasu. Na przykład jeśli masz grupę o nazwie "moja grupa" w usłudze Azure AD, identyczna Grupa o nazwie "moja grupa" zostanie utworzona wewnątrz zapasu czasu.

### <a name="to-enable-provisioning-of-group-objects"></a>Aby włączyć obsługę administracyjną obiektów grup:

1. W sekcji mapowania wybierz pozycję **Synchronizuj grupy Azure Active Directory do zapasu czasu**.

2. W bloku mapowanie atrybutu ustaw wartość tak.

3. W sekcji **mapowania atrybutów** Przejrzyj atrybuty grupy, które będą synchronizowane z usługi Azure AD do zapasu czasu. Należy zauważyć, że atrybuty wybrane jako **pasujące** właściwości będą używane do dopasowania do grup w zapasach czasu dla operacji aktualizacji. 

4. Kliknij przycisk **Zapisz**.

Spowoduje to, że wszystkie obiekty grupy przypisane do zapasu czasu w sekcji **Użytkownicy i grupy** są w pełni synchronizowane z usługą Azure AD do zapasu czasu. Za pomocą sekcji **szczegóły synchronizacji** można monitorować postęp i wykonywać linki do dzienników aktywności aprowizacji, które opisują wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji zapasowej.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Podczas konfigurowania atrybutu **DisplayName** zapasu należy pamiętać o następujących zachowaniach:

  * Wartości nie są całkowicie unikatowe (np. 2 Użytkownicy mogą mieć taką samą nazwę wyświetlaną)

  * Obsługuje znaki inne niż angielskie, spacje i wielkie litery. 
  
  * Dozwolone znaki interpunkcyjne obejmują kropki, podkreślenia, łączniki, apostrofy, nawiasy (np. **[{}])**) i separatory (np. **,/;**).
  
  * Tylko aktualizacje, jeśli są skonfigurowane te dwa ustawienia w obszarze roboczym zapasu/ **Synchronizacja profilu organizacji jest włączona** , a **Użytkownicy nie mogą zmieniać ich nazwy wyświetlanej**.
  
* Atrybut **nazwy użytkownika** zapasu musi znajdować się w zakresie 21 znaków i mieć unikatową wartość.

* Zapasy czasu umożliwiają dopasowanie z użyciem atrybutów **username** i **email**.  

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
