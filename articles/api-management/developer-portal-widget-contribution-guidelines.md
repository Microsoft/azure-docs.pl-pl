---
title: Jak współtwomentować widżety dla portalu dla deweloperów
titleSuffix: Azure API Management
description: Dowiedz się więcej na temat zalecanych wytycznych, które należy stosować w przypadku współtwoczenia widżetu API Management repozytorium portalu deweloperów.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c4d3ed2aeaac57f721d23d7c7aa1c70ef14e4294
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741879"
---
# <a name="how-to-contribute-widgets-to-the-api-management-developer-portal"></a>Jak współtwomentować widżety w API Management portal deweloperów

Jeśli chcesz współtworzyć widżet w repozytorium [GitHub](https://github.com/Azure/api-management-developer-portal)portalu deweloperów API Management, wykonaj trzy kroki tego procesu:

1. Zadychaj repozytorium.

1. Zaim implementuj widżet.

1. Otwórz żądanie ściągnięcie, aby dołączyć widżet do oficjalnego repozytorium.

Widżet będzie dziedziczyć licencję repozytorium. Będzie ona dostępna do [instalacji w](developer-portal-use-community-widgets.md) samodzielnie hostowanej wersji portalu. Zespół portalu deweloperów może zdecydować się również na dołącznie go do zarządzanej wersji portalu.

Zapoznaj się z [samouczkiem implementacji widżetu,](developer-portal-implement-widgets.md) aby zapoznać się z przykładem tworzenia własnego widżetu.

## <a name="contribution-guidelines"></a>Wytyczne dotyczące udziału

Te wskazówki mają na celu zapewnienie bezpieczeństwa i prywatności naszych klientów i osób odwiedzających ich portale. Postępuj zgodnie z tymi wytycznymi, aby upewnić się, że Twój wkład jest akceptowany:

1. Umieść widżet w `community/widgets/<your-widget-name>` folderze .

1. Nazwa widżetu musi być mała i alfanumeryczna z kreskami oddzielającym wyrazy. Na przykład `my-new-widget`.

1. Folder musi zawierać zrzut ekranu widżetu w opublikowanym portalu.

1. Folder musi zawierać `readme.md` plik, który następuje po szablonie z `/scaffolds/widget/readme.md` pliku.

1. Folder może zawierać plik z poleceniami npm do instalowania zależności widżetu `npm_dependencies` lub zarządzania nimi.

    Jawnie określ wersję każdej zależności. Na przykład:  

    ```console
    npm install azure-storage@2.10.3 axios@0.19.1
    ```

    Widżet powinien wymagać minimalnych zależności. Każda zależność będzie dokładnie sprawdzana przez recenzentów. W szczególności podstawowa logika widżetu powinna być typu open source w folderze widżetu. Nie opakuj go w pakiecie npm.

1. Zmiany w plikach spoza folderu widżetu nie są dozwolone w ramach udziału widżetu. Obejmuje to plik , ale nie jest ograniczony do `/package.json` tego pliku.

1. Wstrzykiwanie skryptów śledzenia lub wysyłanie danych niestandardowych do usług niestandardowych jest niedozwolone.

    > [!NOTE]
    > Dane autorstwa klienta można zbierać tylko za pośrednictwem `Logger` interfejsu.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o udziałach, zobacz repozytorium GitHub API Management portal dla [deweloperów.](https://github.com/Azure/api-management-developer-portal/)

- Zobacz [Implementowanie widżetów,](developer-portal-implement-widgets.md) aby dowiedzieć się, jak tworzyć własne widżety krok po kroku.

- Zobacz [Korzystanie z widżetów społeczności,](developer-portal-use-community-widgets.md) aby dowiedzieć się, jak korzystać z widżetów współtwonych przez społeczność.