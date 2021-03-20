---
title: Podróże podrzędne w Azure Active Directory B2C | Microsoft Docs
description: Określ element podpodróży zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f037d4283b4b05081ef47e7223495f6e19d460e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97386871"
---
# <a name="sub-journeys"></a>Podróże podrzędne

Podpodróży mogą służyć do organizowania i uproszczenia przepływu kroków aranżacji w trakcie podróży użytkownika. [Podróże użytkownika](userjourneys.md) określają jawne ścieżki, za pośrednictwem których zasady umożliwiają aplikacji jednostki zależnej uzyskanie żądanych oświadczeń dla użytkownika. Użytkownik jest prowadzony przez te ścieżki w celu pobrania oświadczeń, które mają być prezentowane jednostce zależnej. Innymi słowy, Podróże użytkowników definiują logikę biznesową, przez jaką użytkownik końcowy przechodzi przez użytkownika końcowego, jako że platforma obsługi tożsamości Azure AD B2C przetwarza żądanie. Podróż użytkownika jest reprezentowana jako sekwencja aranżacji, która musi być stosowana w przypadku pomyślnej transakcji. Element [ClaimsExchange](userjourneys.md#claimsexchanges) kroku aranżacji jest powiązany z pojedynczym [profilem technicznym](technicalprofiles.md) , który jest wykonywany.

Podpodróż jest grupą czynności aranżacji, które mogą być wywoływane w dowolnym momencie w trakcie podróży użytkownika. Za pomocą podpodróży można utworzyć sekwencje kroków wielokrotnego użytku lub wdrożyć rozgałęzienie w celu lepszego reprezentowania logiki biznesowej.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>Rozgałęzianie podróży użytkownika

Podpodróży zachowują się jak [podróże użytkowników](userjourneys.md), ponieważ obie są reprezentowane jako sekwencje aranżacji, które muszą zostać wykonane w przypadku pomyślnej transakcji. Podróże użytkowników mogą być wywoływane samodzielnie i wymagają wykonania kroku SendClaims. Podpodróży są składnikami podróży użytkownika i nie mogą być wywoływane niezależnie i są zawsze wywoływane z podróży użytkownika.

Kluczowym elementem rozgałęziania jest umożliwienie lepszego przetwarzania logiki biznesowej w trakcie podróży użytkownika. Typowe kroki aranżacji są pogrupowane w pojedyncze elementy, które mają być wywołane osobno. Podróż podrzędną może uprościć podróż, w której wiele kroków aranżacji jest połączonych razem (z tymi samymi warunkami). Podróż podjazdu jest wywoływana tylko z podróży użytkownika, nie powinna wywoływać innej podróży podrzędnej.

Istnieją dwa typy podróży podrzędnych:

- **Call** -zwraca kontrolę do obiektu wywołującego. Wykonano podjazdę, a następnie formant jest zwracany do kroku aranżacji, który jest aktualnie wykonywany w trakcie podróży użytkownika.
- **Transfer** -transferuje kontrolę do podróży podrzędnej (rozgałęzianie nieodwracalne). Podpodróż musi mieć krok SendClaims, aby zwrócić oświadczenia z powrotem do aplikacji jednostki uzależnionej.

## <a name="example-scenarios"></a>Przykładowe scenariusze

### <a name="call-sub-journey"></a>Wywołanie podrzędne wywołania

Podpodróż wywołania jest przydatna w następujących scenariuszach:

- Kontroli wieku: w przypadku wieku kontroli, między przejazdami użytkowników istnieje wiele współużytkowanych składników. Rozgałęzianie umożliwia kompilowanie wspólnych elementów w udostępnianych składnikach.  
- Zgoda rodziców: rozgałęzienie umożliwia wygodę w projekcie zgody rodzicielskiej przez umożliwienie nam dostępu do oświadczeń od użytkownika podczas podróży, a także do rozgałęziania do osoby, która ma zgodę na użytkownika, po znalezieniu użytkownika wymaga zgody. 
- Zarejestruj się, aby się zalogować: Rozważmy scenariusz, w którym użytkownik już istnieje w katalogu, ale mógł zapomniano utworzyć konto. Może być pożądane w takim przypadku, który zamiast poinformowania użytkownika o tym, że wprowadzone poświadczenia już istnieją, i wymusić ponowne uruchomienie przez użytkownika przełączenia z przepływu rejestracji do przepływu logowania dla tego użytkownika.  

### <a name="transfer-sub-journey"></a>Przenoszenie podróży podrzędnej

Podróż podrzędną transferu jest przydatna w następujących scenariuszach:

- Wyświetlanie strony blokowej.
- Testowanie a/B przez kierowanie żądania do podróży podrzędnej w celu wykonywania i wystawiania tokenu.

## <a name="adding-a-subjourneys-element"></a>Dodawanie elementu subpodróży

Poniżej znajduje się przykład `SubJourney` elementu typu `Call` , który zwraca kontrolę z powrotem do podróży użytkownika.

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

Poniżej znajduje się przykład `SubJourney` elementu typu `Transfer` , który zwraca token z powrotem do aplikacji jednostki uzależnionej.

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-sub-journey-step"></a>Wywołaj krok podróży podrzędnej

Nowy krok aranżacji typu `InvokeSubJourney` jest używany do wykonywania podróży podrzędnej. Poniżej przedstawiono przykład pokazujący wszystkie elementy wykonywania tego kroku aranżacji.

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Podróż podrzędna nie powinna wywoływać innej podróży podrzędnej.

## <a name="components"></a>Składniki

Aby zdefiniować podpodróży obsługiwane przez zasady, Dodaj element **subpodróży** w obszarze najwyższego poziomu w pliku zasad.

Element **transpodróże** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Podpodróż | 1: n | Podpodróż, która definiuje wszystkie konstrukcje niezbędne do całkowitego przepływu użytkownika. |

Element **transpodróże** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Identyfikator przejazdu podrzędnego, który może być używany przez użytkownika do odwołującego się do podróży podrzędnej w zasadach. Element **SubJourneyReferenceId** elementu [kandydującego](userjourneys.md#journeylist) wskazuje na ten atrybut. |
| Typ | Tak | Możliwe wartości: `Call` , lub `Transfer` . Aby uzyskać więcej informacji, zobacz [rozgałęzianie podróży użytkownika](#user-journey-branching)|

Element **subpodróży** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1: n | Sekwencja aranżacji, która musi zostać wykonana w przypadku pomyślnej transakcji. Każda podróż użytkownika obejmuje uporządkowaną listę kroków aranżacji, które są wykonywane w kolejności. Jeśli którykolwiek z kroków zakończy się niepowodzeniem, transakcja nie powiedzie się. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Aby uzyskać pełną listę elementów kroku aranżacji, zobacz [UserJourneys](userjourneys.md).

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat [UserJourneys](userjourneys.md)