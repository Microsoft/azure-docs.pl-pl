---
title: Tworzenie & katalogiem zasobów w zarządzaniu uprawnieniami — Azure AD
description: Dowiedz się, jak utworzyć nowy kontener zasobów i pakietów dostępu w Azure Active Directory zarządzania uprawnieniami.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8cea26bcb0926cd3af360a6489377767d681079
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532562"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Tworzenie katalogu zasobów i zarządzanie nimi w usłudze Azure AD entitlement management

## <a name="create-a-catalog"></a>Tworzenie katalogu

Wykaz jest kontenerem zasobów i pakietów dostępu. Katalog należy utworzyć, gdy chcesz zgrupować powiązane zasoby i pakiety dostępu. Kto tworzy katalog, staje się pierwszym właścicielem katalogu. Właściciel katalogu może dodać kolejnych właścicieli wykazu.

**Rola wymagań wstępnych:** administrator globalny, administrator użytkowników lub twórca katalogu

1. Na stronie Azure Portal kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **Nadzór nad tożsamościami.**

1. W menu po lewej stronie kliknij **pozycję Wykazy**.

    ![Wykazy zarządzania uprawnieniami w Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Kliknij **pozycję Nowy wykaz.**

1. Wprowadź unikatową nazwę katalogu i podaj opis.

    Użytkownicy będą widzieć te informacje w szczegółach pakietu dostępu.

1. Jeśli chcesz, aby pakiety dostępu w tym wykazie były dostępne dla użytkowników, których mogą żądać natychmiast po ich utworzeniu, ustaw opcję **Włączone** na **wartość Tak.**

1. Jeśli chcesz zezwolić użytkownikom w wybranych katalogach zewnętrznych na żądanie pakietów dostępu w tym katalogu, ustaw opcję Włączone dla użytkowników **zewnętrznych** na **wartość Tak.**

    ![Okienko Nowy wykaz](./media/entitlement-management-shared/new-catalog.png)

1. Kliknij **pozycję Utwórz,** aby utworzyć wykaz.

### <a name="creating-a-catalog-programmatically"></a>Programowe tworzenie katalogu

Katalog można również utworzyć przy użyciu Microsoft Graph.  Użytkownik w odpowiedniej roli z aplikacją, która ma delegowane uprawnienia, może wywołać interfejs API, aby utworzyć `EntitlementManagement.ReadWrite.All` [element accessPackageCatalog.](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true)

## <a name="add-resources-to-a-catalog"></a>Dodawanie zasobów do katalogu

Aby uwzględnić zasoby w pakiecie dostępu, zasoby muszą istnieć w wykazie. Typy zasobów, które można dodać, to grupy, aplikacje i witryny usługi SharePoint Online. Grupy mogą być utworzone w chmurze Grupy usługi Microsoft 365 grupy zabezpieczeń usługi Azure AD utworzone w chmurze. Mogą to być aplikacje dla przedsiębiorstw usługi Azure AD, w tym zarówno aplikacje SaaS, jak i własne aplikacje federowane z usługą Azure AD. Witryny mogą być witrynami usługi SharePoint Online lub kolekcjami witryn usługi SharePoint Online.

**Rola wymagań wstępnych:** Zobacz [Temat Required roles to add resources to a catalog (Wymagane role do dodawania zasobów do katalogu)](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Na stronie Azure Portal kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **Nadzór nad tożsamościami.**

1. W menu po lewej stronie kliknij pozycję **Wykazy,** a następnie otwórz katalog, do którego chcesz dodać zasoby.

1. W menu po lewej stronie kliknij pozycję **Zasoby**.

1. Kliknij **pozycję Dodaj zasoby.**

1. Kliknij typ zasobu: **grupy i zespoły,** **aplikacje** lub witryny **programu SharePoint.**

    Jeśli nie widzisz zasobu, który chcesz dodać lub nie możesz dodać zasobu, upewnij się, że masz wymaganą rolę katalogu usługi Azure AD i rolę zarządzania uprawnieniami. Może być konieczne dodanie zasobu do katalogu przez osobę z wymaganymi rolami. Aby uzyskać więcej informacji, zobacz Required roles to add resources to a catalog (Wymagane role [do dodawania zasobów do katalogu).](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Wybierz jeden lub więcej zasobów typu, które chcesz dodać do katalogu.

    ![Dodawanie zasobów do katalogu](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Po zakończeniu kliknij pozycję **Dodaj**.

    Te zasoby można teraz uwzględniać w pakietach dostępu w wykazie.

### <a name="add-a-multi-geo-sharepoint-site-preview"></a>Dodawanie witryny Programu SharePoint z wieloma obszarami geograficznymi (wersja zapoznawcza)

1. Jeśli dla programu SharePoint włączono funkcję [Multi-Geo,](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) wybierz środowisko, z których chcesz wybrać witryny.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multigeo-select.png" alt-text="Pakiet dostępu — dodawanie ról zasobów — wybieranie witryn usługi SharePoint z wieloma obszarami geograficznymi":::

1. Następnie wybierz lokacje, które chcesz dodać do katalogu. 

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Programowe dodawanie zasobu do katalogu

Możesz również dodać zasób do katalogu przy użyciu Microsoft Graph.  Użytkownik w odpowiedniej roli lub wykaz i właściciel zasobu z aplikacją z delegowanymi uprawnieniami może wywołać interfejs API, aby utworzyć `EntitlementManagement.ReadWrite.All` [element accessPackageResourceRequest.](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true)

## <a name="remove-resources-from-a-catalog"></a>Usuwanie zasobów z katalogu

Zasoby można usunąć z katalogu. Zasób można usunąć z katalogu tylko wtedy, gdy nie jest używany w żadnym z pakietów dostępu katalogu.

**Rola wymagań wstępnych:** Zobacz [Temat Required roles to add resources to a catalog (Wymagane role do dodawania zasobów do katalogu)](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Na stronie Azure Portal kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **Nadzór nad tożsamościami.**

1. W menu po lewej stronie kliknij pozycję **Wykazy,** a następnie otwórz katalog, z którego chcesz usunąć zasoby.

1. W menu po lewej stronie kliknij pozycję **Zasoby**.

1. Wybierz zasoby, które chcesz usunąć.

1. Kliknij **pozycję Usuń** (lub kliknij wielokropek ... ), a następnie kliknij pozycję Usuń **zasób**.


## <a name="add-additional-catalog-owners"></a>Dodawanie kolejnych właścicieli wykazu

Użytkownik, który utworzył katalog, staje się pierwszym właścicielem katalogu. Aby delegować zarządzanie wykazem, należy dodać użytkowników do roli właściciela katalogu. Ułatwia to udostępnianie obowiązków w zakresie zarządzania katalogiem. 

Wykonaj następujące kroki, aby przypisać użytkownika do roli właściciela katalogu:

**Rola wymagań wstępnych:** administrator globalny, administrator użytkowników lub właściciel katalogu

1. Na stronie Azure Portal kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **Nadzór nad tożsamościami.**

1. W menu po lewej stronie kliknij pozycję **Wykazy,** a następnie otwórz katalog, do którego chcesz dodać administratorów.

1. W menu po lewej stronie kliknij pozycję **Role i administratorzy.**

    ![Katalogi ról i administratorów](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Kliknij **pozycję Dodaj właścicieli,** aby wybrać członków dla tych ról.

1. Kliknij **pozycję Wybierz,** aby dodać tych członków.

## <a name="edit-a-catalog"></a>Edytowanie katalogu

Możesz edytować nazwę i opis katalogu. Użytkownicy widzą te informacje w szczegółach pakietu dostępu.

**Rola wymagań wstępnych:** administrator globalny, administrator użytkowników lub właściciel katalogu

1. Na stronie Azure Portal kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **Nadzór nad tożsamościami.**

1. W menu po lewej stronie kliknij pozycję **Wykazy,** a następnie otwórz katalog, który chcesz edytować.

1. Na stronie Przegląd **katalogu** kliknij pozycję **Edytuj**.

1. Edytuj nazwę, opis lub włączone ustawienia wykazu.

    ![Edytowanie ustawień wykazu](./media/entitlement-management-shared/catalog-edit.png)

1. Kliknij pozycję **Zapisz**.

## <a name="delete-a-catalog"></a>Usuwanie katalogu

Katalog można usunąć, ale tylko wtedy, gdy nie ma żadnych pakietów dostępu.

**Rola wymagań wstępnych:** administrator globalny, administrator użytkowników lub właściciel katalogu

1. Na stronie Azure Portal kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **Nadzór nad tożsamościami.**

1. W menu po lewej stronie kliknij **pozycję Wykazy,** a następnie otwórz katalog, który chcesz usunąć.

1. Na stronie Przegląd **wykazu kliknij** pozycję **Usuń**.

1. W wyświetlonym oknie komunikatu kliknij przycisk **Tak.**

### <a name="deleting-a-catalog-programmatically"></a>Programowe usuwanie katalogu

Katalog można również usunąć przy użyciu Microsoft Graph.  Użytkownik w odpowiedniej roli z aplikacją, która ma delegowane uprawnienia, może wywołać interfejs API, aby usunąć `EntitlementManagement.ReadWrite.All` [element accessPackageCatalog.](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true)

## <a name="next-steps"></a>Następne kroki

- [Delegowanie nadzoru dostępu do menedżerów pakietów dostępu](entitlement-management-delegate-managers.md)
