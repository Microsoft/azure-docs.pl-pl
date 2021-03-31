---
title: Wyświetlanie listy przypisań Odmów platformy Azure przy użyciu Azure Portal — RBAC
description: Dowiedz się, jak wyświetlić listę użytkowników, grup, nazw podmiotów usługi i zarządzanych tożsamości, dla których odmówiono dostępu do określonych akcji zasobów platformy Azure w określonych zakresach przy użyciu Azure Portal i kontroli dostępu opartej na rolach (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 92046b3a944a747ce76d2426855eec7b6bc2cd70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84790250"
---
# <a name="list-azure-deny-assignments-using-the-azure-portal"></a>Wyświetl listę przypisań Odmów platformy Azure przy użyciu Azure Portal

[Przydziały Odmów na platformie Azure](deny-assignments.md) blokują użytkownikom możliwość wykonywania określonych akcji zasobów platformy Azure nawet wtedy, gdy przypisanie roli udzielą im dostępu. W tym artykule opisano sposób wyświetlania listy zablokowanych przypisań przy użyciu Azure Portal.

> [!NOTE]
> Nie można bezpośrednio tworzyć własnych przypisań Odmów. Aby uzyskać informacje na temat sposobu tworzenia przypisań Odmów, zobacz [Azure Deny przypisań](deny-assignments.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać informacje o przypisaniu odmowy, należy dysponować:

- `Microsoft.Authorization/denyAssignments/read` uprawnienie, które jest zawarte w większości [wbudowanych ról platformy Azure](built-in-roles.md).

## <a name="list-deny-assignments"></a>Lista przypisań odmowy

Wykonaj następujące kroki, aby wyświetlić listę odrzuconych przypisań w zakresie subskrypcji lub grupy zarządzania.

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie **grupy** lub **subskrypcje** zarządzania.

1. Kliknij grupę zarządzania lub subskrypcję, którą chcesz wyświetlić.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **Odmów przypisań** (lub kliknij przycisk **Wyświetl** na kafelku widok Odmów przypisań).

    Jeśli istnieją przypisania odmowy występujące w tym zakresie lub dziedziczone przez ten zakres, zostaną wyświetlone.

    ![Kontrola dostępu — karta Odmów przypisania](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Aby wyświetlić dodatkowe kolumny, kliknij przycisk **Edytuj kolumny**.

    ![Odmów przypisań — kolumny](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Nazwa** | Nazwa przypisania Odmów. |
    | **Typ nazwy głównej** | Użytkownik, Grupa, grupa zdefiniowana przez system lub nazwa główna usługi. |
    | **Odmowa**  | Nazwa podmiotu zabezpieczeń, który jest zawarty w przypisaniu Odmów. |
    | **#C1** | Unikatowy identyfikator przypisania odmowy. |
    | **Wykluczone podmioty zabezpieczeń** | Czy istnieją podmioty zabezpieczeń, które są wykluczone z przypisania Odmów. |
    | **Nie ma zastosowania do elementów podrzędnych** | Czy przypisanie Odmów jest dziedziczone do podzakresów. |
    | **Ochrona systemu** | Czy przypisanie Odmów jest zarządzane przez platformę Azure. Obecnie zawsze tak. |
    | **Zakres** | Grupa zarządzania, subskrypcja, Grupa zasobów lub zasób. |

1. Dodaj znacznik wyboru do dowolnego z włączonych elementów, a następnie kliknij przycisk **OK** , aby wyświetlić wybrane kolumny.

## <a name="list-details-about-a-deny-assignment"></a>Wyświetl szczegóły przypisania odmowy

Wykonaj następujące kroki, aby wyświetlić listę dodatkowych informacji o przypisaniu Odmów.

1. Otwórz okienko **Odmów przypisań** zgodnie z opisem w poprzedniej sekcji.

1. Kliknij nazwę przypisywania Odmów, aby otworzyć blok **Użytkownicy** .

    ![Odmowa przypisania — użytkownicy](./media/deny-assignments-portal/deny-assignment-users.png)

    Blok **Użytkownicy** zawiera dwie poniższe sekcje.

    |  |  |
    | --- | --- |
    | **Przypisanie odmowy dotyczy**  | Podmioty zabezpieczeń, do których odnosi się przypisanie odmowy. |
    | **Wykluczanie przypisania Odmów** | Podmioty zabezpieczeń, które są wykluczone z przypisania Odmów. |

    Nazwa **główna zdefiniowana przez system** reprezentuje wszystkich użytkowników, grupy, nazwy główne usługi i zarządzane tożsamości w katalogu usługi Azure AD.

1. Aby wyświetlić listę zabronionych uprawnień, kliknij przycisk **odmowa uprawnień**.

    ![Odmów uprawnień do odmowy przypisania](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Typ akcji | Opis |
    | --- | --- |
    | **Akcje**  | Odmowa operacji zarządzania. |
    | **NotActions** | Operacje zarządzania zostały wykluczone z odmowy operacji zarządzania. |
    | **Akcje dataactions**  | Odmowa operacji na danych. |
    | **NotDataActions** | Operacje na danych zostały wykluczone z odmowy operacji na danych. |

    W przykładzie pokazanym na poprzednim zrzucie ekranu są następujące czynne uprawnienia:

    - Wszystkie operacje magazynu na płaszczyźnie danych są odrzucane z wyjątkiem operacji obliczeniowych.

1. Aby wyświetlić właściwości przypisania odmowy, kliknij przycisk **Właściwości**.

    ![Odmów przypisania — właściwości](./media/deny-assignments-portal/deny-assignment-properties.png)

    W bloku **Właściwości** można zobaczyć nazwę, identyfikator, opis i zakres przypisania Odmów. Przełącznik nie ma **zastosowania do elementów podrzędnych** wskazuje, czy przypisanie Odmów jest dziedziczone do podzakresów. Przełącznik **chroniony przez system** wskazuje, czy to przypisanie odmowy jest zarządzane przez platformę Azure. Obecnie jest to **tak** we wszystkich przypadkach.

## <a name="next-steps"></a>Następne kroki

* [Informacje o przypisaniach Odmów na platformie Azure](deny-assignments.md)
* [Wyświetl listę przypisań Odmów platformy Azure przy użyciu Azure PowerShell](deny-assignments-powershell.md)
