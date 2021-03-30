---
title: Łączenie Azure Information Protection z platformą Azure — wskaźnikiem
description: Przesyłanie strumieniowe informacji o rejestrowaniu z Azure Information Protection do usługi Azure wskaźnikowej przez skonfigurowanie łącznika danych Azure Information Protection.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 82ea47037902ce3a9449f71a9edf62cb80863d4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655990"
---
# <a name="connect-data-from-azure-information-protection"></a>Łączenie danych z Azure Information Protection

> [!IMPORTANT]
> Łącznik danych Azure Information Protection na platformie Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Informacje o rejestrowaniu można przesyłać z [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) do kontrolki Azure wskaźnikowej przez skonfigurowanie łącznika danych Azure Information Protection. Azure Information Protection pomaga kontrolować i zabezpieczać poufne dane, niezależnie od tego, czy są przechowywane w chmurze, czy lokalnie.

Jeśli [centralne raportowanie dla Azure Information Protection](/azure/information-protection/reports-aip) jest już skonfigurowane, tak aby informacje o rejestrowaniu z tej usługi były przechowywane w tym samym log Analytics obszarze roboczym, co obecnie wybrane dla platformy Azure, możesz pominąć konfigurację tego łącznika danych. Informacje o rejestrowaniu z Azure Information Protection są już dostępne dla usługi Azure wskaźnikowej.

Jeśli jednak rejestrowanie informacji z Azure Information Protection przechodzi do innego obszaru roboczego Log Analytics niż ten, który jest aktualnie wybrany dla platformy Azure, wykonaj jedną z następujących czynności:

- Zmień obszar roboczy wybrany na platformie Azure — wskaźnik.

- Zmień obszar roboczy dla Azure Information Protection, co możesz zrobić przez skonfigurowanie tego łącznika danych.
    
    Jeśli zmienisz obszar roboczy, nowe dane raportowania dla Azure Information Protection będą teraz przechowywane w obszarze roboczym, który jest używany przez wskaźnik kontroli platformy Azure, a dane historyczne nie są dostępne dla platformy Azure. Ponadto, jeśli poprzedni obszar roboczy jest skonfigurowany dla zapytań niestandardowych, alertów lub interfejsów API REST, należy je ponownie skonfigurować dla obszaru roboczego wskaźnikowego platformy Azure, jeśli chcesz korzystać z nich na potrzeby Azure Information Protection. Dla klientów i usług używających Azure Information Protection nie jest wymagana żadna ponowna konfiguracja.

## <a name="prerequisites"></a>Wymagania wstępne

- Jedna z następujących ról administratora usługi Azure AD dla dzierżawy: 
    - Azure Information Protection administrator
    - Administrator zabezpieczeń
    - Administrator zgodności
    - Administrator danych zgodności
    - Administrator globalny
    
    > [!NOTE]
    > Nie można użyć roli administratora Azure Information Protection, jeśli dzierżawa znajduje się na [ujednoliconej platformie etykietowania](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
    
    Te role administratorów są wymagane tylko do konfigurowania łącznika Azure Information Protection i nie są wymagane, gdy wskaźnik "Azure" jest połączony z Azure Information Protection.

- Uprawnienia do odczytu i zapisu w obszarze roboczym Log Analytics używanym do celów platformy Azure i Azure Information Protection.

- Do Azure Portal dodano Azure Information Protection. Jeśli potrzebujesz pomocy dotyczącej tego kroku, zobacz [dodawanie Azure Information Protection do Azure Portal](/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal).

## <a name="connect-to-azure-information-protection"></a>Połącz z Azure Information Protection

Jeśli nie skonfigurowano obszaru roboczego Log Analytics dla Azure Information Protection, należy skorzystać z poniższych instrukcji lub zmienić obszar roboczy, w którym są przechowywane Azure Information Protection informacje o rejestrowaniu.

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych**  >  **Azure Information Protection (wersja zapoznawcza)**.

2. Wybierz pozycję **Otwórz stronę łącznika**.

3. W obszarze **Konfiguracja** wybierz pozycję **Połącz Azure Information Protection dzienniki**.

4. W bloku **Konfiguruj analizę (wersja zapoznawcza)** wybierz obszar roboczy, który jest obecnie używany na potrzeby platformy Azure. W przypadku wybrania innego obszaru roboczego dane raportowania z Azure Information Protection nie są dostępne dla platformy Azure.

5. Po wybraniu obszaru roboczego wybierz **przycisk OK**. **Stan** łącznika zmieni się na **połączono**.

6. Dane raportowania z Azure Information Protection są przechowywane w tabeli **InformationProtectionLogs_CL** w wybranym obszarze roboczym. 
    
    Aby użyć odpowiedniego schematu w Azure Monitor dla tych danych raportowania, wyszukaj ciąg **InformationProtectionEvents**. Informacje o tych funkcjach zdarzeń znajdują się w sekcji [przyjazne informacje o schemacie dla funkcji zdarzeń](/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) z dokumentacji Azure Information Protection.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia Azure Information Protection z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).