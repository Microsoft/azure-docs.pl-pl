---
author: baanders
description: Dołącz plik do usługi Azure Digital bliźniaczych reprezentacji — wymaganie wstępne, aby skonfigurować wystąpienie
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 3f46f7131d5465ec6542d9212e310c04a656f50c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774208"
---
Aby można było korzystać z usługi Azure Digital bliźniaczych reprezentacji w tym artykule, należy najpierw **skonfigurować wystąpienie usługi Azure Digital bliźniaczych reprezentacji** i wymagane uprawnienia do korzystania z niego. Jeśli masz już skonfigurowane wystąpienie usługi Azure Digital bliźniaczych reprezentacji na podstawie poprzedniej pracy, możesz użyć tego wystąpienia.

W przeciwnym razie postępuj zgodnie z instrukcjami podanymi w temacie [*How to: Konfigurowanie wystąpienia i uwierzytelniania*](../articles/digital-twins/how-to-set-up-instance-portal.md). Instrukcje zawierają również kroki umożliwiające zweryfikowanie, że wszystkie czynności zostały wykonane pomyślnie i są gotowe do przejścia do korzystania z nowego wystąpienia.

Po skonfigurowaniu wystąpienia usługi Azure Digital bliźniaczych reprezentacji należy pamiętać o następujących wartościach, które będą potrzebne do późniejszego nawiązania połączenia z wystąpieniem:
* **Nazwa hosta** wystąpienia. Tę wartość można znaleźć w Azure Portal ([instrukcje](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* **subskrypcja platformy Azure** użyta do utworzenia wystąpienia (jego nazwa lub identyfikator będzie działała). Możesz sprawdzić, z jaką subskrypcją jest wystąpienie usługi Azure Digital bliźniaczych reprezentacji, wyświetlając tę samą stronę *przeglądu* dla danego wystąpienia w [Azure Portal](https://portal.azure.com).
