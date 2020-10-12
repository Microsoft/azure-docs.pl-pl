---
title: plik dołączany
description: plik dołączany
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: aa8aea66c5481454f0d7d4d118934f5fbf34a911
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89147474"
---
**Domyślna tożsamość zarządzana** to przypisane do systemu tożsamość zarządzana lub pierwsza tożsamość zarządzana przez użytkownika.

W trakcie uruchamiania istnieją dwie aplikacje tożsamości:

1. System używa tożsamości do konfigurowania instalacji magazynu użytkownika, rejestru kontenerów i magazynów danych.

    * W takim przypadku system będzie używać tożsamości zarządzanej domyślnie.

1. Użytkownik stosuje tożsamość w celu uzyskania dostępu do zasobów z poziomu kodu dla przesłanego przebiegu

    * W takim przypadku podaj *client_id* odpowiadający tożsamości zarządzanej, która ma zostać użyta do pobrania poświadczenia.
    * Alternatywnie możesz uzyskać identyfikator klienta tożsamości przypisanej do użytkownika za pomocą zmiennej środowiskowej *DEFAULT_IDENTITY_CLIENT_ID* .

    Na przykład aby pobrać token dla magazynu danych z domyślną tożsamością zarządzaną:

    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')
    ```