---
title: Łączenie się z urządzeniem Azure Data Box Gateway i zarządzanie nim za pomocą programu Windows PowerShell
description: Opisuje sposób nawiązywania połączenia z Data Box Gateway i zarządzania nim za pomocą interfejsu programu Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: c071d372ba90d29806fd8a44909e2c803a8d3fa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84339284"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Zarządzanie urządzeniem Azure Data Box Gateway za pomocą programu Windows PowerShell

Rozwiązanie Azure Data Box Gateway umożliwia wysyłanie danych za pośrednictwem sieci do platformy Azure. W tym artykule opisano niektóre zadania związane z konfiguracją i zarządzaniem dla urządzenia Data Box Gateway. Aby zarządzać urządzeniem, można użyć Azure Portal, lokalnego interfejsu użytkownika sieci Web lub interfejsu programu Windows PowerShell.

Ten artykuł koncentruje się na zadaniach, które można wykonać za pomocą interfejsu programu PowerShell.

Ten artykuł zawiera następujące procedury:

- Nawiązywanie połączenia z interfejsem programu PowerShell
- Tworzenie pakietu dla pomocy technicznej
- Przekazywanie certyfikatu
- Rozruch w środowisku innym niż DHCP
- Wyświetl informacje o urządzeniu

## <a name="connect-to-the-powershell-interface"></a>Nawiązywanie połączenia z interfejsem programu PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Tworzenie pakietu dla pomocy technicznej

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Przekazywanie certyfikatu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Rozruch w środowisku innym niż DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Wyświetl informacje o urządzeniu

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Następne kroki

- Wdróż rozwiązanie [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) w witrynie Azure Portal.
