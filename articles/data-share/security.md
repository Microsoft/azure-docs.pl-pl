---
title: Omówienie zabezpieczeń usługi Azure Data Share
description: Omówienie zabezpieczeń usługi Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 4e62645dd5a7a8336df4fccf12daebc730a91168
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678420"
---
# <a name="security-overview-for-azure-data-share"></a>Omówienie zabezpieczeń usługi Azure Data Share

Ten artykuł zawiera omówienie zabezpieczeń usługi udziału danych platformy Azure.

## <a name="security-overview"></a>Omówienie zabezpieczeń

Udział danych platformy Azure wykorzystuje podstawowe zabezpieczenia oferowane przez platformę Azure w celu ochrony danych przechowywanych i przesyłanych. Dane są szyfrowane w spoczynku, gdzie są obsługiwane przez podstawowy magazyn danych. Dane są również szyfrowane podczas przesyłania przy użyciu protokołu TLS 1,2. Metadane dotyczące udziału danych są również szyfrowane w czasie spoczynku i podczas przesyłania. Udział danych platformy Azure nie przechowuje zawartości udostępnionych danych klienta.

Udział danych platformy Azure korzysta z tożsamości zarządzanej (znanej wcześniej jako MSI) w celu uzyskiwania dostępu do magazynów danych używanych do udostępniania danych. Nie ma wymiany poświadczeń między dostawcą danych i konsumentem danych. Aby uzyskać więcej informacji na temat tożsamości zarządzanej, zobacz [Zarządzanie tożsamościami dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Aby uzyskać więcej informacji na temat ról i uprawnień wymaganych do udostępniania danych, zapoznaj się z tematem [role i wymagania](concepts-roles-permissions.md).

## <a name="access-control"></a>Kontrola dostępu

Kontrola dostępu do udziału danych platformy Azure można ustawić na poziomie zasobów udostępniania danych, aby upewnić się, że jest on dostępny dla autoryzowanych. Właściciel i współautor zasobu udziału danych mogą udostępniać dane, odbierać udziały i wprowadzać zmiany w istniejących udziałach. Czytnik zasobu udziału danych może wyświetlać udziały, ale nie może wprowadzać zmian. 

Po utworzeniu lub odebraniu udziału użytkownicy z odpowiednimi uprawnieniami do zasobu udział danych mogą wprowadzać zmiany. Gdy użytkownik, który tworzy lub otrzymuje udział, opuszcza organizację, nie kończy udziału ani nie przerywa przepływu danych. Inni użytkownicy z odpowiednimi uprawnieniami do zasobu udział danych mogą nadal zarządzać udziałem.

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Udostępnianie danych z lub do magazynów danych z włączoną zaporą
Aby udostępnić dane z lub do kont magazynu z włączoną zaporą, należy włączyć opcję **Zezwalaj na zaufane usługi firmy Microsoft** na koncie magazynu. Aby uzyskać szczegółowe informacje [, zobacz Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) .


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .
