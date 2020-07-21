---
title: Znane problemy — Azure Digital bliźniaczych reprezentacji
description: Uzyskaj pomoc w rozpoznawaniu i eliminowaniu znanych problemów z usługą Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 8fce451d9b806d2fa9a4f3d9e1c117de0aaa9fc0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532292"
---
# <a name="known-issues-in-azure-digital-twins"></a>Znane problemy w usłudze Azure Digital bliźniaczych reprezentacji

Ten artykuł zawiera informacje o znanych problemach związanych z usługą Azure Digital bliźniaczych reprezentacji.

## <a name="managing-event-routes-in-the-azure-portal"></a>Zarządzanie trasami zdarzeń w Azure Portal

Jeśli logujesz się do portalu przy użyciu osobistego [**konto Microsoft (MSA)**](https://account.microsoft.com/account/Account), takiego jak *@outlook.com* konto, zobaczysz ekran z informacją, *że potrzebujesz uprawnień do wyświetlania tras zdarzeń* podczas próby zarządzania trasami zdarzeń w portalu, niezależnie od poziomu uprawnień.

:::image type="content" source="media/troubleshoot-known-issues/event-route-need-permission.png" alt-text="Zrzut ekranu przedstawiający Azure Portal błędu uprawnień podczas próby utworzenia tras zdarzeń w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji":::

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Użytkownicy, którzy obecnie nie mogą zarządzać trasami zdarzeń w portalu, nadal mogą zarządzać trasami zdarzeń za pomocą cyfrowych bliźniaczych reprezentacji interfejsów API lub interfejsu wiersza polecenia platformy Azure. Aby uniknąć tego problemu, można przełączać się do jednego z tych narzędzi do zarządzania trasami zdarzeń.

Instrukcje dla tego elementu można znaleźć w temacie [*How to: Manage Endpoints and Routes*](how-to-manage-routes.md).

### <a name="possible-causes"></a>Możliwe przyczyny

Użytkownik jest zalogowany w portalu przy użyciu osobistego [konto Microsoft (MSA)](https://account.microsoft.com/account/Account), takiego jak *@outlook.com* konto. Zarządzanie trasami zdarzeń w Azure Portal jest obecnie dostępne tylko dla użytkowników platformy Azure na kontach domeny firmowej.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400 błąd klienta: Nieprawidłowe żądanie" w Cloud Shell

Polecenia w Cloud Shell mogą sporadycznie kończyć się niepowodzeniem z powodu błędu "400 błąd klienta: Nieprawidłowe żądanie adresu URL: http://localhost:50342/oauth2/token ", po którym następuje pełny ślad stosu.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Można to rozwiązać przez ponowne uruchomienie `az login` polecenia i zakończenie kolejnych kroków logowania.

Następnie powinno być możliwe ponowne uruchomienie polecenia.

### <a name="possible-causes"></a>Możliwe przyczyny

Jest to wynik znanego problemu w Cloud Shell: [*pobieranie tokenu z Cloud Shell sporadycznie kończy się niepowodzeniem z powodu błędu 400 klienta: Nieprawidłowe żądanie*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat zabezpieczeń i uprawnień w usłudze Azure Digital bliźniaczych reprezentacji:
* [*Koncepcje: zabezpieczenia rozwiązań Digital bliźniaczych reprezentacji na platformie Azure*](concepts-security.md)