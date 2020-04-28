---
title: Programowanie dla Azure NetApp Files przy użyciu interfejsu API REST | Microsoft Docs
description: Opisuje, jak rozpocząć korzystanie z interfejsu API REST Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: b-juche
ms.openlocfilehash: 996fbcc7c3c9af0da9160216785ecd54840660e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "65957041"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Programowanie dla Azure NetApp Files za pomocą interfejsu API REST 

Interfejs API REST dla usługi Azure NetApp Files definiuje operacje HTTP na podstawie zasobów, takich jak konto NetApp, Pula pojemności, woluminy i migawki. Ten artykuł pomaga rozpocząć korzystanie z interfejsu API REST Azure NetApp Files.

## <a name="azure-netapp-files-rest-api-specification"></a>Specyfikacja interfejsu API REST Azure NetApp Files

Specyfikacja interfejsu API REST dla Azure NetApp Files jest publikowana w serwisie [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager):

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Dostęp do interfejsu API REST Azure NetApp Files  

1. [Zainstaluj interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , jeśli jeszcze tego nie zrobiono.
2. Utwórz nazwę główną usługi w Azure Active Directory (Azure AD):
   1. Sprawdź, czy masz [wystarczające uprawnienia](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

   1. Wprowadź następujące polecenie w interfejsie wiersza polecenia platformy Azure:  

           az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

      Dane wyjściowe polecenia są podobne do poniższego przykładu:  

           { 
               "appId": "appIDgoeshere", 
               "displayName": "APPNAME", 
               "name": "http://APPNAME", 
               "password": "supersecretpassword", 
               "tenant": "tenantIDgoeshere" 
           } 

      Zachowaj dane wyjściowe polecenia.  Będziesz potrzebować wartości `appId`, `password`i. `tenant` 

3. Zażądaj tokenu dostępu OAuth:

    W przykładach w tym artykule użyto zwinięcie.  Można również użyć różnych narzędzi interfejsu API, takich jak [Poster](https://www.getpostman.com/), [o braku usypiania](https://insomnia.rest/)i [dostępem uprzywilejowanym](https://paw.cloud/).  

    Zamień zmienne w poniższym przykładzie na dane wyjściowe polecenia z kroku 2 powyżej. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    Dane wyjściowe zawierają token dostępu podobny do poniższego przykładu:

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    Wyświetlany token jest prawidłowy przez 3600 sekund. Następnie musisz zażądać nowego tokenu. 
    Zapisz token w edytorze tekstu.  Będzie ona potrzebna w następnym kroku.

4. Wyślij wywołanie testowe i Dołącz token, aby zweryfikować dostęp do interfejsu API REST:

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>Przykłady korzystania z interfejsu API  

W tym artykule jest stosowany następujący adres URL dla linii bazowej żądań. Ten adres URL wskazuje katalog główny przestrzeni nazw Azure NetApp Files. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

Należy zastąpić wartości `subID` i `resourceGroups` w poniższych przykładach własnymi wartościami. 

### <a name="get-request-examples"></a>Przykłady żądania GET

Za pomocą żądania GET można wykonywać zapytania dotyczące obiektów Azure NetApp Files w ramach subskrypcji, jak pokazano w następujących przykładach: 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>Przykładowe żądania PUT

Żądanie PUT służy do tworzenia nowych obiektów w Azure NetApp Files, jak pokazano w poniższych przykładach. Treść żądania PUT może zawierać dane w formacie JSON dla zmian lub można określić plik, z którego ma zostać odczytany. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>Przykłady JSON

Poniższy przykład pokazuje, jak utworzyć konto NetApp:

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

Poniższy przykład pokazuje, jak utworzyć pulę pojemności: 

    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME"
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }

Poniższy przykład pokazuje, jak utworzyć nowy wolumin: 

    {
        "name": "MYNEWVOLUME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
        "location": "westus2",
        "properties": {
            "serviceLevel": "Premium",
            "usageThreshold": "322122547200",
            "creationToken": "MY-FILEPATH",
            "snapshotId": "",
            "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
            }
    }

Poniższy przykład pokazuje, jak utworzyć migawkę woluminu: 

    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }

> [!NOTE] 
> Należy określić `fileSystemId` , aby utworzyć migawkę.  Można uzyskać `fileSystemId` wartość za pomocą żądania GET do woluminu. 

## <a name="next-steps"></a>Następne kroki

[Zobacz Dokumentacja interfejsu API REST Azure NetApp Files](https://docs.microsoft.com/rest/api/netapp/)
