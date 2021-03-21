---
title: Uzyskiwanie dostępu do centrum zdarzeń z zadania Azure Stream Analytics za pomocą tożsamości zarządzanych (wersja zapoznawcza)
description: W tym artykule opisano sposób używania tożsamości zarządzanych do uwierzytelniania zadania Azure Stream Analytics na platformie Azure Event Hubs danych wejściowych i wyjściowych.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: 4dc718f21f2ef3beabc31821bd60b571ac07be05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018755"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job-preview"></a>Uzyskiwanie dostępu do centrum zdarzeń z zadania Azure Stream Analytics za pomocą tożsamości zarządzanych (wersja zapoznawcza)

Azure Stream Analytics obsługuje uwierzytelnianie tożsamości zarządzanej dla danych wejściowych i wyjściowych platformy Azure Event Hubs. Tożsamości zarządzane eliminują ograniczenia metod uwierzytelniania opartych na użytkownikach, takich jak konieczność ponownego uwierzytelnienia ze względu na zmiany hasła lub wygaśnięcia tokenów użytkowników, które wystąpiły co 90 dni. Po usunięciu potrzeby ręcznego uwierzytelniania Stream Analytics wdrożenia mogą być w pełni zautomatyzowane.  

Zarządzana tożsamość to zarządzana aplikacja zarejestrowana w Azure Active Directory, która reprezentuje daną Stream Analytics zadanie. Aplikacja zarządzana służy do uwierzytelniania w zasobach przeznaczonych, w tym Event Hubs znajdujących się za zaporą lub siecią wirtualną (VNet). Aby uzyskać więcej informacji na temat obejścia zapór, zobacz [Zezwalanie na dostęp do przestrzeni nazw platformy Azure Event Hubs za pośrednictwem prywatnych punktów końcowych](../event-hubs/private-link-service.md#trusted-microsoft-services).

W tym artykule pokazano, jak włączyć zarządzaną tożsamość dla Event Hubs danych wejściowych lub wyjściowych zadania Stream Analytics za pomocą Azure Portal.Przed włączeniem tożsamości zarządzanej musisz najpierw mieć Stream Analytics zadania i centrum zdarzeń.

### <a name="limitation"></a>Ograniczenie
W trakcie okresu zapoznawczego próbkowanie danych wejściowych z Event Hubs na Azure Portal nie będzie działało w przypadku korzystania z trybu uwierzytelniania tożsamości zarządzanej.

## <a name="create-a-managedidentity"></a>Tworzenie tożsamości zarządzanej  

Najpierw utwórz zarządzaną tożsamość dla zadania Azure Stream Analytics.  

1. W Azure Portal Otwórz zadanie Azure Stream Analytics.  

1. W menu nawigacji po lewej stronie wybierz pozycję **zarządzana tożsamość**   znajdującą się w obszarze *Konfiguruj*. Następnie zaznacz pole wyboru obok **opcji Użyj tożsamości zarządzanej przypisanej do systemu**   i wybierz pozycję **Zapisz**.

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="Tożsamość zarządzana przypisana przez system":::  

1. Nazwa główna usługi dla tożsamości zadania Stream Analytics jest tworzona w Azure Active Directory. Cykl życia nowo utworzonej tożsamości jest zarządzany przez platformę Azure. Po usunięciu zadania Stream Analytics skojarzona tożsamość (czyli nazwa główna usługi) zostanie automatycznie usunięta przez platformę Azure.  

   Podczas zapisywania konfiguracji identyfikator obiektu (OID) jednostki usługi jest wymieniony jako identyfikator podmiotu zabezpieczeń, jak pokazano poniżej:  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="Identyfikator podmiotu zabezpieczeń":::

   Nazwa główna usługi ma taką samą nazwę jak zadanie Stream Analytics. Na przykład jeśli nazwa zadania to  `MyASAJob` , nazwa główna usługi jest również  `MyASAJob` .  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>Przyznaj Stream Analytics uprawnienia dostępu do centrum zdarzeń

Aby zadanie Stream Analytics uzyskać dostęp do centrum zdarzeń przy użyciu tożsamości zarządzanej, utworzona jednostka usługi musi mieć specjalne uprawnienia do centrum zdarzeń.

1. Przejdź do **Access Control (IAM)** w centrum zdarzeń.

1. Wybierz pozycję **+ Dodaj** i **Dodaj przypisanie roli**.

1. Na stronie **Dodawanie przypisania roli** wprowadź następujące opcje:

   |Parametr|Wartość|
   |---------|-----|
   |Rola|Właściciel danych Event Hubs platformy Azure|
   |Przypisz dostęp do|Użytkownik, Grupa lub nazwa główna usługi|
   |Wybierz pozycję|Wprowadź nazwę zadania Stream Analytics|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="Dodaj przypisanie roli":::

1. Wybierz pozycję **Zapisz** i poczekaj chwilę, aby zmiany zostały rozpropagowane.

Możesz również udzielić tej roli na poziomie przestrzeni nazw centrum zdarzeń, co w naturalny sposób propaguje uprawnienia do wszystkich Event Hubs utworzonych w tym obszarze. Oznacza to, że wszystkie Event Hubs w przestrzeni nazw mogą być używane jako zasób uwierzytelniany tożsamości zarządzanej w ramach zadania Stream Analytics.

## <a name="create-anevent-hub-input-or-output"></a>Tworzenie danych wejściowych lub wyjściowych centrum zdarzeń  

Teraz, gdy zarządzana tożsamość została skonfigurowana, możesz dodać zasób centrum zdarzeń jako dane wejściowe lub wyjściowe do zadania Stream Analytics.  

### <a name="add-the-event-hub-as-an-input"></a>Dodawanie centrum zdarzeń jako danych wejściowych 

1. Przejdź do zadania Stream Analytics i przejdź do strony **dane wejściowe** w obszarze **topologia zadania**.

1. Wybierz pozycję **Dodaj dane wejściowe strumienia > centrum zdarzeń**. W oknie właściwości wejściowe Wyszukaj i wybierz centrum zdarzeń, a następnie wybierz pozycję **zarządzana tożsamość** z menu rozwijanego *tryb uwierzytelniania* .

1. Wypełnij pozostałe właściwości i wybierz pozycję **Zapisz**.

### <a name="add-the-event-hub-as-an-output"></a>Dodawanie centrum zdarzeń jako danych wyjściowych

1. Przejdź do zadania Stream Analytics i przejdź do strony dane **wyjściowe** w obszarze **topologia zadania**.

1. Wybierz pozycję **dodaj > centrum zdarzeń**. W oknie właściwości danych wyjściowych Wyszukaj i wybierz centrum zdarzeń, a następnie wybierz pozycję **zarządzana tożsamość** z menu rozwijanego *tryb uwierzytelniania* .

1. Wypełnij pozostałe właściwości i wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

* [Event Hubs dane wyjściowe z Azure Stream Analytics](event-hubs-output.md)
* [Przesyłanie strumieniowe danych z usługi Event Hubs](stream-analytics-define-inputs.md#stream-data-from-event-hubs)
