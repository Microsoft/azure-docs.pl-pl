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
ms.date: 01/23/2017
ms.author: mazha
ms.custom: devx-track-js
ms.openlocfilehash: f5d5c7a6e1f6993b19f38db2ae846b213a1d553e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993372"
---
# <a name="get-started-with-azure-cdn-development"></a>Rozpoczynanie pracy z wdrażaniem usługi Azure CDN
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Możesz użyć [zestawu SDK Azure CDN ](https://www.npmjs.com/package/azure-arm-cdn) , aby Node.jsdo automatyzowania tworzenia profilów i punktów końcowych usługi CDN oraz zarządzania nimi.  Ten samouczek przeprowadzi Cię przez proces tworzenia prostej aplikacji konsolowej Node.js, która pokazuje kilka dostępnych operacji.  Ten samouczek nie ma na celu szczegółowego opisania wszystkich aspektów zestawu Node.js SDK Azure CDN.

Aby ukończyć ten samouczek, należy mieć zainstalowany i skonfigurowany [Node.js](https://www.nodejs.org) **4. x. x** lub nowszy.  Możesz użyć dowolnego edytora tekstu, aby utworzyć aplikację Node.js.  Aby napisać ten samouczek, używam [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> [Ukończony projekt z tego samouczka](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) jest dostępny do pobrania w witrynie MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Tworzenie projektu i Dodawanie zależności NPM
Po utworzeniu grupy zasobów dla naszych profilów usługi CDN i udostępnieniu naszej aplikacji usługi Azure AD uprawnienia do zarządzania profilami i punktami końcowymi sieci CDN w tej grupie możemy rozpocząć tworzenie aplikacji.

Utwórz folder do przechowywania aplikacji.  Z poziomu konsoli programu z narzędziami Node.js w bieżącej ścieżce Ustaw bieżącą lokalizację na ten nowy folder i zainicjuj projekt, wykonując następujące polecenie:

```console
npm init
```

Zostanie wyświetlona seria pytań umożliwiających zainicjowanie projektu.  W przypadku **punktu wejścia** ten samouczek używa *app.js*.  W poniższym przykładzie można zobaczyć inne opcje.

![NPM dane wyjściowe init](./media/cdn-app-dev-node/cdn-npm-init.png)

Nasz projekt jest teraz zainicjowany przy użyciu *packages.js* pliku.  Nasz projekt będzie używać niektórych bibliotek platformy Azure zawartych w pakietach NPM.  Użyjemy środowiska uruchomieniowego klienta platformy Azure dla Node.js (MS-REST-Azure) i biblioteki klienta Azure CDN dla Node.js (Azure-ARM-CD).  Dodajmy je do projektu jako zależności.

```console
npm install --save ms-rest-azure
npm install --save azure-arm-cdn
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
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Na koniec za pomocą edytora tekstów Utwórz pusty plik tekstowy i Zapisz go w folderze głównym naszego folderu projektu jako *app.js*.  Teraz wszystko jest gotowe do rozpoczęcia pisania kodu.

## <a name="requires-constants-authentication-and-structure"></a>Wymaga, stałe, uwierzytelniania i struktury
Dzięki *app.js* otwartym w naszym edytorze Przyjrzyjmy się podstawowej strukturze naszego programu.

1. Dodaj "wymagane" dla naszych pakietów NPM w górnej części z następującymi:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
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
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Jeśli używasz uwierzytelniania pojedynczego użytkownika, te dwa wiersze będą wyglądały nieco inaczej.
   
   > [!IMPORTANT]
   > Tego przykładowego kodu należy używać tylko w przypadku wybrania opcji uwierzytelniania poszczególnych użytkowników zamiast nazwy głównej usługi.  Należy zachować ostrożność, aby zabezpieczyć poświadczenia poszczególnych użytkowników i zachować ich klucz tajny.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Pamiętaj, aby zastąpić elementy w **&lt; nawiasach &gt; kątowych** odpowiednimi informacjami.  W przypadku programu `<redirect URI>` Użyj identyfikatora URI przekierowania wprowadzonego podczas rejestrowania aplikacji w usłudze Azure AD.
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
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
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

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
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

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
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
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
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
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
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
Aby wyświetlić ukończony projekt z tego przewodnika, [Pobierz przykład](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Aby wyświetlić odwołanie do zestawu SDK Azure CDN dla Node.js, Wyświetl [odwołanie](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Aby znaleźć dodatkową dokumentację dotyczącą usługi Azure SDK dla Node.js, Wyświetl [pełne odwołanie](https://azure.github.io/azure-sdk-for-node/).

Zarządzanie zasobami usługi CDN przy użyciu [programu PowerShell](cdn-manage-powershell.md).

