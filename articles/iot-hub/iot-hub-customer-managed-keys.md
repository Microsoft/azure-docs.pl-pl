---
title: Usługa Azure IoT Hub szyfrowanie danych w spoczynku przez klucze zarządzane przez klienta | Microsoft Docs
description: Szyfrowanie danych przechowywanych przy użyciu kluczy zarządzanych przez klienta dla IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 83d2fa59654e038586a7f23eedbe7c656873f35c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84976578"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Szyfrowanie danych przechowywanych przy użyciu kluczy zarządzanych przez klienta dla IoT Hub

IoT Hub obsługuje szyfrowanie danych przechowywanych przy użyciu kluczy zarządzanych przez klienta (CMK), znane także jako dostarczenie własnego klucza (BYOK). Usługa Azure IoT Hub zapewnia szyfrowanie danych przechowywanych i przesyłanych w czasie, gdy są one zapisywane w naszych centrach Domyślnie program IoT Hub szyfruje dane przy użyciu kluczy zarządzanych przez firmę Microsoft. Za pomocą CMK można uzyskać kolejną warstwę szyfrowania na domyślnym szyfrowaniu i można wybrać szyfrowanie danych przechowywanych przy użyciu klucza szyfrowania klucza zarządzanego przez [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Zapewnia to elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Jeśli BYOK jest skonfigurowany dla IoT Hub, zapewniamy również podwójne szyfrowanie, które oferuje drugą warstwę ochrony, a jednocześnie pozwala kontrolować klucz szyfrowania za pomocą Azure Key Vault.

Ta funkcja wymaga utworzenia nowej IoT Hub (warstwa podstawowa lub standardowa). Aby wypróbować tę możliwość, skontaktuj się z nami za pomocą [pomocy technicznej firmy Microsoft](https://azure.microsoft.com/support/create-ticket/). Skontaktuj się z pomocą techniczną firmy Microsoft, aby udostępnić nazwę firmy i Identyfikator subskrypcji.


## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Dowiedz się więcej o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
