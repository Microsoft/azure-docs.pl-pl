---
title: Łączenie Azure AD Identity Protection danych z platformą Azure — wskaźnikiem
description: Dowiedz się, jak przesyłać strumienie dzienników i alertów z Azure AD Identity Protection na platformę Azure, aby wyświetlić pulpity nawigacyjne, utworzyć niestandardowe alerty i poprawić badanie.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: f420e80f10072c440f5401c042d6370a061b1a7f
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632245"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>Łączenie danych z usługi Azure Active Directory (Azure AD) Identity Protection

Można przesyłać strumieniowo dzienniki z [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) do usługi Azure wskaźnikowego, aby przesyłać strumieniowo alerty do usługi Azure wskaźnikowego, aby wyświetlać pulpity nawigacyjne, tworzyć niestandardowe alerty i ulepszać badanie. Azure Active Directory Identity Protection zapewnia skonsolidowany widok narażonych użytkowników, wykrywanie ryzyka i luki w zabezpieczeniach, z możliwością natychmiastowego korygowania ryzyka i ustawiania zasad automatycznego korygowania przyszłych zdarzeń. Usługa jest oparta na doświadczeniu firmy Microsoft chroniącym tożsamości klientów i uzyskuje ogromną dokładność od sygnału od ponad 13 000 000 000 logowań dziennie. 

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć [subskrypcję Azure AD — wersja Premium P2](https://azure.microsoft.com/pricing/details/active-directory/).
- Użytkownik musi mieć uprawnienia administratora globalnego lub administratora zabezpieczeń.

## <a name="connect-to-azure-ad-identity-protection"></a>Połącz z Azure AD Identity Protection

Jeśli masz subskrypcję Azure AD — wersja Premium P2, Azure AD Identity Protection jest uwzględniony. W przypadku [włączenia jakichkolwiek zasad](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) i wygenerowania alertów dane alertów można łatwo przesłać strumieniowo do usługi Azure wskaźnikowej.

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Azure AD Identity Protection** .

1. Kliknij przycisk **Połącz** , aby rozpocząć przesyłanie strumieniowe zdarzeń Azure AD Identity Protection do usługi Azure wskaźnikowej.

1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów Azure AD Identity Protection, Wyszukaj pozycję **SecurityAlert**.

W celu przetestowania łącznika można [symulować wykrywania](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md) w celu wygenerowania przykładowych alertów, które zostaną przesłane strumieniowo do usługi Azure wskaźnikowej.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia Azure AD Identity Protection z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
