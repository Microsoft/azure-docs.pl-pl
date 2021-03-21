---
title: Wyświetlanie jednostki usługi zarządzanej tożsamości w Azure Portal — Azure AD
description: Instrukcje krok po kroku dotyczące wyświetlania nazwy głównej usługi tożsamości zarządzanej w Azure Portal.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b25f2691646192faf4d4c133835b456038abd94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96003739"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Wyświetl nazwę główną usługi tożsamości zarządzanej w Azure Portal

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wyświetlić nazwę główną usługi tożsamości zarządzanej przy użyciu Azure Portal.

 > [!NOTE] 
 > Nazwy główne usług są aplikacjami przedsiębiorstwa. 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Włącz [tożsamość przypisaną przez system na maszynie wirtualnej lub w](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) [aplikacji](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Wyświetlanie nazwy głównej usługi

Ta procedura przedstawia sposób wyświetlania nazwy głównej usługi maszyny wirtualnej z włączoną tożsamością przypisanej do systemu (te same kroki dotyczą aplikacji).

1. Kliknij **Azure Active Directory** a następnie kliknij pozycję **aplikacje dla przedsiębiorstw**.
2. W obszarze **Typ aplikacji** wybierz pozycję **wszystkie aplikacje** , a następnie kliknij pozycję **Zastosuj**.
3. W polu filtr wyszukiwania wpisz nazwę zasobu platformy Azure, dla którego włączono tożsamość zarządzaną, lub wybierz ją z wyświetlonej listy.

   ![Wyświetlanie jednostki usługi zarządzania tożsamościami w portalu](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Następne kroki

[Tożsamości zarządzane dla zasobów platformy Azure](./overview.md)