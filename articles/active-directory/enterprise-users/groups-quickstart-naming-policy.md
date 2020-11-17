---
title: Zasady nazewnictwa grup — szybki start — Azure Active Directory | Microsoft Docs
description: Wyjaśniono, jak dodać nowych użytkowników lub usunąć istniejących użytkowników w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: quickstart
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a594d42485510a525ce4e3c7f03b432554100f8
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650601"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Szybki start: zasady nazewnictwa grup w usłudze Azure Active Directory

W tym przewodniku szybki start ustawisz zasady nazewnictwa w organizacji usługi Azure Active Directory (Azure AD) dla grup Microsoft 365 utworzonych przez użytkowników, aby ułatwić sortowanie i wyszukiwanie grup w organizacji. Zasady nazewnictwa umożliwiają na przykład:

* Przekazywanie informacji na temat funkcji grupy, członkostwa, regionu geograficznego lub twórcy grupy.
* Łatwiejsze kategoryzowanie grup w książce adresowej.
* Blokowanie konkretnych słów, aby uniemożliwić ich używanie w nazwach grup i aliasach.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="configure-the-group-naming-policy-in-the-azure-portal"></a>Skonfiguruj zasady nazewnictwa grup w Azure Portal

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora użytkownika.
1. Wybierz pozycję **grupy**, a następnie wybierz pozycję **zasady nazewnictwa** , aby otworzyć stronę Zasady nazewnictwa.

    ![Otwórz stronę Zasady nazewnictwa w centrum administracyjnym](./media/groups-quickstart-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Wyświetl lub Edytuj zasady nazewnictwa sufiksu prefiksu

1. Na stronie **zasady nazewnictwa** wybierz pozycję **zasady nazewnictwa grup**.
1. Można wyświetlić lub edytować bieżące zasady nazewnictwa prefiksu lub sufiksu, wybierając atrybuty lub ciągi, które mają zostać wymuszone w ramach zasad nazewnictwa.
1. Aby usunąć prefiks lub sufiks z listy, wybierz prefiks lub sufiks, a następnie wybierz pozycję **Usuń**. Jednocześnie można usunąć wiele elementów.
1. Wybierz pozycję **Zapisz** , aby zmienić zasady, aby wejść w życie.

### <a name="view-or-edit-the-custom-blocked-words"></a>Wyświetlanie lub edytowanie niestandardowo zablokowanych słów

1. Na stronie **zasady nazewnictwa** wybierz pozycję **zablokowane słowa**.

    ![Edytuj i przekaż listę zablokowanych wyrazów dla zasad nazewnictwa](./media/groups-quickstart-naming-policy/blockedwords.png)

1. Wyświetl lub Edytuj bieżącą listę niestandardowych zablokowanych wyrazów, wybierając pozycję **Pobierz**.
1. Przekaż nową listę niestandardowych zablokowanych słów, wybierając ikonę pliku.
1. Wybierz pozycję **Zapisz** , aby zmienić zasady, aby wejść w życie.

To wszystko. Skonfigurowano zasady nazewnictwa i dodano niestandardowe słowa zablokowane.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

### <a name="remove-the-naming-policy-using-azure-portal"></a>Usuń zasady nazewnictwa przy użyciu Azure Portal

1. Na stronie **zasady nazewnictwa** wybierz pozycję **Usuń zasady**.
1. Po potwierdzeniu usunięcia zasady nazewnictwa zostaną usunięte, w tym wszystkie zasady nazewnictwa sufiksów prefiksów i wszelkie niestandardowe niezablokowane słowa.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wiesz już, jak ustawić zasady nazewnictwa dla organizacji usługi Azure AD za pomocą Azure Portal.

Przejdź do następnego artykułu, aby uzyskać więcej informacji, w tym poleceń cmdlet programu PowerShell dotyczących zasad nazewnictwa, ograniczeń technicznych, dodawania listy niestandardowych zablokowanych słów i środowiska użytkownika końcowego w aplikacjach Microsoft 365.
> [!div class="nextstepaction"]
> [Zasady nazewnictwa programu PowerShell](groups-naming-policy.md)