---
title: Przekazywanie certyfikatu zarządzania usługami platformy Azure | Microsoft Docs
description: Dowiedz się, jak przekazać certyfikat zarządzania usługą dla Azure Portal.
services: cloud-services
documentationcenter: .net
author: georgewallace
manager: carmonm
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: article
ms.date: 08/01/2017
ms.author: gwallace
ms.openlocfilehash: 4b49a9b391eeca2d2e249b171d99f231bda6fdff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78329123"
---
# <a name="upload-an-azure-service-management-certificate"></a>Przekazywanie certyfikatu zarządzania usługami platformy Azure
Certyfikaty zarządzania umożliwiają uwierzytelnianie przy użyciu klasycznego modelu wdrażania dostarczonego przez platformę Azure. Wiele programów i narzędzi (takich jak program Visual Studio lub zestaw Azure SDK) używa tych certyfikatów do zautomatyzowania konfigurowania i wdrażania różnych usług platformy Azure. 

> [!WARNING]
> Ostrożnie! Te typy certyfikatów umożliwiają osobom, które uwierzytelniają się za ich pomocą, Zarządzanie subskrypcją, z którą są skojarzone.
>
>

Jeśli chcesz uzyskać więcej informacji na temat certyfikatów platformy Azure (w tym tworzenia certyfikatu z podpisem własnym), zobacz [Omówienie certyfikatów dla platformy Azure Cloud Services](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Za pomocą [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) można także uwierzytelniać kod klienta w celach automatyzacji.

**Uwaga:** Aby wykonać dowolne operacje w ramach certyfikatów zarządzania, musisz być współadministratorem w ramach subskrypcji. [Dowiedz się więcej](https://go.microsoft.com/fwlink/?linkid=849300) na temat dodawania lub usuwania współadministratorów z nowej witryny Azure Portal 

## <a name="upload-a-management-certificate"></a>Przekazywanie certyfikatu zarządzania
Po utworzeniu certyfikatu zarządzania (plik. cer z tylko kluczem publicznym) można przekazać go do portalu. Gdy certyfikat jest dostępny w portalu, każda osoba mająca zgodny certyfikat (klucz prywatny) może nawiązać połączenie za pomocą interfejsu API zarządzania i uzyskać dostęp do zasobów dla skojarzonej subskrypcji.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **wszystkie usługi** na dolnej liście usług platformy Azure, a następnie wybierz pozycję **subskrypcje** w grupie usługi _Ogólne_ .

    ![Menu subskrypcji](./media/azure-api-management-certs/subscriptions_menu.png)

3. Upewnij się, że wybrano prawidłową subskrypcję, którą chcesz skojarzyć z certyfikatem.     
4. Po wybraniu odpowiedniej subskrypcji naciśnij pozycję **certyfikaty zarządzania** w grupie _Ustawienia_ .

    ![Ustawienia](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Naciśnij przycisk **Przekaż** .

    ![Strona przekazywania na certyfikaty](./media/azure-api-management-certs/certificates_page.png)
6. Wypełnij informacje w oknie dialogowym i naciśnij przycisk **Przekaż**.

    ![Ustawienia](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy masz certyfikat zarządzania skojarzony z subskrypcją, możesz (po zainstalowaniu zgodnego certyfikatu lokalnie) nawiązać programowo połączenie z [interfejsem API REST klasycznego modelu wdrażania](/azure/?pivot=sdkstools) i zautomatyzować różne zasoby platformy Azure, które również są skojarzone z tą subskrypcją.
