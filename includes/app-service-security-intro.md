---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/15/2020
ms.author: cephalin
ms.openlocfilehash: 0fccb1ed092190f88d197c9bf2cd17678a4d1731
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83649056"
---
Składniki platformy App Service, w tym maszyny wirtualne platformy Azure, magazyn, połączenia sieciowe, platformy sieci Web, funkcje zarządzania i integracji, są aktywnie zabezpieczone i zaostrzone. App Service prowadzi przez silną kontrolę zgodności, aby upewnić się, że:

- Zasoby aplikacji są [zabezpieczone](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) z zasobów platformy Azure innych klientów.
- [Wystąpienia maszyn wirtualnych i oprogramowanie uruchomieniowe są regularnie aktualizowane](../articles/app-service/overview-patch-os-runtime.md) w celu rozwiązywania nowo odkrytych luk w zabezpieczeniach. 
- Komunikacja kluczy tajnych (takich jak parametry połączenia) między aplikacją i innymi zasobami platformy Azure (np. [SQL Database](https://azure.microsoft.com/services/sql-database/)) pozostaje w ramach platformy Azure i nie przekracza żadnych granic sieci. Wpisy tajne są zawsze szyfrowane, gdy są przechowywane.
- Cała komunikacja przez funkcje łączności App Service, na przykład [połączenie hybrydowe](../articles/app-service/app-service-hybrid-connections.md), jest zaszyfrowana. 
- Połączenia ze zdalnymi narzędziami do zarządzania, takimi jak Azure PowerShell, interfejs wiersza polecenia platformy Azure, zestawy SDK platformy Azure, interfejsy API REST, są szyfrowane.
- 24-godzinne zarządzanie zagrożeniami chroni infrastrukturę i platformę przed złośliwym oprogramowaniem, rozproszone odmowa usługi (DDoS), man-in-the-Middle (MITM) i inne zagrożenia.

Aby uzyskać więcej informacji na temat infrastruktury i zabezpieczeń platformy na platformie Azure, zobacz [Centrum zaufania Azure](https://azure.microsoft.com/overview/trusted-cloud/).