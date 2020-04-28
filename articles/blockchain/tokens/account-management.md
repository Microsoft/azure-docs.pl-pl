---
title: Zarządzanie kontami tokenów usługi Azure łańcucha bloków
description: Za pomocą usługi Azure łańcucha bloków Tokens Management można tworzyć grupy i łączyć konta łańcucha bloków w celu kontrolowania dostępu do akcji łańcucha bloków.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74326098"
---
# <a name="azure-blockchain-tokens-account-management"></a>Zarządzanie kontami tokenów usługi Azure łańcucha bloków

[!INCLUDE [Preview note](./includes/preview.md)]

W przypadku rozwiązania łańcucha bloków użytkownicy mogą wymagać różnych poziomów dostępu do tokenów, które są tworzone za pomocą usługi Azure łańcucha bloków tokens. W większości scenariuszy łańcucha bloków należy zaplanować i wdrożyć różne konta łańcucha bloków, które istnieją w księdze. Musisz również zarządzać dostępem między uczestnikami.Za pomocą usługi Azure łańcucha bloków Tokens Management można tworzyć grupy i łączyć konta łańcucha bloków w celu kontrolowania dostępu do akcji łańcucha bloków.

## <a name="blockchain-networks"></a>Sieci łańcucha bloków

Tokeny usługi Azure łańcucha bloków umożliwiają wdrażanie tokenów i zarządzanie nimi w zestawie łańcucha blokówych sieci. Do usługi można podłączyć pojedyncze łańcucha bloków lub kilka ksiąg łańcucha bloków.

## <a name="accounts"></a>Konta

W przypadku sieci łańcucha bloków podłączonych do tokenów usługi Azure łańcucha bloków Usługa tworzy pary kluczy prywatny-publiczny i zarządza nimi oraz wykonuje podpisywanie i przesyłanie transakcji. Tokeny usługi Azure łańcucha bloków zapewniają również mapowanie tożsamości w celu dopasowania do kont z tożsamością klucza publicznego w księdze.

## <a name="groups"></a>Grupy

Grupy umożliwiają zarządzanie dużą liczbą kont łańcucha bloków w połączonych sieciach. Można śledzić i kontrolować, które aplikacje i użytkownicy w katalogu mają możliwość używania kont za pomocą interfejsów API tokenów usługi Azure łańcucha bloków. Można na przykład grupować zestaw kont, które reprezentują różne linie biznesowe lub różne role oraz dostęp do tokenów łańcucha bloków.

Można także skojarzyć grupę z użytkownikiem Azure Active Directory lub jednostką usługi, a podmiot zabezpieczeń ma uprawnienia do grupy i skojarzonych z nią kont.  

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o dostępnych [szablonach tokenów usługi Azure łańcucha bloków](templates.md).
