---
title: Tworzenie aplikacji łańcucha bloków — Azure łańcucha bloków Workbench
description: Samouczek dotyczący sposobu tworzenia aplikacji łańcucha bloków dla usługi Azure łańcucha bloków Workbench Preview.
ms.date: 08/24/2020
ms.topic: tutorial
ms.reviewer: ravastra
ms.openlocfilehash: add790a069d2e0ea66d84bbd632825cf9331fd38
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88784032"
---
# <a name="tutorial-create-a-blockchain-application-for-azure-blockchain-workbench"></a>Samouczek: Tworzenie aplikacji łańcucha bloków dla usługi Azure łańcucha bloków Workbench

Usługa Azure Blockchain Workbench umożliwia tworzenie aplikacji łańcucha bloków reprezentujących przepływy pracy dla wielu podmiotów zdefiniowane za pomocą konfiguracji i kodu kontraktu inteligentnego.

Omawiane tematy:

> [!div class="checklist"]
> * Konfigurowanie aplikacji łańcucha bloków
> * Tworzenie pliku kodu kontraktu inteligentnego
> * Dodawanie aplikacji łańcucha bloków do usługi Blockchain Workbench
> * Dodawanie członków do aplikacji łańcucha bloków

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożenie usługi Blockchain Workbench. Aby uzyskać szczegółowe informacje na temat wdrożenia, zobacz [Wdrożenie usługi Azure Blockchain Workbench](deploy.md).
* Użytkownicy usługi Azure Active Directory w dzierżawie skojarzeni z usługą Blockchain Workbench. Aby uzyskać więcej informacji, zobacz sekcję o [dodawaniu użytkowników usługi Azure AD w usłudze Azure Blockchain Workbench](manage-users.md#add-azure-ad-users).
* Konto administratora usługi Blockchain Workbench. Aby uzyskać więcej informacji, zobacz sekcję o [dodawaniu administratorów usługi Blockchain Workbench w usłudze Azure Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Witaj, łańcuchu bloków!

Utwórzmy podstawową aplikację, w której osoba żądająca wysyła żądanie, a osoba odpowiadająca wysyła odpowiedź na żądanie.
Żądaniem może na przykład być wiadomość „Witaj, jak się masz?”, a odpowiedzią — „Świetnie!”. Żądanie i odpowiedź są rejestrowane w podstawowym łańcuchu bloków.

Postępuj zgodnie z procedurą, aby utworzyć pliki aplikacji. Możesz też [pobrać przykład z witryny GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain).

## <a name="configuration-file"></a>Plik konfiguracji

Metadane konfiguracji definiują ogólne przepływy pracy i model interakcji aplikacji łańcucha bloków. Metadane konfiguracji reprezentują etapy przepływów pracy i model interakcji aplikacji łańcucha bloków. Aby uzyskać więcej informacji o zawartości plików konfiguracji, zobacz [Dokumentacja konfiguracji usługi Azure Blockchain Workflow](configuration.md).

1. Utwórz plik o nazwie `HelloBlockchain.json` w swoim ulubionym edytorze.
2. Dodaj następujący kod JSON, aby zdefiniować konfigurację aplikacji łańcucha bloków.

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. Zapisz plik `HelloBlockchain.json`.

Plik konfiguracji zawiera kilka sekcji. Poniżej znajdują się szczegółowe informacje na temat każdej sekcji:

### <a name="application-metadata"></a>Metadane aplikacji

Na początku pliku konfiguracji znajdują się informacje o aplikacji, w tym nazwa i opis aplikacji.

### <a name="application-roles"></a>Role aplikacji

W sekcji ról aplikacji są definiowane role użytkowników, którzy mogą działać lub uczestniczyć w ramach aplikacji łańcucha bloków. Definiowany jest zestaw odrębnych ról na podstawie funkcjonalności. W scenariuszu żądanie-odpowiedź występuje rozróżnienie między funkcjonalnością osoby żądającej jako jednostki tworzącej żądania oraz osoby odpowiadającej jako jednostki tworzącej odpowiedzi.

### <a name="workflows"></a>Przepływy pracy

Przepływy pracy definiują co najmniej jeden etap i co najmniej jedną akcję kontraktu. W scenariuszu żądanie-odpowiedź pierwszy etap (stan) przepływu pracy to wykonanie akcji (przejście) wysłania żądania (funkcja) przez osobę żądającą (rola). W kolejnym etapie (stan) osoba odpowiadająca (rola) wykonuje akcję (przejście) wysłania odpowiedzi (funkcja). Przepływ pracy aplikacji może obejmować właściwości, funkcje i stany wymagane do opisania przepływu kontraktu.

## <a name="smart-contract-code-file"></a>Plik kodu kontraktu inteligentnego

Kontrakty inteligentne reprezentują logikę biznesową aplikacji łańcucha bloków. Obecnie usługa Blockchain Workbench obsługuje platformę Ethereum na potrzeby rejestru łańcucha bloków. Na platformie Ethereum do pisania samodzielnie wymuszanej logiki biznesowej na potrzeby kontraktów inteligentnych używa się języka programowania [Solidity](https://solidity.readthedocs.io).

Kontrakty inteligentne w języku Solidity są podobne do klas w językach obiektowych. Każdy kontrakt zawiera stan i funkcje w celu implementacji etapów i akcji kontraktu inteligentnego.

Utwórz plik o nazwie `HelloBlockchain.sol` w swoim ulubionym edytorze.

### <a name="version-pragma"></a>Dyrektywa pragma wersji

Najlepszym rozwiązaniem jest wskazanie docelowej wersji języka Solidity. Określenie wersji pomoże uniknąć niezgodności z przyszłymi wersjami języka Solidity.

Dodaj następującą dyrektywę pragma na początku pliku kodu kontraktu inteligentnego `HelloBlockchain.sol`.

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>Relacja kodu kontraktu inteligentnego i konfiguracji

Usługa Blockchain Workbench tworzy aplikację łańcucha bloków, korzystając z pliku konfiguracji i pliku kodu kontraktu inteligentnego. Między danymi zdefiniowanymi w konfiguracji i kodem kontraktu inteligentnego istnieje relacja. Typy, parametry, funkcje i szczegóły kontraktu muszą być zgodne, aby utworzyć aplikację. Usługa Blockchain Workbench weryfikuje pliki przed utworzeniem aplikacji.

### <a name="contract"></a>Kontrakt

Dodaj nagłówek **contract** do pliku kodu kontraktu inteligentnego `HelloBlockchain.sol`.

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>Zmienne stanu

Zmienne stanu przechowują wartości stanu dla każdego wystąpienia kontraktu. Zmienne stanu w kontrakcie muszą być zgodne z właściwościami przepływu pracy zdefiniowanymi w pliku konfiguracji.

Dodaj zmienne stanu do Twojego kontraktu w pliku kodu kontraktu inteligentnego `HelloBlockchain.sol`.

``` solidity
    //Set of States
    enum StateType { Request, Respond}

    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;

    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Konstruktor

Konstruktor definiuje parametry wejściowe dla nowego wystąpienia kontraktu inteligentnego przepływu pracy. Wymagane parametry konstruktora są definiowane jako parametry konstruktora w pliku konfiguracji. Liczba, kolejność i typ parametrów muszą być takie same w obu plikach.

W funkcji konstruktora napisz logikę biznesową, która ma być wykonywana przed utworzeniem kontraktu. Na przykład zainicjuj zmienne stanu przy użyciu początkowych wartości.

Dodaj funkcję konstruktora do Twojego kontraktu w pliku kodu kontraktu inteligentnego `HelloBlockchain.sol`.

``` solidity
    // constructor function
    constructor(string memory message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    }
```

### <a name="functions"></a>Funkcje

Funkcje to wykonywalne jednostki logiki biznesowej w ramach kontraktu. Wymagane parametry funkcji są definiowane jako parametry funkcji w pliku konfiguracji. Liczba, kolejność i typ parametrów muszą być takie same w obu plikach. Funkcje są skojarzone z przejściami w przepływie pracy usługi Blockchain Workbench w pliku konfiguracji. Przejście jest akcją wykonywaną, aby przejść do kolejnego etapu przepływu pracy aplikacji, jak określono w kontrakcie.

Napisz logikę biznesową, która ma być wykonywana w ramach funkcji. Na przykład modyfikowanie wartości zmiennej stanu.

1. Dodaj następujące funkcje do Twojego kontraktu w pliku kodu kontraktu inteligentnego `HelloBlockchain.sol`.

    ``` solidity
        // call this function to send a request
        function SendRequest(string memory requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
        }
    
        // call this function to send a response
        function SendResponse(string memory responseMessage) public
        {
            Responder = msg.sender;
    
            ResponseMessage = responseMessage;
            State = StateType.Respond;
        }
    }
    ```

2. Zapisz plik kodu kontraktu inteligentnego `HelloBlockchain.sol`.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Dodawanie aplikacji łańcucha bloków do usługi Blockchain Workbench

Aby dodać aplikację łańcucha bloków do usługi Blockchain Workbench, należy przekazać plik konfiguracji i plik kontraktu inteligentnego w celu zdefiniowania aplikacji.

1. W przeglądarce internetowej przejdź na adres internetowy usługi Blockchain Workbench. Na przykład `https://{workbench URL}.azurewebsites.net/`. Aplikacja internetowa zostanie utworzona po wdrożeniu usługi Blockchain Workbench. Aby uzyskać informacje na temat znajdowania adresu internetowego usługi Blockchain Workbench, zobacz [Internetowy adres URL usługi Blockchain Workbench](deploy.md#blockchain-workbench-web-url)
2. Zaloguj się jako [administrator usługi Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Wybierz kolejno pozycje **aplikacje**  >  **nowe**. Zostanie wyświetlone okienko **Nowa aplikacja**.
4. Wybierz pozycję **Przekaż konfigurację kontraktu**  >  **Przeglądaj** , aby zlokalizować **HelloBlockchain.jsw** utworzonym pliku konfiguracji. Zostanie wykonana automatyczna walidacja pliku konfiguracji. Wybierz link **Pokaż**, aby wyświetlić błędy walidacji. Usuń błędy walidacji przed wdrożeniem aplikacji.
5. Wybierz pozycję **Przekaż kod kontraktu**  >  **Przeglądaj** , aby zlokalizować plik kodu kontraktu inteligentnego **HelloBlockchain. peruwiański** . Zostanie wykonana automatyczna walidacja pliku kodu. Wybierz link **Pokaż**, aby wyświetlić błędy walidacji. Usuń błędy walidacji przed wdrożeniem aplikacji.
6. Wybierz pozycję **Wdróż**, aby utworzyć aplikację łańcucha bloków na podstawie plików konfiguracji i kontraktu inteligentnego.

Wdrażanie aplikacji łańcucha bloków zajmuje kilka minut. Po zakończeniu wdrażania nowa aplikacja zostanie wyświetlona w sekcji **Aplikacje**. 

> [!NOTE]
> Aplikacje łańcucha bloków możesz również tworzyć przy użyciu [interfejsu API REST usługi Azure Blockchain Workbench](/rest/api/azure-blockchain-workbench).

## <a name="add-blockchain-application-members"></a>Dodawanie członków aplikacji łańcucha bloków

Dodaj do aplikacji członków, którzy będą inicjować kontrakty i wykonywać w ramach nich akcje. Aby dodać członków aplikacji, musisz być [administratorem usługi Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

1. Wybierz pozycję **aplikacje**  >  **Hello, łańcucha bloków!**.
2. Liczba członków skojarzonych z aplikacją jest wyświetlana w prawym górnym rogu strony. W przypadku nowej aplikacji liczba członków będzie równa zero.
3. Wybierz link **członkowie** w prawym górnym rogu strony. Zostanie wyświetlona bieżąca lista członków aplikacji.
4. Na liście członkostwa wybierz pozycję **Dodaj członków**.
5. Wybierz lub wprowadź nazwę członka, którego chcesz dodać. Na liście znajdują się tylko użytkownicy usługi Azure AD, którzy istnieją w dzierżawie usługi Blockchain Workbench. Jeśli użytkownik nie zostanie znaleziony, musisz [dodać użytkowników usługi Azure AD](manage-users.md#add-azure-ad-users).
6. W obszarze **Rola** wybierz rolę członka. Dla pierwszego członka wybierz rolę **Requestor** (Osoba żądająca).
7. Wybierz pozycję **Dodaj**, aby dodać członka ze skojarzoną rolą do aplikacji.
8. Dodaj do aplikacji kolejnego członka o roli **Responder** (Osoba odpowiadająca).

Aby uzyskać więcej informacji na temat zarządzania użytkownikami w usłudze Blockchain Workbench, zobacz [Zarządzanie użytkownikami w usłudze Azure Blockchain Workbench](manage-users.md)

## <a name="next-steps"></a>Następne kroki

W tym artykule z instrukcjami utworzono podstawową aplikację żądań i odpowiedzi. Aby uzyskać informacje na temat korzystania z aplikacji, przejdź do następnego artykułu z instrukcjami.

> [!div class="nextstepaction"]
> [Korzystanie z aplikacji łańcucha bloków](use.md)
