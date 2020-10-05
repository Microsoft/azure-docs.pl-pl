---
title: Tworzenie kolejki Service Bus za pomocą Azure Portal
description: W tym przewodniku szybki start dowiesz się, jak utworzyć Service Bus przestrzeń nazw i kolejkę w przestrzeni nazw przy użyciu Azure Portal.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 0e5a8698da03ee4d9c7d8b0d4d59eda1be3b1883
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88185506"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>Użyj Azure Portal, aby utworzyć Service Bus przestrzeń nazw i kolejkę
Ten przewodnik Szybki Start przedstawia sposób tworzenia Service Bus przestrzeni nazw i kolejki przy użyciu [Azure Portal][Azure portal]. Pokazano w nim także, jak uzyskać poświadczenia autoryzacji, które mogą być używane przez aplikację kliencką do wysyłania/odbierania komunikatów z kolejki. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, upewnij się, że masz subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto][] przed rozpoczęciem.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>Następne kroki
W tym artykule opisano tworzenie Service Bus przestrzeni nazw i kolejki w przestrzeni nazw. Aby dowiedzieć się, jak wysyłać/odbierać komunikaty do/z kolejki, zobacz jeden z następujących przewodników szybki start w sekcji **wysyłanie i odbieranie komunikatów** . 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues-new-package.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
