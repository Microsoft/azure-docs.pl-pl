---
title: Informacje o metadanych konfiguracji usługi Azure łańcucha bloków Workbench
description: Omówienie metadanych konfiguracji aplikacji Azure łańcucha bloków Workbench Preview.
ms.date: 12/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: f0ba19bf1d7fdf05014ac199fae9392b5c3249d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87073085"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Dokumentacja konfiguracji usługi Azure łańcucha bloków Workbench

Aplikacje Workbench platformy Azure łańcucha bloków są przepływami pracy wieloskładnikowymi zdefiniowanymi przez metadane konfiguracji i kod kontraktu inteligentnego. Metadane konfiguracji definiują ogólne przepływy pracy i model interakcji aplikacji łańcucha bloków. Inteligentne kontrakty definiują logikę biznesową aplikacji łańcucha bloków. Workbench używa konfiguracji i inteligentnego kodu kontraktu do generowania środowiska użytkownika aplikacji łańcucha bloków.

Metadane konfiguracji określają następujące informacje dla każdej aplikacji łańcucha bloków:

* Nazwa i opis aplikacji łańcucha bloków
* Unikatowe role dla użytkowników, którzy mogą działać lub uczestniczyć w aplikacji łańcucha bloków
* Co najmniej jeden przepływ pracy. Każdy przepływ pracy działa jako maszyna stanu do sterowania przepływem logiki biznesowej. Przepływy pracy mogą być niezależne lub współpracujące ze sobą.

Każdy zdefiniowany przepływ pracy określa następujące elementy:

* Nazwa i opis przepływu pracy
* Stany przepływu pracy.  Każdy stan jest etapem w przepływie sterowania logiki biznesowej. 
* Akcje przejścia do następnego stanu
* Role użytkowników, które mogą inicjować poszczególne akcje
* Inteligentne kontrakty reprezentujące logikę biznesową w plikach kodu

## <a name="application"></a>Aplikacja

Aplikacja łańcucha bloków zawiera metadane konfiguracji, przepływy pracy i role użytkowników, którzy mogą działać lub wchodzić w skład aplikacji.

| Pole | Opis | Wymagane |
|-------|-------------|:--------:|
| ApplicationName | Unikatowa nazwa aplikacji. Odpowiedni kontrakt inteligentny musi używać tej samej **ApplicationName** dla odpowiedniej klasy kontraktu.  | Tak |
| Nazwa wyświetlana | Przyjazna nazwa wyświetlana aplikacji. | Tak |
| Opis | Opis aplikacji. | Nie |
| ApplicationRoles | Kolekcja [ApplicationRoles](#application-roles). Role użytkowników, którzy mogą działać lub uczestniczyć w aplikacji.  | Tak |
| Przepływy pracy | Kolekcja  [przepływów pracy](#workflows). Każdy przepływ pracy działa jako maszyna stanu do sterowania przepływem logiki biznesowej. | Tak |

Aby zapoznać się z przykładem, zobacz [przykład pliku konfiguracji](#configuration-file-example).

## <a name="workflows"></a>Przepływy pracy

Logika biznesowa aplikacji może być modelowana jako maszyna stanu, w której podejmowana jest akcja powodująca przechodzenie przepływu logiki biznesowej z jednego stanu do drugiego. Przepływ pracy to zbiór takich stanów i akcji. Każdy przepływ pracy składa się z co najmniej jednej inteligentnej umowy, która reprezentuje logikę biznesową w plikach kodu. Kontrakt wykonywalny jest wystąpieniem przepływu pracy.

| Pole | Opis | Wymagane | Długość maksymalna |
|-------|-------------|:--------:|-----------:|
| Nazwa | Unikatowa nazwa przepływu pracy. Odpowiedni kontrakt inteligentny musi używać tej samej **nazwy** dla odpowiedniej klasy kontraktu. | Tak | 50 |
| Nazwa wyświetlana | Przyjazna nazwa wyświetlana przepływu pracy. | Tak | 255 |
| Opis | Opis przepływu pracy. | Nie | 255 |
| Inicjatorów | Kolekcja [ApplicationRoles](#application-roles). Role przypisane do użytkowników, którzy mają uprawnienia do tworzenia kontraktów w przepływie pracy. | Tak | |
| StartState | Nazwa początkowego stanu przepływu pracy. | Tak | |
| Właściwości | Kolekcja [identyfikatorów](#identifiers). Reprezentuje dane, które mogą być odczytywane w łańcuchu lub wizualizowane w narzędziu środowisko użytkownika. | Tak | |
| Konstruktor | Definiuje parametry wejściowe do tworzenia wystąpienia przepływu pracy. | Tak | |
| Funkcje | Kolekcja [funkcji](#functions) , które mogą być wykonywane w przepływie pracy. | Tak | |
| Stany | Kolekcja [Stanów](#states)przepływu pracy. | Tak | |

Aby zapoznać się z przykładem, zobacz [przykład pliku konfiguracji](#configuration-file-example).

## <a name="type"></a>Typ

Obsługiwane typy danych.

| Typ | Opis |
|-------|-------------|
| adres  | Typ adresu łańcucha bloków, taki jak *kontrakty* lub *Użytkownicy*. |
| array    | Tablica jednopoziomowa typu Integer, bool, Money lub Time. Tablice mogą być statyczne lub dynamiczne. Użyj **elementu ElementType** , aby określić typ danych elementów w tablicy. Zobacz [Przykładowa konfiguracja](#example-configuration-of-type-array). |
| bool     | Boolean — Typ danych. |
| przedsiębiorc | Adres typu kontraktu. |
| enum     | Wyliczany zestaw nazwanych wartości. W przypadku korzystania z typu enum należy również określić listę EnumValues. Każda wartość jest ograniczona do 255 znaków. Prawidłowe znaki wartości to wielkie i małe litery (A-Z, a-z) i cyfry (0-9). Zobacz [przykład konfiguracji i użycia w postaci stałej](#example-configuration-of-type-enum). |
| int      | Integer — typ danych. |
| pieniędzy    | Typ danych walutowych. |
| stan    | Stan przepływu pracy. |
| ciąg  | Są to dane typu ciąg. maksymalnie 4000 znaków. Zobacz [Przykładowa konfiguracja](#example-configuration-of-type-string). |
| użytkownik     | Adres typu użytkownika. |
| time     | Typ danych czasu. |
|`[ Application Role Name ]`| Dowolna nazwa określona w roli aplikacji. Ogranicza użytkowników do tego typu roli. |

### <a name="example-configuration-of-type-array"></a>Przykładowa konfiguracja typu Array

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>Używanie właściwości typu Array

Jeśli zdefiniujesz właściwość jako tablicę typu w konfiguracji, musisz dołączyć jawną funkcję Get, aby zwrócić publiczną właściwość typu tablicy w postaci stałej. Na przykład:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Przykładowa konfiguracja typu String

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>Przykładowa konfiguracja typu enum

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>Używanie typu wyliczenia w postaci stałej

Gdy Wyliczenie jest zdefiniowane w konfiguracji, można użyć typów wyliczeniowych w postaci stałej. Na przykład można zdefiniować Wyliczenie o nazwie PropertyTypeEnum.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

Lista ciągów musi być zgodna między konfiguracją a inteligentnym kontraktem, aby były prawidłowe i spójne deklaracje w łańcucha bloków Workbench.

Przykład przypisania:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Przykład parametru funkcji: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>Konstruktor

Definiuje parametry wejściowe dla wystąpienia przepływu pracy.

| Pole | Opis | Wymagane |
|-------|-------------|:--------:|
| Parametry | Kolekcja [identyfikatorów](#identifiers) wymaganych do zainicjowania kontraktu inteligentnego. | Tak |

### <a name="constructor-example"></a>Przykład konstruktora

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}
```

## <a name="functions"></a>Funkcje

Definiuje funkcje, które mogą być wykonywane w przepływie pracy.

| Pole | Opis | Wymagane | Długość maksymalna |
|-------|-------------|:--------:|-----------:|
| Nazwa | Unikatowa nazwa funkcji. Odpowiedni kontrakt inteligentny musi używać tej samej **nazwy** dla odpowiedniej funkcji. | Tak | 50 |
| Nazwa wyświetlana | Przyjazna nazwa wyświetlana funkcji. | Tak | 255 |
| Opis | Opis funkcji | Nie | 255 |
| Parametry | Kolekcja [identyfikatorów](#identifiers) odpowiadająca parametrom funkcji. | Tak | |

### <a name="functions-example"></a>Przykład funkcji

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>Stany

Kolekcja unikatowych stanów w ramach przepływu pracy. Każdy stan przechwytuje krok w przepływie sterowania logiki biznesowej. 

| Pole | Opis | Wymagane | Długość maksymalna |
|-------|-------------|:--------:|-----------:|
| Nazwa | Unikatowa nazwa stanu. Odpowiedni kontrakt inteligentny musi używać tej samej **nazwy** dla danego stanu. | Tak | 50 |
| Nazwa wyświetlana | Przyjazna nazwa wyświetlana stanu. | Tak | 255 |
| Opis | Opis stanu. | Nie | 255 |
| PercentComplete | Wartość całkowita wyświetlana w interfejsie użytkownika łańcucha bloków Workbench do wyświetlania postępu w przepływie sterowania logiki biznesowej. | Tak | |
| Styl | Wskazówka wizualna wskazująca, czy stan reprezentuje stan powodzenie lub niepowodzenie. Istnieją dwie prawidłowe wartości: `Success` lub `Failure` . | Tak | |
| Przejścia | Kolekcja dostępnych [przejść](#transitions) od bieżącego stanu do następnego zestawu Stanów. | Nie | |

### <a name="states-example"></a>Stany — przykład

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Przejścia

Dostępne akcje do następnego stanu. Co najmniej jedna rola użytkownika może wykonać akcję w każdym stanie, w którym akcja może spowodować przejście stanu do innego stanu w przepływie pracy. 

| Pole | Opis | Wymagane |
|-------|-------------|:--------:|
| AllowedRoles | Lista ról aplikacji, które mogą inicjować przejście. Wszyscy użytkownicy określonej roli mogą być w stanie wykonać tę akcję. | Nie |
| AllowedInstanceRoles | Lista ról użytkownika uczestniczących lub określonych w kontrakcie inteligentnym, które mogą inicjować przejście. Role wystąpienia są zdefiniowane we **właściwościach** w przepływach pracy. AllowedInstanceRoles reprezentuje użytkownika biorącego udział w wystąpieniu kontraktu inteligentnego. AllowedInstanceRoles dają możliwość ograniczenia podejmowania akcji do roli użytkownika w wystąpieniu kontraktu.  Na przykład możesz chcieć zezwolić tylko na zakończenie przez użytkownika, który utworzył kontrakt (obiektem InstanceOwner), a nie wszystkich użytkowników w typie roli (właściciela), jeśli określono rolę w AllowedRoles. | Nie |
| Nazwa wyświetlana | Przyjazna nazwa wyświetlana przejścia. | Tak |
| Opis | Opis przejścia. | Nie |
| Funkcja | Nazwa funkcji, w której ma zostać zainicjowane przejście. | Tak |
| NextStates | Kolekcja potencjalnych następnych Stanów po pomyślnym przejściu. | Tak |

### <a name="transitions-example"></a>Przykład przejścia

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Role aplikacji

Role aplikacji definiują zestaw ról, które mogą być przypisane do użytkowników, którzy chcą działać lub uczestniczyć w aplikacji. Role aplikacji mogą służyć do ograniczania akcji i udziału w aplikacji łańcucha bloków oraz odpowiednich przepływów pracy. 

| Pole | Opis | Wymagane | Długość maksymalna |
|-------|-------------|:--------:|-----------:|
| Nazwa | Unikatowa nazwa roli aplikacji. Odpowiedni kontrakt inteligentny musi używać tej samej **nazwy** dla odpowiedniej roli. Nazwy typów podstawowych są zastrzeżone. Nie można nazwać roli aplikacji o takiej samej nazwie jak [Typ](#type)| Tak | 50 |
| Opis | Opis roli aplikacji. | Nie | 255 |

### <a name="application-roles-example"></a>Przykład ról aplikacji

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Identyfikatory

Identyfikatory reprezentują kolekcję informacji używanych do opisywania właściwości przepływu pracy, konstruktora i parametrów funkcji. 

| Pole | Opis | Wymagane | Długość maksymalna |
|-------|-------------|:--------:|-----------:|
| Nazwa | Unikatowa nazwa właściwości lub parametru. Odpowiedni kontrakt inteligentny musi używać tej samej **nazwy** dla odpowiedniej właściwości lub parametru. | Tak | 50 |
| Nazwa wyświetlana | Przyjazna nazwa wyświetlana właściwości lub parametru. | Tak | 255 |
| Opis | Opis właściwości lub parametru. | Nie | 255 |
| Typ | [Typ danych](#type)właściwości. | Tak |

### <a name="identifiers-example"></a>Przykład identyfikatorów

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Przykład pliku konfiguracji

Transfer zasobów to inteligentny scenariusz kontraktu służący do kupowania i sprzedawania zasobów o wysokiej wartości, które wymagają inspektora i rzeczoznawca. Sprzedawcy mogą wyświetlić listę swoich zasobów przez utworzenie wystąpienia kontraktu inteligentnego transferu zasobów. Kupujący mogą robić oferty przez podejmowanie działań w ramach kontraktu inteligentnego, a inne strony mogą podejmować działania w celu sprawdzenia lub oceny elementu zawartości. Gdy element zawartości zostanie oznaczony jako kontrolowany i wyciągnięty, kupujący i sprzedający potwierdzi sprzedaż ponownie, zanim kontrakt zostanie ustawiony jako zakończony. W każdym momencie procesu wszyscy uczestnicy mają wgląd w stan kontraktu w miarę jego aktualizowania. 

Aby uzyskać więcej informacji, w tym pliki kodu, zobacz [przykład transferu zasobów dla usługi Azure łańcucha bloków Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

Następujący plik konfiguracji dotyczy przykładowego transferu zasobów:

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Description",
          "DisplayName": "Description",
          "Description": "Describes the asset being sold",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "description",
            "Description": "The description of this asset",
            "DisplayName": "Description",
            "Type": {
              "Name": "string"
            }
          },
          {
            "Name": "price",
            "Description": "The price of this asset",
            "DisplayName": "Price",
            "Type": {
              "Name": "money"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "Modify",
          "DisplayName": "Modify",
          "Description": "Modify the description/price attributes of this asset transfer instance",
          "Parameters": [
            {
              "Name": "description",
              "Description": "The new description of the asset",
              "DisplayName": "Description",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "price",
              "Description": "The new price of the asset",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Terminate",
          "DisplayName": "Terminate",
          "Description": "Used to cancel this particular instance of asset transfer",
          "Parameters": []
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
      "States": [
        {
          "Name": "Active",
          "DisplayName": "Active",
          "Description": "The initial state of the asset transfer workflow",
          "PercentComplete": 20,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancels this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate Offer"
            },
            {
              "AllowedRoles": [ "Buyer" ],
              "AllowedInstanceRoles": [],
              "Description": "Make an offer for this asset",
              "Function": "MakeOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Make Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Modify attributes of this asset transfer instance",
              "Function": "Modify",
              "NextStates": [ "Active" ],
              "DisplayName": "Modify"
            }
          ]
        },
        {
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "Accepted",
          "DisplayName": "Accepted",
          "Description": "Asset transfer process is complete",
          "PercentComplete": 100,
          "Style": "Success",
          "Transitions": []
        },
        {
          "Name": "Terminated",
          "DisplayName": "Terminated",
          "Description": "Asset transfer has been canceled",
          "PercentComplete": 100,
          "Style": "Failure",
          "Transitions": []
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST usługi Azure Blockchain Workbench](/rest/api/azure-blockchain-workbench)
