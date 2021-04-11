---
title: 'Samouczek: wprowadzenie Dodaj administratora'
description: W tym samouczku dowiesz się, jak dodać innego użytkownika administracyjnego do obszaru roboczego.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021
ms.openlocfilehash: 8b854dfcff7dfb4d03038b542308b6f3ebb6d491
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553502"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>Dodawanie administratora do obszaru roboczego Synapse

W tym samouczku dowiesz się, jak dodać administratora do obszaru roboczego Synapse. Ten użytkownik będzie miał pełną kontrolę nad obszarem roboczym.

## <a name="overview"></a>Omówienie

Do tej pory w przewodniku wprowadzenie *możesz* skupić się na działaniach wykonywanych w obszarze roboczym. Ponieważ obszar roboczy został utworzony w kroku 1, jesteś administratorem obszaru roboczego Synapse. Teraz wprowadzimy innego użytkownika Ryana ( `ryan@contoso.com` ) z uprawnieniami administratora. Gdy skończysz, Ryan będzie w stanie wykonać wszystkie czynności, które można wykonać w obszarze roboczym.

## <a name="azure-rbac-owner-role-for-the-workspace"></a>Azure RBAC: rola właściciela dla obszaru roboczego

Przypisz do `ryan@contoso.com` roli **właściciela** RBAC platformy Azure w obszarze roboczym.

1. Otwórz Azure Portal i Otwórz obszar roboczy Synapse.
1. Po lewej stronie wybierz pozycję **Access Control (IAM)**.
1. Dodaj `ryan@contoso.com` do roli **właściciela** . 
1. Kliknij pozycję **Zapisz**.
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>Synapse RBAC: rola administratora Synapse dla obszaru roboczego

Przypisz do, `ryan@contoso.com` Aby Synapse rolę **administratora RBAC Synapse** w obszarze roboczym.

1. Otwórz obszar roboczy w programie Synapse Studio.
1. Po lewej stronie kliknij pozycję **Zarządzaj** , aby otworzyć Centrum zarządzania.
1. W obszarze **zabezpieczenia** kliknij pozycję **Kontrola dostępu**.
1. Kliknij pozycję **Dodaj**.
1. Pozostaw **zakres** ustawiony na **obszar roboczy**.
1. Dodaj `ryan@contoso.com` do roli **administratora Synapse** . 
1. Następnie kliknij przycisk **Zastosuj**.
 
## <a name="azure-rbac-role-assignments-on-the-primary-storage-account"></a>Azure RBAC: przypisania ról na podstawowym koncie magazynu

Przypisz do `ryan@contoso.com` roli **właściciel** na podstawowym koncie magazynu obszaru roboczego.
Przypisz do `ryan@contoso.com` , aby **Azure Storage BLOB rolę współautor danych** na podstawowym koncie magazynu obszaru roboczego.

1. Otwórz podstawowe konto magazynu obszaru roboczego w Azure Portal.
1. Po lewej stronie kliknij pozycję **Access Control (IAM)**.
1. Dodaj `ryan@contoso.com` do roli **właściciela** . 
1. Dodaj `ryan@contoso.com` do roli **współautor danych Azure Storage BLOB**

## <a name="dedicated-sql-pools-db_owner-role"></a>Dedykowane pule SQL: rola db_owner

Przypisz `ryan@contoso.com` do **db_owner** na każdej dedykowanej puli SQL w obszarze roboczym.

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Synapse Analytics](get-started.md)
* [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
* [Korzystanie z bezserwerowej puli SQL](quickstart-sql-on-demand.md)
