---
title: Jak obrócić klucz dostępu do usługi Azure SignalR Service
description: Informacje o tym, dlaczego klient powinien regularnie wymieniać klucze dostępu oraz jak to robić za pomocą witryny Azure Portal i interfejsu wiersza polecenia platformy Azure.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: cd03720b65883c5f43b11837d4a88a705209fb10
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764103"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Jak obrócić klucz dostępu do usługi Azure SignalR Service

Każde wystąpienie usługi Azure SignalR Service ma parę kluczy dostępu: podstawowy i pomocniczy. Są one używane do uwierzytelniania klientów usługi SignalR podczas wysyłania żądań do usługi. Klucze są skojarzone z adresem URL punktu końcowego wystąpienia. Należy dbać o bezpieczeństwo kluczy i wymieniać je regularnie. Otrzymujesz dwa klucze dostępu, więc możesz obsługiwać połączenia przy użyciu jednego klucza, a w tym czasie generować ponownie drugi.

## <a name="why-rotate-access-keys"></a>Dlaczego należy wymieniać klucze dostępu?

Ze względu na wymogi bezpieczeństwa i zgodności należy regularnie wymieniać klucze dostępu.

## <a name="regenerate-access-keys"></a>Generowanie ponowne kluczy dostępu

1. Przejdź do [Azure Portal](https://portal.azure.com/)i zaloguj się przy użyciu swoich poświadczeń.

1. Znajdź sekcję **Klucze** wystąpienia usługi Azure SignalR Service dotyczącą kluczy, które chcesz wygenerować ponownie.

1. Kliknij pozycję **Klucze** w menu nawigacji.

1. Wybierz pozycję **Wygeneruj ponownie klucz podstawowy** lub **Wygeneruj ponownie klucz pomocniczy**.

   Nowy klucz i odpowiadające mu parametry połączenia zostaną utworzone i wyświetlone.

   ![Ponowne generowanie kluczy](media/signalr-howto-key-rotation/regenerate-keys.png)

Klucze można także wygenerować ponownie za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/signalr/key#az_signalr_key_renew).

## <a name="update-configurations-with-new-connection-strings"></a>Aktualizowanie konfiguracji za pomocą nowych parametrów połączenia

1. Skopiuj nowo wygenerowane parametry połączenia.

1. Zaktualizuj wszystkie konfiguracje do użycia nowych parametrów połączenia.

1. Uruchom ponownie aplikację, jeśli będzie to potrzebne.

## <a name="forced-access-key-regeneration"></a>Wymuszone ponowne generowanie klucza dostępu

Usługa Azure SignalR Service może w niektórych sytuacjach wymuszać obowiązkowe generowanie ponowne klucza dostępu. Usługa powiadamia klientów za pośrednictwem poczty e-mail i powiadomienia w portalu. Jeśli otrzymasz taką wiadomość lub wystąpi błąd usługi spowodowany kluczem dostępu, wymień klucze, wykonując instrukcje przedstawione w tym przewodniku.

## <a name="next-steps"></a>Następne kroki

Regularne wymieniania kluczy dostępu to dobra praktyka.

W tym przewodniku opisano sposób generowania ponownego kluczy dostępu. Przejdź do następnych samouczków dotyczących uwierzytelniania przy użyciu standardu OAuth lub usługi Azure Functions.

> [!div class="nextstepaction"]
> [Integracja z tożsamością platformy ASP.NET Core](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Tworzenie bez serwera aplikacji czasu rzeczywistego z uwierzytelnianiem](./signalr-tutorial-authenticate-azure-functions.md)