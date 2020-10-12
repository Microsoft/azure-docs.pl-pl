---
title: Omówienie zabezpieczeń usługi Azure Data Share
description: Omówienie zabezpieczeń usługi Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 06/05/2020
ms.openlocfilehash: 10f31b74b461941b15f13e45f90b5fbc408c90fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86108417"
---
# <a name="security-overview-for-azure-data-share"></a>Omówienie zabezpieczeń usługi Azure Data Share

Ten artykuł zawiera omówienie zabezpieczeń usługi udziału danych platformy Azure.

## <a name="security-overview"></a>Omówienie zabezpieczeń

Udział danych platformy Azure wykorzystuje podstawowe zabezpieczenia oferowane przez platformę Azure w celu ochrony danych przechowywanych i przesyłanych. Dane są szyfrowane w spoczynku, gdzie są obsługiwane przez podstawowy magazyn danych. Dane są również szyfrowane podczas przesyłania. Metadane dotyczące udziału danych są również szyfrowane w czasie spoczynku i podczas przesyłania. 

Kontrolę dostępu można ustawić na poziomie zasobów udziału danych platformy Azure, aby upewnić się, że jest on dostępny dla autoryzowanych. 

Udział danych platformy Azure korzysta z tożsamości zarządzanej (znanej wcześniej jako MSI) w celu uzyskiwania dostępu do magazynów danych używanych do udostępniania danych. Nie ma wymiany poświadczeń między dostawcą danych i konsumentem danych. Aby uzyskać więcej informacji na temat tożsamości zarządzanej, zobacz [Zarządzanie tożsamościami dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). Aby uzyskać więcej informacji na temat ról i uprawnień wymaganych do udostępniania danych, zapoznaj się z tematem [role i wymagania](concepts-roles-permissions.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .




