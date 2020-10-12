---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67448622"
---
Odpowiedni certyfikat SSL zapewnia, że dane zaszyfrowane są wysyłane do właściwego serwera. Oprócz szyfrowania certyfikat umożliwia również uwierzytelnianie. Można przekazać własny zaufany certyfikat SSL za pośrednictwem interfejsu programu PowerShell urządzenia.

1. [Nawiąż połączenie z interfejsem programu PowerShell](#connect-to-the-powershell-interface).
2. Użyj `Set-HcsCertificate` polecenia cmdlet, aby przekazać certyfikat. Po wyświetleniu monitu podaj następujące parametry:

   - `CertificateFilePath` -Ścieżka do udziału zawierającego plik certyfikatu w formacie *PFX* .
   - `CertificatePassword` — Hasło używane do ochrony certyfikatu.
   - `Credentials` -Username, aby uzyskać dostęp do udziału, który zawiera certyfikat. Po wyświetleniu monitu podaj hasło do udziału sieciowego.

     W poniższym przykładzie pokazano użycie tego polecenia cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

