---
title: Rozpocznij pracę z zestawem SDK Azure CDN dla Node.js | Microsoft Docs
description: Dowiedz się, jak utworzyć prostą aplikację konsolową Node.js, która pokazuje, jak zautomatyzować tworzenie Azure CDN profilów i punktów końcowych i zarządzanie nimi.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/02/2021
ms.author: mazha
ms.custom: devx-track-js
ms.openlocfilehash: 386a424e45d1b718b68cbbf53322fd704317a06b
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285228"
---
# <a name="get-started-with-azure-cdn-development"></a>Rozpoczynanie pracy z wdrażaniem usługi Azure CDN
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Przy użyciu [zestawu SDK Azure CDN dla języka JavaScript](https://www.npmjs.com/package/@azure/arm-cdn) można zautomatyzować tworzenie profilów i punktów końcowych usługi CDN oraz zarządzanie nimi.  Ten samouczek przeprowadzi Cię przez proces tworzenia prostej aplikacji konsolowej Node.js, która pokazuje kilka dostępnych operacji.  Ten samouczek nie jest przeznaczony do opisania wszystkich aspektów zestawu SDK Azure CDN dla języka JavaScript.

Aby ukończyć ten samouczek, należy mieć zainstalowany i skonfigurowany [Node.js](https://www.nodejs.org) **6. x. x** lub nowszy.  Możesz użyć dowolnego edytora tekstu, aby utworzyć aplikację Node.js.  Aby napisać ten samouczek, używam [Visual Studio Code](https://code.visualstudio.com).  


[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Tworzenie projektu i Dodawanie zależności NPM
Po utworzeniu grupy zasobów dla naszych profilów usługi CDN i udostępnieniu naszej aplikacji usługi Azure AD uprawnienia do zarządzania profilami i punktami końcowymi sieci CDN w tej grupie możemy rozpocząć tworzenie aplikacji.

Utwórz folder do przechowywania aplikacji.  Z poziomu konsoli programu z narzędziami Node.js w bieżącej ścieżce Ustaw bieżącą lokalizację na ten nowy folder i zainicjuj projekt, wykonując następujące polecenie:

```console
npm init
```

Zostanie wyświetlona seria pytań umożliwiających zainicjowanie projektu.  W przypadku **punktu wejścia** ten samouczek używa *app.js*.  W poniższym przykładzie można zobaczyć inne opcje.

![NPM dane wyjściowe init](./media/cdn-app-dev-node/cdn-npm-init.png)

Nasz projekt jest teraz zainicjowany przy użyciu *packages.js* pliku.  Nasz projekt będzie używać niektórych bibliotek platformy Azure zawartych w pakietach NPM.  Użyjemy biblioteki do uwierzytelniania Azure Active Directory w Node.js ( @azure/ms-rest-nodeauth ) i biblioteki klienta Azure CDN dla języka JavaScript ( @azure/arm-cdn ).  Dodajmy je do projektu jako zależności.

```console
npm install --save @azure/ms-rest-nodeauth
npm install --save @azure/arm-cdn
```

Po zakończeniu instalacji pakietów *package.jsw* pliku powinien wyglądać podobnie do tego przykładu (numery wersji mogą się różnić):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "@azure/arm-cdn": "^5.2.0",
    "@azure/ms-rest-nodeauth": "^3.0.0"
  }
}
```

Na koniec za pomocą edytora tekstów Utwórz pusty plik tekstowy i Zapisz go w folderze głównym naszego folderu projektu jako *app.js*.  Teraz wszystko jest gotowe do rozpoczęcia pisania kodu.

## <a name="requires-constants-authentication-and-structure"></a>Wymaga, stałe, uwierzytelniania i struktury
Dzięki *app.js* otwartym w naszym edytorze Przyjrzyjmy się podstawowej strukturze naszego programu.

1. Dodaj "wymagane" dla naszych pakietów NPM w górnej części z następującymi:
   
    ``` javascript
    var msRestAzure = require('@azure/ms-rest-nodeauth');
    const { CdnManagementClient } = require('@azure/arm-cdn');
    ```
2. Musimy zdefiniować niektóre stałe nasze metody będą używane.  Dodaj następujące elementy.  Pamiętaj, aby zastąpić symbole zastępcze, w tym **&lt; nawiasy &gt; kątowe**, z własnymi wartościami, zgodnie z potrzebami.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Następnie utworzymy wystąpienie klienta zarządzania siecią CDN i przekażemy mu poświadczenia.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new CdnManagementClient(credentials, subscriptionId);
    ```

4. Nasza aplikacja konsolowa Node.js będzie podejmować kilka parametrów wiersza polecenia.  Sprawdźmy, czy przekazano co najmniej jeden parametr.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. Przekazuje nam do głównej części naszego programu, w którym rozgałęzimy na inne funkcje w oparciu o parametry, które zostały przekazane.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. W kilku miejscach w naszym programie musimy upewnić się, że została przeniesiona właściwa liczba parametrów i jeśli nie będzie wyglądać prawidłowo, może zostać wyświetlona pomoc.  Utwórzmy teraz funkcje.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. Na koniec funkcje, które będą używane na kliencie zarządzania sieci CDN, są asynchroniczne, więc potrzebują metody wywołania zwrotnego po zakończeniu.  Utwórzmy ją, która będzie mogła wyświetlać dane wyjściowe z klienta zarządzania usługi CDN (jeśli istnieje) i bezproblemowo zamyka program.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Teraz, gdy podstawowa struktura naszego programu jest zapisywana, należy utworzyć funkcje wywoływane na podstawie naszych parametrów.

## <a name="list-cdn-profiles-and-endpoints"></a>Wyświetlanie listy profilów i punktów końcowych usługi CDN
Zacznijmy od kodu, aby wyświetlić listę istniejących profilów i punktów końcowych.  Moje komentarze w kodzie zapewniają oczekiwaną składnię, więc wiemy, gdzie się znajduje każdy parametr.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(resourceGroupName, parms[2], callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Utwórz profile i punkty końcowe usługi CDN
Następnie zapiszemy funkcje, aby utworzyć profile i punkty końcowe.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create( resourceGroupName, parms[2], standardCreateParameters, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(resourceGroupName, parms[2], parms[3], endpointProperties, callback);
}
```

## <a name="purge-an-endpoint"></a>Przeczyszczanie punktu końcowego
Przy założeniu, że punkt końcowy został utworzony, jedno typowe zadanie, które możemy chcieć wykonać w naszym programie, powoduje przeczyszczanie zawartości w naszym punkcie końcowym.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(resourceGroupName, parms[2], parms[3], purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Usuń profile i punkty końcowe usługi CDN
Ostatnia funkcja obejmie usuwanie punktów końcowych i profilów.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteMethod(resourceGroupName, parms[2], callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteMethod(resourceGroupName, parms[2], parms[3], callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Uruchamianie programu
Teraz można wykonywać nasze Node.js programu przy użyciu naszego ulubionego debugera lub konsoli programu.

> [!TIP]
> Jeśli używasz Visual Studio Code jako debugera, musisz skonfigurować środowisko, aby przekazać je do parametrów wiersza polecenia.  Visual Studio Code robi to w **launch.js** pliku.  Wyszukaj właściwość o nazwie **args** i Dodaj tablicę wartości ciągów parametrów, tak aby wyglądała podobnie do:  `"args": ["list", "profiles"]` .
> 
> 

Zacznijmy od poinformowania naszych profilów.

![Lista profilów](./media/cdn-app-dev-node/cdn-list-profiles.png)

Mamy do końca pustą tablicę.  Ponieważ nie mamy żadnych profilów w naszej grupie zasobów, jest to oczekiwane.  Utwórz teraz profil.

![Utwórz profil](./media/cdn-app-dev-node/cdn-create-profile.png)

Teraz Dodajmy punkt końcowy.

![Utwórz punkt końcowy](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Na koniec usuńmy profil.

![Usuń profil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Następne kroki

Aby zobaczyć odwołanie do zestawu SDK Azure CDN dla języka JavaScript, Wyświetl [odwołanie](https://docs.microsoft.com/javascript/api/@azure/arm-cdn).

Aby znaleźć dodatkową dokumentację dotyczącą zestawu Azure SDK dla języka JavaScript, zobacz [pełne odwołanie](https://docs.microsoft.com/javascript/api/?view=azure-node-latest).

Zarządzanie zasobami usługi CDN przy użyciu [programu PowerShell](cdn-manage-powershell.md).
