---
title: Nawiązywanie połączenia z klientem Microsoft Store — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta Microsoft Store.
author: Heidilohr
ms.topic: how-to
ms.date: 10/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 5662b32d8e2c9af457ded62b0302c2c64b31edea
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448019"
---
# <a name="connect-with-the-microsoft-store-client"></a>Nawiązywanie połączenia z klientem sklepu Microsoft Store

>Dotyczy: system Windows 10.

Dostęp do zasobów pulpitu wirtualnego systemu Windows można uzyskać na urządzeniach z systemem Windows 10.

## <a name="install-the-microsoft-store-client"></a>Instalowanie klienta Microsoft Store

Klienta programu można zainstalować dla bieżącego użytkownika, który nie wymaga uprawnień administratora. Administrator może również zainstalować i skonfigurować klienta, aby mógł uzyskać do niego dostęp wszyscy użytkownicy na urządzeniu.

Po zainstalowaniu klienta programu można uruchomić z menu Start, wyszukując Pulpit zdalny.

Aby rozpocząć, [Pobierz i zainstaluj klienta programu z Microsoft Store](https://www.microsoft.com/store/productId/9WZDNCRFJ3PS).

## <a name="subscribe-to-a-workspace"></a>Subskrybowanie obszaru roboczego

Zasubskrybuj obszar roboczy udostępniony przez administratora, aby uzyskać listę zasobów zarządzanych, do których możesz uzyskać dostęp na komputerze.

Aby subskrybować obszar roboczy:

1. Na ekranie centrum połączeń naciśnij pozycję **+ Dodaj**, a następnie naciśnij pozycję **obszary robocze**.
2. Wprowadź adres URL obszaru roboczego do pola adresu URL obszaru roboczego dostarczonego przez administratora. Adres URL obszaru roboczego może być adresem URL lub adresem e-mail.
   
   - Jeśli używasz adresu URL obszaru roboczego, użyj adresu URL otrzymanego przez administratora.
   - Jeśli łączysz się z pulpitu wirtualnego systemu Windows, użyj jednego z następujących adresów URL w zależności od używanej wersji usługi:
       - Pulpit wirtualny systemu Windows (klasyczny): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx` .
       - Pulpit wirtualny systemu Windows: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery` .
  
3. Naciśnij pozycję **Subskrybuj**.
4. Po wyświetleniu monitu podaj swoje poświadczenia.
5. Po zasubskrybowaniu obszary robocze powinny być wyświetlane w centrum połączenia.

Obszary robocze można dodawać, zmieniać lub usuwać na podstawie zmian wprowadzonych przez administratora.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o używaniu klienta Microsoft Store, zapoznaj się z tematem Rozpoczynanie [pracy z klientem Microsoft Store](/windows-server/remote/remote-desktop-services/clients/windows/).