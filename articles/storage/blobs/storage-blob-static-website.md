---
title: Hostowanie statycznej witryny internetowej w usłudze Azure Storage
description: Hostowanie statycznej witryny sieci Web usługi Azure Storage, które zapewnia ekonomiczne i skalowalne rozwiązanie do hostowania nowoczesnych aplikacji sieci Web.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 848fd89444281d82d6d0d1bfc4df15d499c09ee0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79370495"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hostowanie statycznej witryny internetowej w usłudze Azure Storage

Zawartości statycznej (HTML, CSS, JavaScript i plików obrazów) można obsłużyć bezpośrednio w kontenerze magazynu o nazwie *$Web*. Hosting zawartości w usłudze Azure Storage umożliwia korzystanie z architektur bezserwerowych, które obejmują [Azure Functions](/azure/azure-functions/functions-overview) i innych usług platformy jako usługi (PaaS).

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE]
> Jeśli lokacja jest zależna od kodu po stronie serwera, należy zamiast tego użyć [Azure App Service](/azure/app-service/overview) .

## <a name="setting-up-a-static-website"></a>Konfigurowanie statycznej witryny sieci Web

Hostowanie statycznej witryny sieci Web to funkcja, którą należy włączyć na koncie magazynu.

Aby włączyć obsługę statycznej witryny sieci Web, wybierz nazwę pliku domyślnego, a następnie Opcjonalnie podaj ścieżkę do niestandardowej strony 404. Jeśli kontener usługi BLOB Storage o nazwie **$Web** nie istnieje już na koncie, zostanie on utworzony. Dodaj pliki witryny do tego kontenera.

Aby uzyskać wskazówki krok po kroku, zobacz [hostowanie statycznej witryny sieci Web w usłudze Azure Storage](storage-blob-static-website-how-to.md).

![Metryka statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Pliki w kontenerze **$Web** są rozróżniane wielkości liter, obsługiwane przez żądania dostępu anonimowego i są dostępne tylko za pomocą operacji odczytu.

## <a name="uploading-content"></a>Przekazywanie zawartości

Aby przekazać zawartość do kontenera **$Web** , można użyć dowolnego z tych narzędzi:

> [!div class="checklist"]
> * [Interfejs wiersza polecenia platformy Azure](storage-blob-static-website-how-to.md#cli)
> * [Moduł programu Azure PowerShell](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Rozszerzenie programu Visual Studio Code](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Wyświetlanie zawartości

Użytkownicy mogą wyświetlać zawartość witryny z przeglądarki przy użyciu publicznego adresu URL witryny sieci Web. Adres URL można znaleźć za pomocą Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Użyj tej tabeli jako przewodnika.

|Narzędzie| Wskazówki |
|----|----|
|**Azure Portal** | [Znajdź adres URL witryny sieci Web przy użyciu Azure Portal](storage-blob-static-website-how-to.md#portal-find-url) |
|**Interfejs wiersza polecenia platformy Azure** | [Znajdowanie adresu URL witryny sieci Web przy użyciu interfejsu wiersza polecenia platformy Azure](storage-blob-static-website-how-to.md#cli-find-url) |
|**Moduł programu Azure PowerShell** | [Znajdowanie adresu URL witryny sieci Web przy użyciu programu PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

Adres URL witryny zawiera kod regionalny. Na przykład adres URL `https://contosoblobaccount.z22.web.core.windows.net/` zawiera kod `z22`regionalny.

Chociaż kod ten musi pozostawać w adresie URL, jest używany tylko do użytku wewnętrznego i nie będzie konieczne używanie tego kodu w żaden inny sposób.

Dokument indeksu określony po włączeniu hostingu statycznej witryny sieci Web jest wyświetlany, gdy użytkownicy otworzą lokację i nie określą określonego pliku (na przykład `https://contosoblobaccount.z22.web.core.windows.net`:).  

Jeśli serwer zwróci błąd 404 i nie został określony dokument błędu po włączeniu witryny sieci Web, do użytkownika zostanie zwrócona domyślna strona 404.

> [!NOTE]
> Mechanizm [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) nie jest obsługiwany w przypadku statycznej witryny sieci Web.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Wpływ ustawienia publicznego poziomu dostępu kontenera sieci Web

Możesz zmodyfikować poziom dostępu publicznego kontenera **$Web** , ale nie ma to wpływu na podstawowy statyczny punkt końcowy witryny sieci Web, ponieważ te pliki są obsługiwane za pomocą żądań dostępu anonimowego. Oznacza to, że dostęp publiczny (tylko do odczytu) do wszystkich plików.

Poniższy zrzut ekranu przedstawia ustawienia poziomu dostępu publicznego w Azure Portal:

![Zrzut ekranu przedstawiający sposób ustawiania poziomu dostępu publicznego w portalu](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Gdy nie ma to wpływu na podstawowy punkt końcowy statycznej witryny internetowej, zmiana poziomu dostępu publicznego ma wpływ na podstawowy punkt końcowy usługi BLOB.

Na przykład w przypadku zmiany publicznego poziomu dostępu kontenera **$Web** z **prywatnego (brak dostępu anonimowego)** do **obiektu BLOB (Anonimowy dostęp do odczytu tylko dla obiektów BLOB)**, poziom publicznego dostępu do podstawowego statycznego punktu końcowego `https://contosoblobaccount.z22.web.core.windows.net/index.html` witryny sieci Web nie jest zmieniany.

Jednak publiczny dostęp do podstawowego punktu końcowego `https://contosoblobaccount.blob.core.windows.net/$web/index.html` usługi BLOB Service zmieni się z prywatnego na publiczny. Teraz użytkownicy mogą otwierać ten plik za pomocą jednego z tych dwóch punktów końcowych.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Mapowanie domeny niestandardowej na adres URL statycznej witryny internetowej

Możesz udostępnić statyczną witrynę sieci Web za pośrednictwem domeny niestandardowej. 

Łatwiej jest włączyć dostęp do protokołu HTTP dla domeny niestandardowej, ponieważ usługa Azure Storage natywnie obsługuje ją. Aby włączyć protokół HTTPS, musisz użyć Azure CDN, ponieważ usługa Azure Storage nie obsługuje jeszcze natywnie protokołu HTTPS z domenami niestandardowymi. Aby uzyskać wskazówki krok po kroku, zobacz [Mapowanie domeny niestandardowej na punkt końcowy usługi Azure Blob Storage](storage-custom-domain-name.md) .

Jeśli konto magazynu jest skonfigurowane tak, aby [wymagało bezpiecznego transferu](../common/storage-require-secure-transfer.md) za pośrednictwem protokołu HTTPS, użytkownicy muszą używać punktu końcowego HTTPS. 

> [!TIP]
> Rozważ Hostowanie domeny na platformie Azure. Aby uzyskać więcej informacji, zobacz [Hostowanie domeny w Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Dodawanie nagłówków HTTP

Nie ma możliwości skonfigurowania nagłówków w ramach funkcji statycznej witryny sieci Web. Można jednak użyć Azure CDN do dodawania nagłówków i dołączania (lub zastępowania) wartości nagłówka. Zobacz [Dokumentacja aparatu reguł standardowych dla Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference).

Jeśli chcesz użyć nagłówków do sterowania buforowaniem, zobacz temat [kontrola Azure CDN buforowania przy użyciu reguł buforowania](https://docs.microsoft.com/azure/cdn/cdn-caching-rules).

## <a name="pricing"></a>Cennik

Bezpłatnie możesz włączyć hosting statycznej witryny sieci Web. Opłaty są naliczane tylko za magazyn obiektów BLOB wykorzystywany przez lokację i koszty operacji. Aby uzyskać więcej informacji na temat cen usługi Azure Blob Storage, zapoznaj się z [cennikiem usługi azure BLOB Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Metryki

Metryki można włączyć na stronach statycznej witryny internetowej. Po włączeniu metryk statystyki ruchu dla plików w kontenerze **$Web** są raportowane na pulpicie nawigacyjnym metryk.

Aby włączyć metryki na stronach statycznej witryny sieci Web, zobacz temat [Włączanie metryk na stronach statycznej witryny internetowej](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Następne kroki

* [Hostowanie statycznej witryny sieci Web w usłudze Azure Storage](storage-blob-static-website-how-to.md)
* [Mapowanie domeny niestandardowej na punkt końcowy usługi Azure Blob Storage](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Tworzenie pierwszej aplikacji sieci Web bezserwerowej](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Samouczek: hostowanie własnej domeny w usłudze Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
