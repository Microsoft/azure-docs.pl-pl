---
title: Łączenie domeny ze zdecentralizowanym identyfikatorem (DID) (wersja zapoznawcza) — Azure Active Directory poświadczenia weryfikowalne
description: Dowiedz się, jak powiązać DNS?
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: ad5bb6e45479b4cccfa0b002427066439135e468
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588449"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did"></a>Łączenie domeny ze zdecentralizowanym identyfikatorem (DID)

> [!IMPORTANT]
> Azure Active Directory Poświadczenia weryfikowalne są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule:
> [!div class="checklist"]
> * Dlaczego musimy połączyć nasze DID z naszą domeną?
> * Jak połączyć identyfikatory DID i domeny?
> * Jak działa proces łączenia domen?
> * Jak działa logika weryfikowania/niezweryfikowanego domeny?

## <a name="prerequisites"></a>Wymagania wstępne

Aby połączyć domenę DID z domeną, należy ukończyć następujące czynności.

- Ukończ [samouczek Wprowadzenie](get-started-verifiable-credentials.md) [kolejnych samouczków.](enable-your-tenant-verifiable-credentials.md)

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>Dlaczego musimy połączyć nasze DID z naszą domeną?

Identyfikator DID zaczyna się od identyfikatora, który nie jest zakotwiczony w istniejących systemach. Did jest przydatne, ponieważ użytkownik lub organizacja może być właścicielem i kontrolować go. Jeśli jednostka, która wchodzi w interakcję z organizacją, nie wie, do kogo należy identyfikator DID, identyfikator DID nie jest tak przydatny.

Połączenie did do domeny rozwiązuje początkowy problem zaufania, umożliwiając każdej jednostce kryptograficznie zweryfikować relację między DID i domeny.

## <a name="how-do-we-link-dids-and-domains"></a>Jak połączyć identyfikatory DID i domeny?

Łączymy domenę z rekordem DID przez zaimplementowanie otwartego standardu napisanego przez zdecentralizowaną platformę Identity Foundation o nazwie [Dobrze znana konfiguracja DID](https://identity.foundation/.well-known/resources/did-configuration/). Weryfikowalna usługa poświadczeń w usłudze Azure Active Directory (Azure AD) pomaga organizacji nawiązać połączenie między domeną DID i domeną, dołączona do informacji o domenie podanych w did i wygenerowaniu dobrze znanego pliku konfiguracji:

1. Usługa Azure AD używa informacji o domenie, które są podane podczas konfigurowania organizacji, do napisania punktu końcowego usługi w dokumencie DID. Wszystkie strony, które wchodzą w interakcję z Twoim DID, mogą zobaczyć domenę, z którym twoje DID jest skojarzone.  

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

2. Weryfikowalna usługa poświadczeń w usłudze Azure AD generuje zgodny, dobrze znany zasób konfiguracji, który można hostować w domenie. Plik konfiguracji zawiera wydane samodzielnie weryfikowalne poświadczenie credentialType "DomainLinkageCredential" podpisane za pomocą twojej domeny DID, która ma źródło Twojej domeny. Oto przykładowy plik doc konfiguracji, który jest przechowywany pod adresem URL domeny głównej.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

Po udostępnieniu dobrze znanego pliku konfiguracji należy udostępnić go przy użyciu nazwy domeny określonej podczas włączania usługi AAD na potrzeby weryfikowalnych poświadczeń.

* Hostuj dobrze znany plik konfiguracji DID w katalogu głównym domeny.
* Nie używaj przekierowań.
* Użyj protokołu https, aby dystrybuować plik konfiguracji.

>[!IMPORTANT]
>Microsoft Authenticator nie honoruje przekierowań, określony adres URL musi być końcowym docelowym adresem URL.

## <a name="user-experience"></a>Środowisko użytkownika 

Gdy użytkownik przechodzi przez przepływ wystawiania lub prezentuje weryfikowalne poświadczenia, powinien wiedzieć coś o organizacji i jej działaniach. Jeśli domena, w ramach Microsoft Authenticator poświadczeń, sprawdza poprawność relacji DID z domeną w dokumencie DID i przedstawia użytkownikom dwa różne doświadczenia w zależności od wyniku.

## <a name="verified-domain"></a>Zweryfikowana domena

Aby Microsoft Authenticator ikona **Zweryfikowano,** należy sprawdzić kilka rzeczy:

* Żądanie DID podpisywania samodzielnie wystawionego otwartego identyfikatora (SIOP) musi mieć punkt końcowy usługi dla połączonej domeny.
* Domena główna nie używa przekierowania i używa protokołu HTTPS.
* Domena wymieniona w dokumencie DID ma rozpoznawalny dobrze znany zasób.
* Poświadczenia weryfikowalne dobrze znanego zasobu są podpisane za pomocą tego samego konta DID, które zostało użyte do podpisania usługi SIOP Microsoft Authenticator użytej do uruchomienia przepływu.

Jeśli wszystkie wymienione wcześniej wartości są prawdziwe, Microsoft Authenticator zostanie wyświetlona zweryfikowana strona z domeną, która została zweryfikowana.

![nowe żądanie uprawnień](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>Niezweryfikowana domena

Jeśli którekolwiek z powyższych nie jest prawdziwe, Microsoft Authenticator wyświetli użytkownikowi ostrzeżenie na pełnej stronie, że domena jest niezweryfikowana, użytkownik jest w trakcie ryzykownych transakcji i należy zachować ostrożność. Wybraliśmy tę trasę, ponieważ:

* Did nie jest zakotwiczony w domenie.
* Konfiguracja nie została prawidłowo skonfigurowany.
* Czy użytkownik wchodzi w interakcję z jest złośliwy i faktycznie nie może udowodnić, że jest właścicielem domeny (ponieważ tak naprawdę nie). Ze względu na ten ostatni punkt konieczne jest połączenie działania z domeną, która jest znana użytkownikowi, aby uniknąć propagacji komunikatu ostrzegawczego.

![ostrzeżenie niezweryfikowanych domen na ekranie dodawania poświadczeń](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>Dystrybuowanie dobrze znanej konfiguracji

1. Przejdź do strony Ustawienia w owalnym poświadczeniu i wybierz pozycję **Sprawdź tę domenę**

   ![Sprawdź tę domenę w ustawieniach](media/how-to-dnsbind/settings-verify.png) 

2. Pobierz plik did-configuration.jspokazany na poniższej ilustracji.

   ![Pobieranie dobrze znanej konfiguracji](media/how-to-dnsbind/verify-download.png) 

3. Skopiuj JWT, otwórz [jwt.ms](https://www.jwt.ms) i sprawdź poprawność domeny.

4. Skopiuj kopię did i otwórz Eksplorator sieci [ION,](https://identity.foundation/ion/explorer) aby sprawdzić, czy ta sama domena znajduje się w dokumencie DID. 

5. Hostuj dobrze znany zasób konfiguracji w określonej lokalizacji. Przykład: `https://www.example.com/.well-known/did-configuration.json`

6. Przetestuj wystawianie lub prezentowanie Microsoft Authenticator weryfikacji. Upewnij się, że ustawienie w aplikacji Authenticator "Ostrzegaj o niebezpiecznych aplikacjach" jest wł.

>[!NOTE]
>Domyślnie opcja "Ostrzegaj o niebezpiecznych aplikacjach" jest włączona.

Gratulacje, masz teraz uruchomioną sieć zaufania za pomocą swojej pracy!

## <a name="next-steps"></a>Następne kroki

Jeśli podczas dołączania zostaną podane nieprawidłowe informacje o domenie, które chcesz zmienić, musisz [zrezygnować z tej opcji.](how-to-opt-out.md) Obecnie nie obsługujemy aktualizowania dokumentu DID. Rezygnacja i rezygnacja z tej opcji spowoduje utworzenie zupełnie nowej opcji DID.
