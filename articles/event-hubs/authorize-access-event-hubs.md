---
title: Autoryzacja dostępu do usługi Azure Event Hubs
description: Ten artykuł zawiera informacje o różnych opcjach autoryzacji dostępu do zasobów usługi Azure Event Hubs.
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 5fc35387f4ac28ad4dd28bea0013bcdf1e1e9f02
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332386"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Autoryzacja dostępu do usługi Azure Event Hubs
Za każdym razem, gdy publikujesz lub zużywasz zdarzenia/dane z centrum zdarzeń, klient próbuje uzyskać dostęp do zasobów Event Hubs. Każde żądanie do bezpiecznego zasobu musi być autoryzowane, aby usługa mogła zapewnić, że klient ma uprawnienia wymagane do publikowania/używania danych. 

Usługa Azure Event Hubs oferuje następujące opcje autoryzacji dostępu do bezpiecznych zasobów:

- Usługa Azure Active Directory
- Sygnatura dostępu współdzielonego

> [!NOTE]
> Ten artykuł dotyczy scenariuszy Event Hubs i [Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) . 

## <a name="azure-active-directory"></a>Usługa Azure Active Directory
Integracja z usługą Azure Active Directory (Azure AD) dla zasobów Event Hubs zapewnia kontrolę dostępu opartą na rolach (Azure RBAC) na potrzeby precyzyjnej kontroli dostępu klientów do zasobów. Możesz użyć kontroli RBAC platformy Azure, aby przyznać uprawnienia podmiotowi zabezpieczeń, które może być użytkownikiem, grupą lub jednostką usługi aplikacji. Podmiot zabezpieczeń jest uwierzytelniany przez usługę Azure AD w celu zwrócenia tokenu OAuth 2,0. Token może służyć do autoryzowania żądania dostępu do zasobu Event Hubs.

Aby uzyskać więcej informacji na temat uwierzytelniania przy użyciu usługi Azure AD, zobacz następujące artykuły:

- [Uwierzytelniaj żądania na platformie Azure Event Hubs przy użyciu Azure Active Directory](authenticate-application.md)
- [Autoryzuj dostęp do zasobów Event Hubs przy użyciu Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="shared-access-signatures"></a>Sygnatury dostępu współdzielonego 
Sygnatury dostępu współdzielonego (SAS) dla zasobów Event Hubs zapewniają ograniczonego delegowanego dostępu do Event Hubs zasobów. Dodanie ograniczeń w przedziale czasowym, dla którego podpis jest prawidłowy lub na uprawnienia, które ma w ten sposób zapewnia elastyczność zarządzania zasobami. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie przy użyciu sygnatur dostępu współdzielonego (SAS)](authenticate-shared-access-signature.md). 

Autoryzowanie użytkowników lub aplikacji przy użyciu tokenu OAuth 2,0 zwróconego przez usługę Azure AD zapewnia doskonałe zabezpieczenia i łatwość użycia w odniesieniu do sygnatur dostępu współdzielonego (SAS). W przypadku usługi Azure AD nie ma potrzeby przechowywania tokenów dostępu wraz z kodem i ryzykiem potencjalnych luk w zabezpieczeniach. Można nadal używać sygnatur dostępu współdzielonego (SAS) do przyznawania szczegółowego dostępu do zasobów Event Hubs, usługa Azure AD oferuje podobne możliwości bez konieczności zarządzania tokenami SAS ani martwania się o odwoływanie się do naruszenia zabezpieczeń SAS. 

Domyślnie wszystkie zasoby Event Hubs są zabezpieczone i są dostępne tylko dla właściciela konta. Mimo że można użyć dowolnego z tych strategii autoryzacji w celu udzielenia klientom dostępu do zasobów centrum zdarzeń. Firma Microsoft zaleca korzystanie z usługi Azure AD, gdy jest to możliwe, aby uzyskać maksymalne zabezpieczenia i łatwość użycia.

Aby uzyskać więcej informacji na temat autoryzacji za pomocą SYGNATURy [dostępu współdzielonego, zobacz Zezwalanie na dostęp do zasobów Event Hubs za pomocą sygnatur danych udostępnionych](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>Następne kroki
- Przejrzyj [przykłady kontroli RBAC platformy Azure](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) opublikowane w naszym repozytorium GitHub. 
- Zobacz następujące artykuły:
    - [Uwierzytelniaj żądania do Event Hubs platformy Azure z aplikacji przy użyciu Azure Active Directory](authenticate-application.md)
    - [Uwierzytelnianie zarządzanej tożsamości za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs](authenticate-managed-identity.md)
    - [Uwierzytelnianie żądań na platformie Azure Event Hubs przy użyciu sygnatur dostępu współdzielonego](authenticate-shared-access-signature.md)
    - [Autoryzuj dostęp do zasobów Event Hubs przy użyciu Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autoryzuj dostęp do zasobów Event Hubs przy użyciu sygnatur dostępu współdzielonego](authorize-access-shared-access-signature.md)

