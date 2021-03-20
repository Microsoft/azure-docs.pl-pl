---
title: Oznacz aplikację jako zweryfikowaną przez wydawcę — Microsoft Identity platform | Azure
description: Opisuje, jak oznaczyć aplikację jako zweryfikowaną przez wydawcę. Gdy aplikacja zostanie oznaczona jako zweryfikowana przez wydawcę, oznacza to, że Wydawca zweryfikował swoją tożsamość przy użyciu konta Microsoft Partner Network, które ukończyło proces weryfikacji i skojarzył to konto MPN z rejestracją aplikacji.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 2b658cdc58777274bb14f9e8069cef2facdb0479
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92363457"
---
# <a name="mark-your-app-as-publisher-verified"></a>Oznaczanie aplikacji jako zweryfikowanej przez wydawcę

Gdy rejestracja aplikacji ma zweryfikowanego wydawcy, oznacza to, że Wydawca aplikacji [zweryfikował](/partner-center/verification-responses) swoją tożsamość przy użyciu konta Microsoft Partner Network (MPN) i powiązana z tym kontem MPN z rejestracją aplikacji. W tym artykule opisano, jak ukończyć proces [weryfikacji wydawcy](publisher-verification-overview.md) .

## <a name="quickstart"></a>Szybki start
Jeśli użytkownik jest już zarejestrowany w Microsoft Partner Network (MPN) i spełnił [wymagania wstępne](publisher-verification-overview.md#requirements), można zacząć od razu: 

1. Logowanie się do [portalu rejestracji aplikacji](https://aka.ms/PublisherVerificationPreview) przy użyciu usługi [uwierzytelniania wieloskładnikowego](../fundamentals/concept-fundamentals-mfa-get-started.md)

1. Wybierz aplikację, a następnie kliknij pozycję **znakowanie**. 

1. Kliknij pozycję **Dodaj identyfikator MPN, aby zweryfikować wydawcę** i przejrzeć wymienione wymagania.

1. Wprowadź identyfikator MPN, a następnie kliknij przycisk **Weryfikuj i Zapisz**.

Aby uzyskać więcej informacji na temat określonych korzyści, wymagań i często zadawanych pytań, zobacz [Omówienie](publisher-verification-overview.md).


## <a name="mark-your-app-as-publisher-verified"></a>Oznaczanie aplikacji jako zweryfikowanej przez wydawcę
Upewnij się, że spełniono [wymagania wstępne](publisher-verification-overview.md#requirements), a następnie wykonaj następujące kroki, aby oznaczyć aplikacje jako zweryfikowane przez wydawcę.  

1. Upewnij się, że zalogowano się przy użyciu [uwierzytelniania wieloskładnikowego](../fundamentals/concept-fundamentals-mfa-get-started.md) na koncie organizacji (Azure AD) autoryzowanym do wprowadzania zmian w aplikacjach, które chcesz oznaczyć jako zweryfikowane przez wydawcę, a na koncie MPN w centrum partnerskim.

    - W usłudze Azure AD ten użytkownik musi być członkiem jednej z następujących [ról](../roles/permissions-reference.md): Administrator aplikacji, administrator aplikacji w chmurze, Administrator globalny. 

    - W centrum partnerskim ten użytkownik musi mieć następujące [role](/partner-center/permissions-overview): Administrator MPN, administrator kont lub Administrator globalny (jest to rola udostępniona w usłudze Azure AD). 

1. Przejdź do portalu rejestracji aplikacji:  

1. Kliknij aplikację, którą chcesz oznaczyć jako zweryfikowaną przez wydawcę, a następnie otwórz blok znakowania. 

1. Upewnij się, że ustawiono [domenę wydawcy](howto-configure-publisher-domain.md) aplikacji. 

1. Upewnij się, że domena wydawcy lub [domena niestandardowa](../fundamentals/add-custom-domain.md) DNS w dzierżawie pasuje do domeny adresu e-mail używanego podczas procesu weryfikacji dla konta usługi MPN.

1. Kliknij pozycję **Dodaj identyfikator MPN, aby zweryfikować wydawcę** w dolnej części strony. 

1. Wprowadź **identyfikator MPN**. Ten identyfikator MPN musi być dla: 

    - Prawidłowe konto Microsoft Partner Network, które ukończyło proces weryfikacji.  

    - Konto globalne partnera (PGA) dla organizacji. 

1. Kliknij przycisk **Weryfikuj i Zapisz**. 

1. Poczekaj na przetworzenie żądania, może to potrwać kilka minut. 

1. Jeśli weryfikacja zakończyła się pomyślnie, okno weryfikacji wydawcy zostanie zamknięte, zwracając Cię do bloku znakowania. Obok zweryfikowanej **nazwy wyświetlanej wydawcy** zobaczysz niebieski wskaźnik zweryfikowany. 

1. Użytkownicy, którzy otrzymają monit o zgodę na Twoją aplikację, rozpoczną korzystanie z tego wskaźnika wkrótce po pomyślnym zakończeniu procesu, chociaż może to potrwać pewien czas na replikację w całym systemie. 

1. Przetestuj tę funkcję, logując się do aplikacji i upewniając się, że zweryfikowany znaczek pojawia się na ekranie wyrażania zgody. Jeśli użytkownik jest zalogowany jako użytkownik, który już udzielił zgody na aplikację, można użyć parametru *monit = wyrażenie* zapytania, aby wymusić monit o zgodę. Ten parametr powinien służyć wyłącznie do testowania i nigdy nie jest sztywno zakodowany w żądaniach aplikacji.

1. Powtórz ten proces w miarę potrzeby, aby uzyskać dodatkowe aplikacje, dla których chcesz wyświetlić wskaźnik. Za pomocą Microsoft Graph można szybciej wykonać tę czynność, a polecenia cmdlet programu PowerShell będą dostępne wkrótce. Aby uzyskać więcej informacji, zobacz [Tworzenie wywołań grafów interfejsu API firmy Microsoft](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) . 

Gotowe. Daj nam znać, jeśli masz jakieś opinie na temat tego procesu, wyniki lub funkcję ogólnie. 

## <a name="next-steps"></a>Następne kroki
Jeśli występują problemy, Przeczytaj [informacje dotyczące rozwiązywania problemów](troubleshoot-publisher-verification.md).
