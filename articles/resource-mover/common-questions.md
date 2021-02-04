---
title: Często zadawane pytania dotyczące przenoszenia zasobów platformy Azure?
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące przenoszenia zasobów platformy Azure
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: raynew
ms.openlocfilehash: ab4b8f5a691bc8e4091e9f3f01b709391deeddb0
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550712"
---
# <a name="common-questions"></a>Często zadawane pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące [przenoszenia zasobów platformy Azure](overview.md).

## <a name="general"></a>Ogólne

### <a name="is-resource-mover-generally-available"></a>Czy przeniesienie zasobów jest ogólnie dostępne?

Przeniesienie zasobów jest obecnie dostępne w publicznej wersji zapoznawczej. Obciążenia produkcyjne są obsługiwane.



## <a name="moving-across-regions"></a>Przechodzenie między regionami

### <a name="can-i-move-resources-across-any-regions"></a>Czy mogę przenieść zasoby w dowolnym regionie?

Obecnie można przenosić zasoby z dowolnego źródłowego regionu publicznego do dowolnego docelowego regionu publicznego, w zależności od [typów zasobów dostępnych w tym regionie](https://azure.microsoft.com/global-infrastructure/services/). Przeniesienie zasobów w regionach Azure Government nie jest obecnie obsługiwane.

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>Jakie zasoby można przenosić między regionami przy użyciu funkcji przenoszenia zasobów?

Korzystając z funkcji przenoszenia zasobów, można obecnie przenosić następujące zasoby między regionami:

- Maszyny wirtualne platformy Azure i skojarzone dyski
- Karty interfejsów sieciowych
- Zestawy dostępności 
- Sieci wirtualne platformy Azure 
- Publiczne adresy IP
- Sieciowe grupy zabezpieczeń
- Wewnętrzne i publiczne usługi równoważenia obciążenia 
- Bazy danych SQL Azure i pule elastyczne


### <a name="can-i-move-resources-across-subscriptions-when-i-move-them-across-regions"></a>Czy mogę przenosić zasoby między subskrypcjami, gdy przechodzą je między regionami?

Możesz zmienić subskrypcję po przeniesieniu zasobów do regionu docelowego. [Dowiedz się więcej](../azure-resource-manager/management/move-resource-group-and-subscription.md) o przenoszeniu zasobów do innej subskrypcji. 

### <a name="does-azure-resource-move-service-store-customer-data"></a>Czy usługa Azure Resource Move przechowuje dane klienta? 
Nie. Usługa przenoszenia zasobów nie przechowuje danych klienta, tylko przechowuje informacje o metadanych, które ułatwiają śledzenie i postęp zasobów wybranych do przeniesienia przez klienta.


### <a name="where-is-the-metadata-for-moving-across-regions-stored"></a>Gdzie znajdują się metadane przenoszone między regionami?

Jest on przechowywany w bazie danych [usługi Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) , a w [usłudze Azure Blob Storage](../storage/common/storage-service-encryption.md)w ramach subskrypcji firmy Microsoft. Obecnie metadane są przechowywane w regionach Wschodnie stany USA 2 i Europa Północna. To pokrycie zostanie rozszerzone na inne regiony. Nie pozwala to na przeniesienie zasobów między wszystkimi regionami publicznymi.

### <a name="is-the-collected-metadata-encrypted"></a>Czy zebrane metadane są szyfrowane?

Tak, zarówno podczas przesyłania, jak i w spoczynku.
- Podczas przesyłania metadane są bezpiecznie wysyłane do usługi przenoszenia zasobów za pośrednictwem Internetu przy użyciu protokołu HTTPS.
- W magazynie metadane są szyfrowane.

### <a name="how-is-managed-identity-used-in-resource-mover"></a>Jak jest używana tożsamość zarządzana w obszarze przenoszenia zasobów?

[Tożsamość zarządzana](../active-directory/managed-identities-azure-resources/overview.md) (znana wcześniej jako tożsamość usługi ZARZĄDZANEJ (msi)) zapewnia usługi platformy Azure z automatyczną tożsamością zarządzaną w usłudze Azure AD.
- Funkcja przenoszenia zasobów używa tożsamości zarządzanej, dzięki czemu może uzyskiwać dostęp do subskrypcji platformy Azure w celu przenoszenia zasobów między regionami.
- Kolekcja przenoszenia musi mieć przypisaną do systemu tożsamość, z dostępem do subskrypcji zawierającej zasoby, które są przenoszone.

- W przypadku przenoszenia zasobów między regionami w portalu ten proces odbywa się automatycznie.
- W przypadku przenoszenia zasobów przy użyciu programu PowerShell należy uruchomić polecenia cmdlet, aby przypisać do kolekcji tożsamość przypisaną do systemu, a następnie przypisać rolę z prawidłowymi uprawnieniami subskrypcji do podmiotu zabezpieczeń tożsamości. 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>Jakie uprawnienia tożsamości zarządzanej są potrzebne do przenoszenia zasobów?

Tożsamość zarządzana usługi Azure Resource Mover wymaga co najmniej następujących uprawnień: 

- Uprawnienie do zapisywania/tworzenia zasobów w ramach subskrypcji użytkownika, dostępne z rolą *współautor* . 
- Uprawnienie do tworzenia przypisań ról. Zazwyczaj dostępne dla ról *administratora dostępu* *właściciela* lub użytkownika lub z rolą niestandardową z przypisanym *uprawnieniem Microsoft. Autoryzacja/przypisań ról/zapisem* . To uprawnienie nie jest wymagane, jeśli tożsamość zarządzana zasobu udziału danych ma już dostęp do magazynu danych platformy Azure. 
 
Po dodaniu zasobów w centrum usługi Resource Mover w portalu, uprawnienia są obsługiwane automatycznie, o ile użytkownik ma uprawnienia opisane powyżej. W przypadku dodawania zasobów przy użyciu programu PowerShell można przypisywać uprawnienia ręcznie.

> [!IMPORTANT]
> Zdecydowanie zalecamy, aby nie modyfikować ani usuwać przypisań ról tożsamości. 

### <a name="what-should-i-do-if-i-dont-have-permissions-to-assign-role-identity"></a>Co należy zrobić, jeśli nie mam uprawnień do przypisywania tożsamości roli?

**Możliwa przyczyna** | **Zalecenie**
--- | ---
Nie jesteś *współautorem* i *administratorem dostępu użytkowników* (lub *właścicielem*) podczas dodawania zasobu po raz pierwszy. | Użyj konta z uprawnieniami *współautora* i *administratorem dostępu użytkowników* (lub *właścicielem*) dla subskrypcji.
Tożsamość zarządzana przenoszenia zasobów nie ma wymaganej roli. | Dodaj role "Współautor" i "Administrator dostępu użytkowników".
Tożsamość zarządzana przenoszenia zasobów została zresetowana do wartości *Brak*. | Ponownie włącz tożsamość przypisaną przez system w kolekcji Move > **Identity**. Alternatywnie Dodaj zasób ponownie w obszarze **Dodaj zasoby**, co oznacza, że to samo.  
Subskrypcja została przeniesiona do innej dzierżawy. | Wyłącz, a następnie Włącz tożsamość zarządzaną dla kolekcji Move.

### <a name="how-can-i-do-multiple-moves-together"></a>Jak można przenieść wiele jednocześnie?

Zmień kombinacje źródłowe/docelowe stosownie do potrzeb, używając opcji Zmień w portalu.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](about-move-process.md) na temat składników przenoszenia zasobów i procesu przenoszenia.
