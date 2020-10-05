---
title: Przepływ pracy zaświadczania platformy Azure
description: Przepływ pracy zaświadczania platformy Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 240b27f897d8e7a34026701cf7fdc844eb9d4086
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89237315"
---
# <a name="workflow"></a>Przepływ pracy

Zaświadczanie Microsoft Azure otrzymuje dowody od enclavess i szacuje dowody dotyczące linii bazowej zabezpieczeń platformy Azure i konfigurowalnych zasad. Po pomyślnym weryfikacja zaświadczanie platformy Azure generuje token zaświadczania, aby potwierdzić wiarygodność enklawy.

Następujące aktory są zaangażowane w przepływ pracy zaświadczania o platformie Azure:

- Jednostka **uzależniona**: składnik, który opiera się na zaświadczeniu platformy Azure, aby zweryfikować ważność enklawy. 
- **Klient**: składnik, który zbiera informacje z enklawy i wysyła żądania do zaświadczania platformy Azure. 
- **Zaświadczanie platformy Azure**: składnik, który akceptuje enklawy dowody z klienta, sprawdza poprawność i zwraca token zaświadczania do klienta


## <a name="enclave-validation-work-flow"></a>Przepływ pracy weryfikacji enklawy

Poniżej przedstawiono ogólne kroki w typowym przepływie pracy zaświadczania SGX enklawy (przy użyciu zaświadczania platformy Azure):

1. Klient zbiera dowody z enklawy. Dowody to informacje o środowisku enklawy i bibliotece klienckiej działającej wewnątrz enklawy.
1. Klient ma identyfikator URI, który odwołuje się do wystąpienia zaświadczania platformy Azure. Klient jest uwierzytelniany w usłudze Azure AD i uzyskuje token dostępu.
1. Klient wysyła dowód do zaświadczania platformy Azure wraz z tokenem dostępu. Dokładne informacje przesłane do dostawcy zależą od typu enklawy.
1. Zaświadczanie platformy Azure weryfikuje przesłane informacje i szacuje je pod kątem skonfigurowanych zasad. Jeśli weryfikacja zakończy się pomyślnie, zaświadczanie platformy Azure wystawia token zaświadczania i zwróci go do klienta. Jeśli ten krok zakończy się niepowodzeniem, zaświadczanie platformy Azure zgłosi błąd klientowi. 
1. Klient wysyła token zaświadczania do jednostki uzależnionej. Jednostka uzależniona wywołuje punkt końcowy metadanych klucza publicznego z zaświadczania platformy Azure w celu pobrania certyfikatów podpisywania. Jednostka uzależniona weryfikuje sygnaturę tokenu zaświadczania i zapewnia wiarygodność enklawy. 

![Enklawy — przepływ walidacji](./media/validation-flow.png)


## <a name="next-steps"></a>Następne kroki
- [Tworzenie i podpisywanie zasad zaświadczania](author-sign-policy.md)
- [Konfigurowanie zaświadczania platformy Azure przy użyciu programu PowerShell](quickstart-powershell.md)
