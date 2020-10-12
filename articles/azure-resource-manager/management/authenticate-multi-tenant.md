---
title: Uwierzytelnianie w wielu dzierżawach
description: Opisuje, w jaki sposób Azure Resource Manager obsługuje żądania uwierzytelniania między dzierżawcami.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75478827"
---
# <a name="authenticate-requests-across-tenants"></a>Uwierzytelnianie żądań między dzierżawcami

Podczas tworzenia aplikacji z wieloma dzierżawcami może być konieczne obsłużenie żądań uwierzytelniania zasobów należących do różnych dzierżawców. Typowy scenariusz polega na tym, że maszyna wirtualna w jednej dzierżawie musi przyłączyć się do sieci wirtualnej w innej dzierżawie. Azure Resource Manager udostępnia wartość nagłówka do przechowywania tokenów pomocniczych w celu uwierzytelniania żądań do innych dzierżawców.

## <a name="header-values-for-authentication"></a>Wartości nagłówka uwierzytelniania

Żądanie ma następujące wartości nagłówka uwierzytelniania:

| Nazwa nagłówka | Opis | Przykładowa wartość |
| ----------- | ----------- | ------------ |
| Autoryzacja | Token podstawowy | &lt;Podstawowy token okaziciela&gt; |
| x-MS-Authorization-pomocniczy | Tokeny pomocnicze | &lt;Pomocniczy pomocnik — token1 &gt; , EncryptedBearer &lt; pomocniczy, token2 pomocniczy — &gt; &lt; token3&gt; |

Nagłówek pomocniczy może zawierać maksymalnie trzy tokeny pomocnicze. 

W kodzie aplikacji z wieloma dzierżawcami Pobierz token uwierzytelniania dla innych dzierżawców i Zapisz je w nagłówkach pomocniczych. Wszystkie tokeny muszą należeć do tego samego użytkownika lub aplikacji. Użytkownik lub aplikacja musiała zostać zaproszeni jako gość do innych dzierżawców.

## <a name="processing-the-request"></a>Przetwarzanie żądania

Gdy aplikacja wysyła żądanie do Menedżer zasobów, żądanie jest uruchamiane w ramach tożsamości z podstawowego tokenu. Token podstawowy musi być prawidłowy i niewygasły. Token musi być z dzierżawy, który może zarządzać subskrypcją.

Gdy żądanie odwołuje się do zasobu z innej dzierżawy, Menedżer zasobów sprawdza tokeny pomocnicze, aby określić, czy można przetworzyć żądanie. Wszystkie tokeny pomocnicze w nagłówku muszą być prawidłowe i niewygasłe. W przypadku wygaśnięcia dowolnego tokenu Menedżer zasobów zwraca kod odpowiedzi 401. Odpowiedź zawiera identyfikator klienta i identyfikator dzierżawy z tokenu, który jest nieprawidłowy. Jeśli nagłówek pomocniczy zawiera prawidłowy token dla dzierżawcy, żądanie Cross dzierżawca jest przetwarzane.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o żądaniach uwierzytelniania, zobacz [przepływy uwierzytelniania i scenariusze aplikacji](../../active-directory/develop/authentication-flows-app-scenarios.md).
* Aby uzyskać więcej informacji o tokenach, zobacz [Azure Active Directory tokeny dostępu](../../active-directory/develop/access-tokens.md).
