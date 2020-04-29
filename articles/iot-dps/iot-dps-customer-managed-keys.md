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
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77675145"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Szyfrowanie danych przechowywanych przy użyciu kluczy zarządzanych przez klienta dla usługi Device Provisioning

## <a name="overview"></a>Omówienie

Usługa Device Provisioning Service (DPS) obsługuje szyfrowanie danych przechowywanych przy użyciu kluczy zarządzanych przez klienta (CMK), znanych również jako funkcje przydzielania własnego klucza (BYOK). Usługa DPS udostępnia szyfrowanie danych przechowywanych i przesyłanych w czasie. Domyślnie usługa DPS szyfruje dane za pomocą kluczy zarządzanych przez firmę Microsoft. Dzięki obsłudze CMK klienci mają teraz możliwość szyfrowania danych przechowywanych przy użyciu klucza szyfrowania klucza zarządzanego przez klientów przy użyciu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Ta funkcja wymaga utworzenia nowego centrum dystrybucji w jednym z następujących regionów: Wschodnie stany USA, zachodnie stany USA 2 lub Południowo-środkowe stany USA. Aby wypróbować tę możliwość, skontaktuj się z nami za pomocą [pomocy technicznej firmy Microsoft](https://azure.microsoft.com/support/create-ticket/). Skontaktuj się z pomocą techniczną firmy Microsoft, aby udostępnić nazwę firmy i Identyfikator subskrypcji.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o usłudze Device Provisioning](https://docs.microsoft.com/azure/iot-dps/)

* [Dowiedz się więcej o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)