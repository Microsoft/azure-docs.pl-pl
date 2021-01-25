---
title: Różnice dotyczące walidacji według obsługiwanych typów kont | Azure
titleSuffix: Microsoft identity platform
description: Zapoznaj się z różnicami w walidacji różnych właściwości dla różnych obsługiwanych typów kont podczas rejestrowania aplikacji za pomocą platformy tożsamości firmy Microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 07/21/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 77521150e73014c5568003597059a9d32f6e80ee
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752960"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Różnice dotyczące walidacji według obsługiwanych typów kont (signInAudience)

Podczas rejestrowania aplikacji za pomocą platformy tożsamości firmy Microsoft dla deweloperów zostanie wyświetlony monit o wybranie typów kont obsługiwanych przez aplikację. W obiekcie aplikacji i manifeście ta właściwość ma wartość `signInAudience` .

Dostępne są następujące opcje:

- *AzureADMyOrg*: tylko konta w katalogu organizacyjnym, w którym zarejestrowano aplikację (pojedyncza dzierżawa)
- *AzureADMultipleOrgs*: konta w dowolnym katalogu organizacji (wiele dzierżawców)
- *AzureADandPersonalMicrosoftAccount*: konta w dowolnym katalogu organizacji (z wieloma dzierżawcami) i osobistymi kontami Microsoft (na przykład Skype, Xbox i Outlook.com)

W przypadku zarejestrowanych aplikacji można znaleźć wartość obsługiwanych typów kont w sekcji **uwierzytelnianie** aplikacji. Można go również znaleźć pod `signInAudience` właściwością w **manifeście**.

Wartość wybrana dla tej właściwości ma wpływ na inne właściwości obiektu aplikacji. W związku z tym, jeśli zmienisz tę właściwość, może być konieczne najpierw zmienić inne właściwości.

Zapoznaj się z poniższą tabelą, aby poznać różnice między walidacją różnych właściwości dla różnych obsługiwanych typów kont.

| Właściwość | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` i `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| Identyfikator URI identyfikatora aplikacji ( `identifierURIs` )  | Musi być unikatowa w dzierżawie <br><br> Obsługiwane są schematy urn:// <br><br> Symbole wieloznaczne nie są obsługiwane <br><br> Obsługiwane są ciągi i fragmenty zapytań <br><br> Maksymalna długość 255 znaków <br><br> Brak limitu * na liczbie identifierURIs  | Musi być globalnie unikatowa <br><br> Obsługiwane są schematy urn:// <br><br> Symbole wieloznaczne nie są obsługiwane <br><br> Obsługiwane są ciągi i fragmenty zapytań <br><br> Maksymalna długość 255 znaków <br><br> Brak limitu * na liczbie identifierURIs | Musi być globalnie unikatowa <br><br> schematy urn://nie są obsługiwane <br><br> Symbole wieloznaczne, fragmenty i ciągi zapytań nie są obsługiwane <br><br> Maksymalna długość 120 znaków <br><br> Maksymalnie 50 identifierURIs |
| Certyfikaty ( `keyCredentials` ) | Symetryczny klucz podpisywania | Symetryczny klucz podpisywania | Szyfrowanie i asymetryczne klucze podpisywania | 
| Wpisy tajne klienta ( `passwordCredentials` ) | Bez limitu * | Bez limitu * | Jeśli liveSDK jest włączona: maksymalnie 2 wpisy tajne klienta | 
| Identyfikatory URI przekierowania ( `replyURLs` ) | Aby uzyskać więcej informacji [, zobacz ograniczenia i ograniczenia adresów URL przekierowania URI/odpowiedzi](reply-url.md) . | | | 
| Uprawnienia interfejsu API ( `requiredResourceAccess` ) | Bez limitu * | Bez limitu * | Maksymalnie 50 zasobów na aplikację i 30 uprawnień dla każdego zasobu (np. Microsoft Graph). Łączny limit 200 dla aplikacji (uprawnienia x zasobów). | 
| Zakresy zdefiniowane przez ten interfejs API ( `oauth2Permissions` ) | Maksymalna długość nazwy zakresu wynosząca 120 znaków <br><br> Brak limitu * na określonej liczbie zakresów | Maksymalna długość nazwy zakresu wynosząca 120 znaków <br><br> Brak limitu * na określonej liczbie zakresów |  Maksymalna długość nazwy zakresu wynosząca 40 znaków <br><br> Zdefiniowano maksymalnie 100 zakresów | 
| Autoryzowane aplikacje klienckie ( `preAuthorizedApplications` ) | Bez limitu * | Bez limitu * | Suma maksymalnie 500 <br><br> Zdefiniowano maksymalnie 100 aplikacji klienckich <br><br> Zdefiniowano maksymalnie 30 zakresów na klienta | 
| appRoles | Obsługiwane <br> Bez limitu * | Obsługiwane <br> Bez limitu * | Nieobsługiwane | 
| Adres URL wylogowywania | http://localhost jest dozwolony <br><br> Maksymalna długość 255 znaków | http://localhost jest dozwolony <br><br> Maksymalna długość 255 znaków | <br><br> https://localhost jest dozwolony, http://localhost kończy się niepowodzeniem dla MSA <br><br> Maksymalna długość 255 znaków <br><br> Schemat HTTP jest niedozwolony <br><br> Symbole wieloznaczne nie są obsługiwane | 

* Istnieje globalny limit dotyczący około 1000 elementów we wszystkich właściwościach kolekcji w obiekcie App

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [rejestrowaniu aplikacji](app-objects-and-service-principals.md)
- Informacje o [manifeście aplikacji](reference-app-manifest.md)
