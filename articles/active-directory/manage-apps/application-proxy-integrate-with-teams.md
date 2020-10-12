---
title: Dostęp do aplikacja usługi Azure AD aplikacji serwera proxy w programie Teams | Microsoft Docs
description: Użyj usługi Azure serwer proxy aplikacji usługi Azure AD, aby uzyskać dostęp do aplikacji lokalnej za pomocą programu Microsoft Teams.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/05/2017
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7afcd3bfe16775d5c99100278eda81da739b8d22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764489"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Uzyskiwanie dostępu do aplikacji lokalnych przy użyciu zespołów Microsoft Teams

Serwer proxy aplikacji usługi Azure Active Directory umożliwia logowanie jednokrotne do aplikacji lokalnych niezależnie od tego, gdzie jesteś. Zespoły firmy Microsoft usprawniają współpracę w jednym miejscu. Integracja obu siebie oznacza, że użytkownicy mogą pracować z członkami zespołu w każdej sytuacji.

Użytkownicy mogą dodawać aplikacje w chmurze do swoich kanałów zespołów [przy użyciu kart](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), ale co z witrynami programu SharePoint lub narzędziem do planowania, które są hostowane lokalnie? Serwer proxy aplikacji to rozwiązanie. Mogą dodawać aplikacje opublikowane za pośrednictwem serwera proxy aplikacji do swoich kanałów przy użyciu tych samych zewnętrznych adresów URL, które są zawsze używane do zdalnego dostępu do swoich aplikacji. Ponieważ serwer proxy aplikacji uwierzytelnia się za pomocą Azure Active Directory, użytkownicy uzyskują funkcję logowania jednokrotnego.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Instalowanie łącznika serwera proxy aplikacji i publikowanie aplikacji

Jeśli jeszcze tego nie zrobiono, [Skonfiguruj serwer proxy aplikacji dla dzierżawy i Zainstaluj łącznik](application-proxy-add-on-premises-application.md). Następnie [Opublikuj aplikację lokalną](application-proxy-add-on-premises-application.md) pod kątem dostępu zdalnego. Gdy publikujesz aplikację, zanotuj zewnętrzny adres URL, ponieważ jest on używany do dodawania aplikacji do zespołów.

Jeśli masz już opublikowane aplikacje, ale nie pamiętasz zewnętrznych adresów URL, zapoznaj się z nimi w [Azure Portal](https://portal.azure.com). Zaloguj się, a następnie przejdź do **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje** > wybierz aplikację > **serwerze proxy aplikacji**.

## <a name="add-your-app-to-teams"></a>Dodawanie aplikacji do zespołów

Po opublikowaniu aplikacji za pomocą serwera proxy aplikacji Powiadom użytkowników, że mogą dodać ją jako kartę bezpośrednio w swoich kanałach zespołów, a następnie aplikacja jest dostępna dla wszystkich członków zespołu. Wykonaj następujące trzy kroki:

1. Przejdź do kanału Teams, w którym chcesz dodać tę aplikację, a następnie wybierz pozycję, **+** Aby dodać kartę.

   ![Wybierz pozycję +, aby dodać kartę w zespołach](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Wybierz pozycję **Witryna sieci Web** na karcie Opcje.

   ![Wybierz witrynę internetową z ekranu Dodaj kartę](./media/application-proxy-integrate-with-teams/website.png)

1. Nadaj karcie nazwę i ustaw adres URL zewnętrznego adresu URL serwera proxy aplikacji.

   ![Nazwij kartę i Dodaj zewnętrzny adres URL](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Gdy jeden z członków zespołu doda kartę, zostanie ona wyświetlona dla wszystkich użytkowników w kanale. Wszyscy użytkownicy, którzy mają dostęp do aplikacji, uzyskują dostęp do logowania jednokrotnego przy użyciu poświadczeń używanych przez program Microsoft Teams. Wszyscy użytkownicy, którzy nie mają dostępu do aplikacji, mogą widzieć kartę w zespołach, ale są Zablokowani do momentu udzielenia im uprawnień do aplikacji lokalnej i Azure Portal opublikowanej wersji aplikacji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [publikować lokalne witryny programu SharePoint](application-proxy-integrate-with-sharepoint-server.md) przy użyciu serwera proxy aplikacji.
- Skonfiguruj aplikacje tak, aby korzystały z [domen niestandardowych](application-proxy-configure-custom-domain.md) dla ich zewnętrznego adresu URL.
