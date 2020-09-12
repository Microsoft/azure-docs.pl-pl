---
title: Często zadawane pytania dotyczące przenoszenia zasobów platformy Azure?
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące przenoszenia zasobów platformy Azure
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 520c2d4fd258bfab5a5a1e0abf890d58bb98fbdc
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653181"
---
# <a name="common-questions"></a>Często zadawane pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące [przenoszenia zasobów platformy Azure](overview.md).

## <a name="general"></a>Ogólne

### <a name="is-resource-mover-generally-available"></a>Czy przeniesienie zasobów jest ogólnie dostępne?

Przeniesienie zasobów jest obecnie dostępne w publicznej wersji zapoznawczej. Obciążenia produkcyjne są obsługiwane.

## <a name="region-move"></a>Przeniesienie regionu

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>Jakie zasoby można przenosić między regionami przy użyciu funkcji przenoszenia zasobów?

Korzystając z funkcji przenoszenia zasobów, można obecnie przenosić następujące zasoby między regionami:

- Maszyny wirtualne platformy Azure i skojarzone dyski
- Karty interfejsów sieciowych
- Zestawy dostępności 
- Sieci wirtualne platformy Azure 
- Publiczne adresy IP sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)
- Wewnętrzne i publiczne usługi równoważenia obciążenia 
- Bazy danych SQL Azure i pule elastyczne

### <a name="where-is-the-metadata-about-a-region-move-stored"></a>Gdzie są przechowywane metadane dotyczące przenoszenia regionu?

Jest on przechowywany w bazie danych [usługi Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) , a w [usłudze Azure Blob Storage](../storage/common/storage-service-encryption.md)w ramach subskrypcji firmy Microsoft.

### <a name="is-the-collected-metadata-encrypted"></a>Czy zebrane metadane są szyfrowane?

Tak, zarówno podczas przesyłania, jak i w spoczynku.
- Podczas przesyłania metadane są bezpiecznie wysyłane do usługi przenoszenia zasobów za pośrednictwem Internetu przy użyciu protokołu HTTPS.
- W magazynie metadane są szyfrowane.

### <a name="how-is-managed-identity-used-in-resource-mover"></a>Jak jest używana tożsamość zarządzana w obszarze przenoszenia zasobów?

[Tożsamość zarządzana](../active-directory/managed-identities-azure-resources/overview.md) (wcześniej znana jako tożsamość usługi ZARZĄDZANEJ (mis)) zapewnia usługi platformy Azure z automatyczną tożsamością zarządzaną w usłudze Azure AD.
- Funkcja przenoszenia zasobów używa tożsamości zarządzanej, dzięki czemu może uzyskiwać dostęp do subskrypcji platformy Azure w celu przenoszenia zasobów między regionami.
- Kolekcja przenoszenia musi mieć przypisaną do systemu tożsamość, z dostępem do subskrypcji zawierającej zasoby, które są przenoszone.

- W przypadku przenoszenia zasobów między regionami w portalu ten proces odbywa się automatycznie.
- W przypadku przenoszenia zasobów przy użyciu programu PowerShell należy uruchomić polecenia cmdlet, aby przypisać do kolekcji tożsamość przypisaną do systemu, a następnie przypisać rolę z prawidłowymi uprawnieniami subskrypcji do podmiotu zabezpieczeń tożsamości. 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>Jakie uprawnienia tożsamości zarządzanej są potrzebne do przenoszenia zasobów?

Tożsamość zarządzana usługi Azure Resource Recovery musi mieć co najmniej następujące uprawnienia: 

- Uprawnienie do zapisywania/tworzenia zasobów w ramach subskrypcji użytkownika, dostępne z rolą *współautor* . 
- Uprawnienie do tworzenia przypisań ról. Zazwyczaj dostępne dla ról *administratora dostępu* *właściciela* lub użytkownika lub z rolą niestandardową z przypisanym *uprawnieniem Microsoft. Autoryzacja/przypisań ról/zapisem* . To uprawnienie nie jest konieczne, jeśli zarządzana tożsamość zasobu udziału danych ma już dostęp do magazynu danych platformy Azure. 
 
Po dodaniu zasobów w centrum przenoszenia zasobów w portalu, uprawnienia są obsługiwane automatycznie, o ile użytkownik ma uprawnienia opisane powyżej. W przypadku dodawania zasobów przy użyciu programu PowerShell można przypisywać uprawnienia ręcznie.

> [!IMPORTANT]
> Zdecydowanie zalecamy, aby nie modyfikować ani usuwać przypisań ról tożsamości. 

### <a name="what-should-i-do-if-i-dont-have-permissions-to-assign-role-identity"></a>Co należy zrobić, jeśli nie mam uprawnień do przypisywania tożsamości roli?

**Możliwa przyczyna** | **Zalecenie**
--- | ---
Nie jesteś *współautorem* i *administratorem dostępu użytkowników* (lub *właścicielem*) podczas dodawania zasobu po raz pierwszy. | Użyj konta z uprawnieniami *współautora* i *administratorem dostępu użytkowników* (lub *właścicielem*) dla subskrypcji.
Tożsamość zarządzana przenoszenia zasobów nie ma wymaganej roli. | Dodaj role "Współautor" i "Administrator dostępu użytkowników".
Tożsamość zarządzana przenoszenia zasobów została zresetowana do wartości *Brak*. | Ponownie włącz tożsamość przypisaną przez system w kolekcji Move > **Identity**. Alternatywnie Dodaj zasób ponownie w obszarze **Dodaj zasoby**, co oznacza, że to samo.  
Subskrypcja została przeniesiona do innej dzierżawy. | Wyłącz, a następnie Włącz tożsamość zarządzaną dla kolekcji Move.


## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](about-move-process.md) na temat składników przenoszenia zasobów i procesu przenoszenia.
