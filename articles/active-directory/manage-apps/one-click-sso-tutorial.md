---
title: Konfiguracja logowania jednokrotnego (SSO) dla aplikacji w portalu Azure Marketplace | Microsoft Docs
description: Kroki związane z konfiguracją logowania jednokrotnego dla aplikacji w portalu Azure Marketplace.
services: active-directory
documentationCenter: na
author: kenwith
manager: daveba
ms.reviewer: kenwith
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50f705e587010d956c29a71127e97dd85cb517ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258256"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Konfiguracja aplikacji jednokrotnego kliknięcia

 W ramach tego samouczka nauczysz się, jak wykonać jedną opcję jednokrotnej rejestracji jednokrotnej (Logowanie jednokrotne) dla aplikacji Azure Active Directory (Azure AD) obsługujących protokół SAML w witrynie Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Wprowadzenie do jednego kliknięcia logowania jednokrotnego

Funkcja logowania jednokrotnego w systemie Azure została zaprojektowana tak, aby konfigurować Logowanie jednokrotne dla aplikacji w portalu Marketplace, które obsługują protokół SAML. Na stronie Konfiguracja logowania jednokrotnego w usłudze Azure AD ta opcja umożliwia automatyczne konfigurowanie metadanych usługi Azure AD po stronie aplikacji. W ten sposób można szybko skonfigurować Logowanie jednokrotne z minimalnym ręcznym nakładem pracy.

## <a name="advantages-of-one-click-sso"></a>Zalety jednego kliknięcia Logowanie jednokrotne

- Szybka konfiguracja logowania jednokrotnego aplikacji w portalu Azure Marketplace, które wymagają ręcznej konfiguracji po stronie aplikacji.
- Bardziej wydajna i dokładna konfiguracja logowania jednokrotnego.
- Brak komunikacji partnerskiej lub pomocy technicznej dla Instalatora. Aplikacja udostępnia interfejs użytkownika do konfiguracji protokołu SAML.

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywna subskrypcja aplikacji do skonfigurowania z logowaniem jednokrotnym. Wymagane są również poświadczenia administratora.
- **Rozszerzenie moje aplikacje bezpieczne logowanie** z firmy Microsoft zainstalowane w przeglądarce. Aby uzyskać więcej informacji, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="one-click-sso-configuration-steps"></a>Jednokrotne kliknięcie czynności konfiguracyjnych Logowanie jednokrotne

1. Dodaj aplikację z witryny Azure Marketplace.

2. Wybierz pozycję **Logowanie jednokrotne**.

3. Wybierz pozycję **Włącz logowanie jednokrotne**.

4. Wypełnij wartości obowiązkowych konfiguracji w sekcji **Podstawowa konfiguracja protokołu SAML** .

    > [!NOTE]
    > Jeśli aplikacja ma oświadczenia niestandardowe, które należy skonfigurować, obsłuż je przed przeprowadzeniem logowania jednokrotnego za pomocą jednego kliknięcia.

5. Jeśli dla aplikacji w portalu Azure Marketplace jest dostępna funkcja rejestracji jednokrotnej, zobaczysz następujący ekran. Może być konieczne zainstalowanie **rozszerzenia przeglądarki Moje aplikacje bezpieczne logowanie** , wybierając pozycję **Zainstaluj rozszerzenie**.

   ![Zainstaluj moje aplikacje bezpieczne logowanie do przeglądarki](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Po dodaniu rozszerzenia do przeglądarki wybierz pozycję **Konfiguracja \<Application Name\>**. Po przekierowaniu do portalu administracyjnego aplikacji Zaloguj się jako administrator.

   ![Nazwa aplikacji konfiguracji](./media/one-click-sso-tutorial/setup-sso.png)

7. Rozszerzenie przeglądarki automatycznie konfiguruje Logowanie jednokrotne w aplikacji. Potwierdź, wybierając pozycję **tak**.

   ![Zapisywanie automatycznie wypełnianych danych](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Jeśli konfiguracja logowania jednokrotnego dla aplikacji wymaga dodatkowych kroków, wykonaj kroki opisane w sekcji.

8. Po zakończeniu konfiguracji wybierz **przycisk OK** , aby zapisać zmiany.

   ![Zapisz automatycznie wypełnione dane](./media/one-click-sso-tutorial/save-data.png)

9. Zostanie wyświetlone okno potwierdzenia informujące o pomyślnym skonfigurowaniu ustawień logowania jednokrotnego.

   ![Skonfigurowano Logowanie jednokrotne](./media/one-click-sso-tutorial/sso-configured.png)

10. Po pomyślnym zakończeniu konfiguracji wylogujesz się z aplikacji i wrócisz do Azure Portal.

11. Możesz wybrać **test** , aby przetestować Logowanie jednokrotne.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](../saas-apps/tutorial-list.md)
* [Co to jest rozszerzenie przeglądarki Moje aplikacje bezpieczne logowanie?](../user-help/my-apps-portal-end-user-access.md)
