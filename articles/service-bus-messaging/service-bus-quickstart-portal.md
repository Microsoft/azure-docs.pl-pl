---
title: Tworzenie kolejki Azure Portal za pomocą Service Bus kolejki
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć przestrzeń nazw Service Bus kolejkę w przestrzeni nazw przy użyciu Azure Portal.
author: spelluru
ms.author: spelluru
ms.date: 08/12/2020
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: e38d32c93453737060f654add58f09902b05ee45
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537200"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>Używanie Azure Portal do tworzenia Service Bus nazw i kolejki
W tym przewodniku Szybki start pokazano, jak utworzyć przestrzeń nazw Service Bus kolejkę przy użyciu [Azure Portal][Azure portal]. Pokazano w nim również, jak uzyskać poświadczenia autoryzacji, których aplikacja klienca może użyć do wysyłania i odbierania komunikatów do/z kolejki. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że masz subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem możesz utworzyć [bezpłatne][] konto.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono przestrzeń Service Bus i kolejkę w przestrzeni nazw . Aby dowiedzieć się, jak wysyłać i odbierać komunikaty do/z kolejki, zobacz jeden z następujących przewodników Szybki start w sekcji Wysyłanie i **odbieranie komunikatów.** 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
