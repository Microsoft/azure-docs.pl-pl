---
title: Wymagania wstępne dotyczące programistycznego dostępu do danych analitycznych
description: Poznaj wymagania, które należy spełnić, zanim będzie można programowo uzyskać dostęp do komercyjnych danych analitycznych portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 1cdd3dba8203ce9e8daeaa963f1722389d89d19d
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563824"
---
# <a name="prerequisites-to-programmatically-access-analytics-data"></a>Wymagania wstępne dotyczące programistycznego dostępu do danych analitycznych

Zanim będzie można programowo uzyskać dostęp do komercyjnych danych analitycznych portalu Marketplace, musisz spełnić poniższe wymagania.

## <a name="commercial-marketplace-enrollment"></a>Komercyjna Rejestracja w portalu Marketplace

Aby programistycznie uzyskiwać dostęp do komercyjnych danych analitycznych portalu Marketplace, należy zarejestrować się w komercyjnym programie Marketplace i dysponować kontem Centrum partnerskiego. Aby dowiedzieć się, jak to zrobić, zobacz [Tworzenie komercyjnego konta witryny Marketplace w centrum partnerskim](./partner-center-portal/create-account.md).

## <a name="create-azure-active-directory-application"></a>Tworzenie aplikacji Azure Active Directory

Zwykłych poświadczeń użytkownika nie można używać do programistycznego dostępu do danych analitycznych portalu Marketplace. Aby uzyskać dostęp do interfejsów API analizy, należy utworzyć aplikację Azure Active Directory (Azure AD) wraz z wpisem tajnym. Aby dowiedzieć się, jak utworzyć aplikację i klucz tajny usługi Azure AD, zobacz [Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](../active-directory/develop/quickstart-register-app.md).

## <a name="associate-the-azure-ad-application-to-the-partner-center-tenant"></a>Kojarzenie aplikacji usługi Azure AD z dzierżawcą Centrum partnerskiego

Aplikacja usługi Azure AD utworzona w Azure Portal musi być połączona z kontem Centrum partnerskiego. Kroki tego procesu są następujące:

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard).
1. W prawym górnym rogu wybierz ikonę koła zębatego, a następnie wybierz pozycję **Ustawienia konta**.
1. W menu **Ustawienia konta** wybierz pozycję **Zarządzanie użytkownikami**.
1. Wybierz pozycję **aplikacje usługi Azure AD** , a następnie wybierz pozycję **+ Utwórz aplikację usługi Azure AD**.
1. Wybierz aplikację usługi Azure AD utworzoną na Azure Portal a następnie wybierz pozycję **dalej**.
1. Zaznacz pole wyboru **Menedżer (Windows)** , a następnie wybierz pozycję **Dodaj**.

    :::image type="content" source="./media/analytics-programmatic-access/azure-ad-roles.png" alt-text="Pokazuje stronę Tworzenie aplikacji usługi Azure AD z polami wyboru dotyczącymi wybierania ról.":::

## <a name="generate-an-azure-ad-token"></a>Generowanie tokenu usługi Azure AD

Musisz wygenerować token usługi Azure AD przy użyciu identyfikatora aplikacji (klienta). Ten identyfikator pomaga w unikatowym identyfikowaniu aplikacji klienckiej na platformie tożsamości firmy Microsoft i w kluczu tajnym klienta z poprzedniego kroku. Aby uzyskać instrukcje dotyczące generowania tokenu usługi Azure AD, zobacz [wywołania usługi Service to Service przy użyciu poświadczeń klienta (wspólny klucz tajny lub certyfikat)](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md).

> [!NOTE]
> Token jest ważny przez jedną godzinę.

## <a name="next-steps"></a>Następne kroki

- [Model dostępu programistycznego](analytics-programmatic-access.md)