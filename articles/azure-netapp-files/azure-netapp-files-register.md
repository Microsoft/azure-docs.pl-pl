---
title: Rejestrowanie się w usłudze Azure NetApp Files | Microsoft Docs
description: Dowiedz się, jak zarejestrować się w celu Azure NetApp Files, przesyłając żądanie waitlist i rejestrując dostawcę zasobów platformy Azure dla Azure NetApp Files.
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
ms.topic: how-to
ms.date: 06/09/2020
ms.author: b-juche
ms.openlocfilehash: a61d6ba1f908a7dae56db066bfae329fc26f1c5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696137"
---
# <a name="register-for-azure-netapp-files"></a>Rejestrowanie w usłudze Azure NetApp Files

> [!IMPORTANT] 
> Przed zarejestrowaniem dostawcy zasobów Azure NetApp Files należy otrzymać wiadomość e-mail od zespołu Azure NetApp Files potwierdzenie, że udzielono dostępu do usługi. 

W tym artykule dowiesz się, jak zarejestrować się w celu Azure NetApp Files, aby rozpocząć korzystanie z usługi.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Prześlij żądanie waitlist w celu uzyskania dostępu do usługi

1. Przejdź do następującej strony i prześlij żądanie waitlist w celu uzyskania dostępu do usługi Azure NetApp Files:  
    [**Strona Azure NetApp Files przesłania waitlist**](https://aka.ms/azurenetappfiles) 

    Rejestracja waitlist nie gwarantuje natychmiastowego dostępu do usługi. 

2. Przed kontynuowaniem innych zadań poczekaj na oficjalną wiadomość e-mail z potwierdzeniem z zespołu Azure NetApp Files. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>Rejestrowanie dostawcy zasobów usługi NetApp

Aby korzystać z usługi, należy zarejestrować dostawcę zasobów platformy Azure dla usługi Azure NetApp Files.

> [!NOTE] 
> Można pomyślnie zarejestrować dostawcę zasobów NetApp nawet bez udzielania dostępu do usługi. Jednak bez dostępu autoryzacja dowolna Azure Portal lub żądanie interfejsu API do utworzenia konta usługi NetApp lub dowolnego innego zasobu Azure NetApp Files zostanie odrzucone z powodu następującego błędu:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. W witrynie Azure Portal kliknij ikonę usługi Azure Cloud Shell w prawym górnym rogu:

      ![Ikona usługi Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Jeśli masz wiele subskrypcji na koncie platformy Azure, wybierz tę, która została zatwierdzona do Azure NetApp Files:
    
    ```azurepowershell
    az account set --subscription <subscriptionId>
    ```

3. W konsoli Azure Cloud Shell wprowadź następujące polecenie, aby sprawdzić, czy subskrypcja została zatwierdzona:
    
    ```azurepowershell
    az feature list | grep NetApp
    ```

   Dane wyjściowe polecenia są podobne do następujących:
   
    ```output
    "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
    "name": "Microsoft.NetApp/ANFGA" 
    ```
       
   `<SubID>` to identyfikator Twojej subskrypcji.

    Jeśli nie widzisz nazwy funkcji `Microsoft.NetApp/ANFGA` , nie masz dostępu do usługi. Zatrzymaj w tym kroku. Aby uzyskać dostęp do usługi przed kontynuowaniem, postępuj zgodnie z instrukcjami w temacie [Prześlij żądanie waitlist](#waitlist) . 

4. W konsoli usługi Azure Cloud Shell wprowadź następujące polecenie, aby zarejestrować dostawcę zasobów platformy Azure: 
    
    ```azurepowershell
    az provider register --namespace Microsoft.NetApp --wait
    ```

   Parametr `--wait` instruuje konsolę, aby zaczekać na ukończenie rejestracji. Proces rejestracji może trochę potrwać.

5. W konsoli usługi Azure Cloud Shell wprowadź następujące polecenie, aby sprawdzić, czy dostawca zasobów platformy Azure został zarejestrowany: 
    
    ```azurepowershell
    az provider show --namespace Microsoft.NetApp
    ```

   Dane wyjściowe polecenia są podobne do następujących:
   
    ```output
    {
     "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
     "namespace": "Microsoft.NetApp", 
     "registrationState": "Registered", 
     "resourceTypes": […. 
    ```

   `<SubID>` to identyfikator Twojej subskrypcji.  Wartość parametru `state` wskazuje stan `Registered`.

6. W witrynie Azure Portal kliknij blok **Subskrypcje**.
7. W bloku Subskrypcje kliknij identyfikator subskrypcji. 
8. W ustawieniach subskrypcji kliknij pozycję **Dostawcy zasobów**, aby sprawdzić, czy dostawca Microsoft.NetApp Provider wskazuje stan Zarejestrowane: 

      ![Zarejestrowany dostawca Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Następne kroki

[Tworzenie konta usługi NetApp](azure-netapp-files-create-netapp-account.md)
