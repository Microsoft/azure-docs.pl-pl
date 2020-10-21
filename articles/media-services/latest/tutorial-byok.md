---
title: Klucze zarządzane przez klienta lub Przenieś własne klucze (BYOK) za pomocą Media Services
description: Jest to samouczek dotyczący korzystania z kluczy zarządzanych przez klienta za pomocą konta usługi Media Services Storage
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c26da9d888bbcdf72c052fa4bd7fcdf443a2d8f7
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326151"
---
# <a name="tutorial-use-customer-managed-keys-or-bring-your-own-key-byok-with-media-services"></a>Samouczek: Używanie kluczy zarządzanych przez klienta lub dołączenie własnego klucza (BYOK) za pomocą Media Services

Za pomocą interfejsu API 2020-05-01 można użyć klucza RSA zarządzanego przez klienta z kontem Media Services, które ma tożsamość zarządzaną przez system.  Ten samouczek obejmuje kolekcję i środowisko programu post do wysyłania żądań REST do usług platformy Azure.  Używane usługi:

- Azure Active Directory rejestracji aplikacji dla programu Poster
- Microsoft Graph API
- Azure Storage
- W usłudze Azure Key Vault
- Media Services

W ramach tego samouczka nauczysz się używać programu Poster do:

> [!div class="checklist"]
> * Uzyskaj tokeny do użycia z usługami platformy Azure.
> * Utwórz grupę zasobów i konto magazynu.
> * Tworzenie konta Media Services przy użyciu tożsamości zarządzanej przez system
> * Utwórz Key Vault do przechowywania klucza RSA (zarządzane przez klienta) do użycia z kontem magazynu.
> * Zaktualizuj konto Media Services, aby użyć klucza RSA z kontem magazynu.
> * Używanie zmiennych w programie Poster.

Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- Zarejestruj jednostkę usługi z odpowiednimi uprawnieniami.
- Zainstaluj program [Poster](https://www.postman.com).
- Pobierz kolekcję programu Poster dla tego samouczka na [platformie Azure przykłady: Media-Services-Customer-Managed-Keys-BYOK](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Rejestrowanie jednostki usługi z wymaganymi uprawnieniami

[Utwórz nazwę główną usługi](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).  Aby uzyskać klucz tajny jednostki usługi, zobacz [dwie opcje](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options) .  Zanotuj wpis tajny w miejscu, tak jak po opuszczeniu strony wpisu tajnego w portalu, będzie niedostępny.

Aby wykonać zadania w tym samouczku, jednostka usługi musi mieć następujące uprawnienia.

![uprawnienia wymagające dla jednostki usługi](./media/tutorial-byok/service-principal-permissions-1.png)

### <a name="install-postman"></a>Zainstaluj program Poster

Jeśli program nie został jeszcze zainstalowany na platformie Azure, możesz go uzyskać pod adresem [Postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>Pobieranie i importowanie kolekcji

Pobierz kolekcję programu Poster dla tego samouczka na [platformie Azure przykłady: Media-Services-Customer-Managed-Keys-BYOK](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

## <a name="installation-of-collection-and-environment"></a>Instalacja kolekcji i środowiska

1. Uruchom notkę.
1. Wybierz pozycję **Importuj**.
1. Wybierz pozycję **Przekaż pliki**.
1. Przejdź do lokalizacji, w której zapisano pliki kolekcji i środowiska.
1. Wybierz plik kolekcji i środowiska.
1. Wybierz pozycję **Open** (Otwórz).  (Zostanie wyświetlone ostrzeżenie, że pliki nie zostaną zaimportowane jako interfejs API, ale jako kolekcje.  Jest to dokładne.  Jest to potrzebne.
1. Ta kolekcja będzie teraz wyświetlana w kolekcjach jako BYOK.
1. Zmienne środowiskowe będą teraz wyświetlane w Twoich środowiskach.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>Zrozumienie żądań interfejsu API REST w kolekcji

Kolekcja zawiera następujące żądania interfejsu API REST. Żądania muszą być wysyłane w sekwencji pod warunkiem, że większość z nich ma Skrypty testowe, które dynamicznie tworzą zmienne globalne dla następnego (lub kolejnego) żądania w sekwencji. Nie jest konieczne ręczne tworzenie zmiennych globalnych.

W programie Poster zobaczysz te zmienne zawarte w `{{ }}` nawiasach.  Na przykład `{{bearerToken}}`.

1. Pobieranie tokenu usługi AAD — Test ustawia zmienną globalną *bearerToken*
2. Pobierz token grafu — Test ustawia zmienną globalną *graphToken*
3. Pobierz szczegóły jednostki usługi — Test ustawia zmienną globalną *servicePrincipalObjectId*
4. Tworzenie konta magazynu — Test ustawia zmienną globalną *storageAccountId*
5. Utwórz konto Media Services przy użyciu tożsamości zarządzanej przez system — Test ustawia zmienną globalną *principalId*
6. Tworzenie Key Vault, udzielanie dostępu do jednostki usługi — Test ustawia zmienną globalną *keyVaultId*
7. Pobierz token Key Vault — *keyVaultTokena* zmienna globalna zestawu testów
8. Utwórz klucz RSA w magazynie kluczy — *keyId* zmiennej globalnej zestawu testów
9. Zaktualizuj konto Media Services, aby użyć klucza z kontem magazynu — nie ma skryptu testowego dla tego żądania.

## <a name="define-environment-variables"></a>Definiuj zmienne środowiskowe

1. Przejdź do pobranego środowiska, wybierając listę rozwijaną środowiska.
1. Ustanów zmienne środowiskowe w programie Poster. Są one również używane jako zmienne zawarte w `{{ }}` nawiasach.  Na przykład `{{tenantId}}`.

    * *tenantId* = identyfikator dzierżawy
    * *servicePrincipalId* = identyfikator jednostki usługi nawiązanej przy użyciu ulubionej metody: Portal, interfejs wiersza polecenia i tak dalej.
    * *servicePrincipalSecret* = wpis tajny utworzony dla jednostki usługi
    * *subskrypcja* = Identyfikator subskrypcji
    * *storagename* = nazwa, którą chcesz nadać magazynowi
    * *AccountName* = nazwa konta usługi Media, którego chcesz użyć
    * Identyfikator *magazynu* kluczy = nazwa Key Vault, której chcesz użyć
    * *resourceLocation* = środkowe (lub gdzie kiedykolwiek chcesz umieścić swoje zasoby.  Ta kolekcja została przetestowana tylko za pomocą centrali.)
    * *resourceName* = nazwa grupy zasobów

    Następujące zmienne są standardowe dla pracy z zasobami platformy Azure, więc nie ma potrzeby ich zmiany.

    * *armResource* = `https://management.core.windows.net`
    * *graphResource* = `https://graph.windows.net/`
    * *keyVaultResource* = `https://vault.azure.net`
    * *armEndpoint* = `management.azure.com`
    * *graphEndpoint* = `graph.windows.net`
    * *aadEndpoint* = `login.microsoftonline.com`
    * *keyVaultDomainSuffix* = `vault.azure.net`

## <a name="send-the-requests"></a>Wyślij żądania

Po zdefiniowaniu zmiennych środowiskowych można uruchomić żądania pojedynczo w powyższej sekwencji lub użyć modułu uruchamiającego Poster do uruchomienia kolekcji.

## <a name="change-the-key"></a>Zmień klucz

Usługa Media Services automatycznie wykryje, kiedy klucz został zmieniony.  Aby to przetestować, Utwórz inną wersję klucza dla tego samego klucza. Media Services powinien wykryć ten klucz w mniej niż 15 minut.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z utworzonych zasobów, nie **musisz już otrzymywać opłat**, usuń je.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak to zrobić...
> [!div class="nextstepaction"]
> [Koduj plik zdalny na podstawie adresu URL i strumieniowego wideo przy użyciu interfejsu REST](stream-files-tutorial-with-rest.md)
