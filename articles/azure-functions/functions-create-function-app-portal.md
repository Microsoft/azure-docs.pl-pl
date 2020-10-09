---
title: Tworzenie aplikacji funkcji przy użyciu witryny Azure Portal
description: Utwórz nową aplikację funkcji na platformie Azure z poziomu portalu.
ms.topic: how-to
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 001b4e4f0ea7fbacd232b2a87abfe353f34919bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80985018"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Tworzenie aplikacji funkcji przy użyciu witryny Azure Portal

W tym temacie pokazano, jak za pomocą Azure Functions utworzyć aplikację funkcji w Azure Portal. Aplikacja funkcji to kontener hostujący wykonywanie poszczególnych funkcji. 

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Po utworzeniu aplikacji funkcji możesz utworzyć indywidualne funkcje w jednym lub kilku różnych językach. Możesz tworzyć funkcje [za pomocą portalu](functions-create-first-azure-function.md#create-function), [ciągłego wdrażania](functions-continuous-deployment.md) lub [przekazywania przy użyciu protokołu FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Plany usługi

Azure Functions ma trzy różne plany usługi: plan zużycia, plan Premium i dedykowany plan (App Service). Musisz wybrać swój plan usługi, gdy aplikacja funkcji zostanie utworzona i nie można jej później zmienić. Aby uzyskać więcej informacji, zobacz [Wybieranie planu hostingu usługi Azure Functions](functions-scale.md).

Jeśli planujesz uruchamianie funkcji JavaScript w ramach dedykowanego planu (App Service), należy wybrać plan z mniejszą liczbą rdzeni. Aby uzyskać więcej informacji, zobacz [Dokumentacja języka JavaScript dla usługi Functions](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Wymagania konta magazynu

Podczas tworzenia aplikacji funkcji należy utworzyć konto usługi Azure Storage ogólnego przeznaczenia lub połączyć się z nim, które obsługuje magazyn obiektów blob, kolejek i tabel. Wewnętrznie usługa Functions używa usługi Storage na potrzeby operacji takich jak zarządzanie wyzwalaczami i rejestrowanie wykonań funkcji. Niektóre konta magazynu nie obsługują kolejek i tabel, na przykład konta magazynu tylko dla obiektów blob, konta usługi Azure Premium Storage i konta magazynu ogólnego przeznaczenia z replikacją ZRS. Te konta są odfiltrowywane z bloku Konto magazynu podczas tworzenia aplikacji funkcji.

>[!NOTE]
>Podczas korzystania z planu hostingu Zużycie kod funkcji i pliki konfiguracji powiązania są przechowywane w usłudze Azure File Storage na głównym koncie magazynu. Po usunięciu głównego konta magazynu ta zawartość zostanie usunięta i nie będzie można jej odzyskać.

Aby dowiedzieć się więcej na temat typów kont magazynu, zobacz [Wprowadzenie do usług Azure Storage](../storage/common/storage-introduction.md#core-storage-services). 

## <a name="next-steps"></a>Następne kroki

Mimo że Azure Portal ułatwia tworzenie i wypróbowanie funkcji, zalecamy [programowanie lokalne](functions-develop-local.md). Po utworzeniu aplikacji funkcji w portalu nadal trzeba dodać funkcję. 

> [!div class="nextstepaction"]
> [Dodaj funkcję wyzwalaną przez protokół HTTP](functions-create-first-azure-function.md#create-function)
