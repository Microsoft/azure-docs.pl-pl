---
title: Łączenie domeny z identyfikatorem zdecentralizowanym (wersja zapoznawcza)
description: Dowiedz się, jak powiązać DNS?
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: be7db16a8e3a827d08c0db637961bf004af1d621
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170242"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did"></a>Połącz domenę z identyfikatorem zdecentralizowanym (DID)

> [!IMPORTANT]
> Azure Active Directory poświadczenia do zweryfikowania są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule:
> [!div class="checklist"]
> * Dlaczego należy połączyć naszą naszą domenę?
> * Jak połączyć się z DIDs i domenami?
> * Jak działa proces łączenia domeny?
> * Jak działa logika weryfikacji/niezweryfikowanej domeny?

## <a name="prerequisites"></a>Wymagania wstępne

Aby połączyć się z domeną, należy wykonać następujące czynności.

- Ukończ [wprowadzenie](get-started-verifiable-credentials.md) i następny [zestaw samouczków](enable-your-tenant-verifiable-credentials.md).

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>Dlaczego należy połączyć naszą naszą domenę?

Obiekt został uruchomiony jako identyfikator, który nie jest zakotwiczony do istniejących systemów. Obiekt jest przydatny, ponieważ użytkownik lub organizacja może go określić i kontrolować. Jeśli jednostka, która współdziała z organizacją, nie wie, komu należy do, a następnie nie jest tak użyteczna.

Łączenie programu z domeną rozwiązuje pierwotny problem z zaufaniem, dzięki czemu każda jednostka może przeprowadzić kryptograficzną weryfikację relacji między a a domeną.

## <a name="how-do-we-link-dids-and-domains"></a>Jak połączyć się z DIDs i domenami?

Firma Microsoft tworzy łącze między domeną a przez implementację otwartego standardu pisanego przez zdecentralizowaną Fundację Identity Foundation o nazwie [dobrze znana konfiguracja](https://identity.foundation/.well-known/resources/did-configuration/). Usługa poświadczeń do zweryfikowania w programie Azure Active Directory (Azure AD) pomaga organizacji w utworzeniu połączenia między serwerem a i domeną przez uwzględnienie informacji o domenie podanych w programie i wygenerowanie dobrze znanego pliku konfiguracji:

1. Usługa Azure AD używa informacji o domenie, które podano podczas konfigurowania organizacji, aby napisać punkt końcowy usługi w ramach dokumentu. Wszystkie strony, które współpracują z użytkownikiem, mogą zobaczyć domenę, z którą zażądano skojarzenia.  

    ```json
        "service": [
          {
            "id": "#linkeddomains",
            "type": "LinkedDomains",
            "serviceEndpoint": {
              "origins": [
                "https://www.contoso.com/"
              ]
            }
          }
    ```

2. Zweryfikowana usługa poświadczeń w usłudze Azure AD generuje zgodny, dobrze znany zasób konfiguracji, który można hostować w domenie. Plik konfiguracji zawiera własne, zweryfikowane poświadczenia typu CredentialType "DomainLinkageCredential" podpisanego za pomocą elementu, który ma pierwotną domenę. Poniżej znajduje się przykład dokumentu konfiguracji, który jest przechowywany w adresie URL domeny katalogu głównego.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

Po udostępnieniu dobrze znanego pliku konfiguracji należy udostępnić plik przy użyciu nazwy domeny określonej podczas włączania usługi AAD do zweryfikowania poświadczeń.

* Hostowanie dobrze znanego pliku konfiguracji w katalogu głównym domeny.
* Nie używaj przekierowań.
* Rozpowszechnij plik konfiguracji przy użyciu protokołu HTTPS.

>[!IMPORTANT]
>Microsoft Authenticator nie honoruje przekierowań, określony adres URL musi być końcowym docelowym adresem URL.

## <a name="user-experience"></a>Doświadczenie użytkownika 

Gdy użytkownik przechodzi przez przepływ wystawiania lub prezentuje poświadczenia do zweryfikowania, powinny wiedzieć coś o organizacji i jego poprawce. Jeśli domena korzysta z naszego programu do zweryfikowania portfela poświadczeń, Microsoft Authenticator, sprawdza poprawność relacji z domeną w dokumencie DID i prezentuje użytkownikom dwa różne środowiska w zależności od wyniku.

## <a name="verified-domain"></a>Zweryfikowana domena

Zanim Microsoft Authenticator zostanie wyświetlona **zweryfikowana** ikona, konieczne jest wykonanie kilku czynności:

* Podpisywanie żądania Open ID (SIOP) z podpisem własnym musi mieć punkt końcowy usługi dla połączonej domeny.
* Domena główna nie korzysta z przekierowania i używa protokołu HTTPS.
* Domena wymieniona w dokumencie DID zawiera rozpoznawalny zasób dobrze znany.
* Dobrze znane poświadczenia zweryfikowanego zasobu są podpisane przy użyciu tego samego, który został użyty do podpisania SIOP, który Microsoft Authenticator używany do uruchamiania przepływu.

Jeśli wszystkie wspomniane wcześniej warunki są spełnione, Microsoft Authenticator wyświetla zweryfikowaną stronę i zawiera domenę, która została sprawdzona.

![nowe żądanie uprawnienia](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>Niezweryfikowana domena

Jeśli którekolwiek z powyższych wartości nie są spełnione, Microsoft Authenticator wyświetli ostrzeżenie o pełnej stronie użytkownika, że domena nie została zweryfikowana, użytkownik jest w środku ryzykownej transakcji i należy zachować ostrożność. Wybrano tę trasę, ponieważ:

* Element nie został zakotwiczony do domeny.
* Konfiguracja nie została prawidłowo skonfigurowana.
* Użytkownik, do którego prowadzi kontakt, jest złośliwy i faktycznie nie może udowodnić, że jest własnością domeny (ponieważ rzeczywiście nie są). Ze względu na ostatni punkt, należy połączyć się z domeną, z którą użytkownik zna, aby uniknąć propagowania komunikatu ostrzegawczego.

![Ostrzeżenie dotyczące niezweryfikowanej domeny na ekranie dodawania poświadczeń](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>Dystrybuuj dobrze znaną konfigurację

1. Przejdź do strony Ustawienia w obszarze poświadczenia do zweryfikowania i wybierz opcję **Weryfikuj tę domenę**

   ![Weryfikuj tę domenę w ustawieniach](media/how-to-dnsbind/settings-verify.png) 

2. Pobierz did-configuration.jsw pliku przedstawionym na poniższej ilustracji.

   ![Pobierz dobrze znaną konfigurację](media/how-to-dnsbind/verify-download.png) 

3. Skopiuj token JWT, Otwórz [JWT.MS](https://www.jwt.ms) i sprawdź, czy domena jest poprawna.

4. Skopiuj i Otwórz [Eksploratora sieci jonu](https://identity.foundation/ion/explorer) , aby sprawdzić, czy ta sama domena jest dołączona do dokumentu. 

5. Hostowanie dobrze znanego zasobu konfiguracji w określonej lokalizacji. Przykład: https://www.example.com/.well-known/did-configuration.json

6. Przetestuj wystawianie lub prezentowanie za pomocą Microsoft Authenticator, aby sprawdzić poprawność. Upewnij się, że ustawienie w obszarze wystawca "Ostrzegaj o niebezpiecznych aplikacjach" jest włączone.

>[!NOTE]
>Domyślnie opcja "Ostrzegaj o niebezpiecznych aplikacjach" jest włączona.

Gratulacje, masz teraz możliwość uruchomienia sieci Web zaufania z Twoją usługą.

## <a name="next-steps"></a>Następne kroki

Jeśli podczas dołączania wprowadzisz niewłaściwe informacje o domenie, zdecydujesz się je zmienić, musisz [zrezygnować](how-to-opt-out.md)z użycia. W tej chwili nie obsługujemy aktualizowania Twojego dokumentu. Wycofaj i Cofnij z powrotem, aby utworzyć zupełnie nowy.