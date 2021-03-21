---
title: Rozwiązywanie problemów dotyczących uwierzytelniania i autoryzacji — Event Hubs platformy Azure
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z uwierzytelnianiem i autoryzacją za pomocą usługi Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 368fd8efda1b828f99bc41da0743768989c1a601
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92329614"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>Rozwiązywanie problemów dotyczących uwierzytelniania i autoryzacji — Event Hubs platformy Azure
Artykuł [Rozwiązywanie problemów z łącznością](troubleshooting-guide.md) zawiera wskazówki dotyczące rozwiązywania problemów z łącznością z usługą Azure Event Hubs. Ten artykuł zawiera porady i zalecenia dotyczące rozwiązywania problemów z uwierzytelnianiem i autoryzacją za pomocą usługi Azure Event Hubs. 

## <a name="if-you-are-using-azure-active-directory"></a>Jeśli używasz Azure Active Directory
Jeśli używasz usługi Azure Active Directory (Azure AD) do uwierzytelniania i autoryzacji za pomocą usługi Azure Event Hubs, potwierdź, że tożsamość uzyskujący dostęp do centrum zdarzeń jest członkiem odpowiedniej **roli platformy Azure** w odpowiednim **zakresie zasobów** (grupy konsumentów, centrum zdarzeń, przestrzeni nazw, grupy zasobów lub subskrypcji).

### <a name="azure-roles"></a>Role platformy Azure
- [Właściciel danych Event Hubs platformy Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) w celu uzyskania pełnego dostępu do zasobów Event Hubs.
- [Nadawca danych usługi Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) dla dostępu do wysyłania.
- [Usługa Azure Event Hubs Data Receiver](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) dla dostępu do odbioru.

Aby uzyskać wbudowane role rejestru schematu, zobacz [role rejestru schematu](schema-registry-overview.md#azure-role-based-access-control).

### <a name="resource-scopes"></a>Zakresy zasobów
- **Grupa konsumentów**: w tym zakresie przypisanie roli ma zastosowanie tylko do tej jednostki. Obecnie Azure Portal nie obsługuje przypisywania roli platformy Azure do podmiotu zabezpieczeń na tym poziomie. 
- **Centrum zdarzeń**: przypisanie roli dotyczy jednostki centrum zdarzeń i grupy konsumentów.
- **Przestrzeń nazw**: przypisanie roli obejmuje całą topologię Event Hubs w przestrzeni nazw oraz do skojarzonej z nią grupy odbiorców.
- **Grupa zasobów**: przypisanie roli dotyczy wszystkich zasobów Event Hubs w grupie zasobów.
- **Subskrypcja**: przypisanie roli dotyczy wszystkich zasobów Event Hubs we wszystkich grupach zasobów w subskrypcji.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Uwierzytelnianie aplikacji za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs](authenticate-application.md)
- [Autoryzuj dostęp do zasobów Event Hubs przy użyciu Azure Active Directory](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>Jeśli używasz sygnatur dostępu współdzielonego (SAS)
Jeśli używasz [sygnatury dostępu współdzielonego](authenticate-shared-access-signature.md), wykonaj następujące kroki: 

- Upewnij się, że używany klucz sygnatury dostępu współdzielonego jest poprawny. Jeśli nie, użyj odpowiedniego klucza SAS.
- Sprawdź, czy klucz ma odpowiednie uprawnienia (wysyłanie, odbieranie lub zarządzanie). Jeśli nie, użyj klucza, który ma wymagane uprawnienie. 
- Sprawdź, czy klucz wygasł. Zalecamy odnowienie sygnatury dostępu współdzielonego przed wygaśnięciem. W przypadku pochylenia zegara między klientem a węzłami usługi Event Hubs token uwierzytelniania może wygasnąć przed jego zapisaniem przez klienta. W przypadku bieżących kont implementacji rozchylony zegar do 5 minut, czyli klient odnawia token 5 minut przed jego wygaśnięciem. W związku z tym, jeśli skośność zegara jest większa niż 5 minut, klient może obserwować sporadyczne błędy uwierzytelniania.
- Jeśli jest ustawiona wartość **czasu rozpoczęcia SAS** , w ciągu kilku minut mogą pojawić **się** sporadyczne błędy spowodowane pochyleniem zegara (różnice w bieżącym czasie na różnych komputerach). Ustaw godzinę rozpoczęcia na co najmniej 15 minut w przeszłości lub nie ustawiaj jej wcale. To samo ogólnie dotyczy czasu wygaśnięcia. 

Aby uzyskać więcej informacji, zobacz następujące artykuły: 

- [Uwierzytelnianie przy użyciu sygnatur dostępu współdzielonego (SAS)](authenticate-shared-access-signature.md). 
- [Autoryzowanie dostępu do zasobów Event Hubs przy użyciu sygnatur dostępu współdzielonego](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły:

* [Rozwiązywanie problemów z łącznością](troubleshooting-guide.md)
