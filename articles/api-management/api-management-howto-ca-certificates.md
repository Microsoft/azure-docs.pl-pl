---
title: Dodawanie niestandardowego certyfikatu urzędu certyfikacji — Azure API Management | Microsoft Docs
description: Dowiedz się, jak dodać niestandardowy certyfikat urzędu certyfikacji w usłudze Azure API Management. Możesz również zobaczyć instrukcje dotyczące usuwania certyfikatu.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51719f23302bfaa036f99e88fcd440d97ca3bc02
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817832"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Jak dodać niestandardowy certyfikat urzędu certyfikacji w usłudze Azure API Management

Usługa Azure API Management umożliwia instalowanie certyfikatów urzędu certyfikacji na maszynie w zaufanych magazynach certyfikatów głównych i pośrednich. Tej funkcji należy używać, jeśli usługi wymagają niestandardowego certyfikatu urzędu certyfikacji.

W tym artykule pokazano, jak zarządzać certyfikatami urzędu certyfikacji wystąpienia usługi Azure API Management w Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Przekazywanie certyfikatu urzędu certyfikacji

![Dodawanie certyfikatów urzędu certyfikacji](media/api-management-howto-ca-certificates/00.png)

Wykonaj poniższe kroki, aby przekazać nowy certyfikat urzędu certyfikacji. Jeśli jeszcze nie utworzono API Management usługi, zobacz samouczek [Tworzenie wystąpienia API Management usługi](get-started-create-service-instance.md).

1. Przejdź do wystąpienia usługi Azure API Management service w Azure Portal.

2. Wybierz z menu **pozycję** Certyfikaty urzędu certyfikacji.

3. Kliknij przycisk **+ Dodaj**.  

    ![Zrzut ekranu przedstawiający przycisk + Dodaj do dodawania certyfikatu urzędu certyfikacji.](media/api-management-howto-ca-certificates/01.png)  

4. Wyszukaj certyfikat i wybierz magazyn certyfikatów. Wymagany jest tylko klucz publiczny, więc hasło nie jest wymagane.

    ![Zrzut ekranu przedstawiający sposób przeglądania certyfikatu.](media/api-management-howto-ca-certificates/02.png)  

5. Kliknij pozycję **Zapisz**. Ta operacja może potrwać kilka minut.

    ![Zrzut ekranu przedstawiający sposób zapisywania certyfikatu.](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Certyfikat urzędu certyfikacji można przekazać za pomocą `New-AzApiManagementSystemCertificate` polecenia programu PowerShell.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Usuwanie certyfikatu klienta

Aby usunąć certyfikat, kliknij menu **kontekstowe ...** i wybierz **pozycję Usuń** obok certyfikatu.

![Usuwanie certyfikatów urzędu certyfikacji](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
