---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 7210b05566f5cd6f3c56792bce0904b3c9b645da
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993063"
---
## <a name="set-up-authentication"></a>Konfigurowanie uwierzytelniania

Aby uzyskać dostęp do usługi, musisz podać klucz konta, token dostępu lub token uwierzytelniania Azure Active Directory. Więcej informacji na ten temat można znaleźć na [stronie koncepcji uwierzytelniania](../articles/spatial-anchors/concepts/authentication.md).

### <a name="account-keys"></a>Klucze konta

Klucze kont to poświadczenia, które umożliwiają aplikacji uwierzytelnianie w usłudze kotwice przestrzenne platformy Azure. Zamierzony cel kluczy konta pomaga szybko rozpocząć pracę. Szczególnie w fazie tworzenia integracji aplikacji z zakotwiczeniami przestrzennymi platformy Azure. W związku z tym możesz użyć kluczy konta, osadzając je w aplikacjach klienckich podczas opracowywania. W miarę postępów poza programowaniem zdecydowanie zaleca się przejście do mechanizmu uwierzytelniania, który jest obsługiwany na poziomie produkcyjnym, obsługiwane przez tokeny dostępu lub Azure Active Directory uwierzytelniania użytkowników. Aby uzyskać klucz konta do celów deweloperskich, odwiedź konto kotwicowe platformy Azure i przejdź do karty "klucze".