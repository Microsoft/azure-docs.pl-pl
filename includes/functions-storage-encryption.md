---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 0bc5977a581006dc760c0435f9d68371ced7e4cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83648782"
---
Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](../articles/storage/common/storage-service-encryption.md).

Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można podać klucze zarządzane przez klienta do szyfrowania obiektów blob i danych plików. Te klucze muszą być obecne w Azure Key Vault, aby funkcje mogły uzyskiwać dostęp do konta magazynu. Aby dowiedzieć się więcej, zobacz [szyfrowanie w spoczynku przy użyciu kluczy zarządzanych przez klienta](../articles/azure-functions/configure-encrypt-at-rest-using-cmk.md).  