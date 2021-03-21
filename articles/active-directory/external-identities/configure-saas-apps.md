---
title: Konfigurowanie aplikacji SaaS na potrzeby współpracy B2B — Azure AD
description: Dowiedz się, jak skonfigurować aplikacje SaaS Azure Active Directory współpracy B2B i wyświetlić dodatkowe dostępne zasoby.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0db5e609a5e6b7d74809810a50f86d72ae85675
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90705514"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Konfigurowanie aplikacji SaaS na potrzeby współpracy B2B

Azure Active Directory (Azure AD) Współpraca B2B działa z większością aplikacji, które integrują się z usługą Azure AD. Ta sekcja zawiera instrukcje dotyczące konfigurowania niektórych popularnych aplikacji SaaS do użycia z usługą Azure AD B2B.

Przed zapoznaj się z instrukcjami dotyczącymi aplikacji, poniżej przedstawiono niektóre reguły przewijania:

* W przypadku większości aplikacji Instalacja użytkownika musi nastąpić ręcznie. Oznacza to, że użytkownicy muszą być również utworzeni ręcznie w aplikacji.

* W przypadku aplikacji obsługujących automatyczną konfigurację, takich jak usługa Dropbox, w aplikacjach tworzone są oddzielne zaproszenia. Użytkownicy muszą mieć pewność, że zaakceptuje każde zaproszenie.

* W przypadku atrybutów użytkowników aby wyeliminować problemy z zniekształcona profilem użytkownika (UPD) w obszarze Użytkownicy-Goście, należy zawsze ustawić **Identyfikator użytkownika** na **User. mail**.


## <a name="dropbox-business"></a>Dropbox Business

Aby umożliwić użytkownikom logowanie się przy użyciu konta organizacji, musisz ręcznie skonfigurować usługę Dropbox, aby używać usługi Azure AD jako dostawcy tożsamości SAML (SAML). Jeśli usługa Dropbox nie została skonfigurowana w taki sposób, nie może monitować ani w inny sposób zezwalać użytkownikom na logowanie się za pomocą usługi Azure AD.

1. Aby dodać aplikację Dropbox do usługi Azure AD, wybierz pozycję **aplikacje dla przedsiębiorstw** w lewym okienku, a następnie kliknij przycisk **Dodaj**.

   ![Przycisk "Dodaj" na stronie aplikacje dla przedsiębiorstw](media/configure-saas-apps/add-dropbox.png)

2. W oknie **Dodawanie aplikacji** wpisz **Dropbox** w polu wyszukiwania, a następnie na liście wyników wybierz pozycję **Dropbox dla firm** .

   ![Wyszukaj frazę "Dropbox" na stronie Dodawanie aplikacji](media/configure-saas-apps/add-app-dialog.png)

3. Na stronie **Logowanie** jednokrotne wybierz pozycję **Logowanie jednokrotne** w okienku po lewej stronie, a następnie wprowadź wartość **User. mail** w polu **Identyfikator użytkownika** . (Domyślnie ustawiono jako nazwę UPN).

   ![Konfigurowanie logowania jednokrotnego dla aplikacji](media/configure-saas-apps/configure-app-sso.png)

4. Aby pobrać certyfikat do użycia na potrzeby konfiguracji usługi Dropbox, wybierz pozycję **Konfiguruj usługę Dropbox**, a następnie wybierz pozycję **adres URL logowania** jednokrotnego w usłudze SAML na liście.

   ![Pobieranie certyfikatu dla konfiguracji usługi Dropbox](media/configure-saas-apps/download-certificate.png)

5. Zaloguj się do usługi Dropbox przy użyciu adresu URL logowania ze strony **logowania** jednokrotnego.

   ![Zrzut ekranu przedstawiający stronę logowania do usługi Dropbox](media/configure-saas-apps/sign-in-to-dropbox.png)

6. W menu wybierz pozycję **Konsola administracyjna**.

   ![Link "Konsola administracyjna" w menu Dropbox](media/configure-saas-apps/dropbox-menu.png)

7. W oknie dialogowym **uwierzytelnianie** wybierz opcję **więcej**, Przekaż certyfikat, a następnie w polu **Zaloguj adres URL** wprowadź adres URL logowania jednokrotnego protokołu SAML.

   ![Łącze "więcej" w oknie dialogowym zwiniętego uwierzytelniania](media/configure-saas-apps/dropbox-auth-01.png)

   !["Adres URL logowania" w oknie dialogowym rozszerzone uwierzytelnianie](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Aby skonfigurować automatyczne konfigurowanie użytkownika w Azure Portal, wybierz opcję **Inicjowanie obsługi** w lewym okienku, wybierz opcję **automatycznie** w polu **tryb aprowizacji** , a następnie wybierz pozycję **Autoryzuj**.

   ![Konfigurowanie automatycznego aprowizacji użytkowników w Azure Portal](media/configure-saas-apps/set-up-automatic-provisioning.png)

Po skonfigurowaniu w aplikacji usługi Dropbox osoby gościa lub członków będące oddzielnymi zaproszeniami od usługi Dropbox. Aby móc korzystać z logowania jednokrotnego w usłudze Dropbox, zapraszanie musi zaakceptować zaproszenie, klikając w nim link.

## <a name="box"></a>Box
Można umożliwić użytkownikom uwierzytelnianie w polu gościa za pomocą konta usługi Azure AD za pomocą Federacji, która jest oparta na protokole SAML. Ta procedura polega na przekazaniu metadanych do Box.com.

1. Dodaj aplikację Box z aplikacji dla przedsiębiorstw.

2. Skonfiguruj Logowanie jednokrotne w następującej kolejności:

   ![Zrzut ekranu przedstawiający ustawienia konfiguracji logowania jednokrotnego](media/configure-saas-apps/configure-box-sso.png)

   a. Upewnij się, że adres URL logowania jest odpowiednio ustawiony dla pola Azure Portal w polu **adres URL logowania** . Ten adres URL jest adresem URL dzierżawy Box.com. Powinna być zgodna z konwencją nazewnictwa *https://.box.com* .  
   **Identyfikator** nie ma zastosowania do tej aplikacji, ale nadal pojawia się jako pole wymagane.

   b. W polu **Identyfikator użytkownika** wprowadź wartość **User. mail** (w przypadku logowania jednokrotnego dla kont Gości).

   c. W obszarze **certyfikat podpisywania SAML** kliknij pozycję **Utwórz nowy certyfikat**.

   d. Aby rozpocząć konfigurowanie dzierżawy Box.com do korzystania z usługi Azure AD jako dostawcy tożsamości, Pobierz plik metadanych, a następnie zapisz go na dysku lokalnym.

   e. Prześlij dalej plik metadanych do zespołu pomocy technicznej usługi Box, który konfiguruje Logowanie jednokrotne.

3. W przypadku automatycznego konfigurowania użytkowników w usłudze Azure AD w lewym okienku wybierz pozycję **Inicjowanie obsługi administracyjnej**, a następnie wybierz pozycję **Autoryzuj**.

   ![Autoryzuj usługę Azure AD, aby połączyć się z usługą Box](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Podobnie jak w przypadku zaproszonych usługi Dropbox, zapraszanie z Box musi zrealizować swoje zaproszenie z aplikacji Box.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi współpracy B2B w usłudze Azure AD:

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Grupy dynamiczne i współpraca B2B](use-dynamic-groups.md)
- [Mapowanie oświadczeń użytkowników współpracy B2B](claims-mapping.md)
- [Microsoft 365 udostępnianie zewnętrzne](o365-external-user.md)

