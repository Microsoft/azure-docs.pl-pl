---
title: Przepływ pracy zaświadczania platformy Azure
description: Przepływ pracy zaświadczania platformy Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 27a97ceb2ca9a7b58df7200930e4e47d89c9ae89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98762195"
---
# <a name="workflow"></a>Przepływ pracy

Zaświadczanie Microsoft Azure otrzymuje dowody od enclaves i szacuje dowody dotyczące linii bazowej zabezpieczeń platformy Azure i konfigurowalnych zasad. Po pomyślnej weryfikacji usługa Azure zaświadczania generuje token zaświadczania, aby potwierdzić wiarygodność enklawy.

Następujące aktory są zaangażowane w przepływ pracy zaświadczania o platformie Azure:

- Jednostka **uzależniona**: składnik, który opiera się na zaświadczeniu platformy Azure, aby zweryfikować ważność enklawy. 
- **Klient**: składnik, który zbiera informacje z enklawy i wysyła żądania do zaświadczania platformy Azure. 
- **Zaświadczanie platformy Azure**: składnik, który akceptuje enklawy dowody z klienta, sprawdza poprawność i zwraca token zaświadczania do klienta


## <a name="intel-software-guard-extensions-sgx-enclave-validation-work-flow"></a>SGX (Intel® Software Guard Extensions) enklawy — przepływ pracy walidacji

Poniżej przedstawiono ogólne kroki w typowym przepływie pracy zaświadczania SGX enklawy (przy użyciu zaświadczania platformy Azure):

1. Klient zbiera dowody z enklawy. Dowody to informacje o środowisku enklawy i bibliotece klienckiej działającej wewnątrz enklawy.
1. Klient ma identyfikator URI, który odwołuje się do wystąpienia zaświadczania platformy Azure. Klient wysyła dowód do zaświadczania platformy Azure. Dokładne informacje przesłane do dostawcy zależą od typu enklawy.
1. Zaświadczanie platformy Azure weryfikuje przesłane informacje i szacuje je pod kątem skonfigurowanych zasad. Jeśli weryfikacja zakończy się pomyślnie, zaświadczanie platformy Azure wystawia token zaświadczania i zwróci go do klienta. Jeśli ten krok zakończy się niepowodzeniem, zaświadczanie platformy Azure zgłosi błąd klientowi. 
1. Klient wysyła token zaświadczania do jednostki uzależnionej. Jednostka uzależniona wywołuje punkt końcowy metadanych klucza publicznego z zaświadczania platformy Azure w celu pobrania certyfikatów podpisywania. Jednostka uzależniona weryfikuje sygnaturę tokenu zaświadczania i zapewnia wiarygodność enklawy. 

![SGX enklawy — przepływ walidacji](./media/sgx-validation-flow.png)

> [!Note]
> W przypadku wysyłania żądań zaświadczania w wersji interfejsu API [2018-09-01-Preview](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/attestation/data-plane/Microsoft.Attestation/stable/2018-09-01-preview) klient musi wysyłać do zaświadczania o platformie Azure, a także token dostępu usługi Azure AD.

## <a name="trusted-platform-module-tpm-enclave-validation-work-flow"></a>Przepływ pracy weryfikacji Trusted Platform Module (TPM) enklawy

Poniżej przedstawiono ogólne kroki w typowym przepływie pracy zaświadczania enklawy TPM (przy użyciu zaświadczania platformy Azure):

1.  W przypadku rozruchowego urządzenia/platformy różne moduły ładujące rozruchowe i usługi uruchomieniowe mierzą zdarzenia, które są obsługiwane przez moduł TPM i są bezpiecznie przechowywane (Dziennik TCG).
2.  Klient zbiera dzienniki TCG z urządzenia i oferty modułu TPM, które są przeznaczone do zaświadczania.
3.  Klient ma identyfikator URI, który odwołuje się do wystąpienia zaświadczania platformy Azure. Klient wysyła dowód do zaświadczania platformy Azure. Dokładne informacje przesłane do dostawcy zależą od platformy.
4.  Zaświadczanie platformy Azure weryfikuje przesłane informacje i szacuje je pod kątem skonfigurowanych zasad. Jeśli weryfikacja zakończy się pomyślnie, zaświadczanie platformy Azure wystawia token zaświadczania i zwróci go do klienta. Jeśli ten krok zakończy się niepowodzeniem, zaświadczanie platformy Azure zgłosi błąd klientowi. Komunikacja między klientem a usługą zaświadczania zależy od protokołu TPM zaświadczania platformy Azure.
5.  Klient wysyła następnie token zaświadczania do jednostki uzależnionej. Jednostka uzależniona wywołuje punkt końcowy metadanych klucza publicznego z zaświadczania platformy Azure w celu pobrania certyfikatów podpisywania. Jednostka uzależniona weryfikuje sygnaturę tokenu zaświadczania i zapewnia wiarygodność platform.

![Przepływ walidacji modułu TPM](./media/tpm-validation-flow.png)

## <a name="next-steps"></a>Następne kroki
- [Tworzenie i podpisywanie zasad zaświadczania](author-sign-policy.md)
- [Konfigurowanie zaświadczania platformy Azure przy użyciu programu PowerShell](quickstart-powershell.md)
