---
title: Jak odwołać poświadczenia zweryfikowane jako wystawca Azure Active Directory poświadczenia do zweryfikowania
description: Dowiedz się, jak odwołać zweryfikowane poświadczenia, które wydano
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 50f270dde59860e7c9dd2ac1b35039d5855859e5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222847"
---
# <a name="revoke-a-previously-issued-verifiable-credential-preview"></a>Odwołaj wcześniej wydane zweryfikowane poświadczenia (wersja zapoznawcza)

W ramach procesu pracy z poświadczeniami do zweryfikowania (VCs) nie ma potrzeby wystawiania poświadczeń, ale czasami trzeba również je odwołać. W tym artykule przejdziemy do części właściwości **status** języka VC i przejdziesz bliżej procesu odwoływania, dlatego warto odwołać poświadczenia i wprowadzić pewne konsekwencje dotyczące danych i prywatności.

> [!IMPORTANT]
> Azure Active Directory poświadczenia do zweryfikowania są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="status-property-in-verifiable-credentials-specification"></a>Właściwość stanu w specyfikacji zweryfikowanych poświadczeń

Zanim będziemy mogli zrozumieć skutki odwoływania poświadczeń do zweryfikowania, może to pomóc w sprawdzeniu, co to jest **Sprawdzanie stanu** i jak działa już dziś.

[Specyfikacja poświadczeń do zweryfikowania W3C](https://www.w3.org/TR/vc-data-model/) odwołuje się do właściwości **status** w sekcji [4,9:](https://www.w3.org/TR/vc-data-model/#status)

"Ta specyfikacja definiuje następującą właściwość **credentialStatus** na potrzeby odnajdywania informacji o bieżącym stanie poświadczeń do zweryfikowania, na przykład w przypadku wstrzymania lub odwołania".

Jednakże specyfikacja W3C nie definiuje formatu sposobu **sprawdzania stanu** .

"Definiowanie modelu danych, formatów i protokołów dla schematów stanu jest poza zakresem dla tej specyfikacji. Rejestr rozszerzenia poświadczeń do zweryfikowania [VC-EXTENSION-REGISTRY] istnieje, który zawiera dostępne schematy stanu dla realizatorów, którzy chcą zaimplementować sprawdzanie poprawności stanu poświadczeń. "

>[!NOTE]
>Na razie implementacja kontroli stanu firmy Microsoft jest zastrzeżona, ale aktywnie pracujemy nad standardem.

## <a name="how-does-the-status-property-work"></a>Jak działa Właściwość **status** ?

W każdym wystawionym przez firmę Microsoft poświadczeniem do zweryfikowania istnieje atrybut o nazwie credentialStatus. Jest on wypełniony interfejsem API stanu zarządzanym przez firmę Microsoft w Twoim imieniu. Oto przykład tego, jak wygląda.

```json
    "credentialStatus": {
      "id": "https://portableidentitycards.azure-api.net/v1.0/7952032d-d1f3-4c65-993f-1112dab7e191/portableIdentities/card/status",
      "type": "PortableIdentityCardServiceCredentialStatus2020"
    }
```

Zestaw SDK do zweryfikowania poświadczeń Open Source obsługuje wywoływanie interfejsu API stanu i dostarczanie niezbędnych danych.

Po wywołaniu interfejsu API i udostępnieniu odpowiednich informacji interfejs API zwróci wartość true lub false. Wartość true oznacza, że zweryfikowane poświadczenia nadal są aktywne w wystawcy, a wartość fałszywa potwierdza, że zweryfikowane poświadczenia zostały aktywnie odwołane przez wystawcy.

## <a name="why-you-may-want-to-revoke-a-vc"></a>Dlaczego warto odwołać VC?

Każdy klient będzie miał własny unikatowy powód, dla którego chcesz odwołać poświadczenia umożliwiające zweryfikowanie, ale poniżej przedstawiono niektóre często spotykane motywy. 

- Identyfikator ucznia: Student nie jest już aktywnym uczniem na Uniwersytecie.
- IDENTYFIKATOR pracownika: pracownik nie jest już aktywnym pracownikiem.
- Licencja na sterowniki stanu: sterownik już nie przebywa w tym stanie.

## <a name="how-to-set-up-a-verifiable-credential-with-the-ability-to-revoke"></a>Jak skonfigurować zweryfikowane poświadczenia z możliwością odwołania

Wszystkie dane poświadczeń do zweryfikowania nie są domyślnie przechowywane w firmie Microsoft. W związku z tym nie mamy żadnych danych do odwołania, aby odwołać konkretny Identyfikator poświadczenia do zweryfikowania. Wystawca musi określić określone pole z atrybutu poświadczenia zweryfikowane dla firmy Microsoft do indeksowania, a następnie do soli i skrótu.

>[!NOTE]
>Mieszanie jest jednokierunkową operacją kryptograficzną, która przekształca dane wejściowe, nazywane a ```preimage``` i tworzy wyjście o nazwie hash o stałej długości. W tym momencie nie jest to możliwe do obliczeń w celu odwrócenia operacji mieszania.

Możesz poinformować firmę Microsoft, który atrybut poświadczenia do zweryfikowania, który ma być indeksowany. Jest to implikacje indeksowania polegające na tym, że indeksowane wartości mogą służyć do wyszukiwania zweryfikowanych poświadczeń dla systemu VCs, który ma zostać odwołany.

**Przykład:** Alicja jest pracownikiem Woodgrove. Alicja z lewej strony Woodgrove do pracy w firmie Contoso. Jan, administrator IT dla Woodgrove, wyszukuje wiadomość e-mail dla programu Alicja w zapytaniu wyszukiwania poświadczeń do zweryfikowania. W tym przykładzie Janina Zaindeksowano pole adres e-mail zweryfikowanego poświadczenia pracownika Woodgrove. 

Poniżej znajduje się przykład modyfikacji pliku reguł w celu uwzględnienia indeksu.

```json
{
  "attestations": {
    "idTokens": [
      { 
        "mapping": {
          "Name": { "claim": "name" },
          "email": { "claim": "email", "indexed": true}
        },
        "configuration": "https://login.microsoftonline.com/tenant-id-here7/v2.0/.well-known/openid-configuration",
        "client_id": "c0d6b785-7a08-494e-8f63-c30744c3be2f",
        "redirect_uri": "vcclient://openid"
      }
    ]
  },
  "validityInterval": 25920000,
  "vc": {
    "type": ["WoodgroveEmployee"]
  }
}
```

>[!NOTE]
>Tylko jeden atrybut może być indeksowany z pliku reguł.  

## <a name="how-do-i-revoke-a-verifiable-credential"></a>Jak mogę odwołać poświadczenia do zweryfikowania

Po ustawieniu i zweryfikowaniu poświadczeń dla użytkowników, aby sprawdzić, jak można odwołać poświadczenia zweryfikowane w bloku VC.

1. Przejdź do bloku poświadczenia zweryfikowane w Azure Active Directory.
1. Wybierz poświadczenia do zweryfikowania, w przypadku których wcześniej skonfigurowano i wydano poświadczenia do zweryfikowania dla użytkownika. =
1. W menu po lewej stronie wybierz pozycję **odwołaj poświadczenia** 
    ![ odwołaj poświadczenia](media/how-to-issuer-revoke/settings-revoke.png) 
1. Wyszukaj atrybut index użytkownika, który ma zostać odwołany. 

   ![Znajdź poświadczenie do odwołania](media/how-to-issuer-revoke/revoke-search.png)

    >[!NOTE]
    >Ponieważ przechowujemy tylko skrót indeksowanego wystąpienia z poświadczenia do zweryfikowania, tylko dokładne dopasowanie spowoduje wypełnienie wyników wyszukiwania. Przyjmujemy dane wejściowe jako przeszukane przez administratora IT i używamy tego samego algorytmu wyznaczania wartości skrótu, aby sprawdzić, czy w bazie danych istnieją dopasowania skrótów.
    
1. Po znalezieniu dopasowania zaznacz opcję **odwołaj** z prawej strony poświadczenia, które chcesz odwołać.

   ![Ostrzeżenie z informacją o tym, że po odwołaniu użytkownik nadal ma poświadczenia](media/how-to-issuer-revoke/warning.png) 

1. Po pomyślnym odwołaniu zobaczysz aktualizację stanu i zielony transparent pojawi się u góry strony. 
   ![Weryfikuj tę domenę w ustawieniach](media/how-to-issuer-revoke/revoke-successful.png) 

Teraz za każdym razem, gdy jednostka uzależniona odwołuje się do sprawdzenia stanu tego konkretnego poświadczenia zweryfikowanego, interfejs API stanu firmy Microsoft działający w imieniu dzierżawcy zwraca odpowiedź "false".

## <a name="next-steps"></a>Następne kroki

Przetestuj własne funkcje z poświadczeniami testu, aby użyć ich do przepływu. Możesz wyświetlić informacje dotyczące sposobu konfigurowania dzierżawy w celu wydawania zweryfikowanych poświadczeń, [przeglądając nasze samouczki](get-started-verifiable-credentials.md).