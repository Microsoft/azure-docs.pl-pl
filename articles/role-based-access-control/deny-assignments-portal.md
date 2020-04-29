---
title: Wyświetl listę odrzuconych przypisań zasobów platformy Azure za pomocą Azure Portal
description: Dowiedz się, jak wyświetlić listę użytkowników, grup, nazw podmiotów usługi i zarządzanych tożsamości, dla których odmówiono dostępu do określonych akcji zasobów platformy Azure w określonych zakresach przy użyciu Azure Portal.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4db76e5c6191457346ca1f95678cf73843334d3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77137419"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Wyświetl listę odrzuconych przypisań zasobów platformy Azure przy użyciu Azure Portal

[Odmowa przypisania](deny-assignments.md) uniemożliwia użytkownikom wykonywanie określonych akcji zasobów platformy Azure nawet wtedy, gdy przypisanie roli przyznaje im dostęp. W tym artykule opisano sposób wyświetlania listy zablokowanych przypisań przy użyciu Azure Portal.

> [!NOTE]
> Nie można bezpośrednio tworzyć własnych przypisań Odmów. Aby uzyskać informacje na temat sposobu tworzenia przypisań Odmów, zobacz [odmowa przypisań](deny-assignments.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać informacje o przypisaniu odmowy, należy dysponować:

- `Microsoft.Authorization/denyAssignments/read`uprawnienie, które jest zawarte w większości [wbudowanych ról dla zasobów platformy Azure](built-in-roles.md).

## <a name="list-deny-assignments"></a>Lista przypisań odmowy

Wykonaj następujące kroki, aby wyświetlić listę odrzuconych przypisań w zakresie subskrypcji lub grupy zarządzania.

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie **grupy** lub **subskrypcje**zarządzania.

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

* [Informacje o odmowie przypisań zasobów platformy Azure](deny-assignments.md)
* [Wyświetl listę odrzuconych przypisań zasobów platformy Azure przy użyciu Azure PowerShell](deny-assignments-powershell.md)
