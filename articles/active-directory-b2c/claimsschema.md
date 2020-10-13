---
title: ClaimsSchema — Azure Active Directory B2C | Microsoft Docs
description: Określ element ClaimsSchema zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d02bc8d97b65f4ea2c2585201654899a63d3229b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201365"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **ClaimsSchema** definiuje typy roszczeń, do których można odwoływać się w ramach zasad. Schemat oświadczeń to miejsce, w którym deklarujesz oświadczenia. Do żądania może być imię, nazwisko, nazwa wyświetlana, numer telefonu i inne. Element ClaimsSchema zawiera listę elementów **ClaimType** . Element **ClaimType** zawiera atrybut **ID** , który jest nazwą oświadczenia.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>Claim

Element **ClaimType** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Identyfikator, który jest używany dla typu zgłoszenia. Inne elementy mogą używać tego identyfikatora w zasadach. |

Element **ClaimType** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Nazwa wyświetlana | 1:1 | Tytuł wyświetlany użytkownikom na różnych ekranach. Wartość może być [zlokalizowana](localization.md). |
| typ_danych | 1:1 | Typ żądania. |
| DefaultPartnerClaimTypes | 0:1 | Domyślne typy roszczeń partnera, które mają być używane przez określony protokół. Wartość można zastąpić w **PartnerClaimType** określonym w elementach **oświadczenie inputclaim** lub **oświadczenie outputclaim** . Użyj tego elementu, aby określić nazwę domyślną dla protokołu.  |
| Maska | 0:1 | Opcjonalny ciąg znaków maskowania, który można zastosować podczas wyświetlania żądania. Na przykład numer telefonu 324-232-4343 może być maskowany jako XXX-XXX-4343. |
| UserHelpText | 0:1 | Opis typu zgłoszenia, który może być przydatny dla użytkowników, aby zrozumieć jego przeznaczenie. Wartość może być [zlokalizowana](localization.md). |
| UserInputType | 0:1 | Typ kontrolki wejściowej, która powinna być dostępna dla użytkownika po ręcznym wprowadzeniu danych roszczeń dla typu zgłoszenia. Zobacz typy danych wejściowych użytkownika zdefiniowane w dalszej części tej strony. |
| AdminHelpText | 0:1 | Opis typu zgłoszenia, który może być przydatny dla administratorów, aby zrozumieć jego przeznaczenie. |
| Ograniczenie | 0:1 | Ograniczenia wartości dla tego żądania, takie jak wyrażenie regularne (regularne) lub lista akceptowalnych wartości. Wartość może być [zlokalizowana](localization.md). |
PredicateValidationReference| 0:1 | Odwołanie do elementu **PredicateValidationsInput** . Elementy **PredicateValidationReference** umożliwiają wykonywanie procesu walidacji w celu zapewnienia, że wprowadzane są tylko prawidłowo uformowane dane. Aby uzyskać więcej informacji, zobacz [predykaty](predicates.md). |



### <a name="datatype"></a>typ_danych

Element **DataType** obsługuje następujące wartości:

| Typ | Opis |
| ------- | ----------- |
|boolean|Reprezentuje wartość logiczną ( `true` lub `false` ).|
|date| Reprezentuje chwilę w czasie, zazwyczaj wyrażoną jako dzień. Wartość daty jest zgodna z Konwencją ISO 8601.|
|Data i godzina|Reprezentuje chwilę w czasie, zwykle wyrażoną jako datę i godzinę dnia. Wartość daty jest zgodna z Konwencją ISO 8601.|
|czas trwania|Reprezentuje przedział czasu w latach, miesiącach, dniach, godzinach, minutach i sekundach. Format is `PnYnMnDTnHnMnS` , gdzie `P` wskazuje wartość dodatnią lub `N` ujemną. `nY` to liczba lat, po której następuje literał `Y` . `nMo` to liczba miesięcy, po której następuje literał `Mo` . `nD` to liczba dni, po której następuje literał `D` . Przykłady: `P21Y` reprezentuje 21 lat. `P1Y2Mo` reprezentuje rok i dwa miesiące. `P1Y2Mo5D` reprezentuje jeden rok, dwa miesiące i pięć dni.  `P1Y2M5DT8H5M620S` reprezentuje jeden rok, dwa miesiące, pięć dni, osiem godzin, pięć minut i dwadzieścia sekund.  |
|phoneNumber|Reprezentuje numer telefonu. |
|int| Reprezentuje liczbę z przedziału od-2 147 483 648 do 2 147 483 647|
|długi| Reprezentuje liczbę z przedziału od-zakresu od do 9 223 372 036 854 775 807 |
|ciąg| Reprezentuje tekst jako sekwencję jednostek kodu UTF-16.|
|stringCollection|Reprezentuje kolekcję `string` .|
|Tożsamość użytkownika| Reprezentuje tożsamość użytkownika.|
|userIdentityCollection|Reprezentuje kolekcję `userIdentity` .|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

**DefaultPartnerClaimTypes** może zawierać następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Protokół | 1: n | Lista protokołów z domyślną nazwą typu zgłoszenia partnera. |

Element **Protocol** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Nazwa | Tak | Nazwa prawidłowego protokołu obsługiwanego przez Azure AD B2C. Możliwe wartości to: OAuth1, OAuth2, SAML2, OpenIdConnect. |
| PartnerClaimType | Tak | Nazwa typu zgłoszenia do użycia. |

W poniższym przykładzie, gdy platforma obsługi tożsamości współdziała z dostawcą tożsamości SAML2 lub aplikacją jednostki uzależnionej, to wniosek o **nazwisko** jest mapowany na `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` , z OpenIdConnect i OAuth2, to jest mapowany na `family_name` .

```xml
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

W związku z tym token JWT wystawiony przez Azure AD B2C emituje `family_name` zamiast nazwy elementu claimname **surname**.

```json
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Maska

Element **Mask** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| `Type` | Tak | Typ maski żądania. Możliwe wartości: `Simple` lub `Regex` . `Simple`Wartość wskazuje, że prosta maska tekstu jest stosowana do wiodącej części żądania ciągu. `Regex`Wartość wskazuje, że wyrażenie regularne jest stosowane do żądania ciągu jako całości.  Jeśli `Regex` wartość jest określona, opcjonalny atrybut musi również być zdefiniowany za pomocą wyrażenia regularnego do użycia. |
| `Regex` | Nie | Jeśli **`Type`** jest ustawiona na `Regex` , Określ wyrażenie regularne, które ma być używane.

Poniższy przykład konfiguruje **wierzytelność** z `Simple` maską:

```xml
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Struktura środowiska tożsamości renderuje numer telefonu podczas ukrywania pierwszych sześciu cyfr:

![Numer telefonu jest wyświetlany w przeglądarce z pierwszych sześcioma cyframi maskowanymi przy użyciu metody XS](./media/claimsschema/mask.png)

Poniższy przykład konfiguruje **AlternateEmail** z `Regex` maską:

```xml
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Struktura środowiska tożsamości renderuje tylko pierwszą literę adresu e-mail i nazwę domeny poczty e-mail:

![W przeglądarce pokazywane są znaki maskowane przez gwiazdki](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Ograniczenie

Element **ograniczenia** może zawierać następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| MergeBehavior | Nie | Metoda służąca do scalania wartości wyliczenia z obiektem ClaimType w zasadach nadrzędnych z tym samym identyfikatorem. Użyj tego atrybutu podczas zastępowania żądania określonego w zasadach podstawowych. Możliwe wartości: `Append` , `Prepend` , lub `ReplaceAll` . `Append`Wartość jest kolekcją danych, które powinny być dołączane na końcu kolekcji określonej w zasadach nadrzędnych. `Prepend`Wartość jest kolekcją danych, które powinny zostać dodane przed kolekcją określoną w zasadach nadrzędnych. `ReplaceAll`Wartość jest kolekcją danych określonych w zasadach nadrzędnych, które powinny być ignorowane. |

Element **ograniczenia** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Wyliczenie | 1: n | Dostępne opcje w interfejsie użytkownika dla użytkownika, które mają zostać wybrane dla roszczeń, takie jak wartość z listy rozwijanej. |
| Wzorce | 1:1 | Wyrażenie regularne, które ma być używane. |

#### <a name="enumeration"></a>Wyliczenie

Element **Enumeration** definiuje dostępne opcje dla użytkownika, które można wybrać dla roszczeń w interfejsie użytkownika, takie jak wartość w `CheckboxMultiSelect` , `DropdownSingleSelect` , lub `RadioSingleSelect` . Alternatywnie można zdefiniować i zlokalizować dostępne opcje z elementem [LocalizedCollections](localization.md#localizedcollections) . Aby wyszukać element z kolekcji **wyliczania** oświadczeń, należy użyć przekształcenia oświadczeń [GetMappedValueFromLocalizedCollection](string-transformations.md#getmappedvaluefromlocalizedcollection) .

Element **Enumeration** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Tekst | Tak | Ciąg wyświetlany, który jest wyświetlany użytkownikowi w interfejsie użytkownika dla tej opcji. |
|Wartość | Tak | Wartość żądania skojarzona z wybraniem tej opcji. |
| SelectByDefault | Nie | Wskazuje, czy ta opcja powinna być wybrana domyślnie w interfejsie użytkownika. Możliwe wartości: true lub false. |

W poniższym przykładzie jest konfigurowane pole listy rozwijanej **miasto** z wartością domyślną ustawioną na `New York` :

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

Lista miast listy rozwijanej z wartością domyślną ustawioną na Nowy Jork:

![Kontrolka listy rozwijanej renderowana w przeglądarce i pokazująca wartość domyślną](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Wzorce

Element **Pattern** może zawierać następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| RegularExpression | Tak | Wyrażenie regularne, które musi być zgodne z typem oświadczeń, aby było prawidłowe. |
| HelpText | Nie | Komunikat o błędzie dla użytkowników, jeśli sprawdzenie wyrażenia regularnego nie powiedzie się. |

Poniższy przykład służy do konfigurowania zgłoszenia **wiadomości e-mail** przy użyciu walidacji danych wejściowych i tekstu pomocy:

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
    </Restriction>
 </ClaimType>
```

Platforma obsługi tożsamości renderuje to konto e-mail przy użyciu walidacji danych wejściowych w formacie wiadomości e-mail:

![Pole tekstowe zawierające komunikat o błędzie wyzwalane przez ograniczenie wyrażenia regularnego](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

Azure AD B2C obsługuje różne typy danych wejściowych użytkownika, takie jak pole tekstowe, hasło i lista rozwijana, które mogą być używane podczas ręcznego wprowadzania danych dotyczących roszczeń dla danego typu. Należy określić **UserInputType** , gdy zbierasz informacje od użytkownika przy użyciu [własnego profilu technicznego](self-asserted-technical-profile.md) i [kontrolek wyświetlania](display-controls.md).

Element **UserInputType** dostępne typy danych wejściowych użytkownika:

| UserInputType | Obsługiwane oświadczenia | Opis |
| --------- | -------- | ----------- |
|CheckboxMultiSelect| `string` |Pole listy rozwijanej wiele zaznaczeń. Wartość żądania jest reprezentowana jako ciąg ogranicznika przecinki dla wybranych wartości. |
|DateTimeDropdown | `date`, `dateTime` |Lista rozwijana, aby wybrać dzień, miesiąc i rok. |
|DropdownSingleSelect |`string` |Pole listy rozwijanej pojedynczy wybór. Wartość żądania jest wybraną wartością.|
|EmailBox | `string` |Pole wejściowe wiadomości e-mail. |
|Akapitu | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|Pole, w którym jest wyświetlany tekst tylko w znaczniku akapitu. |
|Hasło | `string` |Pole tekstowe hasła.|
|RadioSingleSelect |`string` | Kolekcja przycisków radiowych. Wartość żądania jest wybraną wartością.|
|Trybie | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| Pole tekstowe tylko do odczytu. |
|TextBox |`boolean`, `int`, `string` |Jednowierszowe pole tekstowe. |


#### <a name="textbox"></a>TextBox

Typ danych **wejściowych użytkownika jest** używany do udostępniania jednowierszowego pola tekstowego.

![Pole tekstowe z właściwościami określonymi w typie zgłoszenia](./media/claimsschema/textbox.png)

```xml
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>EmailBox

Typ danych wejściowych użytkownika **EmailBox** służy do dostarczania podstawowego pola wejściowego wiadomości e-mail.

![EmailBox pokazujący właściwości określone w typie zgłoszenia](./media/claimsschema/emailbox.png)

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

#### <a name="password"></a>Hasło

Typ danych wejściowych użytkownika **hasła** służy do rejestrowania hasła wprowadzonego przez użytkownika.

![Korzystanie z typu "Claim" z hasłem](./media/claimsschema/password.png)

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DateTimeDropdown

Typ danych wejściowych użytkownika **DateTimeDropdown** służy do udostępniania zestawu list rozwijanych w celu wybrania dnia, miesiąca i roku. Można użyć predykatów i elementów PredicateValidations, aby kontrolować wartości minimalne i maksymalne daty. Aby uzyskać więcej informacji, zobacz sekcję **Konfigurowanie zakresu dat** [predykatów i PredicateValidations](predicates.md).

![Używanie typu usługi z datetimedropdown](./media/claimsschema/datetimedropdown.png)

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

Typ danych wejściowych użytkownika **RadioSingleSelect** służy do udostępniania kolekcji przycisków radiowych, które umożliwiają użytkownikowi wybranie jednej opcji.

![Używanie typu usługi z radiodsingleselect](./media/claimsschema/radiosingleselect.png)

```xml
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

Typ danych wejściowych użytkownika **DropdownSingleSelect** służy do udostępniania pola rozwijanego, które pozwala użytkownikowi wybrać jedną opcję.

![Używanie typu usługi z dropdownsingleselect](./media/claimsschema/dropdownsingleselect.png)

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

Typ danych wejściowych użytkownika **CheckboxMultiSelect** służy do udostępniania kolekcji pól wyboru, które umożliwiają użytkownikowi wybranie wielu opcji.

![Używanie typu usługi z checkboxmultiselect](./media/claimsschema/checkboxmultiselect.png)

```xml
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="readonly"></a>Trybie

Typ danych wejściowych użytkownika **tylko do odczytu** służy do udostępniania pola tylko do odczytu w celu wyświetlenia żądania i wartości.

![Korzystanie z typu "Claim" z tylko do odczytu](./media/claimsschema/readonly.png)

```xml
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Akapitu

Typ danych wejściowych użytkownika **akapitu** służy do podania pola, które wyświetla tekst tylko w znaczniku akapitu.  Na przykład &lt; &gt; tekst &lt; /p &gt; . Typ danych wejściowych użytkownika **akapitu** `OutputClaim` z własnym profilem technicznym, należy ustawić `Required` atrybut `false` (domyślnie).

![Korzystanie z typu "Claim" z akapitem](./media/claimsschema/paragraph.png)

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
