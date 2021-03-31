---
title: Szyfrowanie danych usługi Azure Device Provisioning w spoczynku przez klucze zarządzane przez klienta | Microsoft Docs
description: Szyfrowanie danych przechowywanych przy użyciu kluczy zarządzanych przez klienta dla usługi Device Provisioning
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: d22a01bab81fc330484e7715a65c89a1cfd7802c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967180"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Szyfrowanie danych przechowywanych przy użyciu kluczy zarządzanych przez klienta dla usługi Device Provisioning

## <a name="overview"></a>Omówienie

Usługa Device Provisioning Service (DPS) obsługuje szyfrowanie danych przechowywanych przy użyciu kluczy zarządzanych przez klienta (CMK), znanych również jako funkcje przydzielania własnego klucza (BYOK). Usługa DPS zapewnia szyfrowanie danych przechowywanych i przesyłanych w trakcie ich pisania w naszych centrach, a następnie odszyfrowuje je w miarę uzyskiwania do nich dostępu. Domyślnie usługa DPS szyfruje dane przy użyciu kluczy zarządzanych przez firmę Microsoft. Przy użyciu programu CMK można uzyskać dodatkową warstwę szyfrowania na domyślnym szyfrowaniu platformy, wybierając opcję szyfrowania danych przechowywanych przy użyciu klucza szyfrowania kluczy zarządzanego przez [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Dzięki temu można elastycznie tworzyć, obracać, wyłączać i odwoływać klucze. Jeśli CMK jest skonfigurowany dla usługi DPS, oznacza to, że podwójne szyfrowanie jest włączone z dwiema warstwami ochrony aktywnie chroniących Twoje dane. 

Ta funkcja wymaga utworzenia nowego punktu dystrybucji. Aby wypróbować tę możliwość, skontaktuj się z nami za pomocą [pomocy technicznej firmy Microsoft](https://azure.microsoft.com/support/create-ticket/). Skontaktuj się z pomocą techniczną firmy Microsoft, aby udostępnić nazwę firmy i Identyfikator subskrypcji.


## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o usłudze Device Provisioning](./index.yml)

* [Dowiedz się więcej o Azure Key Vault](../key-vault/general/overview.md)