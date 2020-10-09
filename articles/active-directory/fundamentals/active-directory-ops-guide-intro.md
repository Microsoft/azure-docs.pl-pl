---
title: Przewodnik dotyczący odwołań Azure Active Directory operacji
description: W tym przewodniku odwołuje się opis operacji sprawdzania i działań, które należy wykonać w celu zabezpieczenia i utrzymania zarządzania tożsamościami i dostępem, uwierzytelniania, zarządzania i operacji.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74535316"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Przewodnik dotyczący odwołań Azure Active Directory operacji

W tym przewodniku odwołuje się opis operacji sprawdzania i działań, które należy wykonać w celu zabezpieczenia i utrzymania następujących zagadnień:

- **[Zarządzanie tożsamościami i dostępem](active-directory-ops-guide-iam.md)** — możliwość zarządzania cyklem życia tożsamości i ich uprawnień.
- **[Zarządzanie uwierzytelnianiem](active-directory-ops-guide-auth.md)** — możliwość zarządzania poświadczeniami, definiowania środowiska uwierzytelniania, delegowania przypisywania, określania użycia i definiowania zasad dostępu w oparciu o stan zabezpieczeń przedsiębiorstwa.
- **[Zarządzanie](active-directory-ops-guide-govern.md)** — zdolność do oceny i zaświadczania dostępu, który nie ma uprawnień uprzywilejowanych oraz tożsamości uprzywilejowanych, inspekcji i kontroli w środowisku.
- **[Operacje](active-directory-ops-guide-ops.md)** — Optymalizuj Azure Active Directory operacji (Azure AD).

Niektóre zalecenia mogą nie dotyczyć wszystkich klientów, na przykład AD FS najlepsze rozwiązania mogą nie mieć zastosowania, jeśli Twoja organizacja korzysta z synchronizacji skrótów haseł.

> [!NOTE]
> Te zalecenia są aktualne w dniu opublikowania, ale mogą ulec zmianie w czasie. Organizacje powinny stale sprawdzać swoją tożsamość jako produkty i usługi firmy Microsoft, które są roznoszone wraz z upływem czasu. Zalecenia mogą ulec zmianie, gdy organizacje subskrybują inną licencję Azure AD — wersja Premiumową. Na przykład Azure AD — wersja Premium P2 będzie zawierać więcej zaleceń dotyczących ładu.

## <a name="stakeholders"></a>Zainteresowanych

Każda sekcja w tym przewodniku referencyjnym zaleca przypisanie uczestników projektu do pomyślnego zaplanowania i zaimplementowania kluczowych zadań. Poniższa tabela zawiera listę wszystkich uczestników projektu w tym przewodniku:

| Uczestnik projektu | Opis |
| :- | :- |
| Zespół operacji IAM | Ten zespół obsługuje zarządzanie codziennymi operacjami systemu zarządzania tożsamościami i dostępem |
| Zespół produktywności | Ten zespół jest właścicielem i zarządza aplikacjami produkcyjnymi, takimi jak poczta e-mail, udostępnianie plików i współpraca, wiadomości błyskawiczne i konferencje. |
| Właściciel aplikacji | Ten zespół jest właścicielem konkretnej aplikacji od firmy i zazwyczaj jest to perspektywa techniczna w organizacji. |
| Zespół architektury InfoSec | Ten zespół planuje i projektuje praktyki bezpieczeństwa informacji organizacji. |
| Zespół ds. operacji InfoSec | Ten zespół uruchamia i monitoruje zaimplementowane praktyki bezpieczeństwa informacji zespołu architektury InfoSec. |

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z [kontrolami i akcjami zarządzania dostępem i tożsamościami](active-directory-ops-guide-iam.md).
