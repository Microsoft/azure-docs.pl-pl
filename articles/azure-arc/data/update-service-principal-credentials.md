---
title: Aktualizowanie poświadczeń jednostki usługi
description: Aktualizowanie poświadczeń dla jednostki usługi
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: mikeray
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 1c7ccec6228a6dcfb457bda8f6ecd384775d4b27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669546"
---
# <a name="update-service-principal-credentials"></a>Aktualizowanie poświadczeń jednostki usługi

Po zmianie poświadczeń jednostki usługi należy zaktualizować wpisy tajne w kontrolerze danych.

Jeśli na przykład wdrożono kontroler danych przy użyciu określonego zestawu wartości dla identyfikatora dzierżawy głównej usługi, identyfikatora klienta i klucza tajnego klienta, a następnie zmienisz co najmniej jedną z tych wartości, należy zaktualizować wpisy tajne w kontrolerze danych.  Poniżej znajdują się instrukcje aktualizowania identyfikatora dzierżawy, identyfikatora klienta lub klucza tajnego klienta. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="background"></a>Tło

Nazwa główna usługi została utworzona podczas [tworzenia nazwy głównej usługi](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal). 

## <a name="steps"></a>Kroki

1. Dostęp do klucza tajnego jednostki usługi w domyślnym edytorze.

   ```console
   kubectl edit secret/upload-service-principal-secret -n <name of namespace>
   ```

   Aby na przykład zmodyfikować klucz tajny jednostki usługi do kontrolera danych w `arc` przestrzeni nazw, uruchom następujące polecenie:

   ```console
   kubectl edit secret/upload-service-principal-secret -n arc
   ```

   `kubecl edit`Polecenie otwiera plik Credentials. yml w domyślnym edytorze. 


1. Edytuj klucz tajny jednostki usługi. 

   W domyślnym edytorze Zastąp wartości w sekcji dane zaktualizowanymi informacjami o poświadczeniach.

   Przykład:

   ```console
   # Please edit the object below. Lines beginning with a '#' will be ignored,
   # and an empty file will abort the edit. If an error occurs while saving this file will be
   # reopened with the relevant failures.
   #
   apiVersion: v1
   data:
     authority: aHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29t
     clientId: NDNiNDcwYrFTGWYzOC00ODhkLTk0ZDYtNTc0MTdkN2YxM2Uw
     clientSecret: VFA2RH125XU2MF9+VVhXenZTZVdLdECXFlNKZi00Lm9NSw==
     tenantId: NzJmOTg4YmYtODZmMRFVBGTJLSATkxYWItMmQ3Y2QwMTFkYjQ3
   kind: Secret
   metadata:
     creationTimestamp: "2020-12-02T05:02:04Z"
     name: upload-service-principal-secret
     namespace: arc
     resourceVersion: "7235659"
     selfLink: /api/v1/namespaces/arc/secrets/upload-service-principal-secret
     uid: 7fb693ff-6caa-4a31-b83e-9bf22be4c112
   type: Opaque
   ```

   Edytuj wartości dla `clientID` `clientSecret` i/lub `tenantID` w zależności od potrzeb. 

> [!NOTE]
>Wartości muszą być zakodowane w formacie base64. Nie należy edytować żadnych innych właściwości.

Jeśli podano nieprawidłową wartość dla programu `clientId` , `clientSecret` lub `tenantID` w `control-xxxx` dziennikach kontenera/kontrolera zostanie wyświetlony komunikat o błędzie w następujący sposób:

```output
YYYY-MM-DD HH:MM:SS.mmmm | ERROR | [AzureUpload] Upload task exception: A configuration issue is preventing authentication - check the error message from the server for details.You can modify the configuration in the application registration portal. See https://aka.ms/msal-net-invalid-client for details.  Original exception: AADSTS7000215: Invalid client secret is provided.
```



## <a name="next-steps"></a>Następne kroki

[Pobieranie nazwy użytkownika i hasła w celu nawiązania połączenia z kontrolerem danych usługi Arc](retrieve-the-username-password-for-data-controller.md)

[Tworzenie jednostki usługi](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
