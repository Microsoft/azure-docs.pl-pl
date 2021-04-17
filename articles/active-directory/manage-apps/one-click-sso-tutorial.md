---
title: Konfiguracja logowania jednokrotnego (SSO) jednym kliknięciem Azure Marketplace aplikacji | Microsoft Docs
description: Kroki konfiguracji logowania jednokrotnego dla aplikacji jednym kliknięciem z Azure Marketplace.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.reviewer: kenwith
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: e14944bc92b0d728a917402a1bd2f01b8b9012e4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375648"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Konfiguracja aplikacji jednym kliknięciem dla logowania pojedynczego

 Z tego samouczka dowiesz się, jak wykonać konfigurację logowania jednokrotnego (SSO) jednym kliknięciem dla aplikacji z obsługą języka SAML i Azure Active Directory (Azure AD) z Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Wprowadzenie do logowania jednokrotnego jednym kliknięciem

Funkcja logowania jednokrotnego jednym kliknięciem została zaprojektowana w celu skonfigurowania logowania jednokrotnego dla aplikacji Azure Marketplace, które obsługują protokół SAML. Na stronie konfiguracji logowania jednokrotnego usługi Azure AD ta opcja umożliwia automatyczne skonfigurowanie metadanych usługi Azure AD po stronie aplikacji. W ten sposób można szybko skonfigurować logowanie jednokrotne przy minimalnym wysiłku ręcznym.

## <a name="advantages-of-one-click-sso"></a>Zalety logowania jednokrotnego jednym kliknięciem

- Szybka konfiguracja logowania jednokrotnego Azure Marketplace, które wymagają ręcznej konfiguracji po stronie aplikacji.
- Wydajniejsza i dokładniejsza konfiguracja logowania jednokrotnego.
- Do instalacji nie jest potrzebna żadna komunikacja z partnerami ani pomoc techniczna. Aplikacja udostępnia interfejs użytkownika dla konfiguracji SAML.

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywna subskrypcja aplikacji do skonfigurowania przy użyciu logowania jednokrotnego. Potrzebne są również poświadczenia administratora.
- W Moje aplikacje zainstalowane rozszerzenie **bezpiecznego** logowania firmy Microsoft. Aby uzyskać więcej informacji, zobacz Access and use apps on the Moje aplikacje portal (Uzyskiwanie dostępu do aplikacji i korzystanie [z nich w Moje aplikacje portal).](../user-help/my-apps-portal-end-user-access.md)

## <a name="one-click-sso-configuration-steps"></a>Kroki konfiguracji logowania jednokrotnego jednym kliknięciem

1. Dodaj aplikację z Azure Marketplace.

2. Wybierz **pozycję Logowanie pojedyncze.**

3. Wybierz **pozycję Enable single sign-on (Włącz logowanie pojedynczy).**

4. Wypełnij obowiązkowe wartości konfiguracji w sekcji **Podstawowa konfiguracja saml.**

    > [!NOTE]
    > Jeśli aplikacja ma oświadczenia niestandardowe, które należy skonfigurować, należy je obsługiwać przed wykonaniem logowania jednokrotnego jednym kliknięciem.

5. Jeśli funkcja logowania jednokrotnego jednym kliknięciem jest dostępna dla aplikacji Azure Marketplace, zostanie wyświetlony następujący ekran. Może być konieczne zainstalowanie rozszerzenia **Moje aplikacje przeglądarki** do bezpiecznego logowania, wybierając **pozycję Zainstaluj rozszerzenie**.

   ![Instalowanie Moje aplikacje przeglądarki do bezpiecznego logowania](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Po dodaniu rozszerzenia do przeglądarki wybierz pozycję **Skonfiguruj \<Application Name\>**. Po przekierowaniu do portalu administracyjnego aplikacji zaloguj się jako administrator.

   ![Konfigurowanie nazwy aplikacji](./media/one-click-sso-tutorial/setup-sso.png)

7. Rozszerzenie przeglądarki automatycznie konfiguruje logowanie jednokrotne w aplikacji. Potwierdź, wybierając pozycję **Tak.**

   ![Zapisywanie automatycznie wypełnionych danych](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Jeśli konfiguracja logowania jednokrotnego dla aplikacji wymaga wykonania dodatkowych czynności, wykonaj odpowiednie kroki.

8. Po zakończeniu konfiguracji wybierz przycisk **OK,** aby zapisać zmiany.

   ![Zapisywanie automatycznie wypełnionych danych](./media/one-click-sso-tutorial/save-data.png)

9. Zostanie wyświetlone okno potwierdzenia z potwierdzeniem, że ustawienia logowania jednokrotnego zostały pomyślnie skonfigurowane.

   ![Skonfigurowano logowanie jednokrotne](./media/one-click-sso-tutorial/sso-configured.png)

10. Po pomyślnym zakończeniu konfiguracji aplikacja zostanie wyzowana i wrócisz do Azure Portal.

11. Możesz wybrać pozycję **Test,** aby przetestować logowanie pojedyncze.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków na temat sposobu integrowania aplikacji SaaS z Azure Active Directory](../saas-apps/tutorial-list.md)
* [Jakie jest Moje aplikacje przeglądarki do bezpiecznego logowania?](../user-help/my-apps-portal-end-user-access.md)
