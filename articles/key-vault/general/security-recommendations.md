---
title: Zalecenia dotyczące zabezpieczeń dla usługi Azure Key Vault
description: Zalecenia dotyczące zabezpieczeń Azure Key Vault. Wdrożenie tych wskazówek pomoże Ci zrealizować zobowiązania dotyczące zabezpieczeń zgodnie z opisem w naszym wspólnym modelu odpowiedzialności
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: f96df14fb754578b58fb67ac116ac79cff3ffdcb
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289849"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Zalecenia dotyczące zabezpieczeń dla usługi Azure Key Vault

Ten artykuł zawiera zalecenia dotyczące zabezpieczeń Azure Key Vault. Wdrożenie tych zaleceń pomoże Ci zrealizować swoje zobowiązania w zakresie zabezpieczeń zgodnie z opisem w naszym wspólnym modelu odpowiedzialności. Aby uzyskać więcej informacji na temat tego, co firma Microsoft może spełnić obowiązki dostawcy usług, Przeczytaj [udostępnione obowiązki w chmurze obliczeniowej](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Niektóre zalecenia zawarte w tym artykule mogą być automatycznie monitorowane przez Azure Security Center. Azure Security Center to pierwszy wiersz obrony w ochronie zasobów na platformie Azure. Okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu identyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu ich rozwiązywania.

- Aby uzyskać więcej informacji o Azure Security Center zaleceniach, zapoznaj się [z zaleceniami dotyczącymi zabezpieczeń w programie Azure Security Center](../../security-center/security-center-recommendations.md).
- Aby uzyskać informacje na temat Azure Security Center, zobacz [co to jest Azure Security Center?](../../security-center/security-center-introduction.md)

## <a name="data-protection"></a>Ochrona danych

| Zalecenie | Komentarze | Security Center |
|-|----|--|
|Włącz usuwanie nietrwałe | Funkcja [usuwania nietrwałego](soft-delete-overview.md) umożliwia odzyskanie usuniętych magazynów i obiektów magazynu |  - |
| Ograniczanie dostępu do danych magazynu  | Stosuj zasadę najniższych uprawnień i Ogranicz, którzy członkowie organizacji mają dostęp do danych magazynu |  - |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Ogranicz liczbę użytkowników z dostępem współautora | Jeśli użytkownik ma uprawnienia współautora do płaszczyzny zarządzania magazynu kluczy, użytkownik może udzielić sobie dostępu do płaszczyzny danych przez ustawienie zasad dostępu Key Vault. Należy ściśle kontrolować, kto ma dostęp do roli współautor do Twoich magazynów kluczy. Upewnij się, że tylko osoby, które mają dostęp do autoryzowanych osób, mogą uzyskać dostęp do swoich magazynów i zarządzać nimi. Możesz przeczytać [bezpieczny dostęp do magazynu kluczy](secure-your-key-vault.md) | - |

## <a name="monitoring"></a>Monitorowanie

| Zalecenie | Komentarze | Security Center |
|-|----|--|
 Dzienniki diagnostyczne w usłudze Key Vault powinny być włączone | Włącz dzienniki i zachowaj je na rok. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. | [Tak](../../security-center/security-center-identity-access.md) |
| Ogranicz, kto może uzyskiwać dostęp do dzienników magazynu kluczy platformy Azure | [Dzienniki Key Vault](logging.md)) Zapisuj informacje o działaniach wykonywanych w magazynie, takich jak tworzenie lub usuwanie magazynów, klucze, wpisy tajne i mogą być używane podczas badania |  - |

## <a name="networking"></a>Sieć

| Zalecenie | Komentarze | Security Center |
|-|----|--|
|Ogranicz ekspozycję sieci | Dostęp do sieci powinien być ograniczony do sieci wirtualnych używanych przez rozwiązania wymagające dostępu do magazynu. Przejrzyj informacje o [punktach końcowych usługi sieci wirtualnej dla Azure Key Vault](overview-vnet-service-endpoints.md)) | - |

## <a name="next-steps"></a>Następne kroki

Skontaktuj się z dostawcą aplikacji, aby sprawdzić, czy istnieją dodatkowe wymagania dotyczące zabezpieczeń. Aby uzyskać więcej informacji na temat tworzenia bezpiecznych aplikacji, zobacz temat [bezpieczna programowanie dokumentacji](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).