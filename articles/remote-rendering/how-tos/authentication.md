---
title: Authentication
description: Wyjaśnia, jak działa uwierzytelnianie
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.custom: has-adal-ref
ms.openlocfilehash: 34874e01b526a4d88ff4402edb0c3771c75a3726
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195301"
---
# <a name="configure-authentication"></a>Konfigurowanie uwierzytelniania

Zdalne renderowanie na platformie Azure używa tego samego mechanizmu uwierzytelniania jak [kotwice przestrzenne platformy Azure](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Klienci muszą ustawić *AccountKey*, *AuthenticationToken*lub *AccessToken* , aby pomyślnie wywołać interfejsy API REST. *AccountKey* można uzyskać na karcie "klucze" dla konta renderowania zdalnego na Azure Portal. *AuthenticationToken* jest tokenem usługi Azure AD, który można uzyskać przy użyciu [biblioteki ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries). *AccessToken* to token Mr, który można uzyskać z usługi Azure Mixed Reality Security Token Service (STS).

## <a name="authentication-for-deployed-applications"></a>Uwierzytelnianie wdrożonych aplikacji

 Użycie kluczy konta jest zalecane do szybkiego udostępniania, ale tylko w przypadku tworzenia/prototypowania. Zdecydowanie zaleca się, aby nie dostarczać aplikacji do środowiska produkcyjnego przy użyciu osadzonego klucza konta, a zamiast tego używać podejścia opartego na użytkowniku lub usłudze Azure AD.

 Uwierzytelnianie usługi Azure AD jest opisane w sekcji [uwierzytelnianie użytkowników usługi Azure AD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) w usłudze [Azure przestrzenny kotwics (ASA)](https://docs.microsoft.com/azure/spatial-anchors/) .

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Aby ułatwić kontrolę poziomu dostępu udzielonego aplikacjom, usługom lub użytkownikom usługi Azure AD w usłudze, zostały utworzone następujące role do przypisywania w razie potrzeby do kont zdalnego renderowania na platformie Azure:

* **Administrator zdalnego renderowania**: umożliwia użytkownikowi konwertowanie funkcji konwersji, sesji zarządzania, renderowania i diagnostyki na potrzeby renderowania zdalnego na platformie Azure.
* **Klient renderowania zdalnego**: umożliwia użytkownikowi zarządzanie sesjami, renderowaniem i diagnostyką w przypadku renderowania zdalnego na platformie Azure.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta](create-an-account.md)
* [Używanie interfejsów API frontonu platformy Azure do uwierzytelniania](frontend-apis.md)
* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)
