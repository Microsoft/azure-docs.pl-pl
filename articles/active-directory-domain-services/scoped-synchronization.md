---
title: Synchronizacja z zakresami dla Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak za pomocą Azure Portal skonfigurować synchronizację z zakresem z usługi Azure AD do domeny zarządzanej Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/24/2020
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4e65b47b2a1fd71c69ecb350f60df1fedff66b74
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618913"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-the-azure-portal"></a>Konfigurowanie synchronizacji z zakresem z usługi Azure AD do Azure Active Directory Domain Services przy użyciu Azure Portal

Aby zapewnić usługi uwierzytelniania, Azure Active Directory Domain Services (Azure AD DS) synchronizuje użytkowników i grupy z usługi Azure AD. W środowisku hybrydowym Użytkownicy i grupy ze środowiska lokalnego Active Directory Domain Services (AD DS) można najpierw zsynchronizować z usługą Azure AD przy użyciu Azure AD Connect, a następnie zsynchronizowane z domeną zarządzaną AD DS platformy Azure.

Domyślnie wszyscy użytkownicy i grupy z katalogu usługi Azure AD są synchronizowane z domeną zarządzaną. Jeśli masz określone potrzeby, możesz zamiast tego synchronizować tylko zdefiniowanego zestawu użytkowników.

W tym artykule opisano sposób konfigurowania synchronizacji z zakresem, a następnie zmieniania lub wyłączania zestawu użytkowników z zakresem przy użyciu Azure Portal. [Te kroki można również wykonać przy użyciu programu PowerShell][scoped-sync-powershell].

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby uzupełnij ten samouczek, aby [utworzyć i skonfigurować domenę zarządzaną Azure Active Directory Domain Services][tutorial-create-instance].
* Aby zmienić zakres synchronizacji AD DS platformy Azure, musisz mieć uprawnienia *administratora globalnego* w dzierżawie usługi Azure AD.

## <a name="scoped-synchronization-overview"></a>Synchronizacja z zakresem — Omówienie

Domyślnie wszyscy użytkownicy i grupy z katalogu usługi Azure AD są synchronizowane z domeną zarządzaną. Jeśli tylko kilku użytkowników potrzebuje dostępu do domeny zarządzanej, można synchronizować tylko te konta użytkowników. Ta synchronizacja w zakresie jest oparta na grupach. Podczas konfigurowania synchronizacji z zakresem opartym na grupach tylko konta użytkowników należące do określonych grup są synchronizowane z domeną zarządzaną. Grupy zagnieżdżone nie są zsynchronizowane, tylko wybrane grupy.

Zakres synchronizacji można zmienić podczas tworzenia domeny zarządzanej lub po jej wdrożeniu. Teraz można także zmienić zakres synchronizacji w istniejącej domenie zarządzanej bez konieczności ponownego tworzenia jej.

Aby dowiedzieć się więcej o procesie synchronizacji, zobacz [Omówienie synchronizacji w Azure AD Domain Services][concepts-sync].

> [!WARNING]
> Zmiana zakresu synchronizacji powoduje, że zarządzana domena ponownie zsynchronizuje wszystkie dane. Obowiązują następujące zastrzeżenia:
>
>  * W przypadku zmiany zakresu synchronizacji dla domeny zarządzanej następuje pełna ponowna synchronizacja.
>  * Obiekty, które nie są już wymagane w domenie zarządzanej, są usuwane. Nowe obiekty są tworzone w domenie zarządzanej.

## <a name="enable-scoped-synchronization"></a>Włącz synchronizację z zakresem

Aby włączyć synchronizację w zakresie w Azure Portal, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD Domain Services**. Wybierz domenę zarządzaną, taką jak *aaddscontoso.com*.
1. Z menu po lewej stronie wybierz pozycję **Synchronizacja** .
1. W polu *Typ synchronizacji* wybierz pozycję **zakres**.
1. Wybierz **pozycję Wybierz grupy**, a następnie wyszukaj i wybierz grupy do dodania.
1. Po wprowadzeniu wszystkich zmian wybierz pozycję **Zapisz zakres synchronizacji**.

Zmiana zakresu synchronizacji powoduje, że zarządzana domena ponownie zsynchronizuje wszystkie dane. Obiekty, które nie są już wymagane w domenie zarządzanej, są usuwane, a ponowna synchronizacja może zająć trochę czasu.

## <a name="modify-scoped-synchronization"></a>Modyfikuj synchronizację w zakresie

Aby zmodyfikować listę grup, których użytkownicy powinni synchronizować z domeną zarządzaną, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD Domain Services**. Wybierz domenę zarządzaną, taką jak *aaddscontoso.com*.
1. Z menu po lewej stronie wybierz pozycję **Synchronizacja** .
1. Aby dodać grupę, wybierz pozycję **+ Wybierz grupy** u góry, a następnie wybierz grupy do dodania.
1. Aby usunąć grupę z zakresu synchronizacji, wybierz ją z listy aktualnie zsynchronizowanych grup i wybierz pozycję **Usuń grupy**.
1. Po wprowadzeniu wszystkich zmian wybierz pozycję **Zapisz zakres synchronizacji**.

Zmiana zakresu synchronizacji powoduje, że zarządzana domena ponownie zsynchronizuje wszystkie dane. Obiekty, które nie są już wymagane w domenie zarządzanej, są usuwane, a ponowna synchronizacja może zająć trochę czasu.

## <a name="disable-scoped-synchronization"></a>Wyłącz synchronizację z zakresem

Aby wyłączyć synchronizację z zakresem opartym na grupach dla domeny zarządzanej, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD Domain Services**. Wybierz domenę zarządzaną, taką jak *aaddscontoso.com*.
1. Z menu po lewej stronie wybierz pozycję **Synchronizacja** .
1. Zmień *Typ synchronizacji* z **zakres** na **wszystkie**, a następnie wybierz pozycję **Zapisz zakres synchronizacji**.

Zmiana zakresu synchronizacji powoduje, że zarządzana domena ponownie zsynchronizuje wszystkie dane. Obiekty, które nie są już wymagane w domenie zarządzanej, są usuwane, a ponowna synchronizacja może zająć trochę czasu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o procesie synchronizacji, zobacz [Omówienie synchronizacji w Azure AD Domain Services][concepts-sync].

<!-- INTERNAL LINKS -->
[scoped-sync-powershell]: powershell-scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
